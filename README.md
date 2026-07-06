# Sidepit Public API — Protocol Data

**The official wire contract for the Sidepit exchange.** One file: [`sidepit_api.proto`](sidepit_api.proto)
— every message the exchange speaks, exactly as production speaks it.

## Basic facts
- **Exchange**: Bitcoin-margined forwards. Deposit Bitcoin → trade → get your Bitcoin back.
  Your funding address IS your account id (`sidepit_id`, a bc1q address you control).
- **Transport**: [NNG](https://nng.nanomsg.org) (Scalability Protocols) over TCP, protobuf
  payloads. Host: `api.sidepit.com`. Connections are long-lived.
- **Auction**: orders batch into 1-second epochs — the exchange's core mechanic.

## Ports
| Port | Pattern | What |
|---|---|---|
| 12121 | push → | submit signed `SignedTransaction` (orders, cancels) |
| 12122 | sub ← | market data pulse (quote, bar, depth) |
| 12123 | sub ← | auction echo (the epoch transaction stream) |
| 12124 | sub ← | order + margin feed (`OrderData`, your fills and positions) |
| 12125 | req/rep ↔ | queries (`active_product`, positions/account) + account transactions |
| 12126 | sub ← | auction clearing (market-maker tier) |
| 12127 | sub ← | closed 1-minute bars |
| 12128 | sub ← | rejected transactions |
| 12129 | req/rep ↔ | snapshot sync (mid-session join) |

## Start here
1. First call: `active_product` on 12125 — is the exchange up, current session, tradable contracts.
2. Subscribe 12122/12124 for live data; use 12125 for point-in-time account reads.
3. Orders are signed with your Bitcoin key (secp256k1); the order id is
   `{sidepit_id}:{timestamp_ns}` — you mint it, the exchange echoes it.

## SDK, examples, docs
The Python SDK, TUI, examples, and full protocol documentation live in
[**Public-API**](https://github.com/sidepit/Public-API). Docs: https://docs.sidepit.com
