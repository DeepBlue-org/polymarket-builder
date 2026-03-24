---
name: polymarket-builder
description: Discover and join Polymarket liquidity programs. Scan for reward-eligible markets, post limit orders on both YES/NO sides, and farm daily USDC liquidity rewards. Use when asked about market-making, liquidity farming, or Polymarket builder rewards.
metadata:
  author: DeepBlue
  version: "1.0.0"
  tags:
    - polymarket
    - market-making
    - liquidity
    - defi
    - prediction-markets
    - rewards
---

# Polymarket Builder

Discover reward-eligible Polymarket markets and post limit orders to earn daily USDC rewards.

## Scan for Reward Markets

```bash
python3 scripts/scan_rewards.py
```

## Post Orders

```bash
PRIVATE_KEY=0x... python3 scripts/post_orders.py --market MARKET_ID --size 5
```

## Collect Rewards

```bash
python3 scripts/collect_rewards.py --wallet 0xYOUR_WALLET
```
