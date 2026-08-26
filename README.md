# AI Development Handoff

A single reusable Skill for turning repository-grounded design discussion into an implementation-ready GitHub handoff, then guiding a local coding agent such as Codex through safe execution and review.

The core workflow is:

```text
ChatGPT / Planner
      ↓
read GitHub source
      ↓
stress-test design and boundaries
      ↓
record durable domain/architecture decisions when needed
      ↓
Definition of Ready
      ↓
Implementation Plan
      ↓
GitHub: .chatgpt/plans/*.md
      ↓
local git pull
      ↓
Codex / Local Coding Agent
      ↓
validate plan against real checkout
      ↓
implement + verify + report
      ↓
push implementation
      ↓
ChatGPT / Planner reviews against original plan
```

## Why one Skill

Earlier versions of this repository separated planning skills from a distributable project template. The workflow is now intentionally unified.

The reusable behavior belongs in one Skill. Target repositories only need to keep the project-specific artifacts they actually produce—primarily `.chatgpt/plans/*.md`, plus optional `CONTEXT.md` and ADRs when the planning process discovers durable domain or architecture knowledge.

There is no requirement to copy a workflow template or a dedicated skill directory into every target project.

## Repository layout

```text
ai-development-handoff/
├── SKILL.md
├── README.md
├── VERSION
├── CHANGELOG.md
├── agents/
│   └── openai.yaml
└── references/
    ├── PLAN_FORMAT.md
    ├── CONTEXT_FORMAT.md
    └── ADR_FORMAT.md
```

`SKILL.md` is the single source of truth for the workflow. The files in `references/` are supporting formats used by that Skill, not separate skills.

## How to use it with ChatGPT

When using ChatGPT with GitHub access, have ChatGPT read this repository's `SKILL.md`, then ask it to use that workflow while planning a change in another GitHub repository.

The expected result of planning is a version-controlled implementation plan under the target repository's `.chatgpt/plans/` directory.

ChatGPT does not need to directly control the local coding agent for this workflow to work.

## How to use it with Codex

Install or otherwise make this Skill available to Codex, then point Codex at an approved plan in the local project, for example:

```text
Execute .chatgpt/plans/2026-08-26-example-change.md using the AI Development Handoff workflow.
```

Codex should validate the plan against the real local checkout before editing, respect existing local changes, implement only the agreed scope, run the specified verification, and report deviations.

## Target project artifacts

A project adopting the workflow may contain only:

```text
project/
└── .chatgpt/
    └── plans/
        └── YYYY-MM-DD-short-description.md
```

Additional durable artifacts are created only when useful:

```text
CONTEXT.md
docs/adr/0001-some-decision.md
```

## Core principles

1. Find repository facts instead of asking the user to remember them.
2. Ask the user for decisions, preferences, and unclear requirements.
3. Stress-test the design before implementation.
4. Persist decisions, not chat transcripts.
5. Keep domain glossary, ADRs, and implementation plans separate.
6. Record the source base commit used during planning.
7. Do not require execution HEAD to equal the planning base; validate intervening changes instead.
8. Treat the real local checkout as execution-time truth.
9. Never silently redesign an approved plan.
10. Verify with repository evidence rather than trusting an agent's completion claim.

## Versioning

The current workflow version is stored in `VERSION`.

Until `1.0.0`, the structure and planning protocol may evolve as the workflow is tested on real projects.
