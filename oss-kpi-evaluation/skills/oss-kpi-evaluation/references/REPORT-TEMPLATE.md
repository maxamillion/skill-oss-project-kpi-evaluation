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
  ### Red Hat Engagement (conditional — only if requested)
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
**Category Confidence**: {High|Medium|Low|Very Low}

#### Metrics

| Metric | Value | Score | Confidence | Notes |
|--------|-------|-------|------------|-------|
| {metric_name} | {value} | X/5 | {High|Med|Low} | {threshold met, verification status} |
| {metric_name} | {value} | X/5 | {High|Med|Low} | {threshold met, verification status} |
| ... | ... | ... | ... | ... |

**Confidence Indicators**:
- 🟢 High: Verified from authoritative source, cross-validated
- 🟡 Medium: Single authoritative source, not cross-validated
- 🟠 Low: Secondary source or small sample size
- 🔴 Very Low: Unverified or data quality issues

#### Evidence Table

| Metric | Source | Authority | Retrieved | Verified |
|--------|--------|-----------|-----------|----------|
| {metric_name} | [{source_type}]({url}) | L1-L5 | {timestamp} | ✓/✗ |
| {metric_name} | [{source_type}]({url}) | L1-L5 | {timestamp} | ✓/✗ |
| ... | ... | ... | ... | ... |

#### Observations

- {Factual observation about metric 1}
- {Factual observation about metric 2}
- {Note about any NOT_AVAILABLE metrics}
- {Note about any UNVERIFIED metrics}
```

**Example - Community Health**:
```markdown
### Community Health

**Category Score**: 4.2/5.0 (Good)
**Category Confidence**: High

#### Metrics

| Metric | Value | Score | Confidence | Notes |
|--------|-------|-------|------------|-------|
| Contributor Count | 127 | 5/5 | 🟢 High | ≥100 contributors; verified |
| Contributor Growth | +18% | 4/5 | 🟢 High | 10-24% growth; calculation verified |
| Bus Factor | 4 | 4/5 | 🟢 High | 4 contributors for 50%; intermediate data verified |
| Issue Engagement | 78% | 4/5 | 🟡 Medium | 75-89% response rate; sample=50/342 |
| PR Engagement | 82% | 4/5 | 🟡 Medium | 75-89% review rate; sample=50/289 |

#### Evidence Table

| Metric | Source | Authority | Retrieved | Verified |
|--------|--------|-----------|-----------|----------|
| Contributor Count | [GitHub API](https://api.github.com/repos/owner/repo/contributors) | L1 | 2024-01-15T10:30:00Z | ✓ |
| Contributor Growth | [GitHub API](https://api.github.com/repos/owner/repo/stats/contributors) | L1 | 2024-01-15T10:31:00Z | ✓ |
| Bus Factor | [GitHub API](https://api.github.com/repos/owner/repo/stats/contributors) | L1 | 2024-01-15T10:30:00Z | ✓ |
| Issue Engagement | [gh issue list](gh issue list --state closed --limit 50) | L1 | 2024-01-15T10:32:00Z | - |
| PR Engagement | [gh pr list](gh pr list --state merged --limit 50) | L1 | 2024-01-15T10:33:00Z | - |

#### Observations

- 127 unique contributors active in the past 12 months (verified: 127)
- Top 4 contributors account for 50% of commits (bus factor = 4)
  - Calculation: user1 (312 commits, 36.8%), user2 (198, 60.2%), user3 (95, 71.4%), user4 (78, 80.6%)
- 78% of issues receive maintainer response within 7 days (sample: 50/342 issues, stratified by month)
- 82% of PRs receive review within 7 days (sample: 50/289 PRs)
```

---

### 3a. Red Hat Engagement (Conditional)

**Purpose**: Document Red Hat AI Engineering organization's participation in the project. Only included when Red Hat Engagement analysis is explicitly requested.

**Disclaimer**: RH Engagement scores measure organizational participation, not project quality. A low RH Engagement score does not indicate a low-quality project.

**Format**:
```markdown
### Red Hat Engagement

> **Note**: RH Engagement scores measure organizational participation and investment, not project quality.

**Category Score**: X.X/5.0 ({rating})
**Category Confidence**: {High|Medium|Low|Very Low}
**Identification Method**: {LDAP org traversal|Email-only fallback}

#### RH AI Engineering Org Coverage

| Metric | Value |
|--------|-------|
| Total org members (LDAP) | X |
| GitHub username resolved | X |
| GitHub username unresolved | X |
| Coverage | X% |

{If coverage < 70%}
> ⚠️ **Coverage Gap Warning**: Coverage is below 70%. RH engagement metrics may undercount actual contributions. All metrics in this category have reduced confidence.
{/If}

#### Unresolved Employees

The following AI Engineering org members could not be correlated to a GitHub username.
Their contributions may not be reflected in the metrics above.

| Name | Email | Title | Discovery Attempted |
|------|-------|-------|---------------------|
| ... | ...@redhat.com | ... | Yes/No |

#### Metrics

| Metric | Value | Score | Confidence | Notes |
|--------|-------|-------|------------|-------|
| RH PR Contributions | X% | X/5 | {High|Med|Low} | {threshold met} |
| RH Release Management | {description} | X/5 | {High|Med|Low} | {evidence summary} |
| RH Maintainership | {count} maintainers | X/5 | {High|Med|Low} | {roles identified} |
| RH Roadmap Influence | {description} | X/5 | {High|Med|Low} | {evidence summary} |
| RH Leadership Roles | {count} positions | X/5 | {High|Med|Low} | {positions held} |

#### Evidence Table

| Metric | Source | Authority | Retrieved | Verified |
|--------|--------|-----------|-----------|----------|
| RH PR Contributions | [gh pr list](command) | L1 | {timestamp} | ✓/✗ |
| RH Employee ID | [LDAP](ldapsearch) | L1.5 | {timestamp} | ✓/✗ |
| RH Maintainership | [MAINTAINERS](url) | L1 | {timestamp} | ✓/✗ |
| ... | ... | ... | ... | ... |

#### Observations

- {X}% of merged PRs authored by identified RH AI Engineering employees ({Y} of {Z} employees resolved)
- {Factual observation about maintainership}
- {Factual observation about governance roles}
- {Note about coverage gaps if applicable}
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

- **Total Metrics Defined**: 30 (35 with RH Engagement)
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

| Category | Metrics Available | Verified | Sample Quality | Overall Confidence |
|----------|-------------------|----------|----------------|-------------------|
| Community Health | {X}/5 | {X}/5 | {Good/Fair/Poor} | {High/Medium/Low/Very Low} |
| Maintenance | {X}/5 | {X}/5 | {Good/Fair/Poor} | {High/Medium/Low/Very Low} |
| Security | {X}/5 | {X}/5 | {Good/Fair/Poor} | {High/Medium/Low/Very Low} |
| Documentation | {X}/5 | {X}/5 | {Good/Fair/Poor} | {High/Medium/Low/Very Low} |
| Adoption | {X}/5 | {X}/5 | {Good/Fair/Poor} | {High/Medium/Low/Very Low} |
| Code Quality | {X}/5 | {X}/5 | {Good/Fair/Poor} | {High/Medium/Low/Very Low} |
```

**Confidence Calculation**:

Overall Confidence = weighted average of:
- Source Verified (30%): Metric re-fetched and matched within 5%
- Sample Quality (25%): Sample size meets minimum requirements
- Data Freshness (20%): Data collected within 5 minutes
- Cross-Validated (15%): Confirmed by secondary source
- Bounds Valid (10%): Passes semantic consistency rules

**Confidence Levels**:
- 🟢 **High** (0.8-1.0): All metrics verified, good sample quality, cross-validated
- 🟡 **Medium** (0.6-0.79): Most metrics verified, adequate samples
- 🟠 **Low** (0.4-0.59): Some verification issues or small samples
- 🔴 **Very Low** (<0.4): Significant data quality concerns, use with caution

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
  "report_version": "2.0",
  "repository": {
    "owner": "string",
    "repo": "string",
    "url": "https://github.com/{owner}/{repo}"
  },
  "evaluation_date": "ISO-8601",
  "overall_score": {
    "value": 0.0,
    "rating": "string",
    "confidence": "High|Medium|Low|Very Low",
    "confidence_score": 0.0,
    "metrics_available": 0,
    "metrics_total": "30 (or 35 with RH Engagement)",
    "metrics_verified": 0
  },
  "category_scores": {
    "community_health": {
      "score": 0.0,
      "rating": "string",
      "weight": 0.1667,
      "metrics_available": 0,
      "metrics_total": 5,
      "confidence": {
        "rating": "High|Medium|Low|Very Low",
        "score": 0.0,
        "factors": {
          "source_verified": 0.0,
          "sample_quality": 0.0,
          "data_freshness": 0.0,
          "cross_validated": 0.0,
          "bounds_valid": 0.0
        }
      }
    }
    // ... other categories
    // When RH Engagement is included:
    "rh_engagement": {
      "score": 0.0,
      "rating": "string",
      "weight": 0.1429,
      "conditional": true,
      "metrics_available": 0,
      "metrics_total": 5,
      "confidence": {
        "rating": "High|Medium|Low|Very Low",
        "score": 0.0,
        "coverage_percentage": 0.0,
        "coverage_gap_impact": "none|reduced_confidence"
      }
    }
  },
  "detailed_findings": {
    "community_health": {
      "metrics": {
        "contributor_count": {
          "value": 0,
          "score": 0,
          "threshold": "string",
          "confidence": {
            "rating": "High|Medium|Low|Very Low",
            "score": 0.0,
            "verified": true,
            "verification_details": {
              "subagent_value": 0,
              "verified_value": 0,
              "variance_percent": 0.0,
              "status": "VERIFIED|UNVERIFIED"
            }
          },
          "source": "string",
          "source_authority": "L1|L2|L3|L4|L5",
          "retrieved_at": "ISO-8601"
        },
        "bus_factor": {
          "value": 0,
          "score": 0,
          "threshold": "string",
          "confidence": {
            "rating": "High|Medium|Low|Very Low",
            "score": 0.0,
            "calculation_verified": true
          },
          "calculation": {
            "total_commits_12_months": 0,
            "contributor_distribution": [
              {"contributor": "string", "commits": 0, "percent": 0.0, "cumulative_percent": 0.0}
            ],
            "threshold_50_percent_reached_at": 0
          },
          "source": "string",
          "source_authority": "L1",
          "retrieved_at": "ISO-8601"
        }
        // ... other metrics with their calculation/checklist details
      },
      "observations": ["string"]
    }
    // ... other categories
  },
  "source_verification": {
    "high_priority_metrics": [
      {
        "metric": "star_count",
        "subagent_value": 0,
        "verified_value": 0,
        "variance_percent": 0.0,
        "status": "VERIFIED|UNVERIFIED",
        "verified_at": "ISO-8601"
      }
    ],
    "verification_summary": {
      "total_verified": 0,
      "passed": 0,
      "failed": 0
    }
  },
  "cross_validation": {
    "verified": [],
    "discrepancies": [],
    "semantic_consistency": {
      "rules_evaluated": ["R1", "R2", "R3", "R4", "R5", "R6", "R7", "R8", "R9"],
      "rules_passed": [],
      "rules_failed": [],
      "flags": []
    },
    "bounds_validation": {
      "checks_performed": 0,
      "checks_passed": 0,
      "violations": []
    }
  },
  "data_quality": {
    "total_metrics": 30,
    "collected": 0,
    "unavailable": 0,
    "verified": 0,
    "unverified": 0,
    "missing_data": [],
    "stale_warnings": [],
    "access_issues": [],
    "sample_quality": {
      "adequate_samples": 0,
      "small_samples": 0,
      "very_small_samples": 0
    }
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
