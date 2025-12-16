## Summary
The approach to our analyis starts with a birds-eye view from the channel level based on historical subscription data before the video level analysis focusing on October 2025 performance metrics. The channel-level analysis examines subscriber base, total views distribution, followed by the October-specific video-level analysis that delves into engagement metrics including views, likes, comments, and content duration investigating influence and the sentiments underlying them both from the perspective of a producer and consumer of the content.

## 1. Channel-level Analysis

### Overview
The subscriber analysis orders channels by their first publication date, providing insights into the established presence and audience reach of each channel. Total historical views aggregate all video views per channel, offering insights into cumulative audience engagement over time.

### Insights
- Channels are ranked chronologically based on their earliest video publication date
- Subscriber/View counts indicate the overall channel size and potential reach
- Established channels (earlier publication dates) may show different subscriber patterns compared to newer entrants

Subscriber counts for each channel, ordered by publication timeline
![alt text](<../../Outputs/Figures/Subscribers per Channel.png>)

- **Absolute Views**: Bar chart showing total view counts per channel
![alt text](<../../Outputs/Figures/Total Views per Channel.png>)

- **Relative Share**: Pie chart illustrating each channel's percentage contribution to overall viewership
![alt text](<../../Outputs/Figures/Total Views per Channel.png>)

### Insights
- View distribution reveals which channels command the largest audience share
- The pie chart provides quick visual assessment of market concentration
- Channels with higher view counts demonstrate stronger historical content performance or larger subscriber bases

---

## 2. Video-level Analysis (October 2025)

### 2.1 Methodology
October video data was analyzed using four key engagement metrics:
1. **Total Views**: Aggregate views for October content
2. **Total Likes**: Sum of likes received
3. **Total Comments**: Comment volume indicating audience interaction
4. **Total Duration**: Cumulative video length (converted from ISO 8601 format to seconds)

### Key Performance Indicators

#### Views
- Primary engagement metric showing content reach
- Ranked channels identify top performers in audience attraction

#### Likes
- Indicates content quality and audience approval
- Like-to-view ratio can reveal engagement quality

#### Comments
- Measures active audience participation
- Higher comment counts suggest controversial or engaging content

#### Duration
- Total content production volume
- Longer durations may indicate comprehensive coverage or frequent posting

---

### 2.2 Influence Analysis

We developed a custom influence metric to capture multi-dimensional engagement:
```python
Influence = (3 × Likes × 5 × Comments) × Views
```

### Rationale:

- **Likes weighted 3×**: Indicates positive reception
- **Comments weighted 5×**: Reflects deeper engagement (discussions, debates)
- **Views as multiplier**: Scales influence by reach

### Scaling for Analysis:
```python
Influence_scaled = log(1 + Influence)
```

Log transformation applied to:

- Reduce skewness from viral videos
- Enable meaningful visualization
- Allow cross-channel comparison

### 2.3 Ranking Analysis

Videos were ranked by scaled influence to identify:

- Most influential content per channel
- Patterns in high-performing videos
- Distribution of influence across channels

### 2.4 Distribution Visualization

### Box Plot Analysis
```python
sns.boxplot(x="Channel", y="Influence_scaled", data=df, palette="Set2")
```
![alt text](<../../Outputs/Figures/Scaled Influence.png>)
**Reveals:**

- Median influence per channel
- Spread and variability
- Outliers (viral videos)
- Inter-quartile ranges

### Centered Horizontal Bars

A unique visualization showing channel influence split symmetrically around a central axis:
```python
half_total = channel_influence / 2
neg_half = -half_total  # left side
pos_half = half_total   # right side
```
![alt text](<../../Outputs/Figures/Channel Bars.png>)
**Purpose:** Emphasizes relative magnitude while maintaining visual balance

---

## 3. Video Duration Analysis

### 3.1 Duration Distribution by Channel

Duration was analyzed on a **log scale** due to extreme variability (shorts vs. long-form content):
```python
df["Duration_minutes"] = df["Duration_seconds"] / 60
plt.yscale("log")
```
![alt text](<../../Outputs/Figures/Duration Distribution.png>)
### 3.2 Box Plot Visualization

Multi-colored box plots reveal:

- **Median duration** per channel
- **Range** of content lengths
- **Outliers** (exceptionally long/short videos)
- **Content strategy differences** (e.g., short news clips vs. in-depth analysis)

---
## 4. Topic Modeling with BERTopic

### 4.1 Methodology

**Model Used:** BERTopic with sentence transformers
```python
embedding_model = SentenceTransformer("all-MiniLM-L6-v2")

topic_model = BERTopic(
    embedding_model=embedding_model,
    min_topic_size=3,  # Granular topics
    verbose=False
)
```

### 4.2 Per-Channel Analysis

Topic modeling was performed **separately for each channel** to identify:

- Channel-specific coverage themes
- Dominant narratives
- Niche vs. broad topic distributions

### Process:

1. Filter titles by channel
2. Generate embeddings using sentence transformers
3. Cluster titles into topics
4. Extract representative keywords
5. Identify dominant topic per channel

### 4.3 Output

Results saved to `channel_themes.csv` containing:

- Topic ID
- Topic keywords
- Document count per topic
- Channel assignment

### Dominant Topic Identification
```python
dominant_per_channel = (
    dominant_topics.sort_values(["Channel", "Count"], ascending=[True, False])
    .groupby("Channel")
    .head(1)
)
```

## 5. Sentiment Analysis

### 5.1 Model Architecture

**Base Model:** `cardiffnlp/twitter-roberta-base-sentiment-latest`

- Fine-tuned RoBERTa for sentiment classification
- Optimized for social media text
- Three-class output: NEGATIVE, NEUTRAL, POSITIVE

### Key Features:

- **Batch processing** for efficiency (16 titles per batch)
- **GPU acceleration** when available
- **Confidence scores** for each prediction
- **All class probabilities** retained for analysis

### 5.2 Implementation
```python
def analyze_batch(self, titles: List[str], batch_size: int = 16):
    # Tokenize
    inputs = self.tokenizer(batch, return_tensors="pt", 
                           truncation=True, max_length=512)
    
    # Get predictions
    with torch.no_grad():
        outputs = self.model(**inputs)
        logits = outputs.logits
    
    # Calculate probabilities
    probabilities = torch.nn.functional.softmax(logits, dim=-1)
```

### 5.3 Results

Output DataFrame includes:

- `sentiment`: Predicted class (NEGATIVE/NEUTRAL/POSITIVE)
- `confidence`: Probability of predicted class
- `positive_score`: (positive)
- `neutral_score`:  (neutral)
- `negative_score`: (negative)

### 5.4 Visualization: Sentiment by Channel

Grouped bar plot showing count of videos per sentiment per channel:
```python
sns.barplot(data=channel_sentiment_counts, 
            x='Channel', y='count', hue='sentiment')
```
![alt text](<../../Outputs/Figures/Sentiment Counts by Channel.png>)
## 6. Emotion Classification

### 6.1 Beyond Sentiment: Granular Emotions

While sentiment provides valence (positive/negative), emotion classification identifies **specific affective states**.

**Model:** `j-hartmann/emotion-english-distilroberta-base`

**Emotions Detected:**

- Anger
- Disgust
- Fear
- Joy
- Sadness
- Surprise
- Neutral

### 6.2 Implementation
```python
classifier = pipeline(
    "text-classification",
    model="j-hartmann/emotion-english-distilroberta-base",
    top_k=None  # All emotion scores returned
)

def classify_emotion(title):
    results = classifier(str(title))[0]
    top_emotion = max(results, key=lambda x: x['score'])
    return top_emotion['label'], round(top_emotion['score'], 4)
```

### 6.3 Analysis Output

- Emotion distribution across all videos
- Average confidence by emotion
- Channel-specific emotion patterns
- Example titles for each emotion

## 7. Influence-Sentiment Analysis

### 7.1 Research Question

**Do videos with certain sentiments generate more influence?**

### 7.2 Methodology

### Aggregate Analysis
```python
sentiment_influence = (
    df.groupby("sentiment")["Influence_scaled"]
      .sum()
      .sort_values()
)
```

### Visualization Approaches

**1. Centered Horizontal Bars**

- Symmetrical display around zero axis
- Color-coded by sentiment (red=negative, gray=neutral, green=positive)
- Ranked by total influence

![alt text](<../../Outputs/Figures/Influence Bars by Sentiment.png>)

**2. Box Plot Distribution**
```python
sns.boxplot(data=df, x='sentiment', y='Influence_scaled', palette='Set2')
```
![alt text](<../../Outputs/Figures/Video Influence by Sentiment.png>)

Shows distribution of influence within each sentiment category

# Conclusion

This analysis provides a foundation for understanding patterns in competitive positioning and performance across news channels. The combination of historical metrics (subscribers, total views) and recent performance data (October metrics) enables both strategic assessment and tactical decision-making for content optimization and audience growth strategies.

Future analyses would incorporate time-series trends, demographic insights, and content categorization to deepen understanding of performance drivers.

