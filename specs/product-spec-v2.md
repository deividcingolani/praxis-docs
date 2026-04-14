# Forka v2 — Product Spec: Fixed-Odds Prediction Markets

> **Autor:** PM (generado)
> **Fecha:** 2026-04-13
> **Estado:** Draft — pendiente validación del equipo completo
> **Tipo de cambio:** PIVOT — de exchange P2P a casa fixed-odds

---

## 1. Resumen Ejecutivo

Forka pivotea de un exchange de prediction markets (order book P2P, settlement on-chain) a una **plataforma de apuestas fixed-odds sobre prediction markets**.

**Modelo nuevo:**
- La casa fija los odds basándose en datos de Polymarket como referencia de pricing
- Los usuarios depositan fondos (fiat PIX como prioridad), apuestan sobre eventos del mundo real, y retiran ganancias
- Plataforma independiente con su propia marca, usuarios, wallet interna y sistema de pagos
- Moneda principal: BRL (Real brasileño)
- Mercado target: Brasil, audiencia gaming/betting (perfil Blaze)

**Por qué este pivot:**
- La audiencia target (gaming/betting brasileña) no opera order books — quiere apostar rápido
- Fixed-odds elimina el problema de liquidez vacía (cold start problem del exchange)
- El modelo de casa genera revenue predecible (margen en odds) vs. fees por trade
- PIX es instantáneo y gratis en Brasil — elimina fricción de onboarding
- No se necesita blockchain para el MVP — reduce complejidad técnica 10x

---

## 2. Arquitectura General

```
┌─────────────────────────────────────────────────────────────┐
│                        FRONTEND                              │
│              Web App (Next.js) + Mobile (PWA)                │
└──────────────────────────┬──────────────────────────────────┘
                           │
┌──────────────────────────▼──────────────────────────────────┐
│                      API GATEWAY                             │
│              (Auth, Rate Limiting, Routing)                   │
└──────┬─────────┬──────────┬──────────┬──────────┬───────────┘
       │         │          │          │          │
  ┌────▼───┐ ┌──▼────┐ ┌───▼───┐ ┌───▼────┐ ┌───▼─────┐
  │ Auth   │ │Wallet │ │Markets│ │  Bet   │ │  Risk   │
  │Service │ │Service│ │Service│ │ Engine │ │ Manager │
  └────────┘ └───────┘ └───────┘ └────────┘ └─────────┘
       │         │          │          │          │
  ┌────▼─────────▼──────────▼──────────▼──────────▼───────────┐
  │                    CORE DATABASE                           │
  │     PostgreSQL (users, wallets, markets, bets, txns)       │
  └────────────────────────────────────────────────────────────┘
       │                    │                        │
  ┌────▼──────┐   ┌────────▼────────┐   ┌──────────▼─────────┐
  │  Payment  │   │   Polymarket    │   │   Notification     │
  │  Gateway  │   │   Data Layer    │   │   Service          │
  │(PIX,Crypto│   │(Gamma,CLOB,WS) │   │(Push,Email,SMS)    │
  │ Cards)    │   │                 │   │                    │
  └───────────┘   └─────────────────┘   └────────────────────┘
```

### Delta vs v1
- **Eliminado:** Matching engine (Rust + JS), smart contracts, blockchain service, settlement adapters, on-chain indexer
- **Nuevo:** Odds Engine, Risk Manager, Bet Engine, Polymarket Data Layer, Responsible Gambling
- **Transformado:** Wallet (USDC → BRL, available/locked → available/reserved/bonus), Markets (CRUD manual → sync Polymarket), Payments (MoonPay → PIX nativo)

---

## 3. Módulos — Prioridad y Dependencias

### Mapa de dependencias

```
Auth ──────────────────┐
                       ▼
Wallet ◄──── Payment Gateway
  │
  ▼
Markets ◄──── Polymarket Data Layer
  │
  ▼
Odds Engine
  │
  ▼
Risk Manager
  │
  ▼
Bet Engine ──► Resolution Service
  │
  ▼
Notification Service
  │
  ▼
Responsible Gambling (cross-cutting)
  │
  ▼
Admin Panel (observa todo)
```

### Prioridad por módulo (Fase 1 MVP)

| # | Módulo | Prioridad | Justificación |
|---|--------|-----------|---------------|
| 1 | Auth Service | P0 | Sin auth no hay usuarios |
| 2 | Wallet Service | P0 | Sin wallet no hay fondos |
| 3 | Payment Gateway (PIX) | P0 | Sin depósitos no hay negocio |
| 4 | Markets Service + Polymarket sync | P0 | Sin mercados no hay qué apostar |
| 5 | Odds Engine | P0 | Sin odds no hay precio |
| 6 | Risk Manager | P0 | Sin risk control la casa puede quebrar |
| 7 | Bet Engine | P0 | Core del producto |
| 8 | Resolution Service | P0 | Sin resolución no hay payouts |
| 9 | Admin Panel (básico) | P1 | Necesario para operar mercados |
| 10 | Responsible Gambling (básico) | P1 | Requerido por ley brasileña |
| 11 | Notification Service | P2 | Nice to have para MVP |
| 12 | API (WebSocket streaming) | P2 | REST primero, WS después |

---

## 4. Módulo 1: Auth Service

### Qué se reutiliza de v1
- Email + contraseña (email-auth.service.ts) — funciona
- Google OAuth (google-auth.service.ts) — funciona
- JWT con refresh tokens — funciona
- Rate limiting en auth endpoints — funciona

### Qué cambia
- **Agregar:** Apple Sign-In (nuevo)
- **Agregar:** Verificación de email con código de 6 dígitos (reemplaza link)
- **Agregar:** Campos brasileños: CPF, estado, fecha de nacimiento
- **Agregar:** 2FA TOTP (Google Authenticator) — obligatorio para retiros > R$1,000
- **Agregar:** Device fingerprinting + alertas de nuevo dispositivo
- **Agregar:** IP geofencing (bloquear jurisdicciones restringidas)
- **Eliminar:** SIWE (Sign-In With Ethereum) — no se necesita wallet connect como auth
- **Eliminar:** wagmi/RainbowKit del frontend
- **Modificar:** `users` table — agregar campos BR (cpf, state, date_of_birth), quitar dependencia de `address`
- **Agregar:** Responsible gambling settings en el perfil del usuario
- **Agregar:** Self-exclusion (cooling period, auto-exclusión temporal/permanente)

### KYC — 4 niveles (vs 3 actuales)

| Nivel | Nombre | Requiere | Límites (BRL/día) |
|-------|--------|----------|-------------------|
| 0 | Unverified | Email verificado | No puede apostar |
| 1 | Basic | Nombre, CPF, fecha nacimiento, teléfono | Depósito: 5,000 / Retiro: 2,000 / Apuesta: 5,000 |
| 2 | Full | Doc con foto, selfie liveness, comprobante domicilio | 50,000 / 50,000 / 50,000 |
| 3 | VIP | Revisión manual, fuente de fondos | 500,000 / 500,000 / 200,000 |

**Proveedor KYC:** Sumsub (ya existe integración parcial), Onfido, o iDenfy. Todos soportan docs brasileños (CPF, RG, CNH).

### Modelo de datos: User (cambios vs v1)

```
Campos nuevos:
  - cpf: varchar(14) UNIQUE  (formato: 123.456.789-00)
  - date_of_birth: date
  - state: varchar(2)  (BR state code)
  - phone: varchar(20)
  - phone_verified: boolean
  - kyc_level: integer (0-3, vs 0-2 actual)
  - two_factor_enabled: boolean
  - two_factor_secret: varchar (encrypted)
  - self_exclusion: jsonb (type, start_date, end_date)
  - responsible_gambling: jsonb (daily_deposit_limit, daily_loss_limit, session_time_limit, cooling_period)
  - device_fingerprints: jsonb[]

Campos eliminados:
  - address: varchar (wallet address — ya no necesario como campo principal)

Campos modificados:
  - kyc_level: 0-3 (vs 0-2)
```

---

## 5. Módulo 2: Wallet Service

### Cambio fundamental
De USDC (crypto) a BRL (fiat). De `available + locked` a `available + reserved + bonus`.

### Arquitectura
Wallet interna con balance en BRL. NO es wallet blockchain — es ledger interno. Todas las transacciones son double-entry.

```
Wallet del usuario:
  ├── available_balance    (disponible para apostar)
  ├── reserved_balance     (bloqueado en apuestas activas)
  ├── bonus_balance        (fondos promocionales, no retirables directamente)
  └── total_balance        (available + reserved + bonus)
```

### Qué se reutiliza
- Double-entry ledger (ledger_entries) — concepto idéntico
- SELECT FOR UPDATE para mutaciones — crítico, se mantiene
- CHECK constraints (>= 0) — se mantiene

### Qué cambia
- Moneda: USDC → BRL
- Balance: `available + locked` → `available + reserved + bonus`
- Nuevos tipos de transacción: bet_placed, bet_won, bet_lost, bet_cancelled, cashout, bonus_credit, bonus_wagered
- Lifetime stats: lifetime_deposits, lifetime_withdrawals, lifetime_winnings, lifetime_losses
- La tabla `user_balances` se reemplaza por `wallets` con la nueva estructura

### Tipos de transacción (ledger)

```
deposit           → Usuario deposita fondos
withdrawal        → Usuario retira fondos
bet_placed        → available → reserved
bet_won           → reserved → available + profit de house
bet_lost          → reserved → house
bet_cancelled     → reserved → available
cashout           → payout parcial a available
bonus_credit      → bono acreditado
bonus_wagered     → bono convertido a balance real
adjustment        → ajuste manual admin (con motivo obligatorio)
```

---

## 6. Módulo 3: Payment Gateway

### PIX como canal principal

PIX es instantáneo, gratis (o costo mínimo absorbido), y el estándar de pago en Brasil. Es el equivalente a tener crypto speed con fiat UX.

### Qué se reutiliza
- Mercado Pago integration (existe) — PIX es extensión natural de su API
- Webhook processing con idempotency (webhook_events table) — se mantiene
- Payment transactions table — se adapta

### Qué cambia
- **MoonPay se elimina** (era para card → USDC, ya no necesario)
- **PIX nativo** como método principal (vía Mercado Pago o Pagar.me)
- **Crypto como secundario** (USDC/USDT en Polygon, BTC Lightning)
- **Retiros automáticos PIX** (nuevo — actualmente solo manual)
- **Anti-fraude** (nuevo módulo completo)

### Proveedores recomendados

| Método | Proveedor | Prioridad MVP |
|--------|-----------|---------------|
| PIX | Mercado Pago API (ya integrado) | P0 |
| Boleto | Mercado Pago | P2 |
| Tarjeta | Mercado Pago / Stripe BR | P2 |
| USDC/USDT | NOWPayments | P2 |
| BTC Lightning | BTCPay Server | P2 |

### Flujo de depósito PIX (Fase 1)

```
1. Usuario solicita depósito de R$500
2. Backend genera código PIX (QR + copia-y-pega) vía Mercado Pago
3. Usuario paga desde app de banco
4. Mercado Pago envía webhook de confirmación
5. Backend: verifica signature → verifica monto → crea txn ledger → actualiza balance → notifica
6. Tiempo total: < 30 segundos
```

### Flujo de retiro (Fase 1)

```
1. Usuario solicita retiro
2. Verificaciones: KYC level, 2FA, balance, límites, anti-fraud
3. Retiros > R$10,000: aprobación manual admin
4. Procesamiento vía Mercado Pago PIX
5. Webhook confirmación → marcar completed → notificar
```

### Anti-fraude básico
- Bloquear retiro si depositó y no apostó 1x el monto
- Alertar si mismo CPF en múltiples cuentas
- Velocity check: > 10 depósitos en 1 hora = review manual
- Chargeback detection: congelar cuenta

---

## 7. Módulo 4: Markets Service

### Cambio fundamental
De mercados creados manualmente por admin a **sync automático desde Polymarket**.

### Pipeline

```
Polymarket Gamma API (polling cada 5 min)
    → Filtrar (volumen, tiempo, contenido)
    → Traducir (PT/ES/EN vía LLM)
    → Categorizar (política, economía, crypto, deportes, cultura, tech, clima)
    → Calcular odds (Odds Engine)
    → Publicar
    → Admin puede override
```

### Qué se reutiliza
- Markets table (estructura base) — se modifica
- Categories / tags system — se mantiene
- Admin market management — se adapta
- i18n (PT/ES/EN) — se mantiene

### Qué cambia
- Nuevo campo `polymarket_id` para tracking
- Outcomes cambian: de `shares` a `odds` (YES/NO con odds decimales)
- Precios vienen de Polymarket CLOB API, no del order book interno
- Traducción automática (LLM) vs manual
- Exposure tracking por mercado (cuánto tiene apostado la casa)

### Fuentes de datos Polymarket

| Fuente | Uso | Rate limit |
|--------|-----|------------|
| Gamma API | Metadata mercados | Sin límite documentado, poll cada 5 min |
| CLOB API (midpoint) | Precios referencia | 60 req/min |
| CLOB API (book) | Spread, profundidad | 60 req/min |
| WebSocket | Streaming top 10 mercados | 5 conn/IP |

---

## 8. Módulo 5: Odds Engine (NUEVO)

### Responsabilidad
Convierte probabilidades de Polymarket en odds de la plataforma con margen incorporado.

### Lógica

```
1. polymarket_prob = midpoint price del CLOB API
2. implied_prob = polymarket_prob * (1 + margin/2)     // margin = 8-12%
3. decimal_odds = 1 / implied_prob
4. Ajustar por exposición si un lado > 70%
```

### Configuración de márgenes por categoría

| Categoría | Base | Min | Max |
|-----------|------|-----|-----|
| Política | 8% | 5% | 15% |
| Economía | 10% | 6% | 18% |
| Crypto | 12% | 8% | 20% |
| Deportes | 8% | 5% | 12% |
| Cultura | 10% | 6% | 15% |
| Tech | 10% | 6% | 18% |

### Reglas
- Recalcular cuando precio Polymarket cambia > 1%
- Suspender si spread Polymarket > 10%
- Lock de odds por 10 segundos al usuario cuando entra al bet slip
- Rango permitido: 1.05 — 15.00 (fuera = mercado visible pero no acepta apuestas)

---

## 9. Módulo 6: Risk Manager (NUEVO)

### Responsabilidad
Controla la exposición de la plataforma como casa. Previene pérdidas catastróficas.

### Parámetros

| Scope | Parámetro | Valor default |
|-------|-----------|---------------|
| Per market | Max exposición total | R$500,000 |
| Per market | Max exposición por outcome | R$300,000 |
| Per user | Max apuesta individual | R$50,000 |
| Per user | Min apuesta | R$1 |
| Per user | Max apuestas abiertas | 50 |
| Per user | Max volumen diario | R$100,000 |
| Platform | Max exposición total | R$5,000,000 |
| Platform | Max exposición por categoría | R$2,000,000 |

### Lógica de aceptación
8 checks secuenciales: KYC → balance → límites usuario → responsible gambling → exposición mercado → exposición plataforma → odds vigentes → desviación vs Polymarket.

Si `abs(our_prob - polymarket_prob) > 0.15`: suspender mercado automáticamente.

---

## 10. Módulo 7: Bet Engine (NUEVO)

### Flujo de apuesta

```
1. Usuario selecciona mercado + outcome
2. UI muestra odds + campo monto
3. Usuario ingresa monto → UI calcula payout en real-time
4. Confirma apuesta
5. Backend: lock odds 10s → Risk Manager valida → Wallet: available → reserved → crear bet → actualizar exposición → confirmar (< 500ms)
```

### Status flow

```
pending → won         (mercado resuelve a favor)
pending → lost        (mercado resuelve en contra)
pending → cashed_out  (cash-out anticipado)
pending → cancelled   (cancelación antes de inicio)
pending → voided      (mercado cancelado)
```

### Cash-out

```
cashout_value = bet.amount * (current_prob / prob_at_placement) * (1 - 0.05)
```

No disponible cuando: < 1 hora de resolución, precio stale > 2 min, spread > 15%.

---

## 11. Módulo 8: Resolution Service

### Flujo

```
1. Polling Gamma API cada 1 min para mercados cerca de resolución
2. Cuando Polymarket resuelve:
   a. Esperar confirmación on-chain (~15 min)
   b. Marcar mercado "resolving"
   c. Calcular payouts ganadores
   d. Wallet: ganadores reserved → available + profit
   e. Wallet: perdedores reserved → house
   f. Marcar "resolved"
   g. Notificar usuarios
```

### Casos especiales
- Disputa UMA: mantener en "resolving"
- Mercado voided: devolver todas las apuestas
- Resolución ambigua: queue para revisión manual admin

---

## 12. Módulos 9-12: Notification, Responsible Gambling, Admin, API

Ver spec detallada del founder (incluida arriba). Estos módulos siguen la spec tal cual con estas notas PM:

**Notification Service (Fase 1 MVP):**
- Solo email transaccional (depósito, retiro, apuesta resuelta)
- Push notifications y SMS = Fase 2

**Responsible Gambling (Fase 1 MVP):**
- Límite de depósito diario (configurable por usuario)
- Límite de pérdida diaria
- Auto-exclusión básica (24h, 7d, 30d)
- Reality check cada 60 min (popup con tiempo y balance neto)

**Admin Panel (Fase 1 MVP):**
- Dashboard P&L básico
- Gestión de mercados (activar/suspender/featuring)
- Cola de retiros pendientes
- Listado de usuarios con búsqueda

**API (Fase 1 MVP):**
- REST only (no WebSocket)
- Endpoints públicos: markets list, market detail, odds
- Endpoints auth: bets CRUD, wallet, transactions
- Endpoints admin: dashboard, markets, withdrawals, users
- Webhooks: payment provider callbacks

---

## 13. Stack Técnico v2

### Qué se mantiene
- **Backend:** Node.js + TypeScript + Fastify → **cambiar a NestJS** (spec lo pide, más estructura para un equipo)
- **Database:** PostgreSQL 16 + Drizzle ORM
- **Cache/Queue:** Redis 7 + BullMQ
- **Frontend:** Next.js + React + Tailwind
- **Admin:** Vite + React (separado)
- **Infra:** Railway (backend), Vercel (frontend/admin)

### Qué se agrega
- Socket.io para streaming de odds (Fase 2)
- Recharts o Lightweight Charts para gráficos de odds
- Firebase Cloud Messaging para push (Fase 2)
- SendGrid/AWS SES para email transaccional

### Qué se elimina
- wagmi, viem, RainbowKit (wallet connect)
- Foundry, Solidity, smart contracts
- Matching engine (Rust)
- EIP-712 signing

### Decisión PM: Fastify vs NestJS

La spec pide NestJS. El backend actual es Fastify. **Recomendación: mantener Fastify** por estas razones:
1. Ya hay 17 route files y 28+ services funcionando
2. Migrar a NestJS es esfuerzo puro sin valor para el usuario
3. Fastify es más rápido y el equipo ya lo conoce
4. NestJS agrega complejidad de decorators/modules que no necesitamos

**Si el founder insiste en NestJS:** es un rewrite completo del backend, agrega 3-4 semanas al timeline.

---

## 14. Sprint Plan — Fase 1 MVP

### Pre-requisitos (Semana 0 — decisiones del founder)
- [ ] Confirmar: Fastify o NestJS?
- [ ] Confirmar: proveedor PIX (Mercado Pago actual o Pagar.me?)
- [ ] Confirmar: proveedor KYC (Sumsub, Onfido, iDenfy?)
- [ ] Consulta legal: prediction markets no-deportivos en Brasil
- [ ] Definir: ¿se mantiene la entidad actual o se crea nueva?

### Sprint 1 (Semanas 1-2): Foundation Reset

**Objetivo:** Base de datos nueva, auth adaptado, wallet BRL funcional.

| Task | Owner | Esfuerzo | Deps |
|------|-------|----------|------|
| Diseñar schema v2 (nuevas tablas: wallets, bets, market_odds, risk_exposure) | Tech Lead + Backend | L | — |
| Migrar/adaptar users table (campos BR, KYC 4 niveles) | Backend | M | Schema |
| Implementar wallet service BRL (available/reserved/bonus + ledger) | Backend | L | Schema |
| Verificación email con código 6 dígitos | Backend | S | — |
| Eliminar dependencias blockchain del backend | Backend | S | — |
| Eliminar wagmi/RainbowKit del frontend | Frontend | S | — |
| Setup proyecto frontend v2 (limpiar, nuevo routing) | Frontend | M | — |
| Consulta legal Brasil (prediction markets + LGPD) | Legal | — | — |
| Modelo financiero v2 (fixed-odds P&L, márgenes, exposure) | Finanzas | — | — |

### Sprint 2 (Semanas 3-4): Payment + Markets

**Objetivo:** Un usuario puede depositar BRL vía PIX y ver mercados con odds.

| Task | Owner | Esfuerzo | Deps |
|------|-------|----------|------|
| Integración PIX (depósitos) vía Mercado Pago | Backend | L | Wallet |
| Webhook PIX + idempotency | Backend | M | PIX |
| Polymarket Gamma API sync (polling, filtrado) | Backend | L | Schema |
| Odds Engine (margen fijo, conversión prob → odds) | Backend | M | Markets |
| Traducción de mercados (PT/ES/EN) | Backend + Data | M | Markets |
| Frontend: landing page + lobby de mercados | Frontend | L | — |
| Frontend: detalle de mercado (odds, descripción) | Frontend | M | API |
| Frontend: depósito PIX (QR code + status) | Frontend | M | PIX API |
| Diseño UX: flujos principales | UX | — | — |

### Sprint 3 (Semanas 5-6): Bet Engine + Risk

**Objetivo:** Un usuario puede apostar y ver sus apuestas activas.

| Task | Owner | Esfuerzo | Deps |
|------|-------|----------|------|
| Risk Manager (exposición, límites, validación) | Backend | L | Odds Engine |
| Bet Engine (crear apuesta, lock odds, wallet integration) | Backend | XL | Risk, Wallet |
| KYC nivel 1 (CPF + datos básicos, sin proveedor externo) | Backend | M | Auth |
| Frontend: bet slip (monto, payout calculator, confirmar) | Frontend | L | Bet API |
| Frontend: mi cuenta (balance, apuestas activas) | Frontend | M | API |
| Frontend: retiro PIX (solicitud + status) | Frontend | M | API |
| Retiros PIX (flujo completo con aprobación manual) | Backend | L | Wallet, PIX |
| Anti-fraude básico (reglas automáticas) | Backend | M | Payments |
| Admin: dashboard P&L + exposición | Frontend (admin) | M | API |

### Sprint 4 (Semanas 7-8): Resolution + Polish

**Objetivo:** Ciclo completo: apostar → resolver → cobrar. Plataforma lista para beta cerrada.

| Task | Owner | Esfuerzo | Deps |
|------|-------|----------|------|
| Resolution Service (polling Polymarket, settlement automático) | Backend | L | Markets, Bets |
| Responsible gambling básico (límites, auto-exclusión) | Backend | M | Auth |
| Notification Service (email transaccional) | Backend | M | — |
| Admin: gestión de mercados (activar/suspender/featuring) | Frontend (admin) | M | API |
| Admin: cola de retiros | Frontend (admin) | M | API |
| Frontend: historial de apuestas + P&L | Frontend | M | API |
| Frontend: settings (perfil, responsible gambling) | Frontend | M | API |
| 15-25 mercados curados manualmente + sync | Backend + PM | S | Markets |
| QA: testing E2E del flujo completo | QA | L | Todo |
| Security review pre-launch | Security | M | Todo |

---

## 15. Asignación por Especialista

### Tech Lead
- Diseño de schema v2 (delta vs v1)
- Decisión Fastify vs NestJS
- Arquitectura del Odds Engine y Risk Manager
- Review de todas las PRs de backend
- Definir estrategia de migración (v1 → v2)

### Backend Developer
- Implementar los 8 módulos core
- Adaptar auth, wallet, payments
- Nuevos: odds engine, risk manager, bet engine, resolution
- Tests unitarios de módulos críticos (wallet, bets, risk)

### Frontend Developer
- Eliminar dependencias blockchain (wagmi, RainbowKit)
- Nuevo: landing, lobby mercados, detalle mercado, bet slip
- Nuevo: depósito/retiro PIX, mi cuenta, historial
- Adaptar: auth modal (sin wallet connect), settings
- PWA setup, dark mode default, mobile-first

### Data Scientist
- Calibración de márgenes por categoría
- Modelo de exposure management
- Análisis: ¿qué mercados de Polymarket son rentables para fixed-odds?
- Dashboard de métricas (GGR, NGR, margen efectivo)

### Security Lead
- Revisión de flujos de pago (PIX webhooks, anti-fraude)
- 2FA implementation review
- Rate limiting strategy
- LGPD compliance técnico
- Penetration testing pre-launch

### Legal
- Viabilidad de prediction markets no-deportivos en Brasil
- Lei 14.790/2024 — ¿aplica a esto?
- Licencia Ministério da Fazenda — ¿necesaria?
- LGPD: datos de CPF, biometría KYC
- ToS y política de responsible gambling

### Finanzas (CFO)
- Modelo P&L fixed-odds (revenue = margen en odds)
- Costos PSP (Mercado Pago fees para PIX)
- Exposure management (¿cuánto capital necesita la casa?)
- Unit economics v2 (CAC, LTV, margen por apuesta)
- Política de bonos y su impacto en P&L

### DevOps
- Adaptar Docker Compose (eliminar matching-engine)
- CI/CD pipeline (GitHub Actions)
- Monitoring: Polymarket API health, payment webhooks, odds freshness
- Redis config para cache de precios y rate limiting
- Backup strategy para ledger

### QA
- Test plan completo para Fase 1
- E2E: depósito PIX → apuesta → resolución → retiro
- Edge cases: odds stale, mercado suspendido, balance insuficiente
- Responsible gambling: verificar que límites se enforzan
- Load testing: concurrent bets, webhook throughput

### UX Designer
- Rediseño: de exchange a betting platform
- Flujos: onboarding, depósito PIX, apostar, cash-out, retiro
- Bet slip UX (3 taps: outcome → monto → confirmar)
- Mobile-first, dark mode default
- Responsible gambling UX (popups, límites, auto-exclusión)

### Copywriter
- Tono betting (no crypto/trading)
- Microcopy: bet slip, confirmaciones, errores
- Traducción de mercados (guidelines para LLM)
- Responsible gambling messaging
- Landing page copy (CTA, value proposition)

### Marketing
- GTM para audiencia betting brasileña
- Estrategia de acquisition (¿relación con Blaze?)
- Programa de referral adaptado
- Content strategy (blog, social)
- Soft launch plan (beta cerrada → abierta)

---

## 16. Riesgos del Pivot

| Riesgo | Prob | Impacto | Mitigación |
|--------|------|---------|------------|
| Regulatorio: prediction markets no legales en BR | Alto | Crítico | Consulta legal ANTES de construir |
| Exposición descontrolada: la casa pierde mucho | Medio | Alto | Risk Manager + límites conservadores |
| Polymarket como single point of failure (pricing) | Alto | Alto | Cache agresivo + mercados propios (Fase 3) |
| Polymarket API cambia/rate limita | Medio | Alto | Redundancia (Gamma + CLOB), fallback manual |
| PIX fraud / chargebacks | Medio | Alto | Anti-fraude + KYC obligatorio |
| Timeline: 8 semanas es agresivo | Alto | Medio | Scope cut si es necesario (ver abajo) |

### Scope cut de emergencia (si se pasa de 8 semanas)

Si hay que recortar, en este orden:
1. Eliminar: crypto payments (solo PIX)
2. Eliminar: cash-out (solo apuestas simples)
3. Eliminar: traducción automática (solo inglés + manual)
4. Simplificar: KYC nivel 1 solo (CPF, sin proveedor externo)
5. Simplificar: retiros manuales (admin aprueba todos)
6. Simplificar: 10 mercados curados manualmente (sin sync automático)

---

## 17. Métricas de Éxito — Fase 1

| Métrica | Target |
|---------|--------|
| Flujo completo funcional | Depósito → apuesta → resolución → retiro |
| Mercados activos | 15-25 curados |
| Latencia de apuesta | < 500ms |
| Latencia de precios vs Polymarket | < 60s |
| Uptime | > 99% |
| Bugs críticos (money-losing) | 0 |
