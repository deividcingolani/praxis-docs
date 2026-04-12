# Plan: Plataforma de Prediction Markets (tipo Polymarket)

## Contexto

Construir desde cero una plataforma de prediction markets similar a Polymarket. El proyecto está vacío. Se cuenta con 7 skills especializadas como equipo virtual: tech-lead, backend-dev, frontend-dev, finanzas, legal, ux-designer, marketing.

Polymarket usa: Polygon + Gnosis CTF + CLOB híbrido (órdenes off-chain, settlement on-chain) + UMA Oracle + USDC.

---

## Stack Tecnológico Recomendado

| Capa | Tecnología | Justificación |
|---|---|---|
| Blockchain | Polygon PoS | Bajo gas, rápido, probado por Polymarket |
| Smart Contracts | Solidity 0.8.x + Foundry | Foundry es lo que usa Polymarket, mejor testing |
| Tokens | ERC-1155 (Gnosis CTF) | Estándar probado en producción |
| Collateral | USDC | Stablecoin estándar, alta liquidez |
| Oracle | UMA Optimistic Oracle V3 | Sistema de disputa robusto |
| Backend | Node.js + TypeScript + Fastify | 2x más rápido que Express, type-safe |
| Base de datos | PostgreSQL 16 + Prisma | ACID para datos financieros |
| Cache/PubSub | Redis 7 + BullMQ | Order book, real-time, jobs |
| Frontend | Next.js 15 + TypeScript | SSR para SEO, App Router |
| Charts | TradingView Lightweight Charts | El mismo que usa Polymarket |
| UI | Tailwind CSS + Radix UI | Accesible, performante |
| Wallet | wagmi v2 + viem + RainbowKit | Estándar Web3 para React |
| State | Zustand + TanStack Query | Ligero, sin boilerplate |

### Estructura del monorepo

```
polymarket/
  packages/
    contracts/        -- Foundry: smart contracts Solidity
    backend/          -- Fastify API server
    frontend/         -- Next.js web app
    shared/           -- Tipos TS compartidos, ABIs
    sdk/              -- SDK cliente para terceros
  docs/               -- Arquitectura, API specs
  scripts/            -- Deploy, migraciones, seeds
```

---

## Phase 0: Foundation (Semanas 1-4)

### 0.1 Estructura Legal y Jurisdicción — `legal` (3 sem)
- Análisis jurisdiccional: Panamá, BVI, Malta, Curazao
- Estructura corporativa recomendada
- Matriz de riesgo regulatorio (CFTC, MiCA, FCA)
- Framework de ToS y Privacy Policy
- Requisitos de geo-blocking
- Estructura de KYC por tiers: sin KYC (browse) → básico ($10K/mes) → full (sin límite)

### 0.2 Modelo de Negocio — `finanzas` (2 sem) → depende de 0.1
- Estructura de fees (1-2% sobre posiciones ganadoras)
- Unit economics con 3 escenarios
- Proyección financiera a 18 meses
- Estrategia de fundraising
- Plan de treasury management
- Análisis competitivo: Polymarket, Kalshi, Azuro, Augur

### 0.3 Identidad de Marca — `marketing` + `ux-designer` (2 sem)
- Nombre, dominio, redes sociales
- Brand guidelines (logo, colores, tipografía, tono)
- Posicionamiento y propuesta de valor
- Estrategia de comunidad pre-launch (Discord, Twitter/X)

### 0.4 Arquitectura Técnica — `tech-lead` (2 sem, en paralelo)
- Documento de arquitectura con diagramas
- Diseño de data model (PostgreSQL)
- Arquitectura de smart contracts
- Diseño de API (REST + WebSocket)
- Setup del monorepo
- Pipeline CI/CD

---

## Phase 1: MVP Core (Semanas 5-18)

### 1.1 Smart Contracts — `tech-lead` + `backend-dev` (5 sem) → depende de 0.4
- **ConditionalTokens.sol**: Fork/integración de Gnosis CTF (ERC-1155)
- **CTFExchange.sol**: Exchange con órdenes firmadas EIP-712, matching atómico, fees, cancelación
- **UMACTFAdapter.sol**: Conexión UMA OO V3 → CTF para resolución
- Deploy scripts: Mumbai testnet → Polygon mainnet
- 100% test coverage + fuzz tests + invariant tests + Slither

### 1.2 Backend Services — `backend-dev` (6 sem) → depende de 0.4, 1.1
- **User Service** (sem 5-6): Auth JWT + SIWE, wallet, KYC stub
- **Market Service** (sem 6-8): CRUD mercados, estados, categorías, búsqueda full-text
- **Order Service + Matching Engine** (sem 7-10): Order book in-memory + Redis, matching price-time priority, EIP-712, WebSockets
- **Settlement Service** (sem 10-12): Tx manager (nonce, gas, retry), eventos on-chain, resolución
- **Price Service** (sem 8-10): Mid-price, OHLCV candles, broadcast WebSocket
- **API Gateway**: OpenAPI docs, validación, CORS, rate limiting, logging (Pino)

### 1.3 Frontend — `frontend-dev` (6 sem) → depende de 0.4, 1.2, 0.3
- **Design System** (sem 7-8): Tokens, componentes core, layout, dark mode
- **Wallet + Auth** (sem 8-9): RainbowKit, SIWE, USDC balance, approval flow
- **Market Explorer** (sem 9-10): Grid de mercados, filtros, búsqueda, sort, infinite scroll
- **Market Detail** (sem 10-12): TradingView charts, order book visual, trade history
- **Trading Panel** (sem 10-12): Buy YES/NO, limit/market, confirmación, firma EIP-712
- **Portfolio** (sem 11-13): Posiciones, P&L, órdenes abiertas, historial

### 1.4 UX Design — `ux-designer` (4 sem, overlap con 1.3)
- User flows para onboarding, trading, portfolio
- Wireframes por página antes de implementación
- Specs de interacción: loading, error, empty states
- Layouts responsive mobile-first
- Target: < 3 minutos de landing a primer trade

### 1.5 KYC Flow — `backend-dev` + `legal` (2 sem) → depende de 1.2.1, 0.1
- Integración Sumsub (KYC provider)
- 3 tiers de verificación
- Geo-blocking por IP + país KYC
- Panel admin para revisión manual

**Milestone: Testnet launch semana 14, Mainnet MVP semana 18**

---

## Phase 2: Growth (Semanas 19-30)

### 2.1 Mercados Multi-Outcome — `tech-lead` + `backend-dev` (4 sem)
- NegRiskAdapter.sol + NegRiskCTFExchange.sol
- Soporte backend para creación y settlement multi-outcome
- UI: barras de probabilidad, trading por outcome

### 2.2 Trading Avanzado — `backend-dev` + `frontend-dev` (3 sem)
- Batch orders (hasta 15 por request)
- GTC y GTD order types
- Notificaciones (in-app + email)
- Price alerts
- Charts avanzados: múltiples timeframes, volumen

### 2.3 Mobile — `frontend-dev` + `ux-designer` (3 sem)
- PWA con install prompt y offline browsing
- Trading panel optimizado (bottom sheet, touch targets grandes)
- Push notifications via service worker
- Target: < 3s FCP en 4G

### 2.4 Social y Comunidad — `frontend-dev` + `backend-dev` (3 sem)
- Comentarios en mercados
- Perfiles públicos (opt-in)
- Leaderboard (ROI, volumen, streaks)
- Social sharing con Open Graph auto-generado
- Embed widget (iframe)
- Sistema de referidos

### 2.5 Marketing Launch — `marketing` (4 sem, en paralelo)
- Campaña de lanzamiento Twitter/X + Discord
- Content calendar (3 posts/semana)
- Outreach a 20+ influencers crypto
- Programa de referidos con incentivos
- PR: TechCrunch, CoinDesk, The Block
- Torneo testnet con premios → launch mainnet coordinado

---

## Phase 3: Scale (Semanas 31-42)

### 3.1 Performance — `tech-lead` + `backend-dev` (4 sem)
- Extraer matching engine a servicio dedicado (Rust o worker optimizado)
- Read replicas PostgreSQL
- Redis Cluster para WebSocket pub/sub
- CDN optimization
- Target: 1000 orders/seg, < 50ms P99

### 3.2 Analytics Avanzados — `backend-dev` + `frontend-dev` (3 sem)
- Dashboard de mercados: volumen, traders, liquidez
- Analytics de portfolio: rendimiento, win rate
- Admin dashboard: GMV, revenue, retención
- API de datos históricos

### 3.3 API Pública — `tech-lead` + `backend-dev` (3 sem)
- REST API con API keys (free: 100 req/min, paid: 1000 req/min)
- WebSocket API para datos real-time
- SDK TypeScript (npm) + Python (PyPI)
- Documentación con Mintlify
- Rate limiting por API key

### 3.4 Tipos de Mercado Adicionales — `tech-lead` + `backend-dev` (4 sem)
- Scalar/range markets (ej: "¿A cuánto cierra BTC?")
- Mercados combinatoriales (A dado B)
- Creación de mercados por usuarios (con depósito + aprobación)
- Templates recurrentes

### 3.5 Security Hardening — `tech-lead` (3 sem, en paralelo)
- Completar auditoría de smart contracts
- Bug bounty en Immunefi
- Penetration testing de APIs
- Playbook de incidentes
- Circuit breaker (pausar trading en emergencia)
- Multi-sig para admin y treasury

---

## Registro de Riesgos

| Riesgo | Prob. | Impacto | Mitigación |
|---|---|---|---|
| Exploit de smart contract | Media | Crítico | Auditoría, bug bounty, circuit breaker, caps de TVL |
| Acción regulatoria | Media | Alto | Entidad offshore, geo-blocking, abogado en retainer |
| Baja liquidez / order books vacíos | Alta | Alto | Capital propio, partnerships con market makers |
| Manipulación de oracle | Baja | Crítico | Mecanismo de disputa UMA, override manual |
| Dependencia de persona clave | Media | Medio | Documentación, code reviews |
| Congestión de Polygon | Baja | Medio | Retry logic, degradación graceful |

---

## Timeline Total

| Fase | Duración | Milestone |
|---|---|---|
| Phase 0: Foundation | Semanas 1-4 | Arquitectura + legal + marca |
| Phase 1: MVP | Semanas 5-18 | Testnet (sem 14), Mainnet MVP (sem 18) |
| Phase 2: Growth | Semanas 19-30 | Multi-outcome + mobile + social + launch |
| Phase 3: Scale | Semanas 31-42 | API pública + markets avanzados + performance |

**Total: ~42 semanas (~10.5 meses) de kickoff a plataforma completa.**

---

## Archivos Críticos a Crear Primero

- `packages/contracts/src/CTFExchange.sol` — Contrato core de exchange
- `packages/backend/src/services/order-matching-engine.ts` — Motor de matching (más crítico para performance)
- `packages/backend/src/services/settlement-service.ts` — Puente entre matching off-chain y settlement on-chain
- `packages/shared/src/types/index.ts` — Tipos compartidos que fuerzan consistencia
- `packages/frontend/src/app/markets/[id]/page.tsx` — Página de detalle de mercado (más tráfico)

## Verificación End-to-End

1. **Contracts**: Fuzz tests, invariant tests, Slither, auditoría externa
2. **Backend**: Unit tests matching engine, integration tests ciclo completo de orden, load test 100 ord/seg
3. **Frontend**: Playwright E2E (wallet → browse → trade → portfolio), Lighthouse > 90
4. **Full flow testnet**: Crear mercado → tradear → resolver con UMA → cobrar payout
