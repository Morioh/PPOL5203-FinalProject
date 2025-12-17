# PPOL5203-FinalProject
![alt text](<Outputs/Figures/Screenshot 2025-12-16 at 15.43.07.png>)
# YouTube News Channel Analysis

A comprehensive analysis of YouTube videos from major news channels using web scraping, NLP, and sentiment analysis.

## Project Overview

This project analyzes YouTube content from 9 major news channels during October 2025, combining engagement metrics, sentiment analysis, emotion detection, and topic modeling to understand the news media landscape.

**Channels Analyzed:**
ABC News • CBS News • CNN • Fox News • NBC News • The New York Times • The Wall Street Journal • The Young Turks • USA Today

## Key Features

- **Web Scraping**: Automated collection of video metadata using YouTube Data API
- **Engagement Metrics**: Views, likes, comments, and video duration analysis
- **Influence Scoring**: Custom metric weighing engagement depth and reach
- **Sentiment Analysis**: RoBERTa-based classification (Negative/Neutral/Positive)
- **Topic Modeling**: BERTopic for identifying channel-specific themes

## 📁 Project Structure
```
PPOL5203-FinalProject/
Data/
1. raw/
- october_videos.csv
- news_channel_stats.csv
- october_video_comments.csv
2. processed/
- channel_themes.csv
- titles_with_sentiment.csv
3. Notebooks/
- Data Collection
- Channel_Data_Scrapping.ipynb
- README.md
4. Data Analysis
-Channel_Analysis.ipynb
-Video_Text_Analysis.ipynb
- Video_Title_Analysis.ipynb
-README.md
5. Outputs/
- figures/
- README.md
```

## Quick Start

### Prerequisites

Python 3.8+ / YouTube Data API key / 8GB RAM (16GB recommended) / GPU optional (speeds up transformer models)

## 🔄 Reproducibility

- **Step 1**: Data Collection
- **Step 2**: Performance Analysis
- **Step 3**: NLP Analysis
- **Step 4**: Topic Modeling


##  Methods

### 1. Data Collection
- **Tool**: YouTube Data API v3
- **Period**: October 2025
- **Fields**: Title, views, likes, comments, duration, publish date

### 2. Influence Calculation
```python
Influence = (3 × Likes + 5 × Comments) × Views
Influence_scaled = log(1 + Influence)
```

### 3. Sentiment Analysis
- **Model**: `cardiffnlp/twitter-roberta-base-sentiment-latest`
- **Classes**: Negative, Neutral, Positive
- **Method**: Batch processing with confidence scores


### 4. Topic Modeling
- **Framework**: BERTopic
- **Embeddings**: `all-MiniLM-L6-v2`
- **Strategy**: Per-channel clustering with min_topic_size=3

## Key Findings

- ✅ **Neutral titles and negative comments dominates** across all channels (85%+ of videos)
- ✅ **Clear influence hierarchy** with distinct engagement patterns
- ✅ **Channel-specific topics** reflect editorial focus areas
- ✅ **Video duration** varies significantly by channel strategy

**Note**: This project is for research and educational purposes.
