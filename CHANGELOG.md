# Changelog

## 0.3.4 — Ask one grilling question at a time

- Changed the ChatGPT grilling interaction to ask exactly one unresolved decision question per assistant turn.
- Kept the design-tree and frontier model, but recompute the entire tree and frontier after every user answer before choosing the next question.
- Removed the previous `ask the whole frontier` behavior from this workflow because later questions can be reshaped or invalidated by the answer to the current question.
- Kept repository fact-finding as ChatGPT's responsibility and user decisions as the user's responsibility.
- Kept the frontier-empty plus shared-understanding completion rule.
- Documented this as a deliberate ChatGPT workflow adaptation based on observed Codex `grill-with-docs` interaction behavior, rather than claiming it is identical to the literal `grilling` skill text.

## 0.3.3 — Restore original to-spec semantics

- Restored Phase 5 to the original `to-spec` sequence: repository understanding, testing-seam proposal/confirmation, then spec synthesis.
- Restored the narrow To Spec interaction that asks the user to confirm proposed testing seams rather than forcing seam confirmation into Grilling or Ready-to-Spec.
- Restored the original seam guidance: prefer existing seams, use the highest seam possible, place new seams as high as possible, and prefer fewer seams with an ideal of one when sufficient.
- Restored the stronger user-story requirement: a LONG, extremely extensive numbered list covering all aspects of the feature.
- Restored the original rule to omit specific file paths and code snippets from Implementation Decisions, retaining only the prototype-derived decision-rich snippet exception.
- Restored the original Testing Decisions content: external behavior, modules under test, confirmed seams, and prior-art tests.
- Kept `.chatgpt/specs/*.md` publication plus Status, Repository, Branch, Base commit, and dates as explicit GitHub handoff adaptations rather than part of the core To Spec method.

## 0.3.2 — Restore original domain-modeling semantics

- Restored Phase 3 as an active domain-modeling discipline rather than a lighter terminology check.
- Restored immediate glossary behavior: when a domain term is resolved, update the current `CONTEXT.md` artifact state immediately rather than deferring capture until spec synthesis.
- Clarified that immediate domain capture does not require a separate Git commit per resolved term; GitHub publication timing belongs to the handoff publication phase.
- Restored lazy file creation: create `CONTEXT.md` when the first durable term is resolved and create an ADR directory only when the first ADR is justified.
- Restored explicit single-context versus multi-context repository structure, including root `CONTEXT-MAP.md` and context-specific `CONTEXT.md`/ADR locations.
- Restored the original behaviors to challenge glossary conflicts, sharpen fuzzy terminology, test relationships with concrete scenarios, and cross-reference claimed domain behavior with code.
- Preserved the original three-part ADR threshold: hard to reverse, surprising without context, and the result of a real trade-off.
- Kept GitHub-specific batching/publishing rules in Phase 6 rather than changing the domain-modeling method itself.

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
