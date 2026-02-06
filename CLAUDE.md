# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Overview

This is a **Claude Code skill** (not a traditional application). It evaluates open source GitHub projects against 30 Key Performance Indicators (+ 5 conditional RH Engagement) across 6 categories (+ 1 conditional): Community Health, Maintenance, Security, Documentation, Adoption, Code Quality, and optionally Red Hat Engagement.

The skill uses a **multi-agent architecture** with 6 independent research subagents (+ 1 conditional) that run in parallel to prevent bias contamination.

## Repository Structure

```
oss-kpi-evaluation/
├── SKILL.md              # Main skill definition (entry point)
├── assets/
│   └── scoring-rubric.json   # Machine-readable scoring thresholds
└── references/
    ├── KPI-FRAMEWORK.md      # Complete metric definitions
    ├── DATA-SOURCES.md       # API endpoints and data collection methods
    ├── RESEARCH-PROMPTS.md   # Subagent prompt templates (use verbatim)
    ├── BIAS-PREVENTION.md    # Quality control protocols
    └── REPORT-TEMPLATE.md    # Output format specification
```

## Architecture

### Evaluation Workflow

1. **Input Validation**: Validate GitHub repository via `gh repo view`
2. **Parallel Research Dispatch**: Launch 6 subagents simultaneously using Task tool
3. **Result Collection**: Parse JSON from each subagent
4. **Cross-Validation**: Compare overlapping metrics, flag >10% discrepancies
5. **Mechanical Scoring**: Apply `assets/scoring-rubric.json` thresholds
6. **Report Generation**: Use `references/REPORT-TEMPLATE.md` format

### Subagent Isolation

Each subagent receives ONLY:
- Repository identifier (`{owner}/{repo}`)
- Metrics to collect (from `RESEARCH-PROMPTS.md`)
- No opinions, no context, no other subagent results

This isolation is intentional for bias prevention. Never share context between subagents.

### Subagents

| Subagent | Metrics | Primary Tools |
|----------|---------|---------------|
| kpi-community-researcher | Contributors, bus factor, engagement | gh CLI, WebSearch |
| kpi-maintenance-researcher | Commits, releases, issue/PR time | gh CLI |
| kpi-security-researcher | SECURITY.md, CVEs, dependencies | gh CLI, WebSearch |
| kpi-documentation-researcher | README, API docs, changelog | gh CLI, WebSearch, Read |
| kpi-adoption-researcher | Stars, downloads, dependents | gh CLI, WebFetch |
| kpi-codequality-researcher | Tests, CI/CD, code review | gh CLI, WebFetch |
| kpi-rh-engagement-researcher | RH contributions, maintainership, governance | ldapsearch, gh CLI, git log *(conditional)* |

**Note**: The `kpi-rh-engagement-researcher` is conditional — only dispatched when Red Hat Engagement analysis is explicitly requested. It requires LDAP access to `ldap.corp.redhat.com` (scoped to AI Engineering org under `shuels`) for full employee identification. Falls back to email-based identification if LDAP is unavailable.

## Key Constraints

- **GitHub only**: Projects must be hosted on GitHub
- **Single project**: One evaluation per invocation
- **Live data only**: Never cache or estimate values
- **Evidence required**: Every metric needs source URL/command
- **NOT_AVAILABLE**: Missing data flagged explicitly, never inferred
- **Neutral language**: Facts without subjective adjectives

## Allowed Tools

From `SKILL.md`: `WebSearch`, `WebFetch`, `Read`, `Glob`, `Grep`, `Bash(gh:*)`, `Task`

Pre-approved domains in `.claude/settings.local.json`: codecov.io, pypi.org, clickpy.clickhouse.com, ecosyste.ms, libraries.io, star-history.com, eightknot.osci.io, insights.linuxfoundation.org

## Scoring

- All metrics scored 1-5
- Category score = average of available metric scores
- Overall score = weighted average of categories (default: equal 16.67% weights)
- Scores calculated mechanically from `assets/scoring-rubric.json` thresholds

## Report Output

Markdown format with sections:
1. Executive Summary (overall score, top 3 strengths/concerns)
2. Category Scores table
3. Detailed Findings (per-category metrics with evidence tables)
4. Cross-Validation Results
5. Data Quality Assessment
6. Sources list with timestamps
