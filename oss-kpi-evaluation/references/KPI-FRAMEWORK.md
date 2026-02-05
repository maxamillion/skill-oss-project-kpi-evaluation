# KPI Framework

This document defines the Key Performance Indicators (KPIs) used to evaluate open source projects. Each metric includes its definition, data sources, measurement method, and scoring rubric.

## Scoring Scale

All metrics are scored on a 1-5 scale:

| Score | Rating | Description |
|-------|--------|-------------|
| 5 | Excellent | Exceeds best practices, exemplary |
| 4 | Good | Meets best practices, healthy |
| 3 | Adequate | Acceptable, room for improvement |
| 2 | Concerning | Below expectations, risks present |
| 1 | Critical | Significant issues, high risk |

---

## Category 1: Community Health

Measures the contributor community's size, diversity, engagement, and sustainability.

### 1.1 Contributor Count

**Definition**: Total number of unique contributors with merged commits in the past 12 months.

**Data Sources**:
- GitHub API: `gh api repos/{owner}/{repo}/contributors`
- GitHub Insights page

**Measurement**: Count unique contributor accounts with commits in past 12 months.

**Scoring Rubric**:
| Score | Threshold |
|-------|-----------|
| 5 | ≥100 contributors |
| 4 | 50-99 contributors |
| 3 | 20-49 contributors |
| 2 | 5-19 contributors |
| 1 | <5 contributors |

### 1.2 Contributor Growth Rate

**Definition**: Percentage change in active contributors compared to previous 12-month period.

**Data Sources**:
- GitHub API contributor history
- WebSearch for historical data

**Measurement**: (Current period contributors - Previous period) / Previous period × 100

**Scoring Rubric**:
| Score | Threshold |
|-------|-----------|
| 5 | ≥25% growth |
| 4 | 10-24% growth |
| 3 | 0-9% growth |
| 2 | -10% to -1% (slight decline) |
| 1 | <-10% decline |

### 1.3 Bus Factor

**Definition**: Minimum number of contributors who account for 50% of commits in past 12 months.

**Data Sources**:
- GitHub API contributor statistics
- `gh api repos/{owner}/{repo}/stats/contributors`

**Measurement**: Count contributors (sorted by commits descending) until cumulative commits reach 50%.

**Scoring Rubric**:
| Score | Threshold |
|-------|-----------|
| 5 | ≥5 people |
| 4 | 4 people |
| 3 | 3 people |
| 2 | 2 people |
| 1 | 1 person |

### 1.4 Issue Engagement

**Definition**: Percentage of issues receiving maintainer response within 7 days.

**Data Sources**:
- GitHub API issues endpoint
- `gh issue list` with filters

**Measurement**: (Issues with maintainer response ≤7 days) / (Total issues opened) × 100

**Scoring Rubric**:
| Score | Threshold |
|-------|-----------|
| 5 | ≥90% response rate |
| 4 | 75-89% response rate |
| 3 | 50-74% response rate |
| 2 | 25-49% response rate |
| 1 | <25% response rate |

### 1.5 PR Engagement

**Definition**: Percentage of PRs receiving review within 7 days.

**Data Sources**:
- GitHub API pull requests endpoint
- `gh pr list` with filters

**Measurement**: (PRs with review ≤7 days) / (Total PRs opened) × 100

**Scoring Rubric**:
| Score | Threshold |
|-------|-----------|
| 5 | ≥90% review rate |
| 4 | 75-89% review rate |
| 3 | 50-74% review rate |
| 2 | 25-49% review rate |
| 1 | <25% review rate |

---

## Category 2: Maintenance

Measures ongoing project maintenance activity and responsiveness.

### 2.1 Commit Frequency

**Definition**: Average commits per week over the past 12 months.

**Data Sources**:
- GitHub API commit statistics
- `gh api repos/{owner}/{repo}/stats/commit_activity`

**Measurement**: Total commits in 52 weeks / 52

**Scoring Rubric**:
| Score | Threshold |
|-------|-----------|
| 5 | ≥20 commits/week |
| 4 | 10-19 commits/week |
| 3 | 3-9 commits/week |
| 2 | 1-2 commits/week |
| 1 | <1 commit/week |

### 2.2 Release Cadence

**Definition**: Number of releases in the past 12 months.

**Data Sources**:
- GitHub Releases API
- `gh release list`

**Measurement**: Count releases with published date in past 12 months.

**Scoring Rubric**:
| Score | Threshold |
|-------|-----------|
| 5 | ≥12 releases (monthly+) |
| 4 | 6-11 releases |
| 3 | 3-5 releases |
| 2 | 1-2 releases |
| 1 | 0 releases |

### 2.3 Issue Resolution Time

**Definition**: Median time to close issues (in days) for issues closed in past 6 months.

**Data Sources**:
- GitHub API issues endpoint
- `gh issue list --state closed`

**Measurement**: Median of (closed_at - created_at) for closed issues.

**Scoring Rubric**:
| Score | Threshold |
|-------|-----------|
| 5 | ≤7 days median |
| 4 | 8-14 days median |
| 3 | 15-30 days median |
| 2 | 31-90 days median |
| 1 | >90 days median |

### 2.4 PR Merge Time

**Definition**: Median time to merge PRs (in days) for PRs merged in past 6 months.

**Data Sources**:
- GitHub API pull requests endpoint
- `gh pr list --state merged`

**Measurement**: Median of (merged_at - created_at) for merged PRs.

**Scoring Rubric**:
| Score | Threshold |
|-------|-----------|
| 5 | ≤3 days median |
| 4 | 4-7 days median |
| 3 | 8-14 days median |
| 2 | 15-30 days median |
| 1 | >30 days median |

### 2.5 Last Commit Recency

**Definition**: Days since the most recent commit to the default branch.

**Data Sources**:
- GitHub API commits endpoint
- `gh api repos/{owner}/{repo}/commits`

**Measurement**: Current date - most recent commit date.

**Scoring Rubric**:
| Score | Threshold |
|-------|-----------|
| 5 | ≤7 days ago |
| 4 | 8-30 days ago |
| 3 | 31-90 days ago |
| 2 | 91-180 days ago |
| 1 | >180 days ago |

---

## Category 3: Security

Measures security posture, practices, and vulnerability management.

### 3.1 Security Policy

**Definition**: Presence and completeness of SECURITY.md file.

**Data Sources**:
- GitHub repository file check
- `gh api repos/{owner}/{repo}/contents/SECURITY.md`

**Measurement**: Check for file presence and content quality.

**Scoring Rubric**:
| Score | Criteria |
|-------|----------|
| 5 | Comprehensive policy with disclosure process, SLA, and PGP key |
| 4 | Policy with disclosure process and response timeline |
| 3 | Basic SECURITY.md with reporting instructions |
| 2 | Security info in README only |
| 1 | No security documentation |

### 3.2 Vulnerability Disclosure

**Definition**: Evidence of responsible vulnerability handling.

**Data Sources**:
- GitHub Security Advisories
- `gh api repos/{owner}/{repo}/security-advisories`
- WebSearch for CVE history

**Measurement**: Check for security advisory usage and CVE response history.

**Scoring Rubric**:
| Score | Criteria |
|-------|----------|
| 5 | Active advisory usage, published CVEs with fixes, coordinated disclosure |
| 4 | Security advisories enabled, some historical CVE responses |
| 3 | Ad-hoc security issue handling via issues |
| 2 | Slow or incomplete vulnerability responses |
| 1 | No evidence of security issue handling |

### 3.3 Dependency Status

**Definition**: Status of dependency security based on Dependabot or similar tools.

**Data Sources**:
- GitHub Dependabot alerts
- `gh api repos/{owner}/{repo}/vulnerability-alerts`
- WebSearch for known vulnerable dependencies

**Measurement**: Count open dependency vulnerability alerts.

**Scoring Rubric**:
| Score | Threshold |
|-------|-----------|
| 5 | 0 open alerts, Dependabot enabled |
| 4 | 1-2 low-severity open alerts |
| 3 | 3-5 open alerts or 1 high-severity |
| 2 | 6-10 open alerts or 2+ high-severity |
| 1 | >10 open alerts or critical vulnerabilities |

### 3.4 Security Audit

**Definition**: Evidence of third-party security audits.

**Data Sources**:
- WebSearch for audit reports
- Project documentation
- README or website security page

**Measurement**: Search for published audit reports or security certifications.

**Scoring Rubric**:
| Score | Criteria |
|-------|----------|
| 5 | Recent (<2 years) public audit with issues resolved |
| 4 | Public audit >2 years old or partial audit recent |
| 3 | Internal security review documented |
| 2 | Security practices mentioned but no formal audit |
| 1 | No evidence of security audits |

### 3.5 Signed Releases

**Definition**: Cryptographic signing of releases or commits.

**Data Sources**:
- GitHub Releases API
- `gh release view` for signature info
- GPG signature verification

**Measurement**: Check for signed tags, releases, or commit signing.

**Scoring Rubric**:
| Score | Criteria |
|-------|----------|
| 5 | All releases signed, SLSA provenance, reproducible builds |
| 4 | All releases signed with GPG/Sigstore |
| 3 | Some releases signed or signed commits |
| 2 | Unsigned releases but checksums provided |
| 1 | No signing or integrity verification |

---

## Category 4: Documentation

Measures documentation quality, completeness, and maintenance.

### 4.1 README Completeness

**Definition**: Presence of key README sections.

**Data Sources**:
- GitHub repository README
- `gh api repos/{owner}/{repo}/readme`

**Measurement**: Check for required sections: description, installation, usage, contributing, license.

**Scoring Rubric**:
| Score | Criteria |
|-------|----------|
| 5 | All sections + badges + examples + screenshots/diagrams |
| 4 | All 5 core sections present and detailed |
| 3 | 4 of 5 core sections present |
| 2 | 2-3 core sections present |
| 1 | Minimal or no README |

### 4.2 API Documentation

**Definition**: Presence and quality of API/library documentation.

**Data Sources**:
- Project docs site
- GitHub Pages
- ReadTheDocs/similar platforms
- WebSearch for documentation

**Measurement**: Evaluate documentation presence, coverage, and quality.

**Scoring Rubric**:
| Score | Criteria |
|-------|----------|
| 5 | Comprehensive API docs with search, examples, versioning |
| 4 | Complete API reference with examples |
| 3 | Partial API documentation |
| 2 | Inline code comments only |
| 1 | No API documentation |

### 4.3 Getting Started Guide

**Definition**: Presence of beginner-friendly onboarding documentation.

**Data Sources**:
- README quick start section
- Dedicated getting started page
- Tutorial content

**Measurement**: Evaluate quickstart content quality and completeness.

**Scoring Rubric**:
| Score | Criteria |
|-------|----------|
| 5 | Multiple tutorials, video content, interactive examples |
| 4 | Comprehensive getting started with working examples |
| 3 | Basic getting started section in README |
| 2 | Installation instructions only |
| 1 | No getting started content |

### 4.4 Changelog Maintenance

**Definition**: Presence and maintenance of changelog.

**Data Sources**:
- CHANGELOG.md file
- GitHub Releases notes
- WebSearch for changelog

**Measurement**: Check changelog presence, format, and recency.

**Scoring Rubric**:
| Score | Criteria |
|-------|----------|
| 5 | Keep-a-Changelog format, up to date, all versions documented |
| 4 | Regular changelog updates with recent releases |
| 3 | Changelog exists but inconsistently maintained |
| 2 | Release notes only (no consolidated changelog) |
| 1 | No changelog |

### 4.5 Contributing Guide

**Definition**: Presence and quality of contribution guidelines.

**Data Sources**:
- CONTRIBUTING.md file
- GitHub repository files
- Docs site contribution page

**Measurement**: Evaluate contributing guide presence and completeness.

**Scoring Rubric**:
| Score | Criteria |
|-------|----------|
| 5 | Comprehensive guide: setup, testing, style, review process, CoC |
| 4 | Complete guide with development setup and PR process |
| 3 | Basic CONTRIBUTING.md with PR instructions |
| 2 | Contributing info in README only |
| 1 | No contributing guidelines |

---

## Category 5: Adoption

Measures project adoption, usage trends, and ecosystem impact.

### 5.1 Star Count

**Definition**: Total GitHub stars.

**Data Sources**:
- GitHub API repository endpoint
- `gh repo view --json stargazerCount`

**Measurement**: Current star count.

**Scoring Rubric**:
| Score | Threshold |
|-------|-----------|
| 5 | ≥10,000 stars |
| 4 | 1,000-9,999 stars |
| 3 | 100-999 stars |
| 2 | 10-99 stars |
| 1 | <10 stars |

### 5.2 Star Trend

**Definition**: Star growth rate over past 6 months.

**Data Sources**:
- Star history tools (star-history.com)
- WebSearch for star growth data
- GitHub Archive data

**Measurement**: Percentage growth in stars over 6 months.

**Scoring Rubric**:
| Score | Threshold |
|-------|-----------|
| 5 | ≥50% growth |
| 4 | 20-49% growth |
| 3 | 5-19% growth |
| 2 | 0-4% growth (stagnant) |
| 1 | Negative growth |

### 5.3 Fork Activity

**Definition**: Forks with recent activity (commits in past 6 months).

**Data Sources**:
- GitHub API forks endpoint
- `gh api repos/{owner}/{repo}/forks`

**Measurement**: Count forks with commits in past 6 months / Total forks × 100

**Scoring Rubric**:
| Score | Threshold |
|-------|-----------|
| 5 | ≥20% active forks |
| 4 | 10-19% active forks |
| 3 | 5-9% active forks |
| 2 | 1-4% active forks |
| 1 | <1% active forks |

### 5.4 Download/Install Count

**Definition**: Package downloads from primary package registry.

**Data Sources**:
- npm: npm-stat.com, npmjs.com
- PyPI: pypistats.org
- Maven Central: mvnrepository.com
- Other registries via WebSearch

**Measurement**: Monthly download count from primary registry.

**Scoring Rubric**:
| Score | Threshold |
|-------|-----------|
| 5 | ≥1M monthly downloads |
| 4 | 100K-999K monthly |
| 3 | 10K-99K monthly |
| 2 | 1K-9K monthly |
| 1 | <1K monthly |

### 5.5 Dependent Projects

**Definition**: Count of public projects depending on this package.

**Data Sources**:
- GitHub dependency graph
- `gh api repos/{owner}/{repo}/dependents`
- Package registry dependents data

**Measurement**: Count of dependent repositories or packages.

**Scoring Rubric**:
| Score | Threshold |
|-------|-----------|
| 5 | ≥10,000 dependents |
| 4 | 1,000-9,999 dependents |
| 3 | 100-999 dependents |
| 2 | 10-99 dependents |
| 1 | <10 dependents |

---

## Category 6: Code Quality

Measures code quality practices, testing, and development standards.

### 6.1 Test Presence

**Definition**: Evidence of automated tests in the repository.

**Data Sources**:
- GitHub repository file search
- Test directory presence
- CI configuration analysis

**Measurement**: Check for test directories and test files.

**Scoring Rubric**:
| Score | Criteria |
|-------|----------|
| 5 | Comprehensive test suite: unit, integration, e2e |
| 4 | Good test coverage with multiple test types |
| 3 | Basic test suite present |
| 2 | Minimal tests or examples only |
| 1 | No tests |

### 6.2 Test Coverage

**Definition**: Code coverage percentage reported by coverage tools.

**Data Sources**:
- Codecov/Coveralls badges
- CI logs with coverage reports
- README coverage badges

**Measurement**: Extract coverage percentage from badges or reports.

**Scoring Rubric**:
| Score | Threshold |
|-------|-----------|
| 5 | ≥90% coverage |
| 4 | 70-89% coverage |
| 3 | 50-69% coverage |
| 2 | 20-49% coverage |
| 1 | <20% or unknown |

### 6.3 CI/CD Configuration

**Definition**: Presence and completeness of CI/CD automation.

**Data Sources**:
- GitHub Actions workflows
- `.github/workflows/` directory
- Other CI config files (travis.yml, circle.yml)

**Measurement**: Evaluate CI/CD presence and coverage.

**Scoring Rubric**:
| Score | Criteria |
|-------|----------|
| 5 | Full CI/CD: tests, linting, security scans, automated releases |
| 4 | CI with tests, linting, and build verification |
| 3 | Basic CI with test runs |
| 2 | CI present but minimal checks |
| 1 | No CI/CD |

### 6.4 Code Review Practice

**Definition**: Evidence of code review in PR merge history.

**Data Sources**:
- GitHub PR history
- `gh pr list --state merged`
- PR review requirements in branch protection

**Measurement**: Percentage of merged PRs with at least one review.

**Scoring Rubric**:
| Score | Threshold |
|-------|-----------|
| 5 | ≥95% PRs reviewed, required reviews enforced |
| 4 | ≥80% PRs reviewed |
| 3 | ≥50% PRs reviewed |
| 2 | <50% PRs reviewed |
| 1 | No code review practice |

### 6.5 Linting and Formatting

**Definition**: Presence of code quality tooling configuration.

**Data Sources**:
- Config files: .eslintrc, .prettierrc, pyproject.toml, etc.
- Pre-commit hooks configuration
- CI linting steps

**Measurement**: Check for linting/formatting tool configuration.

**Scoring Rubric**:
| Score | Criteria |
|-------|----------|
| 5 | Linting + formatting + pre-commit hooks + CI enforcement |
| 4 | Linting and formatting with CI enforcement |
| 3 | Linting configuration present |
| 2 | EditorConfig or basic formatting only |
| 1 | No linting or formatting configuration |

---

## Calculating Scores

### Metric Score

Each metric receives a score of 1-5 based on the rubric above.

### Category Score

Category score is the arithmetic mean of all metric scores within that category:

```
Category Score = Sum(Metric Scores) / Count(Metrics)
```

If a metric has `NOT_AVAILABLE` data, it is excluded from the calculation (not counted as 0).

### Overall Score

Overall score is the weighted average of category scores:

```
Overall Score = Sum(Category Score × Weight) / Sum(Weights)
```

Default weights: All categories weighted equally at 16.67% (100% / 6).

### Score Interpretation

| Overall Score | Interpretation |
|---------------|----------------|
| 4.5-5.0 | Excellent - Low risk, high quality project |
| 3.5-4.4 | Good - Healthy project with minor areas for improvement |
| 2.5-3.4 | Adequate - Acceptable but review identified areas carefully |
| 1.5-2.4 | Concerning - Significant risks, proceed with caution |
| 1.0-1.4 | Critical - Major issues, high risk adoption |
