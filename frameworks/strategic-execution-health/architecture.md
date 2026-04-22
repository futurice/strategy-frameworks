# Strategic Execution Health Framework - Technical Architecture

## System Overview

This framework analyzes communication and project data to measure strategic alignment. The system ingests data from Slack/Teams and Jira/Linear, detects conversation threads, classifies them by strategic intent, and calculates health metrics.

## Architecture Diagram

```
┌─────────────────────────────────────────────┐
│           Data Sources                      │
│  - Slack/Teams (communication)              │
│  - Jira/Linear (projects)                   │
│  - Strategy Docs (company vision)           │
└──────────────────┬──────────────────────────┘
                   │
                   ▼
┌─────────────────────────────────────────────┐
│        Data Ingestion Layer                 │
│  - Slack export parser                      │
│  - Jira/Linear API connectors               │
│  - Document loader                          │
│  - Data validation & normalization          │
└──────────────────┬──────────────────────────┘
                   │
                   ▼
┌─────────────────────────────────────────────┐
│       Thread Detection Layer                │
│  - Message clustering (97%+ reduction)      │
│  - Time-based grouping                      │
│  - @mention tracking                        │
│  - Topic detection                          │
└──────────────────┬──────────────────────────┘
                   │
                   ▼
┌─────────────────────────────────────────────┐
│    AI Classification Layer                  │
│  - GPT-4 sample labeling (500-1k threads)   │
│  - Local classifier training                │
│  - Bulk classification (local model)        │
│  - Intent assignment                        │
└──────────────────┬──────────────────────────┘
                   │
                   ▼
┌─────────────────────────────────────────────┐
│      Metrics Calculation Layer              │
│  - Intent Balance (actual vs target)        │
│  - Velocity (response/cycle times)          │
│  - Friction (blocker detection)             │
│  - Coherence (sentiment, alignment)         │
└──────────────────┬──────────────────────────┘
                   │
                   ▼
┌─────────────────────────────────────────────┐
│          Output Layer                       │
│  - Streamlit dashboard                      │
│  - Executive reports                        │
│  - LLM-powered insights                     │
│  - Visualizations                           │
└─────────────────────────────────────────────┘
```

## Technology Stack

### Core
- **Python**: 3.8+
- **pandas**: Data manipulation
- **numpy**: Numerical operations

### Data Processing
- **python-dotenv**: Environment variables
- **pyyaml**: Configuration files
- **slack-sdk**: Slack API integration (optional)
- **atlassian-python-api**: Jira integration (optional)

### Machine Learning / AI
- **openai**: GPT-4 classification and insights
- **scikit-learn**: Local classifier training
- **sentence-transformers**: Text embeddings
- **transformers**: NLP models

### NLP
- **nltk**: Text processing
- **spacy**: Entity extraction (optional)
- **textblob**: Sentiment analysis

### Visualization
- **streamlit**: Interactive dashboard
- **plotly**: Interactive charts
- **matplotlib**: Static visualizations
- **seaborn**: Statistical plots

## Data Flow

### Phase 1: Data Collection

**Input**: Slack export, Jira API credentials, strategy documents

**Process**:
```python
# Load Slack export
slack_data = load_slack_export("data/slack_export/")

# Fetch Jira issues
jira_data = fetch_jira_issues(
    api_url="https://org.atlassian.net",
    project_keys=["PROJ1", "PROJ2"]
)

# Load strategy docs
strategy_docs = load_strategy_documents("data/strategy/")
```

**Output**: Raw data DataFrames
- `messages_df`: Slack messages
- `issues_df`: Jira issues
- `strategy_df`: Strategy documents

---

### Phase 2: Thread Detection

**Input**: Raw Slack messages (typically 100k+)

**Challenge**: Too many messages to analyze individually

**Solution**: Cluster into conversation threads

```python
def detect_threads(messages_df, time_window=60):
    """
    Cluster messages into conversation threads.

    Args:
        messages_df: DataFrame with columns [timestamp, user, channel, text]
        time_window: Minutes between messages to consider same thread

    Returns:
        threads_df: DataFrame of threads
    """
    threads = []
    current_thread = []

    for idx, msg in messages_df.iterrows():
        # Check if new thread
        if should_start_new_thread(msg, current_thread, time_window):
            if current_thread:
                threads.append(aggregate_thread(current_thread))
            current_thread = [msg]
        else:
            current_thread.append(msg)

    return pd.DataFrame(threads)

def should_start_new_thread(msg, current_thread, time_window):
    """Determine if message starts a new thread."""
    if not current_thread:
        return True

    last_msg = current_thread[-1]
    time_diff = (msg['timestamp'] - last_msg['timestamp']).total_seconds() / 60

    # New thread if:
    # 1. Time gap > window
    # 2. Different channel
    # 3. Explicit topic change
    return (
        time_diff > time_window or
        msg['channel'] != last_msg['channel'] or
        is_topic_change(msg, current_thread)
    )
```

**Output**: Threads DataFrame (2-3k threads from 100k+ messages)

**Columns**:
- `thread_id`: Unique identifier
- `start_time`: Thread start timestamp
- `end_time`: Thread end timestamp
- `participants`: List of users
- `channel`: Channel name
- `message_count`: Number of messages
- `text`: Concatenated thread text

---

### Phase 3: Strategic Classification

**Input**: Threads DataFrame, strategic intent definitions

**Process**:

**Step 1: GPT-4 Sample Labeling**
```python
def classify_sample_with_gpt(threads_df, sample_size=500):
    """Use GPT-4 to label sample threads."""
    sample = threads_df.sample(n=sample_size)
    labels = []

    for thread_text in sample['text']:
        prompt = f"""
        Classify this conversation into one of these strategic intents:

        Intent 0: Not Strategically Aligned (routine ops, bug fixes)
        Intent 1: Product Expansion (new features, verticals)
        Intent 2: Customer Growth (sales, marketing)
        Intent 3: Platform Modernization (unified platform, tech)
        Intent 4: Business Model Innovation (pricing, upsell)
        Intent 5: Technical Foundation (infra, DevOps, tech debt)

        Conversation:
        {thread_text}

        Return only the intent number (0-5).
        """

        response = openai.ChatCompletion.create(
            model="gpt-4",
            messages=[{"role": "user", "content": prompt}],
            temperature=0
        )

        labels.append(int(response.choices[0].message.content))

    return sample, labels
```

**Step 2: Train Local Classifier**
```python
from sklearn.linear_model import LogisticRegression
from sentence_transformers import SentenceTransformer

def train_local_classifier(sample_threads, gpt_labels):
    """Train local classifier on GPT-labeled data."""
    # Get embeddings
    embedding_model = SentenceTransformer('all-MiniLM-L6-v2')
    embeddings = embedding_model.encode(sample_threads['text'].tolist())

    # Train classifier
    classifier = LogisticRegression(max_iter=1000)
    classifier.fit(embeddings, gpt_labels)

    return classifier, embedding_model
```

**Step 3: Classify All Threads**
```python
def classify_all_threads(threads_df, classifier, embedding_model):
    """Classify all threads using local model."""
    embeddings = embedding_model.encode(threads_df['text'].tolist())
    predictions = classifier.predict(embeddings)

    threads_df['strategic_intent'] = predictions
    return threads_df
```

**Output**: Classified threads DataFrame with `strategic_intent` column

---

### Phase 4: Metrics Calculation

#### 4.1 Intent Balance

```python
def calculate_intent_balance(classified_threads, targets):
    """
    Calculate intent balance: actual vs target allocation.

    Args:
        classified_threads: DataFrame with strategic_intent column
        targets: Dict of intent -> target percentage

    Returns:
        balance_df: DataFrame with intent, actual, target, gap
    """
    # Calculate actual distribution
    intent_counts = classified_threads['strategic_intent'].value_counts()
    total = len(classified_threads)
    actual = {intent: (count / total) * 100
              for intent, count in intent_counts.items()}

    # Calculate gaps
    balance = []
    for intent, target in targets.items():
        actual_pct = actual.get(intent, 0)
        gap = actual_pct - target
        balance.append({
            'intent': intent,
            'actual': actual_pct,
            'target': target,
            'gap': gap,
            'status': 'on_track' if abs(gap) < 5 else 'off_track'
        })

    return pd.DataFrame(balance)
```

#### 4.2 Velocity

```python
def calculate_velocity(classified_threads):
    """
    Calculate response and cycle times per intent.

    Returns:
        velocity_df: DataFrame with intent, response_time, cycle_time
    """
    velocity = []

    for intent in classified_threads['strategic_intent'].unique():
        intent_threads = classified_threads[
            classified_threads['strategic_intent'] == intent
        ]

        # Response time: time to first reply
        response_times = []
        for _, thread in intent_threads.iterrows():
            if thread['message_count'] > 1:
                response_time = calculate_response_time(thread)
                response_times.append(response_time)

        # Cycle time: start to end
        cycle_times = []
        for _, thread in intent_threads.iterrows():
            cycle_time = (
                thread['end_time'] - thread['start_time']
            ).total_seconds() / 3600  # hours

            cycle_times.append(cycle_time)

        velocity.append({
            'intent': intent,
            'avg_response_time_min': np.mean(response_times),
            'avg_cycle_time_hours': np.mean(cycle_times),
            'velocity_score': calculate_velocity_score(
                np.mean(response_times),
                np.mean(cycle_times)
            )
        })

    return pd.DataFrame(velocity)

def calculate_response_time(thread):
    """Calculate time to first response in minutes."""
    messages = sorted(thread['messages'], key=lambda x: x['timestamp'])
    if len(messages) < 2:
        return 0

    first_msg = messages[0]
    second_msg = messages[1]
    return (second_msg['timestamp'] - first_msg['timestamp']).total_seconds() / 60
```

#### 4.3 Friction

```python
BLOCKER_KEYWORDS = [
    'blocked', 'blocker', 'stuck', 'waiting on', 'waiting for',
    'blocked by', 'can\'t proceed', 'dependency', 'bottleneck',
    'delayed', 'on hold', 'pending'
]

BLOCKER_CATEGORIES = {
    'legal': ['legal', 'compliance', 'regulation', 'approval'],
    'resource': ['budget', 'headcount', 'resource', 'capacity'],
    'decision': ['decision', 'approval', 'waiting on', 'stakeholder'],
    'technical': ['bug', 'technical', 'infrastructure', 'outage'],
    'dependency': ['depends on', 'dependency', 'waiting for team']
}

def detect_blockers(classified_threads):
    """
    Detect and categorize blockers.

    Returns:
        friction_df: DataFrame with intent, friction_index, top_blockers
    """
    friction = []

    for intent in classified_threads['strategic_intent'].unique():
        intent_threads = classified_threads[
            classified_threads['strategic_intent'] == intent
        ]

        # Detect blockers
        threads_with_blockers = 0
        blocker_details = []

        for _, thread in intent_threads.iterrows():
            text_lower = thread['text'].lower()

            # Check for blocker keywords
            has_blocker = any(
                keyword in text_lower
                for keyword in BLOCKER_KEYWORDS
            )

            if has_blocker:
                threads_with_blockers += 1

                # Categorize blocker
                category = categorize_blocker(text_lower)
                blocker_details.append({
                    'thread_id': thread['thread_id'],
                    'category': category,
                    'text_snippet': extract_blocker_context(thread['text'])
                })

        friction_index = (threads_with_blockers / len(intent_threads)) * 100

        friction.append({
            'intent': intent,
            'friction_index': friction_index,
            'threads_with_blockers': threads_with_blockers,
            'total_threads': len(intent_threads),
            'severity': 'high' if friction_index > 30 else
                       'medium' if friction_index > 15 else 'low',
            'top_blockers': get_top_blockers(blocker_details)
        })

    return pd.DataFrame(friction)

def categorize_blocker(text):
    """Categorize blocker type."""
    for category, keywords in BLOCKER_CATEGORIES.items():
        if any(kw in text for kw in keywords):
            return category
    return 'other'
```

#### 4.4 Coherence

```python
from textblob import TextBlob

def analyze_coherence(classified_threads):
    """
    Analyze cross-team alignment and sentiment.

    Returns:
        coherence_df: DataFrame with intent, coherence_score, sentiment
    """
    coherence = []

    for intent in classified_threads['strategic_intent'].unique():
        intent_threads = classified_threads[
            classified_threads['strategic_intent'] == intent
        ]

        # Sentiment analysis
        sentiments = []
        for _, thread in intent_threads.iterrows():
            blob = TextBlob(thread['text'])
            sentiments.append(blob.sentiment.polarity)

        avg_sentiment = np.mean(sentiments)
        sentiment_variance = np.var(sentiments)

        # Team participation
        unique_participants = set()
        for _, thread in intent_threads.iterrows():
            unique_participants.update(thread['participants'])

        team_diversity = len(unique_participants)

        # Coherence score
        coherence_score = calculate_coherence_score(
            sentiment_variance,
            team_diversity,
            len(intent_threads)
        )

        coherence.append({
            'intent': intent,
            'coherence_score': coherence_score,
            'avg_sentiment': avg_sentiment,
            'sentiment_variance': sentiment_variance,
            'unique_participants': team_diversity,
            'status': 'high' if coherence_score > 0.8 else
                     'medium' if coherence_score > 0.5 else 'low'
        })

    return pd.DataFrame(coherence)

def calculate_coherence_score(sentiment_var, team_diversity, thread_count):
    """Calculate overall coherence score."""
    # Lower variance = higher coherence
    sentiment_coherence = 1 / (1 + sentiment_var)

    # More participants = more discussion
    participation_score = min(1.0, team_diversity / 20)

    # More threads = more engagement
    engagement_score = min(1.0, thread_count / 100)

    return (
        sentiment_coherence * 0.5 +
        participation_score * 0.3 +
        engagement_score * 0.2
    )
```

---

### Phase 5: Insight Generation

**LLM-Powered Insights**:
```python
def generate_insights(balance_df, velocity_df, friction_df, coherence_df):
    """Generate executive insights using LLM."""
    summary = {
        'balance': balance_df.to_dict('records'),
        'velocity': velocity_df.to_dict('records'),
        'friction': friction_df.to_dict('records'),
        'coherence': coherence_df.to_dict('records')
    }

    prompt = f"""
    Analyze this strategic execution health data and provide:

    1. **Key Findings** (3-5 bullet points)
    2. **Root Cause Analysis** (why gaps exist)
    3. **Actionable Recommendations** (specific, prioritized actions)

    Data Summary:
    {json.dumps(summary, indent=2)}

    Format as executive summary, concise and actionable.
    """

    response = openai.ChatCompletion.create(
        model="gpt-4",
        messages=[{"role": "user", "content": prompt}],
        temperature=0.7
    )

    return response.choices[0].message.content
```

---

### Phase 6: Dashboard

**Streamlit Dashboard**:
```python
import streamlit as st
import plotly.express as px

def main():
    st.title("Strategic Execution Health Dashboard")

    # Load data
    balance = load_intent_balance()
    velocity = load_velocity_metrics()
    friction = load_friction_analysis()
    coherence = load_coherence_scores()

    # Overall alignment score
    alignment_score = calculate_overall_alignment(balance)
    st.metric("Overall Strategic Alignment", f"{alignment_score:.1f}%")

    # Intent balance chart
    st.subheader("Intent Balance: Actual vs Target")
    fig = px.bar(
        balance,
        x='intent',
        y=['actual', 'target'],
        barmode='group',
        title="Work Distribution by Strategic Intent"
    )
    st.plotly_chart(fig)

    # Velocity by intent
    st.subheader("Velocity by Intent")
    fig = px.scatter(
        velocity,
        x='avg_response_time_min',
        y='avg_cycle_time_hours',
        size='velocity_score',
        color='intent',
        title="Response Time vs Cycle Time"
    )
    st.plotly_chart(fig)

    # Friction heatmap
    st.subheader("Friction Analysis")
    st.dataframe(friction[['intent', 'friction_index', 'severity', 'top_blockers']])

    # Coherence scores
    st.subheader("Team Coherence")
    fig = px.bar(
        coherence,
        x='intent',
        y='coherence_score',
        color='status',
        title="Cross-Team Alignment by Intent"
    )
    st.plotly_chart(fig)

if __name__ == "__main__":
    main()
```

---

## File Structure

```
strategic-execution-health/
├── app.py                              # Streamlit dashboard
├── requirements.txt                    # Dependencies
├── config/
│   ├── config.yml                      # Main configuration
│   ├── sample_config.yml               # Example config
│   └── strategic_intents.yml           # Intent definitions
├── scripts/
│   ├── strategic_alignment/
│   │   ├── detect_threads.py           # Thread detection
│   │   ├── classify_threads.py         # GPT classification
│   │   ├── train_classifier.py         # Local model training
│   │   ├── calculate_clockspeed.py     # Velocity metrics
│   │   └── detect_blockers.py          # Friction analysis
│   └── utils/
│       ├── data_loading.py             # Data ingestion
│       ├── slack_parser.py             # Slack export parser
│       └── jira_connector.py           # Jira API client
├── data/                               # Data directory (gitignored)
│   ├── slack_export/
│   ├── jira_export/
│   ├── strategy/
│   └── processed/
│       ├── detected_threads.json
│       └── classified_threads.json
├── outputs/                            # Generated outputs (gitignored)
│   ├── reports/
│   └── visualizations/
└── examples/
    └── analysis_example.ipynb          # Example notebook
```

## Configuration

### Main Config (`config/config.yml`)

```yaml
data_sources:
  slack:
    export_path: "data/slack_export/"

  jira:
    api_url: "https://your-org.atlassian.net"
    api_token: ${JIRA_API_TOKEN}
    project_keys:
      - "PROJ1"
      - "PROJ2"

  strategy_docs:
    path: "data/strategy/"

thread_detection:
  time_window_minutes: 60
  min_messages: 2

classification:
  sample_size: 500
  model: "gpt-4"
  embedding_model: "all-MiniLM-L6-v2"

metrics:
  velocity_thresholds:
    excellent_response_min: 120
    good_response_min: 480
    excellent_cycle_hours: 168  # 1 week

  friction_thresholds:
    low: 15
    medium: 30
    high: 50

  coherence_thresholds:
    high: 0.8
    medium: 0.5
    low: 0.3
```

### Strategic Intents (`config/strategic_intents.yml`)

```yaml
intents:
  0:
    name: "Not Strategically Aligned"
    description: "Routine operations, bug fixes, maintenance"

  1:
    name: "Product Expansion"
    description: "New features, verticals, product lines"

  2:
    name: "Customer Growth"
    description: "Sales, marketing, new segments"

  3:
    name: "Platform Modernization"
    description: "Unified platform, tech modernization"

  4:
    name: "Business Model Innovation"
    description: "Pricing, cross-sell, upsell"

  5:
    name: "Technical Foundation"
    description: "Infrastructure, DevOps, tech debt"

targets:
  1: 25
  2: 20
  3: 20
  4: 15
  5: 20
```

## Performance Considerations

### Data Volume
- **Input**: 100k+ Slack messages
- **After thread detection**: 2-3k threads (97%+ reduction)
- **Processing time**: 5-15 minutes for full pipeline
- **Memory**: <2GB RAM

### Cost Optimization

**Hybrid Approach** (Recommended):
- GPT classifies 500-1000 sample threads: $0.50-1.00
- Train local classifier: ~$0.10 (embeddings)
- Local classifier processes remaining: $0
- **Total: <$2 per analysis**

vs. Full GPT: $50-100

## Security & Privacy

### API Keys
Store in `.env` file:
```
OPENAI_API_KEY=sk-...
JIRA_API_TOKEN=...
SLACK_TOKEN=xoxb-...
```

### Data Privacy
- Slack data may contain sensitive information
- Store data locally, don't commit to git
- Consider anonymizing user names in reports
- Respect data retention policies

## Testing

```python
# tests/test_thread_detection.py
def test_thread_detection():
    messages = load_sample_messages()
    threads = detect_threads(messages, time_window=60)
    assert len(threads) < len(messages)
    assert all('thread_id' in t for t in threads)

# tests/test_classification.py
def test_intent_classification():
    thread_text = "Working on new customer acquisition campaign"
    intent = classify_thread(thread_text)
    assert intent == 2  # Customer Growth
```

## Deployment

### Local
```bash
pip install -r requirements.txt
python scripts/strategic_alignment/detect_threads.py
streamlit run app.py
```

### Scheduled Analysis
```bash
# Weekly cron job
0 9 * * 1 cd /path/to/framework && python scripts/run_full_analysis.py
```

## Future Enhancements

1. **Real-time integration**: Connect directly to Slack/Jira APIs (no export needed)
2. **Predictive analytics**: Forecast strategic drift before it happens
3. **Team-level dashboards**: Drill-down to individual team metrics
4. **Integration with OKRs**: Link strategic intents to OKR tracking
5. **Multi-org benchmarking**: Compare across companies or divisions
