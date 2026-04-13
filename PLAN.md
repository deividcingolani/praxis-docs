# Plan: Forka — Plataforma de Prediction Markets (tipo Polymarket)

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
| Base de datos | PostgreSQL 16 + Drizzle ORM | ACID para datos financieros |
| Cache/PubSub | Redis 7 | Order book, real-time |
| Frontend | Next.js 15 + TypeScript | SSR para SEO, App Router |
| Charts | TradingView Lightweight Charts | El mismo que usa Polymarket |
| UI | Tailwind CSS v4 | Performante |
| Wallet | wagmi v2 + viem + RainbowKit | Estándar Web3 para React |
| State | Zustand + TanStack Query | Ligero, sin boilerplate |
| Payments | MoonPay + Mercado Pago | Fiat onramp (Card, Apple Pay, Google Pay, LATAM) |

### Estructura del proyecto (5 repos separados)

```
backend/          -- Fastify API server (GitHub: deividcingolani/praxis-backend, Deploy: Railway)
frontend/         -- Next.js 15 web app (GitHub: deividcingolani/praxis-frontend, Deploy: Vercel forka.io)
admin/            -- Vite + React admin panel (GitHub: deividcingolani/praxis-admin, Deploy: Vercel)
contracts/        -- Foundry: smart contracts Solidity (GitHub: deividcingolani/praxis-contracts)
docs/             -- Project documentation (GitHub: deividcingolani/praxis-docs)
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
- Nombre: Forka (domain: forka.io, Twitter: @Forkad2026)
- Brand guidelines (logo, colores, tipografía, tono)
- Posicionamiento y propuesta de valor
- Estrategia de comunidad pre-launch (Discord, Twitter/X)
- Multi-branding config, i18n (EN/ES/PT), light/dark theme

### 0.4 Arquitectura Técnica — `tech-lead` (2 sem, en paralelo)
- Documento de arquitectura con diagramas
- Diseño de data model (PostgreSQL) — **con modelo de balance multi-currency** (no asumir USDC everywhere)
- Arquitectura de smart contracts
- Diseño de API (REST + WebSocket)
- **Abstracción PaymentProvider**: interfaz común para crypto (Polygon/USDC) y fiat (PSP futuro). El motor de trading opera en unidades internas normalizadas, agnóstico al origen del dinero
- **Preparación multibranding**: arquitectura multi-tenant por configuración (`brand_config`: currencies permitidas, mercados, KYC requerido, tema). No implementar frontends múltiples aún, solo asegurar que ningún componente asuma una sola marca
- Setup de repos separados (backend, frontend, admin, contracts, docs)
- Pipeline CI/CD

---

## Phase 1: MVP Core (Semanas 5-18)

### 1.1 Smart Contracts — `tech-lead` + `backend-dev` (5 sem) → depende de 0.4
- **ConditionalTokens.sol**: Fork/integración de Gnosis CTF (ERC-1155)
- **CTFExchange.sol**: Exchange con órdenes firmadas EIP-712, matching atómico, fees, cancelación
- **UMACTFAdapter.sol**: Conexión UMA OO V3 → CTF para resolución
- Deploy scripts: Mumbai testnet → Polygon mainnet
- 100% test coverage + fuzz tests + invariant tests + Slither

### 1.2 Backend Services — `backend-dev` (7 sem) → depende de 0.4, 1.1
- **User Service** (sem 5-6): Auth JWT + SIWE, wallet, KYC stub
- **Market Service** (sem 6-8): CRUD mercados, estados, categorías, búsqueda full-text
- **Order Service + Matching Engine** (sem 7-10): Order book in-memory, matching price-time priority, EIP-712, WebSockets
- **Settlement Service** (sem 10-12): Tx manager (nonce, gas, retry), eventos on-chain, resolución
- **Price Service** (sem 8-10): Mid-price, OHLCV candles, broadcast WebSocket
- **Payment Service** (sem 10-12): MoonPay (Card, Apple Pay, Google Pay — browser SDK overlay) + Mercado Pago (Checkout Pro — LATAM fiat) + Crypto (USDC on Polygon). Deposit/withdrawal flows with KYC-gated limits. Modelo de balance interno (`user_balances` con available + locked)
- **API Gateway**: OpenAPI docs, validación, CORS, rate limiting, logging (Pino)

### 1.3 Frontend — `frontend-dev` (6 sem) → depende de 0.4, 1.2, 0.3
- **Design System** (sem 7-8): Tokens, componentes core, layout, dark mode
- **Wallet + Auth** (sem 8-9): RainbowKit, SIWE, USDC balance, approval flow. **Onboarding dual**: flujo crypto (connect wallet) + flujo fiat (registro con email/Google → deposit con tarjeta vía PSP widget)
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

### 1.6 Security Controls — `tech-lead` + `security` (3 sem, en paralelo con 1.2-1.3)

**CRÍTICO: Estos controles deben estar implementados ANTES del primer depósito fiat en mainnet.**

- **PSP Webhook Security**: Validar firma criptográfica de cada webhook (HMAC-SHA256 MoonPay). Tabla `webhook_events` con idempotency key (UNIQUE constraint). Verificación server-to-server post-webhook. IP allowlist del PSP. Rechazar webhooks con timestamp > 5 min
- **Balance Integrity**: Toda operación de balance en transacción PostgreSQL con `SELECT FOR UPDATE`. Constraints `CHECK (available >= 0 AND locked >= 0)`. Tabla `ledger_entries` con double-entry bookkeeping. Reconciliación automática cada hora. Usar `decimal.js` en TypeScript — prohibir `number` para montos
- **Auth Dual Security**: Account linking requiere verificación de ownership de ambos métodos. Tabla `user_auth_methods` (un user, múltiples auth methods). CSRF protection en OAuth flows. Magic links: expiración 10 min, single-use, rate limit 3/hora
- **Principio fiat inamovible**: Forka NUNCA toca fiat directamente. Flujo obligatorio: Usuario → PSP → Conversión a USDC → Wallet Forka. Si fiat pasa por cuentas de Forka = Money Transmitter license requerida

### 1.7 Admin Panel (separate repo: admin/) — `backend-dev` + `frontend-dev` (3 sem) → depende de 1.2

**El admin es una aplicación separada del frontend público (Vite + React).** Auth por email+password y Google (NO wallet). Sistema RBAC.

#### Current State (implemented)
- **Auth**: email+password + Google OAuth, separate `admin_users` table
- **RBAC**: 3 roles implemented (super_admin, admin, editor)
- **Dashboard**: Platform stats
- **Markets**: CRUD + status management + resolution
- **Users**: User listing
- **KYC**: Approve/reject

#### Planned Roles (full RBAC)

| Rol | Permisos |
|---|---|
| **Super Admin** | Todo. Gestión de usuarios admin, configuración de plataforma, brand_configs |
| **Market Manager** | Crear/editar/pausar/resolver mercados. Gestionar categorías y outcomes |
| **Compliance Officer** | Revisar KYC submissions, aprobar/rechazar, gestionar geo-blocking, ver audit logs |
| **Finance** | Ver dashboards financieros, aprobar withdrawals manuales, gestionar treasury, ver ledger |
| **Support** | Ver usuarios (read-only), ver transacciones, gestionar tickets. NO puede modificar balances |
| **Viewer** | Read-only de dashboards y métricas. Sin acceso a datos personales de usuarios |

#### Planned Features (not yet implemented)
- Payment/withdrawal approval UI
- Audit log (registro inmutable de toda acción admin)
- 2FA obligatorio para Super Admin y Finance
- Configuration UI (brand configs, fee structure, KYC limits, geo-blocking rules, PSP settings)

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

### 2.6 Proxy Wallet + Gas Abstraction — `tech-lead` + `backend-dev` (2 sem)
- Proxy wallet por usuario: la plataforma genera una wallet interna que agrupa transacciones, eliminando gas fees individuales para el usuario
- Meta-transactions o ERC-4337 (Account Abstraction) para que el usuario no necesite ETH/MATIC para gas
- El usuario fiat opera sin saber que hay blockchain por debajo; el usuario crypto puede conectar su propia wallet o usar la proxy

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

### 2.5 Marketing Launch — `marketing` (6 sem, inicia semana 16 en paralelo con beta abierto)

**IMPORTANTE:** Con fiat onramp desde MVP, el marketing pesado debe coincidir con el lanzamiento, no empezar después. El usuario target principal viene del mundo betting/gaming y no necesita ser crypto-nativo.

#### Semanas 16-17: Pre-launch (durante beta abierto)
- Landing page con dual CTA: "Registrate con email" (principal) + "Conectá tu wallet" (secundario)
- Waitlist activada con referral system (bonus $5 por referido que deposita)
- Content calendar: 5 posts/semana en Twitter/X + 3 TikToks/semana + 2 Instagram reels/semana
- Outreach a 30+ influencers: 15 crypto/fintech + 15 betting/sports/gaming (tipsters, streamers, analistas deportivos)
- Testnet tournament con premios para beta testers ($5K pool)
- Meta Ads y TikTok Ads en modo test: $5K para validar audiencias y creatives antes del launch

#### Semana 18: Launch coordinado con evento deportivo/político de alto interés
- Campaña omnicanal: Twitter/X + TikTok + Instagram + Meta Ads + Google Ads (SEM)
- Influencers de betting y crypto publican contenido coordinado el día de lanzamiento
- PR dual: medios crypto (CoinDesk, The Block, Decrypt) + medios fintech/betting (TechCrunch, Sportsbusiness, medios deportivos LATAM)
- Product Hunt launch
- Twitter Spaces + TikTok Live con demo en vivo de registro → depósito con tarjeta → primer trade
- Mercado flagship dual: evento deportivo de la semana + evento político activo

#### Semanas 19-21: Growth post-launch
- Paid acquisition: $15-20K/mes en Meta Ads + TikTok Ads + Google SEM. Target CAC: $8-15/usuario que deposita
- Programa de referidos permanente: referidor y referido reciben $5 en crédito de trading
- Bonus de primer depósito: "Depositá $10, operá con $15" (primeros 30 días)
- Partnerships con tipsters y cappers LATAM: programa de afiliados (rev share 1% del volumen de referidos)
- Contenido orgánico: 5 tweets/día + 3 threads/semana + daily TikToks con trades en vivo
- Community: Discord para power users, Telegram para alertas de mercado, WhatsApp para LATAM casual

#### KPIs del primer mes post-launch
- Usuarios registrados: 5,000 (70% vía email/Google, 30% vía wallet)
- Usuarios que depositaron: 1,500 (30% conversión)
- Usuarios que operaron 1+ vez: 1,000 (20% conversión)
- Volumen total: $500K USD
- CAC blended: <$12 USD
- D7 retention: >35%
- D30 retention: >18%

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

### 3.3 API Pública + Datos como Producto — `tech-lead` + `backend-dev` (4 sem)
- REST API con API keys (free: 100 req/min, paid: 1000 req/min)
- WebSocket API para datos real-time
- SDK TypeScript (npm) + Python (PyPI)
- Documentación con Mintlify
- Rate limiting por API key
- **Data feed comercial**: probabilidades en tiempo real, históricos de mercados, volumen agregado. Revenue stream explícito para instituciones, medios, y analistas (tier Enterprise $499+/mes)

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
| Fiat PSP restricciones por país | Media | Alto | Integrar 2+ PSPs (MoonPay + Mercado Pago), fallback entre ellos, KYC asimétrico crypto/fiat |
| Fragmentación de liquidez por marca | Baja | Medio | Order book compartido único, multibranding solo en frontend |
| Proxy wallet key compromise | Media | Crítico | HSM/KMS obligatorio (AWS KMS), hot/cold separation, circuit breaker de withdrawals |
| PSP webhook forgery | Media | Crítico | Signature validation, idempotency table, server-to-server verification, IP allowlist |
| Balance manipulation (race condition) | Media | Alto | SELECT FOR UPDATE, CHECK constraints, double-entry ledger, reconciliación automática |
| Double-spend por falta de balance check | Alta | Crítico | Lock de balance en creación de orden. `UPDATE WHERE available >= amount` atómico |
| Clasificación como Money Transmitter | Media | Crítico | Forka NUNCA toca fiat. Solo flujo PSP→USDC. No aceptar USD ni BRL |
| Chargebacks en depósitos fiat | Media | Alto | 3DS obligatorio, periodo retención 14 días, chargeback reserve 2-3% volumen fiat |
| Adverse selection fiat vs crypto | Media | Medio | Slippage protection en UI fiat, monitoreo PnL por segmento, educación in-app |

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

## Archivos Críticos

- `contracts/src/CTFExchange.sol` — Contrato core de exchange
- `backend/src/services/order-matching-engine.ts` — Motor de matching (más crítico para performance)
- `backend/src/services/settlement-service.ts` — Puente entre matching off-chain y settlement on-chain
- `frontend/src/app/markets/[id]/page.tsx` — Página de detalle de mercado (más tráfico)

## Verificación End-to-End

1. **Contracts**: Fuzz tests, invariant tests, Slither, auditoría externa
2. **Backend**: Unit tests matching engine, integration tests ciclo completo de orden, load test 100 ord/seg
3. **Frontend**: Playwright E2E (wallet → browse → trade → portfolio), Lighthouse > 90
4. **Full flow testnet**: Crear mercado → tradear → resolver con UMA → cobrar payout
