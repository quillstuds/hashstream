# Hedera Mainnet — Live Traffic

A full-screen, real-time visualization of [Hedera](https://hedera.com) mainnet activity. Every transaction reaching consensus becomes a glowing dot flowing across the screen toward a "consensus bar," grouped into lanes by transaction type and sized by the fee paid.

## What it shows

- **Live transactions per second**, derived from the most recent ~20 consensus blocks.
- **A "transaction river"** where every transaction is drawn as a dot — at any rate. Detailed records are fetched per-transaction; beyond the API's reach, dot counts are reconciled against block-header totals so the on-screen count always matches the real count.
- **Fixed type lanes** chosen at load time from a rate-weighted sample of the past 3 hours of traffic. Any type accounting for ≥1% of transactions gets its own lane (ordered by share); everything rarer is combined into a permanent "Other transactions" lane. Lanes never reorder or appear/disappear during a session.
- **Per-lane rate, share, and fee revenue**, smoothed over a rolling 30-second window so sparse types read as a steady trickle rather than flickering.
- **Hover any dot** to freeze it and inspect that exact transaction (ID, fee in ℏ and USD, memo, success/failure), with a link to view it on [HashScan](https://hashscan.io).
- **Fee revenue, daily pace, HBAR price, and success rate** in the status bar, plus a per-block throughput sparkline.

All data comes live from the public Hedera mirror node — no backend, no build step, no API key.

## Usage

It's a single self-contained HTML file. Open it in a browser:

```sh
open index.html
```

Or serve it locally:

```sh
python3 -m http.server 8765
# then visit http://localhost:8765/
```

### Stress testing

Append `?stress=N` to inject `N` synthetic transactions/second on top of live traffic, to see how the visualization behaves under load:

```
/?stress=1500
```

Synthetic traffic is clearly labeled in the status line. Verified to hold 60 fps at 1,500 tx/s with ~4,500 concurrent dots.

## How it works

- **Data source:** [`mainnet-public.mirrornode.hedera.com`](https://mainnet-public.mirrornode.hedera.com/api/v1/docs/) — blocks, transactions, and the on-chain HBAR exchange rate, polled every 5 seconds.
- **Rendering:** a single `<canvas>` with pre-rendered radial-gradient sprites and additive blending; level-of-detail rendering drops motion trails as particle density rises to keep the frame rate steady.
- **Completeness:** block headers give exact transaction counts, so dots are never silently dropped — only the per-transaction detail (inspectable on hover) is capped.

## License

MIT
