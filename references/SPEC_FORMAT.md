# Implementation Spec Format

Use this format for `.chatgpt/specs/YYYY-MM-DD-short-description.md`.

The body preserves the original To Spec structure. The metadata block is a GitHub handoff adaptation for this workflow.

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

Describe the problem that the user is facing, from the user's perspective.

## Solution

Describe the solution to the problem, from the user's perspective.

## User Stories

Write a LONG, numbered list of user stories. The list should be extremely extensive and cover all aspects of the feature, including meaningful boundary and edge cases.

Each user story should use this form:

1. As an <actor>, I want a <feature>, so that <benefit>.

## Implementation Decisions

Record the implementation decisions that were made. This may include:

- modules that will be built or modified;
- interfaces of those modules that will be modified;
- technical clarifications from the developer;
- architectural decisions;
- schema changes;
- API contracts;
- specific interactions.

Do NOT include specific file paths or code snippets. They may become outdated quickly.

Exception: if a prototype produced a snippet that encodes a decision more precisely than prose can—for example a state machine, reducer, schema, or type shape—inline only the decision-rich fragment and note briefly that it came from a prototype. Do not include a working demo.

## Testing Decisions

Record the testing decisions that were made. Include:

- what makes a good test for this feature: test external behavior, not implementation details;
- which modules will be tested;
- the testing seam or seams confirmed with the user;
- prior art for the tests, such as similar existing tests in the codebase.

Prefer existing seams to new ones. Use the highest seam possible. If new seams are needed, place them at the highest practical point. Fewer seams are better; the ideal number is one when one seam adequately verifies the behavior.

## Out of Scope

Describe the things that are explicitly outside this spec.

## Further Notes

Include any further execution-relevant notes about the feature.
```

## Status rules

### Draft

Use when the spec is being shared for review or user approval is not yet clear.

### Ready for implementation

Use only when the user has approved the design, or approval is already unambiguous, and no material design decision remains unresolved.

A ready spec must be sufficient for an implementation agent that can inspect the repository but cannot see the earlier ChatGPT conversation.
