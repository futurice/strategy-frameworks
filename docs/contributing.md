# Contributing Guide

Thank you for your interest in contributing to the Organizational Frameworks repository! This guide will help you add new frameworks, improve existing ones, or contribute in other ways.

## Ways to Contribute

1. **Add a new framework** - Share your organizational assessment methodology
2. **Improve existing frameworks** - Enhance features, fix bugs, or optimize performance
3. **Share use cases** - Document how you've adapted and used these frameworks
4. **Improve documentation** - Help others understand and use frameworks better
5. **Report issues** - Help identify bugs or suggest improvements

## Adding a New Framework

### Step 1: Use the Template

```bash
# Copy the template
cp -r templates/framework-template frameworks/your-framework-name
cd frameworks/your-framework-name
```

### Step 2: Define Your Framework

Fill out the template files:

#### README.md
- Clear overview and value proposition
- Quick start instructions
- Configuration guide
- Example outputs

#### framework.md
- Detailed methodology
- Core model and dimensions
- Scoring algorithms
- Use cases
- Customization guide

#### architecture.md
- Technical implementation details
- Data flow diagrams
- Technology stack
- API integrations
- Performance considerations

### Step 3: Implement Your Framework

Required components:

```
your-framework-name/
├── README.md              # User-facing documentation
├── framework.md           # Methodology documentation
├── architecture.md        # Technical documentation
├── requirements.txt       # Python dependencies
├── config/
│   └── sample_config.yml # Example configuration
├── scripts/              # Implementation code
│   ├── data_collection.py
│   ├── processing.py
│   └── analysis.py
├── examples/             # Usage examples
│   └── quickstart.ipynb
└── data/                 # Placeholder for user data
    └── .gitkeep
```

### Step 4: Follow Framework Standards

#### Code Quality
- Follow PEP 8 style guide for Python
- Include docstrings for all functions
- Add type hints where appropriate
- Write clear, self-documenting code

#### Configuration
- Use YAML for configuration files
- Provide comprehensive sample configs
- Document all configuration options
- Never hardcode credentials

#### Error Handling
- Include meaningful error messages
- Validate inputs and configurations
- Fail gracefully with clear guidance
- Log errors appropriately

#### Documentation
- Write clear, concise documentation
- Include code examples
- Provide troubleshooting guidance
- Document all assumptions

### Step 5: Add Examples

Create practical examples:

```python
# examples/quickstart.ipynb
"""
Quick Start Example for [Framework Name]

This notebook demonstrates:
1. Basic configuration
2. Data collection
3. Running analysis
4. Interpreting results
"""
```

### Step 6: Update Main README

Add your framework to the main repository README:

```markdown
### N. [Your Framework Name]
Brief description of what it measures.

- **Use when**: [Scenario]
- **Outputs**: [What it produces]
- **Key metrics**: [Primary metrics]
- **[Quick Start →](frameworks/your-framework-name/README.md)**
```

### Step 7: Test Your Framework

Create a test suite:

```python
# tests/test_your_framework.py
import pytest

def test_data_collection():
    """Test data collection functionality"""
    pass

def test_metric_calculation():
    """Test metric calculations"""
    pass

def test_output_generation():
    """Test report generation"""
    pass
```

Run tests:
```bash
pytest tests/
```

### Step 8: Submit a Pull Request

1. Create a new branch
   ```bash
   git checkout -b feature/your-framework-name
   ```

2. Commit your changes
   ```bash
   git add .
   git commit -m "Add [Framework Name] framework"
   ```

3. Push to GitHub
   ```bash
   git push origin feature/your-framework-name
   ```

4. Open a Pull Request with:
   - Clear description of the framework
   - Use cases and benefits
   - Screenshots or example outputs
   - Testing evidence

## Framework Quality Checklist

Before submitting, ensure your framework meets these criteria:

### Documentation
- [ ] README.md with clear quick start
- [ ] framework.md with detailed methodology
- [ ] architecture.md with technical details
- [ ] All configuration options documented
- [ ] Examples provided

### Code Quality
- [ ] Follows PEP 8 style guidelines
- [ ] Includes docstrings
- [ ] Has error handling
- [ ] No hardcoded credentials
- [ ] Uses configuration files

### Functionality
- [ ] Runs without errors
- [ ] Produces expected outputs
- [ ] Handles edge cases
- [ ] Validates inputs
- [ ] Includes logging

### Security & Privacy
- [ ] No credentials in code
- [ ] Sensitive data properly handled
- [ ] .gitignore configured correctly
- [ ] API keys use environment variables

### Reusability
- [ ] Configurable for different organizations
- [ ] Customizable dimensions/metrics
- [ ] Clear extension points
- [ ] Modular architecture

## Improving Existing Frameworks

### Bug Fixes

1. Identify the issue
2. Create an issue on GitHub
3. Create a branch: `bugfix/issue-description`
4. Fix and test thoroughly
5. Submit PR with reference to issue

### Feature Enhancements

1. Discuss the enhancement in GitHub Issues first
2. Create a branch: `feature/feature-name`
3. Implement and document
4. Add tests
5. Submit PR with clear description

### Documentation Improvements

Documentation improvements are always welcome!

- Fix typos or unclear explanations
- Add more examples
- Improve troubleshooting guides
- Add visual diagrams

## Code Style Guidelines

### Python Code

```python
# Good example
def calculate_metric(
    data: pd.DataFrame,
    threshold: float = 0.5
) -> float:
    """
    Calculate the metric score based on data.

    Args:
        data: Input dataframe with required columns
        threshold: Minimum threshold for scoring

    Returns:
        Calculated metric score between 0 and 1

    Raises:
        ValueError: If data is empty or missing required columns
    """
    if data.empty:
        raise ValueError("Input data cannot be empty")

    # Implementation
    score = data['value'].mean()
    return max(0.0, min(1.0, score))
```

### Configuration Files

```yaml
# Good example - clear, well-commented YAML
metrics:
  # Strategic alignment score (0-1)
  alignment_score:
    name: "Strategic Alignment"
    weight: 0.3  # 30% of overall score
    threshold_excellent: 0.8
    threshold_good: 0.6
    threshold_poor: 0.4
```

### Documentation

Use clear, actionable language:

```markdown
<!-- Good -->
## Quick Start

1. Install dependencies:
   ```bash
   pip install -r requirements.txt
   ```

2. Configure your API key in `.env`:
   ```
   API_KEY=your-key-here
   ```

3. Run the analysis:
   ```bash
   python scripts/run_analysis.py
   ```
```

## Sharing Use Cases

We love hearing how organizations use these frameworks!

### Write a Case Study

Create a file in `docs/case-studies/`:

```markdown
# [Organization Name] - [Framework Name] Implementation

## Context
[Describe your organization and challenge]

## Implementation
[How you adapted the framework]

## Results
[What insights you gained]

## Lessons Learned
[What worked well, what didn't]
```

## Community Guidelines

- **Be respectful**: Treat all contributors with respect
- **Be constructive**: Provide actionable feedback
- **Be collaborative**: Work together to improve frameworks
- **Be patient**: Remember that contributors volunteer their time

## Getting Help

- **Questions about contributing**: Open a discussion on GitHub
- **Technical issues**: Create an issue
- **General questions**: Check existing documentation first

## Recognition

Contributors will be recognized in:
- Repository contributors list
- Release notes
- Framework documentation (where applicable)

## License

By contributing, you agree that your contributions will be licensed under the MIT License.

---

Thank you for contributing to the Organizational Frameworks repository! Your contributions help organizations worldwide make better data-driven decisions.
