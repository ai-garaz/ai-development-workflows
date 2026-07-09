# AI Code Review — Project Configuration

Read at review time by `.github/workflows/claude-code-review.yml` (CI) and by
local pre-push reviews (`/ai-code-review` skill). This file holds
**repo-specific facts only** — the generic review doctrine (tiers, validation,
severities) lives in the workflow prompt and the skill. Keep this file current
when conventions change; the review reads it fresh on every run.

## Stack

Markdown knowledge repository — structured prompts, coding rules, and workflow
documentation for AI-assisted development. No application code. Nearly all PRs
touch only prose and should classify as the docs-only review tier.

## Coding rules

- Location: none — the rule files under `rules-for-agents/` are **content**
  (rules for consuming projects' Angular/Fastify code), not rules governing
  contributions to this repository. Do not apply them to diffs here.

## PRD convention

None.

## Diff noise — exclude from review and from tier line-counts

- `package-lock.json`

## Project-specific: do NOT flag

_(none yet — add entries here as false-positive patterns emerge, quoting the
pattern precisely so reviewers can match it)_

## Project-specific: extra attention

- Contradictions between a changed rule/prompt file and the README's or
  CLAUDE.md's description of it — this repo's product is its prose, so internal
  consistency is the primary quality bar.

## Doc sync

- Status: **disabled**
- Severity: **capped** (🔵/🟠 only)
- Doc-sync false positives (feed the pattern back here, quoted precisely):
  _(none yet)_

## Skipping a review (break-glass)

A repo maintainer comments `break-glass: <reason>` on the PR, or applies the
`skip-ai-review` label. The workflow posts an acknowledgement comment and
skips the review. This is tracked and visible — use it for emergency
hotfixes, not convenience.
