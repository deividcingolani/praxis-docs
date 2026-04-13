# Settlement Service — Technical Spec

## Overview

El Settlement Service es el puente entre el matching engine off-chain y los smart contracts on-chain. Cuando el matching engine crea un trade en la DB, el Settlement Service lo ejecuta en la blockchain Polygon a través del contrato CTFExchange.

**Estado actual:** El servicio existe (`settlement.worker.ts`, `settlement.service.ts`, `blockchain.service.ts`) pero corre en **mock mode** (`BLOCKCHAIN_ENABLED=false`). Los contratos no están deployados.

---

## Flujo Completo

```
1. Matching Engine crea Trade (DB) → txHash = NULL
   ↓
2. BullMQ job "settle-trade" enqueued
   ↓
3. Settlement Worker picks up job
   ↓
4. Settlement Service:
   a. Fetch trade + maker/taker orders + outcome tokenId
   b. Build CTFExchange.Order struct (EIP-712)
   c. Get operator nonce from Redis
   d. Estimate gas (1.2x buffer)
   e. Send TX: exchange.fillOrder(order, signature, fillAmount)
   f. Wait for receipt
   g. Update trade.txHash = receipt.hash
   ↓
5. Indexer polls OrderFilled event (5s interval)
   ↓
6. Confirm trade, broadcast via WebSocket
```

---

## Smart Contracts a deployar

| Contrato | Función | Dependencia |
|----------|---------|-------------|
| **ConditionalTokens** | ERC-1155 outcome tokens + minting/burning/redemption | USDC contract |
| **CTFExchange** | Order execution, fee collection, operator role | ConditionalTokens, USDC |
| **UMACTFAdapter** | Market resolution via UMA oracle | ConditionalTokens, UMA Oracle V3 |

### Deploy order:
1. ConditionalTokens (sin dependencias)
2. CTFExchange (requiere ConditionalTokens + USDC address)
3. UMACTFAdapter (requiere ConditionalTokens + UMA Oracle)
4. Registrar token pairs en CTFExchange (`registerToken`)
5. Set operator address en CTFExchange

---

## TX Manager

### Nonce Management (ya implementado)
- Nonce almacenado en Redis key `operator:nonce`
- Primera vez: seed desde on-chain `wallet.getNonce()`
- Atomic increment con Redis `INCR`
- Lock con TTL 10s para prevenir race conditions

### Gas Estimation (ya implementado)
- `estimateGasWithBuffer()` — 1.2x multiplicador
- Fallback: 500K gas si estimation falla

### Retry Logic (ya implementado)
- 3 intentos con exponential backoff (2^attempt * 1000ms)
- Si nonce error: reset a on-chain nonce y retry
- Si otro error: backoff y retry

### Stuck TX Handling (FALTA)
- **Necesita:** Monitor de TXs pendientes > 5 min
- **Acción:** Speed up con mayor gas price o cancel con nonce replacement
- **Implementar:** Cron job cada 1 min que checkea TXs pending en Redis

---

## Batching

### Actual: fillOrder (1 trade = 1 TX)
- Simple pero costoso en gas
- ~150K gas por trade

### Optimizado: fillOrders (N trades = 1 TX)
- CTFExchange ya soporta `fillOrders(Order[], bytes[], uint256[])`
- Agrupar trades del mismo market o batch temporal (cada 5 min)
- settle-batch job cada 5 min ya existe en el worker
- **Límite:** max 10 trades por batch (gas limit ~1.5M)

### Implementación recomendada:
1. **Phase 1 (MVP):** fillOrder individual — simple, funciona
2. **Phase 2:** fillOrders batch cada 5 min — reduce costos 60-70%

---

## Indexer (ya implementado)

### Polling
- Intervalo: 5 segundos
- Max 1000 blocks por poll
- Estado: último block procesado en Redis `indexer:last_processed_block`

### Eventos monitoreados

| Contrato | Evento | Acción |
|----------|--------|--------|
| CTFExchange | `OrderFilled` | Confirma trade, actualiza DB, broadcast WS |
| CTFExchange | `OrderCancelled` | Marca orden cancelada |
| ConditionalTokens | `ConditionResolution` | Market resuelto, broadcast |
| ConditionalTokens | `PayoutRedemption` | Payout confirmado |
| UMACTFAdapter | `QuestionResolved` | Resolución confirmada |
| UMACTFAdapter | `ResolutionRequested` | Resolución en proceso |

---

## Error Handling

| Error | Causa | Acción |
|-------|-------|--------|
| TX revert | Insufficient balance, cancelled order, overfill | Mark trade as failed (`0xFFF...`), log, alert |
| Out of gas | Gas estimate too low | Retry con 1.5x gas |
| Nonce too low | TX already mined | Reset nonce from chain, retry |
| Nonce too high | Gap in nonces | Wait for previous TX, retry |
| RPC timeout | Node down | Retry con backoff, switch RPC |
| Signer error | Key issue | Alert admin, pause settlement |

### Trade failure states:
- `txHash = NULL` → pendiente de settlement
- `txHash = "0xFFF..."` (64 F's) → settlement failed
- `txHash = "0x..."` (valid hash) → settled on-chain

**FALTA:** Agregar un campo `settlementStatus` al schema de trades: `pending | submitted | confirmed | failed`

---

## Reconciliación

### Qué verificar:
1. Trades con `txHash != NULL` pero sin evento `OrderFilled` → TX pendiente o dropped
2. Eventos `OrderFilled` sin trade correspondiente en DB → inconsistencia
3. Balance on-chain de outcome tokens vs posiciones en DB

### Implementación:
- Cron job cada 1 hora
- Query: trades con txHash donde status no es "confirmed"
- Para cada uno: verificar receipt on-chain
- Si receipt.status === 0: marcar como failed, intentar re-settle
- Si no hay receipt: TX fue dropped, re-submit

---

## Dependencias y Prerequisitos

### Antes de activar BLOCKCHAIN_ENABLED=true:

| # | Item | Estado | Esfuerzo |
|---|------|--------|----------|
| 1 | Deploy ConditionalTokens a Polygon Amoy (testnet) | Pendiente | 1 día |
| 2 | Deploy CTFExchange | Pendiente | 1 día |
| 3 | Deploy UMACTFAdapter | Pendiente | 1 día |
| 4 | Fondear operator wallet con MATIC (gas) | Pendiente | 1 hora |
| 5 | Fondear con USDC para collateral | Pendiente | 1 hora |
| 6 | Registrar token pairs en CTFExchange | Pendiente | 1 hora |
| 7 | Implementar EIP-712 signing en frontend | Pendiente | 3 días |
| 8 | Agregar `settlementStatus` a trades schema | Pendiente | 2 horas |
| 9 | Implementar stuck TX monitor | Pendiente | 1 día |
| 10 | Implementar reconciliation cron | Pendiente | 1 día |
| 11 | Tests de settlement (testnet) | Pendiente | 2 días |

### Estimación total: 2-3 semanas

---

## Orden de implementación recomendado

### Semana 1: Contracts + Backend
1. Deploy contracts a Amoy testnet
2. Fondear operator wallet
3. Agregar `settlementStatus` al schema
4. Activar `BLOCKCHAIN_ENABLED=true` en staging
5. Test: crear market → trade → settlement → verificar on-chain

### Semana 2: Frontend + Signing
6. Implementar EIP-712 signing en frontend (real signatures, no placeholders)
7. Implementar stuck TX monitor
8. Test E2E: usuario crea orden → firma → match → settle → posición on-chain

### Semana 3: Hardening
9. Reconciliation cron
10. Batch settlement (fillOrders)
11. Market resolution via UMA
12. Load testing en testnet

---

## Env vars necesarias (ya definidas)

```
BLOCKCHAIN_ENABLED=true
POLYGON_RPC_URL=https://polygon-amoy.g.alchemy.com/v2/YOUR_KEY
OPERATOR_PRIVATE_KEY=0x...
CTF_EXCHANGE_ADDRESS=0x... (post-deploy)
CONDITIONAL_TOKENS_ADDRESS=0x... (post-deploy)
USDC_ADDRESS=0x... (Amoy USDC)
UMA_CTF_ADAPTER_ADDRESS=0x... (post-deploy)
FEE_COLLECTOR_ADDRESS=0x... (operator or multisig)
```
