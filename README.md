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
<img src="images/netflix_line_plot.png?raw=true" align="center" width="600" alt="line plot with reference line">

After hours trades:
```python
netflix_viz.after_hours_trades()
plt.show()
```

<img src="images/netflix_after_hours_trades.png?raw=true" align="center" width="800" alt="after hours trades plot">

Differential in closing price versus another asset:
```python
netflix_viz.fill_between_other(fb)
plt.show()
```
<img src="images/nflx_vs_fb_closing_price.png?raw=true" align="center" width="600" alt="differential between NFLX and FB">

Candlestick plots with resampling (uses `mplfinance`):
```python
netflix_viz.candlestick(resample='2W', volume=True, xrotation=90, datetime_format='%Y-%b -')
```

<img src="images/candlestick.png?raw=true" align="center" width="600" alt="resampled candlestick plot">

*Note: run `help()` on `StockVisualizer` for more visualizations*

#### Asset groups
Correlation heatmap:
```python
from stock_analysis import AssetGroupVisualizer

faang_viz = AssetGroupVisualizer(faang)
faang_viz.heatmap(True)
```

<img src="images/faang_heatmap.png?raw=true" align="center" width="450" alt="correlation heatmap">

*Note: run `help()` on `AssetGroupVisualizer` for more visualizations. This object has many of the visualizations of the `StockVisualizer` class.*

### Analyzing data
Below are a few of the metrics you can calculate.

#### Single asset
```python
from stock_analysis import StockAnalyzer

nflx_analyzer = stock_analysis.StockAnalyzer(nflx)
nflx_analyzer.annualized_volatility()
```

#### Asset group
Methods of the `StockAnalyzer` class can be accessed by name with the `AssetGroupAnalyzer` class's `analyze()` method.
```python
from stock_analysis import AssetGroupAnalyzer

faang_analyzer = AssetGroupAnalyzer(faang)
faang_analyzer.analyze('annualized_volatility')

faang_analyzer.analyze('beta')
```

### Modeling
```python
from stock_analysis import StockModeler
```

#### Time series decomposition
```python
decomposition = StockModeler.decompose(nflx, 20)
fig = decomposition.plot()
plt.show()
```

<img src="images/nflx_ts_decomposition.png?raw=true" align="center" width="450" alt="time series decomposition">

#### ARIMA
Build the model:
```python
arima_model = StockModeler.arima(nflx, 10, 1, 5)
```

Check the residuals:
```python
StockModeler.plot_residuals(arima_model)
plt.show()
```

<img src="images/arima_residuals.png?raw=true" align="center" width="650" alt="ARIMA residuals">

Plot the predictions:
```python
arima_ax = StockModeler.arima_predictions(
    arima_model, start=start, end=end,
    df=nflx, ax=axes[0], title='ARIMA'
)
plt.show()
```

<img src="images/arima_predictions.png?raw=true" align="center" width="450" alt="ARIMA predictions">

#### Linear regression
Build the model:
```python
X, Y, lm = StockModeler.regression(nflx)
```

Check the residuals:
```python
StockModeler.plot_residuals(lm)
plt.show()
```

<img src="images/lm_residuals.png?raw=true" align="center" width="650" alt="linear regression residuals">

Plot the predictions:
```python
linear_reg = StockModeler.regression_predictions(
    lm, start=start, end=end,
    df=nflx, ax=axes[1], title='Linear Regression'
)
plt.show()
```

<img src="images/lm_predictions.png?raw=true" align="center" width="450" alt="linear regression predictions">
