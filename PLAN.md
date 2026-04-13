# Forka — Project Plan & Status

**Last updated:** April 13, 2026

Forka is a prediction markets platform (Polymarket-style) targeting LATAM betting/gaming audiences with a fiat-first onboarding experience. Domain: forka.io. Twitter: @Forkad2026.

---

## 1. Platform Overview

Users bet on outcomes of real-world events (sports, politics, crypto prices) by buying YES/NO shares. Prices reflect crowd-estimated probabilities. Winners receive payouts; Forka collects a fee on winning positions.

**Core principle:** Fiat-first. The target audience comes from betting/gaming, not DeFi. Crypto powers the settlement layer but is invisible to most users. Forka never touches fiat directly — all fiat flows through PSPs (MoonPay, Mercado Pago) who convert to USDC before funds reach the platform.

---

## 2. Architecture

### 2.1 Repository Structure (5 repos, monorepo on disk)

| Repo | Tech | Deploy | GitHub |
|---|---|---|---|
| `backend/` | Fastify 5, TypeScript, Drizzle ORM, PostgreSQL 16, Redis 7, BullMQ, Socket.io | Railway | deividcingolani/praxis-backend |
| `frontend/` | Next.js 15, React 19, Tailwind v4, wagmi, RainbowKit | Vercel (forka.io) | deividcingolani/praxis-frontend |
| `admin/` | Vite + React, Tailwind, RBAC | Vercel | deividcingolani/praxis-admin |
| `contracts/` | Solidity 0.8.28, Foundry | Not deployed yet | deividcingolani/praxis-contracts |
| `docs/` | Markdown documentation | GitHub | deividcingolani/praxis-docs |

### 2.2 Tech Stack

| Layer | Technology |
|---|---|
| Blockchain | Polygon PoS (Amoy testnet for now) |
| Smart Contracts | Solidity 0.8.28 + Foundry |
| Tokens | ERC-1155 (Gnosis CTF fork) |
| Collateral | USDC |
| Oracle | UMA Optimistic Oracle V3 |
| Backend | Node.js + TypeScript + Fastify 5 |
| Database | PostgreSQL 16 + Drizzle ORM |
| Cache/Queue | Redis 7 + BullMQ |
| Real-time | Socket.io |
| Frontend | Next.js 15 + React 19 + TypeScript |
| Charts | TradingView Lightweight Charts |
| UI | Tailwind CSS v4 |
| Wallet | wagmi v2 + viem + RainbowKit |
| State | Zustand + TanStack Query |
| Payments | MoonPay (Card, Apple Pay, Google Pay) + Mercado Pago (PIX, Boleto, Local Debit) + USDC direct |
| Auth | SIWE (wallet) + email/password + Google OAuth (users); email/password + Google OAuth (admin) |

### 2.3 Key Architectural Principles

- **Fiat-first:** Default onboarding is email/Google registration + card deposit. Wallet connect is secondary.
- **Never touch fiat:** All fiat goes PSP -> USDC -> Forka. No USD or BRL in Forka accounts. Only EUR via PSP conversion.
- **PaymentProvider abstraction:** Trading engine operates on normalized internal units, agnostic to money origin.
- **Multibranding ready:** `brand_config` architecture supports multiple brands over a single shared order book. Only Forka brand active in MVP.
- **Balance integrity:** `user_balances` (available + locked) with `SELECT FOR UPDATE`, CHECK constraints >= 0, and double-entry `ledger_entries`.
- **Settlement mock mode:** Off-chain matching is live; on-chain settlement runs in mock mode (`BLOCKCHAIN_ENABLED=false`) until contracts are deployed.

---

## 3. Database Schema

**26 tables** defined in `backend/src/db/schema.ts`, **16 enums**:

| Table | Status |
|---|---|
| `users` | DONE |
| `admin_users` | DONE |
| `markets` | DONE |
| `outcomes` | DONE |
| `orders` | DONE |
| `trades` (with `settlement_status`) | DONE |
| `positions` | DONE |
| `market_prices` (OHLCV) | DONE |
| `market_tags` | DONE |
| `user_balances` (available + locked, CHECK >= 0) | DONE |
| `ledger_entries` (double-entry audit trail) | DONE |
| `payment_transactions` | DONE |
| `webhook_events` (idempotency) | DONE |
| `withdrawals` (with admin review workflow) | DONE |
| `user_payout_methods` | DONE |
| `referrals` | DONE |
| `push_subscriptions` | DONE |
| `market_proposals` | DONE |
| `market_comments` (threaded) | DONE |
| `market_reactions` | DONE |
| `blog_posts` | DONE |
| `password_reset_tokens` | DONE |

Full schema documentation: `docs/SCHEMA.md`

---

## 4. Feature Status

### 4.1 Smart Contracts

| Feature | Status | Notes |
|---|---|---|
| ConditionalTokens.sol (ERC-1155 CTF) | DONE | Written + tested |
| CTFExchange.sol (EIP-712, matching, fees) | DONE | Written + tested |
| UMACTFAdapter.sol (UMA Oracle) | DONE | Written + tested |
| Interfaces + Mocks (MockUSDC, MockUMAOracle) | DONE | |
| Foundry tests (27/27 passing) | DONE | 3 test files |
| Deploy script | DONE | |
| Slither static analysis | NOT DONE | Planned, never executed |
| Deploy to Amoy testnet | NOT DONE | Blocked on founder action |
| Deploy to Polygon mainnet | NOT DONE | After testnet validation |

### 4.2 Backend Services

**17 route files, 28+ service files** in `backend/src/`.

| Service | Route File | Status | Notes |
|---|---|---|---|
| Auth (SIWE + email/password + Google) | `auth.routes.ts` | DONE | Dual onboarding, JWT, account linking |
| Admin Auth (email + Google, RBAC) | `admin-auth.routes.ts` | DONE | Separate admin_users table |
| Admin (stats, users, markets, KYC) | `admin.routes.ts` | DONE | |
| Markets (CRUD, states, search) | `market.routes.ts` | DONE | |
| Orders + Matching Engine | `order.routes.ts` | DONE | In-memory order book, price-time priority, balance lock |
| Trades | `trade.routes.ts` | DONE | |
| Positions (P&L) | `position.routes.ts` | DONE | |
| Prices (OHLCV candles) | `price.routes.ts` | DONE | |
| Balances (credit/debit/lock/unlock) | `balance.routes.ts` | DONE | SELECT FOR UPDATE, ledger entries |
| Payments (MoonPay + Mercado Pago + crypto) | `payment.routes.ts` | DONE | Deposit flows complete |
| Webhooks (PSP callbacks) | `webhook.routes.ts` | DONE | HMAC signature validation, idempotency |
| Withdrawals | `withdrawal.routes.ts` | DONE | Schema + routes, anti-fraud rules defined |
| KYC (3 tiers, limits) | `kyc.routes.ts` | DONE | Mock mode; Sumsub partial |
| Referrals | `referral.routes.ts` | DONE | |
| Social (comments, reactions) | `social.routes.ts` | DONE | |
| Blog (CMS) | `blog.routes.ts` | DONE | |
| Push notifications | `push.routes.ts` | DONE | Web push subscriptions |
| WebSocket (Socket.io) | — | DONE | orderbook, trades, prices, user events |
| Settlement Service | — | IN PROGRESS | Adapter pattern (Mock + Blockchain), BullMQ workers, indexer. Running in mock mode. |
| Blockchain Service | — | DONE | Nonce mgmt, gas estimation, retry logic |
| Stuck TX Monitor | — | DONE | `stuck-tx-monitor.service.ts` |
| Reconciliation | — | DONE | `reconciliation.service.ts` |
| Limits Service | — | DONE | KYC-tier-based deposit/withdrawal limits |
| Email Service | — | DONE | `email.service.ts` |
| Payout Service | — | DONE | `payout.service.ts` |
| Market Proposals | — | DONE | User-submitted proposals + admin review |
| Resolution Service | — | DONE | `resolution.service.ts` |
| Sumsub Service | — | PARTIAL | Webhook exists, full integration flow incomplete |

**Backend tests:** 16 tests passing across 4 files (Vitest). Coverage is low — settlement mock, stuck TX, reconciliation tested. Balance, webhook, and matching engine tests missing.

### 4.3 Frontend Pages

**15 page directories** in `frontend/src/app/`:

| Page | Status | Notes |
|---|---|---|
| `/markets` (explorer) | DONE | Filters, search, sort, categories |
| `/markets/[id]` (detail) | DONE | TradingView charts, order book, trading panel, trade history |
| `/portfolio` | DONE | Positions, orders, P&L |
| `/profile` | DONE | User profile |
| `/kyc` | DONE | 3-step wizard + banner |
| `/blog` + `/blog/[slug]` | DONE | CMS-backed blog |
| `/referral` | DONE | Referral program page |
| `/analytics` | DONE | |
| `/event` | DONE | |
| `/docs` | DONE | 4 help/guide pages |
| `/terms` | DONE | Terms of service |
| `/privacy` | DONE | Privacy policy |
| `/cookies` | DONE | Cookie policy |
| `/disclaimer` | DONE | |
| `/aml-policy` | DONE | AML policy |
| `/reset-password` | DONE | Password reset flow |

**Frontend components built:** AuthModal (dual), DepositModal (MoonPay/Mercado Pago/crypto), TradingView price chart (area + candlestick + volume + timeframes), WebSocket real-time updates, design system (9 core components), i18n (EN/ES/PT), light/dark theme.

**Frontend gaps:** No BrandProvider/useCurrency abstraction, mobile responsive not tested, no E2E tests, no EIP-712 real signing (placeholder signatures).

### 4.4 Admin Panel

**9 page components** in `admin/src/pages/`:

| Page | Status | Notes |
|---|---|---|
| Login | DONE | email/password + Google OAuth |
| Dashboard | DONE | Platform stats |
| CreateMarket | DONE | Market creation form |
| Comments | DONE | Comment moderation |
| Proposals | DONE | User proposal review |
| Blog / BlogEditor | DONE | CMS for blog posts |
| Withdrawals | DONE | Withdrawal review queue |
| NotFound | DONE | 404 page |

**Admin RBAC:** 3 of 6 planned roles implemented (super_admin, admin, editor). Missing: Market Manager, Compliance Officer, Finance, Support, Viewer roles.

**Admin gaps:** No audit log, no 2FA for sensitive roles, no configuration UI (brand configs, fees, geo-blocking).

---

## 5. Phase 0: Foundation — DONE

| Deliverable | Status |
|---|---|
| 0.1 Legal structure (5 jurisdictions, KYC, AML, ToS, geo-blocking) | DONE |
| 0.2 Business model (fees, unit economics, projections, fundraising) | DONE |
| 0.3 Brand identity (Forka, guidelines, GTM, i18n, multi-branding config) | DONE |
| 0.4 Architecture (diagrams, data model, API design, infra) | DONE |
| CI/CD pipeline | NOT DONE — designed but `.github/workflows/` never created |
| Fiat onramp regulation section (Legal 0.1 addendum) | NOT DONE |
| Fiat costs in business model (Finance 0.2 addendum) | NOT DONE |
| Vocabulary guidelines (Copywriter 0.3 addendum) | NOT DONE |

Documentation in `docs/phase-0/`: legal structure, business model, brand identity, architecture.

---

## 6. Phase 1: MVP Core — ~85% DONE

### What is built and working:
- Full database schema (26 tables, all migrations)
- Complete backend API (17 route files, 28+ services)
- Order matching engine (in-memory, price-time priority, balance locking)
- Payment system (MoonPay + Mercado Pago + USDC deposits)
- Withdrawal system (schema, routes, anti-fraud rules, admin review queue)
- Balance system (available/locked, SELECT FOR UPDATE, ledger entries, CHECK constraints)
- Auth dual (SIWE wallet + email/password + Google OAuth)
- Admin panel (dashboard, markets CRUD, users, KYC review, withdrawals, blog CMS, proposals)
- Frontend (15 pages, TradingView charts, trading panel, portfolio, deposit modal, WebSocket real-time)
- Smart contracts (3 contracts written + 27 tests passing)
- Settlement service infrastructure (adapter pattern, BullMQ workers, indexer, stuck TX monitor, reconciliation)
- 3-tier KYC system with limits enforcement
- Referral system, social features (comments, reactions), blog CMS, push notifications

### What is NOT built / incomplete:
- Smart contracts not deployed (not even to testnet)
- Settlement running in mock mode (no real on-chain execution)
- No EIP-712 real signing in frontend
- Sumsub KYC integration incomplete (webhook exists, full flow missing)
- No geo-blocking
- No CI/CD pipeline
- No E2E tests, minimal unit tests (16 tests)
- No Slither analysis on contracts
- No BrandProvider/useCurrency frontend abstraction
- Admin RBAC partial (3/6 roles), no audit log, no 2FA

---

## 7. Blockers for Mainnet Launch

These 7 items require founder decisions or external actions before the platform can handle real money:

| # | Blocker | Why It Matters | Effort |
|---|---|---|---|
| 1 | **Deploy contracts to Amoy testnet** | Cannot test real settlement without deployed contracts | 1-2 days |
| 2 | **Fund operator wallet** (MATIC for gas + USDC for collateral) | Settlement service needs gas to submit TXs | 1 hour + funds |
| 3 | **Activate `BLOCKCHAIN_ENABLED=true`** and test E2E settlement | Currently all trades settle in mock mode — no on-chain proof | 1 week testing |
| 4 | **Sumsub KYC account + API keys** | Cannot run real identity verification without a Sumsub contract | External signup + 2-3 days integration |
| 5 | **MoonPay + Mercado Pago production API keys** | Currently using test/sandbox keys — no real deposits possible | External approval process |
| 6 | **Legal entity formation** (jurisdiction: Panama, BVI, or Malta) | Need a legal entity to sign PSP agreements, hold treasury, accept ToS liability | Founder decision + weeks |
| 7 | **Smart contract security audit** | Handling real funds without audit is unacceptable risk — Slither at minimum, external audit ideal | 1-2 weeks (Slither), 4-8 weeks (external) |

---

## 8. Phase 2: Growth — PLANNED

| Feature | Status | Notes |
|---|---|---|
| Multi-outcome markets (NegRiskAdapter) | PLANNED | New contracts + backend + UI |
| Batch orders (up to 15/request) | PLANNED | |
| GTC/GTD order types | PLANNED | |
| Notifications (in-app + email) | PARTIAL | Push subscriptions + email service exist |
| Price alerts | PLANNED | |
| Proxy Wallet + Gas Abstraction (ERC-4337) | PLANNED | AWS KMS key management designed |
| PWA + mobile optimization | PLANNED | Responsive classes exist, not tested |
| Social features (leaderboard, public profiles) | PARTIAL | Comments + reactions done; leaderboard, profiles planned |
| Referral program | DONE | Schema + routes + frontend page built |
| Marketing launch campaign | PLANNED | GTM strategy documented in brand identity |

---

## 9. Phase 3: Scale — PLANNED

| Feature | Status |
|---|---|
| Dedicated matching engine (Rust/optimized worker) | PLANNED |
| Read replicas, Redis Cluster | PLANNED |
| Public REST + WebSocket API with API keys | PLANNED |
| TypeScript + Python SDKs | PLANNED |
| Scalar/range markets, combinatorial markets | PLANNED |
| User-created markets | PARTIAL — market_proposals table + admin review exists |
| Bug bounty (Immunefi) | PLANNED |
| External security audit | PLANNED |
| Data feed commercial product | PLANNED |

---

## 10. Timeline Estimate

| Phase | Duration | Status | Key Milestone |
|---|---|---|---|
| Phase 0: Foundation | Weeks 1-4 | DONE | Architecture + legal + brand |
| Phase 1: MVP Core | Weeks 5-18 | ~85% DONE | Off-chain platform functional. On-chain settlement blocked on contract deployment. |
| Testnet launch | ~2-3 weeks from contract deploy | BLOCKED | Requires blockers #1-3 resolved |
| Mainnet MVP | ~4-6 weeks from testnet | BLOCKED | Requires all 7 blockers resolved |
| Phase 2: Growth | 12 weeks | NOT STARTED | Multi-outcome + mobile + marketing launch |
| Phase 3: Scale | 12 weeks | NOT STARTED | Public API + advanced markets + performance |

**Current state as of April 13, 2026:** The off-chain platform is substantially built. The database, backend API, frontend, admin panel, and smart contract code all exist and are deployed (backend on Railway, frontend + admin on Vercel). The critical gap is the on-chain layer: contracts are written and tested but not deployed, so all trades settle in mock mode. Bridging this gap — deploying contracts, funding wallets, testing real settlement — is the next milestone and requires founder action.

---

## 11. Risk Register

| Risk | Prob. | Impact | Mitigation |
|---|---|---|---|
| Smart contract exploit | Medium | Critical | Audit, bug bounty, circuit breaker, TVL caps |
| Regulatory action | Medium | High | Offshore entity, geo-blocking, retained counsel |
| Low liquidity / empty order books | High | High | Seed capital, market maker partnerships |
| Oracle manipulation | Low | Critical | UMA dispute mechanism, manual override |
| Key-person dependency | Medium | Medium | Documentation, code reviews |
| Polygon congestion | Low | Medium | Retry logic, graceful degradation |
| Fiat PSP country restrictions | Medium | High | 2+ PSPs (MoonPay + Mercado Pago), fallback |
| Proxy wallet key compromise | Medium | Critical | HSM/KMS (AWS KMS), hot/cold separation |
| PSP webhook forgery | Medium | Critical | Signature validation, idempotency table, IP allowlist |
| Balance manipulation (race condition) | Medium | High | SELECT FOR UPDATE, CHECK constraints, double-entry ledger |
| Money Transmitter classification | Medium | Critical | Forka never touches fiat. PSP->USDC only. No USD/BRL. |
| Chargebacks on fiat deposits | Medium | High | 3DS mandatory, 14-day hold, 2-3% chargeback reserve |

---

## 12. Critical Files

| File | Role |
|---|---|
| `contracts/src/CTFExchange.sol` | Core exchange contract |
| `backend/src/services/order.service.ts` | Matching engine (most critical for correctness) |
| `backend/src/services/settlement.service.ts` | Off-chain to on-chain bridge |
| `backend/src/services/balance.service.ts` | Balance mutations (money integrity) |
| `backend/src/services/blockchain.service.ts` | TX manager (nonce, gas, retry) |
| `backend/src/db/schema.ts` | All 26 tables |
| `frontend/src/app/markets/[id]/page.tsx` | Market detail page (highest traffic) |

---

## 13. Documentation Index

| Document | Path |
|---|---|
| This plan | `docs/PLAN.md` |
| Gaps & pendientes | `docs/GAPS.md` |
| Database schema reference | `docs/SCHEMA.md` |
| Deposit & withdrawal spec | `docs/specs/deposit-withdrawal.md` |
| Settlement service spec | `docs/specs/settlement-service.md` |
| Legal structure | `docs/phase-0/01-legal-structure.md` |
| Business model | `docs/phase-0/02-business-model.md` |
| Brand identity | `docs/phase-0/03-brand-identity.md` |
| System architecture | `docs/phase-0/04-architecture.md` |
| UX design | `docs/phase-1/04-ux-design.md` |
