# Forka — Prediction Markets Platform

## Project Structure
- backend/ — Fastify API server (TypeScript, Drizzle ORM) — GitHub: deividcingolani/praxis-backend — Deploy: Railway
- frontend/ — Next.js 15 web app (TypeScript, Tailwind v4) — GitHub: deividcingolani/praxis-frontend — Deploy: Vercel (forka.io)
- admin/ — Vite + React admin panel — GitHub: deividcingolani/praxis-admin — Deploy: Vercel (praxis-admin.vercel.app)
- contracts/ — Solidity smart contracts (Foundry) — GitHub: deividcingolani/praxis-contracts
- docs/ — Project documentation — GitHub: deividcingolani/praxis-docs

## Tech Stack
- Blockchain: Polygon, Solidity 0.8.28, Foundry
- Backend: Fastify 5, Drizzle ORM, PostgreSQL 16, Redis 7, Socket.io
- Frontend: Next.js 15, React 19, Tailwind v4, wagmi, RainbowKit
- Auth: SIWE (wallet) + email/password + Google OAuth — dual onboarding for users. Admin panel has separate email+Google auth.
- Payments: MoonPay (Card, Apple Pay, Google Pay — browser SDK overlay) + Mercado Pago (Checkout Pro — LATAM fiat) + Crypto (USDC on Polygon)
- Admin: Vite + React, email+password + Google OAuth, RBAC with 3 roles (super_admin, admin, editor)

## Key Architectural Principles
- **Forka NEVER touches fiat directly.** All fiat flows through PSPs (MoonPay/Mercado Pago) who convert to USDC before funds reach Forka. This avoids Money Transmitter classification.
- **Currencies:** USDC (crypto) + EUR via PSP→USDC. NO USD (breaks US zero-nexus), NO BRL (contradicts Brazil geo-blocking).
- **PaymentProvider abstraction:** Trading engine operates on normalized internal units, agnostic to money origin (crypto or fiat).
- **Multibranding ready:** `brand_config` table supports multiple brands over a single shared order book. Only Forka brand active in MVP.
- **Balance model:** `user_balances` (available + locked) with `SELECT FOR UPDATE` for all mutations. `ledger_entries` for double-entry audit trail.
- **Auth dual:** Users can register with wallet (SIWE) OR email/Google. `user_auth_methods` table supports multiple methods per user.

## Key Commands
- `cd backend && pnpm dev` — Start backend dev server
- `cd backend && pnpm typecheck` — Typecheck backend
- `cd frontend && pnpm dev` — Start frontend dev server
- `cd frontend && pnpm build` — Build frontend
- `cd admin && pnpm dev` — Start admin panel (port 3001)
- `cd contracts && forge test` — Run contract tests

## Architecture Notes
- Order matching happens in-memory (order.service.ts), persisted to DB
- WebSocket channels: orderbook:{marketId}, trades:{marketId}, prices:{marketId}
- KYC has 3 tiers: tier_0 (browse + first deposit up to $100), tier_1 ($10K/mo), tier_2 (unlimited)
- Deposit limits enforced per KYC tier: tier_0=$100 lifetime, tier_1=$10K/month, tier_2=unlimited. Withdrawals blocked for tier_0.
- Mock mode available for KYC and trading (USE_MOCK flags)
- PSP webhooks processed synchronously in route handlers with idempotency via webhook_events table
- Proxy wallets (Phase 2): AWS KMS envelope encryption for private keys, never stored in plaintext

## Services (Backend)
- **Auth Service**: SIWE (wallet) + email/password + Google OAuth for users. Separate admin auth.
- **Market Service**: CRUD markets, states, categories, search
- **Order Service + Matching Engine**: In-memory order book, price-time priority
- **Trade Service**: Trade recording and history
- **Position Service**: User positions, P&L calculations
- **Price Service**: OHLCV candles, mid-price, tick data
- **Payment Service**: MoonPay signed URLs + browser SDK, Mercado Pago Checkout Pro, deposit/withdrawal flows, KYC-gated limits
- **Balance Service**: credit/debit/lock/unlock with SELECT FOR UPDATE, ledger entries
- **Limits Service**: KYC-tier-based deposit/withdrawal limits
- **Admin Service**: Platform stats, user management, market management, KYC review
- **Settlement Service** (planned): On-chain settlement via CTFExchange
- **KYC Service**: 3 tiers, mock mode

## Admin Panel
Admin panel exists at admin/. Auth: email+password + Google OAuth (separate admin_users table). RBAC: 3 roles (super_admin, admin, editor). Features: Dashboard with platform stats, Market CRUD + status management + resolution, User listing, KYC approve/reject.

## CI/CD
No CI/CD pipeline implemented yet. Deployment is via auto-deploy from GitHub main branch (Railway for backend, Vercel for frontend/admin).

## Known Bugs (see GAPS.md for full list)
- `users.email` lacks UNIQUE constraint — allows duplicate email registrations
- Zero backend tests (no Vitest configured)
- No CHECK constraints on user_balances to prevent negative values
- No CSRF protection on OAuth flows
- Settlement service not implemented (trades are recorded but not settled on-chain)

## Documentation
- docs/PLAN.md — Master plan with phases, timeline, risk register
- docs/GAPS.md — All gaps and bugs ordered by priority
- docs/phase-0/01-legal-structure.md — Legal analysis, jurisdictions, KYC, AML
- docs/phase-0/02-business-model.md — Revenue, costs, treasury, projections
- docs/phase-0/03-brand-identity.md — Brand, positioning, GTM, personas
- docs/phase-0/04-architecture.md — System architecture, data model, API design, infra
- docs/phase-1/04-ux-design.md — User flows, interaction specs, responsive specs
