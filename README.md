# Real-Time Reddit Analytics

A real-time data pipeline that collects live Reddit posts, processes them with **PySpark Structured Streaming**, and shows the results on an auto-refreshing **Streamlit** dashboard.

---

## How It Works

```
reddit_stream.py          spark_reddit.py                 dashboard_reddit.py
   (COLLECT)                 (PROCESS)                        (VISUALISE)
Fetches the newest   -->  PySpark Structured Streaming  -->  Streamlit dashboard
posts from 6              reads new files continuously       refreshes every 3 seconds
subreddits every          and computes live KPIs             with interactive charts
5 seconds
        |                          |
   ./reddit_data/             ./analytics/
   (JSON Lines files)         (JSON results)
```

1. **Data collection** (`reddit_stream.py`): calls Reddit's public JSON API every 5 seconds and fetches the 10 newest posts from each of 6 subreddits: `technology`, `worldnews`, `science`, `stocks`, `cryptocurrency`, and `India`. Each batch is saved as a JSON Lines file.
2. **Stream processing** (`spark_reddit.py`): PySpark Structured Streaming watches the data folder, applies a fixed schema, and continuously updates five analytics outputs.
3. **Dashboard** (`dashboard_reddit.py`): a Streamlit app reads the analytics files and shows them in 6 tabs, refreshing automatically every 3 seconds.

---

## Analytics Computed

| KPI | Description |
|---|---|
| **Top Subreddits** | Number of posts and average score per subreddit |
| **Top Authors** | Most active authors by post count |
| **Word Trends** | Most frequent words in post titles (lowercased, punctuation removed) |
| **Most Commented Posts** | Posts with the highest number of comments |
| **Active Hours** | Number of posts per hour of the day (UTC) |

---

## Dashboard Tabs

- **Overview**: total posts, top subreddit, top author
- **Subreddits**: bar chart of post count, coloured by average score
- **Authors**: top 10 most active authors
- **Words**: top 20 most frequent title words
- **Comments**: table of the most commented posts
- **Active Hours**: line chart of posting activity by hour

---

## Tech Stack

- **Python**: data collection (`requests`)
- **PySpark**: Structured Streaming, schema definition, aggregations
- **Pandas**: writing results from each micro-batch
- **Streamlit** + **Altair**: interactive, auto-refreshing dashboard

---

## Getting Started

### Prerequisites
- Python 3.8+
- Java 8 or 11 (required by PySpark)

### Installation
```bash
pip install -r Requirements.txt
```

### Run (in three separate terminals)
```bash
# 1. Start collecting live Reddit data
python reddit_stream.py

# 2. Start the Spark streaming job
python spark_reddit.py

# 3. Launch the dashboard
streamlit run dashboard_reddit.py
```
Then open the URL Streamlit shows (usually `http://localhost:8501`).

---

## Project Structure

```
├── reddit_stream.py      # Fetches live posts and writes JSON files
├── spark_reddit.py       # PySpark Structured Streaming analytics
├── dashboard_reddit.py   # Streamlit dashboard
├── Requirements.txt      # Python dependencies
└── README.md
```
`reddit_data/` and `analytics/` are created automatically when the scripts run.

---

## Future Improvements

- **Remove duplicate posts:** the same post can be fetched more than once, so store each post's ID and deduplicate it in Spark to keep the counts accurate.
- **Filter stop words** (e.g. "the", "a", "to") so the word trends show more meaningful words.
- **Add sentiment analysis** on post titles.
- **Use Kafka** in place of the file folder for production-grade streaming.
