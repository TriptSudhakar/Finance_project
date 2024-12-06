# Backtesting trading strategies

## How to run code
First run the python script provided using the command `python script.py`
This will generate csv files in the `stock_data` for various stocks.
We will work with the `consolidated_stock_data.csv` file.

Now there are two trading strategies, namely `SimpleMovingAverage` and `ExponentialMovingAverage`.
To execute these, simple run the main functions in the respective classes

## Methodology
I have used the daily data of last 5 years for the top 20 stocks in yfinance library in python. 

### Simple Moving Average
In this strategy, we first divide the initial capital **equally** among all the stocks.

Then for each stock, we maintain two averages: short average and long average.
These are calculated at every point using the data points in a previous window; window size for long average is larger than that of short average.

We consider a **long-only strategy**, where we can only sell stocks as much as we own.

Whenever the short average exceeds the long average, it is a signal to buy and when the short average falls below the long average, it is a signal to sell.

In the code, three combinations of window sizes are tested. Performance metrics used are Sharpe ratio and Maximum Drawdown.

### Exponential Moving Average
Building on the above strategy, certain improvements are introduced:
1. **Exponential moving average (EMA)** is used instead of the simple moving average (SMA) to give more weight to recent prices and less weight to older prices
2. Buy/sell signals for stock are now generated using the previous condition for long and short averages along with **RSI** over the last 14 days
   - **Buy** a stock only if **short EMA > long EMA** and **RSI > 30** (RSI < 30 indicates that the stock is overbought)
   - **Sell** a stock only if **short EMA < long EMA** and **RSI < 70** (RSI > 70 indicates that the stock is oversold)
3. Now we define `RISK_PER_TRADE` as **0.5%**, which is the maximum risk that we can take per trade.
4. To calculate the position size of a trade, we use **ATR** (Average True Range) calculated over the last 14 days. This is to limit trading in a stock that is highly volatile.
5. The position size for a stock is calculated as `RISK_PER_TRADE * portfolioValue / StopLossDistance`. Stop loss distance is the maximum loss per share that we can bear. It is defined to be twice the ATR. 

We observe that the Sharpe Ratio improves considerably from the first strategy to the second strategy.