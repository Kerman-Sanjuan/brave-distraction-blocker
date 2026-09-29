# Tasks

## 1. Configuration and spec

- [x] 1.1 Add `github` section to `openspec/config.yaml` with `enabled` flag and optional `repo` override <!-- agent: fullstack-engineer.fast, depends_on: [], touches: [openspec/config.yaml] -->
- [x] 1.2 Write the openspec-github-storage spec file with requirements and scenarios <!-- agent: fullstack-engineer.build, depends_on: [], touches: [openspec/changes/openspec-github-storage/specs/openspec-github-storage/spec.md] -->

## 2. Proposal phase

- [ ] 2.1 Modify openspec-propose: create GitHub Issue after scaffold, post proposal as body, store issue number in `.openspec.yaml`, post design.md as comment, sync tasks.md to issue body <!-- agent: fullstack-engineer.build, depends_on: [1.1], touches: [.agents/skills/openspec-propose/SKILL.md] -->

## 3. Apply and integration phase

- [ ] 3.1 Modify pc-plan-propose: call GitHub sync after writing artifacts to disk in Step 4 <!-- agent: fullstack-engineer.build, depends_on: [2.1], touches: [.agents/skills/pc-plan-propose/SKILL.md] -->
- [ ] 3.2 Modify openspec-apply-change: sync task checkbox state to issue body after each task completion <!-- agent: fullstack-engineer.build, depends_on: [2.1], touches: [.agents/skills/openspec-apply-change/SKILL.md] -->
- [ ] 3.3 Modify pc-plan-apply: sync task checkboxes to issue body after each wave commit <!-- agent: fullstack-engineer.build, depends_on: [2.1], touches: [.agents/skills/pc-plan-apply/SKILL.md] -->

## 4. Archive phase

- [ ] 4.1 Modify openspec-archive-change: close the GitHub Issue after archiving <!-- agent: fullstack-engineer.build, depends_on: [2.1], touches: [.agents/skills/openspec-archive-change/SKILL.md] -->
- [ ] 4.2 Modify pc-plan-archive: close the GitHub Issue in interactive and autonomous modes <!-- agent: fullstack-engineer.build, depends_on: [2.1], touches: [.agents/skills/pc-plan-archive/SKILL.md] -->

## 5. Ship and userstory

- [ ] 5.1 Modify pc-ops-ship: add `Closes #{issue-number}` to PR description <!-- agent: fullstack-engineer.fast, depends_on: [2.1], touches: [.agents/skills/pc-ops-ship/SKILL.md] -->
- [ ] 5.2 Modify pc-userstory: use existing issue as proposal issue when creating from issue URL <!-- agent: fullstack-engineer.build, depends_on: [2.1], touches: [.agents/skills/pc-userstory/SKILL.md] -->

## 6. Documentation and mirror

- [ ] 6.1 Update AGENTS.md to document the GitHub storage workflow <!-- agent: fullstack-engineer.fast, depends_on: [2.1,3.1,3.2,3.3,4.1,4.2,5.1,5.2], touches: [AGENTS.md] -->
- [ ] 6.2 Mirror modified openspec-* skills from `.agents/skills/` to `.opencode/skills/` <!-- agent: fullstack-engineer.fast, depends_on: [2.1,3.2,4.1], touches: [.opencode/skills/openspec-propose/SKILL.md,.opencode/skills/openspec-apply-change/SKILL.md,.opencode/skills/openspec-archive-change/SKILL.md] -->

## 7. Verification

- [ ] 7.1 Run `openspec validate` on the change and verify all artifacts pass <!-- agent: fullstack-engineer.fast, depends_on: [1.2,6.1,6.2], touches: [] -->
