# Data Sources

This document maps KPI metrics to their data collection methods and sources.

## Data Source Categories

### Primary Sources

Direct API access providing authoritative data:

| Source | Access Method | Rate Limits | Data Freshness |
|--------|---------------|-------------|----------------|
| GitHub API | `gh` CLI | 5,000/hour authenticated | Real-time |
| npm Registry | WebFetch | 1,000/hour | ~15 min delay |
| PyPI | WebFetch | Generous | ~1 hour delay |
| Maven Central | WebFetch | Generous | ~1 hour delay |

### Secondary Sources

Aggregated or processed data:

| Source | Access Method | Data Type |
|--------|---------------|-----------|
| OpenSSF Scorecard | WebFetch | Security metrics |
| Libraries.io | WebFetch | Dependency data |
| Star History | WebFetch | Historical trends |
| Codecov/Coveralls | WebFetch | Coverage metrics |
| ClickPy | WebFetch | PyPI download analytics |
| Ecosyste.ms | WebFetch | Package ecosystem data |
| Eight Knot | WebFetch | Community health metrics |
| LF Insights | WebFetch | OSS project analytics |

### Web Research Sources

For data not available via APIs:

| Data Type | Search Strategy |
|-----------|-----------------|
| Audit reports | WebSearch: "{project} security audit report" |
| Corporate adoption | WebSearch: "{project} used by companies" |
| News/announcements | WebSearch: "{project} release announcement" |
| Case studies | WebSearch: "{project} case study production" |

---

## GitHub CLI Commands

The `gh` CLI provides authenticated access to GitHub APIs.

### Repository Metadata

```bash
# Basic repo info (stars, forks, language, license)
gh repo view {owner}/{repo} --json stargazerCount,forkCount,primaryLanguage,licenseInfo

# Full repository details
gh api repos/{owner}/{repo}
```

### Contributors

```bash
# List contributors with commit counts
gh api repos/{owner}/{repo}/contributors --paginate

# Contributor statistics (commit distribution)
gh api repos/{owner}/{repo}/stats/contributors
```

### Commits

```bash
# Recent commits
gh api repos/{owner}/{repo}/commits --per-page=100

# Commit activity by week
gh api repos/{owner}/{repo}/stats/commit_activity

# Last commit date
gh api repos/{owner}/{repo}/commits?per_page=1 --jq '.[0].commit.author.date'
```

### Issues

```bash
# Open issues count
gh issue list --repo {owner}/{repo} --state open --json number | jq length

# Closed issues in date range
gh issue list --repo {owner}/{repo} --state closed --search "closed:>2024-01-01" --json number,createdAt,closedAt

# Issues with labels
gh issue list --repo {owner}/{repo} --label "bug" --json number,title
```

### Pull Requests

```bash
# Open PRs
gh pr list --repo {owner}/{repo} --state open --json number

# Merged PRs with dates
gh pr list --repo {owner}/{repo} --state merged --json number,createdAt,mergedAt --limit 100

# PRs with reviews
gh pr list --repo {owner}/{repo} --state merged --json number,reviews --limit 50
```

### Releases

```bash
# List releases
gh release list --repo {owner}/{repo}

# Release details with dates
gh api repos/{owner}/{repo}/releases --per-page=50 --jq '.[] | {tag: .tag_name, date: .published_at}'
```

### Security

```bash
# Security policy
gh api repos/{owner}/{repo}/contents/SECURITY.md --jq '.content' | base64 -d

# Code scanning alerts (if enabled)
gh api repos/{owner}/{repo}/code-scanning/alerts

# Dependabot alerts (if enabled)
gh api repos/{owner}/{repo}/dependabot/alerts
```

### Repository Files

```bash
# Check file existence
gh api repos/{owner}/{repo}/contents/CONTRIBUTING.md

# List workflow files
gh api repos/{owner}/{repo}/contents/.github/workflows

# Get README
gh api repos/{owner}/{repo}/readme
```

---

## Package Registry APIs

### npm (JavaScript/TypeScript)

```bash
# Package metadata
WebFetch: https://registry.npmjs.org/{package}

# Download stats
WebFetch: https://api.npmjs.org/downloads/point/last-month/{package}
WebFetch: https://api.npmjs.org/downloads/range/last-year/{package}

# Alternative: npm-stat.com
WebFetch: https://npm-stat.com/api/download-counts?package={package}&from=2024-01-01&until=2024-12-31
```

### PyPI (Python)

```bash
# Package metadata
WebFetch: https://pypi.org/pypi/{package}/json

# Download stats
WebFetch: https://pypistats.org/api/packages/{package}/recent
WebFetch: https://pypistats.org/api/packages/{package}/overall
```

### Maven Central (Java)

```bash
# Package search
WebFetch: https://search.maven.org/solrsearch/select?q=g:{groupId}+AND+a:{artifactId}

# Download stats via mvnrepository
WebSearch: "mvnrepository {groupId} {artifactId} usages"
```

### RubyGems (Ruby)

```bash
# Gem metadata
WebFetch: https://rubygems.org/api/v1/gems/{gem}.json

# Download stats included in metadata
```

### Crates.io (Rust)

```bash
# Crate metadata
WebFetch: https://crates.io/api/v1/crates/{crate}

# Download stats included in metadata
```

---

## Security Data Sources

### OpenSSF Scorecard

```bash
# Get scorecard for a repository
WebFetch: https://api.securityscorecards.dev/projects/github.com/{owner}/{repo}
```

The scorecard provides scores for:
- Code-Review
- Maintained
- CII-Best-Practices
- License
- Signed-Releases
- Branch-Protection
- Dangerous-Workflow
- Dependency-Update-Tool
- Fuzzing
- Pinned-Dependencies
- SAST
- Security-Policy
- Token-Permissions
- Vulnerabilities

### Snyk Advisor

```bash
# npm packages
WebFetch: https://snyk.io/advisor/npm-package/{package}

# Python packages
WebFetch: https://snyk.io/advisor/python/{package}
```

### CVE Databases

```bash
# NVD search
WebSearch: "CVE {project} site:nvd.nist.gov"

# GitHub Advisory Database
WebSearch: "site:github.com/advisories {project}"
```

---

## Documentation Sources

### ReadTheDocs

```bash
# Check for ReadTheDocs presence
WebFetch: https://{project}.readthedocs.io/

# API for project info
WebFetch: https://readthedocs.org/api/v3/projects/{project}/
```

### GitHub Pages

```bash
# Check for GitHub Pages
gh api repos/{owner}/{repo}/pages

# Common patterns
WebFetch: https://{owner}.github.io/{repo}/
```

### Documentation Platforms

```bash
# Docusaurus/GitBook/etc.
WebSearch: "{project} documentation site"

# API documentation
WebSearch: "{project} API reference documentation"
```

---

## Adoption Metrics Sources

### Star History

```bash
# Star history data
WebFetch: https://api.star-history.com/svg?repos={owner}/{repo}

# Alternative: GitHub Archive
WebSearch: "github archive {owner}/{repo} stars"
```

### Dependency Data

```bash
# GitHub dependency graph
gh api repos/{owner}/{repo}/dependents

# Libraries.io
WebFetch: https://libraries.io/api/github/{owner}/{repo}/dependents

# npm dependents
WebSearch: "npmjs.com {package} dependents"
```

### Corporate Adoption

```bash
# Company usage
WebSearch: "{project} used in production"
WebSearch: "{project} case study enterprise"
WebSearch: "companies using {project}"

# Logos/testimonials on project site
WebFetch: {project website} - look for "used by" section
```

---

## Code Quality Sources

### Coverage Services

```bash
# Codecov
WebFetch: https://codecov.io/gh/{owner}/{repo}
WebFetch: https://codecov.io/api/gh/{owner}/{repo}

# Coveralls
WebFetch: https://coveralls.io/github/{owner}/{repo}
```

### Code Analysis

```bash
# SonarCloud
WebFetch: https://sonarcloud.io/project/overview?id={project-key}

# Code Climate
WebFetch: https://codeclimate.com/github/{owner}/{repo}
```

### CI Status

```bash
# GitHub Actions
gh api repos/{owner}/{repo}/actions/runs --jq '.workflow_runs[0].conclusion'

# Check workflow files
gh api repos/{owner}/{repo}/contents/.github/workflows
```

---

## OSS Analytics Platforms

### ClickPy (PyPI Download Analytics)

ClickPy provides detailed Python package download statistics powered by ClickHouse.

```bash
# Package download statistics
WebFetch: https://clickpy.clickhouse.com/dashboard/{package}

# API endpoints for download data
WebFetch: https://clickpy.clickhouse.com/api/v1/downloads/{package}
```

Provides:
- Daily/weekly/monthly download counts
- Download trends over time
- Country-level download distribution
- Python version distribution
- System/installer breakdown

### Ecosyste.ms

Ecosyste.ms provides comprehensive package ecosystem data across multiple registries.

```bash
# Package information
WebFetch: https://ecosyste.ms/api/v1/packages/{registry}/{package}

# Repository information
WebFetch: https://repos.ecosyste.ms/api/v1/repositories/lookup?url=https://github.com/{owner}/{repo}

# Package dependencies
WebFetch: https://ecosyste.ms/api/v1/packages/{registry}/{package}/dependencies

# Dependent packages
WebFetch: https://ecosyste.ms/api/v1/packages/{registry}/{package}/dependents
```

Provides:
- Cross-registry package data (npm, PyPI, RubyGems, etc.)
- Dependency graphs
- Dependent project counts
- Repository metadata
- Maintainer information
- Version history

### Eight Knot (OSCI Community Insights)

Eight Knot provides open source community health and contributor metrics.

```bash
# Project community metrics
WebFetch: https://eightknot.osci.io/api/projects/{project}

# Contributor activity data
WebFetch: https://eightknot.osci.io/api/projects/{project}/contributors

# Community health indicators
WebFetch: https://eightknot.osci.io/api/projects/{project}/health
```

Provides:
- Contributor diversity metrics
- Organizational contributor breakdown
- Community growth trends
- Contributor retention rates
- Collaboration patterns

### Linux Foundation Insights

LF Insights provides analytics for Linux Foundation and CNCF projects.

```bash
# Project overview
WebFetch: https://insights.linuxfoundation.org/projects/{project}

# Community metrics
WebFetch: https://insights.linuxfoundation.org/api/v1/projects/{project}/community

# Contribution activity
WebFetch: https://insights.linuxfoundation.org/api/v1/projects/{project}/contributions
```

Provides:
- Contribution activity (commits, PRs, issues)
- Contributor demographics
- Organization participation
- Velocity metrics
- Comparative project analytics (for LF/CNCF projects)

---

## Red Hat Internal Sources (Conditional)

These sources are only used when the Red Hat Engagement category is explicitly requested. They require Red Hat internal network access.

### Prerequisites

| Requirement | Verification | Failure Mode |
|-------------|-------------|--------------|
| Kerberos ticket | `klist` — must show valid TGT | All RH metrics → NOT_AVAILABLE |
| LDAP connectivity | `ldapsearch -x -h ldap.corp.redhat.com -b dc=redhat,dc=com '(uid=shuels)' uid` | All RH metrics → NOT_AVAILABLE |
| VPN / internal network | Implicit via LDAP connectivity test | All RH metrics → NOT_AVAILABLE |

### LDAP Org Traversal

Enumerate AI Engineering org members starting from Steven Huels (`shuels`):

```bash
# Step 1: Find direct reports of shuels
ldapsearch -LLL -x -h ldap.corp.redhat.com -b ou=users,dc=redhat,dc=com \
  '(manager=uid=shuels,ou=users,dc=redhat,dc=com)' uid cn mail rhatSocialURL

# Step 2: Recursively find reports for each manager discovered
# For each uid returned that has direct reports, repeat the query:
ldapsearch -LLL -x -h ldap.corp.redhat.com -b ou=users,dc=redhat,dc=com \
  '(manager=uid={manager_uid},ou=users,dc=redhat,dc=com)' uid cn mail rhatSocialURL

# Continue until no new reports are found (leaf employees)
```

### GitHub Username Extraction from LDAP

Parse `rhatSocialURL` field for GitHub usernames:

```bash
# rhatSocialURL format contains entries like:
# Github->https://github.com/{username}
# Extract username from this pattern

# If rhatSocialURL contains a GitHub entry → resolved (high confidence)
# If rhatSocialURL is absent or has no GitHub entry → unresolved (needs discovery)
```

### GitHub Username Discovery Fallback

For employees without `rhatSocialURL` GitHub entries:

```bash
# Search GitHub by name and email
gh search users "{employee_name}" --limit 5

# Search git log for their @redhat.com email in the target repo
git log --all --format='%ae %an' | grep -i "{employee_email}"

# Check if any discovered user has commits to @redhat.com-affiliated repos
```

### Git Email Extraction

Identify RH contributors by email domain:

```bash
# Extract all committer emails from repo
git log --all --format='%ae' | sort -u | grep -i '@redhat.com'

# Map emails to contributors
git log --all --format='%ae %an' | grep -i '@redhat.com' | sort -u
```

### Governance File Parsing

Identify RH employees in project governance:

```bash
# Common governance files to check
gh api repos/{owner}/{repo}/contents/MAINTAINERS
gh api repos/{owner}/{repo}/contents/OWNERS
gh api repos/{owner}/{repo}/contents/CODEOWNERS
gh api repos/{owner}/{repo}/contents/GOVERNANCE.md

# Parse for GitHub usernames matching identified RH employees
```

### Release Author Identification

Identify who creates releases:

```bash
# Get release authors
gh api repos/{owner}/{repo}/releases --jq '.[] | {tag: .tag_name, author: .author.login, date: .published_at}'

# Cross-reference authors with identified RH employees
```

---

## Sampling Methodology

When collecting data from populations (issues, PRs, contributors, etc.), follow these sampling requirements to ensure statistical validity.

### Sample Size Requirements

| Population Size | Sample Size | Sampling Method | Confidence Level |
|-----------------|-------------|-----------------|------------------|
| < 50 | All (census) | N/A - full population | 100% |
| 50 - 100 | 50 | Stratified by time period | ~95% |
| 101 - 500 | 100 | Stratified by time period | ~95% |
| > 500 | 100-150 | Stratified by time period | ~95% |

### Stratified Sampling by Time Period

For time-series data (issues, PRs, commits), stratify samples to avoid recency bias:

```
Population: 200 closed issues in past 6 months
Sample size: 100
Stratification:
  - Month 1: 17 issues (proportional to monthly volume)
  - Month 2: 18 issues
  - Month 3: 15 issues
  - Month 4: 16 issues
  - Month 5: 17 issues
  - Month 6: 17 issues
Selection: Random within each stratum
```

If proportional stratification is impractical, use most-recent sampling but document the limitation.

### Required Sampling Documentation

Every sampled metric must include:

```json
{
  "metric": "issue_resolution_time",
  "population_size": 342,
  "sample_size": 100,
  "sampling_method": "stratified_by_month",
  "confidence_level": "95%",
  "margin_of_error": "±8%",
  "sampling_notes": "Proportional allocation across 6 months"
}
```

### Minimum Sample Sizes for Validity

| Metric | Minimum Sample | Rationale |
|--------|----------------|-----------|
| Issue resolution time | 30 | Median calculation stability |
| PR merge time | 30 | Median calculation stability |
| Issue engagement | 30 | Percentage reliability |
| PR engagement | 30 | Percentage reliability |
| Code review practice | 30 | Percentage reliability |
| Fork activity | 50 | Fork behavior variance |

If population is smaller than minimum sample, use census (all items) and note low confidence.

### Sample Quality Flags

Flag samples that may not be representative:

| Condition | Flag | Action |
|-----------|------|--------|
| Sample < 10 | "Very small sample" | Reduce confidence to "Low" |
| Sample < 30 | "Small sample" | Reduce confidence to "Medium" |
| >50% from single month | "Temporal clustering" | Note potential seasonality |
| >30% from single author | "Author concentration" | Note in findings |

---

## Data Collection Best Practices

### Rate Limit Management

1. **GitHub API**: Use `gh` CLI with authentication for 5,000 requests/hour
2. **Batch Requests**: Use `--paginate` flag for large datasets
3. **Cache Responses**: Note timestamps for data freshness

### Handling Missing Data

When data is unavailable:
1. Record as `"value": "NOT_AVAILABLE"`
2. Include `"reason"`: why data couldn't be obtained
3. Try alternative sources before marking unavailable

### Source Validation

1. **Verify URLs**: Ensure sources are accessible
2. **Check Freshness**: Record retrieval timestamps
3. **Cross-Reference**: Use multiple sources for critical metrics
4. **Note Discrepancies**: Flag when sources disagree

### JSON Output Format

Each data point should include:

```json
{
  "metric": "contributor_count",
  "value": 47,
  "unit": "contributors",
  "source": "https://api.github.com/repos/owner/repo/contributors",
  "retrieved_at": "2024-01-15T10:30:00Z",
  "notes": "Active in past 12 months"
}
```

For unavailable data:

```json
{
  "metric": "test_coverage",
  "value": "NOT_AVAILABLE",
  "reason": "No coverage badge or codecov integration found",
  "sources_checked": [
    "https://codecov.io/gh/owner/repo",
    "README.md badges",
    "CI workflow files"
  ],
  "retrieved_at": "2024-01-15T10:30:00Z"
}
```
