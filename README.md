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
2.1. **Buy** a stock only if **short EMA > long EMA** and **RSI > 30** (RSI < 30 indicates that the stock is overbought)
2.2 **Sell** a stock only if **short EMA < long EMA** and **RSI < 70** (RSI > 70 indicates that the stock is oversold)
3. Now we define `RISK_PER_TRADE` as **0.5%**, which is the maximum value of the portfolio that can be invested in any stock in a given trade.
4. To calculate the position size of a trade in a stock, we use **ATR** (Average True Range). This is to ensure that less value is invested in a stock that is highly volatile, i.e. has a high ATR. ATR is calculated over the last 14 days.
5. The position size for a stock is calculated as `RISK_PER_TRADE * portfolioValue / ATR`. The no of shares to be bought or sold can then be calculated as `position_size / closing_price`.

We observe that the Sharpe Ratio improves considerably from the first strategy to the second strategy.