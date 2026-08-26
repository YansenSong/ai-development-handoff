# Implementation Plan Format

Use this format for `.chatgpt/plans/YYYY-MM-DD-short-description.md`.

```md
# Implementation Plan: <Short Title>

## Metadata

- **Status:** Draft
- **Repository:** `<owner>/<repo>`
- **Branch:** `<branch>`
- **Base commit:** `<full commit SHA>`
- **Created:** `YYYY-MM-DD`
- **Updated:** `YYYY-MM-DD`
- **Plan file:** `.chatgpt/plans/YYYY-MM-DD-short-description.md`

## 1. Background

Describe the problem, request, or opportunity that led to this plan.
Include only context that affects implementation. Do not paste the conversation transcript.

## 2. Goal

State the desired outcome in concrete behavioral terms.

## 3. Non-Goals

Explicitly list related work that is out of scope.

- ...

## 4. Current Behavior

Summarize the relevant implementation as verified from the repository.
Include important flows, modules, APIs, state, or constraints the executor must understand.

## 5. Agreed Design

Record the decisions reached during planning.
This is the main design contract and should contain choices the executor must not silently redesign.

- ...

## 6. Implementation Requirements

1. ...
2. ...

Prefer observable behavior and necessary architectural constraints over brittle line-by-line instructions.

## 7. Likely Affected Areas

- `path/to/file`
- `path/to/module/`

This is guidance, not an exclusive allowlist unless explicitly stated.

## 8. Constraints

- ...

Examples include no new dependencies, preserve a public API, avoid schema changes, maintain compatibility, or leave unrelated modules untouched.

## 9. Verification Plan

```sh
# exact commands when known
```

Include targeted manual checks when automated verification is insufficient.

## 10. Acceptance Criteria

- [ ] ...
- [ ] Required tests/checks pass.
- [ ] No stated constraint or non-goal is violated.

## 11. Open Questions

List unresolved questions. If none remain for an approved plan, write:

`None.`

Material unresolved questions normally require `Draft` status.

## 12. Execution Notes

Optional execution-only information that does not change the design, such as environment setup or generated-file rules.

## 13. Change Log

Record material changes to a previously shared plan.
```

## Status rules

- `Draft`: not yet ready or not yet approved.
- `Approved`: ready for local execution.
- `Implemented`: optional historical state after implementation review.
- `Superseded`: replaced by another plan; link or name the replacement.

The user must explicitly approve movement from `Draft` to `Approved` unless that approval is already unambiguous in the planning conversation.
