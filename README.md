# Polymarket Builder Program

Tools for autonomous agents participating in Polymarket liquidity programs — discover reward markets, post limit orders, and farm daily liquidity rewards.

## What is the Builder Program?

Polymarket rewards liquidity providers who place limit orders on active prediction markets. Agents earn daily $USDC rewards proportional to their share of liquidity provided.

- No trading edge required — profits come from reward distributions
- Works on both YES and NO sides simultaneously
- Rewards paid daily in USDC directly to your wallet

## How It Works

```
1. Scan reward-eligible markets (via CLOB API)
2. Get current YES/NO prices
3. Post limit BUY orders on both sides near the spread
4. Earn daily rewards from Polymarket builder program
5. Collect fills as a bonus when orders execute
```

## Market Discovery

```bash
# Find reward-eligible markets
curl -s "https://clob.polymarket.com/markets?next_cursor=MA==" | python3 -c "
import sys, json
data = json.load(sys.stdin)
markets = data.get(\"markets\", [])
reward_markets = [m for m in markets if m.get(\"rewards_daily_rate\", 0) > 0]
for m in sorted(reward_markets, key=lambda x: float(x.get(\"rewards_daily_rate\",0)), reverse=True)[:10]:
    rate = m.get(\"rewards_daily_rate\", 0)
    spread = m.get(\"minimum_order_size\", 0)
    print(f\"\${rate}/day | min \${spread} | {m[\"question\"][:70]}\")
"
```

## Posting Limit Orders

Requires: Polymarket API key (from polymarket.com/profile).

```python
from py_clob_client.client import ClobClient
from py_clob_client.clob_types import OrderArgs, OrderType

client = ClobClient(
    host="https://clob.polymarket.com",
    key=PRIVATE_KEY,
    chain_id=137,  # Polygon
)

# Post limit order (BUY YES at 0.48 for $5)
order = client.create_order(OrderArgs(
    token_id=YES_TOKEN_ID,
    price=0.48,
    size=5.0,
    side="BUY",
))
client.post_order(order, OrderType.GTC)
```

## Risk Parameters

The DeepBlue market-making engine uses these defaults:

| Parameter | Value | Notes |
|-----------|-------|-------|
| Capital | $100 | Total across all markets |
| Max markets | 10 | Diversify risk |
| Max per market | $10 | Per-side cap |
| Min order size | $2 | CLOB minimum |
| Spread buffer | 2% | Distance from mid |
| Daily loss limit | $15 | Circuit breaker |

## DeepBlue API (Live Signals)

Get real-time market data to inform order placement:

```bash
# Live BTC directional signal (x402, $0.005)
curl https://api.deepbluebase.xyz/btc/realtime-signal

# Current 5-min market discovery (x402, $0.01)
curl https://api.deepbluebase.xyz/polymarket/5min

# Full market intelligence (x402, $0.03)
curl https://api.deepbluebase.xyz/prediction-markets
```

## Files

| File | Purpose |
|------|---------|
| `scripts/scan_rewards.py` | Discover reward-eligible markets |
| `scripts/post_orders.py` | Post limit orders on both sides |
| `scripts/collect_rewards.py` | Check and collect daily rewards |
| `references/strategies.md` | Market-making strategy notes |

## Requirements

```
pip install py-clob-client web3
```

- Polygon wallet with USDC (for collateral)
- Polymarket API key (from polymarket.com)
- Python 3.10+

## Links

- [Polymarket CLOB Docs](https://docs.polymarket.com)
- [py-clob-client](https://github.com/Polymarket/py-clob-client)
- [DeepBlue API](https://api.deepbluebase.xyz)
- [deepbluebase.xyz](https://deepbluebase.xyz)

## License

MIT