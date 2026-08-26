# ChatGPT → GitHub → Local Coding Agent Handoff Workflow

Workflow version: 0.1.0

## 1. Purpose

This workflow defines a reusable way to separate **planning and design discussion** from **local code execution**.

The intended flow is:

1. ChatGPT reads and analyzes the project through the GitHub integration.
2. The user and ChatGPT discuss the problem, alternatives, constraints, and desired behavior.
3. After sufficient agreement is reached, ChatGPT writes an implementation plan into `.chatgpt/plans/` and commits that plan to GitHub when the user asks it to publish the handoff.
4. The user pulls the latest repository state to the local machine.
5. A local coding agent reads the agreed plan, validates it against the real local checkout, implements the agreed scope, and runs the required checks.
6. The user reviews the local result and decides whether to commit and push the implementation.
7. After the implementation is pushed, ChatGPT can use GitHub again to review the resulting code against the original plan.

GitHub is the durable handoff medium. Markdown plans are the interface between the planning side and the execution side.

The workflow does **not** require ChatGPT to directly control the local coding agent.

---

## 2. Core model

The workflow separates three kinds of truth:

- **Conversation truth:** exploration, alternatives, questions, and temporary reasoning discussed between the user and ChatGPT.
- **Plan truth:** the durable decisions, scope, constraints, and acceptance criteria written into an implementation plan.
- **Execution truth:** the actual state of the local repository at the time the coding agent begins work.

Conversation produces decisions. GitHub preserves the decisions. The local coding agent executes those decisions against the real repository.

A plan is therefore an execution contract, not a transcript of the conversation.

---

## 3. Roles

### 3.1 ChatGPT — Planner and later Reviewer

During planning, ChatGPT should:

- inspect the relevant GitHub source before proposing implementation details;
- explain the current behavior and identify relevant modules or files;
- discuss alternatives with the user instead of prematurely committing to a design;
- distinguish confirmed repository facts from assumptions;
- record explicit decisions, constraints, non-goals, verification requirements, and acceptance criteria;
- keep the plan focused on execution-relevant context rather than reproducing the chat transcript;
- record the repository, branch, and source base commit used during planning;
- avoid putting secrets, credentials, access tokens, or private local filesystem paths into plan documents.

When this handoff workflow is being used, the default planning-stage deliverable is a plan document rather than an unreviewed source-code change. ChatGPT may still edit source code directly through GitHub when the user explicitly asks for that different workflow.

After implementation is pushed, ChatGPT may act as reviewer by comparing the resulting code with the plan's agreed design and acceptance criteria.

### 3.2 GitHub repository — Durable handoff record

The repository stores shared durable context:

- source code visible to ChatGPT;
- the adopted workflow version;
- the workflow and plan template;
- version-controlled implementation plans;
- later implementation commits and pull requests.

Repository history should make it possible to determine:

- what was agreed;
- which source revision planning was based on;
- what constraints and non-goals existed;
- what was eventually implemented.

### 3.3 Local coding agent — Executor

The local coding agent works against the local checkout and should:

- read `AGENTS.md`;
- read this workflow;
- read the exact implementation plan named by the user;
- inspect the actual local source before changing it;
- verify that the plan remains compatible with the current repository state;
- implement only the agreed scope;
- run required verification;
- report deviations and unresolved issues rather than silently redefining the design.

The executor is not expected to reconstruct the earlier ChatGPT conversation. All execution-relevant decisions must be present in the plan.

Codex is a primary target for this workflow, but the protocol is intentionally agent-agnostic.

### 3.4 User — Approval and repository authority

The user decides:

- when planning has reached sufficient agreement;
- when a plan becomes approved for execution;
- when the local repository should be pulled or synchronized;
- whether implementation results are acceptable;
- whether changes should be committed, pushed, merged, released, or abandoned.

Neither ChatGPT nor the local executor should silently infer approval for destructive or repository-history-changing operations.

---

## 4. Repository layout

A project adopting this workflow should contain:

```text
/
├── AGENTS.md
└── .chatgpt/
    ├── VERSION
    ├── WORKFLOW.md
    ├── PLAN_TEMPLATE.md
    └── plans/
        ├── README.md
        └── YYYY-MM-DD-short-description.md
```

### Upstream-managed workflow files

The following files originate from the workflow distribution and may be refreshed when adopting a newer workflow version:

- `AGENTS.md`
- `.chatgpt/VERSION`
- `.chatgpt/WORKFLOW.md`
- `.chatgpt/PLAN_TEMPLATE.md`

`.chatgpt/plans/README.md` may be installed initially as guidance but should not be treated as a reason to overwrite project plan history.

### Project-owned files

All actual plan files under `.chatgpt/plans/` are owned by the target project.

A workflow updater must never delete or overwrite project plan files as part of a normal workflow-version update.

---

## 5. Plan lifecycle

Plans use these statuses:

### `Draft`

The design is still being discussed, assumptions remain unresolved, or the user has not yet approved execution.

A local coding agent should not normally implement a Draft plan unless the user explicitly instructs it to proceed despite that status.

### `Approved`

The user has agreed that the plan is ready to hand off for implementation.

Before marking a plan `Approved`, material open design questions should normally be resolved. Remaining known uncertainties should be documented explicitly.

### `Implemented`

The plan has been implemented and reviewed sufficiently for the project to record it as completed.

Changing the plan status to `Implemented` is a documentation action and should not be silently performed by the local coding agent unless requested.

### `Superseded`

The plan is no longer the active implementation contract because a newer plan or design replaced it.

A superseded plan should remain in Git history and should identify its replacement when known.

---

## 6. Planning protocol in ChatGPT

### Step 1 — Inspect before designing

ChatGPT should read the relevant GitHub source before giving detailed implementation instructions.

The plan must be grounded in the repository version that was actually inspected.

### Step 2 — Discuss the problem

The user and ChatGPT should clarify:

- the desired outcome;
- current behavior;
- design alternatives where relevant;
- constraints;
- non-goals;
- compatibility requirements;
- verification expectations;
- acceptance criteria.

Not every task requires a long design discussion. The goal is enough clarity to make execution deterministic without preserving unnecessary conversation.

### Step 3 — Record the planning base

The plan records:

- repository;
- branch;
- **base commit**: the source commit that ChatGPT used as the primary code reference during planning.

The base commit is intentionally the source revision used to form the plan. It is normally earlier than the commit that later adds the plan file itself.

### Step 4 — Write the plan

Use `.chatgpt/PLAN_TEMPLATE.md`.

Recommended filename:

```text
.chatgpt/plans/YYYY-MM-DD-short-description.md
```

If multiple plans are created on the same date with similar names, add a short disambiguating suffix rather than overwriting an existing plan.

### Step 5 — Approval

A plan should remain `Draft` while material design decisions are unresolved.

Once the user explicitly agrees that the plan is ready for execution, set its status to `Approved` before or as part of publishing the handoff.

Do not treat the mere act of creating a plan file as implicit approval.

---

## 7. Base revision and local compatibility

This section is critical because the plan itself is committed after the source revision used during planning.

### 7.1 Do not require `HEAD == Base commit`

That equality will often be false even in the healthy case. For example:

```text
A -- B -- P
     ^    ^
     |    current HEAD after git pull
     planning base B

P = commit that adds the implementation plan
```

The correct question is whether the current repository is still compatible with the plan.

### 7.2 Executor preflight

Before editing, the local coding agent should inspect at least:

```sh
git branch --show-current
git rev-parse HEAD
git status --short
git merge-base --is-ancestor <base-commit> HEAD
```

Equivalent Git commands or APIs are acceptable.

The executor should then examine relevant changes between the plan's base commit and current `HEAD` when necessary:

```sh
git diff <base-commit>..HEAD -- <relevant paths>
```

### 7.3 Compatible newer HEAD

A newer `HEAD` may still be compatible when the commits after the base only contain:

- the implementation plan itself;
- workflow/documentation changes;
- unrelated changes that do not invalidate plan assumptions.

In that case execution may continue after verification.

### 7.4 Material divergence

The executor should stop before speculative implementation when, for example:

- the base commit is not an ancestor of the current history and compatibility cannot be established safely;
- relevant source files changed after the base in a way that invalidates the agreed design;
- required APIs, modules, or assumptions no longer exist;
- the current branch materially differs from the branch the plan targeted;
- unresolved merge/conflict state exists;
- pre-existing local changes overlap the task and make safe execution ambiguous.

The executor should explain the conflict and ask for a refreshed plan or explicit user direction rather than silently redesigning the task.

### 7.5 Dirty worktree rule

The executor must never automatically discard, reset, clean, overwrite, or stash pre-existing local work.

A dirty worktree is not automatically fatal, but if existing changes overlap or materially affect the planned task, the executor should stop and report the situation before editing.

---

## 8. Implementation protocol for the local coding agent

After preflight succeeds:

1. Re-read the Goal, Non-Goals, Agreed Design, Constraints, Verification Plan, and Acceptance Criteria.
2. Inspect the relevant local implementation in enough detail to execute safely.
3. Make the smallest coherent change that satisfies the plan.
4. Avoid unrelated cleanup and opportunistic refactors unless required by the plan.
5. Preserve existing behavior not identified for change.
6. Add or update tests when required by the plan or needed to verify changed behavior.
7. Run the plan's verification steps.
8. Review the resulting diff against the plan before declaring completion.
9. Report implementation results to the user.

The local coding agent should not edit the plan simply to make its own implementation appear compliant.

---

## 9. Handling unspecified details and deviations

### Small unspecified details

If an implementation detail is not specified but can be resolved without changing the agreed design, the executor may choose the narrowest reasonable implementation.

It should report that choice afterward when it is material.

### Material design deviation

If implementation requires changing a stated design decision, public contract, constraint, non-goal, or acceptance criterion, the executor should stop and surface the issue.

The normal response is to return to planning, update the plan deliberately, and then continue from the revised approved plan.

### Verification failure

If a required test or check fails:

- investigate failures caused by the implementation;
- do not hide or delete failing tests merely to obtain a green result;
- distinguish new failures from clearly pre-existing failures when evidence supports that distinction;
- report any verification that could not be completed.

---

## 10. Completion report

When implementation work is finished, the local coding agent should report:

### Changes made

A concise summary of implemented behavior.

### Files changed

The main files or modules changed.

### Verification

Commands/checks run and their outcomes.

### Deviations

Any difference from the approved plan. Write `None` when there were no material deviations.

### Remaining risks or follow-up

Anything the user should know before committing, pushing, or deploying.

The executor should not automatically commit, push, merge, release, or publish unless the user explicitly requests those operations.

---

## 11. Post-implementation GitHub review

After local changes are committed and pushed, ChatGPT may inspect the updated repository or pull request and review it against the plan.

Review should focus on:

- whether the agreed design was implemented;
- whether non-goals and constraints were respected;
- whether acceptance criteria appear satisfied;
- whether unexpected scope expansion occurred;
- whether tests and documentation match the change.

The implementation plan remains useful after coding because it records the intent against which the code can be reviewed.

---

## 12. Security and sensitive information

Plans are repository content and may be visible to everyone who can read the repository.

Do not place the following in implementation plans:

- passwords;
- API keys;
- access tokens;
- private keys;
- authentication cookies;
- secrets copied from local configuration;
- sensitive private local filesystem details unless genuinely required and appropriate for the repository.

Refer to secret names or configuration concepts instead of secret values.

---

## 13. Workflow updates

The workflow version adopted by a project is stored in `.chatgpt/VERSION`.

When updating the workflow:

- upstream-managed workflow files may be replaced with the newer distribution;
- project-owned implementation plans must be preserved;
- project-specific additions to `AGENTS.md` should be reviewed and merged rather than blindly overwritten if a project has customized that file;
- changes that alter plan semantics should be recorded in the upstream changelog.

Until version `1.0.0`, this workflow should be treated as evolving and validated through real project use.
