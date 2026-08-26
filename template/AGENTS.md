# Agent Instructions

This repository uses the **ChatGPT → GitHub → Local Coding Agent Handoff Workflow**.

The shared workflow is defined in `.chatgpt/WORKFLOW.md`.

When the user asks you to implement a plan stored under `.chatgpt/plans/`, follow these rules:

1. Read `.chatgpt/WORKFLOW.md` before implementation.
2. Read the exact plan file named by the user. Do not silently choose a different plan when multiple plans exist.
3. Implement only plans whose status permits execution according to the workflow. If the plan is still `Draft`, do not treat it as approved work unless the user explicitly overrides that state.
4. Treat the plan as the agreed design contract, but treat the current local repository as execution-time truth.
5. Before editing files, inspect the current branch, `HEAD`, worktree status, and relevant source. Verify the plan's base revision and assumptions according to `.chatgpt/WORKFLOW.md`.
6. Never discard, reset, clean, overwrite, or stash pre-existing local changes unless the user explicitly asks you to do so. If existing changes could interfere with the task, stop and explain the conflict.
7. If the plan is materially incompatible with the current code, stop before making speculative changes and report the conflict clearly.
8. Implement only the agreed scope. Preserve stated non-goals and constraints. Avoid unrelated refactors.
9. If a small implementation detail is unspecified but can be resolved without changing the agreed design, use the narrowest reasonable solution and report it afterward.
10. Run the verification steps required by the plan. If a required check cannot be run, say exactly why.
11. At completion, report:
    - what changed;
    - files changed;
    - tests/checks run and their results;
    - any deviation from the plan;
    - any remaining risk or follow-up.
12. Do not modify the implementation plan, commit, push, publish, create releases, or perform other repository-history operations unless the user explicitly asks you to do so.

`.chatgpt/PLAN_TEMPLATE.md` defines how new handoff plans should be authored. It is not an instruction to implement every plan in the repository.
