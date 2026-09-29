# AGENTS.md

<!-- PC-NOT-INITIALIZED -->

# Agent operating guide

The operating contract for AI agents in this repository. Agent-agnostic: OpenCode, Claude Code, Codex, Gemini and others.

## Session context

Before a non-trivial change, read `AGENTS.md` for workflow rules, `ARCHITECTURE.md` for boundaries and component interactions, `DESIGN.md` for UI and design-system work, and the active OpenSpec change or the specification covering the area you are changing.

Command aliases: OpenSpec skills may reference `/opsx-propose`, `/opsx-apply`, `/opsx-archive`, or `/opsx-explore`. Always substitute them with the `pc-plan-propose`, `pc-plan-apply`, `pc-plan-archive`, and `pc-plan-explore` skills respectively. User-facing command names are `/plan-propose`, `/plan-apply`, `/plan-archive`, and `/plan-explore`. Never mention the `opsx-` names to the user.

## Workflow ownership

<!-- PC-PLATFORM-WORKFLOW-START -->
A GitHub Issue or PR URL in the user's message means run the pipeline, in whatever words it arrives, unless they asked for analysis or context only.

- Issue URL: `pc-userstory` to parse it, `pc-plan-propose` for the plan, confirm, `pc-plan-apply` to build, then ship.
- An existing OpenSpec change, plus any of `implement` / `start` / `go`: `pc-plan-apply`, then ship.
- A PR URL with a feedback or fix request: read the PR comments, fix, update the PR.
<!-- PC-PLATFORM-WORKFLOW-END -->

## OpenSpec GitHub Storage

OpenSpec planning artifacts (proposal, design, tasks) sync to GitHub Issues when the `github.enabled` flag is set in `openspec/config.yaml`. The issue body holds the proposal content and a task checklist. The design document is posted as an issue comment. The feature branch PR links to the issue via `Closes #{issue-number}` in its description.

When a change is proposed, a GitHub Issue is created and its number is stored in `.openspec.yaml` under `github.issue`. As tasks are completed, the issue body is updated with checked items. When the change is archived, the issue is closed.

Local artifact files remain on disk for OpenSpec CLI compatibility. GitHub is the human-readable surface, and local files are the machine interface. There is no pull from GitHub back to local files.

When a change is created from a GitHub Issue URL (via `/plan-propose` with an issue URL), the existing issue is reused. No new issue is created.

If GitHub sync fails due to authentication or network errors, the workflow continues with local files and a warning is logged.

## Planning and execution

- Never combine an unrelated refactor with the work you were asked to do. It arrives under a message that does not mention it, and the reviewer approves both.
- Never introduce a new convention where the repository already has one, and never guess when requirements, architecture or a security constraint are unclear. Ask.

## Engineer selection

Inspect `.opencode/agents/*.md` before spawning. Prefer the most specialized custom engineer. `build` and `plan` are the only primaries and are never spawned; `fullstack-engineer` is the body they share and the fallback worker, so prefer a specialist over it. If no specialist matches, tell the user to create one with `/make-engineer`. Spawn only engineers present in that directory.

The `pc-plan-apply` skill is authoritative for subagent waves, dependency ordering, retries, and concurrency. A spawn past `agents.maxConcurrent` is denied (`pc-subagent-monitor`); a denied spawn is not a failed task, so re-issue it in the next wave.

## Tool and repository safety

- Never expose or commit secrets, credentials, tokens, or production data.
- Never overwrite a generated file, or uncommitted changes you did not make. The tree may be shared with a person and another agent.
- Never bypass a check, weaken a test, or silence a lint rule to reach a green result. A green run that was arranged is worse than a red one, because nobody looks again.
- Commit, push, open a pull request, change dependencies, or touch deployment configuration only with the user's explicit approval and the repository's stated process.

## Verification and completion

- Never call a bug fixed without a test that would have caught it, where one is practical.
- Never leave a specification, `ARCHITECTURE.md` or `DESIGN.md` asserting something this change made false.
- Never end on a blocker without naming it and the decision it needs. An unattended run that stops quietly looks like one that finished.

## Skills

Skills live in `.agents/skills/`. Always installed: `@pc-guardrails-generic`, `@pc-guardrails-project`, and `@browser-automation`. An entry under `## Abilities` is not a passive reference: call the `skill` tool once per `@skill-name`, guardrails first. Editing, shell and spawning stay blocked until you have (`pc-system-reminders`), and a loaded skill can require further loads.

<!-- PC-PLATFORM-SKILLS-GUIDE-START -->
Platform skills (GitHub):
- `@pc-userstory`: load when a GitHub Issue URL is detected. Fetches the issue via `gh` CLI and creates an OpenSpec change. NEVER use webfetch to access GitHub URLs.
- `pc-ops-ship`: load in ship mode to create a PR with screenshots, or in feedback mode to read and classify PR review comments.
<!-- PC-PLATFORM-SKILLS-GUIDE-END -->
