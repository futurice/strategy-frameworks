# Getting Started

Welcome to the Organizational Frameworks repository! This guide will help you get started with using these frameworks to assess and improve your organization.

## Prerequisites

Before you begin, ensure you have:

- **Python 3.8 or higher** installed
- **Git** for version control
- **pip** for package management
- Basic familiarity with command line operations
- (Optional) **Virtual environment** tool (venv, conda, etc.)

## Installation

### 1. Clone the Repository

```bash
git clone https://github.com/your-org/frameworks.git
cd frameworks
```

### 2. Set Up a Virtual Environment (Recommended)

```bash
# Create virtual environment
python -m venv venv

# Activate it
# On macOS/Linux:
source venv/bin/activate
# On Windows:
venv\Scripts\activate
```

### 3. Choose Your Framework

Navigate to the framework you want to use:

```bash
# For AI Maturity Assessment
cd frameworks/ai-maturity-assessment

# For Strategic Execution Health
cd frameworks/strategic-execution-health
```

### 4. Install Dependencies

```bash
pip install -r requirements.txt
```

## Quick Start by Framework

### Strategic Execution Health Framework

**Best for**: Understanding if your organization is working on the right things

1. **Configure data sources**
   ```bash
   cp config/sample_config.yml config/config.yml
   # Edit config.yml with your Slack/Jira credentials
   ```

2. **Run the pipeline**
   ```bash
   python scripts/strategic_alignment/detect_threads.py
   python scripts/strategic_alignment/classify_threads.py
   python scripts/strategic_alignment/calculate_clockspeed.py
   ```

3. **View results**
   ```bash
   streamlit run app.py
   ```

### AI Maturity Assessment Framework

**Best for**: Evaluating AI readiness and capability

1. **Configure assessment parameters**
   ```bash
   cp config/sample_config.yml config/config.yml
   ```

2. **Run assessment**
   ```bash
   python scripts/run_assessment.py
   ```

3. **View reports**
   Reports will be generated in `outputs/reports/`

## Configuration

Each framework requires some configuration to match your organization:

### Environment Variables

Create a `.env` file in the framework directory:

```bash
# .env example
SLACK_API_TOKEN=xoxb-your-token
JIRA_API_KEY=your-key
OPENAI_API_KEY=sk-your-key
```

### Configuration Files

Edit `config/config.yml` to customize:
- Data source connections
- Metric thresholds
- Organizational targets
- Processing options

## Understanding the Output

### Reports

Generated reports include:
- **Executive Summary**: High-level metrics and insights
- **Detailed Analysis**: Dimension-by-dimension breakdown
- **Recommendations**: Actionable next steps

### Dashboards

Interactive dashboards provide:
- Real-time metric visualization
- Drill-down capabilities
- Historical trends
- Comparative analysis

## Common Workflows

### Workflow 1: Initial Assessment

1. Configure framework for your organization
2. Run full analysis
3. Review executive summary
4. Identify priority areas
5. Share findings with stakeholders

### Workflow 2: Ongoing Monitoring

1. Schedule regular data collection
2. Automate analysis pipeline
3. Monitor dashboard for changes
4. Alert on threshold violations
5. Track improvement over time

### Workflow 3: Custom Analysis

1. Adapt framework dimensions to your needs
2. Modify metric calculations
3. Create custom visualizations
4. Generate tailored reports

## Troubleshooting

### Common Issues

**Issue**: API authentication errors
- **Solution**: Check your API keys in `.env` file
- Ensure tokens have correct permissions

**Issue**: No data collected
- **Solution**: Verify data source configuration
- Check date ranges and filters

**Issue**: Slow processing
- **Solution**: Reduce batch size in config
- Enable caching
- Use local classifier instead of API calls

**Issue**: Missing dependencies
- **Solution**: `pip install -r requirements.txt`
- Check Python version compatibility

## Getting Help

- **Framework-specific questions**: See individual framework READMEs
- **Technical issues**: Check [GitHub Issues](https://github.com/your-org/frameworks/issues)
- **General questions**: See [FAQ](faq.md)
- **Feature requests**: Open a discussion on GitHub

## Next Steps

1. **Explore framework documentation**: Each framework has detailed `framework.md` and `architecture.md` files
2. **Review examples**: Check the `examples/` directory for sample usage
3. **Customize for your org**: Adapt metrics and thresholds
4. **Run your first analysis**: Follow the quick start above
5. **Share results**: Generate reports for your team

## Best Practices

### Data Privacy
- Never commit API keys or credentials
- Use `.env` files for sensitive data
- Review data before sharing reports

### Customization
- Start with default settings
- Make incremental changes
- Document your customizations
- Test thoroughly

### Collaboration
- Share configuration templates with your team
- Document organizational decisions
- Version control your configs
- Use consistent naming conventions

## Additional Resources

- [Contributing Guide](contributing.md)
- [Framework Comparison](../README.md#framework-comparison)
- [Creating New Frameworks](contributing.md#adding-a-new-framework)
