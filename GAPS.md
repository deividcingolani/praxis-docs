# Gaps & Pendientes — Phase 0 y Phase 1

Lo que se salteó o quedó incompleto de las fases 0 y 1.
Actualizado con feedback del equipo completo (12 especialistas) — Abril 2026.

---

## Phase 0: Foundation

| Deliverable | Estado | Detalle |
|---|---|---|
| 0.1 Estructura Legal | **Completo** | Documento con análisis de 5 jurisdicciones, KYC tiers, AML, ToS, geo-blocking |
| 0.2 Modelo de Negocio | **Completo** | Fee structure, unit economics, proyecciones 24 meses, fundraising |
| 0.3 Identidad de Marca | **Completo** | 5 nombres propuestos (Praxis recomendado), brand guidelines, GTM |
| 0.4 Arquitectura Técnica | **Completo** | Diagramas Mermaid, data model, API design, infra |
| 0.4 CI/CD Pipeline | **Salteado** | Diseñado en el doc pero no se creó `.github/workflows/` |
| 0.4 Monorepo `sdk/` package | **Salteado** | Directorio `packages/sdk/` planeado pero no creado |
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
| Market Service (CRUD, búsqueda, estados) | Completo | |
| Order Service + Matching Engine | Completo | In-memory, price-time priority |
| Trade Service | Completo | |
| Position Service (P&L) | Completo | |
| Price Service (OHLCV candles) | Completo | |
| WebSocket Service (Socket.io) | Completo | Estructura lista, falta testing real |
| KYC Service | Completo | Mock mode, sin Sumsub real |
| API Routes (auth, markets, orders, trades, positions, prices, kyc) | Completo | |
| Middleware (auth, validation, kyc) | Completo | |
| **Settlement Service** | **Salteado** | No hay worker que envíe txs on-chain |
| **Blockchain Transaction Manager** | **Salteado** | Nonce management, gas estimation, retry logic |
| **Background Workers (BullMQ)** | **Salteado** | BullMQ en deps pero no hay workers |
| **On-chain event listener / Indexer** | **Salteado** | No escucha eventos del contrato |
| **Rate limiting por endpoint** | **Parcial** | Global (100 req/min) existe, falta por endpoint |
| ~~**Payment Service**~~ | **✅ Completo** | Deposit/withdraw flows, payment_transactions table, PaymentProvider support |
| ~~**Balance Service**~~ | **✅ Completo** | user_balances (available+locked), SELECT FOR UPDATE, ledger_entries |
| **Auth dual (email/Google)** | **Salteado** | Solo SIWE. No hay email/password ni OAuth Google |
| ~~**Webhook endpoints PSP**~~ | **✅ Completo** | /webhooks/moonpay + /webhooks/transak + HMAC signature + idempotency |

### 1.3 Frontend — Parcial
| Deliverable | Estado | Notas |
|---|---|---|
| Design System (9 componentes UI) | Completo | button, input, modal, card, badge, skeleton, tabs, toast, spinner |
| Layout (header, footer) | Completo | Con wallet connect, nav, search |
| Wallet + Auth (RainbowKit, SIWE) | Completo | |
| Market Explorer page | Completo | Con filtros, búsqueda, sort, mock data |
| Market Detail page | Completo | Con order book, trading panel, trade history |
| Trading Panel | Completo | Buy YES/NO, limit/market, confirmación |
| Portfolio page | Completo | Posiciones, órdenes, historial, P&L |
| KYC page (3 pasos) | Completo | Con banner contextual |
| Legal pages (Terms, Privacy) | Completo | |
| Docs pages (4 guías) | Completo | |
| Mock data (10 mercados) | Completo | |
| **TradingView Charts** | **Salteado** | Placeholder div, no se integró `lightweight-charts` |
| **Order book click-to-fill** | **Salteado** | |
| **WebSocket real-time updates** | **Salteado** | Hooks existen pero no conectados |
| **Mobile responsive** | **Parcial** | Classes existen, no testeado |
| **E2E Tests (Playwright)** | **Salteado** | |
| **AuthModal dual** | **Salteado** | No hay componente de registro email/Google ni FundingModal para PSP |
| **BrandProvider + useCurrency** | **Salteado** | No hay abstracción de moneda ni multibranding en frontend |

### 1.4 UX Design — Parcial
| Deliverable | Estado |
|---|---|
| User flows (5 flujos con Mermaid) | Completo |
| Interaction specs | Completo |
| Responsive layout specs | Completo |
| Accessibility checklist | Completo |
| **Flow 1B: Onboarding fiat** | **Falta** | Propuesto por UX: flujo email/Google → depósito tarjeta → primer trade |
| **Flow 5A: Depósito fiat** | **Falta** | Propuesto por UX: selección método → monto → PSP iframe → confirmación |
| **Error states fiat** | **Falta** | Card declined, PSP timeout, KYC required, deposit limits |

### 1.5 KYC Flow — Parcial
| Deliverable | Estado | Notas |
|---|---|---|
| KYC Service (3 tiers, limits) | Completo | Mock mode |
| KYC Routes | Completo | |
| KYC Middleware (requireKyc) | Completo | |
| Trading limits enforcement | Completo | Integrado en order creation |
| KYC Frontend page | Completo | 3-step wizard |
| KYC Banner component | Completo | |
| **Sumsub integration** | **Salteado** | Sin integración con proveedor KYC real |
| **Geo-blocking middleware** | **Salteado** | |
| **Admin panel para KYC review** | **Salteado** | Solo endpoints, no UI |

### 1.6 Security Controls — No implementado
| Deliverable | Estado |
|---|---|
| **PSP webhook signature validation** | **Falta** — CRÍTICO |
| **Webhook idempotency table** | **Falta** — CRÍTICO |
| **Balance CHECK constraints** | **Falta** — CRÍTICO |
| **Ledger entries (double-entry)** | **Falta** |
| **Auth dual CSRF protection** | **Falta** |

### 1.7 Admin Panel — No implementado
| Deliverable | Estado |
|---|---|
| **Admin app (repo separado)** | **Falta** — No existe |
| **RBAC system** | **Falta** — No hay roles definidos en código |
| **Admin auth (email+password+Google+2FA)** | **Falta** |
| **Market management UI** | **Falta** |
| **KYC review UI** | **Falta** |
| **Payment/withdrawal approval UI** | **Falta** |
| **Audit log** | **Falta** |

---

## Bugs encontrados en código existente

| Bug | Severidad | Archivo | Detalle |
|---|---|---|---|
| ~~`users.address` es NOT NULL~~ | ~~CRÍTICO~~ | — | ✅ RESUELTO 12-abr — `address` ahora nullable |
| ~~Matching engine no verifica balance~~ | ~~CRÍTICO~~ | — | ✅ RESUELTO 12-abr — `lockFunds` en `createOrder`, `unlockFunds` en `cancelOrder` |
| ~~Matching engine no usa transacción DB~~ | ~~ALTO~~ | — | ✅ RESUELTO 12-abr — `matchOrders` envuelto en `db.transaction()` |
| ~~Self-trade prevention bloquea book~~ | ~~MEDIO~~ | — | ✅ RESUELTO 12-abr — ahora hace `break` (skip) en vez de detener el loop |
| ~~Faltan tablas en Drizzle schema~~ | ~~ALTO~~ | — | ✅ PARCIAL 12-abr — Agregadas: `user_balances`, `ledger_entries`, `payment_transactions`, `webhook_events`. Faltan: `brand_configs` |
| Faltan campos en users | **ALTO** | `backend/src/db/schema.ts` | Falta `auth_method`, `brand_id`, `google_id`. Email no tiene UNIQUE |
| Zero tests de backend | **ALTO** | — | No hay Vitest configurado, no hay un solo `.test.ts` |

---

## Resumen de Gaps Críticos (ordenados por prioridad)

### P0 — Bloqueantes antes de mainnet (money-losing si faltan)
1. ~~**Balance Service + verificación en matching engine**~~ — ✅ RESUELTO 12-abr
2. ~~**`users.address` nullable**~~ — ✅ RESUELTO 12-abr
3. ~~**PSP webhook signature validation + idempotency**~~ — ✅ RESUELTO 12-abr
4. **Settlement Service + Blockchain TX Manager** — Trades no se liquidan on-chain
5. **On-chain Event Indexer** — No se sincroniza blockchain ↔ DB
6. **Principio "Praxis nunca toca fiat"** — Documentar y enforcer que solo flujo PSP→USDC. Currencies: NO USD, NO BRL, solo EUR vía PSP
7. ~~**Ledger entries (double-entry bookkeeping)**~~ — ✅ RESUELTO 12-abr

### P1 — Necesarios antes de mainnet
8. ~~**Payment Service completo**~~ — ✅ RESUELTO 12-abr
9. **Auth dual (email/Google + SIWE)** — Endpoints, tabla `user_auth_methods`, account linking
10. **Admin Panel con RBAC** — Repo separado, auth email+password+Google+2FA, roles (Super Admin, Market Manager, Compliance, Finance, Support, Viewer)
11. **Security Controls** — CHECK constraints en balances, CSRF en OAuth, rate limiting auth email
12. **Sumsub KYC real** — Necesario para compliance
13. **Geo-blocking** — Requerido por compliance
14. **Background Workers (BullMQ)** — Settlement, webhook processing, reconciliation, resolution
15. **Webhook Processing Worker** — Procesa webhooks PSP con reintentos y DLQ
16. **Chargeback protection** — 3DS obligatorio, período retención 14 días, clausula ToS

### P2 — Necesarios para launch
17. **TradingView Charts** — Core UX de trading
18. **WebSocket real-time** — Hooks existen pero sin conexión
19. **Frontend: AuthModal + FundingModal + BrandProvider + useCurrency** — Onboarding dual completo
20. **UX flows fiat** — Flow 1B onboarding fiat, Flow 5A depósito fiat, error states
21. **CI/CD Pipeline** — GitHub Actions
22. **E2E Tests (Playwright)** — Al menos fiat onboarding + wallet onboarding + trade
23. **Vitest setup + unit tests** — Zero tests actualmente. Prioridad: balance, webhooks, matching
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
