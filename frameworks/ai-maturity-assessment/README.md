# AI Maturity Assessment Framework

A data-driven framework for evaluating organizational AI capability maturity through job posting analysis, using a 5-level model with 8 evaluation dimensions.

## Overview

This framework analyzes hiring patterns to assess how organizations develop AI capabilities over time. By examining job titles, seniority levels, and functional distribution of AI roles, it provides objective insights into AI maturity progression.

**Key Question**: How mature is an organization's AI capability based on their talent acquisition patterns?

## Key Metrics

The framework evaluates AI maturity across 8 dimensions:

1. **Technical Depth**: Sophistication of AI/ML roles (e.g., specialized vs. generic)
2. **Technical Width**: Spread of AI talent across business functions
3. **Business Integration**: AI embedded in product, operations, and strategy roles
4. **Governance & Leadership**: Presence of senior leaders and governance structures
5. **Platformisation**: Platform, infrastructure, and MLOps capabilities
6. **Seniority Mix**: Balance of junior vs. senior talent
7. **Ecosystem**: Partnerships, external collaboration, open innovation roles
8. **Regulatory & Ethics**: Ethics, compliance, and responsible AI focus

### Maturity Levels

- **Level 1**: Ad-hoc / Experimentation
- **Level 2**: Foundational / Emerging
- **Level 3**: Scaling / Functional Integration
- **Level 4**: Enterprise Integration
- **Level 5**: AI-Native / Transformational

## Quick Start

### Prerequisites

- Python 3.8+
- Access to job posting data (e.g., company career pages, job boards, APIs)

### Installation

```bash
cd frameworks/ai-maturity-assessment
pip install -r requirements.txt
```

### Configuration

```bash
cp config/sample_config.yml config/config.yml
# Edit config/config.yml with your data sources
```

### Basic Usage

```bash
# 1. Collect job posting data
python scripts/collect_job_data.py

# 2. Classify AI-related roles
python scripts/classify_ai_roles.py

# 3. Calculate maturity scores
python scripts/calculate_maturity.py

# 4. Generate report
python scripts/generate_report.py
```

## Configuration

### Data Sources

The framework supports multiple data sources:

- **Job Board APIs**: LinkedIn, Indeed, Glassdoor
- **Company Career Pages**: Direct scraping or API access
- **Data Warehouses**: Snowflake, BigQuery (if you have existing job data)

Configure in `config/config.yml`:

```yaml
data_sources:
  type: "api"  # or "csv", "database"
  source: "linkedin"
  companies:
    - "Company A"
    - "Company B"
  date_range:
    start: "2022-01-01"
    end: "2024-12-31"
```

### Customization

Adapt the framework:

1. **Adjust dimension weights**: Edit `config/config.yml` to emphasize different dimensions
2. **Customize keywords**: Modify AI role detection keywords for your industry
3. **Set thresholds**: Calibrate maturity level thresholds based on your benchmarks

## Output

The framework generates:

- **Maturity Progression Report**: Year-over-year maturity evolution
- **Dimension Scores**: Breakdown across 8 dimensions
- **Comparative Analysis**: Side-by-side company benchmarking
- **Visualizations**: Charts showing hiring trends and maturity gaps

### Example Output

```
Company A - AI Maturity Assessment (2022-2024)

Year 2022: Level 2 - Foundational
  15 AI roles | Focus: Engineering, IT
  Key signal: Basic ML roles, no leadership

Year 2023: Level 3 - Scaling
  45 AI roles | Focus: Engineering, Product, IT
  Key signal: First manager positions, cross-functional expansion

Year 2024: Level 3.5 - Scaling & Integration
  90 AI roles | Focus: 4 business functions
  Key signal: Director-level leadership, MLOps team emerging

Dimension Breakdown (2024):
  Technical Depth:        3/5
  Technical Width:        4/5
  Business Integration:   3/5
  Governance:             3/5
  Platformisation:        2/5
  Seniority Mix:          3/5
  Ecosystem:              1/5
  Regulatory & Ethics:    2/5
```

## Use Cases

### 1. Competitive Intelligence
Track competitor AI hiring to benchmark your organization's AI maturity

### 2. Strategic Planning
Identify gaps in AI capability development and inform hiring strategy

### 3. M&A Due Diligence
Assess AI maturity of acquisition targets through public hiring data

### 4. Talent Market Analysis
Understand AI talent demand trends across industries

## Documentation

- [Framework Methodology](framework.md) - Detailed maturity model and dimensions
- [Technical Architecture](architecture.md) - Implementation details
- [Examples](examples/) - Sample analyses and notebooks

## Cost Estimation

- Job data collection: $0-$50/month (depending on API usage)
- Processing: Minimal compute costs
- Total: <$100/month for ongoing monitoring

## Limitations

- **Hiring lag**: Job postings may lag actual capability by 3-6 months
- **Public data only**: Cannot assess internal projects
- **Volume bias**: Larger companies naturally score higher on some dimensions

## License

MIT License - see [LICENSE](../../LICENSE)
