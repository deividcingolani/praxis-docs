# Praxis — Prediction Markets Platform

## Project Structure
- Monorepo with pnpm workspaces + Turborepo
- packages/contracts: Solidity smart contracts (Foundry)
- packages/backend: Fastify API server (TypeScript, Drizzle ORM)
- packages/frontend: Next.js 15 web app (TypeScript, Tailwind v4)
- packages/shared: Shared types and constants

## Tech Stack
- Blockchain: Polygon, Solidity 0.8.28, Foundry
- Backend: Fastify 5, Drizzle ORM, PostgreSQL 16, Redis 7, BullMQ, Socket.io
- Frontend: Next.js 15, React 19, Tailwind v4, wagmi, RainbowKit, TradingView Lightweight Charts
- Auth: SIWE (Sign In With Ethereum) + JWT

## Key Commands
- `pnpm install` — Install all dependencies
- `pnpm --filter @polymarket/shared build` — Build shared package (required before backend/frontend)
- `pnpm --filter @polymarket/backend exec tsc --noEmit` — Typecheck backend
- `pnpm --filter @polymarket/frontend exec next build` — Build frontend
- `cd packages/contracts && forge test` — Run contract tests
- `docker compose up -d postgres redis` — Start local DB and Redis
- `pnpm --filter @polymarket/backend db:push` — Push DB schema changes
- `pnpm --filter @polymarket/backend db:migrate` — Run DB migrations
- `pnpm --filter @polymarket/backend dev` — Start backend in dev mode
- `pnpm --filter @polymarket/frontend dev` — Start frontend in dev mode

## Architecture Notes
- Order matching happens in-memory (order.service.ts), persisted to DB
- Settlement is async via BullMQ workers (settlement on-chain)
- WebSocket channels: orderbook:{marketId}, trades:{marketId}, prices:{marketId}
- KYC has 3 tiers: tier_0 (browse), tier_1 ($10K/mo), tier_2 (unlimited)
- Mock mode available for KYC and trading (USE_MOCK flags)

## Package Dependencies
- @polymarket/shared must be built before backend or frontend (they depend on it)
- Turborepo handles this automatically via `dependsOn: ["^build"]` in turbo.json

## Docker
- `docker compose up -d postgres redis` — Start infrastructure only
- `docker compose up` — Start everything including backend
- Backend Dockerfile uses multi-stage build from repo root context

## CI/CD
- GitHub Actions CI runs on push to main/develop and PRs to main
- Deployment is manual via workflow_dispatch (staging or production)
- Contracts are tested independently with Foundry
