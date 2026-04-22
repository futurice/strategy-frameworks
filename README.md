# Organizational Frameworks

A collection of data-driven frameworks for measuring organizational effectiveness, maturity, and strategic health.

## Overview

These frameworks provide systematic approaches to assess and improve organizational capabilities using quantitative metrics, AI-powered analysis, and actionable insights. Each framework is self-contained and can be used independently or in combination with others.

## Available Frameworks

### 1. AI Maturity Assessment
Evaluates organizational AI readiness and capabilities across multiple dimensions.

- **Use when**: Assessing readiness for AI adoption or measuring AI transformation progress
- **Outputs**: Maturity scores, capability assessments, roadmap recommendations
- **[Quick Start →](frameworks/ai-maturity-assessment/README.md)**

### 2. Strategic Execution Health
Measures strategic alignment by analyzing workplace communication and project management data to answer: "Are we working on the right things?"

- **Use when**: Need visibility into strategic alignment, execution velocity, or organizational blockers
- **Outputs**: 4-dimensional health score (Intent Balance, Velocity, Friction, Coherence), executive dashboard, actionable recommendations
- **Key metrics**: Strategic alignment %, velocity scores, blocker analysis, cross-team coherence
- **[Quick Start →](frameworks/strategic-execution-health/README.md)**

## Quick Start

1. **Clone this repository**
   ```bash
   git clone https://github.com/your-org/frameworks.git
   cd frameworks
   ```

2. **Choose a framework**
   Navigate to the framework directory:
   ```bash
   cd frameworks/strategic-execution-health
   ```

3. **Follow framework-specific setup**
   Each framework has its own README with:
   - Installation instructions
   - Configuration guide
   - Usage examples
   - Customization options

4. **Customize for your organization**
   - Adapt metrics and dimensions to your context
   - Configure data sources (Slack, Jira, etc.)
   - Set organizational thresholds and targets

## Repository Structure

```
frameworks/           # Individual frameworks
├── ai-maturity-assessment/
├── strategic-execution-health/
└── [future frameworks]/

shared/              # Shared utilities across frameworks
├── data_connectors/ # Slack, Jira, Teams integrations
├── ml_utils/        # Common ML helpers
└── visualization/   # Shared visualization components

templates/           # Template for creating new frameworks
docs/               # Documentation and guides
```

## Framework Comparison

| Framework | Primary Focus | Data Sources | Output Type | Best For |
|-----------|---------------|--------------|-------------|----------|
| AI Maturity Assessment | AI capability readiness | Surveys, documents, interviews | Scores, roadmaps | Leadership planning |
| Strategic Execution Health | Strategic alignment | Slack, Jira, docs | Dashboards, metrics | Execution visibility |

## Contributing

We welcome contributions! Whether you want to:
- **Add a new framework**: See our [Framework Template](templates/framework-template/)
- **Improve existing frameworks**: Submit PRs with enhancements
- **Share use cases**: Document how you've adapted these frameworks

See [CONTRIBUTING.md](docs/contributing.md) for detailed guidelines.

## Creating a New Framework

Use our template to create a new framework:

```bash
cp -r templates/framework-template frameworks/your-framework-name
cd frameworks/your-framework-name
# Follow template instructions
```

Every framework should include:
- `README.md` - Quick overview and installation
- `framework.md` - Detailed methodology and model
- `architecture.md` - Technical implementation
- `requirements.txt` - Dependencies
- `scripts/` - Implementation code
- `examples/` - Sample usage and notebooks

## License

MIT License - see [LICENSE](LICENSE) for details.

## Support

- **Documentation**: See individual framework READMEs
- **Issues**: [GitHub Issues](https://github.com/your-org/frameworks/issues)
- **Discussions**: [GitHub Discussions](https://github.com/your-org/frameworks/discussions)

---

Built for organizations seeking data-driven insights into their strategic execution and organizational health.
