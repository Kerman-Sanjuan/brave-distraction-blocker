---
name: pc-userstory
description: Parse GitHub Issue URL and create OpenSpec change. Use when user provides a GitHub Issue URL.
license: MIT
compatibility: Requires openspec CLI and gh CLI.
metadata:
  author: copilots
  version: "1.1"
---

Turn a GitHub Issue URL into an OpenSpec change, then hand the change to `pc-plan-propose`.

## Rules

- Issue data comes from `gh`, never from a page fetch (denied by `pc-system-reminders`). An unavailable or unauthenticated `gh` is a blocker to report: `gh auth status` says which.
- Always pass `--repo {owner}/{repo}`. Git context resolves to the wrong repository in a fork or a worktree, and the failure looks like a missing issue.
- Never load `pc-plan-apply` until the user has said yes.

## Contracts

`https://github.com/{owner}/{repo}/issues/42` gives owner, repo and number `42`.

```bash
gh issue view 42 --repo {owner}/{repo} --json number,title,body,labels,milestone,state
```

An auth error or a 404 here is the blocker; do not work around it. From the JSON take `number`, `title`, `body` (description and acceptance criteria), `labels`, `milestone` and `state`.

```bash
openspec new change "gh-{number}-{slug}"
```

Write the issue number to `.openspec.yaml` so that `pc-plan-propose` reuses the existing GitHub issue as the proposal issue instead of creating a new one. The file lives at `openspec/changes/gh-{number}-{slug}/.openspec.yaml`:

```yaml
schema: spec-driven
created: {YYYY-MM-DD}
github:
  issue: {number}
```

This step must complete before `pc-plan-propose` runs, because `pc-plan-propose` calls `openspec-propose`, which checks `.openspec.yaml` for an existing issue number. If it finds one, it skips issue creation and uses the existing issue.

The existing issue body will be updated with the proposal content and task list when `pc-plan-propose` writes the artifacts and syncs to GitHub.

Screenshots live in the change folder, `openspec/changes/{change-name}/images/{name}.png`, and embed as a blob URL pinned to a commit SHA with `?raw=true`: `https://github.com/{owner}/{repo}/blob/{sha}/openspec/changes/{change}/images/{file}.png?raw=true`.

Report:

```
## Issue Parsed

Issue: #{number}
Title: {title}
State: {state}
Milestone: {milestone}

Change Created: gh-{number}-{slug}

The existing issue #{number} will be used as the proposal issue. No new issue will be created.
```

Then load `pc-plan-propose` (interactive) and, once it returns, ask:

```json
{
  "questions": [
    {
      "header": "Ready to implement",
      "question": "Ready to implement?",
      "options": [
        { "label": "yes", "description": "Load the pc-plan-apply skill to start implementation." },
        { "label": "no", "description": "Stop here. You can run /plan-apply later." }
      ]
    }
  ]
}
```
