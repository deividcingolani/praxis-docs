# Gaps & Pendientes — Phase 0 y Phase 1

Lo que se salteó o quedó incompleto de las fases 0 y 1.
Actualizado con feedback del equipo completo (12 especialistas) — Abril 2026.

---

## Phase 0: Foundation

| Deliverable | Estado | Detalle |
|---|---|---|
| 0.1 Estructura Legal | **Completo** | Documento con análisis de 5 jurisdicciones, KYC tiers, AML, ToS, geo-blocking |
| 0.2 Modelo de Negocio | **Completo** | Fee structure, unit economics, proyecciones 24 meses, fundraising |
| 0.3 Identidad de Marca | **Completo** | Forka brand guidelines, GTM, multi-branding config, i18n (EN/ES/PT) |
| 0.4 Arquitectura Técnica | **Completo** | Diagramas Mermaid, data model, API design, infra |
| 0.4 CI/CD Pipeline | **Salteado** | Diseñado en el doc pero no se creó `.github/workflows/` |
| **0.1 Regulación fiat onramp** | **Falta** | Sección 9 propuesta por Legal: regulación de servicios de pago, money transmission, chargebacks, currencies permitidas (NO USD, NO BRL, solo EUR vía PSP→USDC) |
| **0.2 Costos fiat onramp** | **Falta** | Sección 2.8 propuesta por Finanzas: PSP platform fees, fraud monitoring, chargeback liability, KYC incremental (+$6,300/mes en Phase 3) |
| **0.3 Vocabulary guidelines** | **Falta** | Sección 3.6 propuesta por Copywriter: tabla DO/DON'T de términos crypto, regla "fiat es el camino default" |

---

## Phase 1: MVP Core

### 1.1 Smart Contracts — Completo
| Deliverable | Estado |
|---|---|
| ConditionalTokens.sol (ERC-1155 CTF) | Completo |
| CTFExchange.sol (EIP-712, matching, fees) | Completo |
| UMACTFAdapter.sol (Oracle) | Completo |
| Interfaces + Mocks (MockUSDC, MockUMAOracle) | Completo |
| Tests (27/27 passing) | Completo |
| Deploy script | Completo |
| **Auditoría de seguridad (Slither)** | **Salteado** — se planeó correr Slither static analysis, no se ejecutó |

### 1.2 Backend Services — Parcial
| Deliverable | Estado | Notas |
|---|---|---|
| Auth Service (SIWE + JWT) | Completo | |
| Auth dual (email/password + Google OAuth) | **✅ Completo** | `user_auth_methods` table, account linking |
| Market Service (CRUD, búsqueda, estados) | Completo | |
| Order Service + Matching Engine | Completo | In-memory, price-time priority |
| Trade Service | Completo | |
| Position Service (P&L) | Completo | |
| Price Service (OHLCV candles) | Completo | |
| WebSocket Service (Socket.io) | Completo | Estructura lista, falta testing real |
| KYC Service | Completo | Mock mode, sin Sumsub real |
| API Routes (auth, markets, orders, trades, positions, prices, kyc) | Completo | |
| Middleware (auth, validation, kyc) | Completo | |
| Payment Service | **✅ Completo** | Deposit/withdraw flows, payment_transactions table, PaymentProvider support |
| Balance Service | **✅ Completo** | user_balances (available+locked), SELECT FOR UPDATE, ledger_entries |
| PSP Integration (MoonPay + Mercado Pago) | **✅ Completo** | MoonPay browser SDK + Mercado Pago Checkout Pro |
| Webhook endpoints PSP | **✅ Completo** | /webhooks/moonpay + HMAC signature + idempotency |
| Admin auth (email+Google, separate admin_users) | **✅ Completo** | Separate auth system for admin panel |
| **Settlement Service** | **En progreso — Semana 1 DONE** | Adapter pattern implementado (Mock + Blockchain), 11 tests, `settlementStatus` en schema. Falta: deploy contracts + activar path real (B1.2–B1.7) |
| **Blockchain Transaction Manager** | **✅ Completo** | Implementado en `blockchain.service.ts` — nonce mgmt, gas estimation, retry logic |
| **Background Workers (BullMQ)** | **✅ Completo** | Implementado en `settlement.worker.ts` — settle-trade + settle-batch jobs |
| **On-chain event listener / Indexer** | **✅ Completo** | Implementado en `indexer.service.ts` — polling 5s, 6 eventos monitoreados |
| **Rate limiting por endpoint** | **✅ Parcial → Auth DONE** | Global (100 req/min) + auth endpoints (10/min strict, 30/min moderate) + orders (10/s). Falta: otros endpoints |

### 1.3 Frontend — Parcial
| Deliverable | Estado | Notas |
|---|---|---|
| Design System (9 componentes UI) | Completo | button, input, modal, card, badge, skeleton, tabs, toast, spinner |
| Layout (header, footer) | Completo | Con wallet connect, nav, search |
| Wallet + Auth (RainbowKit, SIWE) | Completo | |
| AuthModal dual (email/Google + wallet) | **✅ Completo** | Onboarding dual para fiat y crypto users |
| Market Explorer page | Completo | Con filtros, búsqueda, sort, mock data |
| Market Detail page | Completo | Con order book, trading panel, trade history |
| Trading Panel | Completo | Buy YES/NO, limit/market, confirmación |
| Portfolio page | Completo | Posiciones, órdenes, historial, P&L |
| KYC page (3 pasos) | Completo | Con banner contextual |
| Legal pages (Terms, Privacy) | Completo | |
| Docs pages (4 guías) | Completo | |
| Mock data (10 mercados) | Completo | |
| TradingView Charts | **✅ Completo** | `price-chart.tsx` — area + candlestick + volume + timeframes + tooltips + real-time via WS |
| **Order book click-to-fill** | **Salteado** | |
| WebSocket real-time updates | **✅ Completo** | socket.io-client + broadcasts en matching engine (orderbook, trades, prices, user events) |
| **Mobile responsive** | **Parcial** | Classes existen, no testeado |
| **E2E Tests (Playwright)** | **Salteado** | |
| **BrandProvider + useCurrency** | **Salteado** | No hay abstracción de moneda ni multibranding en frontend |

### 1.4 UX Design — Parcial
| Deliverable | Estado |
|---|---|
| User flows (5 flujos con Mermaid) | Completo |
| Interaction specs | Completo |
| Responsive layout specs | Completo |
| Accessibility checklist | Completo |
| **Flow 1B: Onboarding fiat** | **Parcial** | Auth dual + DepositModal existen. Falta: wizard guiado para primer trade |
| Flow 5A: Depósito fiat | **✅ Completo** | DepositModal con MoonPay/Mercado Pago/Crypto + polling de estado + indicadores pending/completed/failed |
| Error states fiat | **✅ Completo** | Polling post-depósito, toast notifications, status indicators. MoonPay maneja card declined en su widget |

### 1.5 KYC Flow — Parcial
| Deliverable | Estado | Notas |
|---|---|---|
| KYC Service (3 tiers, limits) | Completo | Mock mode |
| KYC Routes | Completo | |
| KYC Middleware (requireKyc) | Completo | |
| Trading limits enforcement | Completo | Integrado en order creation |
| KYC Frontend page | Completo | 3-step wizard |
| KYC Banner component | Completo | |
| **Sumsub integration** | **Parcial** | Webhook exists, full flow partial |
| **Geo-blocking middleware** | **Salteado** | |
| **Admin panel para KYC review** | **✅ Completo** | KYC approve/reject in admin panel |

### 1.6 Security Controls — Parcial
| Deliverable | Estado |
|---|---|
| PSP webhook signature validation | **✅ Completo** — HMAC signature + idempotency via webhook_events |
| Webhook idempotency table | **✅ Completo** — webhook_events table |
| Ledger entries (double-entry) | **✅ Completo** — ledger_entries table |
| Balance CHECK constraints | **✅ Completo** — `available >= 0`, `locked >= 0` en schema |
| Auth rate limiting | **✅ Completo** — 10/min en login/register/verify, 30/min en nonce/refresh |
| Auth dual CSRF protection | **N/A** — App usa JWT Bearer tokens (no cookies), CSRF no aplica. Google OAuth usa nonce + audience verification |

### 1.7 Admin Panel — Parcial (basic version exists)
| Deliverable | Estado |
|---|---|
| Admin app (Vite + React) | **✅ Completo** — admin/ repo, deployed to Vercel |
| Admin auth (email+password + Google OAuth) | **✅ Completo** — separate admin_users table |
| RBAC system | **Parcial** — 3 of 6 roles implemented (super_admin, admin, editor) |
| Dashboard with platform stats | **✅ Completo** |
| Market management UI (CRUD + status + resolution) | **✅ Completo** |
| User listing | **✅ Completo** |
| KYC review UI (approve/reject) | **✅ Completo** |
| **Payment/withdrawal approval UI** | **Falta** |
| **Audit log** | **Falta** |
| **2FA for sensitive roles** | **Falta** |

---

## Bugs encontrados en código existente

| Bug | Severidad | Archivo | Detalle |
|---|---|---|---|
| ~~`users.address` es NOT NULL~~ | ~~CRÍTICO~~ | — | ✅ RESUELTO 12-abr — `address` ahora nullable |
| ~~Matching engine no verifica balance~~ | ~~CRÍTICO~~ | — | ✅ RESUELTO 12-abr — `lockFunds` en `createOrder`, `unlockFunds` en `cancelOrder` |
| ~~Matching engine no usa transacción DB~~ | ~~ALTO~~ | — | ✅ RESUELTO 12-abr — `matchOrders` envuelto en `db.transaction()` |
| ~~Self-trade prevention bloquea book~~ | ~~MEDIO~~ | — | ✅ RESUELTO 12-abr — ahora hace `break` (skip) en vez de detener el loop |
| ~~Faltan tablas en Drizzle schema~~ | ~~ALTO~~ | — | ✅ PARCIAL 12-abr — Agregadas: `user_balances`, `ledger_entries`, `payment_transactions`, `webhook_events`. Faltan: `brand_configs` |
| ~~`users.email` no tiene UNIQUE constraint~~ | ~~ALTO~~ | — | ✅ RESUELTO — `.unique()` en schema |
| ~~Zero tests de backend~~ | ~~ALTO~~ | — | ✅ PARCIAL 12-abr — Vitest configurado, 16 tests passing (4 archivos) |
| ~~No CHECK constraints en user_balances~~ | ~~ALTO~~ | — | ✅ RESUELTO — `available >= 0`, `locked >= 0` |
| ~~No CSRF protection en OAuth flows~~ | ~~MEDIO~~ | — | N/A — JWT Bearer tokens, no cookies. CSRF no aplica |
| ~~Settlement service sin adapter~~ | ~~MEDIO~~ | — | ✅ RESUELTO 12-abr — Adapter pattern implementado, `settlementStatus` en schema, 11 tests. Pendiente: activar blockchain path (B1.2+) |

---

## Resumen de Gaps Críticos (ordenados por prioridad)

### P0 — Bloqueantes antes de mainnet (money-losing si faltan)
1. ~~**Balance Service + verificación en matching engine**~~ — ✅ RESUELTO 12-abr
2. ~~**`users.address` nullable**~~ — ✅ RESUELTO 12-abr
3. ~~**PSP webhook signature validation + idempotency**~~ — ✅ RESUELTO 12-abr
4. **Settlement Service** — En progreso: adapter pattern DONE, mock protegido con tests. Falta: deploy contracts + activar blockchain path
5. **On-chain Event Indexer** — Implementado (`indexer.service.ts`), pendiente activación con `BLOCKCHAIN_ENABLED=true`
6. **Principio "Forka nunca toca fiat"** — Documentar y enforcer que solo flujo PSP→USDC. Currencies: NO USD, NO BRL, solo EUR vía PSP
7. ~~**Ledger entries (double-entry bookkeeping)**~~ — ✅ RESUELTO 12-abr

### P1 — Necesarios antes de mainnet
8. ~~**Payment Service completo**~~ — ✅ RESUELTO 12-abr
9. ~~**Auth dual (email/Google + SIWE)**~~ — ✅ RESUELTO — Endpoints, `user_auth_methods`, account linking
10. ~~**Admin Panel con RBAC**~~ — ✅ PARCIAL — Admin app exists with 3 roles (super_admin, admin, editor). Missing: 3 additional roles, 2FA, audit log
11. ~~**Security Controls**~~ — ✅ RESUELTO — CHECK constraints ✅, rate limiting auth ✅, CSRF N/A (JWT Bearer, no cookies)
12. **Sumsub KYC real** — Parcial: webhook exists, full flow needed for compliance
13. **Geo-blocking** — Requerido por compliance
14. ~~**Background Workers (BullMQ)**~~ — ✅ RESUELTO — `settlement.worker.ts` con settle-trade, settle-batch, resolve-market, finalize-resolution
15. ~~**Webhook Processing**~~ — ✅ RESUELTO — Processed synchronously with idempotency
16. **Chargeback protection** — 3DS obligatorio, período retención 14 días, clausula ToS

### P2 — Necesarios para launch
17. ~~**TradingView Charts**~~ — ✅ RESUELTO — `price-chart.tsx` completo con area/candlestick/volume
18. ~~**WebSocket real-time**~~ — ✅ RESUELTO — socket.io-client + broadcasts en matching engine
19. **Frontend: BrandProvider + useCurrency** — Multibranding UI (FundingModal ya existe como DepositModal)
20. **UX flows fiat** — Flow 1B onboarding fiat, Flow 5A depósito fiat, error states
21. **CI/CD Pipeline** — GitHub Actions
22. **E2E Tests (Playwright)** — Al menos fiat onboarding + wallet onboarding + trade
23. **Vitest unit tests** — 16 tests passing (settlement mock, stuck TX, reconciliation). Falta: balance, webhooks, matching
24. **Vocabulary guidelines** — Sección 3.6 en brand identity: DO/DON'T de términos crypto
25. **Costos fiat en business model** — Sección 2.8: PSP fees, chargeback reserve, fraud monitoring

### P3 — Nice to have para MVP, necesarios para scale
26. **Proxy Wallet + Gas Abstraction (ERC-4337)** — Phase 2. AWS KMS para key management ($5-20/mes)
27. **Multibranding activo** — Arquitectura preparada, solo una marca en MVP
28. **Data feed comercial** — Phase 3. Empezar a capturar tick data + depth desde Phase 1
29. **Regulación fiat** — Sección 9 en doc legal: money transmission, chargebacks, currencies
30. **Segmentación analytics crypto/fiat** — Métricas separadas por origen de usuario
31. **Load testing (k6)** — Balance race conditions bajo carga, webhook throughput
32. **SDK package** — Phase 3
33. **Slither analysis** — Pre-auditoría
34. **Quick bet mode** — UX simplificada Sí/No + monto para fiat users (propuesta Marketing)

### New Gaps (identified post-audit)
35. **market_tags table needs migration docs** — Table exists but migration not documented
36. **Mercado Pago webhook secret needs documentation** — Secret configuration undocumented
