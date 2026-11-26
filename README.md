# Confirmo

**Shopify app that confirms cash-on-delivery (COD) orders over WhatsApp before they ship, and scores each order's return risk.** Built for COD-heavy markets (Latin America first) where unconfirmed orders cause costly returns.

Website: [confirmo-app.sezerlabs.com](https://confirmo-app.sezerlabs.com)

> Source code is private (commercial product). This page describes the architecture and engineering.

## What it does
- Listens to new Shopify orders and messages the customer on WhatsApp with confirm / cancel buttons
- Writes the answer back to the order (tags, notes) so the merchant ships only confirmed orders
- Scores return-to-origin (RTO) risk per order from address, history and behaviour signals
- "Link mode": confirmation via a signed one-time link when the merchant has no WhatsApp Cloud API account
- Embedded admin inside Shopify, usage-based billing through the Shopify Billing API
- Spanish customer messaging out of the box, English merchant admin

## Architecture
```
Shopify webhooks ──► Cloudflare Worker (TypeScript) ──► WhatsApp Cloud API
                          │        ▲                         │
                          ▼        └──── Meta webhooks ◄─────┘
                    Cloudflare D1 (SQLite)      Cron triggers (reminders, retries)
                          │
                    Shopify Admin GraphQL API (tags, notes, billing)
```

## Engineering highlights
- Serverless on Cloudflare Workers + D1, zero servers to run
- HMAC verification on every Shopify and Meta webhook, GDPR/privacy webhooks implemented
- Idempotent webhook handling and retry queue for message delivery
- **330+ automated tests** (Vitest on the Workers runtime, unit + end-to-end order flow)
- Submitted to the Shopify App Store

## Stack
TypeScript · Cloudflare Workers · D1 · Shopify Admin GraphQL API · Shopify Billing API · WhatsApp Cloud API · Vitest
