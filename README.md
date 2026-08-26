# AI Development Handoff

A single planning Skill for turning a repository-grounded ChatGPT conversation into an implementation-ready GitHub spec for a local Codex agent.

```text
ChatGPT + GitHub
      ↓
Grill with docs
      ├── inspect the real codebase
      ├── question the design tree
      ├── sharpen boundaries and edge cases
      ├── update domain context when needed
      └── record true ADRs when justified
      ↓
Ready-to-spec gate
      ↓
To spec
      ├── Problem Statement
      ├── Solution
      ├── extensive User Stories
      ├── Implementation Decisions
      ├── Testing Decisions / seams
      └── Out of Scope
      ↓
GitHub: .chatgpt/specs/*.md
      ↓
local git pull
      ↓
Codex + separately installed implement skill
```

As of v0.3.0, this repository deliberately covers the **planning half only**. The final contract between ChatGPT and Codex is the published spec.

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
    ├── SPEC_FORMAT.md
    ├── CONTEXT_FORMAT.md
    └── ADR_FORMAT.md
```

`SKILL.md` is the workflow. `references/` contains supporting artifact formats; they are not separate skills.

## Target-project artifacts

Primary handoff artifact:

```text
.chatgpt/specs/YYYY-MM-DD-short-description.md
```

Durable planning can also produce:

```text
CONTEXT.md
docs/adr/0001-short-decision.md
```

For multiple bounded contexts, use `CONTEXT-MAP.md` plus context-specific `CONTEXT.md` files.

## ChatGPT usage

Ask ChatGPT to read this repository's `SKILL.md`, then apply it while discussing a change in a target GitHub repository. ChatGPT investigates repository facts, grills the design with you, records durable domain/architecture knowledge when appropriate, then switches to synthesis and publishes the spec.

## Codex handoff

After pulling the target repository locally, point Codex's separately installed implementation skill at the spec:

```text
Implement .chatgpt/specs/2026-08-26-example-change.md.
```

Codex should use its own implementation/TDD/review workflow. This repository intentionally does not duplicate that skill.

## Principles

- Facts are ChatGPT's job to retrieve; decisions are the user's job to make.
- Grill before specifying.
- Domain context, ADRs, and task specs are different artifacts with different lifetimes.
- To-spec is synthesis, not a second full interview.
- Prefer high-level behavioral testing seams and existing seams.
- Specs capture stable decisions, not chat transcripts or brittle file-by-file instructions.
- GitHub is the durable handoff medium.
- Implementation belongs to Codex, not this Skill.

## Versioning

Current version: `0.3.0`.
