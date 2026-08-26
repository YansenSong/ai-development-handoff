---
name: ai-development-handoff
description: Turn repository-grounded discussion into an implementation-ready plan, persist the agreed plan in GitHub, and guide a local coding agent to execute it safely against the real checkout. Use when planning software changes, sharpening implementation details and boundaries, creating handoff plans, or executing an approved handoff plan locally.
---

# AI Development Handoff

Use this skill as one complete workflow for software changes that begin in ChatGPT or another planning conversation and are later executed by a local coding agent such as Codex.

The workflow is intentionally split between planning and execution, but both halves are governed by this single skill.

## Operating model

The durable handoff artifact is an implementation plan stored in the target repository under:

```text
.chatgpt/plans/YYYY-MM-DD-short-description.md
```

The target project does not need a copied workflow template, a copied skill directory, or a project-level `AGENTS.md` solely for this workflow. The plan is the project-specific handoff artifact. This skill carries the reusable method.

Three kinds of truth must remain distinct:

- **Conversation truth**: exploration, alternatives, temporary reasoning, and unresolved questions.
- **Plan truth**: the durable agreed scope, design decisions, constraints, verification, and acceptance criteria.
- **Execution truth**: the real state of the local repository when implementation begins.

Conversation produces decisions. GitHub preserves the decisions. The local coding agent executes those decisions against the real repository.

## Roles

### Planner

The planner is usually ChatGPT working from the GitHub repository. It must inspect the real repository before turning assumptions into implementation decisions.

The planner is responsible for:

- understanding the relevant current code;
- identifying facts from the repository instead of asking the user to recall them;
- separating user decisions from repository facts;
- stress-testing the design and implementation boundaries;
- sharpening domain language when terminology matters;
- recording only durable execution-relevant decisions;
- producing and publishing an implementation plan when the design is ready.

### GitHub repository

GitHub is the durable handoff medium. It stores:

- the source revision used during planning;
- the approved implementation plan;
- optional domain context or ADRs created during planning;
- later implementation commits and review history.

### Local coding agent

The executor is usually Codex operating on a local checkout. It is responsible for validating the plan against execution-time reality, implementing only the agreed scope, running required checks, and reporting deviations.

The executor must not reconstruct the earlier planning conversation. The plan must be sufficient for execution.

---

# Phase 1 — Repository understanding

Before asking design questions, inspect the target repository and establish the current facts relevant to the request.

Typical facts include:

- current architecture and module boundaries;
- public interfaces and callers;
- existing tests and verification commands;
- persistence and state transitions;
- compatibility constraints already visible in code or documentation;
- existing domain terminology;
- prior ADRs or design documentation;
- package/runtime/tooling constraints.

Do not ask the user for a fact that can reasonably be obtained from the repository or available tools.

Use this distinction:

- **Fact** → investigate it.
- **Preference** → ask the user.
- **Product requirement** → ask the user when unclear.
- **Design trade-off** → explain options, recommend one, and ask the user to decide.
- **Unknown code behavior** → inspect the source.

If repository evidence contradicts the user's description, surface the contradiction before planning further.

---

# Phase 2 — Design grilling

Treat planning as a design tree.

Each unsettled decision may unlock more decisions beneath it. Work the tree in rounds. The **frontier** is the set of important questions whose prerequisites are already settled.

For each round:

1. ask only questions that can be answered without guessing at still-unsettled prerequisites;
2. prioritize questions that can materially change architecture, scope, interfaces, data, security, compatibility, failure behavior, or verification;
3. normally ask no more than 3–5 substantive questions in one round, even if a larger frontier exists;
4. number each question;
5. provide a recommended answer with a short rationale;
6. wait for the user's decisions before expanding dependent branches.

A useful priority order is:

1. architecture or ownership boundaries;
2. public interface and compatibility decisions;
3. data, persistence, security, and permission decisions;
4. failure modes and state transitions;
5. implementation boundaries and operational behavior;
6. lower-impact implementation details;
7. naming and cosmetic preferences.

Do not ask low-impact questions while a higher-impact prerequisite is still unresolved.

The user owns decisions. The planner owns fact-finding.

Planning is not complete merely because the conversation feels long. It is complete when no material decision branch remains silently assumed and the Definition of Ready is satisfied.

---

# Phase 3 — Domain modeling

Use domain modeling when the discussion depends on project-specific terminology, ownership boundaries, or concepts whose ambiguity could change implementation.

## Canonical vocabulary

If the repository already contains `CONTEXT.md`, use its vocabulary and challenge conflicting language immediately.

If a term is vague or overloaded, propose a canonical term and distinguish it from nearby concepts.

Stress-test important terms with concrete scenarios, especially at boundaries between concepts.

Cross-check claimed domain behavior against the actual code. If the glossary, user's statement, and implementation disagree, surface the mismatch.

## CONTEXT.md

Create or update `CONTEXT.md` only when domain language actually needs to be recorded.

`CONTEXT.md` is a glossary, not an implementation spec. It must remain free of implementation details.

Use the format in `references/CONTEXT_FORMAT.md`.

During a planning conversation, capture resolved vocabulary logically as it crystallizes, but do not create noisy GitHub commits after every sentence. Publish coherent documentation updates at a sensible planning checkpoint or together with the handoff plan.

## ADRs

Offer an ADR only when all three conditions are true:

1. the decision is meaningfully hard to reverse;
2. a future reader would find the result surprising without context;
3. there was a real trade-off between plausible alternatives.

If any condition is missing, do not create an ADR.

Use the format in `references/ADR_FORMAT.md`.

---

# Phase 4 — Convergence and Definition of Ready

Before publishing an implementation plan as approved work, verify that the relevant dimensions are settled.

Evaluate the dimensions below for relevance. Do not mechanically ask about irrelevant categories.

## Required readiness dimensions

- **Goal**: the desired resulting behavior is concrete.
- **Non-goals**: nearby work that must remain out of scope is explicit when necessary.
- **Current behavior**: the relevant existing implementation has been verified from the repository.
- **Ownership/boundary**: the component or context responsible for the behavior is clear.
- **Interfaces**: relevant inputs, outputs, public API behavior, events, commands, or file formats are clear.
- **Failure behavior**: meaningful error and edge cases are defined.
- **State transitions**: lifecycle or state changes are defined when applicable.
- **Data/persistence**: data ownership, storage, migration, and lifetime are clear when applicable.
- **Security/permissions**: trust and permission boundaries are clear when applicable.
- **Compatibility**: backward-compatibility expectations are clear.
- **Operational constraints**: performance, platform, deployment, or environmental constraints are known when relevant.
- **Verification**: there is a concrete way to prove the change is correct.
- **Open decisions**: no unresolved issue remains that could materially change the implementation direction.

If material design questions remain, the plan must remain `Draft`.

Do not turn a planning document into `Approved` work merely because implementation details can be guessed.

---

# Phase 5 — Write the implementation plan

Use the format in `references/PLAN_FORMAT.md`.

Plans live at:

```text
.chatgpt/plans/YYYY-MM-DD-short-description.md
```

Create `.chatgpt/plans/` lazily when the first plan is published.

A plan is an execution contract, not a transcript. Include only context that affects implementation.

The plan must record:

- repository;
- branch;
- planning base commit;
- status;
- background;
- goal;
- non-goals;
- current behavior;
- agreed design;
- implementation requirements;
- likely affected areas;
- constraints;
- verification plan;
- acceptance criteria;
- open questions;
- optional execution notes;
- change log when the shared plan changes materially.

Prefer behavior and architectural requirements over brittle line-by-line instructions.

Likely affected files are guidance, not an exclusive allowlist unless the plan explicitly says otherwise.

## Plan status

Use these statuses:

- `Draft` — material planning is still open or the user has not approved execution.
- `Approved` — the design is ready for local implementation.
- `Implemented` — optional status after implementation has been reviewed and the plan is being updated as a historical record.
- `Superseded` — replaced by another plan; name the replacement.

Do not silently treat `Draft` as `Approved`.

The user's explicit approval is required before changing a plan from `Draft` to `Approved` when approval has not already been clearly given in the conversation.

---

# Phase 6 — Publish the handoff

When the user asks to publish or commit the agreed plan:

1. determine the target repository and branch;
2. record the source base commit used for planning;
3. write the plan under `.chatgpt/plans/`;
4. include any coherent `CONTEXT.md` or ADR changes that became durable decisions;
5. avoid unrelated source-code edits unless the user explicitly requests them;
6. commit the handoff documentation to GitHub.

Do not put secrets, credentials, access tokens, or private local filesystem paths in the plan.

A planning base commit identifies the source revision against which the design was reasoned. It is expected that the plan commit itself will advance repository HEAD beyond that base.

---

# Phase 7 — Local execution preflight

When using this skill as the local executor, read the exact plan named by the user.

Before editing anything:

1. confirm the plan is `Approved`, unless the user explicitly overrides the status;
2. inspect the current branch and `HEAD`;
3. inspect worktree/index status;
4. inspect the relevant current source;
5. verify that the plan's base commit is present in the current repository history;
6. inspect changes between the planning base and current `HEAD` that touch the plan's assumptions or likely affected areas;
7. determine whether the plan remains compatible with the current source.

Do **not** require `HEAD` to equal the planning base commit. The plan publication commit and later non-conflicting commits legitimately move HEAD forward.

If the planning base is not an ancestor of the execution revision, or intervening changes materially invalidate assumptions, stop and report that the plan needs revalidation.

Never discard, reset, clean, overwrite, or stash pre-existing local changes unless the user explicitly asks. If local changes could interfere with the task, stop and explain the conflict.

---

# Phase 8 — Execute the approved plan

During implementation:

- implement only the agreed scope;
- preserve non-goals and constraints;
- avoid unrelated refactors;
- treat the current repository as execution-time truth;
- do not silently redesign an agreed architecture;
- if a small unspecified detail can be resolved without changing the agreed design, choose the narrowest reasonable implementation and report the choice afterward;
- if a material conflict or missing decision appears, stop and request replanning instead of guessing.

Do not modify the plan, commit, push, publish, create a release, or perform other repository-history operations unless the user explicitly asks.

---

# Phase 9 — Verification and execution report

Run the checks required by the plan.

If a required check cannot run, state exactly why. Do not claim success for unexecuted verification.

At completion, report:

- what changed;
- files changed;
- tests/checks run and results;
- whether all acceptance criteria are satisfied;
- any deviation from the plan;
- any remaining risk, limitation, or follow-up.

If implementation intentionally deviated from the agreed design, make the deviation conspicuous. Do not bury it in a generic summary.

---

# Phase 10 — GitHub review

After implementation is committed and pushed, the planner may review the GitHub result against the original plan.

Review should compare:

- implementation versus agreed design;
- changed files versus intended scope;
- tests/checks versus verification plan;
- resulting behavior versus acceptance criteria;
- any implementation deviation versus the decisions recorded in the plan;
- whether new long-lived domain or architecture decisions should update `CONTEXT.md` or an ADR.

The implementation is not considered plan-conformant merely because the coding agent says it completed the task. Use repository evidence.

---

# General rules

## Keep durable artifacts separated

- `CONTEXT.md` answers: **what do project-specific domain terms mean?**
- ADRs answer: **why did we make this durable, non-obvious architectural decision?**
- `.chatgpt/plans/*.md` answers: **what exactly are we implementing in this change?**

Do not collapse all three into the plan.

## Prefer narrow plans

A plan that mixes unrelated changes is harder to reason about, execute, verify, and review. Split unrelated work into separate plans.

## Do not confuse plans with prompts

Plans are version-controlled engineering artifacts. They should make sense to a future human reviewer even if ChatGPT and Codex are replaced by different tools.

## Be agent-agnostic at the protocol boundary

The handoff format is Markdown + Git. Codex is a primary executor, but the plan should not depend on Codex-specific session state or proprietary APIs unless the requested task itself requires that.
