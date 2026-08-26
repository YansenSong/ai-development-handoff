# AI Development Handoff

A reusable **ChatGPT → GitHub → local coding agent** handoff workflow.

This repository defines a lightweight engineering protocol for separating planning from execution:

1. ChatGPT reads and analyzes a project through its GitHub integration.
2. The user and ChatGPT discuss the problem, constraints, alternatives, and desired behavior.
3. Once the direction is agreed, ChatGPT writes a version-controlled implementation plan into the project's `.chatgpt/plans/` directory.
4. The user pulls the latest repository state locally.
5. A local coding agent such as Codex reads the plan, verifies it against the real local checkout, implements the agreed scope, and runs the required checks.
6. The resulting implementation can be pushed back to GitHub for review against the original plan.

GitHub is the durable handoff medium. Markdown is the interface between planning and execution.

## Why this exists

Chat conversations are useful for exploration, but they are a poor long-term execution contract. A local coding agent also should not be expected to reconstruct decisions from an earlier ChatGPT conversation.

This workflow turns the agreed outcome of a conversation into a repository artifact that is:

- version controlled;
- reviewable by humans;
- readable by coding agents;
- tied to a repository branch and base commit;
- portable across projects and execution agents;
- auditable after implementation.

## Repository layout

```text
ai-development-handoff/
├── README.md
├── VERSION
├── CHANGELOG.md
└── template/
    ├── AGENTS.md
    └── .chatgpt/
        ├── VERSION
        ├── WORKFLOW.md
        ├── PLAN_TEMPLATE.md
        └── plans/
            └── README.md
```

The `template/` directory contains the files intended to be placed into a target project.

## Target project layout

After adopting the workflow, a project should contain:

```text
project/
├── AGENTS.md
└── .chatgpt/
    ├── VERSION
    ├── WORKFLOW.md
    ├── PLAN_TEMPLATE.md
    └── plans/
        ├── README.md
        └── YYYY-MM-DD-short-description.md
```

### Upstream-managed files

These files come from this repository and may be updated when the workflow version changes:

- `AGENTS.md`
- `.chatgpt/VERSION`
- `.chatgpt/WORKFLOW.md`
- `.chatgpt/PLAN_TEMPLATE.md`
- `.chatgpt/plans/README.md` only as initial directory guidance

### Project-owned files

These files belong to the target project and must not be overwritten by workflow updates:

- `.chatgpt/plans/*.md` implementation plans

The plan history is part of the project's own engineering record.

## First adoption

For v0.1.0, adoption is intentionally simple: copy the contents of `template/` into the root of the target repository and commit them as normal files.

Do not use a Git submodule for the first version of this workflow. Keeping the files materialized in each project makes them straightforward for GitHub, ChatGPT, Codex, and other coding agents to read.

An installer/updater CLI may be added later after the workflow has been exercised across multiple real projects.

## Daily workflow

### Planning in ChatGPT

Ask ChatGPT to inspect the relevant GitHub source and discuss the desired change. Before handing the task to a local coding agent, create an implementation plan based on `.chatgpt/PLAN_TEMPLATE.md` and store it under `.chatgpt/plans/`.

A plan should capture the decisions needed for execution, not a transcript of the chat.

### Local execution

Pull the repository locally, then instruct Codex or another coding agent to execute a specific plan, for example:

```text
Read AGENTS.md and execute .chatgpt/plans/2026-08-26-example-change.md.
Verify the plan against the current repository before editing anything.
```

The coding agent should treat the plan as the agreed design contract and the current local repository as execution-time truth.

### Review

After implementation is pushed, ChatGPT can inspect the resulting GitHub changes and compare them with the original plan and acceptance criteria.

## Core principles

1. **Discuss before executing.** Planning and implementation are separate stages.
2. **Persist decisions, not chat transcripts.** The plan contains execution-relevant consensus.
3. **Pin planning context.** Plans record repository, branch, and base commit.
4. **Verify before editing.** Local source is execution-time truth; stale plans must not be followed blindly.
5. **Keep scope explicit.** Goals, non-goals, constraints, and acceptance criteria are first-class fields.
6. **Report deviations.** Coding agents must not silently redesign an approved plan.
7. **Keep the protocol agent-agnostic.** Codex is a primary target, but the handoff format is Markdown + Git rather than a proprietary agent API.
8. **Keep project plans project-owned.** Workflow upgrades must never erase local decision history.

## Versioning

The current workflow version is stored in `VERSION`. Target projects store the adopted version in `.chatgpt/VERSION`.

The initial release is `0.1.0`. Until the workflow reaches `1.0.0`, structure and conventions may evolve as they are tested on real projects.
