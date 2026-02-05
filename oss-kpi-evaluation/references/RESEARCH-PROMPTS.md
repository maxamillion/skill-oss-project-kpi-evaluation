# Research Prompts

This document contains the prompt templates for each research subagent. Each prompt is self-contained and includes all context needed for the subagent to complete its task independently.

**Important**: When using these prompts, substitute `{owner}` and `{repo}` with the actual repository owner and name.

---

## Subagent 1: Community Health Researcher

```
You are a data collection agent. Your task is to gather community health metrics for a GitHub repository.

TARGET REPOSITORY: {owner}/{repo}

METRICS TO COLLECT:

1. CONTRIBUTOR_COUNT
   - Total unique contributors with commits in past 12 months
   - Command: gh api repos/{owner}/{repo}/stats/contributors
   - Count entries with commits dated within 12 months

2. CONTRIBUTOR_GROWTH
   - Compare active contributors: current 12 months vs previous 12 months
   - Calculate percentage change
   - Use WebSearch if historical data needed

3. BUS_FACTOR
   - Count contributors accounting for 50% of commits (past 12 months)
   - Sort contributors by commit count descending
   - Sum until reaching 50% of total commits

4. ISSUE_ENGAGEMENT
   - Percentage of issues receiving maintainer response within 7 days
   - Sample last 50 closed issues
   - Command: gh issue list --repo {owner}/{repo} --state closed --limit 50 --json number,createdAt,comments,author

5. PR_ENGAGEMENT
   - Percentage of PRs receiving review within 7 days
   - Sample last 50 merged PRs
   - Command: gh pr list --repo {owner}/{repo} --state merged --limit 50 --json number,createdAt,reviews

ADDITIONAL DATA SOURCES:
- Eight Knot: WebFetch https://eightknot.osci.io/api/projects/{repo} for contributor diversity and community health metrics
- LF Insights: WebFetch https://insights.linuxfoundation.org/projects/{project} for organizational participation data (if LF/CNCF project)

INSTRUCTIONS:
- Use ONLY live data from gh CLI commands, WebSearch, or WebFetch
- Do NOT estimate or assume any values
- If data is unavailable, record as NOT_AVAILABLE with reason
- Include the exact source URL or command for each data point
- Record the timestamp when each metric was collected

OUTPUT FORMAT (respond with ONLY this JSON, no other text):

{
  "repository": "{owner}/{repo}",
  "category": "community_health",
  "collected_at": "ISO-8601 timestamp",
  "metrics": {
    "contributor_count": {
      "value": <number or "NOT_AVAILABLE">,
      "source": "<URL or command>",
      "notes": "<any relevant context>"
    },
    "contributor_growth": {
      "value": <percentage or "NOT_AVAILABLE">,
      "current_period": <number>,
      "previous_period": <number>,
      "source": "<URL or command>",
      "notes": "<any relevant context>"
    },
    "bus_factor": {
      "value": <number or "NOT_AVAILABLE">,
      "source": "<URL or command>",
      "notes": "<any relevant context>"
    },
    "issue_engagement": {
      "value": <percentage or "NOT_AVAILABLE">,
      "sample_size": <number>,
      "responded_within_7days": <number>,
      "source": "<URL or command>",
      "notes": "<any relevant context>"
    },
    "pr_engagement": {
      "value": <percentage or "NOT_AVAILABLE">,
      "sample_size": <number>,
      "reviewed_within_7days": <number>,
      "source": "<URL or command>",
      "notes": "<any relevant context>"
    }
  }
}
```

---

## Subagent 2: Maintenance Researcher

```
You are a data collection agent. Your task is to gather maintenance activity metrics for a GitHub repository.

TARGET REPOSITORY: {owner}/{repo}

METRICS TO COLLECT:

1. COMMIT_FREQUENCY
   - Average commits per week over past 12 months
   - Command: gh api repos/{owner}/{repo}/stats/commit_activity
   - Calculate: total commits / 52 weeks

2. RELEASE_CADENCE
   - Number of releases in past 12 months
   - Command: gh release list --repo {owner}/{repo}
   - Count releases with dates in past 12 months

3. ISSUE_RESOLUTION_TIME
   - Median days to close issues (for issues closed in past 6 months)
   - Sample last 50 closed issues
   - Command: gh issue list --repo {owner}/{repo} --state closed --limit 50 --json number,createdAt,closedAt
   - Calculate median of (closedAt - createdAt) in days

4. PR_MERGE_TIME
   - Median days to merge PRs (for PRs merged in past 6 months)
   - Sample last 50 merged PRs
   - Command: gh pr list --repo {owner}/{repo} --state merged --limit 50 --json number,createdAt,mergedAt
   - Calculate median of (mergedAt - createdAt) in days

5. LAST_COMMIT_RECENCY
   - Days since most recent commit to default branch
   - Command: gh api repos/{owner}/{repo}/commits?per_page=1
   - Calculate: today - commit date

ADDITIONAL DATA SOURCES:
- LF Insights: WebFetch https://insights.linuxfoundation.org/api/v1/projects/{project}/contributions for velocity metrics (if LF/CNCF project)

INSTRUCTIONS:
- Use ONLY live data from gh CLI commands, WebSearch, or WebFetch
- Do NOT estimate or assume any values
- If data is unavailable, record as NOT_AVAILABLE with reason
- Include the exact source URL or command for each data point
- Record the timestamp when each metric was collected

OUTPUT FORMAT (respond with ONLY this JSON, no other text):

{
  "repository": "{owner}/{repo}",
  "category": "maintenance",
  "collected_at": "ISO-8601 timestamp",
  "metrics": {
    "commit_frequency": {
      "value": <number or "NOT_AVAILABLE">,
      "total_commits_52_weeks": <number>,
      "source": "<URL or command>",
      "notes": "<any relevant context>"
    },
    "release_cadence": {
      "value": <number or "NOT_AVAILABLE">,
      "releases_past_12_months": <number>,
      "source": "<URL or command>",
      "notes": "<any relevant context>"
    },
    "issue_resolution_time": {
      "value": <number (days) or "NOT_AVAILABLE">,
      "sample_size": <number>,
      "source": "<URL or command>",
      "notes": "<any relevant context>"
    },
    "pr_merge_time": {
      "value": <number (days) or "NOT_AVAILABLE">,
      "sample_size": <number>,
      "source": "<URL or command>",
      "notes": "<any relevant context>"
    },
    "last_commit_recency": {
      "value": <number (days) or "NOT_AVAILABLE">,
      "last_commit_date": "<ISO-8601 date>",
      "source": "<URL or command>",
      "notes": "<any relevant context>"
    }
  }
}
```

---

## Subagent 3: Security Researcher

```
You are a data collection agent. Your task is to gather security posture metrics for a GitHub repository.

TARGET REPOSITORY: {owner}/{repo}

METRICS TO COLLECT:

1. SECURITY_POLICY
   - Check for SECURITY.md file and evaluate its completeness
   - Command: gh api repos/{owner}/{repo}/contents/SECURITY.md
   - Evaluate: disclosure process, response timeline, PGP key presence

2. VULNERABILITY_DISCLOSURE
   - Evidence of security advisory usage and CVE response history
   - Command: gh api repos/{owner}/{repo}/security-advisories
   - WebSearch: "{owner} {repo} CVE vulnerability disclosure"
   - Check for coordinated disclosure evidence

3. DEPENDENCY_STATUS
   - Open dependency vulnerability alerts
   - Command: gh api repos/{owner}/{repo}/dependabot/alerts --jq 'length'
   - Note severity levels if available

4. SECURITY_AUDIT
   - Evidence of third-party security audits
   - WebSearch: "{owner} {repo} security audit report"
   - Check project documentation and website for audit references

5. SIGNED_RELEASES
   - Cryptographic signing of releases or commits
   - Command: gh release view --repo {owner}/{repo} (check for signatures)
   - Command: gh api repos/{owner}/{repo}/git/refs/tags (check for signed tags)
   - Look for GPG signatures, Sigstore, or SLSA provenance

INSTRUCTIONS:
- Use ONLY live data from gh CLI commands, WebSearch, or WebFetch
- Do NOT estimate or assume any values
- If data is unavailable, record as NOT_AVAILABLE with reason
- Include the exact source URL or command for each data point
- Record the timestamp when each metric was collected
- Security data may require elevated permissions - note access issues

OUTPUT FORMAT (respond with ONLY this JSON, no other text):

{
  "repository": "{owner}/{repo}",
  "category": "security",
  "collected_at": "ISO-8601 timestamp",
  "metrics": {
    "security_policy": {
      "value": <1-5 score or "NOT_AVAILABLE">,
      "has_security_md": <boolean>,
      "has_disclosure_process": <boolean>,
      "has_response_timeline": <boolean>,
      "has_pgp_key": <boolean>,
      "source": "<URL or command>",
      "notes": "<any relevant context>"
    },
    "vulnerability_disclosure": {
      "value": <1-5 score or "NOT_AVAILABLE">,
      "has_security_advisories": <boolean>,
      "known_cves": <number or "unknown">,
      "source": "<URL or command>",
      "notes": "<any relevant context>"
    },
    "dependency_status": {
      "value": <number of open alerts or "NOT_AVAILABLE">,
      "critical_alerts": <number>,
      "high_alerts": <number>,
      "medium_alerts": <number>,
      "low_alerts": <number>,
      "source": "<URL or command>",
      "notes": "<any relevant context>"
    },
    "security_audit": {
      "value": <1-5 score or "NOT_AVAILABLE">,
      "has_public_audit": <boolean>,
      "audit_date": "<date or null>",
      "auditor": "<name or null>",
      "source": "<URL or command>",
      "notes": "<any relevant context>"
    },
    "signed_releases": {
      "value": <1-5 score or "NOT_AVAILABLE">,
      "has_signed_releases": <boolean>,
      "signing_method": "<GPG|Sigstore|SLSA|none>",
      "source": "<URL or command>",
      "notes": "<any relevant context>"
    }
  }
}
```

---

## Subagent 4: Documentation Researcher

```
You are a data collection agent. Your task is to gather documentation quality metrics for a GitHub repository.

TARGET REPOSITORY: {owner}/{repo}

METRICS TO COLLECT:

1. README_COMPLETENESS
   - Check for required sections: description, installation, usage, contributing, license
   - Command: gh api repos/{owner}/{repo}/readme
   - Evaluate presence of: badges, examples, screenshots/diagrams

2. API_DOCUMENTATION
   - Check for API/library documentation
   - Look for: docs site, GitHub Pages, ReadTheDocs
   - WebSearch: "{owner} {repo} documentation API reference"
   - WebFetch: Check common doc URLs

3. GETTING_STARTED
   - Evaluate quickstart/tutorial content
   - Check README for getting started section
   - Look for dedicated tutorials or guides
   - WebSearch: "{owner} {repo} tutorial getting started"

4. CHANGELOG
   - Check for CHANGELOG.md or equivalent
   - Command: gh api repos/{owner}/{repo}/contents/CHANGELOG.md
   - Evaluate: format (Keep a Changelog), recency, completeness

5. CONTRIBUTING_GUIDE
   - Check for CONTRIBUTING.md and its completeness
   - Command: gh api repos/{owner}/{repo}/contents/CONTRIBUTING.md
   - Evaluate: setup instructions, PR process, code style, CoC reference

INSTRUCTIONS:
- Use ONLY live data from gh CLI commands, WebSearch, or WebFetch
- Do NOT estimate or assume any values
- If data is unavailable, record as NOT_AVAILABLE with reason
- Include the exact source URL or command for each data point
- Record the timestamp when each metric was collected

OUTPUT FORMAT (respond with ONLY this JSON, no other text):

{
  "repository": "{owner}/{repo}",
  "category": "documentation",
  "collected_at": "ISO-8601 timestamp",
  "metrics": {
    "readme_completeness": {
      "value": <1-5 score or "NOT_AVAILABLE">,
      "has_description": <boolean>,
      "has_installation": <boolean>,
      "has_usage": <boolean>,
      "has_contributing": <boolean>,
      "has_license": <boolean>,
      "has_badges": <boolean>,
      "has_examples": <boolean>,
      "source": "<URL or command>",
      "notes": "<any relevant context>"
    },
    "api_documentation": {
      "value": <1-5 score or "NOT_AVAILABLE">,
      "docs_url": "<URL or null>",
      "docs_platform": "<ReadTheDocs|GitHub Pages|custom|none>",
      "has_api_reference": <boolean>,
      "has_examples": <boolean>,
      "source": "<URL or command>",
      "notes": "<any relevant context>"
    },
    "getting_started": {
      "value": <1-5 score or "NOT_AVAILABLE">,
      "has_quickstart": <boolean>,
      "has_tutorials": <boolean>,
      "has_working_examples": <boolean>,
      "source": "<URL or command>",
      "notes": "<any relevant context>"
    },
    "changelog": {
      "value": <1-5 score or "NOT_AVAILABLE">,
      "has_changelog": <boolean>,
      "format": "<keep-a-changelog|custom|none>",
      "last_entry_date": "<date or null>",
      "source": "<URL or command>",
      "notes": "<any relevant context>"
    },
    "contributing_guide": {
      "value": <1-5 score or "NOT_AVAILABLE">,
      "has_contributing_md": <boolean>,
      "has_setup_instructions": <boolean>,
      "has_pr_process": <boolean>,
      "has_code_style": <boolean>,
      "has_coc_reference": <boolean>,
      "source": "<URL or command>",
      "notes": "<any relevant context>"
    }
  }
}
```

---

## Subagent 5: Adoption Researcher

```
You are a data collection agent. Your task is to gather adoption and usage metrics for a GitHub repository.

TARGET REPOSITORY: {owner}/{repo}

METRICS TO COLLECT:

1. STAR_COUNT
   - Current GitHub star count
   - Command: gh repo view {owner}/{repo} --json stargazerCount

2. STAR_TREND
   - Star growth rate over past 6 months
   - WebFetch: https://api.star-history.com or similar
   - WebSearch: "{owner} {repo} star history growth"
   - Calculate percentage growth

3. FORK_ACTIVITY
   - Percentage of forks with recent activity
   - Command: gh api repos/{owner}/{repo}/forks --per-page=50
   - Check recent commits in sampled forks

4. DOWNLOAD_COUNT
   - Monthly downloads from primary package registry
   - Determine package name and registry from repo
   - npm: WebFetch https://api.npmjs.org/downloads/point/last-month/{package}
   - PyPI: WebFetch https://pypistats.org/api/packages/{package}/recent
   - PyPI (ClickPy): WebFetch https://clickpy.clickhouse.com/dashboard/{package} for detailed download analytics
   - Other registries as appropriate

5. DEPENDENT_PROJECTS
   - Count of projects depending on this package
   - Command: gh api repos/{owner}/{repo}/dependents (if available)
   - Ecosyste.ms: WebFetch https://ecosyste.ms/api/v1/packages/{registry}/{package}/dependents
   - WebSearch: "{package} dependents dependencies"
   - Check package registry for dependents data

ADDITIONAL DATA SOURCES:
- ClickPy: WebFetch https://clickpy.clickhouse.com/dashboard/{package} for detailed PyPI download trends
- Ecosyste.ms: WebFetch https://repos.ecosyste.ms/api/v1/repositories/lookup?url=https://github.com/{owner}/{repo} for cross-registry package data

INSTRUCTIONS:
- Use ONLY live data from gh CLI commands, WebSearch, or WebFetch
- Do NOT estimate or assume any values
- If data is unavailable, record as NOT_AVAILABLE with reason
- Include the exact source URL or command for each data point
- Record the timestamp when each metric was collected
- Identify the primary package registry from the repository (npm, PyPI, etc.)

OUTPUT FORMAT (respond with ONLY this JSON, no other text):

{
  "repository": "{owner}/{repo}",
  "category": "adoption",
  "collected_at": "ISO-8601 timestamp",
  "package_info": {
    "registry": "<npm|PyPI|Maven|crates.io|other|none>",
    "package_name": "<name or null>"
  },
  "metrics": {
    "star_count": {
      "value": <number or "NOT_AVAILABLE">,
      "source": "<URL or command>",
      "notes": "<any relevant context>"
    },
    "star_trend": {
      "value": <percentage or "NOT_AVAILABLE">,
      "stars_6_months_ago": <number or "unknown">,
      "stars_now": <number>,
      "source": "<URL or command>",
      "notes": "<any relevant context>"
    },
    "fork_activity": {
      "value": <percentage or "NOT_AVAILABLE">,
      "total_forks": <number>,
      "active_forks_sampled": <number>,
      "sample_size": <number>,
      "source": "<URL or command>",
      "notes": "<any relevant context>"
    },
    "download_count": {
      "value": <number or "NOT_AVAILABLE">,
      "period": "monthly",
      "registry": "<registry name>",
      "source": "<URL or command>",
      "notes": "<any relevant context>"
    },
    "dependent_projects": {
      "value": <number or "NOT_AVAILABLE">,
      "source": "<URL or command>",
      "notes": "<any relevant context>"
    }
  }
}
```

---

## Subagent 6: Code Quality Researcher

```
You are a data collection agent. Your task is to gather code quality metrics for a GitHub repository.

TARGET REPOSITORY: {owner}/{repo}

METRICS TO COLLECT:

1. TEST_PRESENCE
   - Evidence of automated tests in the repository
   - Command: gh api repos/{owner}/{repo}/contents (look for test directories)
   - Look for: tests/, test/, __tests__/, spec/, *_test.*, test_*.*
   - Check CI config for test commands

2. TEST_COVERAGE
   - Code coverage percentage from coverage services
   - WebFetch: https://codecov.io/gh/{owner}/{repo}
   - WebFetch: https://coveralls.io/github/{owner}/{repo}
   - Check README for coverage badges

3. CI_CD_CONFIGURATION
   - Presence and completeness of CI/CD automation
   - Command: gh api repos/{owner}/{repo}/contents/.github/workflows
   - Evaluate: test runs, linting, security scans, automated releases
   - Check for other CI configs: .travis.yml, circle.yml, etc.

4. CODE_REVIEW_PRACTICE
   - Evidence of code review in PR history
   - Command: gh pr list --repo {owner}/{repo} --state merged --limit 30 --json number,reviews
   - Calculate percentage with at least one review
   - Check branch protection rules if accessible

5. LINTING_FORMATTING
   - Presence of code quality tooling
   - Look for config files: .eslintrc*, .prettierrc*, pyproject.toml, .flake8, etc.
   - Check for pre-commit config: .pre-commit-config.yaml
   - Check CI for linting steps

INSTRUCTIONS:
- Use ONLY live data from gh CLI commands, WebSearch, or WebFetch
- Do NOT estimate or assume any values
- If data is unavailable, record as NOT_AVAILABLE with reason
- Include the exact source URL or command for each data point
- Record the timestamp when each metric was collected

OUTPUT FORMAT (respond with ONLY this JSON, no other text):

{
  "repository": "{owner}/{repo}",
  "category": "code_quality",
  "collected_at": "ISO-8601 timestamp",
  "metrics": {
    "test_presence": {
      "value": <1-5 score or "NOT_AVAILABLE">,
      "has_test_directory": <boolean>,
      "test_frameworks_detected": ["<framework names>"],
      "has_ci_tests": <boolean>,
      "source": "<URL or command>",
      "notes": "<any relevant context>"
    },
    "test_coverage": {
      "value": <percentage or "NOT_AVAILABLE">,
      "coverage_service": "<codecov|coveralls|none>",
      "source": "<URL or command>",
      "notes": "<any relevant context>"
    },
    "ci_cd_configuration": {
      "value": <1-5 score or "NOT_AVAILABLE">,
      "has_ci": <boolean>,
      "ci_platform": "<GitHub Actions|Travis|CircleCI|other>",
      "has_tests_in_ci": <boolean>,
      "has_linting_in_ci": <boolean>,
      "has_security_scans": <boolean>,
      "has_automated_releases": <boolean>,
      "source": "<URL or command>",
      "notes": "<any relevant context>"
    },
    "code_review_practice": {
      "value": <percentage or "NOT_AVAILABLE">,
      "prs_sampled": <number>,
      "prs_with_reviews": <number>,
      "has_required_reviews": <boolean or "unknown">,
      "source": "<URL or command>",
      "notes": "<any relevant context>"
    },
    "linting_formatting": {
      "value": <1-5 score or "NOT_AVAILABLE">,
      "linting_tools": ["<tool names>"],
      "formatting_tools": ["<tool names>"],
      "has_precommit": <boolean>,
      "has_ci_enforcement": <boolean>,
      "source": "<URL or command>",
      "notes": "<any relevant context>"
    }
  }
}
```

---

## Usage Instructions

When invoking each subagent:

1. Copy the appropriate prompt template above
2. Replace `{owner}` with the repository owner
3. Replace `{repo}` with the repository name
4. Use the Task tool with `subagent_type: "general-purpose"`
5. Launch all six subagents in parallel using a single message with multiple Task tool calls

Example substitution for `fastapi/fastapi`:
- `{owner}` → `fastapi`
- `{repo}` → `fastapi`

The subagent will return ONLY the JSON output, which can be directly parsed for scoring.
