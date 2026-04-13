# Forka — Deposit & Withdrawal Product Spec

**Author:** Product Management
**Date:** April 13, 2026
**Status:** Approved — Phase 1 in development
**Audience:** Engineering, Operations, Compliance

---

## 0. Executive Summary

Forka users need to put money in and take money out. Deposits are partially built (MoonPay card, Mercado Pago redirect, USDC direct). Withdrawals are entirely missing.

This spec covers the end-to-end money movement system: deposit completion, withdrawal from scratch, KYC gating, balance management, and compliance guardrails.

**Core principle:** Fiat-first. Our users are Blaze bettors, not DeFi degens. Crypto is an option, not the default.

**Decisions resolved:**
1. Keep boleto deposits — Yes, show "Pending" status
2. Unique deposit address per user via HD wallet
3. No fee absorption in MVP
4. First withdrawal always manual review
5. KYC provider: Sumsub
6. Polling for balance updates in MVP
7. Rollover multiplier: 1x total deposits

---

## 1. Deposit Flow

### 1.1 Supported Methods

| Method | Regions | Provider | Status |
|---|---|---|---|
| Credit/Debit Card | Global | MoonPay | Built |
| Apple Pay | Global (iOS) | MoonPay | Built |
| Google Pay | Global (Android) | MoonPay | Built |
| PIX (instant) | Brazil | Mercado Pago | Built |
| Boleto Bancario | Brazil | Mercado Pago | Built |
| Local Debit Card | Brazil, Argentina, Mexico | Mercado Pago | Built |
| USDC on Polygon | Global | Direct wallet transfer | Built |

### 1.2 Amounts & Fees

| Method | Min | MoonPay/MP Fee | Forka Fee | Processing Time |
|---|---|---|---|---|
| Card | $10 | ~3.5% | 0% | 1-5 min |
| Apple/Google Pay | $10 | ~1% | 0% | 1-5 min |
| PIX | R$20 (~$4) | 0.99% | 0% | Instant |
| Boleto | R$50 (~$10) | 0% | 0% | 1-3 business days |
| Local Debit | R$20 (~$4) | 1.99% | 0% | Instant |
| USDC Polygon | 5 USDC | Gas (fractions of cent) | 0% | 2-3 min |

---

## 2. Withdrawal Flow (Phase 1 MVP)

### 2.1 Supported Methods (MVP)

| Method | Regions | Provider |
|---|---|---|
| PIX payout | Brazil | Mercado Pago Payouts API |
| USDC to external wallet | Global | Direct on-chain transfer |

### 2.2 Withdrawal Limits

| KYC Tier | Daily | Monthly | Per-Transaction |
|---|---|---|---|
| tier_0 | $50 | $100 | $50 |
| tier_1 | $2,000 | $10,000 | $2,000 |
| tier_2 | $25,000 | $100,000 | $25,000 |

Minimum withdrawal: $5 USD (all methods).

### 2.3 Fees

| Method | Fee | Notes |
|---|---|---|
| PIX | $0.50 flat | Deducted from amount |
| USDC Polygon | $0.25 flat | Covers gas + margin |

### 2.4 Information Required

| Method | User Provides |
|---|---|
| PIX | PIX key (CPF, email, phone, or random key) |
| USDC | Polygon wallet address |

### 2.5 Anti-Fraud Rules

| Rule | Trigger | Action |
|---|---|---|
| Deposit-then-withdraw | Within 24h of deposit | Manual review |
| Min trading requirement | Wagered < 1x deposits | Manual review |
| Velocity | >3 requests in 24h | Block + review |
| Large (tier_1) | > $1,000 single | Manual review |
| New account | Created < 48h ago | Manual review |
| First withdrawal | First ever for user | Manual review |

### 2.6 Auto-Approve Thresholds

| Condition | Action |
|---|---|
| Amount <= $200 AND no fraud flags AND not first withdrawal | Auto-approve |
| Amount <= $1,000 AND tier_2 AND no fraud flags AND not first | Auto-approve |
| Everything else | Manual review |

---

## 3. KYC Integration (Sumsub)

### 3.1 Tier Progression

| Tier | Requirements | Deposit Limit | Withdrawal Limit |
|---|---|---|---|
| tier_0 | Email verified | $100 lifetime | $50/day |
| tier_1 | Government ID + selfie | $10,000/month | $2,000/day |
| tier_2 | Proof of address + source of funds | Unlimited | $25,000/day |

### 3.2 When KYC Triggers

- Cumulative deposits > $100 → must complete tier_1
- Withdrawal > $50/day → must be tier_1+
- Monthly deposits > $10,000 → must be tier_2

---

## 4. Technical Requirements

### 4.1 New API Endpoints

| Endpoint | Method | Description |
|---|---|---|
| /api/withdrawals | POST | Create withdrawal request |
| /api/withdrawals | GET | List user's withdrawals |
| /api/withdrawals/:id/cancel | POST | Cancel pending withdrawal |
| /api/payments/methods | GET | Available methods for user's region |
| /api/payments/quote | POST | Fee + FX rate quote (60s lock) |
| /api/admin/withdrawals | GET | Pending review queue |
| /api/admin/withdrawals/:id/approve | POST | Approve withdrawal |
| /api/admin/withdrawals/:id/reject | POST | Reject with reason |
| /api/transactions | GET | User's full transaction history |
| /api/webhooks/mercadopago/payouts | POST | Payout status webhook |

### 4.2 New Database Tables

- `withdrawals` — withdrawal requests with status tracking
- `user_payout_methods` — saved PIX keys / wallet addresses
- `kyc_verifications` — KYC submission tracking

### 4.3 Schema Changes

- `users`: add `kyc_tier`, `kyc_verified_at`
- `balance_transactions`: add `method`, `original_amount`, `original_currency`, `fx_rate`

---

## 5. Phased Rollout

### Phase 1 — MVP (4 weeks)
- PIX withdrawal (Brazil)
- USDC withdrawal (global)
- Anti-fraud rules + manual review queue
- Admin panel: withdrawal review with Slack alerts
- Transaction history page
- Sumsub KYC (tier_0 → tier_1)
- WithdrawalModal component
- Saved payout methods

### Phase 2 — Full LATAM (+3 weeks)
- Bank transfer (TED) withdrawals
- Mercado Pago balance payouts
- tier_2 KYC
- WebSocket balance updates
- 1 free withdrawal/month (>$500 volume)

### Phase 3 — Global (+4 weeks)
- MoonPay Off-Ramp (international bank)
- Mexico (SPEI), Argentina (CBU) withdrawals
- Multi-currency display
- Advanced fraud detection
