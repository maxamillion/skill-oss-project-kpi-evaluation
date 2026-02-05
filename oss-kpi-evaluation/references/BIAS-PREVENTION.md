# Bias Prevention Protocols

This document defines the protocols and controls used to ensure objective, unbiased evaluation of open source projects.

## Core Principles

### 1. Subagent Isolation

Each research subagent operates in complete isolation:

- **No Shared Context**: Subagents receive only the repository identifier and metrics to collect
- **No Prior Knowledge**: Subagents have no information about why the evaluation is being performed
- **No Cross-Contamination**: Results from one category cannot influence research in another
- **Parallel Execution**: All subagents run simultaneously to prevent sequential bias

**Isolation Checklist**:
- [ ] Subagent prompt contains only project ID and metric definitions
- [ ] No opinions or expectations included in prompt
- [ ] No information about evaluation purpose or requester
- [ ] No references to other categories or their results

### 2. Evidence-Based Only

Every claim in the evaluation must have a verifiable source:

- **Source Required**: No data point accepted without URL or command source
- **Verifiable**: Sources must be accessible for independent verification
- **Current**: Data must be from live sources, not cached or historical
- **Direct**: Prefer primary sources over aggregated or third-party data

**Evidence Checklist**:
- [ ] Every metric value has a source URL or gh command
- [ ] Sources are accessible and return the claimed data
- [ ] No claims based on general knowledge or assumptions
- [ ] Timestamps recorded for all data collection

### 3. No Assumptions

Missing data is flagged, never estimated:

- **NOT_AVAILABLE**: Clear marker for unavailable data
- **Reason Required**: Explanation of why data couldn't be obtained
- **No Interpolation**: Never estimate from related metrics
- **No Inference**: Never deduce values from circumstantial evidence

**Missing Data Format**:
```json
{
  "value": "NOT_AVAILABLE",
  "reason": "Coverage service integration not found",
  "sources_checked": [
    "https://codecov.io/gh/owner/repo",
    "https://coveralls.io/github/owner/repo",
    "README.md badges"
  ]
}
```

### 4. Multiple Source Validation

Critical metrics require cross-validation:

**Cross-Validation Requirements**:

| Metric Type | Validation Approach |
|-------------|---------------------|
| Contributor count | GitHub API vs. Insights page |
| Download counts | Registry API vs. third-party trackers |
| Star counts | GitHub API vs. star history tools |
| Security issues | Dependabot vs. Snyk vs. NVD |
| Coverage | Badge vs. service API vs. CI logs |

**Discrepancy Handling**:
- Flag discrepancies >10% between sources
- Report all source values, not just one
- Note which source is considered authoritative
- Include discrepancy in data quality assessment

### 5. Neutral Language

Reports state facts without value judgments:

**Prohibited Language**:
- Subjective adjectives: great, poor, excellent, terrible, impressive
- Comparative statements: better than, worse than (without explicit comparison)
- Predictive statements: will likely, probably, expected to
- Emotional language: unfortunately, thankfully, worryingly

**Preferred Language**:
- Quantitative statements: "47 contributors in the past 12 months"
- Factual observations: "SECURITY.md file is present"
- Metric-based assessments: "Score: 4/5 based on 85% PR review rate"

**Example Transformations**:

| Biased | Neutral |
|--------|---------|
| "Excellent community engagement" | "Issue response rate: 92% within 7 days" |
| "Poor maintenance" | "Last commit: 94 days ago; 2 releases in 12 months" |
| "The project is thriving" | "Star count: 15,234; Growth rate: +32% (6 months)" |
| "Security is a concern" | "3 high-severity Dependabot alerts open; no SECURITY.md" |

### 6. Temporal Accuracy

All data includes collection context:

**Required Timestamps**:
- `collected_at`: When the data was retrieved
- `data_period`: Time range the data covers (e.g., "past 12 months")
- `source_updated`: When the source was last updated (if available)

**Freshness Warnings**:
- Data older than 24 hours: Note in data quality section
- Data older than 7 days: Flag as potentially stale
- Data older than 30 days: Consider re-collection

---

## Semantic Consistency Rules

Cross-category validation rules to detect data errors and suspicious patterns.

### Rule Definitions

| Rule ID | Name | Condition | Detection | Action |
|---------|------|-----------|-----------|--------|
| R1 | Bus Factor Bound | `bus_factor > contributor_count` | Data error | Flag as data error, require recalculation from intermediate data |
| R2 | Small Sample High Engagement | `contributor_count < 5` AND (`issue_engagement > 90%` OR `pr_engagement > 90%`) | Suspicious sample | Flag as "Small sample size - engagement rate may not be representative" |
| R3 | Inactive but Releasing | `last_commit_recency > 90 days` AND `release_cadence > 6 releases/year` | Inconsistent data | Flag for investigation - releases without recent commits |
| R4 | Growing but Dormant | `contributor_growth > 25%` AND `last_commit_recency > 180 days` | Inconsistent data | Flag for investigation - growth metric inconsistent with activity |
| R5 | Coverage Without Tests | `test_coverage > 0%` AND `test_presence.has_test_directory = false` | Data error | Flag as impossible - coverage requires tests |
| R6 | Reviews Without PRs | `code_review_practice > 0%` AND sample shows zero merged PRs | Data error | Flag as data collection error |

### Rule Application

During Phase 4 (Cross-Validation), apply all semantic consistency rules:

```
For each rule R1-R6:
  1. Evaluate condition using collected metrics
  2. If condition is TRUE, trigger the specified action
  3. Record in cross_validation_results:
     - rule_id
     - condition_evaluated
     - result (pass/fail)
     - action_taken
```

### Flagged Metric Handling

When a metric combination triggers a semantic rule:

1. **Data Errors (R1, R5, R6)**:
   - Recalculate from intermediate data if available
   - If recalculation fails, mark affected metrics as "INCONSISTENT"
   - Reduce confidence score for affected category

2. **Suspicious Patterns (R2)**:
   - Add note to metric: "Small sample size - interpret with caution"
   - Do not change the value, but reduce confidence score

3. **Inconsistent Data (R3, R4)**:
   - Investigate both metrics for data freshness issues
   - If timestamps differ significantly, prefer more recent data
   - Add note explaining the inconsistency

---

## Source Authority Hierarchy

When multiple sources provide conflicting values for the same metric, use this hierarchy to determine which value to report.

### Authority Levels (Highest to Lowest)

| Level | Source Type | Examples | Trust Reason |
|-------|-------------|----------|--------------|
| 1 | GitHub API (authenticated) | `gh api repos/...`, `gh repo view` | Authoritative source, real-time |
| 2 | Package Registry API | npm registry, PyPI JSON API, crates.io API | Authoritative for package data |
| 3 | Real-time Analytics | Codecov API, Ecosyste.ms current data | Direct integration, near real-time |
| 4 | Cached/Historical Analytics | star-history.com, wayback data | May be stale, but verifiable |
| 5 | WebSearch Results | Search engine snippets, third-party articles | Unverified, may be outdated or incorrect |

### Discrepancy Resolution Protocol

When sources at different levels disagree:

1. **Always prefer higher authority level** (lower number wins)
2. **Report the discrepancy** in Cross-Validation Results:
   ```
   | Metric | Authoritative Source | Value | Secondary Source | Value | Variance |
   |--------|---------------------|-------|------------------|-------|----------|
   | star_count | GitHub API (L1) | 15,234 | star-history (L4) | 14,890 | 2.3% |
   ```
3. **If variance > 10%** between adjacent levels, investigate for:
   - Timing differences (check timestamps)
   - API version differences
   - Scope differences (e.g., total vs. recent)

### WebSearch-Derived Data

Data from WebSearch (Level 5) requires special handling:

1. **Never report as fact** without primary source confirmation
2. **For CVEs**: Must verify at nvd.nist.gov
3. **For audits**: Must locate actual audit report
4. **For usage claims**: Treat as "reported" not "confirmed"

Mark unconfirmed WebSearch claims as:
```json
{
  "value": "UNCONFIRMED",
  "claim": "Security audit by XYZ Corp in 2023",
  "source": "WebSearch result",
  "verification_attempted": true,
  "primary_source_found": false
}
```

---

## Bias Types and Mitigations

### Confirmation Bias

**Risk**: Looking for evidence to support a predetermined conclusion.

**Mitigations**:
- Subagent isolation prevents knowing the "expected" outcome
- Structured metrics with predefined rubrics
- All categories evaluated regardless of early results

### Selection Bias

**Risk**: Cherry-picking data points that support a narrative.

**Mitigations**:
- Complete metric set evaluated for every project
- Standardized sampling (e.g., "last 50 PRs")
- Missing data explicitly flagged, not omitted

### Recency Bias

**Risk**: Over-weighting recent events over historical patterns.

**Mitigations**:
- Defined time windows for each metric
- Historical trends included (growth rates)
- Recent activity flagged when anomalous

### Anchoring Bias

**Risk**: First data point unduly influences interpretation.

**Mitigations**:
- All scores calculated mechanically from rubrics
- No subjective "adjustment" of scores
- Cross-validation of critical metrics

### Authority Bias

**Risk**: Trusting high-profile projects or maintainers without verification.

**Mitigations**:
- Same metrics applied to all projects
- Star count is just one metric among many
- Corporate backing doesn't affect rubric application

---

## Quality Control Checklist

### Pre-Research

- [ ] Repository URL validated and accessible
- [ ] Project is on GitHub (skill scope)
- [ ] Project is public
- [ ] Subagent prompts prepared with only project ID

### During Research

- [ ] Each subagent runs independently
- [ ] Live data sources used (not cached)
- [ ] All metric collection attempts documented
- [ ] Unavailable data explicitly marked

### Post-Research

- [ ] JSON output validates against schema
- [ ] All source URLs accessible
- [ ] Cross-validation completed for overlapping metrics
- [ ] Discrepancies flagged and documented

### Report Generation

- [ ] Scores calculated mechanically from rubrics
- [ ] No subjective language in findings
- [ ] All claims have source citations
- [ ] Data quality section included
- [ ] Missing data impact documented

---

## Red Flags for Bias

During review, flag any of the following:

### In Subagent Output

1. **Subjective adjectives** without quantitative backing
2. **Claims without sources** - every value needs a URL
3. **Rounded or estimated values** - real data has specific numbers
4. **Missing NOT_AVAILABLE markers** - silence is not acceptable
5. **Comparisons to unstated baselines** - "high" compared to what?

### In Scoring

1. **Score not matching rubric** - verify threshold application
2. **Subjective adjustments** - scores must be mechanical
3. **Ignored metrics** - all must be included or marked unavailable
4. **Inconsistent weighting** - verify weight application

### In Report

1. **Conclusions not supported by data** - check every claim
2. **Selective emphasis** - all categories equally represented
3. **Recommendations without evidence** - stay factual
4. **Unstated assumptions** - make all reasoning explicit

---

## Dispute Resolution

If evaluation results are disputed:

1. **Provide All Sources**: Every data point is traceable
2. **Re-Collection**: Run subagents again with timestamps
3. **Rubric Review**: Verify score calculation against thresholds
4. **Discrepancy Analysis**: Document any differences between runs
5. **Methodology Transparency**: Share this document and all prompts

---

## Continuous Improvement

### Feedback Loop

- Track metrics that frequently return NOT_AVAILABLE
- Identify data sources with reliability issues
- Update rubrics based on industry standard evolution
- Refine prompts based on subagent output quality

### Audit Trail

All evaluations should retain:
- Original subagent prompts used
- Raw JSON output from each subagent
- Scoring calculations
- Final report
- Timestamp of evaluation
