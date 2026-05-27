# ORB Backtesting + Risk Management Example

A Python backtesting project for an **Opening Range Breakout (ORB)** strategy with a special focus on **risk management and position sizing**.

Most traders focus only on entries.

But in real trading:

> Risk management decides survival.

This project demonstrates how the **same strategy can produce completely different outcomes depending on position sizing and risk per trade.**

The strategy uses intraday JSON candle data and demonstrates:

- ORB entries
- Stop Loss
- Risk:Reward targets
- End of Day exits
- Position sizing concepts
- Fixed quantity vs risk-based quantity
- Performance comparison

---

# Why This Project Exists

Two traders can take:

- Same strategy
- Same entry
- Same stop loss
- Same target

And still get completely different results.

Example:

Trader A:

```text
Risk per trade = ₹500
```

Trader B:

```text
Risk per trade = ₹5000
```

Same trade.

Different outcome.

One survives drawdowns.

The other blows up the account.

This repo is built to demonstrate that **risk management matters more than entries.**

---

# Strategy Overview

The script calculates an opening range from:

```text
09:15 AM → 09:29 AM
```

Then waits for a breakout.

Only one trade is taken per day.

---

# Opening Range Calculation

The script calculates:

```text
Opening High
=
Highest High
between
09:15–09:29
```

and

```text
Opening Low
=
Lowest Low
between
09:15–09:29
```

---

# Entry Rules

## BUY Entry

Enter BUY when:

```text
Current candle High > Opening High
```

Trade parameters:

```text
Entry = Current candle Close

SL = Opening Low

Risk Per Share = Entry − SL

Target = Entry + (Risk × RR)
```

Example:

```text
Opening High = 1700

Opening Low = 1690

Breakout candle closes = 1705

Risk:

1705−1690

=15

RR=2

Target:

1705 + (15×2)

=1735
```

---

## SELL Entry

Enter SELL when:

```text
Current candle Low < Opening Low
```

Trade parameters:

```text
Entry = Current candle Close

SL = Opening High

Risk Per Share = SL − Entry

Target = Entry − (Risk × RR)
```

Example:

```text
Opening High=1700

Opening Low=1690

Breakdown candle closes=1685

Risk:

1700−1685

=15

RR=2

Target:

1685−(15×2)

=1655
```

---

# Exit Rules

Trade exits when:

### BUY

- Target reached
- Stop Loss reached
- Time ≥ 3:15 PM

### SELL

- Target reached
- Stop Loss reached
- Time ≥ 3:15 PM

---

# Risk Management Focus

This repository emphasizes:

```text
Position Size
=
Allowed Risk Per Trade
÷
Risk Per Share
```

Example:

Account:

```text
₹100,000
```

Risk allowed:

```text
1%
```

Maximum loss:

```text
₹1000
```

Trade:

```text
Entry = 1705

SL = 1695
```

Risk per share:

```text
1705−1695

=10
```

Quantity:

```text
1000 ÷ 10

=100 shares
```

Meaning:

```text
No matter what happens,

max loss ≈ ₹1000
```

This is the core idea behind professional risk management.

---

# Why Fixed Quantity Can Mislead Backtests

Most beginner backtests use:

```python
QTY=100
```

Problem:

Trade 1:

```text
SL distance=5
```

Actual risk:

```text
5×100

=₹500
```

Trade 2:

```text
SL distance=30
```

Actual risk:

```text
30×100

=₹3000
```

Same quantity.

Completely different risk.

This creates unrealistic backtest results.

---

# Better Approach

Instead of:

```python
QTY=100
```

Use:

```text
Risk Based Position Sizing
```

Formula:

```text
Quantity

=

Allowed Trade Risk

÷

Risk Per Share
```

Benefits:

✓ Consistent losses

✓ Controlled drawdowns

✓ Fair strategy comparison

✓ Realistic backtesting

✓ Easier account scaling

---

# Expected Folder Structure

```text
project/

│
├── backtest.py
│
└── back_data/
    │
    └── HDFCBANK-EQ/
        │
        ├── 2026-01-01.json
        ├── 2026-01-02.json
        ├── 2026-01-03.json
        └── ...
```

---

# Expected JSON Format

```json
[
  {
      "stat":"Ok",
      "time":"01-01-2026 09:15:00",
      "into":"1700",
      "inth":"1705",
      "intl":"1698",
      "intc":"1702"
  }
]
```

Field meaning:

| Field | Description |
|--------|-------------|
| into | Open |
| inth | High |
| intl | Low |
| intc | Close |
| time | Candle timestamp |
| stat | API status |

---

# Config Variables

Modify values:

```python
DATA_DIR="./back_data"

SYMBOL="HDFCBANK-EQ"

START_DATE=datetime(
    2026,
    1,
    1
)

END_DATE=datetime(
    2026,
    5,
    13
)

TARGET_RR=2

QTY=100
```

Variable explanation:

| Variable | Meaning |
|-----------|----------|
| DATA_DIR | Data folder |
| SYMBOL | Symbol |
| START_DATE | Backtest start |
| END_DATE | Backtest end |
| TARGET_RR | Risk reward ratio |
| QTY | Trade quantity |

---

# Install

Install dependencies:

```bash
pip install pandas
```

---

# Run

```bash
python backtest.py
```

---

# Sample Output

```text
===== 2026-01-02 =====

Opening High: 1720.25

Opening Low: 1698.10

BUY : 1723.50

SL : 1698.10

TARGET : 1774.30

QTY : 100

EXIT: TARGET

PnL: 5080

Day PnL: 5080
```

---

# Final Summary Example

```text
===== FINAL =====

Trades: 25

Wins: 14

Losses: 11

Win Rate: 56%

PnL: 21850


===== DAYS =====

Profitable:18

Loss:6

Flat:1
```

---

# Metrics Tracked

Script tracks:

- Total PnL
- Trades
- Wins
- Losses
- Win rate
- Profitable days
- Losing days
- Flat days

---

# Current Limitations

Current version:

✓ One trade per day

✓ ORB strategy

✓ Fixed quantity sizing

✓ End of day square-off

Missing:

✗ Brokerage

✗ Slippage

✗ Dynamic risk %

✗ Equity curve

✗ Drawdown analysis

✗ Multiple trades/day

✗ Trailing stop loss

✗ Tradebook CSV

---

# Future Improvements

Planned:

- Dynamic risk %
- Position sizing engine
- Drawdown metrics
- Equity curve
- Brokerage simulation
- Slippage simulation
- Trade export CSV
- Portfolio-level risk
- Multiple symbols

---

# Key Lesson

Most traders think:

```text
Entry Strategy
=
Profit
```

Reality:

```text
Strategy + Risk Management

=

Long-Term Survival
```

This project exists to demonstrate exactly that.