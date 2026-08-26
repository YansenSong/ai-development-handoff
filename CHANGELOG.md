# Changelog

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
