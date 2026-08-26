# Changelog

All notable changes to the AI Development Handoff workflow are recorded here.

## 0.1.0 — 2026-08-26

Initial workflow release.

### Added

- Defined the ChatGPT → GitHub → local coding agent handoff model.
- Added a distributable target-project template under `template/`.
- Added root `AGENTS.md` instructions for local coding agents.
- Added `.chatgpt/WORKFLOW.md` as the shared workflow contract.
- Added `.chatgpt/PLAN_TEMPLATE.md` for durable implementation plans.
- Added `.chatgpt/VERSION` so target repositories can record the adopted workflow version.
- Defined `.chatgpt/plans/` as project-owned implementation history that workflow updates must not overwrite.
- Defined base-revision compatibility checks that account for plan/documentation commits after the source revision used during planning.
