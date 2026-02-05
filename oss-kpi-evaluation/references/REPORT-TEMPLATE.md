# Report Template

This document defines the output format for OSS KPI evaluations.

## Report Structure

```
# OSS KPI Evaluation Report: {owner}/{repo}

## Executive Summary
## Category Scores
## Detailed Findings
  ### Community Health
  ### Maintenance
  ### Security
  ### Documentation
  ### Adoption
  ### Code Quality
## Cross-Validation Results
## Data Quality Assessment
## Methodology Notes
## Sources
```

---

## Section Specifications

### 1. Executive Summary

**Purpose**: Quick overview for decision-makers.

**Format**:
```markdown
## Executive Summary

**Repository**: {owner}/{repo}
**Evaluation Date**: {YYYY-MM-DD}
**Overall Score**: {X.X}/5.0 ({rating})

### Score Summary

| Category | Score | Rating |
|----------|-------|--------|
| Community Health | X.X | {rating} |
| Maintenance | X.X | {rating} |
| Security | X.X | {rating} |
| Documentation | X.X | {rating} |
| Adoption | X.X | {rating} |
| Code Quality | X.X | {rating} |

### Key Strengths
1. {Metric}: {value} - {factual observation}
2. {Metric}: {value} - {factual observation}
3. {Metric}: {value} - {factual observation}

### Key Concerns
1. {Metric}: {value} - {factual observation}
2. {Metric}: {value} - {factual observation}
3. {Metric}: {value} - {factual observation}

### Data Completeness
- Metrics collected: {X}/{Y}
- Categories with missing data: {list or "None"}
```

**Rating Scale**:
| Score Range | Rating |
|-------------|--------|
| 4.5-5.0 | Excellent |
| 3.5-4.4 | Good |
| 2.5-3.4 | Adequate |
| 1.5-2.4 | Concerning |
| 1.0-1.4 | Critical |

---

### 2. Category Scores

**Purpose**: Visual overview of all category scores.

**Format**:
```markdown
## Category Scores

| Category | Score | Metrics Available | Weight |
|----------|-------|-------------------|--------|
| Community Health | X.X/5.0 | X/5 | 16.67% |
| Maintenance | X.X/5.0 | X/5 | 16.67% |
| Security | X.X/5.0 | X/5 | 16.67% |
| Documentation | X.X/5.0 | X/5 | 16.67% |
| Adoption | X.X/5.0 | X/5 | 16.67% |
| Code Quality | X.X/5.0 | X/5 | 16.67% |
| **Overall** | **X.X/5.0** | **X/30** | **100%** |
```

---

### 3. Detailed Findings

Each category follows the same format.

**Format**:
```markdown
### {Category Name}

**Category Score**: X.X/5.0 ({rating})

#### Metrics

| Metric | Value | Score | Threshold Met |
|--------|-------|-------|---------------|
| {metric_name} | {value} | X/5 | {threshold description} |
| {metric_name} | {value} | X/5 | {threshold description} |
| ... | ... | ... | ... |

#### Evidence Table

| Metric | Source | Retrieved |
|--------|--------|-----------|
| {metric_name} | [{source_type}]({url}) | {timestamp} |
| {metric_name} | [{source_type}]({url}) | {timestamp} |
| ... | ... | ... |

#### Observations

- {Factual observation about metric 1}
- {Factual observation about metric 2}
- {Note about any NOT_AVAILABLE metrics}
```

**Example - Community Health**:
```markdown
### Community Health

**Category Score**: 4.2/5.0 (Good)

#### Metrics

| Metric | Value | Score | Threshold Met |
|--------|-------|-------|---------------|
| Contributor Count | 127 | 5/5 | ≥100 contributors |
| Contributor Growth | +18% | 4/5 | 10-24% growth |
| Bus Factor | 4 | 4/5 | 4 people |
| Issue Engagement | 78% | 4/5 | 75-89% response rate |
| PR Engagement | 82% | 4/5 | 75-89% review rate |

#### Evidence Table

| Metric | Source | Retrieved |
|--------|--------|-----------|
| Contributor Count | [GitHub API](https://api.github.com/repos/owner/repo/contributors) | 2024-01-15T10:30:00Z |
| Contributor Growth | [GitHub Insights](https://github.com/owner/repo/graphs/contributors) | 2024-01-15T10:31:00Z |
| Bus Factor | [GitHub API](https://api.github.com/repos/owner/repo/stats/contributors) | 2024-01-15T10:30:00Z |
| Issue Engagement | [gh issue list](gh issue list --state closed --limit 50) | 2024-01-15T10:32:00Z |
| PR Engagement | [gh pr list](gh pr list --state merged --limit 50) | 2024-01-15T10:33:00Z |

#### Observations

- 127 unique contributors active in the past 12 months
- Top 4 contributors account for 50% of commits (bus factor = 4)
- 78% of issues receive maintainer response within 7 days (sample: 50 issues)
- 82% of PRs receive review within 7 days (sample: 50 PRs)
```

---

### 4. Cross-Validation Results

**Purpose**: Document verification of overlapping metrics and discrepancies.

**Format**:
```markdown
## Cross-Validation Results

### Verified Metrics

| Metric | Primary Source | Secondary Source | Match |
|--------|----------------|------------------|-------|
| Star Count | GitHub API | star-history.com | ✓ |
| Contributor Count | GitHub API | GitHub Insights | ✓ |
| ... | ... | ... | ... |

### Discrepancies

| Metric | Source 1 | Value 1 | Source 2 | Value 2 | Variance |
|--------|----------|---------|----------|---------|----------|
| {metric} | {source} | {value} | {source} | {value} | {%} |

### Resolution Notes

- {Discrepancy 1}: {Explanation of which source was used and why}
- {Discrepancy 2}: {Explanation}
```

If no discrepancies: "No discrepancies exceeding 10% variance were detected."

---

### 5. Data Quality Assessment

**Purpose**: Transparency about data completeness and reliability.

**Format**:
```markdown
## Data Quality Assessment

### Collection Summary

- **Total Metrics Defined**: 30
- **Metrics Successfully Collected**: {X}
- **Metrics Unavailable**: {Y}
- **Collection Timestamp**: {ISO-8601}

### Missing Data

| Metric | Category | Reason |
|--------|----------|--------|
| {metric} | {category} | {reason for NOT_AVAILABLE} |
| ... | ... | ... |

### Stale Data Warnings

| Metric | Source Age | Concern |
|--------|------------|---------|
| {metric} | {age} | {description} |

### Source Accessibility Issues

| Source | Issue | Impact |
|--------|-------|--------|
| {url} | {error} | {which metrics affected} |

### Confidence Assessment

| Category | Data Quality | Confidence |
|----------|--------------|------------|
| Community Health | {X}/5 metrics | {High/Medium/Low} |
| Maintenance | {X}/5 metrics | {High/Medium/Low} |
| Security | {X}/5 metrics | {High/Medium/Low} |
| Documentation | {X}/5 metrics | {High/Medium/Low} |
| Adoption | {X}/5 metrics | {High/Medium/Low} |
| Code Quality | {X}/5 metrics | {High/Medium/Low} |
```

**Confidence Levels**:
- **High**: All 5 metrics collected with verified sources
- **Medium**: 3-4 metrics collected, remaining unavailable with documented reasons
- **Low**: <3 metrics collected, limited data for category scoring

---

### 6. Methodology Notes

**Purpose**: Explain how the evaluation was conducted.

**Format**:
```markdown
## Methodology Notes

### Evaluation Approach

This evaluation used the OSS KPI Evaluation skill v1.0 with the following methodology:

1. **Data Collection**: Six independent research agents collected metrics in parallel
2. **Scoring**: Mechanical scoring using predefined rubrics
3. **Validation**: Cross-validation of overlapping metrics
4. **Weighting**: {Equal weights | Custom weights: ...}

### Scoring Rubric

See [KPI-FRAMEWORK.md] for complete scoring definitions.

### Limitations

- Evaluation limited to GitHub-hosted projects
- Some metrics require API access that may be rate-limited
- Package download data depends on registry availability
- Security metrics may be limited by repository access controls

### Custom Parameters

{If any custom weights or category focus was applied}

- Categories evaluated: {all | specific list}
- Custom weights: {if applicable}
- Minimum score threshold: {if applicable}
```

---

### 7. Sources

**Purpose**: Complete list of all sources used.

**Format**:
```markdown
## Sources

### GitHub API

| Endpoint | Data Retrieved |
|----------|----------------|
| repos/{owner}/{repo} | Repository metadata |
| repos/{owner}/{repo}/contributors | Contributor data |
| repos/{owner}/{repo}/stats/commit_activity | Commit frequency |
| ... | ... |

### Web Sources

| URL | Data Retrieved | Retrieved At |
|-----|----------------|--------------|
| {url} | {description} | {timestamp} |
| ... | ... | ... |

### Package Registry

| Registry | Package | Data Retrieved |
|----------|---------|----------------|
| {npm/PyPI/etc.} | {package_name} | {download counts, etc.} |

### Third-Party Services

| Service | URL | Data Retrieved |
|---------|-----|----------------|
| {Codecov/Snyk/etc.} | {url} | {description} |
```

---

## JSON Output Format

For programmatic consumption, the report can also be output as JSON.

```json
{
  "report_version": "1.0",
  "repository": {
    "owner": "string",
    "repo": "string",
    "url": "https://github.com/{owner}/{repo}"
  },
  "evaluation_date": "ISO-8601",
  "overall_score": {
    "value": 0.0,
    "rating": "string",
    "metrics_available": 0,
    "metrics_total": 30
  },
  "category_scores": {
    "community_health": {
      "score": 0.0,
      "rating": "string",
      "weight": 0.1667,
      "metrics_available": 0,
      "metrics_total": 5
    }
    // ... other categories
  },
  "detailed_findings": {
    "community_health": {
      "metrics": {
        "contributor_count": {
          "value": 0,
          "score": 0,
          "threshold": "string",
          "source": "string",
          "retrieved_at": "ISO-8601"
        }
        // ... other metrics
      },
      "observations": ["string"]
    }
    // ... other categories
  },
  "cross_validation": {
    "verified": [],
    "discrepancies": []
  },
  "data_quality": {
    "total_metrics": 30,
    "collected": 0,
    "unavailable": 0,
    "missing_data": [],
    "stale_warnings": [],
    "access_issues": []
  },
  "sources": {
    "github_api": [],
    "web_sources": [],
    "package_registry": [],
    "third_party": []
  }
}
```

---

## Rendering Notes

### Markdown Output

- Use GitHub-flavored Markdown
- Tables must be properly aligned
- Code blocks for commands and JSON
- Links must be valid and accessible

### Styling Recommendations

When rendered:
- Use color coding for ratings (green=Excellent, red=Critical)
- Score badges for quick visual reference
- Collapsible sections for detailed findings
- Hover tooltips for threshold explanations
