# Database Schema

All tables in the Forka backend PostgreSQL database.

| Table | Purpose | Key Columns | Relationships |
|---|---|---|---|
| `users` | End-user accounts (wallet, email, or Google) | `id` (UUID PK), `address`, `email`, `password_hash`, `google_id`, `kyc_status`, `kyc_tier`, `referral_code`, `referred_by` | Has many: orders, positions, balances, ledger_entries, payment_transactions, withdrawals, payout_methods, referrals, push_subscriptions |
| `admin_users` | Admin panel accounts with role-based access | `id` (UUID PK), `email`, `password_hash`, `google_id`, `name`, `role` (super_admin/admin/editor), `is_active` | Referenced by: withdrawals (reviewer), market_proposals (reviewer) |
| `markets` | Prediction markets | `id` (UUID PK), `slug`, `question`, `description`, `category`, `status`, `condition_id`, `end_date`, `resolution_source` | Has many: outcomes, orders, trades, positions, market_prices, tags, comments, reactions |
| `outcomes` | Possible outcomes for a market (min 2) | `id` (UUID PK), `market_id` (FK), `label`, `token_id`, `price` | Belongs to: markets. Has many: orders, positions, market_prices |
| `orders` | User limit/market orders on outcomes | `id` (UUID PK), `user_id` (FK), `market_id` (FK), `outcome_id` (FK), `side`, `type`, `price`, `amount`, `filled`, `status`, `signature` | Belongs to: users, markets, outcomes. Has many: trades (as maker or taker) |
| `trades` | Matched trades between two orders | `id` (UUID PK), `market_id` (FK), `maker_order_id` (FK), `taker_order_id` (FK), `price`, `amount`, `tx_hash`, `settlement_status` | Belongs to: markets, orders (maker), orders (taker) |
| `positions` | Aggregated user position per market outcome | `id` (UUID PK), `user_id` (FK), `market_id` (FK), `outcome_id` (FK), `shares`, `avg_cost`, `realized_pnl` | Belongs to: users, markets, outcomes. Unique on (user, market, outcome) |
| `market_prices` | OHLCV candle data for charting | Composite PK: (`market_id`, `outcome_id`, `timestamp`), `open`, `high`, `low`, `close`, `volume` | Belongs to: markets, outcomes |
| `market_tags` | Tags assigned to markets | `id` (UUID PK), `market_id` (FK), `tag` | Belongs to: markets. Unique on (market, tag) |
| `user_balances` | User currency balances (available + locked) | `id` (UUID PK), `user_id` (FK), `currency`, `available`, `locked` | Belongs to: users. Unique on (user, currency). Check constraints: >= 0 |
| `ledger_entries` | Immutable audit log of balance changes | `id` (UUID PK), `user_id` (FK), `type` (deposit/withdrawal/order_lock/etc.), `amount`, `currency`, `reference_id`, `description` | Belongs to: users |
| `payment_transactions` | Fiat/crypto deposit and withdrawal transactions | `id` (UUID PK), `user_id` (FK), `type`, `provider` (crypto/moonpay/transak/mercadopago), `status`, `amount`, `currency`, `external_id`, `tx_hash` | Belongs to: users |
| `webhook_events` | Idempotent log of incoming payment webhooks | `id` (UUID PK), `provider`, `external_id`, `event_type`, `payload`, `processed_at` | Unique on (provider, external_id, event_type) |
| `withdrawals` | Withdrawal requests with admin review workflow | `id` (UUID PK), `user_id` (FK), `amount`, `fee`, `net_amount`, `method`, `destination` (JSONB), `status`, `review_type`, `reviewed_by` (FK admin), `rejection_reason`, `fraud_flags` (JSONB) | Belongs to: users, admin_users (reviewer) |
| `user_payout_methods` | Saved payout destinations for withdrawals | `id` (UUID PK), `user_id` (FK), `method`, `label`, `destination` (JSONB), `is_default` | Belongs to: users |
| `referrals` | Referral tracking between users | `id` (UUID PK), `referrer_id` (FK), `referred_id` (FK), `referral_code`, `status` (pending/qualified/rewarded), `bonus_amount` | Belongs to: users (referrer), users (referred). Unique on referred_id |
| `push_subscriptions` | Web push notification subscriptions | `id` (UUID PK), `user_id` (FK), `endpoint`, `p256dh`, `auth` | Belongs to: users. Unique on endpoint |
| `market_proposals` | User-submitted market proposals for admin review | `id` (UUID PK), `user_id` (FK), `question`, `description`, `category`, `end_date`, `outcomes` (JSONB), `status`, `reviewed_by` (FK admin), `market_id` (FK) | Belongs to: users, admin_users (reviewer), markets |
| `market_comments` | Threaded comments on markets | `id` (UUID PK), `market_id` (FK), `user_id` (FK), `content`, `parent_id` (self-ref FK), `is_deleted` | Belongs to: markets, users. Self-referencing (parent/replies) |
| `market_reactions` | User reactions (emoji/type) on markets | `id` (UUID PK), `market_id` (FK), `user_id` (FK), `type` | Belongs to: markets, users. Unique on (market, user, type) |
| `blog_posts` | CMS blog posts | `id` (UUID PK), `slug`, `title`, `excerpt`, `content`, `category`, `cover_image`, `author`, `status` (draft/published/archived), `published_at`, `reading_time` | None |
| `password_reset_tokens` | Time-limited tokens for password reset flow | `id` (UUID PK), `user_id` (FK), `token` (varchar 64, unique), `expires_at`, `used_at`, `created_at` | Belongs to: users |

## Enums

| Enum | Values |
|---|---|
| `AdminRole` | super_admin, admin, editor |
| `KycStatus` | none, pending, approved, rejected |
| `KycTier` | tier_0, tier_1, tier_2 |
| `MarketStatus` | draft, active, paused, closed, resolved |
| `OrderSide` | buy, sell |
| `OrderType` | limit, market |
| `OrderStatus` | open, partially_filled, filled, cancelled, expired |
| `SettlementStatus` | pending, submitted, confirmed, failed |
| `PaymentStatus` | pending, processing, completed, failed, expired |
| `PaymentType` | deposit, withdrawal |
| `PaymentProvider` | crypto, moonpay, transak, mercadopago |
| `LedgerEntryType` | deposit, withdrawal, order_lock, order_unlock, trade_debit, trade_credit, fee |
| `ProposalStatus` | pending, approved, rejected |
| `BlogStatus` | draft, published, archived |
| `ReferralStatus` | pending, qualified, rewarded |
| `WithdrawalStatus` | pending, processing, completed, failed, rejected, cancelled |
