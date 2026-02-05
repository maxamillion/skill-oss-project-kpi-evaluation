# OSS KPI Evaluation Skill

A Claude Code skill that evaluates open source projects against Key Performance Indicators using multi-agent research. Performs live web research to gather current metrics on community health, maintenance, security, documentation, adoption, and code quality.

## Features

- **Comprehensive Evaluation**: Analyzes 30 metrics across 6 KPI categories
- **Multi-Agent Research**: Parallel subagents gather data independently to prevent bias
- **Evidence-Based**: Every metric includes source URLs and timestamps
- **Bias Prevention**: Strict protocols ensure objective, verifiable assessments
- **Customizable**: Adjust category weights or focus on specific areas

### KPI Categories

| Category | Weight | What It Measures |
|----------|--------|------------------|
| Community Health | 16.67% | Contributor count, growth rate, bus factor, engagement |
| Maintenance | 16.67% | Commit frequency, release cadence, issue/PR resolution time |
| Security | 16.67% | Security policy, vulnerability disclosure, dependency status |
| Documentation | 16.67% | README, API docs, guides, changelog, contributing guide |
| Adoption | 16.67% | Stars, forks, downloads, dependent projects |
| Code Quality | 16.67% | Tests, coverage, CI/CD, code review practices |

## Installation

### Using Claude Code Plugin Marketplace

Install directly from the Claude Code CLI:

```bash
claude /plugin add admiller/skill-oss-project-kpi-evaluation
```

### From GitHub URL

Install directly from the GitHub repository:

```bash
claude /plugin add https://github.com/admiller/skill-oss-project-kpi-evaluation
```

### Manual Installation

1. Clone the repository:
   ```bash
   git clone https://github.com/admiller/skill-oss-project-kpi-evaluation.git
   ```

2. Add the skill to your Claude Code configuration:
   ```bash
   claude /plugin add ./skill-oss-project-kpi-evaluation
   ```

## Requirements

- **Claude Code CLI**: Latest version
- **GitHub CLI (`gh`)**: Authenticated for API access
- **Internet Access**: Required for live web research

## Usage

### Basic Evaluation

```
Evaluate the project: https://github.com/fastapi/fastapi
```

### Focused Evaluation

Evaluate specific categories:

```
Evaluate https://github.com/psf/requests focusing on security and maintenance
```

### Custom Weights

Adjust category importance:

```
Evaluate https://github.com/owner/repo with weights:
- Security: 30%
- Maintenance: 25%
- Community: 15%
- Adoption: 15%
- Documentation: 10%
- Code Quality: 5%
```

### Minimum Score Threshold

Set a pass/fail threshold:

```
Evaluate https://github.com/owner/repo with minimum score 4.0
```

## Output

The skill generates a structured report containing:

1. **Executive Summary**: Overall score (1-5), key strengths and concerns
2. **Category Details**: Per-category scores with evidence tables
3. **Cross-Validation Results**: Discrepancy reports between data sources
4. **Data Quality Assessment**: Missing or stale data warnings
5. **Sources**: Complete list of URLs with retrieval timestamps

### Score Interpretation

| Score | Rating | Meaning |
|-------|--------|---------|
| 4.5-5.0 | Excellent | Low risk, high quality project |
| 3.5-4.4 | Good | Healthy project, minor improvements needed |
| 2.5-3.4 | Adequate | Acceptable but review carefully |
| 1.5-2.4 | Concerning | Significant risks, proceed with caution |
| 1.0-1.4 | Critical | Major issues, high risk adoption |

## Use Cases

- **Adoption Decisions**: Should we use this OSS project?
- **Investment Analysis**: Is this project healthy enough to fund?
- **Project Comparison**: Which library should we choose?
- **Due Diligence**: What are the risks of this dependency?
- **Risk Assessment**: Are there maintenance, security, or community risks?

## Limitations

- **GitHub Only**: Projects must be hosted on GitHub
- **Single Project**: Evaluates one project per invocation
- **English Language**: Optimized for English-language projects

## Documentation

Detailed documentation is available in the `oss-kpi-evaluation/references/` directory:

| Document | Description |
|----------|-------------|
| `KPI-FRAMEWORK.md` | Complete metric definitions and scoring rubrics |
| `DATA-SOURCES.md` | Data collection methods and APIs |
| `RESEARCH-PROMPTS.md` | Subagent prompt templates |
| `BIAS-PREVENTION.md` | Quality control protocols |
| `REPORT-TEMPLATE.md` | Output format specification |

## License

MIT License - see [LICENSE](LICENSE) for details.

## Author

Adam Miller ([@admiller](https://github.com/admiller))
