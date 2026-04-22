# AI Maturity Assessment Framework - Technical Architecture

## System Overview

This framework analyzes job posting data to assess organizational AI maturity. The system collects job postings, classifies AI-related roles, calculates maturity metrics, and generates comparative reports.

## Architecture Diagram

```
┌─────────────────────────────────────────────┐
│        Data Sources                         │
│  - Job Board APIs (LinkedIn, Indeed)        │
│  - Company Career Pages                     │
│  - Data Warehouses (Snowflake, BigQuery)    │
└──────────────────┬──────────────────────────┘
                   │
                   ▼
┌─────────────────────────────────────────────┐
│     Data Collection Layer                   │
│  - API connectors                           │
│  - Web scrapers                             │
│  - Data validation                          │
└──────────────────┬──────────────────────────┘
                   │
                   ▼
┌─────────────────────────────────────────────┐
│    AI Classification Layer                  │
│  - AI role detection                        │
│  - Seniority classification                 │
│  - Function mapping                         │
└──────────────────┬──────────────────────────┘
                   │
                   ▼
┌─────────────────────────────────────────────┐
│    Maturity Analysis Layer                  │
│  - Dimension scoring (8 dimensions)         │
│  - Overall maturity calculation             │
│  - Temporal trend analysis                  │
│  - Comparative benchmarking                 │
└──────────────────┬──────────────────────────┘
                   │
                   ▼
┌─────────────────────────────────────────────┐
│         Output Layer                        │
│  - Reports (markdown, PDF)                  │
│  - Visualizations (charts, graphs)          │
│  - Comparative tables                       │
└─────────────────────────────────────────────┘
```

## Technology Stack

### Core
- **Python**: 3.8+
- **pandas**: Data manipulation and analysis
- **numpy**: Numerical computations

### Data Collection
- **requests**: HTTP requests for APIs
- **beautifulsoup4**: Web scraping (career pages)
- **linkedin-api** (optional): LinkedIn job data
- **snowflake-connector-python** (optional): Data warehouse access

### Analysis
- **scikit-learn**: Text classification (AI role detection)
- **nltk / spaCy**: Natural language processing
- **python-dotenv**: Environment variable management

### Visualization
- **matplotlib**: Charts and graphs
- **seaborn**: Statistical visualizations
- **plotly**: Interactive dashboards

### Optional
- **openai**: LLM-powered insights and narrative generation
- **streamlit**: Interactive dashboard

## Data Flow

### Phase 1: Data Collection

**Input**: Company names, date range, data source configuration

**Process**:
```python
# Collect job postings from configured sources
jobs_data = collect_job_postings(
    companies=["Company A", "Company B"],
    date_range=("2022-01-01", "2024-12-31"),
    source="api"  # or "csv", "scraper"
)
```

**Output**: Raw job postings DataFrame with columns:
- `company`: Company name
- `title`: Job title
- `description`: Job description
- `posted_date`: Date posted
- `function`: Business function (if available)
- `location`: Job location

---

### Phase 2: AI Role Classification

**Input**: Raw job postings DataFrame

**Process**:
```python
# Classify AI-related roles
jobs_data['is_ai_role'] = jobs_data.apply(
    lambda row: classify_ai_role(row['title'], row['description']),
    axis=1
)

# Filter to AI roles only
ai_jobs = jobs_data[jobs_data['is_ai_role'] == True]

# Add seniority classification
ai_jobs['seniority'] = ai_jobs['title'].apply(classify_seniority)

# Add temporal columns
ai_jobs['year'] = pd.to_datetime(ai_jobs['posted_date']).dt.year
ai_jobs['quarter'] = pd.to_datetime(ai_jobs['posted_date']).dt.quarter
```

**AI Role Detection Logic**:
```python
AI_KEYWORDS = [
    'machine learning', 'ml engineer', 'data scientist',
    'artificial intelligence', 'ai', 'deep learning',
    'nlp', 'natural language processing', 'computer vision',
    'mlops', 'ml platform', 'research scientist'
]

def classify_ai_role(title, description):
    text = f"{title} {description}".lower()
    return any(keyword in text for keyword in AI_KEYWORDS)
```

**Seniority Classification**:
```python
SENIORITY_KEYWORDS = {
    'Leadership': ['chief', 'vp', 'vice president', 'head of', 'director'],
    'Senior': ['senior', 'lead', 'principal', 'staff', 'architect'],
    'Mid-Level': ['engineer', 'scientist', 'analyst', 'manager'],
    'Junior': ['junior', 'associate', 'entry']
}

def classify_seniority(title):
    title_lower = title.lower()
    for level, keywords in SENIORITY_KEYWORDS.items():
        if any(kw in title_lower for kw in keywords):
            return level
    return 'Mid-Level'  # Default
```

**Output**: Classified AI jobs DataFrame

---

### Phase 3: Dimension Scoring

**Input**: Classified AI jobs DataFrame

**Process**: Calculate each of the 8 dimensions

```python
def calculate_maturity_dimensions(ai_jobs_df):
    dimensions = {}

    # Dimension 1: Technical Depth
    specialized_keywords = ['nlp', 'computer vision', 'reinforcement learning',
                           'research scientist']
    specialized_count = ai_jobs_df['title'].str.lower().str.contains(
        '|'.join(specialized_keywords)
    ).sum()
    dimensions['technical_depth'] = score_dimension(
        specialized_count / len(ai_jobs_df)
    )

    # Dimension 2: Technical Width
    unique_functions = ai_jobs_df['function'].nunique()
    dimensions['technical_width'] = min(5, unique_functions)

    # Dimension 3: Business Integration
    business_keywords = ['product', 'strategy', 'operations', 'business']
    business_count = ai_jobs_df['title'].str.lower().str.contains(
        '|'.join(business_keywords)
    ).sum()
    dimensions['business_integration'] = score_dimension(
        business_count / len(ai_jobs_df)
    )

    # Dimension 4: Governance & Leadership
    leadership_count = (
        ai_jobs_df['seniority'] == 'Leadership'
    ).sum()
    dimensions['governance'] = score_dimension(
        leadership_count / len(ai_jobs_df)
    )

    # Dimension 5: Platformisation
    platform_keywords = ['mlops', 'ml platform', 'ml infrastructure']
    platform_count = ai_jobs_df['title'].str.lower().str.contains(
        '|'.join(platform_keywords)
    ).sum()
    dimensions['platformisation'] = score_dimension(
        platform_count / len(ai_jobs_df)
    )

    # Dimension 6: Seniority Mix
    senior_count = ai_jobs_df['seniority'].isin(['Senior', 'Leadership']).sum()
    dimensions['seniority_mix'] = score_dimension(
        senior_count / len(ai_jobs_df)
    )

    # Dimension 7: Ecosystem
    ecosystem_keywords = ['partnership', 'alliance', 'ecosystem', 'developer relations']
    ecosystem_count = ai_jobs_df['title'].str.lower().str.contains(
        '|'.join(ecosystem_keywords)
    ).sum()
    dimensions['ecosystem'] = score_dimension(
        ecosystem_count / len(ai_jobs_df)
    )

    # Dimension 8: Regulatory & Ethics
    ethics_keywords = ['ethics', 'responsible ai', 'governance', 'compliance']
    ethics_count = ai_jobs_df['title'].str.lower().str.contains(
        '|'.join(ethics_keywords)
    ).sum()
    dimensions['regulatory'] = score_dimension(
        ethics_count / len(ai_jobs_df)
    )

    return dimensions

def score_dimension(ratio):
    """Convert ratio to 1-5 score"""
    if ratio == 0:
        return 1
    elif ratio < 0.1:
        return 2
    elif ratio < 0.25:
        return 3
    elif ratio < 0.5:
        return 4
    else:
        return 5
```

**Output**: Dictionary of dimension scores (1-5 for each dimension)

---

### Phase 4: Overall Maturity Calculation

**Input**: Dimension scores, job count, year-over-year data

**Process**:
```python
def calculate_overall_maturity(dimensions, total_jobs, yoy_growth=0):
    # Base maturity = average of dimensions
    base_maturity = sum(dimensions.values()) / len(dimensions)

    # Scale adjustments
    if total_jobs >= 300:
        scale_boost = 1.0
    elif total_jobs >= 150:
        scale_boost = 0.5
    elif total_jobs < 50:
        scale_boost = -0.5
    else:
        scale_boost = 0

    # Growth boost
    growth_boost = 0.5 if yoy_growth >= 2.0 else 0

    # Calculate final maturity
    overall = base_maturity + scale_boost + growth_boost
    overall = min(5, max(1, overall))

    # Round to nearest 0.5
    return round(overall * 2) / 2
```

**Output**: Overall maturity level (1.0 to 5.0, in 0.5 increments)

---

### Phase 5: Report Generation

**Input**: Maturity scores, dimension breakdowns, job data

**Process**:
```python
def generate_maturity_report(company_name, yearly_data):
    report = f"# {company_name} - AI Maturity Assessment\n\n"

    for year, data in yearly_data.items():
        maturity = data['overall_maturity']
        job_count = data['job_count']
        dimensions = data['dimensions']

        report += f"## Year {year}: Level {maturity}\n"
        report += f"**Total AI Roles**: {job_count}\n\n"
        report += "**Dimension Breakdown**:\n"
        for dim, score in dimensions.items():
            report += f"- {dim}: {score}/5\n"
        report += "\n"

    return report
```

**Output**: Markdown report, visualizations, comparative tables

---

## File Structure

```
ai-maturity-assessment/
├── scripts/
│   ├── collect_job_data.py       # Data collection
│   ├── classify_ai_roles.py      # AI role classification
│   ├── calculate_maturity.py     # Maturity scoring
│   ├── generate_report.py        # Report generation
│   └── utils/
│       ├── data_processing.py    # Helper functions
│       ├── classification.py     # Classification logic
│       └── visualization.py      # Chart generation
├── config/
│   ├── config.yml                # Main configuration
│   └── sample_config.yml         # Example config
├── data/                         # Job posting data (gitignored)
│   ├── raw/
│   └── processed/
├── outputs/                      # Generated reports (gitignored)
│   ├── reports/
│   └── visualizations/
└── examples/
    └── analysis_example.ipynb    # Jupyter notebook example
```

## Key Components

### 1. Data Collectors (`scripts/collect_job_data.py`)

**Purpose**: Fetch job postings from various sources

**Key Functions**:
- `collect_from_api()`: Fetch from job board APIs
- `scrape_career_page()`: Scrape company career pages
- `load_from_warehouse()`: Query data warehouse

### 2. AI Classifier (`scripts/classify_ai_roles.py`)

**Purpose**: Identify AI-related roles and classify attributes

**Key Functions**:
- `classify_ai_role()`: Determine if role is AI-related
- `classify_seniority()`: Categorize seniority level
- `map_function()`: Map to business function

### 3. Maturity Calculator (`scripts/calculate_maturity.py`)

**Purpose**: Calculate dimension scores and overall maturity

**Key Functions**:
- `calculate_dimension_scores()`: Score 8 dimensions
- `calculate_overall_maturity()`: Compute final maturity level
- `calculate_yoy_growth()`: Year-over-year growth analysis

### 4. Report Generator (`scripts/generate_report.py`)

**Purpose**: Generate reports and visualizations

**Key Functions**:
- `generate_maturity_report()`: Create markdown report
- `create_dimension_chart()`: Visualize dimension scores
- `create_comparison_table()`: Compare multiple companies

## Configuration

### Required Settings

```yaml
# config/config.yml
data_sources:
  type: "api"  # or "csv", "scraper", "warehouse"

  api:
    provider: "linkedin"  # or "indeed", "glassdoor"
    api_key: ${JOBS_API_KEY}  # From .env

  csv:
    path: "data/raw/job_postings.csv"

  warehouse:
    type: "snowflake"
    account: ${SNOWFLAKE_ACCOUNT}
    database: "JOB_DATA"
    schema: "PUBLIC"

companies:
  - "Company A"
  - "Company B"
  - "Company C"

date_range:
  start: "2022-01-01"
  end: "2024-12-31"

analysis:
  dimensions:
    technical_depth: 1.0      # Dimension weights
    technical_width: 1.0
    business_integration: 1.0
    governance: 1.0
    platformisation: 1.0
    seniority_mix: 1.0
    ecosystem: 1.0
    regulatory: 1.0

  ai_keywords:  # Customizable AI detection keywords
    - "machine learning"
    - "artificial intelligence"
    - "data scientist"
    # Add more...

output:
  format: "markdown"  # or "pdf", "html"
  include_visualizations: true
```

## Performance Considerations

### Data Volume
- **Expected**: 100-1000 job postings per company per year
- **Processing time**: 1-5 minutes for typical analysis
- **Memory**: <1GB RAM for most analyses

### API Rate Limits
- Implement exponential backoff for API calls
- Cache results to minimize repeated requests
- Respect rate limits (typically 100-1000 requests/hour)

### Optimization Strategies
- **Batch processing**: Process multiple companies in parallel
- **Caching**: Store intermediate results
- **Incremental updates**: Only fetch new job postings

## Cost Optimization

### API Usage
- LinkedIn API: ~$0.01-0.05 per job posting
- Indeed API: Free tier available, paid for high volume
- Web scraping: Free but requires maintenance

### LLM Usage (Optional)
- Use local classification where possible
- Reserve LLM calls for narrative generation only
- Estimated cost: <$1 per company analysis

## Security & Privacy

### API Keys
Store in `.env` file (gitignored):
```
JOBS_API_KEY=your_api_key_here
OPENAI_API_KEY=your_openai_key
SNOWFLAKE_ACCOUNT=your_account
```

### Data Privacy
- Job postings are public data
- No PII collection
- Aggregate analysis only (no individual candidate data)

## Error Handling

### Common Issues
1. **API failures**: Retry with exponential backoff
2. **Invalid data**: Skip malformed records, log for review
3. **Missing fields**: Use defaults, flag incomplete data
4. **Classification errors**: Manual review of edge cases

## Testing

### Unit Tests
```python
# tests/test_classification.py
def test_ai_role_detection():
    assert classify_ai_role("ML Engineer", "") == True
    assert classify_ai_role("Accountant", "") == False

def test_seniority_classification():
    assert classify_seniority("Senior Data Scientist") == "Senior"
    assert classify_seniority("Data Scientist") == "Mid-Level"
```

### Integration Tests
- End-to-end pipeline test with sample data
- Validate dimension scores against known benchmarks
- Test report generation

## Deployment

### Local Deployment
```bash
# Setup
pip install -r requirements.txt
cp config/sample_config.yml config/config.yml

# Run analysis
python scripts/collect_job_data.py
python scripts/classify_ai_roles.py
python scripts/calculate_maturity.py
python scripts/generate_report.py
```

### Scheduled Analysis (Optional)
```bash
# cron job for monthly analysis
0 0 1 * * cd /path/to/framework && python scripts/run_full_analysis.py
```

## Future Enhancements

1. **Real-time dashboard**: Streamlit app for live monitoring
2. **Predictive analytics**: Forecast maturity trajectory
3. **Skill extraction**: Parse job descriptions for required skills
4. **Salary analysis**: Correlate maturity with compensation trends
5. **Industry benchmarks**: Compare across industries
