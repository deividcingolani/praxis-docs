# Gaps & Pendientes — Phase 0 y Phase 1

Lo que se salteó o quedó incompleto de las fases 0 y 1.

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
| **Settlement Service** | **Salteado** | No hay worker que envíe txs on-chain. El matching engine solo escribe en DB, no submitea a blockchain |
| **Blockchain Transaction Manager** | **Salteado** | Nonce management, gas estimation, retry logic — no implementado |
| **Background Workers (BullMQ)** | **Salteado** | Settlement worker, indexer, price aggregation, resolution — BullMQ está en deps pero no hay workers |
| **On-chain event listener / Indexer** | **Salteado** | No hay servicio que escuche eventos del contrato (OrderFilled, ConditionResolution, etc.) |
| **Rate limiting por endpoint** | **Parcial** | Rate limit global (100 req/min) existe, pero el rate limit específico de orders (10 req/seg) no se configuró |

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
| **TradingView Charts** | **Salteado** | Hay placeholder div, pero no se integró `lightweight-charts` de TradingView |
| **Order book click-to-fill** | **Salteado** | Click en precio del order book debería llenar el input de precio en trading panel |
| **WebSocket real-time updates** | **Salteado** | Hooks y socket client existen, pero no están conectados a nada real |
| **Sidebar de filtros** | **Parcial** | Componente existe pero no se usa en la página de markets |
| **Mobile responsive** | **Parcial** | Mobile-first classes existen, pero no se testeó ni ajustó (bottom sheet, hamburger menu funcionalidad) |
| **E2E Tests (Playwright)** | **Salteado** | Planeado pero no se creó ni configuró |
| **Lighthouse audit** | **Salteado** | Target era >90, no se corrió |

### 1.4 UX Design — Completo (documentación)
| Deliverable | Estado |
|---|---|
| User flows (5 flujos con Mermaid) | Completo |
| Interaction specs (loading, error, empty, success) | Completo |
| Responsive layout specs | Completo |
| Accessibility checklist | Completo |
| Micro-interactions | Completo |
| Component behavior specs | Completo |

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
| **Geo-blocking middleware** | **Salteado** | Planeado en legal doc pero no implementado |
| **Admin panel para KYC review** | **Salteado** | Solo hay endpoints, no hay UI de admin |

---

## Resumen de Gaps Críticos (ordenados por prioridad)

### Prioridad Alta — Necesarios antes de mainnet
1. **Settlement Service + Blockchain TX Manager** — Sin esto, los trades no se liquidan on-chain
2. **On-chain Event Indexer** — Sin esto, no se sincroniza estado blockchain ↔ DB
3. **TradingView Charts** — Core de la UX de trading, el placeholder no es aceptable
4. **WebSocket real-time** — Los hooks existen pero no hay conexión real
5. **Sumsub KYC real** — Necesario para compliance antes de manejar fondos

### Prioridad Media — Necesarios para launch
6. **Background Workers (BullMQ)** — Settlement, resolution, notificaciones
7. **CI/CD Pipeline** — GitHub Actions para lint, test, build, deploy
8. **E2E Tests** — Al menos para flujos críticos (connect → trade → portfolio)
9. **Geo-blocking** — Requerido por compliance
10. **Admin Panel** — Para crear/resolver mercados y revisar KYC

### Prioridad Baja — Nice to have para MVP
11. **SDK package** — Para terceros, puede esperar a Phase 3
12. **Slither analysis** — Buena práctica pero la auditoría real es lo que importa
13. **Lighthouse audit** — Optimización, no bloquea launch
14. **Order book click-to-fill** — UX improvement
15. **Mobile bottom sheet** — Responsive mejora
