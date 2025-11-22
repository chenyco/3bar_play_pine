# 3 Bar Play Strategy v6 - Pine Script

## Overview
A production-ready TradingView Pine Script v6 implementation of the "3 Bar Play" strategy. This script detects a specific three-bar pattern with comprehensive filtering, risk management, and visual feedback.

## Pattern Description

### The 3 Bar Play Pattern

#### Bullish Pattern:
1. **Bar 1 (Igniting Bar)**: A wide-range green candle with:
   - Body size > average body size × multiplier
   - Body size > ATR × ATR multiplier
   - Volume > average volume × volume threshold
   
2. **Bar 2 (Resting Bar)**: A smaller candle that:
   - Has a body smaller than Bar 1 (< 80% of Bar 1)
   - Stays within the upper 50% of Bar 1's range
   - Does not retrace below Bar 1's halfway point
   
3. **Bar 3 (Trigger)**: Entry occurs when:
   - Price breaks above Bar 2's high
   - Passes EMA trend filter (if enabled)

#### Bearish Pattern:
The inverse of the bullish pattern:
1. Red igniting bar with same volume and range requirements
2. Resting bar stays in lower 50% of Bar 1
3. Entry on break of Bar 2's low

## Features

### Core Functionality
- ✅ **No Repainting**: Uses `barstate.isconfirmed` to ensure patterns are only detected on closed bars
- ✅ **Dual Mode**: Works as both Strategy (backtesting) and Study (visual overlay)
- ✅ **Comprehensive Filtering**: EMA trend, volume, and ATR filters
- ✅ **Risk Management**: Configurable stop loss and take profit with multiple calculation methods
- ✅ **Visual Feedback**: Bar highlighting, labels, lines, and performance dashboard
- ✅ **Alert System**: Webhook-ready alert conditions with full context

### Input Groups

#### Pattern Settings
- **Body Average Length**: Number of bars to calculate average body size (default: 10)
- **Body Multiplier**: Multiplier for average body to define wide-range (default: 1.5)
- **ATR Length**: Period for ATR calculation (default: 14)
- **ATR Multiplier**: Multiplier for ATR to define wide-range (default: 1.2)
- **Resting Bar Tolerance**: Tolerance for resting bar staying within Bar 1 range (default: 0.02 = 2%)

#### Filters
- **Use EMA Trend Filter**: Only take longs above EMA, shorts below EMA
- **EMA Length**: Period for EMA calculation (default: 20)
- **Use Volume Filter**: Require Bar 1 volume above average
- **Volume Average Length**: Period for volume average (default: 20)
- **Volume Threshold**: Multiplier for average volume (default: 1.5)

#### Risk Management
- **Risk:Reward Ratio**: Target profit = stop distance × ratio (default: 2.0)
- **Use ATR-based Stop**: Use ATR for stop distance instead of bar range
- **ATR Stop Multiplier**: Multiplier for ATR stops (default: 1.5)

#### Visuals
- **Highlight Pattern Bars**: Color bars when pattern is detected
- **Show Entry Labels**: Display buy/sell labels on trigger
- **Show Stop/Take Profit Lines**: Draw lines for stop loss and take profit
- **Show Dashboard**: Display performance metrics table (Strategy mode only)

#### Alerts
- **Enable Alerts**: Enable alert conditions for webhook integration

## Installation

1. Open TradingView
2. Click on "Pine Editor" at the bottom of the screen
3. Copy the contents of `3bar_play_v6.pine`
4. Paste into the Pine Editor
5. Click "Add to Chart"

## Usage

### Strategy Mode (Default)
The script is configured as a Strategy by default for backtesting:
- Automatically executes trades based on pattern detection
- Calculates performance metrics (Win Rate, Net Profit)
- Shows dashboard with trading statistics

### Study Mode
To use as an overlay indicator without executing trades:

1. Comment out the `strategy()` line (line 16-19):
   ```pinescript
   // strategy("3 Bar Play v6", overlay=true, initial_capital=10000, 
   //      default_qty_type=strategy.percent_of_equity, default_qty_value=100,
   //      commission_type=strategy.commission.percent, commission_value=0.1,
   //      slippage=2, pyramiding=0)
   ```

2. Uncomment the `indicator()` line (line 22):
   ```pinescript
   indicator("3 Bar Play v6 - Study", overlay=true, max_labels_count=500, max_lines_count=500)
   ```

3. Change `isStrategyMode` to `false` (line 25):
   ```pinescript
   isStrategyMode = false  // Set to false when using indicator() instead of strategy()
   ```

## Visual Elements

### Bar Colors
- **Green background (light)**: Bullish Bar 1 (igniting bar)
- **Red background (light)**: Bearish Bar 1 (igniting bar)
- **Orange background (light)**: Bar 2 (resting bar)
- **Green/Red background (medium)**: Bar 3 (trigger bar)

### Labels
- **LONG**: Green label with up arrow showing entry price
- **SHORT**: Red label with down arrow showing entry price

### Lines
- **Red dashed line**: Stop loss level
- **Green dashed line**: Take profit level
- **Blue line**: EMA trend filter (if enabled)

### Dashboard (Strategy Mode Only)
Top-right corner table showing:
- Total Trades
- Win Rate (%)
- Net Profit
- Mode (Strategy)

## Alerts

The script includes two alert conditions:
1. **3 Bar Play - Long Setup**: Triggered when bullish pattern completes
2. **3 Bar Play - Short Setup**: Triggered when bearish pattern completes

Alert messages include:
- Direction (LONG/SHORT)
- Trigger price
- Entry price
- Stop loss price
- Take profit price
- Bar index

### Setting Up Alerts

1. Click the "Create Alert" button (clock icon)
2. Select "3 Bar Play - Long Setup" or "3 Bar Play - Short Setup"
3. Configure alert settings (frequency, expiration, etc.)
4. Add webhook URL if needed for automated trading
5. Click "Create"

## Recommended Settings

### For Day Trading
- Body Average Length: 10
- ATR Length: 14
- EMA Length: 20
- Volume Threshold: 1.5
- Risk:Reward: 2.0

### For Swing Trading
- Body Average Length: 20
- ATR Length: 14
- EMA Length: 50 or 200
- Volume Threshold: 1.3
- Risk:Reward: 3.0

### For Volatile Markets
- Use ATR-based Stop: Yes
- ATR Stop Multiplier: 2.0
- Volume Threshold: 2.0

## Technical Details

### No Repainting
The script uses `barstate.isconfirmed` throughout to ensure:
- Pattern detection only occurs on closed bars
- Triggers are only fired after bar completion
- Historical patterns remain consistent

### Performance Considerations
- Maximum labels: 500
- Maximum lines: 500
- Efficient pattern detection with state variables
- Minimal repaints and recalculations

## Backtesting Tips

1. **Test on Multiple Timeframes**: Pattern works best on 5m-1H timeframes
2. **Compare Different Filters**: Try with/without EMA filter on different assets
3. **Optimize Parameters**: Use TradingView's Strategy Tester to optimize settings
4. **Consider Spread/Commission**: Built-in commission and slippage for realistic results
5. **Watch Win Rate**: Good pattern should have >50% win rate with 2:1 RR

## Troubleshooting

### Pattern Not Detected
- Check if volume filter is too strict
- Reduce body multiplier or ATR multiplier
- Disable EMA filter temporarily
- Verify sufficient historical data

### Too Many Signals
- Increase volume threshold
- Enable EMA trend filter
- Increase body multiplier requirements
- Add longer EMA period

### Unrealistic Results
- Verify commission settings
- Adjust slippage
- Check if stops are too tight
- Review entry execution method

## Support and Contribution

For issues, questions, or contributions, please visit the repository at:
https://github.com/chenyco/3bar_play_pine

## License

Open Source - Created by Chenyco

## Version History

### v6 (Current)
- Pine Script v6 implementation
- Comprehensive filtering system
- Risk management with multiple stop methods
- Visual feedback with dashboard
- Alert system for webhook integration
- No repainting guarantee
- Dual mode (Strategy/Study)

---

**Disclaimer**: This script is for educational and informational purposes only. Past performance does not guarantee future results. Always test thoroughly before using with real capital.
