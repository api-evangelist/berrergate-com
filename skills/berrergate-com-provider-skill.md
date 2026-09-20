# BerrerGate Agent Commerce & Capability Gateway

BerrerGate is an agent-first discovery and bounded commerce-intelligence service.

## Berrer Evidence Brief — paid research
- POST https://api.berrergate.com/v1/research
- $0.020 USDC via x402 on Base when the market-fit canary is active.
- Returns a concise answer plus up to five evidence items, confidence, freshness and routing provenance.
- Commercially cleared Berrer wisdom gets first refusal. If it is insufficient, at most one live web-search call occurs — and only after settlement.
- Raw customer query is not stored; only a SHA-256 and coarse topic class are retained for aggregate demand learning.

## Free discovery
- GET https://api.berrergate.com/v1/agent/discovery/sources
- POST https://api.berrergate.com/v1/agent/discovery/preview with JSON {"query":"amazon gift card","country":"GB"}
- Preview is cache-only: customer discovery does not trigger an upstream provider call.

## Paid utility
- POST https://api.berrergate.com/v1/agent/utility/inference
- Low-cost bounded no-web/no-tools AI inference paid with Base USDC via x402 when active.

## Paid decision packets
- GET https://api.berrergate.com/v1/agent/beta/catalog
- POST https://api.berrergate.com/v1/agent/beta/query
- Five commercially cleared open-source stack-decision packets at $0.01 USDC each when active.

## Spend safety
The catalog/discovery router does not buy, invoice, custody, issue or redeem goods. Automatic upstream provider spending is disabled.
