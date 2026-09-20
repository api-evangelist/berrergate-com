---
name: berrergate-com-free-preview-then-buy-decision
description: Check BerrerGate's free procurement preview for a capability, then — only if it is worth it — buy one of the five $0.01 USDC open-source stack decision packets over x402 and read the receipt.
api: openapi/berrergate-com-openapi.json
operations: []
routes:
  - GET /v1/agent/procurement/catalog
  - POST /v1/agent/procurement/preview
  - GET /v1/agent/beta/catalog
  - POST /v1/agent/beta/query
  - GET /v1/agent/beta/receipts/{receipt_id}
method: generated
generated: '2026-09-19'
source: openapi/berrergate-com-openapi.json, https://api.berrergate.com/llms.txt, https://api.berrergate.com/skill.md, live 402 on 2026-09-19
---

# Free preview first, then buy a decision packet

BerrerGate sells "decision packets" — an evidence-backed shortlist of open-source implementations for one capability. Everything before the purchase is free and anonymous; the purchase is $0.01 USDC on Base via x402. The provider declares **no operationIds**, so steps name routes.

## Rules that apply to every step
- Base URL `https://api.berrergate.com`. No API key, no account. `Content-Type: application/json`.
- Every paid or committing POST **requires** `x-idempotency-key` (caller-generated). Reuse the same key when you retry the same request — including the paid retry after a 402 — or the server answers `400 IDEMPOTENCY_KEY_REQUIRED` / may charge twice.
- Errors are `{"ok":false,"error":"<CODE>","trace_id":"...","request_id":"..."}`; log `trace_id`. See `errors/berrergate-com-problem-types.yml`.
- Paid scope is **open-source implementation selection only** — the provider says it "is not a hosted-vendor ranking". Do not present a packet as a vendor ranking.

## Steps
1. **List the capabilities** — `GET /v1/agent/procurement/catalog`. Ten `capability_key`s, all `free_preview: true` (e.g. `agent.browser_automation`, `agent.web_search_api`).
2. **Take the free preview** — `POST /v1/agent/procurement/preview` with `{"query": "<what your agent needs>"}` or `{"capability_key": "...", "intent": "SELECT"}`. Cache-only; no upstream call, no cost. Decide from this whether a paid packet adds anything.
3. **Check what is for sale** — `GET /v1/agent/beta/catalog`. On 2026-09-19: five products, `price_atomic: 10000` (= $0.01 USDC), state `ACTIVE`, keys `agent.agent_observability`, `agent.automation_orchestration`, `agent.browser_scraping_automation`, `agent.product_analytics`, `agent.browser_automation`. Only `intent: SELECT` is sold.
4. **Get the quote** — `POST /v1/agent/beta/query` with header `x-idempotency-key: <uuid>` and body `{"capability_key": "agent.browser_automation", "intent": "SELECT"}`, **without** `PAYMENT-SIGNATURE`. Expect `402`. The `PAYMENT-REQUIRED` header (base64 JSON) and the body carry `accepts[]`: `scheme exact`, `network eip155:8453`, `amount` in atomic USDC, `asset 0x8335…2913`, `payTo`, `maxTimeoutSeconds 60`. Confirm the amount is `10000` before paying.
5. **Pay and retry** — settle per x402 v2 and repeat the identical request with `PAYMENT-SIGNATURE` and the **same** `x-idempotency-key`. `200` returns the packet (provider evaluations, shortlists, content-hash and commercial-authority certificates, settlement receipt). `404` = unknown key; `409` = conflict; `429` = capped.
6. **Keep the receipt** — `GET /v1/agent/beta/receipts/{receipt_id}`. There is no cancel or refund route (see `conventions/…reversibility`), so the receipt is the only post-purchase artifact.

## Do not
- Do not call `POST /v1/search` — declared `410 Gone`.
- Do not guess a `capability_key`; use the catalog.
- Do not retry a timed-out paid call with a new idempotency key.
