# Deploy and Host LNbits on Railway

[![Deploy on Railway](https://railway.com/button.svg)](https://railway.com/new/template/lnbits?utm_medium=integration&utm_source=button&utm_campaign=lnbits)

[LNbits](https://lnbits.com/) is a free, open-source Lightning wallet and accounts system: create wallets for yourself or your users, accept Bitcoin Lightning payments, and extend it with plugins (payment links, paywalls, points of sale, vouchers, and more). This template pairs it with [phoenixd](https://phoenix.acinq.co/server), ACINQ's lightweight self-custodial Lightning node, so you get a complete, working bitcoin payments stack with no blockchain sync and no channel management.

## Screenshots

![LNbits wallet with receive and send](https://raw.githubusercontent.com/nomideusz/lnbits-railway/main/images/01.jpg)

## About Hosting LNbits

Hosting LNbits normally requires connecting it to a Lightning funding source — often a full bitcoind + LND/CLN stack that needs tens of gigabytes and days of sync. This template uses phoenixd instead: a self-custodial node that offloads chain-watching to ACINQ's infrastructure while your keys stay in your volume. Both services deploy in minutes on Railway's private network — phoenixd is not publicly exposed, LNbits reaches it over the internal network, and the shared API password is auto-generated at deploy time. Each service keeps its state on a persistent volume: your Lightning seed lives in phoenixd's volume, and wallets/accounts live in LNbits' SQLite database.

**Back up your seed.** phoenixd generates a 12-word seed at `/phoenix/.phoenix/seed.dat` on first boot. This is self-custodial software — if you delete the volume without a backup, your funds are gone. Read the file right after your first deploy and store the words somewhere safe.

## Common Use Cases

- Accept Lightning payments on a website or store with payment links and LNURL
- Run a multi-user community wallet where each user gets their own sub-wallet
- Point-of-sale terminal, paywalled content, vouchers, or tipping via LNbits extensions
- A programmatic Lightning API backend for your own applications

## Dependencies for LNbits Hosting

- phoenixd (included) — self-custodial Lightning node, no chain storage
- SQLite on a Railway volume (included) — swap for Postgres via `LNBITS_DATABASE_URL` if you outgrow it

### Deployment Dependencies

- [LNbits documentation](https://docs.lnbits.org/)
- [phoenixd documentation](https://phoenix.acinq.co/server)
- [Template source on GitHub](https://github.com/nomideusz/lnbits-railway)

### Implementation Details

- LNbits connects to phoenixd via `LNBITS_BACKEND_WALLET_CLASS=PhoenixdWallet` and `PHOENIXD_API_ENDPOINT=http://phoenixd.railway.internal:9740/`; the shared `PHOENIXD_API_PASSWORD` is generated per deploy.
- phoenixd manages liquidity automatically: inbound capacity is purchased on demand (fees apply, ~1% + mining fee on incoming payments until sufficient liquidity exists — see [phoenixd fees](https://phoenix.acinq.co/server/faq)). This is the trade-off for skipping node ops entirely.
- Lightning-only: this stack accepts and sends Lightning payments. For on-chain BTC invoicing you'd want BTCPay Server, which requires a bitcoind node and a Pro-plan-sized volume.
- First user to open the web UI becomes the instance owner (enable `LNBITS_ADMIN_UI` is already set). Set `LNBITS_ADMIN_USERS` to lock it down further.

## Why Deploy LNbits on Railway?

Railway is a singular platform to deploy your infrastructure stack. Railway will host your infrastructure so you don't have to deal with configuration, while allowing you to vertically and horizontally scale it.

By deploying LNbits on Railway, you are one step closer to supporting a complete full-stack application with minimal burden. Host your servers, databases, AI agents, and more on Railway.
