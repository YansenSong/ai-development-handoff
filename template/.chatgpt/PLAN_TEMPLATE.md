# Implementation Plan: <Short Title>

## Metadata

- **Status:** Draft
- **Repository:** `<owner>/<repo>`
- **Branch:** `<branch>`
- **Base commit:** `<full commit SHA used for planning>`
- **Created:** `YYYY-MM-DD`
- **Updated:** `YYYY-MM-DD`
- **Plan file:** `.chatgpt/plans/YYYY-MM-DD-short-description.md`
- **Related issue/PR:** `None` or `<reference>`

> The Base commit is the source revision used during planning. It does not need to equal the executor's later local `HEAD`; see `.chatgpt/WORKFLOW.md` for compatibility checks.

## 1. Background

Describe the problem, request, or opportunity that led to this plan.

Include only context that affects implementation. Do not paste the entire ChatGPT conversation.

## 2. Goal

State the desired outcome in concrete terms.

A good goal describes resulting behavior rather than only naming files to change.

## 3. Non-Goals

Explicitly list related work that is out of scope.

- ...
- ...

If there are no meaningful non-goals, write `None identified.` rather than deleting this section.

## 4. Current Behavior

Summarize the relevant current implementation as observed at the Base commit.

Include important source locations, flows, APIs, data contracts, or constraints that the executor needs to understand.

Distinguish confirmed repository facts from assumptions.

## 5. Agreed Design

Record the design decisions reached during planning.

This section is the primary design contract. Include choices the executor should not silently redesign during implementation.

- ...
- ...

## 6. Implementation Requirements

List concrete implementation requirements.

1. ...
2. ...
3. ...

Prefer observable behavior and necessary architecture over brittle line-by-line editing instructions.

## 7. Likely Affected Areas

List files or modules expected to be relevant.

This is guidance, not an exclusive allowlist unless the plan explicitly says otherwise.

- `path/to/file`
- `path/to/module/`

## 8. Constraints

Record boundaries that must be preserved.

Examples:

- no new third-party dependencies;
- preserve the existing public API;
- do not change the database schema;
- do not modify unrelated modules;
- retain backward compatibility with ...

## 9. Verification Plan

Specify the checks the local coding agent should run after implementation.

```sh
# example
npm test
npm run typecheck
```

Add targeted manual checks when automated tests are insufficient.

If a verification step depends on credentials, services, hardware, or environment that may not be available locally, say so explicitly.

## 10. Acceptance Criteria

The implementation is complete only when all applicable criteria are satisfied.

- [ ] ...
- [ ] ...
- [ ] Required tests/checks pass, or any inability to run them is explicitly reported.
- [ ] No stated constraint or non-goal is violated.
- [ ] Any material deviation from the approved design has been brought back to the user rather than silently accepted.

## 11. Open Questions

List unresolved questions that must be answered before or during implementation.

If none remain when the plan becomes `Approved`, write:

`None.`

A material unresolved design question should normally keep the plan in `Draft` status.

## 12. Execution Notes

Optional operational notes for the local executor that do not change the agreed design.

Examples:

- platform-specific test commands;
- setup needed before running verification;
- generated files that should not be edited manually;
- known local tooling requirements.

If no notes are needed, write `None.`

## 13. Risks and Rollback Considerations

Record meaningful implementation or rollout risks when applicable.

For changes where rollback is relevant, describe the expected rollback approach at an appropriate level. Do not invent deployment procedures that the project does not use.

If this section is not applicable, write `None identified.`

## 14. Change Log

Record material changes to an already shared plan.

| Date | Change | Reason |
| --- | --- | --- |
| YYYY-MM-DD | Initial draft | Initial planning handoff |

When an approved plan is materially changed, update `Updated`, add a row here, and confirm whether the plan should return to `Draft` for renewed approval.
