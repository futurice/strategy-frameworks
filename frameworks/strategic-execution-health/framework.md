# Strategic Execution Health Framework - Methodology

## Framework Overview

This framework measures organizational strategic alignment by analyzing workplace communication and project management data. It provides objective, data-driven insights into whether teams are working on strategic priorities and executing effectively.

**Core Premise**: What teams talk about and work on reveals actual strategic priorities, not just stated ones.

## The 4-Dimensional Health Model

```
┌─────────────────────────────────────┐
│         INTENT BALANCE              │
│    "Are we working on the          │
│     right things?"                 │
└──────────┬──────────────────────────┘
           │
    ┌──────┴──────┬──────────┬────────┐
    ▼             ▼          ▼        ▼
┌────────┐  ┌─────────┐  ┌──────────┐
│VELOCITY│  │FRICTION │  │COHERENCE │
│        │  │         │  │          │
│"Moving │  │"What's  │  │"Shared   │
│ fast   │  │ blocking│  │ under-   │
│ enough?"│  │ us?"    │  │ standing"│
└────────┘  └─────────┘  └──────────┘
```

---

## Dimension 1: Intent Balance

### Definition
Measures allocation of work across strategic priorities. Compares actual effort distribution to target allocation.

### What It Measures
- **Target vs. Actual**: How does work distribution match strategic intent?
- **Over/Under-Indexing**: Which priorities are getting too much or too little attention?
- **Strategic Drift**: Are we drifting away from stated strategy?

### Calculation

```python
# For each strategic intent
actual_allocation = (threads_for_intent / total_threads) * 100
target_allocation = strategic_targets[intent]
balance_gap = actual_allocation - target_allocation

# Overall alignment score
alignment_score = sum(threads_matching_strategy) / total_threads * 100
```

### Interpretation

**High Balance (>80% alignment)**:
- Work distribution closely matches strategic targets
- Teams are focused on priorities
- Strategy is being executed as intended

**Medium Balance (50-80% alignment)**:
- Some misalignment between strategy and execution
- Certain priorities may be under-served
- Requires attention to rebalance

**Low Balance (<50% alignment)**:
- Significant gap between strategy and actual work
- Teams working on non-strategic activities
- Strategic drift occurring

### Example

```
Strategic Intent: Customer Growth
Target: 25% of effort
Actual: 16.7% of effort
Gap: -8.3% (under-indexed)

Action: Reallocate resources or adjust target
```

---

## Dimension 2: Velocity

### Definition
Measures speed of execution on strategic work. Tracks how quickly teams respond to and complete strategic initiatives.

### What It Measures
- **Response Time**: Time to first response on strategic topics
- **Cycle Time**: Time from start to completion
- **Relative Speed**: Velocity compared to organizational average

### Calculation

```python
# For each conversation thread
response_time = time_to_first_reply(thread)
cycle_time = time_to_resolution(thread)

# By strategic intent
velocity_score = org_avg_velocity / intent_velocity

# Interpretation
# > 1.0 = Faster than average (good)
# < 1.0 = Slower than average (needs attention)
```

### Metrics

**Response Time**: Time until first response in thread
- Excellent: < 2 hours
- Good: 2-8 hours
- Slow: > 24 hours

**Cycle Time**: Time from start to resolution
- Excellent: < 1 week
- Good: 1-4 weeks
- Slow: > 1 month

### Interpretation

**High Velocity** (>1.2x avg):
- Strategic work moving quickly
- Team is responsive and engaged
- Clear ownership and urgency

**Medium Velocity** (0.8-1.2x avg):
- Normal pace of execution
- No major concerns

**Low Velocity** (<0.8x avg):
- Strategic work is stalling
- May indicate blockers, unclear ownership, or low priority
- Investigate root causes

---

## Dimension 3: Friction

### Definition
Identifies blockers and obstacles slowing down strategic execution. Detects and categorizes mentions of blockers in conversations.

### What It Measures
- **Blocker Frequency**: How often blockers are mentioned
- **Blocker Types**: Categories (legal, resource, decision, technical)
- **Severity**: Impact on execution

### Blocker Detection

```python
BLOCKER_KEYWORDS = [
    'blocked', 'blocker', 'stuck', 'waiting on',
    'waiting for', 'blocked by', 'can\'t proceed',
    'dependency', 'bottleneck'
]

# Detect blockers in thread
if any(keyword in thread_text.lower() for keyword in BLOCKER_KEYWORDS):
    thread['has_blocker'] = True
```

### Blocker Categories

1. **Legal/Compliance**: Legal review, regulatory approval, compliance issues
2. **Resource Constraints**: Budget, headcount, tool access
3. **Decision Pending**: Awaiting stakeholder decision, approval needed
4. **Technical**: Technical dependencies, infrastructure issues, bugs
5. **Cross-Team Dependencies**: Waiting on another team

### Calculation

```python
# Friction index per intent
friction_index = (threads_with_blockers / total_threads) * 100

# Severity scoring
severity = (
    blocker_frequency * 0.4 +
    blocker_diversity * 0.3 +
    resolution_time * 0.3
)
```

### Interpretation

**Low Friction** (<15% threads):
- Smooth execution
- Few blockers
- Clear paths forward

**Medium Friction** (15-30% threads):
- Some blockers present
- Monitor and address systematically

**High Friction** (>30% threads):
- Significant blockers impeding progress
- Requires urgent intervention
- Identify root causes

### Example

```
Intent: Customer Growth
Friction: 34% of threads mention blockers (HIGH)

Top Blockers:
1. Legal review delays (23 mentions)
2. Resource constraints (12 mentions)
3. Cross-team dependencies (8 mentions)

Action: Fast-track legal reviews, allocate resources
```

---

## Dimension 4: Coherence

### Definition
Measures organizational alignment. Assesses whether teams share a common understanding of strategic themes.

### What It Measures
- **Cross-Team Understanding**: Do teams interpret strategy consistently?
- **Sentiment Alignment**: Are teams positive/negative about strategic priorities?
- **Communication Patterns**: Are strategic topics discussed broadly or in silos?

### Calculation

```python
# Sentiment analysis on strategic topics
sentiment_score = analyze_sentiment(threads_about_intent)

# Cross-team participation
team_participation = unique_teams_discussing_intent / total_teams

# Coherence score
coherence = (
    sentiment_alignment * 0.5 +
    team_participation * 0.3 +
    message_consistency * 0.2
)
```

### Interpretation

**High Coherence** (>0.8):
- Teams aligned on strategic priorities
- Consistent understanding across org
- Positive sentiment and engagement

**Medium Coherence** (0.5-0.8):
- Some alignment, but inconsistencies exist
- May need clearer communication

**Low Coherence** (<0.5):
- Fragmented understanding of strategy
- Teams not aligned
- Requires strategic communication effort

---

## Strategic Intent Classification

### What Are Strategic Intents?

Strategic intents are your organization's top priorities (typically 4-6). Examples:

**Intent 0**: Not Strategically Aligned
- Routine operations, bug fixes, maintenance, admin work

**Intent 1**: Product Expansion
- New features, new product lines, entering new verticals

**Intent 2**: Customer Growth
- Sales, marketing, new customer segments, customer acquisition

**Intent 3**: Platform Modernization
- Unified platform, technical architecture, system consolidation

**Intent 4**: Business Model Innovation
- Pricing changes, monetization, cross-sell/upsell strategies

**Intent 5**: Technical Foundation
- Infrastructure, DevOps, technical debt, tooling improvements

### How to Define Your Intents

1. **Start with company strategy docs**: Extract 4-6 top priorities
2. **Make them mutually exclusive**: Each should cover distinct area
3. **Write clear descriptions**: Include examples for each
4. **Define target allocation**: What % of work should go to each?

### Classification Process

**Step 1: Sample Labeling (GPT-4)**
```python
# Use GPT-4 to label 500-1000 sample threads
prompt = f"""
Classify this conversation thread into one of these strategic intents:
{intent_definitions}

Thread: {thread_text}

Return only the intent number (0-5).
"""
```

**Step 2: Train Local Classifier**
```python
# Train local model on GPT-labeled data
from sklearn.linear_model import LogisticRegression
from sentence_transformers import SentenceTransformer

# Get embeddings
embeddings = model.encode(threads)

# Train classifier
classifier = LogisticRegression()
classifier.fit(embeddings, gpt_labels)
```

**Step 3: Classify All Threads**
```python
# Use local classifier for remaining threads
predictions = classifier.predict(all_thread_embeddings)
```

---

## Data Pipeline

### Phase 1: Thread Detection

**Challenge**: 100k+ Slack messages are too many to analyze individually

**Solution**: Convert messages into conversation threads

**Approach**:
- Group messages by time proximity (60-minute window)
- Use @mentions and reply chains
- Detect topic markers (keywords, projects)

**Result**: 97%+ reduction (100k messages → 2-3k threads)

```python
def detect_threads(messages, time_window=60):
    threads = []
    current_thread = []

    for msg in messages:
        if is_new_thread(msg, current_thread, time_window):
            threads.append(current_thread)
            current_thread = [msg]
        else:
            current_thread.append(msg)

    return threads
```

---

### Phase 2: Strategic Classification

**Input**: Detected threads

**Process**:
1. Use GPT-4 to label 500-1000 sample threads
2. Train local classifier on labeled data
3. Classify remaining threads with local model

**Cost Optimization**:
- GPT labels ~1000 threads: $0.50-1.00
- Local model classifies rest: $0
- **Total: <$2** vs. $50-100 for full GPT

---

### Phase 3: Metrics Calculation

**Intent Balance**:
```python
balance = {
    intent: (count / total) * 100
    for intent, count in thread_counts.items()
}
```

**Velocity**:
```python
velocity = {
    intent: calculate_response_cycle_times(threads)
    for intent in intents
}
```

**Friction**:
```python
friction = {
    intent: detect_blockers(threads)
    for intent in intents
}
```

**Coherence**:
```python
coherence = {
    intent: analyze_sentiment_alignment(threads)
    for intent in intents
}
```

---

### Phase 4: Insight Generation

**LLM-Powered Summarization**:
```python
prompt = f"""
Analyze this strategic health data and provide:
1. Key findings
2. Root cause analysis
3. Actionable recommendations

Data: {metrics_summary}
"""
```

---

## Use Cases

### Use Case 1: Quarterly Strategic Review

**Scenario**: Leadership wants to know if strategy is being executed

**Approach**:
1. Run framework on last quarter's data
2. Compare intent balance to targets
3. Present dashboard at strategy review meeting

**Outcome**:
- Data-driven conversation about priorities
- Identify misaligned teams
- Adjust resource allocation

---

### Use Case 2: Blocker Resolution

**Scenario**: Teams complain work is moving slowly

**Approach**:
1. Run friction analysis
2. Categorize top blockers
3. Prioritize resolution efforts

**Outcome**:
- Systematic blocker identification
- Clear action items
- Track resolution progress

---

### Use Case 3: Strategy Communication

**Scenario**: Leadership suspects teams don't understand strategy

**Approach**:
1. Run coherence analysis
2. Identify teams with low alignment
3. Tailor communication to gaps

**Outcome**:
- Data shows where communication is needed
- Targeted strategy sessions
- Improved alignment

---

## Customization Guide

### Defining Custom Intents

**Industry-Specific Examples**:

**Healthcare**:
- Clinical Integration
- Regulatory Compliance
- Patient Experience
- Research & Development

**Retail**:
- Omnichannel Experience
- Supply Chain Optimization
- Customer Personalization
- Store Operations

**Finance**:
- Risk Management
- Product Innovation
- Regulatory Compliance
- Customer Acquisition

### Adjusting Thresholds

Customize velocity, friction, coherence thresholds based on your org:

```yaml
thresholds:
  velocity:
    excellent: 1.2  # 1.2x avg speed
    good: 0.8
    poor: 0.5

  friction:
    low: 15%    # % of threads with blockers
    medium: 30%
    high: 50%

  coherence:
    high: 0.8
    medium: 0.5
    low: 0.3
```

---

## Limitations

### 1. Communication-Based Analysis
- Only captures documented conversations
- Offline discussions, meetings, calls not included
- May miss context from private channels

### 2. Classification Accuracy
- Depends on clear intent definitions
- Edge cases may be misclassified
- Requires periodic validation

### 3. Lag, Not Prediction
- Shows current state, not future trajectory
- Reactive rather than predictive
- Best used for course correction

### 4. Volume Dependency
- Requires sufficient conversation data
- Small teams may have limited threads
- Best for orgs with 50+ people

---

## Theoretical Foundation

This framework builds on:

1. **Strategy Execution Research**: Academic work on strategy-execution gap
2. **Organizational Communication**: Research on communication patterns and alignment
3. **Natural Language Processing**: Text classification and sentiment analysis
4. **Agile Metrics**: Cycle time, velocity concepts from agile methodologies

---

## References

- "Bridging the Strategy-Execution Gap" - Harvard Business Review
- "The Execution Premium" - Kaplan & Norton
- "Organizational Communication and Alignment" - MIT Sloan
- "Agile Metrics" - Leading Agile
