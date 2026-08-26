# Implementation Spec Format

Use this format for `.chatgpt/specs/YYYY-MM-DD-short-description.md`.

```md
# Spec: <Short Title>

## Metadata

- **Status:** Draft | Ready for implementation
- **Repository:** `<owner>/<repo>`
- **Branch:** `<branch>`
- **Base commit:** `<full commit SHA>`
- **Created:** `YYYY-MM-DD`
- **Updated:** `YYYY-MM-DD`

## Problem Statement

Describe the problem from the user's perspective. Explain the pain, limitation, or desired capability rather than jumping directly to code changes.

## Solution

Describe the agreed solution from the user's perspective: what will become possible or behave differently after implementation.

## User Stories

Write an extensive numbered list covering the feature thoroughly, including meaningful edge cases and boundary behavior.

1. As an <actor>, I want <behavior>, so that <benefit>.

## Implementation Decisions

Record decisions already made during planning, such as module/component ownership, interfaces/contracts, technical clarifications, architecture choices, schema/data decisions, API/event/command behavior, state transitions, failure behavior, compatibility, permissions/security, and operational constraints.

Do not write brittle line-by-line instructions. Do not include specific file paths merely to direct edits. Stable module or interface names are fine when they are part of the design.

Avoid code snippets. Exception: a compact prototype-derived shape may be included when it captures a decision more precisely than prose; include only the decision-rich fragment and note why it is present.

## Testing Decisions

Describe what external behavior tests must prove, the chosen testing seam(s), why those seams are appropriate, which stable modules/interfaces are exercised, and similar repository tests/patterns that should be used as prior art when known.

Prefer existing seams and the highest practical seam. Prefer fewer seams when they verify the behavior adequately.

## Out of Scope

State related work that is explicitly not part of this spec, concretely enough that the implementation agent does not expand the task opportunistically.

## Further Notes

Include only additional execution-relevant context that does not fit above, such as rollout notes, compatibility context, references to relevant ADRs/domain context, or intentionally deferred follow-up work.
```

## Status rules

### Draft

Use when the spec is being shared for review or user approval is not yet clear.

### Ready for implementation

Use only when the user has approved the design, or approval is already unambiguous, and no material design decision remains unresolved.

A ready spec must be sufficient for an implementation agent that can inspect the repository but cannot see the earlier ChatGPT conversation.
