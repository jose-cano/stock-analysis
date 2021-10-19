# stock-analysis
Based on Stefanie Molin's Hands on Data Analysis with Pandas stock_analysis package.

Package for making elements of technical analysis of a stock easier.

Updated to work on Python 3.7.12 (Google Colab's default Python version) as of 10/19/2021.

Link to original -> https://github.com/stefmolin/stock-analysis/tree/2nd_edition


## Setup on Colab
```shell
!git clone https://github.com/jose-cano/stock-analysis.git
!pip3 install -r stock-analysis/requirements.txt
```
*You must restart runtime after running commands*

Then, drag the stock_analysis folder outside the stock-analysis folder.
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
