---
name: ai-development-handoff
description: Turn repository-grounded discussion into a complete implementation spec for a local coding agent. Use when ChatGPT should clarify requirements, stress-test design and boundaries, maintain domain/ADR documentation, synthesize the agreed solution into a GitHub spec, and hand that spec to Codex for implementation. This skill stops before code implementation.
---

# AI Development Handoff

Use this skill on the **planning side** of a software change.

Its job is to combine two disciplines into one ChatGPT workflow:

1. **Grill with docs** — inspect the real repository, interview the user until important design decisions are explicit, sharpen domain language, and capture durable domain/architecture knowledge.
2. **To spec** — once the design is settled, stop interviewing and synthesize the existing conversation plus repository evidence into an implementation-ready spec.

The workflow ends when the spec and any durable supporting documents are published to GitHub. **Do not implement the feature under this skill.** Local implementation belongs to a separately installed implementation skill such as `implement` in Codex.

The normal flow is:

```text
ChatGPT + GitHub
      ↓
repository understanding
      ↓
grill with docs
      ├── design tree / frontier questions
      ├── domain vocabulary
      └── ADR candidates
      ↓
shared understanding / readiness gate
      ↓
to spec
      ├── problem + solution
      ├── exhaustive user stories
      ├── implementation decisions
      ├── testing decisions / seams
      └── out of scope
      ↓
GitHub: .chatgpt/specs/*.md
      ↓
local git pull
      ↓
Codex + separately installed implement skill
```

## Role boundary

### ChatGPT / planner

The planner owns repository investigation, fact finding, requirement clarification, design grilling, implementation boundaries, domain-model clarification, durable context/ADR documentation when justified, test-seam decisions, and synthesis/publication of the final spec.

The planner does **not** own production-code implementation, running the implementation agent, or silently filling material design gaps during synthesis.

If the user explicitly asks for a different workflow that includes direct code edits, that is outside this skill.

### GitHub / handoff store

GitHub stores the durable planning artifacts:

```text
.chatgpt/specs/YYYY-MM-DD-short-description.md
CONTEXT.md                                  # when durable domain vocabulary exists
docs/adr/NNNN-short-decision.md             # when a real ADR is justified
```

For multi-context repositories, domain context may instead use a root `CONTEXT-MAP.md` plus context-specific `CONTEXT.md` files as described in `references/CONTEXT_FORMAT.md`.

### Codex / executor

Codex consumes the published spec using its separately installed implementation workflow. The spec must therefore be sufficient for a fresh executor that did not participate in the ChatGPT conversation.

Do not rely on Codex reconstructing missing decisions from chat history.

---

# Phase 1 — Understand the repository first

Before interviewing the user about implementation details, inspect the target repository and establish the relevant facts.

Typical facts include current architecture and ownership boundaries, public interfaces and callers, affected behavior, state/persistence, errors and compatibility, existing tests and test helpers, testing seams and prior art, runtime/tooling constraints, domain vocabulary, and relevant ADRs.

Use this distinction rigorously:

- **Repository fact** → investigate it yourself.
- **Unknown code behavior** → inspect the source.
- **User preference** → ask the user.
- **Product requirement** → ask when unclear.
- **Design trade-off** → explain options, recommend one, and ask the user to decide.

Do not ask the user to remember facts that can reasonably be retrieved from GitHub or available tools.

If repository evidence contradicts the user's description, surface the contradiction before continuing.

---

# Phase 2 — Grill with docs

Treat the unresolved design as a **design tree**.

A decision can unlock dependent decisions. The **frontier** is the set of questions whose prerequisites are already settled and which can therefore be answered now without guessing.

Work in rounds. For each round:

1. recompute the frontier from the latest answers and repository evidence;
2. prioritize decisions that materially affect architecture, scope, interfaces, data, security, compatibility, failures, or testing;
3. normally ask 3–5 substantive questions at a time rather than dumping the entire tree on the user;
4. number each question;
5. give a recommended answer and concise rationale;
6. wait for the user's decisions before asking dependent questions.

A useful priority order is:

1. problem and product behavior;
2. ownership and architecture boundaries;
3. public interfaces and compatibility;
4. data, persistence, security, and permissions;
5. failure behavior and state transitions;
6. test seams and observability;
7. lower-level implementation boundaries;
8. naming and cosmetic choices.

The user owns decisions. ChatGPT owns fact finding.

## What must be stress-tested

Evaluate these dimensions for relevance; do not mechanically ask all of them for every task:

- exact goal and user-visible outcome;
- non-goals and scope boundary;
- actors and user stories;
- inputs and outputs;
- ownership/module boundaries;
- API/event/schema contracts;
- state transitions and lifecycle;
- data ownership, persistence, migration, and retention;
- failures, retries, cancellation, partial success, and edge cases;
- authorization, trust, and permission boundaries;
- backward compatibility and rollout constraints;
- concurrency/idempotency when relevant;
- operational/platform constraints;
- observability when relevant;
- testing strategy and test seams.

Planning is complete only when no material branch is silently assumed.

---

# Phase 3 — Domain modeling while grilling

Domain modeling runs alongside Phase 2 when terminology or ownership boundaries matter.

If the repository has `CONTEXT.md`, use its language consistently. If the user uses a conflicting term, call it out immediately. When language is vague or overloaded, propose a precise canonical term and distinguish it from nearby concepts. Use concrete scenarios to pressure-test domain relationships, and cross-check claimed behavior against the source.

## CONTEXT.md

Create or update domain context only when durable project-specific vocabulary is actually resolved.

`CONTEXT.md` is a glossary. It must remain free of implementation details. Use `references/CONTEXT_FORMAT.md`.

Capture resolved terms during the conversation, but do not create a separate Git commit for every sentence. Publish coherent context updates at a planning checkpoint or together with the final spec.

## ADRs

Create an ADR only when all three are true:

1. the choice is meaningfully hard to reverse;
2. a future engineer would find the choice surprising without explanation;
3. there was a real trade-off between plausible alternatives.

If any condition is missing, keep the decision in the spec rather than creating an ADR. Use `references/ADR_FORMAT.md`.

ADRs are durable architectural memory; the spec is the task-specific implementation contract. Do not duplicate every spec decision into an ADR.

---

# Phase 4 — Ready-to-spec gate

Before switching from grilling to synthesis, confirm that the feature is ready to specify.

Relevant dimensions should be settled:

- the problem is understood from the user's perspective;
- the proposed solution is explicit;
- important user stories and edge cases are understood;
- scope and out-of-scope behavior are explicit;
- current repository behavior has been verified;
- implementation boundaries and important contracts are decided;
- compatibility expectations are decided;
- meaningful failure behavior is decided;
- persistence/security/permissions are decided when relevant;
- testing intent and candidate seams are understood;
- no unresolved decision remains that could materially change the implementation approach.

If a material design decision remains unresolved, continue grilling. Do not move to spec just because the conversation is lengthy.

Once this gate is satisfied, **stop broad interviewing** and move to synthesis.

---

# Phase 5 — To spec

This phase synthesizes what has already been learned. It is not another general interview.

Before writing the spec:

1. refresh repository facts if the planning conversation was long or the repository may have changed;
2. use the project's canonical domain vocabulary;
3. respect relevant ADRs;
4. identify the testing seams implied by the design and existing code.

## Testing seams

Prefer existing seams over introducing new ones. Use the highest practical seam that verifies external behavior rather than implementation details. Prefer fewer seams; one high-value seam is better than many low-level seams when it covers behavior well. Use similar existing tests as prior art.

The test seam should normally have been discussed during grilling. If synthesis reveals that a material seam was never agreed, do **not** silently invent it. Return only that unresolved decision to the user, settle it, then resume synthesis.

## Spec rules

Use `references/SPEC_FORMAT.md`.

Specs live at:

```text
.chatgpt/specs/YYYY-MM-DD-short-description.md
```

Create `.chatgpt/specs/` lazily when the first spec is published.

The spec must be understandable by an implementation agent that has the repository but not the earlier conversation.

It must describe the problem and solution from the user's perspective, contain an extensive numbered user-story list including meaningful edge cases, record implementation decisions already made, record testing decisions and seams, state out-of-scope work, record repository/branch/base commit, and contain enough detail to prevent the executor from needing to redesign the feature.

Do not paste the conversation transcript. Do not include brittle line-by-line implementation instructions. Do not include specific file paths merely to direct edits; stable module/component/interface names are fine when they are part of the design.

Avoid code snippets. Exception: a compact prototype-derived shape may be included when it captures a decision more precisely than prose. Include only the decision-rich fragment and note why it is present.

## Readiness status

A published handoff spec should normally be `Ready for implementation` only after the user has explicitly approved the design, unless approval is already unambiguous in the conversation.

If it is still being shared for review, publish it as `Draft` and do not describe it as ready for Codex.

---

# Phase 6 — Publish the GitHub handoff

When the user asks to publish the result:

1. identify the target repository and branch;
2. record the source revision used for planning as the **Base commit**;
3. create/update coherent `CONTEXT.md` or ADR artifacts justified by the discussion;
4. create the spec under `.chatgpt/specs/`;
5. commit documentation changes to GitHub;
6. do not edit production source code as part of this workflow.

The Base commit describes the source revision against which the solution was reasoned. The documentation commit that adds the spec will naturally move repository HEAD beyond that base.

Do not put secrets, credentials, tokens, or private local filesystem paths in these artifacts.

After publication, tell the user the exact spec path that Codex should consume.

Typical local handoff:

```text
Pull the latest repository, then use the separately installed implement skill to implement:
.chatgpt/specs/YYYY-MM-DD-short-description.md
```

---

# Phase 7 — Stop

This skill ends after planning artifacts are published.

Do not implement the production change under this skill. Do not duplicate the local `implement` workflow here.

If implementation later reveals a material missing decision or invalid assumption, return to this planning workflow, inspect the new repository state, revise the spec, and publish a new revision before implementation continues.

---

# Artifact separation

```text
CONTEXT.md
= What does this domain language mean?

docs/adr/*.md
= Why did we make this durable architectural decision?

.chatgpt/specs/*.md
= What exactly should the implementation agent build for this piece of work?
```

The spec may reference relevant domain terms and ADRs, but should not duplicate their full content.

# Supporting references

- `references/SPEC_FORMAT.md` — final implementation spec format.
- `references/CONTEXT_FORMAT.md` — domain glossary format.
- `references/ADR_FORMAT.md` — ADR format and threshold.
