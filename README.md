# Bitcoin-DCA
Smart Dollar Cost Averaging Application


```python
import ccxt
import pandas as pd

# Configuration
api_key = 'your_api_key'
api_secret = 'your_api_secret'
exchange_name = 'binance'  # Example: 'binance', 'coinbasepro', etc.
base_currency = 'BTC'
quote_currency = 'USD'
base_investment = 100.0  # Base amount in quote_currency to invest

# Initialize Exchange
exchange_class = getattr(ccxt, exchange_name)
exchange = exchange_class({
    'apiKey': api_key,
    'secret': api_secret,
    'enableRateLimit': True,
})

def fetch_historical_data(symbol, timeframe='1d', limit=90):
    bars = exchange.fetch_ohlcv(symbol, timeframe=timeframe, limit=limit)
    df = pd.DataFrame(bars, columns=['timestamp', 'open', 'high', 'low', 'close', 'volume'])
    df['timestamp'] = pd.to_datetime(df['timestamp'], unit='ms')
    df.set_index('timestamp', inplace=True)
    return df

def calculate_moving_average(df, period=90):
    return df['close'].rolling(window=period).mean()

def determine_investment_amount(current_price, ma_price):
    if current_price < ma_price:
        return base_investment * (ma_price / current_price)  # Increase investment
    else:
        return base_investment * (current_price / ma_price)  # Decrease investment

def main():
    symbol = f'{base_currency}/{quote_currency}'
    df = fetch_historical_data(symbol)
    ma_price = calculate_moving_average(df).iloc[-1]  # Get the latest MA
    current_price = df['close'].iloc[-1]
    amount_to_invest = determine_investment_amount(current_price, ma_price)

    print(f"Current Price: {current_price}")
    print(f"90-day Moving Average Price: {ma_price}")
    print(f"Amount to Invest: {amount_to_invest}")

    # Placeholder for buying Bitcoin, to be replaced with actual API call
    # exchange.create_market_buy_order(symbol, amount_to_invest)

if __name__ == "__main__":
    main()
```
