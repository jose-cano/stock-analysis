# stock-analysis
Based on Stefanie Molin's Hands on Data Analysis with Pandas stock_analysis package.

The stock_analysis package contains classes for gathering 
stock data from the Internet (StockReader); visualizing individual assets or groups of 
them (Visualizer family); calculating metrics for single assets or groups of them for 
comparisons (StockAnalyzer and AssetGroupAnalyzer, respectively); and time 
series modeling with decomposition, ARIMA, and linear regression (StockModeler).

Updated to work on Python 3.7.12 (Google Colab's default Python version) as of 10/19/2021.

Link to [original][1].

Check out the financial analysis [notebook][2].

[1]: https://github.com/stefmolin/stock-analysis/tree/2nd_edition

[2]: https://nbviewer.org/github/jose-cano/Exploring-data/blob/main/financial_analysis.ipynb

## Setup on Colab
```shell
!git clone https://github.com/jose-cano/stock-analysis.git
!pip3 install -r stock-analysis/requirements.txt
```
*You must restart runtime after running commands*

Then, drag the stock_analysis folder outside the stock-analysis folder (to your working directory).
You're done with the setup!

### Getting data
```python
from stock_analysis import StockReader

reader = StockReader('2017-01-01', '2018-12-31')

# get bitcoin data in USD
bitcoin = reader.get_bitcoin_data('USD')

# get faang data
fb, aapl, amzn, nflx, goog = (
    reader.get_ticker_data(ticker) \
    for ticker in ['FB', 'AAPL', 'AMZN', 'NFLX', 'GOOG']
)

# get S&P 500 data
sp = reader.get_index_data('S&P 500')
```
### Grouping data
```python
from stock_analysis.utils import group_stocks, describe_group

faang = group_stocks(
    {
        'Facebook': fb,
        'Apple': aapl,
        'Amazon': amzn,
        'Netflix': nflx,
        'Google': goog
    }
)

# describe the group
describe_group(faang)
```

### Building a portfolio
Groups assets by date and sums columns to build a portfolio.
```python
from stock_analysis.utils import make_portfolio

faang_portfolio = make_portfolio(faang)
```

### Visualizing data
Be sure to check out the other methods here for different plot types, reference lines, shaded regions, and more!

#### Single asset
Evolution over time:
```python
import matplotlib.pyplot as plt
from stock_analysis import StockVisualizer

netflix_viz = StockVisualizer(nflx)

ax = netflix_viz.evolution_over_time(
    'close',
    figsize=(10, 4),
    legend=False,
    title='Netflix closing price over time'
)
netflix_viz.add_reference_line(
    ax,
    x=nflx.high.idxmax(),
    color='k',
    linestyle=':',
    label=f'highest value ({nflx.high.idxmax():%b %d})',
    alpha=0.5
)
ax.set_ylabel('price ($)')
plt.show()
```
