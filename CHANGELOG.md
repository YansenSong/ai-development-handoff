# Changelog

## 0.3.1 — Restore original grilling semantics

- Restored Phase 2 to the original `grilling` design-tree/frontier protocol.
- Restored the rule to ask the **whole frontier** in each round rather than limiting rounds to 3–5 questions.
- Removed the added fixed priority ordering for architecture, interfaces, security, testing, naming, and related categories.
- Removed the added Phase 2 stress-test checklist from the grilling protocol.
- Restored the original dependency rule: a question that depends on another still-open question belongs to a later round.
- Preserved the original responsibility split: ChatGPT finds facts; the user makes decisions.
- Restored the completion rule: the frontier must be empty and the user must confirm shared understanding before synthesis.
- Kept the Ready-to-Spec gate as a separate completeness review. If it discovers a missed material decision, that decision becomes a new design-tree branch and returns through the normal grilling frontier process.

## 0.3.0 — Planning-only grill → spec workflow

- Reframed the Skill as the ChatGPT planning half of the engineering workflow.
- Integrated the `grill-with-docs` pattern: repository fact finding, design-tree grilling, domain modeling, and selective ADR creation.
- Integrated the `to-spec` pattern as a distinct synthesis phase after design convergence.
- Replaced `.chatgpt/plans/*.md` with `.chatgpt/specs/*.md` as the primary Codex handoff artifact.
- Added exhaustive user stories and explicit testing-seam decisions to the handoff format.
- Clarified that to-spec should not reopen broad interviewing; unresolved material decisions return briefly to grilling before synthesis resumes.
- Removed all local implementation, verification, execution-report, and code-review behavior from the Skill.
- Removed the imported `implement/` skill because implementation is independently installed in Codex.
- Removed the imported `to-spec/` directory after absorbing its method into the root Skill.
- Replaced `PLAN_FORMAT.md` with `SPEC_FORMAT.md`.

## 0.2.0 — Unified single-skill workflow

- Replaced the separate `skills/` and `template/` architecture with one root `SKILL.md`.
- Folded design grilling, domain modeling, handoff planning, local execution, and GitHub review into one lifecycle.
- Moved plan, context, and ADR formats into supporting `references/` files.
- Removed the requirement to copy workflow/template files into every target project.
- Defined `.chatgpt/plans/*.md` as the primary project-specific handoff artifact.
- Added a Definition of Ready gate before approved execution plans.
- Clarified repository-fact versus user-decision responsibilities.
- Clarified planning-base versus execution-HEAD compatibility checks.

## 0.1.0 — Initial workflow

- Introduced the ChatGPT → GitHub → local coding agent handoff model.
- Added a distributable project template with workflow, plan template, version marker, and agent instructions.
- Defined implementation plans as version-controlled project artifacts.
