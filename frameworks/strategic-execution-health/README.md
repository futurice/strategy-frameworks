# Strategic Execution Health Framework

A data-driven framework that measures organizational strategic alignment by analyzing workplace communication and project management data. It answers the fundamental question: **"Are we working on the right things?"**

## Overview

This framework analyzes your team's communication patterns (Slack, Teams) and project data (Jira, Linear) to assess how well daily work aligns with strategic priorities. Using AI-powered analysis, it provides objective insights into execution health across four key dimensions.

**Key Question**: Are we working on the right things, and are we executing effectively?

## Key Metrics

The framework evaluates strategic health across 4 dimensions:

1. **Intent Balance**: Are we allocating effort according to strategic priorities?
2. **Velocity**: How fast are we executing on strategic work?
3. **Friction**: What blockers are slowing us down?
4. **Coherence**: Do teams share a common understanding of strategy?

### The 4-Dimensional Health Model

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

## Quick Start

### Prerequisites

- Python 3.8+
- Access to Slack/Teams data export
- Access to Jira/Linear API (optional but recommended)
- OpenAI API key (for classification and insights)

### Installation

```bash
cd frameworks/strategic-execution-health
pip install -r requirements.txt
```

### Configuration

```bash
# Create config file
cp config/sample_config.yml config/config.yml

# Set up environment variables
cp .env.example .env
# Edit .env with your API keys
```

### Basic Usage

```bash
# Step 1: Define your strategic intents
# Edit config/strategic_intents.yml

# Step 2: Export Slack data
# Download from Slack workspace settings

# Step 3: Detect conversation threads
python scripts/strategic_alignment/detect_threads.py

# Step 4: Classify threads by strategic intent
python scripts/strategic_alignment/classify_threads.py

# Step 5: Calculate metrics
python scripts/strategic_alignment/calculate_clockspeed.py
python scripts/strategic_alignment/detect_blockers.py

# Step 6: Launch dashboard
streamlit run app.py
```

## Configuration

### 1. Define Strategic Intents

Your organization's strategic priorities (typically 4-6):

```yaml
# config/strategic_intents.yml
intents:
  0:
    name: "Not Strategically Aligned"
    description: "Routine operations, bug fixes, maintenance"

  1:
    name: "Product Expansion"
    description: "New features, verticals, product lines"

  2:
    name: "Customer Growth"
    description: "Sales, marketing, new customer segments"

  3:
    name: "Platform Modernization"
    description: "Unified platform, tech modernization"

  4:
    name: "Business Model Innovation"
    description: "Pricing changes, cross-sell, upsell"

  5:
    name: "Technical Foundation"
    description: "Infrastructure, DevOps, technical debt"

# Target allocation (should sum to 100%)
targets:
  1: 25%
  2: 20%
  3: 20%
  4: 15%
  5: 20%
```

### 2. Data Sources

Configure your data sources in `config/config.yml`:

```yaml
data_sources:
  slack:
    export_path: "data/slack_export/"

  jira:
    api_url: "https://your-org.atlassian.net"
    api_token: ${JIRA_API_TOKEN}

  strategy_docs:
    path: "data/strategy/"
```

## Output

The framework generates:

### 1. Executive Dashboard
Interactive Streamlit dashboard with:
- Overall strategic alignment score
- Intent balance vs. targets
- Velocity metrics by intent
- Friction analysis (top blockers)
- Coherence scores

### 2. Reports
- **Strategic Health Report**: Executive summary with key findings
- **Intent Analysis**: Deep-dive per strategic priority
- **Blocker Report**: Categorized blockers with frequency
- **Team Coherence**: Cross-team alignment assessment

### 3. Insights
AI-generated insights including:
- Root cause analysis
- Actionable recommendations
- Priority realignment suggestions

### Example Output

```
EXECUTIVE SUMMARY
─────────────────────────────────────────
Overall Strategic Alignment: 28.1%
Intents On Track: 2 of 5
Priority Actions:
  1. [CRITICAL] Customer Growth — Blocked by legal review
  2. [WARNING] Tech Foundation — Over-indexed, reallocate

INTENT: CUSTOMER GROWTH
─────────────────────────────────────────
Balance:    Target 25% | Actual 16.7% | Gap -8.3%
Velocity:   924 min response | 28 days cycle | 0.41x avg
Friction:   34% threads mention blockers | HIGH
Top Blockers:
  1. Legal review delays (23 mentions)
  2. Resource constraints (12 mentions)
```

## Use Cases

### 1. Strategic Review Meetings
Bring data to quarterly strategy reviews showing actual vs. intended work allocation

### 2. Resource Reallocation
Identify over/under-indexed priorities and adjust team assignments

### 3. Blocker Resolution
Systematically identify and address organizational bottlenecks

### 4. Cross-Team Alignment
Assess whether teams share common understanding of strategic goals

## Methodology Highlights

### Thread Detection
- Converts 100k+ messages → 2-3k conversation threads (97%+ reduction)
- Uses time proximity, @mentions, topic markers

### AI Classification
- Teacher-student model: GPT labels sample → trains local classifier
- Reduces cost from $50-100 (full GPT) to <$2 (hybrid approach)

### Metrics
- **Intent Balance**: % of work per intent vs. targets
- **Velocity**: Response time, cycle time per intent
- **Friction**: Blocker detection and categorization
- **Coherence**: Sentiment and alignment scoring

## Documentation

- [Framework Methodology](framework.md) - Detailed 4-dimension model
- [Technical Architecture](architecture.md) - Implementation guide
- [Examples](examples/) - Sample analyses and notebooks

## Cost Estimation

- GPT classification (500-1000 threads): $0.50-1.00
- Local classifier training (embeddings): ~$0.10
- Local classification (remaining threads): $0
- **Total**: <$2 for 100k+ messages

vs. Full GPT approach: $50-100

## Limitations

- **Communication-based**: Only analyzes documented conversations, not offline discussions
- **Lag**: Shows current state, not predictive
- **Classification accuracy**: Depends on clear strategic intent definitions

## License

MIT License - see [LICENSE](../../LICENSE)
