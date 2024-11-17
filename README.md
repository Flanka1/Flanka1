- 👋 Hi, I’m @Flanka1
- 👀 I’m interested in ...
- 🌱 I’m currently learning ...
- 💞️ I’m looking to collaborate on ...
- 📫 How to reach me ...
- 😄 Pronouns: ...
- ⚡ Fun fact: ...

<!---
Flanka1/Flanka1 is a ✨ special ✨ repository because its `README.md` (this file) appears on your GitHub profile.
You can click the Preview link to take a look at your changes.
--->
from flask import Flask, render_template
import openai
import pandas as pd
import numpy as np
from newsapi import NewsApiClient

# Initialize APIs
openai.api_key = "your_openai_api_key"
newsapi = NewsApiClient(api_key='your_newsapi_key')

app = Flask(__name__)

# Analyze Market
def analyze_market(symbol, data):
    levels = detect_psychological_levels(data)
    prompt = f"Analyze the market for {symbol} with data {data.tail(5).to_string()}. Psychological levels: {levels}."
    response = openai.Completion.create(model="text-davinci-003", prompt=prompt, max_tokens=100)
    return response['choices'][0]['text']

# Detect Psychological Levels
def detect_psychological_levels(data):
    return np.round(data['close'] / 50) * 50

# Fetch News
def fetch_news(keyword):
    articles = newsapi.get_everything(q=keyword, language='en', sort_by='relevance', page_size=5)
    return [f"{article['title']}: {article['url']}" for article in articles['articles']]

@app.route('/')
def home():
    # Mock Data
    data = pd.DataFrame({'close': [1850, 1860, 1875, 1880, 1900]})
    xauusd_summary = analyze_market("XAUUSD", data)
    btcusd_summary = analyze_market("BTCUSD", data)
    news = fetch_news("XAUUSD OR BTCUSD")
    
    return render_template("index.html", xauusd=xauusd_summary, btcusd=btcusd_summary, news=news)

if __name__ == "__main__":
    app.run(debug=True)
