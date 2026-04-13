# Phase 0.2: Modelo de Negocio y Proyecciones Financieras

> **DISCLAIMER:** Este documento contiene estimaciones y proyecciones basadas en datos publicos del mercado de prediction markets (2024-2026), benchmarks de la industria y supuestos razonables. Las cifras no constituyen garantias de rendimiento. Se recomienda validar todos los supuestos con asesores financieros y ajustar las proyecciones conforme se obtengan datos reales de operacion.

> **[UPDATE April 2026]** This document was written during Phase 0 planning, before development began. The platform has since been built and deployed. Sections marked with **[UPDATE April 2026]** reflect what was actually implemented vs. what was planned. Original content is preserved intact.

---

## Tabla de Contenidos

1. [Revenue Streams (Fuentes de Ingreso)](#1-revenue-streams-fuentes-de-ingreso)
2. [Cost Structure (Estructura de Costos)](#2-cost-structure-estructura-de-costos)
3. [Unit Economics](#3-unit-economics)
4. [Proyeccion Financiera a 24 Meses](#4-proyeccion-financiera-a-24-meses)
5. [Fundraising Strategy](#5-fundraising-strategy)
6. [Analisis Competitivo Financiero](#6-analisis-competitivo-financiero)
7. [Treasury Management](#7-treasury-management)
8. [KPIs y Metricas Financieras](#8-kpis-y-metricas-financieras)

---

## 1. Revenue Streams (Fuentes de Ingreso)

### 1.1 Trading Fees - Analisis de Modelos

#### Modelo A: Fee sobre Ganancias Netas (estilo Polymarket)

| Aspecto | Detalle |
|---|---|
| **Mecanica** | ~2% sobre las ganancias netas del usuario al resolver un mercado |
| **Ejemplo** | Usuario compra $100 en shares a $0.40, mercado resuelve SI, recibe $250, ganancia neta = $150, fee = $3.00 |
| **Revenue por $1M GMV** | ~$2,000-$4,000 (depende del ratio de ganancia promedio) |

**Pros:**
- Alineacion de incentivos: el usuario solo paga si gana
- Menor friccion para traders casuales
- Narrativa de marketing poderosa ("solo pagas si ganas")
- Modelo probado por Polymarket con traccion masiva

**Contras:**
- Revenue impredecible (depende de como resuelven los mercados)
- Take rate efectivo muy bajo (~0.2-0.4% del GMV)
- Dificil proyectar ingresos con precision
- Usuarios sofisticados pueden estructurar posiciones para minimizar "ganancias netas"

#### Modelo B: Fee por Trade (maker/taker)

| Aspecto | Detalle |
|---|---|
| **Mecanica** | Fee por cada operacion ejecutada en el orderbook |
| **Maker fee** | 0.5% (incentiva provision de liquidez) |
| **Taker fee** | 1.0% (penaliza tomar liquidez) |
| **Ejemplo** | Usuario compra $100 como taker: paga $1.00 de fee |
| **Revenue por $1M GMV** | ~$7,500-$10,000 (asumiendo 50/50 maker/taker) |

**Pros:**
- Revenue predecible y directamente proporcional al volumen
- Take rate mas alto (~0.75-1.0% del GMV)
- Modelo estandar en exchanges (facil de entender para inversores)
- Permite ajustes granulares (descuentos por volumen, tiers VIP)

**Contras:**
- Mayor friccion: el usuario paga incluso si pierde
- Desincentiva trading de alta frecuencia / small trades
- Competitivamente desventajoso vs Polymarket (que cobra 0 en trades)

#### Modelo C: Hibrido (RECOMENDADO)

| Componente | Fee | Logica |
|---|---|---|
| **Trading fee** | 0% maker / 0.5% taker | Competitivo, incentiva liquidez |
| **Settlement fee** | 2% sobre ganancias netas | Alineado con Polymarket, cobra al ganar |
| **Withdrawal fee** | $1.00 flat + gas | Cubre costos de on-chain settlement |

**Justificacion:** El modelo hibrido captura revenue de dos fuentes, mantiene competitividad con Polymarket en la fase de entrada al mercado, y permite reducir/eliminar el taker fee como herramienta de growth si es necesario. Take rate estimado: **0.5-0.8% del GMV**.

**Revenue estimado por $1M GMV (modelo hibrido): $5,000-$8,000**

> **[UPDATE April 2026] — Trading Fee Model Actually Implemented:**
>
> The platform implemented a **simplified flat fee model** instead of the recommended hybrid (Model C):
>
> | Planned (Hybrid) | Implemented |
> |---|---|
> | 0% maker / 0.5% taker | **0.5% flat fee on all trades** (no maker/taker distinction) |
> | 2% settlement fee on net profits | **Not implemented** — no settlement fee on winnings |
> | $1.00 flat + gas withdrawal fee | **PIX: $0.50 flat, USDC Polygon: $0.25 flat** (lower than planned, no gas pass-through) |
>
> **Key differences:**
> - The fee is calculated as `cost * 0.005` (0.5%) on every trade in `trading-panel.tsx`, applied uniformly regardless of order type (market or limit).
> - There is **no maker/taker fee split** — the order book does not distinguish between liquidity providers and takers for fee purposes.
> - The **settlement fee on net profits was dropped entirely**. This simplifies the model but reduces the take rate to a flat **0.5% of GMV** vs. the projected 0.5-0.8%.
> - **Revenue per $1M GMV is now ~$5,000** (flat 0.5%), at the low end of the hybrid estimate.
> - Withdrawal fees are significantly lower: $0.50 (PIX) and $0.25 (USDC) vs. the planned $1.00 + gas. This reduces friction but also eliminates withdrawal revenue as a meaningful line item.
> - **Settlement is in mock mode** — trades are recorded in the database but not yet settled on-chain (Polygon mainnet). Gas costs are therefore $0 currently.

### 1.2 Market Creation Fees

| Tipo de Mercado | Fee | Detalle |
|---|---|---|
| **Mercados creados por la plataforma** | Sin fee | Curados internamente, alto trafico |
| **Mercados creados por usuarios (Tier 1)** | $25 USD | Validacion basica, resolucion por UMA |
| **Mercados creados por usuarios (Tier 2)** | $10 USD | Descuento por volumen y reputacion |
| **Mercados custom/enterprise** | $100-$500 | Resolucion customizada, SLA de soporte |

**Revenue estimado mes 12:** $500-$2,000/mes (bajo volumen inicial de creacion de mercados por usuarios)

> **[UPDATE April 2026]** Market creation is admin-only via the admin panel (market CRUD + status management). User-created markets and the associated fee tiers were **not implemented** in MVP. All markets are curated internally. This revenue stream is $0 and deferred to a future phase.

### 1.3 API Access Tiers

| Tier | Precio | Incluye | Target |
|---|---|---|---|
| **Free** | $0/mes | 100 requests/min, datos de mercado basicos, websocket publico | Desarrolladores, bots hobby |
| **Pro** | $99/mes | 1,000 req/min, datos historicos completos, orderbook L2, webhooks | Traders algoritmicos, analistas |
| **Enterprise** | $499/mes | 10,000 req/min, datos L3, feed raw, soporte dedicado, SLA 99.9% | Fondos, market makers, integradores |
| **Market Maker** | Custom ($1,000-$5,000/mes) | Acceso colocado, latencia minima, fee rebates, linea directa | Market makers profesionales |

**Revenue estimado mes 12:** $1,000-$5,000/mes
**Revenue estimado mes 24:** $5,000-$25,000/mes

> **[UPDATE April 2026]** No public API or API access tiers were implemented. The backend exposes REST + WebSocket APIs for the frontend and admin panel only. There is no developer portal, API keys, or tiered access. This revenue stream is $0 and deferred to a future phase.

### 1.4 Premium Features

| Feature | Precio | Detalle |
|---|---|---|
| **Analytics Pro** | $19/mes | Graficos avanzados, heatmaps de volumen, flow analysis, correlaciones entre mercados |
| **Alertas Inteligentes** | $9/mes | Alertas de precio, volumen inusual, nuevos mercados por categoria, resolucion inminente |
| **Portfolio Tracker** | $14/mes | P&L detallado, tax reporting export, analisis de performance historico |
| **Bundle Premium** | $29/mes | Todo incluido + badge de perfil + acceso early a nuevos features |

**Revenue estimado mes 12:** $2,000-$8,000/mes (asumiendo 2-5% de conversion a premium)
**Revenue estimado mes 24:** $10,000-$40,000/mes

> **[UPDATE April 2026]** No premium subscription features were implemented. There are no analytics tiers, alert packages, portfolio tracker subscriptions, or premium bundles. The platform is free-to-use beyond trading fees. This revenue stream is $0 and deferred to a future phase.

### 1.5 Market Making (Spread Capture)

La plataforma puede actuar como market maker en mercados seleccionados para:
- Proveer liquidez inicial en mercados nuevos
- Capturar spread bid-ask (tipicamente 2-5% en prediction markets)
- Generar revenue adicional sobre capital propio

| Parametro | Valor |
|---|---|
| **Capital asignado** | $50,000-$200,000 |
| **Spread promedio capturado** | 2-4% |
| **Turnover mensual del capital** | 3-5x |
| **Gross profit mensual estimado** | $3,000-$40,000 |
| **Riesgo** | Medio-alto (exposicion a resolucion adversa) |

**Nota:** Esta actividad requiere capital significativo y modelos de riesgo. Se recomienda iniciar conservadoramente con max 10% del treasury y solo en mercados de alta liquidez.

> **[UPDATE April 2026]** Market making by the platform was **not implemented**. There is no automated market maker, no proprietary spread capture, and no capital allocation for this purpose. The order book relies entirely on user-submitted orders with in-memory matching (price-time priority). This revenue stream is $0.

### 1.6 Data Feed Comercial (Datos como Producto)

La informacion agregada de los mercados (probabilidades en tiempo real, volumen, historicos) tiene valor comercial para instituciones, medios de comunicacion, y analistas. Este revenue stream se activa en Phase 3 junto con la API publica.

| Tier | Precio | Incluye | Target |
|---|---|---|---|
| **Data Basic** | Incluido en API Pro ($99/mes) | Probabilidades actuales, historico 30 dias | Analistas, periodistas |
| **Data Premium** | $299/mes | Historico completo, datos de liquidez, orderbook snapshots, export CSV/JSON | Fondos, consultoras |
| **Data Enterprise** | $1,500-$2,500/mes | Feed real-time via WebSocket, datos granulares tick-level, SLA 99.9%, soporte dedicado | Medios (TV, prensa), instituciones financieras, plataformas de analytics |
| **Data Redistribution** | Custom ($5,000+/mes) | Licencia para redistribuir datos en productos propios | Agregadores, plataformas fintech |

**Revenue estimado mes 18:** $2,000-$8,000/mes
**Revenue estimado mes 24:** $10,000-$50,000/mes

**Por que es valioso:** Los prediction markets generan la "wisdom of crowds" — probabilidades que historicamente superan a encuestas y modelos expertos. Medios como Bloomberg y Reuters ya citan datos de Polymarket. Una plataforma que empaquete esto como producto tiene un revenue stream de alto margen con costo marginal casi cero.

> **[UPDATE April 2026]** Data feed comercial was **not implemented**. No data API, no data licensing, no commercial data products. Deferred to a future phase.

### 1.7 Resumen de Revenue Streams

| Fuente | % Revenue Estimado (Mes 12) | % Revenue Estimado (Mes 24) |
|---|---|---|
| Trading fees (hibrido) | 60-70% | 45-55% |
| Premium features | 10-15% | 12-18% |
| API access | 5-10% | 8-12% |
| Data feed comercial | 0% | 5-10% |
| Market making | 10-15% | 10-15% |
| Market creation fees | 2-5% | 3-5% |

> **[UPDATE April 2026] — Revenue Streams Actually Implemented:**
>
> Of the 6 planned revenue streams, only **1 is active** in the MVP:
>
> | Revenue Stream | Planned | Status |
> |---|---|---|
> | Trading fees | 60-70% of revenue | **ACTIVE** — 0.5% flat per trade (sole revenue source, 100% of revenue) |
> | Premium features | 10-15% | **NOT IMPLEMENTED** |
> | API access | 5-10% | **NOT IMPLEMENTED** |
> | Data feed comercial | 0% (M12) | **NOT IMPLEMENTED** |
> | Market making | 10-15% | **NOT IMPLEMENTED** |
> | Market creation fees | 2-5% | **NOT IMPLEMENTED** (admin-only market creation) |
>
> **Additional revenue-adjacent implementations not in original plan:**
> - **Referral program**: $5 bonus per qualified referral (`referral.service.ts`). This is a cost center (user acquisition), not a revenue stream, but was implemented as a growth mechanism.
> - **Blog CMS**: Content marketing infrastructure built into the platform for SEO/organic acquisition.
> - **Multi-language (EN/ES/PT)**: Supports the LATAM positioning described in Section 6.2.
> - **PWA**: Mobile distribution without app store fees — reduces CAC for mobile users.
>
> **Implication for financial projections:** All revenue projections in Section 4 assumed diversified revenue. With only trading fees active, the effective take rate is **0.5% of GMV** (not 0.65%), and there is no MRR from subscriptions. Revenue projections should be revised downward by ~25-35% at the same GMV levels.

---

## 2. Cost Structure (Estructura de Costos)

### 2.1 Equipo (Team)

Salarios estimados para equipo distribuido con base LATAM. Todos los montos son costos mensuales brutos incluyendo cargas sociales estimadas.

#### Fase 1: Pre-Launch / MVP (Meses 1-6)

| Rol | Cantidad | Salario USD/mes (por persona) | Total/mes |
|---|---|---|---|
| CTO / Lead Developer | 1 | $6,000 | $6,000 |
| Senior Fullstack Dev | 1 | $4,500 | $4,500 |
| Smart Contract Dev (Solidity) | 1 | $5,000 | $5,000 |
| Frontend Dev (React/Next.js) | 1 | $3,500 | $3,500 |
| DevOps / Infra | 0.5 (part-time) | $4,000 | $2,000 |
| Product / Founder | 1 | $3,000* | $3,000 |
| Legal / Compliance (externo) | 0.25 | $8,000 | $2,000 |
| Community Manager | 1 | $1,500 | $1,500 |
| **Total Equipo Fase 1** | | | **$27,500/mes** |

*Salario reducido del founder para conservar runway.

#### Fase 2: Post-Launch / Growth (Meses 7-18)

| Rol | Cantidad | Salario USD/mes (por persona) | Total/mes |
|---|---|---|---|
| CTO / Lead Developer | 1 | $7,000 | $7,000 |
| Senior Fullstack Dev | 2 | $5,000 | $10,000 |
| Smart Contract Dev | 1 | $5,500 | $5,500 |
| Frontend Dev | 2 | $4,000 | $8,000 |
| DevOps / Infra | 1 | $4,500 | $4,500 |
| Backend Dev | 1 | $4,500 | $4,500 |
| Product Manager | 1 | $4,000 | $4,000 |
| Head of BD (Dubai) | 1 | $5,000 | $5,000 |
| Legal / Compliance Officer | 1 | $4,000 | $4,000 |
| Community Manager | 1 | $2,000 | $2,000 |
| Marketing Lead | 1 | $3,500 | $3,500 |
| Customer Support | 1 | $1,500 | $1,500 |
| **Total Equipo Fase 2** | | | **$59,500/mes** |

#### Fase 3: Escalamiento (Meses 19-24)

| Rol | Cantidad | Salario USD/mes (por persona) | Total/mes |
|---|---|---|---|
| Equipo Tech (8 personas) | 8 | $5,250 avg | $42,000 |
| Equipo Ops/Legal (3 personas) | 3 | $4,000 avg | $12,000 |
| Equipo BD/Marketing (3 personas) | 3 | $3,500 avg | $10,500 |
| Equipo Soporte (2 personas) | 2 | $1,750 avg | $3,500 |
| C-Suite (2 founders) | 2 | $5,000 avg | $10,000 |
| **Total Equipo Fase 3** | | | **$78,000/mes** |

> **[UPDATE April 2026]** The MVP was built with a radically leaner approach than planned. Development was driven primarily by the founder (David) with Claude Code (AI pair programming), eliminating the need for the full engineering team projected in Fase 1. No salaries were paid for CTO, Senior Fullstack, Smart Contract Dev, Frontend Dev, or DevOps roles. The actual team cost during MVP development was a fraction of the $27,500/mes projected. This dramatically changes the bootstrapping math and runway calculations.

### 2.2 Infraestructura Tech

| Servicio | Proveedor | Costo Mes 1-6 | Costo Mes 7-12 | Costo Mes 13-24 |
|---|---|---|---|---|
| **Hosting Frontend** | Vercel (Pro) | $20/mes | $50/mes | $150/mes |
| **Backend / API** | Railway o AWS ECS | $100/mes | $300/mes | $800/mes |
| **Base de Datos** | Supabase Pro / RDS | $75/mes | $200/mes | $500/mes |
| **Redis Cache** | Upstash / ElastiCache | $30/mes | $100/mes | $250/mes |
| **RPC Nodes** | Alchemy Growth | $49/mes | $199/mes | $399/mes |
| **Indexer** | The Graph (hosted) / Goldsky | $0/mes | $100/mes | $300/mes |
| **CDN / Storage** | Cloudflare + S3 | $20/mes | $50/mes | $150/mes |
| **Monitoring** | Datadog / Sentry | $30/mes | $100/mes | $250/mes |
| **Dominio + DNS** | Cloudflare | $15/mes | $15/mes | $15/mes |
| **Email / Comms** | Resend + Slack | $25/mes | $50/mes | $100/mes |
| **CI/CD** | GitHub Actions | $20/mes | $50/mes | $100/mes |
| **Total Infra** | | **$384/mes** | **$1,214/mes** | **$3,014/mes** |

> **[UPDATE April 2026] — Actual Infrastructure Stack:**
>
> | Service | Planned | Actual |
> |---|---|---|
> | Frontend hosting | Vercel (Pro) | **Vercel** (forka.io) |
> | Backend / API | Railway o AWS ECS | **Railway** (Fastify API) |
> | Database | Supabase Pro / RDS | **Railway PostgreSQL 16** |
> | Redis Cache | Upstash / ElastiCache | **Railway Redis 7** |
> | RPC Nodes | Alchemy Growth | **Alchemy** (Polygon Amoy testnet only) |
> | Indexer | The Graph / Goldsky | **Not implemented** (mock settlement) |
> | CDN / Storage | Cloudflare + S3 | **Vercel Edge** (Next.js built-in) |
> | Monitoring | Datadog / Sentry | **Not implemented** (pino logging only) |
> | Email / Comms | Resend + Slack | **Resend** (email service implemented) |
> | CI/CD | GitHub Actions | **Auto-deploy from GitHub main** (Railway + Vercel) |
> | Admin panel | Not in original infra plan | **Vercel** (separate Vite + React app) |
>
> Actual infrastructure costs are likely in the $50-150/mes range during MVP, significantly below the $384/mes estimate.

### 2.3 Costos Blockchain

| Concepto | Estimacion Mes 1-6 | Estimacion Mes 7-12 | Estimacion Mes 13-24 |
|---|---|---|---|
| **Gas fees Polygon (trades)** | $50/mes (bajo volumen) | $300/mes | $1,500/mes |
| **Gas fees (market creation)** | $20/mes | $100/mes | $300/mes |
| **Gas fees (settlements)** | $30/mes | $200/mes | $800/mes |
| **Oracle costs (UMA bonds)** | $500/mes* | $1,000/mes | $2,000/mes |
| **Relayer / meta-tx gas** | $50/mes | $200/mes | $500/mes |
| **Total Blockchain** | **$650/mes** | **$1,800/mes** | **$5,100/mes** |

*Polygon gas fees son extremadamente bajos ($0.001-$0.01 por tx), pero a volumen alto se acumulan. UMA bonds requieren deposito de colateral (~$1,500 por disputa, recuperable si la resolucion es correcta).

**Calculo de gas por volumen:**
- Polygon avg gas price: ~30-50 gwei (~$0.001-$0.005 por tx)
- 1,000 trades/dia = ~$5/dia en gas = ~$150/mes
- 10,000 trades/dia = ~$50/dia en gas = ~$1,500/mes
- 50,000 trades/dia = ~$250/dia en gas = ~$7,500/mes

> **[UPDATE April 2026]** Blockchain costs are currently **$0**. Settlement is running in **mock mode** — trades are recorded in the database via `settlement.service.ts` using a `MockSettlementAdapter`, but no actual on-chain transactions occur. The platform uses Polygon Amoy testnet (not mainnet) for development. Contracts are deployed via Foundry (Solidity 0.8.28) but are not yet active for production settlement. UMA oracle integration is also not live, eliminating oracle bond costs. These costs will only materialize when mainnet settlement is enabled.

### 2.4 Compliance y Legal

| Concepto | Costo Mensual (Promedio Anual) |
|---|---|
| **KYC Provider (Sumsub)** | $500-$2,000/mes (segun volumen de verificaciones) |
| **Legal Counsel (Panama/BVI)** | $2,000-$5,000/mes (retainer) |
| **Auditoria Smart Contracts** | $1,000-$2,500/mes (amortizado: $15,000-$30,000/auditoria, 1-2/ano) |
| **Compliance Officer (incluido en equipo)** | $0 (incluido en salarios) |
| **Registro corporativo anual** | $200/mes (amortizado) |
| **Seguro D&O** | $500-$1,000/mes |
| **AML monitoring tools** | $200-$500/mes |
| **Total Compliance** | **$4,400-$11,200/mes** |

*Alineado con presupuesto compliance ano 1 de Phase 0.1: $245K-$515K = $20K-$43K/mes (incluye setup inicial).*

> **[UPDATE April 2026]** KYC is implemented with 3 tiers (tier_0, tier_1, tier_2) via Sumsub integration, but **mock mode is available** for development (`USE_MOCK` flags). Smart contract audits have not been performed. No D&O insurance or AML monitoring tools are in place. The legal structure (Panama/BVI) status should be cross-referenced with `01-legal-structure.md`. Actual compliance costs during MVP are minimal — Sumsub fees only apply when mock mode is off.

### 2.5 Marketing

| Concepto | Costo Mes 1-6 | Costo Mes 7-12 | Costo Mes 13-24 |
|---|---|---|---|
| **Community management** | $500/mes | $1,500/mes | $3,000/mes |
| **Contenido (blog, video)** | $500/mes | $1,000/mes | $2,000/mes |
| **Influencers / KOLs cripto** | $0 | $2,000/mes | $5,000/mes |
| **Paid ads (Twitter, crypto media)** | $0 | $1,500/mes | $5,000/mes |
| **PR / prensa** | $0 | $1,000/mes | $2,000/mes |
| **Referral program (rewards)** | $0 | $2,000/mes | $5,000/mes |
| **Eventos / sponsorships** | $0 | $500/mes | $2,000/mes |
| **Total Marketing** | **$1,000/mes** | **$9,500/mes** | **$24,000/mes** |

### 2.6 Otros Costos

| Concepto | Costo Mensual |
|---|---|
| **Contabilidad (firma externa)** | $500-$1,000/mes |
| **Software / SaaS (Notion, Figma, etc.)** | $300-$500/mes |
| **Banking fees (multi-currency)** | $100-$300/mes |
| **Viajes (BD, conferencias)** | $500-$2,000/mes |
| **Contingencias (5% del total)** | Variable |
| **Total Otros** | **$1,400-$3,800/mes** |

### 2.8 Costos Fiat Onramp (PSP)

La integracion con PSPs (MoonPay, Transak) para depositos fiat desde MVP introduce costos operativos adicionales. Los fees de conversion (1-4.5%) son pass-through al usuario y no afectan el P&L directamente, pero los costos de plataforma y compliance si.

| Concepto | Costo Mes 1-6 | Costo Mes 7-12 | Costo Mes 13-24 |
|---|---|---|---|
| **PSP platform fee** | $500/mes | $1,000/mes | $2,000/mes |
| **Fraud monitoring (Sardine/Sift)** | $0 | $500/mes | $1,500/mes |
| **Chargeback disputes (labor + liability)** | $0 | $500/mes | $2,000/mes |
| **KYC incremental (fiat tiers)** | $200/mes | $500/mes | $800/mes |
| **Total Fiat Onramp** | **$700/mes** | **$2,500/mes** | **$6,300/mes** |

**Nota:** Los fees de conversion PSP (1-4.5% segun metodo de pago) son absorbidos por el usuario (pass-through), no por la plataforma. La politica de no subsidiar PSP fees es critica para preservar margenes.

> **[UPDATE April 2026] — Fiat Onramp Actually Implemented:**
>
> Two PSPs were integrated (plus direct crypto), with 7 deposit methods total:
>
> | PSP | Methods | Fee Model |
> |---|---|---|
> | **MoonPay** (browser SDK overlay) | Card, Apple Pay, Google Pay | ~3.5% card fee (pass-through to user) |
> | **Mercado Pago** (Checkout Pro) | PIX, Boleto, Local Debit | ~1% PIX fee (pass-through to user) |
> | **Direct Crypto** | USDC on Polygon | 0% deposit fee |
>
> **Forka charges 0% deposit fee** — all PSP conversion fees are pass-through to the user, as planned.
>
> **Withdrawal methods (2 in MVP):**
> - PIX: $0.50 flat fee
> - USDC Polygon: $0.25 flat fee
>
> **KYC-gated deposit limits (from `limits.service.ts`):**
> - tier_0: $100 lifetime deposit limit
> - tier_1: $10,000/month
> - tier_2: Unlimited
>
> **KYC-gated withdrawal limits (from `withdrawal.service.ts`):**
> - tier_0: $50/day, $100/month, $50/tx
> - tier_1: $2,000/day, $10,000/month, $2,000/tx
> - tier_2: $25,000/day, $100,000/month, $25,000/tx
> - Minimum withdrawal: $5
> - Auto-approve threshold: $200 (above requires manual review)
>
> **Key difference from plan:** Transak was included in the payment provider enum but MoonPay and Mercado Pago are the active integrations. The PSP platform fees and fraud monitoring costs projected ($700-$6,300/mes) have not materialized at scale yet since the platform is in early deployment.

### 2.9 Resumen de Costos Mensuales por Fase

| Categoria | Fase 1 (M1-6) | Fase 2 (M7-12) | Fase 3 (M13-24) |
|---|---|---|---|
| Equipo | $27,500 | $59,500 | $78,000 |
| Infraestructura | $384 | $1,214 | $3,014 |
| Blockchain | $650 | $1,800 | $5,100 |
| Compliance | $6,000 | $7,000 | $8,000 |
| Marketing | $1,000 | $9,500 | $24,000 |
| Otros | $2,000 | $3,000 | $3,500 |
| **Total Mensual** | **$37,534** | **$82,014** | **$121,614** |
| **Total Anualizado** | **$225,204** (6 meses) | **$492,084** (6 meses) | **$1,459,368** (12 meses) |

**Costo total estimado 24 meses: ~$2,176,656**

> **[UPDATE April 2026]** Actual MVP costs are dramatically lower than projected. With AI-assisted development replacing a 6-person engineering team, minimal infrastructure costs on Railway/Vercel, zero blockchain costs (mock settlement), and no marketing spend yet, the actual monthly burn during MVP build is estimated at **$1,000-$3,000/mes** (infrastructure + tools only, excluding founder opportunity cost). The $37,534/mes Fase 1 projection was based on hiring a full team. The bootstrapping minimum of $192,000 was not needed — the MVP was built for a fraction of that amount.

---

## 3. Unit Economics

### 3.1 Lifetime Value (LTV) por Tipo de Usuario

#### Casual Trader

| Metrica | Valor |
|---|---|
| Avg monthly trading volume | $200 |
| Take rate (modelo hibrido) | 0.65% |
| Monthly revenue per user | $1.30 |
| Avg lifespan (meses) | 4 |
| Premium conversion | 1% |
| Premium revenue contribution | $0.12/mes |
| **LTV** | **$5.68** |

#### Regular Trader

| Metrica | Valor |
|---|---|
| Avg monthly trading volume | $2,000 |
| Take rate | 0.65% |
| Monthly revenue per user | $13.00 |
| Avg lifespan (meses) | 10 |
| Premium conversion | 15% |
| Premium revenue contribution | $4.35/mes |
| **LTV** | **$173.50** |

#### Power Trader

| Metrica | Valor |
|---|---|
| Avg monthly trading volume | $25,000 |
| Take rate | 0.55% (volumen descuento) |
| Monthly revenue per user | $137.50 |
| Avg lifespan (meses) | 18 |
| Premium/API conversion | 60% |
| Premium/API revenue contribution | $45/mes |
| **LTV** | **$3,285.00** |

#### Distribucion Estimada de Usuarios

| Segmento | % de Usuarios | % de Volumen | % de Revenue |
|---|---|---|---|
| Casual (< $500/mes) | 70% | 10% | 8% |
| Regular ($500 - $10K/mes) | 25% | 35% | 32% |
| Power (> $10K/mes) | 5% | 55% | 60% |

**LTV Ponderado: ~$52.40 por usuario**

### 3.2 Customer Acquisition Cost (CAC) por Canal

| Canal | CAC Estimado | % de Adquisicion | Calidad |
|---|---|---|---|
| **Organico (SEO, social)** | $2-$5 | 30% | Alta (usuarios con intencion) |
| **Referral program** | $8-$15 | 20% | Alta (social proof) |
| **Content marketing** | $5-$10 | 15% | Media-Alta |
| **Crypto Twitter / KOLs** | $10-$25 | 20% | Media (alta volatilidad) |
| **Paid ads (digital)** | $15-$40 | 10% | Media-Baja |
| **PR / prensa** | $3-$8 | 5% | Alta (credibilidad) |

**CAC Blended promedio: $8-$15**

### 3.3 Take Rate

| Escenario | Take Rate (% del GMV) | Benchmark |
|---|---|---|
| **Conservador** | 0.4% | Similar a Polymarket actual |
| **Base** | 0.65% | Modelo hibrido con taker fee |
| **Optimista** | 1.0% | Con premium features + API revenue |

**Target take rate: 0.65% del GMV (escenario base)**

> **[UPDATE April 2026]** The actual take rate is **0.5% flat** (trading fee only, no settlement fee). This is below the 0.65% base case and closer to the conservative 0.4% scenario. The LTV calculations in Section 3.1 should be revised: multiply all "Monthly revenue per user" figures by (0.5/0.65) = 0.77x. Additionally, with no premium features or API revenue, the premium revenue contributions in LTV calculations are currently $0. The blended LTV is likely lower than the projected $52.40.
>
> However, the CAC could also be lower if organic/referral channels (blog CMS, PWA, referral program with $5 bonus) drive acquisition without paid marketing spend.

### 3.4 Payback Period

| Segmento | CAC | Monthly Revenue | Payback Period |
|---|---|---|---|
| Casual | $10 | $1.30 | 7.7 meses |
| Regular | $12 | $17.35 | 0.7 meses |
| Power | $20 | $182.50 | 0.1 meses (3 dias) |
| **Blended** | **$11** | **$4.80** | **2.3 meses** |

### 3.5 Monthly Burn Rate

| Escenario | Burn Rate | Detalle |
|---|---|---|
| **Pre-revenue (M1-6)** | $37,534/mes | Solo costos, $0 revenue |
| **Early revenue (M7-12)** | $72,014/mes | $82,014 costos - $10,000 revenue |
| **Growth (M13-18)** | $81,614/mes | $121,614 costos - $40,000 revenue |
| **Scaling (M19-24)** | $21,614/mes | $121,614 costos - $100,000 revenue |

**Cash necesario para 24 meses sin fundraising adicional: ~$2.0M**

### 3.6 Metricas Clave de Unit Economics

| Metrica | Target | Status |
|---|---|---|
| LTV/CAC ratio | >3x | 4.8x (blended) - SALUDABLE |
| Payback period | <6 meses | 2.3 meses - EXCELENTE |
| Gross margin | >70% | ~75% (costos variables bajos) |
| Net margin (at scale) | >20% | Proyectado M24: 15-25% |

---

## 4. Proyeccion Financiera a 24 Meses

### 4.1 Supuestos Base

| Supuesto | Valor | Fuente/Logica |
|---|---|---|
| Polymarket daily volume (2025-2026) | $200M-$1B | Datos publicos, picos en eventos |
| Mercado total prediction markets diario | $300M-$1.2B | Polymarket + Kalshi + otros |
| Market share capturado ano 1 | 0.05-0.5% | Plataforma nueva, sin brand |
| Market share capturado ano 2 | 0.2-1.5% | Con traccion y marketing |
| Take rate promedio | 0.65% | Modelo hibrido |
| Conversion registro -> trader activo | 15% | Benchmark crypto apps |
| MAU/registro ratio | 25% despues de M6 | Benchmark mobile/web |
| Avg trades per MAU per month | 8 | Benchmark Polymarket |
| Avg trade size | $50 (casual) - $500 (power) | Distribucion 70/25/5 |

### 4.2 Escenario Conservador

*Supuesto: Crecimiento lento, funding limitado, mercado competitivo.*

| Mes | Usuarios Reg. | MAU | GMV Mensual | Revenue Trading | Revenue Premium | Revenue API | Revenue Total | Costos | EBITDA | Cash Position* |
|---|---|---|---|---|---|---|---|---|---|---|
| M1 | 0 | 0 | $0 | $0 | $0 | $0 | $0 | $37,534 | -$37,534 | $462,466 |
| M2 | 0 | 0 | $0 | $0 | $0 | $0 | $0 | $37,534 | -$37,534 | $424,932 |
| M3 | 0 | 0 | $0 | $0 | $0 | $0 | $0 | $37,534 | -$37,534 | $387,398 |
| M4 | 100 | 0 | $0 | $0 | $0 | $0 | $0 | $37,534 | -$37,534 | $349,864 |
| M5 | 300 | 50 | $10,000 | $65 | $0 | $0 | $65 | $37,534 | -$37,469 | $312,395 |
| M6 | 800 | 150 | $40,000 | $260 | $0 | $0 | $260 | $37,534 | -$37,274 | $275,121 |
| M7 | 1,500 | 350 | $120,000 | $780 | $200 | $99 | $1,079 | $82,014 | -$80,935 | $194,186 |
| M8 | 2,500 | 600 | $250,000 | $1,625 | $400 | $198 | $2,223 | $82,014 | -$79,791 | $114,395 |
| M9 | 4,000 | 950 | $450,000 | $2,925 | $700 | $297 | $3,922 | $82,014 | -$78,092 | $36,303 |
| M10 | 5,500 | 1,300 | $700,000 | $4,550 | $1,100 | $495 | $6,145 | $82,014 | -$75,869 | -$39,566 |
| M11 | 7,000 | 1,700 | $1,000,000 | $6,500 | $1,500 | $693 | $8,693 | $82,014 | -$73,321 | -$112,887 |
| M12 | 9,000 | 2,200 | $1,400,000 | $9,100 | $2,000 | $990 | $12,090 | $82,014 | -$69,924 | -$182,811 |
| M13 | 11,000 | 2,800 | $1,900,000 | $12,350 | $3,000 | $1,500 | $16,850 | $121,614 | -$104,764 | -$287,575 |
| M14 | 13,500 | 3,400 | $2,500,000 | $16,250 | $4,000 | $2,000 | $22,250 | $121,614 | -$99,364 | -$386,939 |
| M15 | 16,000 | 4,000 | $3,200,000 | $20,800 | $5,000 | $2,500 | $28,300 | $121,614 | -$93,314 | -$480,253 |
| M16 | 18,500 | 4,600 | $4,000,000 | $26,000 | $6,500 | $3,000 | $35,500 | $121,614 | -$86,114 | -$566,367 |
| M17 | 21,000 | 5,200 | $4,800,000 | $31,200 | $8,000 | $3,500 | $42,700 | $121,614 | -$78,914 | -$645,281 |
| M18 | 24,000 | 6,000 | $5,800,000 | $37,700 | $10,000 | $4,500 | $52,200 | $121,614 | -$69,414 | -$714,695 |
| M19 | 27,000 | 6,700 | $6,800,000 | $44,200 | $12,000 | $5,500 | $61,700 | $121,614 | -$59,914 | -$774,609 |
| M20 | 30,000 | 7,500 | $8,000,000 | $52,000 | $14,000 | $7,000 | $73,000 | $121,614 | -$48,614 | -$823,223 |
| M21 | 33,000 | 8,200 | $9,200,000 | $59,800 | $16,000 | $8,500 | $84,300 | $121,614 | -$37,314 | -$860,537 |
| M22 | 36,000 | 9,000 | $10,500,000 | $68,250 | $18,500 | $10,000 | $96,750 | $121,614 | -$24,864 | -$885,401 |
| M23 | 39,000 | 9,700 | $11,800,000 | $76,700 | $21,000 | $12,000 | $109,700 | $121,614 | -$11,914 | -$897,315 |
| M24 | 42,000 | 10,500 | $13,200,000 | $85,800 | $24,000 | $14,000 | $123,800 | $121,614 | $2,186 | -$895,129 |

*Cash position asume $500K de capital inicial. En este escenario se necesita fundraising adicional en M9-M10.

**Resumen Conservador Ano 1:** Revenue total ~$34,477 | Costos totales ~$717,288 | EBITDA: -$682,811
**Resumen Conservador Ano 2:** Revenue total ~$746,250 | Costos totales ~$1,459,368 | EBITDA: -$713,118

### 4.3 Escenario Base

*Supuesto: Seed round exitoso, crecimiento moderado, buen product-market fit.*

| Mes | Usuarios Reg. | MAU | GMV Mensual | Revenue Total | Costos | EBITDA | Cash Position* |
|---|---|---|---|---|---|---|---|
| M1 | 0 | 0 | $0 | $0 | $37,534 | -$37,534 | $1,462,466 |
| M2 | 0 | 0 | $0 | $0 | $37,534 | -$37,534 | $1,424,932 |
| M3 | 0 | 0 | $0 | $0 | $37,534 | -$37,534 | $1,387,398 |
| M4 | 200 | 30 | $5,000 | $33 | $37,534 | -$37,501 | $1,349,897 |
| M5 | 800 | 150 | $50,000 | $375 | $37,534 | -$37,159 | $1,312,738 |
| M6 | 2,000 | 500 | $200,000 | $1,600 | $37,534 | -$35,934 | $1,276,804 |
| M7 | 4,000 | 1,000 | $500,000 | $4,500 | $82,014 | -$77,514 | $1,199,290 |
| M8 | 7,000 | 1,800 | $1,000,000 | $9,000 | $82,014 | -$73,014 | $1,126,276 |
| M9 | 10,000 | 2,800 | $1,800,000 | $16,200 | $82,014 | -$65,814 | $1,060,462 |
| M10 | 14,000 | 4,000 | $3,000,000 | $27,000 | $82,014 | -$55,014 | $1,005,448 |
| M11 | 18,000 | 5,500 | $4,500,000 | $40,500 | $82,014 | -$41,514 | $963,934 |
| M12 | 23,000 | 7,000 | $6,500,000 | $58,500 | $82,014 | -$23,514 | $940,420 |
| M13 | 28,000 | 8,500 | $8,500,000 | $76,500 | $121,614 | -$45,114 | $895,306 |
| M14 | 34,000 | 10,000 | $11,000,000 | $99,000 | $121,614 | -$22,614 | $872,692 |
| M15 | 40,000 | 12,000 | $14,000,000 | $126,000 | $121,614 | $4,386 | $877,078 |
| M16 | 46,000 | 14,000 | $17,500,000 | $157,500 | $121,614 | $35,886 | $912,964 |
| M17 | 52,000 | 16,000 | $21,000,000 | $189,000 | $121,614 | $67,386 | $980,350 |
| M18 | 58,000 | 18,000 | $25,000,000 | $225,000 | $121,614 | $103,386 | $1,083,736 |
| M19 | 64,000 | 20,000 | $29,000,000 | $261,000 | $121,614 | $139,386 | $1,223,122 |
| M20 | 70,000 | 22,000 | $33,000,000 | $297,000 | $121,614 | $175,386 | $1,398,508 |
| M21 | 76,000 | 24,000 | $37,000,000 | $333,000 | $121,614 | $211,386 | $1,609,894 |
| M22 | 82,000 | 26,000 | $41,000,000 | $369,000 | $121,614 | $247,386 | $1,857,280 |
| M23 | 88,000 | 28,000 | $45,000,000 | $405,000 | $121,614 | $283,386 | $2,140,666 |
| M24 | 95,000 | 30,000 | $50,000,000 | $450,000 | $121,614 | $328,386 | $2,469,052 |

*Cash position asume $1.5M de capital (seed round).

**Resumen Base Ano 1:** Revenue total ~$157,708 | Costos totales ~$717,288 | EBITDA: -$559,580
**Resumen Base Ano 2:** Revenue total ~$2,988,000 | Costos totales ~$1,459,368 | EBITDA: $1,528,632
**Breakeven: Mes 15**

### 4.4 Escenario Optimista

*Supuesto: Viral growth, mercado favorable (elecciones 2026, eventos macro), partnership estrategica.*

| Mes | Usuarios Reg. | MAU | GMV Mensual | Revenue Total | Costos | EBITDA | Cash Position* |
|---|---|---|---|---|---|---|---|
| M1 | 0 | 0 | $0 | $0 | $37,534 | -$37,534 | $2,462,466 |
| M2 | 0 | 0 | $0 | $0 | $37,534 | -$37,534 | $2,424,932 |
| M3 | 0 | 0 | $0 | $0 | $37,534 | -$37,534 | $2,387,398 |
| M4 | 500 | 100 | $25,000 | $163 | $37,534 | -$37,371 | $2,350,027 |
| M5 | 2,000 | 500 | $200,000 | $1,500 | $37,534 | -$36,034 | $2,313,993 |
| M6 | 5,000 | 1,500 | $800,000 | $6,400 | $37,534 | -$31,134 | $2,282,859 |
| M7 | 12,000 | 4,000 | $2,500,000 | $22,500 | $82,014 | -$59,514 | $2,223,345 |
| M8 | 22,000 | 7,500 | $5,000,000 | $45,000 | $82,014 | -$37,014 | $2,186,331 |
| M9 | 35,000 | 12,000 | $10,000,000 | $90,000 | $82,014 | $7,986 | $2,194,317 |
| M10 | 50,000 | 18,000 | $18,000,000 | $162,000 | $82,014 | $79,986 | $2,274,303 |
| M11 | 68,000 | 25,000 | $28,000,000 | $252,000 | $82,014 | $169,986 | $2,444,289 |
| M12 | 90,000 | 35,000 | $42,000,000 | $378,000 | $82,014 | $295,986 | $2,740,275 |
| M13 | 110,000 | 42,000 | $55,000,000 | $495,000 | $155,000 | $340,000 | $3,080,275 |
| M14 | 130,000 | 50,000 | $70,000,000 | $630,000 | $155,000 | $475,000 | $3,555,275 |
| M15 | 155,000 | 60,000 | $88,000,000 | $792,000 | $155,000 | $637,000 | $4,192,275 |
| M16 | 180,000 | 70,000 | $105,000,000 | $945,000 | $175,000 | $770,000 | $4,962,275 |
| M17 | 210,000 | 82,000 | $125,000,000 | $1,125,000 | $175,000 | $950,000 | $5,912,275 |
| M18 | 240,000 | 95,000 | $150,000,000 | $1,350,000 | $200,000 | $1,150,000 | $7,062,275 |
| M19 | 275,000 | 110,000 | $175,000,000 | $1,575,000 | $200,000 | $1,375,000 | $8,437,275 |
| M20 | 310,000 | 125,000 | $200,000,000 | $1,800,000 | $225,000 | $1,575,000 | $10,012,275 |
| M21 | 350,000 | 140,000 | $230,000,000 | $2,070,000 | $225,000 | $1,845,000 | $11,857,275 |
| M22 | 390,000 | 155,000 | $260,000,000 | $2,340,000 | $250,000 | $2,090,000 | $13,947,275 |
| M23 | 430,000 | 170,000 | $290,000,000 | $2,610,000 | $250,000 | $2,360,000 | $16,307,275 |
| M24 | 475,000 | 190,000 | $325,000,000 | $2,925,000 | $275,000 | $2,650,000 | $18,957,275 |

*Cash position asume $2.5M de capital (seed + strategic).

**Resumen Optimista Ano 1:** Revenue total ~$957,563 | Costos totales ~$717,288 | EBITDA: $240,275
**Resumen Optimista Ano 2:** Revenue total ~$20,657,000 | Costos totales ~$2,485,000 | EBITDA: $18,172,000
**Breakeven: Mes 9**

### 4.5 Resumen Comparativo de Escenarios (Mes 24)

| Metrica | Conservador | Base | Optimista |
|---|---|---|---|
| Usuarios registrados | 42,000 | 95,000 | 475,000 |
| MAU | 10,500 | 30,000 | 190,000 |
| GMV mensual | $13.2M | $50M | $325M |
| GMV anualizado (run-rate) | $158M | $600M | $3.9B |
| Revenue mensual | $123,800 | $450,000 | $2,925,000 |
| Revenue anualizado (run-rate) | $1.5M | $5.4M | $35.1M |
| EBITDA mensual | $2,186 | $328,386 | $2,650,000 |
| Cash position | Requiere funding | $2.47M | $18.96M |
| Breakeven alcanzado | Mes 24 | Mes 15 | Mes 9 |
| Capital total requerido | $1.5M+ | $1.5M | $2.5M |

> **[UPDATE April 2026] — Projection Assumptions That Need Revising:**
>
> 1. **Take rate**: All three scenarios used 0.65% hybrid take rate. Actual is 0.5% flat. Revenue columns should be multiplied by ~0.77x at equivalent GMV.
> 2. **Revenue diversification**: Projections assumed premium features and API revenue contributing 15-25% by M12. These are $0. Revenue is 100% trading fees.
> 3. **Cost structure**: Actual costs are 90%+ lower than projected in Fase 1 due to AI-assisted solo development. This means breakeven is achievable at much lower GMV levels than projected.
> 4. **Fiat onramp timing**: The plan placed fiat onramp in Phase 2, but it was built into MVP (MoonPay + Mercado Pago). This is a positive deviation — the 7 deposit methods available from launch should improve the conversion funnel for non-crypto-native users.
> 5. **Settlement costs**: $0 currently (mock mode). When mainnet settlement is enabled, the blockchain cost line items will activate.
> 6. **The breakeven calculation fundamentally changes**: With ~$2,000/mes in actual costs (vs. $37,534 projected), the platform could break even with just $400K/mes in GMV (generating ~$2,000 in trading fees at 0.5%). The original projections required $6.5M/mes GMV to approach breakeven.

---

## 5. Fundraising Strategy

### 5.1 Bootstrapping: Capital Minimo para MVP + 6 Meses

| Concepto | Monto |
|---|---|
| Desarrollo MVP (4-5 meses, equipo lean) | $110,000 |
| Infraestructura + servicios (6 meses) | $4,000 |
| Legal setup (Fundacion Panama + OpCo) | $25,000 |
| Smart contract audit (1 ronda) | $15,000 |
| KYC provider setup + primeros meses | $5,000 |
| Marketing pre-launch + launch | $8,000 |
| Colchon de contingencia (15%) | $25,000 |
| **Total Bootstrapping Minimo** | **$192,000** |

**Con 6 meses de operacion post-launch adicionales:** $192,000 + ($82,014 x 6) - (~$20,000 revenue) = **$664,000**

**Recomendacion: $500K minimo para llegar a launch + 6 meses operativos con equipo lean.**

> **[UPDATE April 2026]** The MVP was built for dramatically less than the $192,000 bootstrapping minimum or the $500K recommendation. AI-assisted development (Claude Code) replaced the need for a hired engineering team during the build phase. The actual capital consumed to reach a deployable MVP is estimated at under $20,000 (infrastructure, domains, PSP setup, tools). This changes the fundraising calculus: a seed round is still valuable for growth/marketing/hiring, but the platform can operate at near-zero burn without one. The "lean startup" approach has been taken to an extreme that was not anticipated in the original plan.

### 5.2 Seed Round

| Parametro | Detalle |
|---|---|
| **Monto a levantar** | $1.5M - $2.5M |
| **Valuacion pre-money** | $6M - $10M |
| **Instrumento** | SAFE (Simple Agreement for Future Equity) con cap y descuento |
| **Cap** | $8M - $12M |
| **Descuento** | 20% |
| **Dilucion** | 15-25% |
| **Runway target** | 18-24 meses |

#### Perfil de Inversores Target

| Tipo | Ejemplos | Ticket | Por que |
|---|---|---|---|
| **Crypto VCs (Tier 2-3)** | Framework Ventures, Polychain (si no conflict), Pantera, Dragonfly | $250K-$1M | Entienden prediction markets, red en crypto |
| **Angel investors crypto** | Ex-founders de protocolos DeFi, traders prominentes | $25K-$100K | Validacion, red, early adopters |
| **Prediction market angels** | Ex-Polymarket, Augur, Gnosis team members | $10K-$50K | Expertise de dominio |
| **LATAM-focused VCs** | Kaszek, Canary, Platanus Ventures | $100K-$500K | Entienden equipo LATAM, menor valuacion |
| **Strategic angels** | Influencers crypto, periodistas, analistas | $5K-$25K | Distribucion, marketing organico |

#### Timeline de Fundraising

| Fase | Duracion | Actividad |
|---|---|---|
| Preparacion | 4-6 semanas | Deck, data room, modelo financiero, lista de targets |
| Outreach | 2-4 semanas | First meetings, warm intros |
| Diligence | 3-6 semanas | Follow-ups, demos, technical diligence |
| Cierre | 2-4 semanas | Term sheets, legales, wire |
| **Total** | **3-5 meses** | |

### 5.3 Use of Funds ($1.5M Seed)

| Categoria | Monto | % | Detalle |
|---|---|---|---|
| **Producto & Engineering** | $600,000 | 40% | Salarios equipo tech por 18 meses |
| **Operations & Legal** | $250,000 | 17% | Compliance, legal, admin, contabilidad |
| **Marketing & Growth** | $200,000 | 13% | Community, KOLs, contenido, referrals |
| **Market Making Capital** | $150,000 | 10% | Liquidez propia para bootstrap de mercados |
| **Infraestructura** | $100,000 | 7% | Cloud, blockchain, SaaS tools |
| **BD & Partnerships** | $100,000 | 7% | Viajes, eventos, Dubai operations |
| **Contingencia** | $100,000 | 6% | Buffer para imprevistos |
| **Total** | **$1,500,000** | **100%** | |

### 5.4 Token Sale: Analisis de Pros/Contras

#### Estructura Potencial

| Parametro | Detalle |
|---|---|
| **Token** | Governance + utility token (fee discounts, staking, market creation) |
| **Supply total** | 1,000,000,000 tokens |
| **Distribucion** | Team 20% (4-year vest, 1-year cliff), Investors 15%, Community/Airdrops 25%, Treasury/Ecosystem 30%, Advisors 5%, Public sale 5% |
| **Timing** | No antes de Mes 12-18 (post product-market fit) |
| **Mecanismo** | LBP (Liquidity Bootstrapping Pool) en Balancer o similar |
| **Target raise** | $3M-$10M |

#### Pros

| Pro | Detalle |
|---|---|
| Capital adicional significativo | $3M-$10M+ sin dilucion de equity tradicional |
| Community building | Token holders se convierten en evangelistas de la plataforma |
| Incentivos alineados | Traders con tokens tienen skin-in-the-game en el exito de la plataforma |
| Liquidez para equipo | Token vesting permite compensar equipo con upside |
| Gobernanza descentralizada | Alinea con la narrativa de la Fundacion de Interes Privado |
| Competitive moat | Polymarket no tiene token (todavia); esto podria ser un diferenciador |

#### Contras

| Contra | Detalle |
|---|---|
| **Riesgo regulatorio ALTO** | SEC puede clasificar como security. Howey test problematico. Jurisdiccion Panama mitiga pero no elimina. |
| Complejidad legal | Requiere opinion legal, estructura de token sale, compliance con regulaciones de cada jurisdiccion de compradores |
| Distraccion operativa | Token launch consume 2-4 meses de recursos del equipo |
| Presion de precio | Token holders esperan apreciacion, generando presion sobre el equipo |
| Mercado bajista | Si se lanza en bear market, la percepcion puede ser negativa |
| Costo de ejecucion | $50K-$150K en legales, market making, exchange listings |

#### Recomendacion sobre Token

**ESPERAR.** No lanzar token antes del Mes 18. Priorizar:
1. Product-market fit demostrado (GMV > $5M/mes consistente)
2. Base de usuarios estable (>10K MAU)
3. Revenue sostenible (breakeven o cerca)
4. Opinion legal completa de firma especializada (costo: $30K-$50K)
5. Mercado cripto favorable (no lanzar en bear market profundo)

Si se decide proceder, considerar un **"points program"** primero (sin token, sin riesgo regulatorio) que luego se pueda convertir en airdrop. Modelo probado por Blur, Jito, EigenLayer.

---

## 6. Analisis Competitivo Financiero

### 6.1 Tabla Comparativa

| Metrica | Polymarket | Kalshi | Augur v2 | Azuro | Nuestra Plataforma (M24 Base) |
|---|---|---|---|---|---|
| **Volumen diario (2025-2026)** | $200M-$1B+ | $5M-$15M | <$500K | $1M-$5M | $1.7M |
| **Volumen mensual** | $6B-$30B+ | $150M-$450M | <$15M | $30M-$150M | $50M |
| **Fee model** | 2% net profit | Per-contract ($0.01-$0.99) | 1% settlement | 2-5% | Hibrido (0.5% taker + 2% profit) |
| **Take rate** | ~0.2-0.4% | ~1-3% | ~0.5% | ~2-3% | ~0.65% |
| **Revenue mensual est.** | $12M-$60M+ | $2M-$10M | <$75K | $500K-$4M | $450K |
| **Usuarios registrados** | 500K-2M+ | 200K-500K | <10K | 50K-200K | 95K |
| **MAU** | 200K-800K+ | 50K-150K | <2K | 20K-80K | 30K |
| **Blockchain** | Polygon (+ Ethereum settle) | Centralized (CFTC) | Ethereum L1 | Multi-chain | Polygon |
| **KYC requerido** | No (wallet only) | Si (US regulated) | No | No | Tiered (Tier 0 browse, Tier 1-2 trade) |
| **Geo-restrictions** | US blocked (oficialmente) | US only (CFTC) | None | Varies | US + sanctioned blocked |
| **Funding total** | ~$74M+ | ~$130M+ | ~$5M (Forecast Foundation) | ~$10M+ | $1.5M (seed) |
| **Valuacion estimada** | $1B+ (2024) | $200M-$400M | N/A | $50M-$100M | $8M (pre-money seed) |
| **Ventaja competitiva** | Liquidez, brand, first-mover | Regulacion CFTC, US legal | Descentralizacion pura | Multi-chain, sports | [Ver abajo] |

### 6.2 Nuestras Ventajas Competitivas Potenciales

| Ventaja | vs Polymarket | vs Kalshi | vs Augur | vs Azuro |
|---|---|---|---|---|
| Mercados LATAM-focused | No atiende LATAM especificamente | Solo US | No focus geografico | Europa/Sports |
| KYC tiered (UX balance) | Sin KYC (riesgo regulatorio) | Full KYC (friccion alta) | Sin KYC | Sin KYC |
| Multi-idioma (ES/PT nativo) | Solo ingles | Solo ingles | Solo ingles | Solo ingles |
| Premium analytics | Basico | Decente | Inexistente | Basico |
| API robusta | Buena pero no monetizada | Buena, monetizada | Limitada | Buena |
| Costos operativos bajos (LATAM) | Team NYC/global (caro) | Team NYC (muy caro) | Volunteer-driven | Team Europa |

> **[UPDATE April 2026]** The competitive advantages listed above have been **partially validated** by the implementation:
> - **Multi-idioma (ES/PT nativo)**: Implemented (EN/ES/PT i18n).
> - **KYC tiered**: Implemented (3 tiers with progressive limits).
> - **Costos operativos bajos**: Even lower than projected — AI-assisted solo development further reduces costs vs. competitors.
> - **Mercados LATAM-focused**: Supported by Mercado Pago integration (PIX, Boleto, Local Debit) — unique among competitors.
> - **Premium analytics / API robusta**: NOT implemented — these differentiators are not yet available.
>
> **Additional competitive advantages not in original plan:**
> - **Fiat-first onboarding**: 7 deposit methods including non-crypto methods, lowering the barrier vs. Polymarket (wallet-only).
> - **PWA mobile distribution**: No app store dependency, instant install.
> - **Blog CMS**: Built-in content marketing for SEO — competitors rely on external content.
> - **Dual auth (wallet + email/Google)**: Users can onboard without a crypto wallet, unlike Polymarket.

### 6.3 Market Size & Share Analysis

| Metrica | 2024 | 2025 | 2026 (Est.) |
|---|---|---|---|
| **TAM global prediction markets** | $15B-$25B/ano | $30B-$60B/ano | $50B-$100B/ano |
| **SAM (crypto-native, non-US)** | $5B-$10B | $15B-$30B | $25B-$50B |
| **SOM (market share realista)** | N/A (pre-launch) | 0.01-0.05% | 0.1-0.5% |
| **GMV anualizado target M24** | - | - | $600M (base) |

---

## 7. Treasury Management

### 7.1 Politica de Reservas

| Categoria | Asignacion | Instrumento | Razon |
|---|---|---|---|
| **Reserva operativa** | 40% del treasury | USDC (Circle) | Liquidez inmediata para gastos operativos |
| **Reserva diversificada** | 30% del treasury | USDT (20%) + DAI (10%) | Diversificacion de riesgo de stablecoin |
| **Market making capital** | 15% del treasury | USDC en contratos de la plataforma | Capital de trabajo para liquidez |
| **Yield generation** | 10% del treasury | Protocolos DeFi de bajo riesgo (ver 7.3) | Rendimiento sobre capital idle |
| **Contingencia fiat** | 5% del treasury | Cuenta bancaria USD (Panama/BVI) | Emergencias, pagos que requieren fiat |
| **Chargeback reserve (fiat)** | 2-3% del volumen fiat mensual | USDC segregado | Cubrir chargebacks de depositos con tarjeta (ventana 120 dias) |
| **PSP settlement buffer** | 7 dias de volumen fiat | USDC | Cubrir gap de liquidacion PSP (T+2 a T+7) |

**Nota sobre fiat onramp:** Si el 30% del GMV entra via fiat, la chargeback reserve y el settlement buffer pueden representar $50K-$500K adicionales en capital inmovilizado segun el volumen. Este capital no genera yield y debe considerarse en la planificacion de runway.

**Regla clave:** Nunca mas del 50% del treasury en un solo stablecoin. USDC como base por ser el mas regulado y transparente (reservas auditadas por Deloitte).

### 7.2 Gestion de Liquidez para Market Making

| Parametro | Valor |
|---|---|
| **Capital maximo asignado** | 15% del treasury (hard cap) |
| **Mercados elegibles** | Solo mercados con volumen >$100K y time-to-resolution <90 dias |
| **Max exposicion por mercado** | 5% del capital de market making |
| **Spread minimo target** | 2% (bid-ask) |
| **Hedging** | Obligatorio para posiciones >$10K en un solo outcome |
| **P&L review** | Semanal |
| **Stop-loss por mercado** | -15% del capital asignado al mercado |

### 7.3 Estrategia de Yield sobre Treasury Idle

**Principios: Capital preservation > yield. Solo protocolos battle-tested, auditados, con >$500M TVL.**

| Protocolo | Tipo | APY Estimado | Riesgo | Asignacion Max |
|---|---|---|---|---|
| **Aave v3 (Polygon)** | Lending USDC | 3-5% | Bajo | 40% del allocation de yield |
| **Compound v3** | Lending USDC | 2-4% | Bajo | 30% del allocation de yield |
| **MakerDAO DSR** | DAI Savings Rate | 5-8% | Bajo-Medio | 20% del allocation de yield |
| **T-Bills tokenizados (Ondo/Mountain)** | RWA backed | 4-5% | Bajo | 10% del allocation de yield |

**Yield estimado anual (sobre 10% del treasury):**
- Treasury de $500K: yield allocation $50K, rendimiento ~$2,000-$3,000/ano
- Treasury de $2M: yield allocation $200K, rendimiento ~$8,000-$14,000/ano

**Restricciones:**
- No yield farming con APY >15% (alto riesgo de impermanent loss o exploit)
- No protocolos con <6 meses de track record
- No lock-ups mayores a 30 dias (liquidez)
- Revisiones de seguridad trimestrales de todos los protocolos en uso
- Emergency withdrawal plan documentado para cada posicion

### 7.4 Contingency Fund

| Escenario | Reserva Target | Accion |
|---|---|---|
| **Normal (burn < 50% de revenue)** | 6 meses de runway en reserva | Yield sobre excedente |
| **Precaucion (burn = 70-100% de revenue)** | 4 meses de runway en reserva | Reducir market making, cortar marketing discretional |
| **Emergencia (burn > revenue + cash <3 meses)** | Preservar 3 meses minimo | Congelar contrataciones, reducir equipo a core, emergency fundraising |
| **Critico (cash < 2 meses)** | Modo supervivencia | Reducir equipo a fundadores + 2 devs, eliminar todos los costos no esenciales |

**Triggers automaticos:**
- Si cash position < 6 meses de burn rate: alerta al board, revision de gastos
- Si cash position < 4 meses: congelamiento de hiring, revision de vendors
- Si cash position < 3 meses: plan de emergencia activado, fundraising urgente
- Si cash position < 2 meses: reestructuracion de equipo

> **[UPDATE April 2026]** Treasury management policies, yield strategies, and contingency frameworks described in this section are **not implemented**. With near-zero burn rate and no significant capital deployed, treasury management is not yet a priority. The chargeback reserve and PSP settlement buffer concepts are relevant once fiat deposit volume scales, but are not yet needed. These policies should be revisited when the platform reaches meaningful GMV.

---

## 8. KPIs y Metricas Financieras

### 8.1 Dashboard de Metricas Core

#### Metricas de Volumen y Revenue

| Metrica | Definicion | Frecuencia | Target M12 (Base) | Target M24 (Base) |
|---|---|---|---|---|
| **GMV (Gross Merchandise Volume)** | Valor total de trades ejecutados | Diario | $6.5M/mes | $50M/mes |
| **Net Revenue** | Revenue total despues de costos variables (gas, oracle) | Mensual | $55,000/mes | $440,000/mes |
| **Take Rate** | Net Revenue / GMV | Mensual | 0.65% | 0.65-0.80% |
| **EBITDA** | Revenue - todos los costos operativos | Mensual | -$23,514 | $328,386 |
| **EBITDA Margin** | EBITDA / Revenue | Mensual | -40% | 73% |
| **Gross Margin** | (Revenue - COGS) / Revenue | Mensual | 85% | 88% |
| **MRR (Monthly Recurring Revenue)** | Revenue de suscripciones (premium + API) | Mensual | $5,000 | $40,000 |
| **Revenue por empleado** | Revenue total / headcount | Mensual | $4,200 | $25,000 |

#### Metricas de Usuarios

| Metrica | Definicion | Frecuencia | Target M12 | Target M24 |
|---|---|---|---|---|
| **Usuarios registrados (total)** | Total acumulado de cuentas creadas | Semanal | 23,000 | 95,000 |
| **MAU (Monthly Active Users)** | Usuarios unicos con al menos 1 trade en 30 dias | Mensual | 7,000 | 30,000 |
| **DAU (Daily Active Users)** | Usuarios unicos con al menos 1 trade en 24h | Diario | 1,200 | 5,500 |
| **DAU/MAU ratio** | Stickiness del producto | Semanal | 17% | 18% |
| **New users/mes** | Registros nuevos por mes | Mensual | 5,000 | 7,000 |
| **Churn rate** | % de MAU que dejan de estar activos al mes siguiente | Mensual | <15% | <10% |

#### Metricas de Engagement

| Metrica | Definicion | Frecuencia | Target M12 | Target M24 |
|---|---|---|---|---|
| **Avg trade size** | GMV / numero de trades | Diario | $75 | $120 |
| **Trades per user per month** | Total trades / MAU | Mensual | 8 | 12 |
| **Avg positions per user** | Mercados activos por usuario | Semanal | 3 | 5 |
| **Time on platform** | Minutos promedio por sesion | Semanal | 8 min | 12 min |
| **Market creation rate** | Mercados nuevos creados / semana | Semanal | 15 | 40 |

#### Metricas de Retencion

| Metrica | Definicion | Target |
|---|---|---|
| **D1 Retention** | % de usuarios que vuelven al dia siguiente de registrarse | >30% |
| **D7 Retention** | % de usuarios activos a los 7 dias | >20% |
| **D30 Retention** | % de usuarios activos a los 30 dias | >12% |
| **D90 Retention** | % de usuarios activos a los 90 dias | >8% |
| **Cohort LTV curve** | Revenue acumulado por cohorte mensual a lo largo del tiempo | Creciente |

#### Metricas de Unit Economics

| Metrica | Definicion | Target |
|---|---|---|
| **LTV** | Lifetime Value ponderado por segmento | >$50 |
| **CAC** | Blended Customer Acquisition Cost | <$15 |
| **LTV/CAC ratio** | Eficiencia de adquisicion | >3.0x |
| **Payback period** | Meses para recuperar CAC | <3 meses |
| **ARPU (monthly)** | Revenue / MAU | >$7 |
| **ARPPU (monthly)** | Revenue / paying users (premium) | >$25 |

#### Metricas por Segmento de Usuario (Crypto vs Fiat)

| Metrica | Definicion | Frecuencia | Target M12 | Target M24 |
|---|---|---|---|---|
| **GMV by source** | Volumen separado crypto vs fiat | Diario | Fiat >30% del GMV | Fiat >40% del GMV |
| **ARPU by source** | Revenue/MAU por segmento | Mensual | Fiat ARPU >50% del crypto | Fiat ARPU >60% del crypto |
| **Retention by source** | D1/D7/D30 separados por origen | Semanal | D30 fiat >10% | D30 fiat >15% |
| **PnL by source** | Profit/loss promedio por segmento | Semanal | Fiat PnL > -10% | Fiat PnL > -5% |
| **Fiat deposit volume** | Total depositado via PSPs | Mensual | $500K/mes | $5M/mes |
| **Fiat conversion rate** | Visitors que completan deposito / que inician | Semanal | >60% | >70% |
| **Chargeback rate** | Chargebacks / total fiat transactions | Mensual | <1% | <0.5% |
| **Time to first trade by source** | Registro a primer trade (minutos) | Semanal | Fiat <5min | Fiat <3min |

### 8.2 Reporting Cadence

| Reporte | Frecuencia | Audiencia | Contenido |
|---|---|---|---|
| **Daily metrics** | Diario | Equipo interno | GMV, DAU, trades, uptime |
| **Weekly review** | Semanal | Leadership | KPIs, trends, issues, OKR progress |
| **Monthly P&L** | Mensual | Board / Investors | Revenue, costos, EBITDA, cash position, runway |
| **Quarterly board deck** | Trimestral | Board | Financials completos, strategy, hiring, competitive landscape |
| **Annual report** | Anual | Inversores + reguladores | Auditado (si aplica), compliance, governance |

### 8.3 Alertas y Thresholds

| Metrica | Green | Yellow | Red |
|---|---|---|---|
| **Cash runway** | >6 meses | 4-6 meses | <4 meses |
| **DAU/MAU** | >15% | 10-15% | <10% |
| **Take rate** | >0.5% | 0.3-0.5% | <0.3% |
| **D30 retention** | >12% | 8-12% | <8% |
| **LTV/CAC** | >3x | 2-3x | <2x |
| **EBITDA margin** (post-breakeven) | >20% | 10-20% | <10% |
| **Uptime** | >99.9% | 99-99.9% | <99% |
| **Monthly burn vs budget** | <100% | 100-115% | >115% |

> **[UPDATE April 2026]** The KPI framework and reporting cadence described above are **not yet operational**. The admin panel provides basic platform stats (user counts by KYC tier, market stats), but there is no automated KPI dashboard, no alerting system, and no formal reporting cadence. The metrics definitions and targets remain valid as goals, but the infrastructure to track them needs to be built. Key metrics that CAN be tracked from existing data:
> - GMV and trade volume (from `trades` table)
> - User registrations and KYC tier distribution (from `users` table)
> - Deposit/withdrawal volume by method (from `payment_transactions` and `withdrawals` tables)
> - Revenue from trading fees (calculable from trade volume * 0.5%)
> - Referral conversion (from `referrals` table)
>
> Metrics that CANNOT be tracked yet: DAU/MAU (no session tracking), retention cohorts, time on platform, LTV/CAC, ARPU.

---

## Apendice A: Glosario

| Termino | Definicion |
|---|---|
| **GMV** | Gross Merchandise Volume - valor total de todas las transacciones en la plataforma |
| **Take Rate** | Porcentaje del GMV que la plataforma retiene como revenue |
| **MAU/DAU** | Monthly/Daily Active Users |
| **LTV** | Lifetime Value - revenue total esperado de un usuario durante su vida en la plataforma |
| **CAC** | Customer Acquisition Cost - costo promedio de adquirir un nuevo usuario |
| **EBITDA** | Earnings Before Interest, Taxes, Depreciation, and Amortization |
| **MRR** | Monthly Recurring Revenue - ingresos recurrentes mensuales (suscripciones) |
| **ARPU** | Average Revenue Per User |
| **Burn rate** | Tasa de consumo de capital mensual |
| **Runway** | Meses de operacion restantes con el cash actual |

## Apendice B: Supuestos y Sensibilidad

### Variables Clave de Sensibilidad

| Variable | Rango | Impacto en Revenue M24 (Base) |
|---|---|---|
| **Take rate** | 0.4% - 1.0% | $300K - $750K/mes |
| **MAU** | 15K - 50K | $225K - $750K/mes |
| **Avg trade size** | $50 - $200 | $225K - $900K/mes |
| **Trades per user** | 5 - 15 | $180K - $675K/mes |
| **Premium conversion** | 1% - 8% | $4K - $70K/mes |

### Riesgos Clave a los Supuestos

| Riesgo | Probabilidad | Impacto | Mitigacion |
|---|---|---|---|
| Polymarket lanza funcionalidades que eliminan diferenciacion | Alta | Alto | Foco en nicho LATAM, features unicas |
| Regulacion adversa en Panama/BVI | Media | Alto | Estructura legal redundante, plan de migracion |
| Mercado crypto bear prolongado | Media | Alto | Revenue diversificado, costos LATAM bajos |
| Exploit de smart contracts | Baja | Muy Alto | Auditorias multiples, bug bounty, seguro |
| Equipo clave se va | Media | Alto | Vesting, cultura, compensacion competitiva LATAM |
| Oracle manipulation | Baja | Alto | UMA dispute mechanism, multi-oracle fallback |

---

*Documento preparado por la Direccion de Finanzas. Ultima actualizacion: Abril 2026.*
*Revision recomendada: Mensual durante los primeros 12 meses de operacion, trimestral despues.*

---

## [UPDATE April 2026] — Executive Summary of Plan vs. Reality

### What was built (MVP feature set)

| Feature | Status | Source |
|---|---|---|
| Trading fee: 0.5% flat per trade | Implemented | `trading-panel.tsx` — `cost * 0.005` |
| Deposit fees: 0% (PSP pass-through) | Implemented | MoonPay ~3.5% card, Mercado Pago ~1% PIX |
| Withdrawal fees: PIX $0.50, USDC $0.25 | Implemented | `withdrawal.service.ts` FEES constant |
| 7 deposit methods | Implemented | Card, Apple Pay, Google Pay, PIX, Boleto, Local Debit, USDC |
| 2 withdrawal methods | Implemented | PIX, USDC Polygon |
| KYC 3 tiers with limits | Implemented | `limits.service.ts`, `withdrawal.service.ts` |
| Referral: $5 per qualified referral | Implemented | `referral.service.ts` REFERRAL_BONUS |
| Settlement: mock mode | Implemented | `MockSettlementAdapter` (blockchain not on mainnet) |
| Blog CMS | Implemented | Content marketing infrastructure |
| Multi-language (EN/ES/PT) | Implemented | i18n |
| PWA | Implemented | Mobile distribution |
| Dual auth (wallet + email/Google) | Implemented | SIWE + email/password + Google OAuth |
| Admin panel with RBAC | Implemented | 3 roles: super_admin, admin, editor |

### What was NOT built (deferred from this plan)

| Feature | Planned Revenue Contribution | Status |
|---|---|---|
| Settlement fee (2% on net profits) | Part of hybrid model | Not implemented |
| User-created markets + fees | $500-$2,000/mes M12 | Not implemented |
| API access tiers | $1,000-$25,000/mes | Not implemented |
| Premium subscriptions | $2,000-$40,000/mes | Not implemented |
| Platform market making | $3,000-$40,000/mes | Not implemented |
| Data feed comercial | $2,000-$50,000/mes | Not implemented |
| On-chain settlement | Required for gas revenue | Mock mode only |
| KPI dashboard + alerting | Operational necessity | Not implemented |
| Token/governance | Deferred per plan | Not implemented (correct) |

### Key financial implications

1. **Revenue is simpler**: One stream (0.5% trading fee) vs. six projected. Take rate is 0.5% vs. 0.65% projected.
2. **Costs are radically lower**: ~$2,000/mes actual vs. $37,534/mes projected for Fase 1. AI-assisted development eliminated the need for a hired engineering team.
3. **Breakeven GMV is much lower**: ~$400K/mes GMV needed at current costs vs. $6.5M/mes projected.
4. **Fiat onramp is ahead of schedule**: Planned for Phase 2, built into MVP with 7 methods.
5. **Blockchain settlement is behind schedule**: Mock mode means no gas costs but also no on-chain finality.
6. **Fundraising needs are different**: The platform can operate near-zero burn without funding. A seed round is a growth accelerator, not a survival necessity.
