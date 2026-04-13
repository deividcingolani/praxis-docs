# Forka — Product Backlog

> Actualizado: 12 abril 2026
> Sprint actual: Sprint 2 (14–25 abril)
> Objetivo del sprint: **Cerrar los bugs que pierden plata y habilitar el flujo de fondos**

---

## DONE

| # | Item | P | Esfuerzo | Notas |
|---|------|---|----------|-------|
| D1 | Estructura legal + jurisdicciones | P0 | L | 5 jurisdicciones analizadas, KYC tiers, AML, ToS |
| D2 | Business model + unit economics | P0 | L | Fee structure, proyecciones 24 meses |
| D3 | Brand identity (Forka) | P1 | M | Guidelines, GTM, personas, multi-branding config, i18n (EN/ES/PT) |
| D4 | Arquitectura técnica + data model | P0 | L | Diagramas, API design, infra |
| D5 | Smart contracts (CTF + Exchange + Oracle) | P0 | XL | 27/27 tests passing |
| D6 | Auth Service (SIWE + JWT) | P0 | M | |
| D7 | Market Service (CRUD, búsqueda, estados) | P0 | M | |
| D8 | Order Service + Matching Engine | P0 | XL | In-memory, price-time priority |
| D9 | Trade / Position / Price Services | P1 | L | |
| D10 | WebSocket Service (estructura) | P1 | S | Socket.io, sin testing real |
| D11 | KYC Service (mock, 3 tiers) | P1 | M | |
| D12 | Frontend: Design system (9 componentes) | P1 | L | |
| D13 | Frontend: Layout, wallet auth, explorer | P0 | L | |
| D14 | Frontend: Market detail + trading panel | P0 | L | |
| D15 | Frontend: Portfolio + KYC + legal pages | P1 | M | |
| D16 | Backend deploy (Railway) | P0 | S | Health OK, DB + Redis connected |
| D17 | Frontend deploy (Vercel) | P0 | S | Connected to backend, forka.io |
| D18 | DB schema pushed to production | P0 | XS | |
| D19 | `users.address` nullable | P0 | XS | Fiat-only users desbloqueados |
| D20 | `BLOCKCHAIN_ENABLED` flag | P1 | XS | Blockchain features off hasta deploy |
| D21 | Balance Service + `user_balances` + `ledger_entries` | P0 | M | SELECT FOR UPDATE, double-entry audit trail |
| D22 | Balance verification en matching engine | P0 | S | lockFunds en createOrder, unlockFunds en cancelOrder |
| D23 | Matching engine DB transaction wrapping | P0 | S | Atomicidad en matchOrders |
| D24 | Self-trade prevention fix | P0 | XS | Skip pair en vez de bloquear book |
| D25 | Auth dual (email/password + Google OAuth) | P1 | L | user_auth_methods table, account linking |
| D26 | Frontend AuthModal dual | P1 | M | Onboarding email/Google + wallet |
| D27 | Admin Panel (Vite + React) | P1 | XL | Dashboard, market CRUD, user listing, KYC review |
| D28 | Admin auth (email+password + Google OAuth) | P1 | M | Separate admin_users table |
| D29 | PSP Integration: MoonPay browser SDK | P0 | L | Signed URLs, Card/Apple Pay/Google Pay |
| D30 | PSP Integration: Mercado Pago Checkout Pro | P0 | L | LATAM fiat onramp |
| D31 | Deposit limits (KYC-tier-based) | P0 | M | tier_0=$100 lifetime, tier_1=$10K/month, tier_2=unlimited |
| D32 | Session persistence | P1 | S | |
| D33 | market_tags table | P1 | S | Market categorization |
| D34 | PSP webhook endpoints + signature validation | P0 | M | /webhooks/moonpay + HMAC + idempotency (synchronous processing) |

---

## IN PROGRESS

| # | Item | P | Esfuerzo | Owner | Deps | Notas |
|---|------|---|----------|-------|------|-------|
| W2 | Smart contract deploy a Polygon Amoy | P1 | S | — | POL faucet | Se resuelve ~13 abril |

---

## TODO — Sprint actual (14–25 abril)

> Criterio: lo que pierde plata o bloquea el primer depósito. Max 6 items.

| # | Item | P | Esfuerzo | Deps | Por qué ahora |
|---|------|---|----------|------|---------------|
| ~~S1~~ | ~~Balance verification en matching engine~~ | P0 | S | — | ✅ DONE |
| ~~S2~~ | ~~Matching engine: DB transaction wrapping~~ | P0 | S | — | ✅ DONE |
| ~~S3~~ | ~~Self-trade prevention fix~~ | P0 | XS | — | ✅ DONE |
| ~~S4~~ | ~~Payment Service (deposit flow)~~ | P0 | L | — | ✅ DONE — deposit/withdraw + payment_transactions table |
| ~~S5~~ | ~~PSP webhook endpoints + signature validation~~ | P0 | M | — | ✅ DONE — /webhooks/moonpay + HMAC + idempotency |
| ~~S6~~ | ~~Ledger entries (double-entry bookkeeping)~~ | P0 | M | — | ✅ DONE — Implementado con Balance Service (ledger_entries table) |

### Notas del sprint
- S1, S2, S3 son bug fixes — se pueden hacer en paralelo, idealmente los primeros 2 días
- S4 y S5 van juntos — el Payment Service necesita los webhooks para confirmar depósitos
- S6 se puede empezar una vez que W1 esté merged
- Al final del sprint: un usuario puede depositar fondos (vía crypto), crear órdenes con balance real, y cada movimiento queda en el ledger

---

## BACKLOG — Próximos sprints

### Sprint 3 — On-chain settlement + security (estimado)

| # | Item | P | Esfuerzo | Deps | Notas |
|---|------|---|----------|------|-------|
| B1 | Settlement Service + Blockchain TX Manager | P0 | L | W2 | Nonce mgmt, gas estimation, retry logic |
| B2 | On-chain Event Indexer | P0 | M | W2 | Escuchar eventos de contratos, sync blockchain ↔ DB |
| B5 | Campos faltantes en `users` table | P1 | XS | — | email UNIQUE constraint |
| B6 | Background Workers (BullMQ) | P1 | M | B1 | Settlement worker |
| B7 | Security controls (CHECK constraints, rate limiting) | P1 | M | — | Balance CHECK, rate limiting por endpoint, CSRF en OAuth |

### Sprint 4 — Compliance + admin improvements

| # | Item | P | Esfuerzo | Deps | Notas |
|---|------|---|----------|------|-------|
| B8 | Sumsub KYC real (full flow) | P1 | M | — | Webhook exists, complete integration needed |
| B9 | Geo-blocking middleware | P1 | S | B8 | Requerido por compliance |
| B10 | Admin Panel: additional RBAC roles | P1 | M | — | Add Compliance Officer, Finance, Support, Viewer roles |
| B11 | Admin Panel: Payment/withdrawal approval UI | P1 | M | — | Retiros con approval queue, período retención 14 días |
| B12 | Chargeback protection | P1 | S | — | 3DS obligatorio, clausula ToS |
| B34 | Intercom Identity Verification (JWT) | P2 | S | — | Generar JWT en backend con secret Intercom, pasar al widget |
| B35 | Admin Panel: Audit log | P1 | M | — | Registro inmutable de acciones admin |
| B36 | market_tags migration docs | P2 | XS | — | Document existing migration |
| B37 | Mercado Pago webhook secret docs | P2 | XS | — | Document secret configuration |

### Sprint 5 — UX + testing + launch prep

| # | Item | P | Esfuerzo | Deps | Notas |
|---|------|---|----------|------|-------|
| B13 | TradingView Charts | P2 | M | — | `lightweight-charts` integration |
| B14 | WebSocket real-time updates | P2 | M | — | Conectar hooks existentes |
| B15 | UX flows fiat (onboarding + depósito + errors) | P2 | M | — | Flow 1B, Flow 5A, error states |
| B16 | CI/CD Pipeline (GitHub Actions) | P2 | M | — | CI en push, deploy manual |
| B17 | Vitest setup + unit tests backend | P2 | L | — | Prioridad: balance, webhooks, matching |
| B18 | E2E Tests (Playwright) | P2 | L | B16 | Fiat onboarding + wallet + trade |

### Icebox — P3 (post-launch / scale)

| # | Item | P | Esfuerzo | Notas |
|---|------|---|----------|-------|
| B19 | Proxy Wallet + Gas Abstraction (ERC-4337) | P3 | XL | Phase 2. AWS KMS |
| B20 | Multibranding activo | P3 | L | Arquitectura lista, solo 1 marca en MVP |
| B21 | SDK package | P3 | L | Phase 3 |
| B22 | Data feed comercial | P3 | M | Capturar tick data + depth |
| B23 | Load testing (k6) | P3 | M | Race conditions bajo carga |
| B24 | Slither analysis (contratos) | P3 | S | Pre-auditoría |
| B25 | Quick bet mode | P3 | M | UX simplificada para fiat users |
| B26 | Vocabulary guidelines (DO/DON'T crypto) | P3 | XS | Copywriting |
| B27 | Costos fiat en business model | P3 | S | Sección 2.8 |
| B28 | Regulación fiat (sección legal) | P3 | M | Sección 9 |
| B29 | Analytics segmentación crypto/fiat | P3 | S | Métricas por origen |
| B30 | Order book click-to-fill | P3 | S | UX improvement |
| B31 | Mobile responsive testing | P3 | S | Classes existen, falta validar |
| B33 | BrandProvider + useCurrency (frontend) | P3 | M | Abstracción moneda + multibranding |

---

## Decisiones de priorización

### Por qué el sprint actual se enfoca en fondos y no en features
La audiencia viene de Blaze — entienden apuestas, esperan poder depositar y operar desde el día 1. Si el exchange permite operar sin fondos (bug actual) o si los depósitos no funcionan, no hay producto. Cada día con el matching engine sin balance verification es un riesgo existencial.

### Por qué Auth dual no está en este sprint
SIWE funciona. Los early adopters crypto pueden operar. Auth dual (email/Google) es P1 porque desbloquea la audiencia fiat de Blaze, pero sin el flujo de fondos resuelto, no importa cómo se autentiquen — no pueden hacer nada útil. **UPDATE: Auth dual now DONE.**

### Por qué tests no están en este sprint
Zero tests es un riesgo alto pero no un blocker de funcionalidad. Los tests de balance y webhooks son más valiosos cuando el Balance Service y Payment Service existan. Sprint 5 tiene un bloque dedicado a testing.

### Por qué Admin Panel está en Sprint 4
No necesitás admin panel para el testnet. Lo necesitás para mainnet cuando haya usuarios reales, KYC reviews, y pagos que aprobar. **UPDATE: Basic admin panel now DONE (D27). Sprint 4 items are enhancements (additional roles, audit log, payment approval).**

---

## Métricas de éxito por sprint

| Sprint | Outcome esperado |
|--------|-----------------|
| Sprint 2 (actual) | Un usuario puede depositar crypto, crear órdenes con balance real, y cada movimiento queda en el ledger |
| Sprint 3 | Trades se liquidan on-chain. Security controls implementados |
| Sprint 4 | KYC real, geo-blocking, admin enhancements (more roles, audit log, payment approval) |
| Sprint 5 | Plataforma lista para beta cerrada con charts, real-time, y test coverage mínimo |
