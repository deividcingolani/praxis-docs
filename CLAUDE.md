# Praxis — Prediction Markets Platform

## Project Structure
- Monorepo with pnpm workspaces + Turborepo
- packages/contracts: Solidity smart contracts (Foundry)
- packages/backend: Fastify API server (TypeScript, Drizzle ORM)
- packages/frontend: Next.js 15 web app (TypeScript, Tailwind v4)
- packages/shared: Shared types and constants
- Admin panel: Separate repo (planned, not yet created)

## Tech Stack
- Blockchain: Polygon, Solidity 0.8.28, Foundry
- Backend: Fastify 5, Drizzle ORM, PostgreSQL 16, Redis 7, BullMQ, Socket.io
- Frontend: Next.js 15, React 19, Tailwind v4, wagmi, RainbowKit, TradingView Lightweight Charts
- Auth: SIWE (wallet) + email/password + Google OAuth (dual onboarding)
- Payments: PaymentProvider interface — CryptoProvider (USDC/Polygon) + PSP providers (MoonPay, Transak) for fiat
- Admin: Separate app with email+password+Google auth, RBAC roles

## Key Architectural Principles
- **Praxis NEVER touches fiat directly.** All fiat flows through PSPs (MoonPay/Transak) who convert to USDC before funds reach Praxis. This avoids Money Transmitter classification.
- **Currencies:** USDC (crypto) + EUR via PSP→USDC. NO USD (breaks US zero-nexus), NO BRL (contradicts Brazil geo-blocking).
- **PaymentProvider abstraction:** Trading engine operates on normalized internal units, agnostic to money origin (crypto or fiat).
- **Multibranding ready:** `brand_config` table supports multiple brands over a single shared order book. Only Praxis brand active in MVP.
- **Balance model:** `user_balances` (available + locked) with `SELECT FOR UPDATE` for all mutations. `ledger_entries` for double-entry audit trail.
- **Auth dual:** Users can register with wallet (SIWE) OR email/Google. `user_auth_methods` table supports multiple methods per user.

## Key Commands
- `pnpm install` — Install all dependencies
- `pnpm --filter @praxis/shared build` — Build shared package (required before backend/frontend)
- `pnpm --filter @praxis/backend exec tsc --noEmit` — Typecheck backend
- `pnpm --filter @praxis/frontend exec next build` — Build frontend
- `cd packages/contracts && forge test` — Run contract tests
- `docker compose up -d postgres redis` — Start local DB and Redis
- `pnpm --filter @praxis/backend db:push` — Push DB schema changes
- `pnpm --filter @praxis/backend db:migrate` — Run DB migrations
- `pnpm --filter @praxis/backend dev` — Start backend in dev mode
- `pnpm --filter @praxis/frontend dev` — Start frontend in dev mode

## Architecture Notes
- Order matching happens in-memory (order.service.ts), persisted to DB
- Settlement is async via BullMQ workers (settlement on-chain)
- WebSocket channels: orderbook:{marketId}, trades:{marketId}, prices:{marketId}
- KYC has 3 tiers: tier_0 (browse + first deposit up to $100), tier_1 ($10K/mo), tier_2 (unlimited)
- Mock mode available for KYC and trading (USE_MOCK flags)
- PSP webhooks: received at /webhooks/moonpay and /webhooks/transak, processed async via BullMQ with idempotency (webhook_events table)
- Proxy wallets (Phase 2): AWS KMS envelope encryption for private keys, never stored in plaintext

## Services (Backend)
- **User Service**: Auth (SIWE + email/Google), user CRUD
- **Market Service**: CRUD markets, states, categories, search
- **Order Service + Matching Engine**: In-memory order book, price-time priority
- **Trade Service**: Trade recording and history
- **Position Service**: User positions, P&L calculations
- **Price Service**: OHLCV candles, mid-price, tick data
- **Payment Service** (planned): PaymentProvider interface, BalanceService (lock/unlock/credit/debit), PSP integration
- **Settlement Service** (planned): On-chain settlement via CTFExchange
- **KYC Service**: 3 tiers, mock mode

## Admin Panel (planned, separate repo)
- Auth: email+password + Google OAuth + 2FA for sensitive roles
- RBAC roles: Super Admin, Market Manager, Compliance Officer, Finance, Support, Viewer
- Features: Market CRUD, KYC review queue, payment approval, dashboards, audit log
- Separate `admin_users` table (admins are not traders)

## Package Dependencies
- @praxis/shared must be built before backend or frontend (they depend on it)
- Turborepo handles this automatically via `dependsOn: ["^build"]` in turbo.json

## Docker
- `docker compose up -d postgres redis` — Start infrastructure only
- `docker compose up` — Start everything including backend
- Backend Dockerfile uses multi-stage build from repo root context

## CI/CD
- GitHub Actions CI runs on push to main/develop and PRs to main
- Deployment is manual via workflow_dispatch (staging or production)
- Contracts are tested independently with Foundry

## Known Bugs (see GAPS.md for full list)
- `users.address` is NOT NULL in Drizzle schema — blocks fiat-only users. Must be nullable.
- Matching engine does not verify user balance before creating orders
- Matching engine does not wrap operations in DB transaction
- Zero backend tests exist (no Vitest configured)

## Documentation
- docs/PLAN.md — Master plan with phases, timeline, risk register
- docs/GAPS.md — All gaps and bugs ordered by priority
- docs/phase-0/01-legal-structure.md — Legal analysis, jurisdictions, KYC, AML
- docs/phase-0/02-business-model.md — Revenue, costs, treasury, projections
- docs/phase-0/03-brand-identity.md — Brand, positioning, GTM, personas
- docs/phase-0/04-architecture.md — System architecture, data model, API design, infra
- docs/phase-1/04-ux-design.md — User flows, interaction specs, responsive specs
