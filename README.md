# ORB Backtesting + Risk Management + Brokerage + Slippage

A Python backtesting project for an **Opening Range Breakout (ORB)** strategy with a strong focus on:

- Risk management
- Position sizing
- Slippage simulation
- Brokerage simulation
- Realistic backtesting

Most traders focus only on entries.

But in actual trading:

> Survival is decided by execution quality and risk management.

This project demonstrates how the **same strategy can produce very different results once real-world costs are added.**

The strategy uses intraday JSON candle data and supports:

- ORB entries
- Stop Loss
- Risk:Reward targets
- End-of-Day exits
- Risk-based position sizing
- Brokerage simulation
- Slippage simulation
- Realistic PnL calculations
- Performance statistics

---

# Why This Project Exists

Two traders can use:

- Same strategy
- Same entry
- Same stop loss
- Same target

And still get very different outcomes.

Example:

Trader A:

```text
No brokerage
No slippage
```

Trader B:

```text
Real brokerage
Real slippage
```

Same trade.

Different result.

One sees fantasy profits.

The other sees realistic results.

This project exists to demonstrate why:

```text
Backtest realism
>
Backtest profits
```

---

# Biggest Beginner Mistake

Many beginner backtests assume:

```text
Entry price = chart price
Exit price = chart price
Brokerage = 0
Slippage = 0
```

Reality:

You almost never get chart price.

There are:

- execution delays
- bid/ask spread
- slippage
- brokerage
- taxes
- liquidity impact

Ignoring these can completely distort a strategy.

Example:

Without costs:

```text
Trades:81

Win Rate:55.56%

PnL:

₹16,077
```

After adding brokerage + slippage:

```text
Trades:81

Win Rate:48.15%

PnL:

₹5,198
```

Same strategy.

Same trades.

Only realism changed.

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

# BUY Entry Rules

Enter BUY when:

```text
Current candle High

>

Opening High
```

Trade parameters:

```text
Entry = Current candle Close

SL = Opening Low

Risk Per Share

=

Entry − SL

Target

=

Entry + (Risk × RR)
```

Example:

```text
Opening High=1700

Opening Low=1690

Breakout candle closes=1705

Risk:

1705−1690

=15

RR=2

Target:

1705+(15×2)

=1735
```

---

# SELL Entry Rules

Enter SELL when:

```text
Current candle Low

<

Opening Low
```

Trade parameters:

```text
Entry = Current candle Close

SL = Opening High

Risk Per Share

=

SL − Entry

Target

=

Entry − (Risk × RR)
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

- Target hit
- Stop loss hit
- Time ≥ 3:15 PM

### SELL

- Target hit
- Stop loss hit
- Time ≥ 3:15 PM

---

# Risk Management Logic

The project uses:

```text
Risk Based Position Sizing
```

Formula:

```text
Quantity

=

Allowed Risk

÷

Risk Per Share
```

Example:

Account:

```text
₹100,000
```

Risk:

```text
1%
```

Allowed loss:

```text
₹1000
```

Trade:

```text
Entry=1705

SL=1695
```

Risk/share:

```text
1705−1695

=10
```

Quantity:

```text
1000÷10

=100 shares
```

Meaning:

```text
Max expected loss

≈ ₹1000
```

---

# Why Fixed Quantity Is Misleading

Many beginner backtests use:

```python
QTY=100
```

Trade 1:

```text
SL distance=5
```

Risk:

```text
5×100

=₹500
```

Trade 2:

```text
SL distance=30
```

Risk:

```text
30×100

=₹3000
```

Same quantity.

Different risk.

This creates misleading results.

---

# Better Approach

Instead of:

```python
QTY=100
```

Use:

```text
Risk Based Quantity
```

Benefits:

✓ Controlled drawdowns

✓ Consistent risk

✓ Easier scaling

✓ Fair strategy comparison

✓ Professional position sizing

---

# Brokerage Simulation

The project now supports brokerage.

Example config:

```python
BROKERAGE=0.0003
```

Brokerage gets applied on:

```text
Entry turnover

+

Exit turnover
```

Formula:

```text
Turnover

=

(entry×qty)

+

(exit×qty)
```

Charges:

```text
Brokerage

=

Turnover × BROKERAGE
```

Final:

```text
PnL

=

Gross PnL

−

Brokerage
```

---

# Slippage Simulation

The project also supports slippage.

Example:

```python
SLIPPAGE=0.0005
```

Equivalent:

```text
0.05%
```

Applied during execution:

BUY entry:

```text
Actual Entry

=

Price × (1+slippage)
```

SELL entry:

```text
Actual Entry

=

Price × (1−slippage)
```

BUY exit:

```text
Actual Exit

=

Price × (1−slippage)
```

SELL exit:

```text
Actual Exit

=

Price × (1+slippage)
```

This simulates real market execution.

---

# Folder Structure

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

Field explanation:

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

```python
DATA_DIR="./back_data"

SYMBOL="JIOFIN-EQ"

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

CAPITAL=100000

RISK_PERCENT=1

SLIPPAGE=0.0005

BROKERAGE=0.0003
```

---

# Variable Meaning

| Variable | Meaning |
|---|---|
| DATA_DIR | Data location |
| SYMBOL | Symbol |
| START_DATE | Backtest start |
| END_DATE | Backtest end |
| TARGET_RR | Risk reward ratio |
| CAPITAL | Total capital |
| RISK_PERCENT | Risk per trade |
| SLIPPAGE | Execution slippage |
| BROKERAGE | Broker charges |

---

# Install

Install dependency:

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

Opening High:1720

Opening Low:1698

BUY:1723

SL:1698

TARGET:1774

QTY:100

EXIT:TARGET

PnL:4820

Day PnL:4820
```

---

# Final Summary Example

```text
===== FINAL =====

Trades:81

Wins:39

Losses:42

Win Rate:48.15%

PnL:5198


===== DAYS =====

Profitable:39

Loss:42

Flat:2
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
- Loss days
- Flat days

---

# Current Features

✓ ORB strategy

✓ One trade/day

✓ End-of-day square off

✓ Dynamic position sizing

✓ Brokerage simulation

✓ Slippage simulation

✓ Risk management

✓ Trade statistics

---

# Planned Improvements

- Equity curve
- Drawdown metrics
- Tradebook CSV export
- Portfolio level testing
- Multiple trades/day
- Trailing SL
- Sector analysis
- Multi-symbol testing

---

# Key Lesson

Many traders think:

```text
Entry Strategy

=

Profit
```

Reality:

```text
Strategy

+

Risk Management

+

Brokerage

+

Slippage

=

Reality
```

Backtesting should not sell dreams.

It should move closer to reality.