---
name: berrergate-com-evidence-brief
description: Buy a $0.020 USDC Berrer Evidence Brief — a concise, evidence-cited research answer — over x402, with the idempotency, size and privacy rules the provider states.
api: openapi/berrergate-com-openapi.json
operations: []
routes:
  - POST /v1/research
method: generated
generated: '2026-09-19'
source: openapi/berrergate-com-openapi.json, https://api.berrergate.com/skill.md, https://api.berrergate.com/llms.txt, observed 402 body on 2026-09-19
---

# Buy an Evidence Brief

`POST https://api.berrergate.com/v1/research` returns a short answer with up to five evidence items, a confidence band, a freshness label and routing provenance. Commercially cleared "Berrer wisdom" is used first; at most **one** live web-search call is made, and only after settlement. The raw query is not stored (a SHA-256 and a coarse topic class are).

## Request
- Headers: `Content-Type: application/json`, `x-idempotency-key: <uuid>` (required — `400 IDEMPOTENCY_KEY_REQUIRED` without it).
- Body: `{"query": "<1-700 chars>", "max_evidence": 1-5}` (`additionalProperties: false`; default `max_evidence` 5).

## Flow
1. Send the request **without** `PAYMENT-SIGNATURE`. Expect `402`: `PAYMENT-REQUIRED` header + body `{"x402Version":2,"accepts":[{"scheme":"exact","network":"eip155:8453","amount":"20000","asset":"0x8335…2913","payTo":"0x1090…0a8a","maxTimeoutSeconds":60}], ...}`. `amount 20000` = $0.020 USDC. The body also states `provider_call_performed: false` and `settlement_contacted: false` — nothing has happened yet.
2. Settle per x402 v2 within 60 s and repeat the **identical** request with `PAYMENT-SIGNATURE` and the **same** `x-idempotency-key`.
3. `200` body shape (from the provider's Bazaar example): `{"answer": "...", "confidence": "HIGH", "freshness": "LIVE_WEB|...", "routing_mode": "...", "evidence": [{"source_type","title","url","snippet","evidence_sha256"}]}`. Cite `evidence[].url`; the provider labels this as an AI-assembled brief.
4. Handle `429` (capped — no Retry-After is published), `502` (upstream search failed) and `503` (product inactive; skill.md says the price applies "when the market-fit canary is active" — check `GET /v1/agent/capabilities` first if you get 503).

## Notes
- There is no refund or cancel route; a failed call is not settled ("payment_before_provider_cost" applies to the provider's own upstream cost, not a reversal). Do not tell a user a brief can be refunded.
- `POST /v1/search` is the retired predecessor (`410`).
