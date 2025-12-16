# Overview

This is a summary of a Python-based YouTube data scraping project that collected comprehensive data from major news channels using the YouTube Data API v3.

## Project Objective

The project aims to collect and analyze video metrics and user engagement data from prominent news channels on YouTube, focusing on content published in October 2025, a period precedding the most recent election event in the United States.

---

# Data Collection Methodology

## Target Channels

The script monitors **10 major news channels**:

- ABC News
- CNN
- Fox News
- NBC News
- CBS News
- The Young Turks
- USA Today
- TMZ
- The Wall Street Journal
- The New York Times

## API Configuration

- **API Service**: YouTube Data API v3
- **Authentication**: API key-based authentication
- **Rate Limiting**: Built-in delays to avoid quota exhaustion

---

# Data Collection Components

## 1. Channel Statistics Collection

The script retrieves comprehensive channel-level metrics:

### Metrics Collected
- **Subscriber count** (or "Hidden" if private)
- **Total view count**
- **Total video count**
- **Channel creation date**
- **Channel description** (first 200 characters)

### Output
- File: `news_channel_stats.csv`
- Records: 9 channels successfully scraped
- Failed: TMZ (could not be fetched)

### Implementation Features
- Retry mechanism (up to 3 attempts per channel)
- Error handling for failed API requests
- 1-second delay between requests

---

## 2. Video Metadata Collection

The script collects detailed video information for a specific month.

### Parameters
- **Target Period**: October 2025 (Year: 2025, Month: 10)
- **Maximum Videos per Channel**: 100
- **Batch Size**: 50 videos per API request

### Video Metrics Collected
- Video ID
- Title
- Publication date/time
- View count
- Like count
- Comment count
- Video duration

### Results Summary
```
ABC News:              100 videos
CNN:                   100 videos
Fox News:              100 videos
NBC News:              100 videos
CBS News:              100 videos
The Young Turks:       100 videos
USA Today:             100 videos
TMZ:                     0 videos (failed)
Wall Street Journal:    37 videos
New York Times:         81 videos
```

### Output
- File: `october_videos.csv`
- Total Videos: 818 videos collected

---

## 3. Comments Collection

The most extensive component collects all comments (including replies) from the collected videos.

### Features
- Retrieves top-level comments
- Includes all replies to comments
- Handles disabled comments gracefully
- 100 comments per API request
- 0.5-second delay between video requests

### Comment Data Collected
- Video ID
- Comment ID
- Author display name
- Comment text
- Publication timestamp
- Like count on comment

### Results
- **Total Comments Collected**: 293,848 comments
- **Videos with Disabled Comments**: 2 videos encountered
- **Comments per Video**: Ranges from 0 to 5,564

### Notable Engagement
High-engagement videos include:
- Maximum comments on single video: 5,564
- Multiple videos with 3,000+ comments
- Average engagement varies significantly by channel

### Output
- File: `october_video_comments.csv`
- Processing Time: ~18 minutes

---

# Technical Implementation

## Error Handling

The script implements robust error handling:

1. **API Request Failures**: Try-catch blocks around all API calls
2. **Retry Logic**: Up to 3 attempts for channel statistics
3. **Disabled Comments**: Graceful handling with error messages
4. **Missing Data**: Type casting with fallback to None/0

## Rate Limiting Strategy

To respect API quotas:
- 0.1-second delay during pagination
- 0.5-second delay between videos
- 1-second delay between channels
- 2-second delay before retry attempts

## Data Validation

- Integer casting with error handling
- Handling of hidden subscriber counts
- Timestamp parsing with timezone awareness
- Empty result set handling

---

# Data Output Structure

## Channel Statistics CSV
```
Columns: channel_name, subscribers, total_views, total_videos, 
         published_at, description, label
```

## Videos CSV
```
Columns: Channel, Video ID, Title, Published, Views, 
         Likes, Comments, Duration
```

## Comments CSV
```
Columns: video_id, comment_id, author, text, 
         published_at, like_count
```

---

# Key Insights from Collection

## Success Metrics
- **Overall Success Rate**: 90% (9 out of 10 channels)
- **Video Collection**: 818 videos successfully retrieved
- **Comment Collection**: Nearly 300,000 comments gathered
- **API Efficiency**: Implemented optimal pagination and batching

## Challenges Encountered
1. TMZ channel consistently failed to respond
2. Two videos had comments disabled
3. Variable video counts across channels for the same period

---

# Potential Analysis Applications

This dataset enables multiple research directions:

1. **Engagement Analysis**: Compare comment rates across channels
2. **Content Strategy**: Analyze posting frequency and timing
3. **Sentiment Analysis**: Process comment text for sentiment
4. **Viral Content**: Identify high-performing video characteristics
5. **Cross-Channel Comparison**: Compare metrics across news outlets
6. **Temporal Patterns**: Analyze when channels post and when users engage

---
# Conclusion

This Python script successfully demonstrates a comprehensive YouTube data collection pipeline, gathering multi-level data (channel, video, and comment) from major news outlets. The resulting dataset of 818 videos and 293,848 comments provides rich material for analyzing news media engagement patterns on YouTube.

The implementation showcases best practices in API usage, including rate limiting, error handling, and efficient batch processing, making it a robust foundation for YouTube data research projects.