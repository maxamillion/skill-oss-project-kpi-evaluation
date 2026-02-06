---
name: oss-kpi-evaluation
description: Evaluates open source projects against Key Performance Indicators using multi-agent research. Performs live web research to gather current metrics on community health, maintenance, security, documentation, adoption, and code quality. Use when evaluating OSS projects for adoption, investment, or comparison.
license: MIT
compatibility: Requires internet access for web research. Designed for Claude Code.
metadata:
  author: admiller
  version: "1.0"
allowed-tools: WebSearch WebFetch Read Glob Grep Bash(gh:*) Bash(ldapsearch:*) Bash(klist:*) Bash(git log:*) Task
---

# OSS KPI Evaluation Agent Skill

## Purpose and Scope

This skill evaluates open source projects against six Key Performance Indicator (KPI) categories using multi-agent research with built-in bias prevention. It performs live web research to gather current, verifiable metrics. An optional 7th category (Red Hat Engagement) can be included when explicitly requested to measure Red Hat AI Engineering's participation in a project.

### When to Use This Skill

- **Adoption Decisions**: Evaluating whether to adopt an OSS project
- **Investment Analysis**: Assessing project health for funding decisions
- **Project Comparison**: Objective comparison of competing projects
- **Due Diligence**: Comprehensive project health assessment
- **Risk Assessment**: Identifying maintenance, security, or community risks

### Scope Limitations

- **GitHub Only**: Projects must be hosted on GitHub
- **Single Project**: Evaluates one project per invocation
- **English Language**: Optimized for English-language projects and documentation

## Quick Start

```
Evaluate the project: https://github.com/fastapi/fastapi
```

Or with specific focus:

```
Evaluate https://github.com/psf/requests focusing on security and maintenance
```

## Evaluation Workflow

The evaluation follows six phases with strict quality controls.

### Phase 1: Input Validation

- [ ] Extract owner and repository name from input
- [ ] Validate repository exists via `gh repo view`
- [ ] Confirm repository is public
- [ ] Record repository metadata (stars, forks, language, license)

### Phase 1.5: LDAP Prerequisite Check (Conditional)

If Red Hat Engagement analysis is requested, verify prerequisites before dispatch:

- [ ] Verify Kerberos ticket: `klist` — must show a valid TGT
- [ ] Test LDAP connectivity: `ldapsearch -x -h ldap.corp.redhat.com -b dc=redhat,dc=com '(uid=shuels)' uid`
- [ ] If both checks pass: Set `ldap_available=true` for the RH subagent
- [ ] If either check fails: Set `ldap_available=false` — RH subagent will use email-only fallback with reduced confidence
- [ ] Log prerequisite check results for inclusion in the report

### Phase 2: Parallel Research Dispatch

Launch six independent research subagents in parallel. Each subagent:
- Receives ONLY the project identifier and metrics to collect
- Has no access to other subagent results
- Must use live data sources (WebSearch, WebFetch, gh CLI)
- Returns structured JSON with sources

**Subagents to dispatch:**
1. `kpi-community-researcher` - Community health metrics
2. `kpi-maintenance-researcher` - Maintenance activity metrics
3. `kpi-security-researcher` - Security posture metrics
4. `kpi-documentation-researcher` - Documentation quality metrics
5. `kpi-adoption-researcher` - Adoption and usage metrics
6. `kpi-codequality-researcher` - Code quality metrics
7. `kpi-rh-engagement-researcher` - Red Hat Engagement metrics *(conditional — only if requested)*

See `references/RESEARCH-PROMPTS.md` for subagent prompt templates.

### Phase 3: Result Collection & Parsing

- [ ] Collect JSON output from each subagent
- [ ] Validate JSON structure matches expected schema
- [ ] Flag any subagent failures or incomplete data
- [ ] Aggregate results into unified data structure

### Phase 3.5: Source Verification

**Purpose**: Prevent hallucinated values by independently re-fetching critical metrics.

**HIGH Priority Metrics** (must verify):
- `star_count` - via `gh repo view {owner}/{repo} --json stargazerCount`
- `contributor_count` - via `gh api repos/{owner}/{repo}/stats/contributors`
- `download_count` - via package registry API

**Verification Protocol**:
- [ ] Re-fetch each HIGH priority metric using the source command
- [ ] Compare subagent-reported value with freshly fetched value
- [ ] Apply 5% tolerance for timing differences
- [ ] Mark metrics exceeding tolerance as "UNVERIFIED" with both values
- [ ] If >3 metrics fail verification, trigger subagent re-run for affected category

**Verification Output Format**:
```json
{
  "metric": "star_count",
  "subagent_value": 15234,
  "verified_value": 15189,
  "variance_percent": 0.3,
  "status": "VERIFIED",
  "verified_at": "ISO-8601 timestamp"
}
```

**Unverified Metric Handling**:
- NEVER report disputed values as fact
- Report as: "star_count: UNVERIFIED (subagent: 15234, verification: 12891)"
- Flag in Data Quality Assessment section
- Reduce confidence score for affected category

### Phase 4: Cross-Validation

- [ ] Compare overlapping metrics between subagents
- [ ] Flag discrepancies exceeding 10% variance
- [ ] Verify all URLs are accessible
- [ ] Check temporal consistency (data freshness)
- [ ] **Bounds Validation**: Verify logical constraints between metrics
  - `bus_factor` must be ≤ `contributor_count`
  - `contributor_count` must be ≤ `fork_count * 10` (heuristic upper bound)
  - `issue_engagement` >90% with <5 contributors warrants scrutiny
- [ ] **Independent Recalculation**: For calculated metrics (bus_factor, engagement rates), recompute from intermediate data provided by subagents. Flag >5% variance from reported value.
- [ ] **Temporal Sanity**: Verify all subagent collections completed within 5 minutes of each other

See `references/BIAS-PREVENTION.md` for validation protocols and semantic consistency rules.

### Phase 5: Mechanical Scoring

- [ ] Load scoring rubric from `assets/scoring-rubric.json`
- [ ] Apply threshold-based scoring to each metric
- [ ] Calculate category scores (average of metric scores)
- [ ] Calculate overall score (weighted average of categories)
- [ ] Apply custom weights if specified by user

### Phase 6: Report Generation

- [ ] Generate executive summary with overall score
- [ ] Create per-category detailed sections
- [ ] Include evidence tables with sources
- [ ] Document data quality issues
- [ ] List all sources with retrieval timestamps

See `references/REPORT-TEMPLATE.md` for output format.

## Subagent Architecture

Each research subagent operates in complete isolation to prevent bias contamination.

### Architecture Principles

1. **Statelessness**: Each subagent receives a complete, self-contained prompt
2. **No Shared Context**: Subagents cannot reference parent conversation
3. **Live Data Only**: All metrics from WebSearch, WebFetch, or gh CLI
4. **Structured Output**: JSON format for unambiguous parsing
5. **Source Attribution**: Every data point includes its source

### Subagent Responsibilities

| Subagent | Purpose | Primary Tools |
|----------|---------|---------------|
| kpi-community-researcher | Contributor metrics, engagement | WebSearch, gh CLI |
| kpi-maintenance-researcher | Release cadence, issue resolution | WebSearch, gh CLI |
| kpi-security-researcher | Security policy, vulnerabilities | WebSearch, gh CLI |
| kpi-documentation-researcher | Docs quality, completeness | WebSearch, Read |
| kpi-adoption-researcher | Usage metrics, trends | WebSearch, WebFetch |
| kpi-codequality-researcher | Tests, CI, code review | WebSearch, gh CLI |
| kpi-rh-engagement-researcher | RH employee contributions, governance | ldapsearch, gh CLI, git log *(conditional)* |

### Launching Subagents

Use the Task tool with `subagent_type: "general-purpose"` for each researcher. Launch all six in parallel using a single message with multiple Task tool invocations.

**Critical**: Use the prompts from `references/RESEARCH-PROMPTS.md` verbatim, substituting only the `{owner}` and `{repo}` placeholders.

## Bias Prevention Protocol

This skill implements strict bias prevention measures. See `references/BIAS-PREVENTION.md` for complete protocols.

### Key Controls

1. **Subagent Isolation**: Research agents receive only project ID and metrics to collect - no opinions, no context about why evaluation is happening
2. **Evidence-Based Only**: Every claim requires a verifiable source URL
3. **No Assumptions**: Missing data is flagged as "NOT_AVAILABLE" - never estimated
4. **Multiple Sources**: Critical metrics cross-validated with 2+ sources
5. **Neutral Language**: Report states facts without value judgments
6. **Temporal Accuracy**: All data includes collection timestamp

### Red Flags for Bias

- Subjective adjectives (great, poor, excellent, terrible)
- Claims without source URLs
- Inferred or estimated values
- Comparison to unstated baselines
- Assumptions about project intent

## KPI Categories Overview

See `references/KPI-FRAMEWORK.md` for detailed metric definitions and scoring rubrics.

### 1. Community Health (Weight: 16.67%)

Measures the contributor community's size, diversity, and engagement.

- Contributor count and growth rate
- Bus factor (contributor concentration)
- Issue and PR engagement
- Community responsiveness

### 2. Maintenance (Weight: 16.67%)

Measures ongoing project maintenance activity.

- Commit frequency
- Release cadence
- Issue resolution time
- PR merge time
- Dependency updates

### 3. Security (Weight: 16.67%)

Measures security posture and practices.

- Security policy presence
- Vulnerability disclosure process
- Dependency vulnerability status
- Security audit history

### 4. Documentation (Weight: 16.67%)

Measures documentation quality and completeness.

- README completeness
- API documentation
- Getting started guide
- Changelog maintenance
- Example coverage

### 5. Adoption (Weight: 16.67%)

Measures project adoption and usage trends.

- Star count and growth trend
- Fork activity
- Download/install counts
- Dependent project count
- Corporate adoption evidence

### 6. Code Quality (Weight: 16.67%)

Measures code quality practices and metrics.

- Test presence and coverage
- CI/CD configuration
- Code review practices
- Linting configuration
- Type checking (where applicable)

### 7. Red Hat Engagement (Weight: ~14.29% — Conditional)

Measures Red Hat AI Engineering organization's participation and investment. Only evaluated when explicitly requested. When included, all 7 categories are weighted equally at ~14.29%.

- RH PR contributions (percentage of merged PRs)
- RH release management involvement
- RH maintainership roles
- RH roadmap influence
- RH leadership and governance roles

**Note**: Requires LDAP access to `ldap.corp.redhat.com` for full employee identification. Falls back to email-based identification if LDAP is unavailable.

## Output Format

The evaluation produces a structured report. See `references/REPORT-TEMPLATE.md` for the complete specification.

### Report Sections

1. **Executive Summary**
   - Overall score (1-5 scale)
   - Category score summary
   - Key strengths (top 3)
   - Key concerns (top 3)

2. **Category Details** (×6)
   - Category score
   - Individual metric scores
   - Evidence table with sources
   - Data quality notes

3. **Cross-Validation Results**
   - Discrepancy report
   - Source reliability assessment

4. **Data Quality Assessment**
   - Missing data summary
   - Stale data warnings
   - Source accessibility issues

5. **Sources**
   - Complete URL list
   - Retrieval timestamps
   - Source categorization

## Customization Options

### Custom Weights

Specify category weights in the invocation:

```
Evaluate https://github.com/owner/repo with weights:
- Security: 30%
- Maintenance: 25%
- Community: 15%
- Adoption: 15%
- Documentation: 10%
- Code Quality: 5%
```

### Category Focus

Evaluate only specific categories:

```
Evaluate https://github.com/owner/repo for security and maintenance only
```

### Threshold Override

Set custom pass/fail thresholds:

```
Evaluate https://github.com/owner/repo with minimum score 4.0
```

### Red Hat Engagement Analysis

Include the conditional RH Engagement category to measure Red Hat AI Engineering's participation:

```
Evaluate https://github.com/owner/repo with Red Hat engagement analysis
```

This adds a 7th category and adjusts all category weights to ~14.29%. Requires Red Hat internal network access (VPN, Kerberos) for LDAP-based employee identification.

## Error Handling

### Common Issues

| Error | Cause | Resolution |
|-------|-------|------------|
| Repository not found | Invalid URL or private repo | Verify URL, check repo visibility |
| Rate limit exceeded | Too many API calls | Wait and retry, or use authenticated gh |
| Subagent timeout | Network issues | Retry failed subagent |
| Missing metrics | Data not available | Noted in report, score adjusted |
| LDAP unavailable | No Kerberos ticket or network access | RH subagent uses email-only fallback; reduced confidence noted |

### Partial Results

If some subagents fail, the evaluation continues with available data. The report clearly indicates:
- Which categories have incomplete data
- Impact on overall score reliability
- Recommendation for re-evaluation

## References

- `references/KPI-FRAMEWORK.md` - Complete KPI definitions and scoring rubrics
- `references/DATA-SOURCES.md` - Data collection methods and APIs
- `references/RESEARCH-PROMPTS.md` - Subagent prompt templates
- `references/BIAS-PREVENTION.md` - Bias prevention protocols
- `references/REPORT-TEMPLATE.md` - Output format specification
- `assets/scoring-rubric.json` - Machine-readable scoring criteria
