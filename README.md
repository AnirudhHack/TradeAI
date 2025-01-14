# Trade AI 

![image](https://github.com/user-attachments/assets/15382a19-b017-414b-b884-91d2d6defa0e)

## Introduction

The Trade AI is an AI-powered cryptocurrency trading system that automates trading decisions using real-time market data and advanced technical analysis. The system integrates multiple technologies, including Tavily Search API for fetching the latest news on assets like Bitcoin (BTC), CoinAPI for retrieving multi-timeframe price data, and Google Gemini LLM for evaluating market sentiment and trends. This combination enables the AI model to generate actionable trade suggestions, helping users make informed and data-driven decisions in the volatile cryptocurrency market.

## video Demo
https://drive.google.com/file/d/1dos1OfdyV54LxVFZ0UCWk2DqgJmDYIid/view?usp=sharing

## How it Works:

![image](https://github.com/user-attachments/assets/dcea2744-cf3f-4dcc-a39e-adc6b4a82cdd)


1. **Data Collection**:
   - The system fetches the latest news about Bitcoin (BTC) using the Tavily Search API. This news is essential for understanding the market sentiment and any potential influences on price movements.
   - It then collects multi-timeframe price data (5-minute, 1-day, and 1-month) for the BTC/USDT pair using CoinAPI. This data includes the Open, High, Low, Close, and Volume (OHLCV) values, which are crucial for performing technical analysis.
   
2. **Sentiment and Technical Analysis**:
   - The system processes the gathered news data and the technical chart data to analyze the current market sentiment. It evaluates the sentiment based on recent news, as well as price movement patterns using technical indicators such as RSI (Relative Strength Index), ATR (Average True Range), and moving averages.
   - The sentiment is assessed for different timeframes (5-minutes, 1-day, and 1-month) to ensure consistency and clarity in decision-making.
   
3. **Trade Suggestions**:
   - Based on the sentiment and technical analysis, the AI model generates trade suggestions. For each suggestion, it provides an entry price, stop loss, take profit, and the rationale behind the trade.
   - If no valid trade opportunities are identified, the AI model provides detailed reasoning for why no trades are recommended.

4. **Execution**:
   - If a valid trade is identified, the trade is executed on Hyperliquid with optimal entry, stop-loss, and take-profit levels.

## Code Explanation

### Step 1: Fetch Latest News Data from Tavily
The `fetch_tavily_data` function fetches the latest news data from the Tavily API. The function takes a query with parameters like the search query and the desired depth of search. It uses the Tavily API to obtain the relevant news articles, which are then used for sentiment analysis.

```python
def fetch_tavily_data(query: dict) -> dict:
    query_params = query.get('params', {})
    payload = {
        "api_key": TAVILY_API_KEY,
        "query": query_params.get('query'),
        "search_depth": query_params.get('search_depth', "basic"),
        "include_answer": True,
        "max_results": query_params.get('max_results', 5),
    }
    try:
        response = requests.post("https://api.tavily.com/search", json=payload)
        response.raise_for_status()
        return response.json()
    except requests.exceptions.RequestException as error:
        print("Error fetching data from Tavily:", error)
        return None
```

### Step 2: Fetch OHLCV Data from CoinAPI
The fetch_ohlcv function fetches historical price data (Open, High, Low, Close, Volume) for BTC/USDT from CoinAPI. This data is used for technical analysis and to determine market sentiment based on recent price movements.

```python
def fetch_ohlcv(symbol, period_id, time_start):
    formatted_timestamp = datetime.strptime(time_start, "%Y-%m-%dT%H:%M:%S.%f").strftime("%Y-%m-%dT%H:%M:%S")
    url = f"https://rest.coinapi.io/v1/ohlcv/{symbol}/history?period_id={period_id}&time_start={formatted_timestamp}"
    headers = {"X-CoinAPI-Key": COIN_API_KEY}
    response = requests.get(url, headers=headers)
    if response.status_code == 200 and response.content:
        return response.json()
    else:
        print(f"Failed to fetch {period_id} data. Status code: {response.status_code}")
        return []
```

### Step 3: Sentiment Analysis
The system performs sentiment analysis based on the technical chart data and news. The sentiment is evaluated for different timeframes (5-minute, 1-day, and 1-month), and an overall sentiment (Bullish, Bearish, or Neutral) is assigned.

### Step 4: Generating Trade Suggestions with Google Gemini LLM
The system uses Google Gemini LLM to generate trade suggestions based on the sentiment analysis, technical indicators, and market news. It creates a prompt that includes the latest news, sentiment analysis, and chart data, and then invokes the LLM to get a response.

### Step 5: Execute the Trade on Hyperliquid
If a valid trade is suggested, the trade is executed on Hyperliquid using the suggested entry price, stop loss, and take profit levels. The system optimizes the trade execution to maximize potential profits while managing risk.


## Conclusion
The Trade AI Project leverages AI, real-time data, and advanced analytics to provide actionable trade signals for cryptocurrency trading. By integrating news analysis, technical indicators, and sentiment evaluation, it empowers users to make informed and precise trading decisions. The automated nature of the system ensures that trades are executed with optimal conditions, helping users maximize their trading success in the volatile cryptocurrency market.
