---
name: ai-development-handoff
description: Turn repository-grounded discussion into a complete implementation spec for a local coding agent. Use when ChatGPT should clarify requirements, stress-test design and boundaries, maintain domain/ADR documentation, synthesize the agreed solution into a GitHub spec, and hand that spec to Codex for implementation. This skill stops before code implementation.
---

# AI Development Handoff

Use this skill on the **planning side** of a software change.

Its job is to combine two disciplines into one ChatGPT workflow:

1. **Grill with docs** — inspect the real repository, interview the user until important design decisions are explicit, sharpen domain language, and capture durable domain/architecture knowledge.
2. **To spec** — once the design is settled, synthesize the current conversation and codebase understanding into an implementation-ready spec, including an explicit testing-seam confirmation step.

The workflow ends when the spec and any durable supporting documents are published to GitHub. **Do not implement the feature under this skill.** Local implementation belongs to a separately installed implementation skill such as `implement` in Codex.

The normal flow is:

```text
ChatGPT + GitHub
      ↓
repository understanding
      ↓
grill with docs
      ├── design tree / one frontier question at a time
      ├── domain vocabulary
      └── ADR candidates
      ↓
shared understanding / readiness gate
      ↓
to spec
      ├── confirm testing seams
      ├── problem + solution
      ├── extremely extensive user stories
      ├── implementation decisions
      ├── testing decisions
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

The planner owns repository investigation, fact finding, requirement clarification, design grilling, implementation boundaries, domain-model clarification, durable context/ADR documentation when justified, testing-seam proposal and confirmation, and synthesis/publication of the final spec.

The planner does **not** own production-code implementation, running the implementation agent, or silently filling material design gaps.

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

Interview the user relentlessly until you reach a shared understanding. Map the unresolved design as a **design tree**: every decision branches into the decisions that hang off it.

The **frontier** is every decision whose prerequisites are already settled: the questions that could be asked _now_ without guessing at answers that have not been heard yet.

For this ChatGPT workflow, traverse that frontier **one question at a time**.

Before every grilling question:

1. recompute the entire design tree from the latest user answer and repository evidence;
2. recompute the current frontier;
3. select one decision from that frontier;
4. ask **only that one question**;
5. give a recommended answer with concise reasoning;
6. wait for the user's response before asking anything else.

Do **not** batch multiple decision questions into one message, even when several decisions are simultaneously on the frontier. A user's answer to the current question may reshape, remove, split, merge, or reprioritize questions that previously appeared independent.

A grilling turn should look like this:

```text
❓ Q<n> — <question title>: <question body, possibly including choices>

➡️ <your recommended answer>
```

You may number questions sequentially for traceability, but each assistant turn contains only one unresolved decision question.

After every user answer:

- incorporate the decision into the design tree;
- update domain-model artifacts immediately when Phase 3 applies;
- investigate any newly required repository facts yourself;
- recompute the frontier from scratch;
- ask the next single frontier question only after that recomputation.

A question whose answer depends on another unresolved decision is **not** on the frontier yet and must wait.

Finding **facts** is ChatGPT's job, never the user's. When a possible frontier question depends on a fact from the repository or available environment, use GitHub and available tools to find it rather than asking the user. Treat unresolved fact-finding as an unsettled prerequisite: only decisions downstream of that fact must wait.

The **decisions** are the user's. Put each decision to the user one at a time and wait for their answer.

The grilling session is done when the frontier is empty: every branch of the design tree has been visited and nothing remains silently assumed.

Do not move on until the user confirms that you have reached a shared understanding.

---

# Phase 3 — Domain modeling while grilling

Actively build and sharpen the project's domain model as you design. This is an **active discipline**: challenge terms, invent edge-case scenarios, and record glossary entries and durable decisions the moment they crystallize.

Merely reading an existing `CONTEXT.md` for vocabulary is not domain modeling. Use this phase when the conversation is changing or sharpening the model itself.

## File structure

Most repositories have a single context:

```text
/
├── CONTEXT.md
├── docs/
│   └── adr/
│       ├── 0001-some-decision.md
│       └── 0002-another-decision.md
└── src/
```

If a root `CONTEXT-MAP.md` exists, treat the repository as having multiple contexts. The map points to each context and its relationships:

```text
/
├── CONTEXT-MAP.md
├── docs/
│   └── adr/                          # system-wide decisions
└── src/
    ├── ordering/
    │   ├── CONTEXT.md
    │   └── docs/adr/                 # context-specific decisions
    └── billing/
        ├── CONTEXT.md
        └── docs/adr/
```

Create files lazily. If no `CONTEXT.md` exists, create one when the first domain term is resolved. If no ADR directory exists, create it only when the first ADR is actually justified.

Use `references/CONTEXT_FORMAT.md` and `references/ADR_FORMAT.md` for artifact shape.

## During the session

### Challenge against the glossary

When the user uses a term that conflicts with the existing language in `CONTEXT.md`, call it out immediately and ask which meaning is intended.

### Sharpen fuzzy language

When the user uses vague or overloaded terms, propose a precise canonical term and distinguish it from nearby concepts.

### Discuss concrete scenarios

When domain relationships are being discussed, stress-test them with specific scenarios. Invent scenarios that probe edge cases and force precision about the boundaries between concepts.

### Cross-reference with code

When the user states how something works, check whether the repository agrees. If the code contradicts the stated domain behavior, surface the contradiction immediately rather than silently choosing one interpretation.

### Update CONTEXT.md inline

When a term is resolved, record it in the current `CONTEXT.md` artifact state **right then**. Do not defer glossary capture until the end of the grilling session or until spec synthesis.

`CONTEXT.md` must be totally devoid of implementation details. Do not treat it as a spec, scratch pad, implementation plan, or repository for technical decisions. It is a glossary and nothing else.

In the ChatGPT → GitHub environment, “update inline” means the resolved glossary content is updated as part of the current working planning artifacts immediately. It does **not** require a separate Git commit for every resolved term; GitHub publication timing is handled in Phase 6.

### Offer ADRs sparingly

Only offer to create an ADR when all three are true:

1. **Hard to reverse** — the cost of changing the decision later is meaningful.
2. **Surprising without context** — a future reader would reasonably wonder why this approach was chosen.
3. **The result of a real trade-off** — there were genuine alternatives and one was chosen for specific reasons.

If any of the three is missing, skip the ADR. Keep ordinary task-specific implementation decisions in the spec instead.

ADRs are durable architectural memory; the spec is the task-specific implementation contract. Do not duplicate every spec decision into an ADR.

---

# Phase 4 — Ready-to-spec gate

After the grilling frontier is empty and the user has confirmed shared understanding, perform a separate completeness check before switching to To Spec.

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
- no unresolved decision remains that could materially change the implementation approach.

This gate is **not part of the grilling question-ordering protocol**. Do not use it to impose a fixed priority order or checklist-driven interview on Phase 2.

Testing-seam confirmation is intentionally not required by this gate: the original To Spec method performs that narrow confirmation itself in Phase 5.

If this check reveals a material decision that was missed, treat it as a newly discovered branch in the design tree, return to Phase 2, and resume the normal one-question frontier process. After that branch is resolved and the user reconfirms shared understanding, run this gate again.

Once this gate is satisfied, move to To Spec.

---

# Phase 5 — To spec

Take the current conversation context and codebase understanding and produce the spec. **Do not start another general interview.** Synthesize what has already been discussed, with the one narrow confirmation step for testing seams described below.

## Step 1 — Explore the repository if needed

Explore the repository to understand the current state of the codebase if that work has not already been done, or refresh relevant facts if the repository may have changed during a long planning session.

Use the project's domain glossary vocabulary throughout the spec and respect relevant ADRs in the area being changed.

## Step 2 — Sketch and confirm testing seams

Sketch the seams at which the feature should be tested.

- Prefer existing seams to new ones.
- Use the **highest seam possible**.
- If new seams are needed, propose them at the highest point possible.
- The fewer seams across the codebase, the better. The **ideal number is one** when one seam can adequately verify the behavior.
- Tests should verify external behavior rather than implementation details.
- Use similar existing tests in the repository as prior art when available.

**Check with the user that these seams match their expectations before writing the spec.**

This testing-seam check is part of To Spec itself; do not force it into the earlier Grilling protocol.

If the user's response merely confirms or selects among the proposed seams, continue directly to writing the spec. If the response exposes a broader unresolved product or design decision, return that newly discovered decision to the normal design-tree/frontier process, re-establish shared understanding, then resume To Spec.

## Step 3 — Write the spec

Use `references/SPEC_FORMAT.md`.

Specs live at:

```text
.chatgpt/specs/YYYY-MM-DD-short-description.md
```

Create `.chatgpt/specs/` lazily when the first spec is published.

The spec must be understandable by an implementation agent that has the repository but not the earlier conversation.

### Problem Statement

Describe the problem from the user's perspective.

### Solution

Describe the solution from the user's perspective.

### User Stories

Write a **LONG, numbered list** of user stories. The list should be **extremely extensive and cover all aspects of the feature**, including meaningful edge cases and boundary behavior.

Use the form:

```text
1. As an <actor>, I want a <feature>, so that <benefit>.
```

### Implementation Decisions

Record the implementation decisions that were actually made. These may include:

- modules that will be built or modified;
- interfaces of those modules that will change;
- technical clarifications from the developer;
- architectural decisions;
- schema changes;
- API contracts;
- specific interactions.

**Do NOT include specific file paths or code snippets.** They may become outdated quickly.

Exception: if a prototype produced a snippet that encodes a decision more precisely than prose can—for example a state machine, reducer, schema, or type shape—inline only the decision-rich fragment and note briefly that it came from a prototype. Do not include a working demo.

### Testing Decisions

Record the testing decisions that were made, including:

- what makes a good test for this feature: verify external behavior, not implementation details;
- which modules will be tested;
- the confirmed testing seam or seams;
- prior art for the tests, such as similar existing tests in the codebase.

### Out of Scope

Describe the things that are explicitly outside this spec.

### Further Notes

Include any further execution-relevant notes about the feature.

## Handoff metadata adaptation

The original To Spec method publishes to an issue tracker. This workflow instead publishes a version-controlled spec document for Codex, so `references/SPEC_FORMAT.md` adds these handoff fields:

- Status;
- Repository;
- Branch;
- Base commit;
- Created / Updated dates.

These fields are a GitHub handoff adaptation, not part of the core To Spec synthesis method.

A published spec should be `Ready for implementation` only after the user has approved the design or that approval is already unambiguous. Otherwise publish it as `Draft`.

---

# Phase 6 — Publish the GitHub handoff

When the user asks to publish the result:

1. identify the target repository and branch;
2. record the source revision used for planning as the **Base commit**;
3. publish the current coherent `CONTEXT.md`, `CONTEXT-MAP.md`, and/or ADR artifact state that was recorded during domain modeling;
4. create the spec under `.chatgpt/specs/`;
5. commit the documentation changes to GitHub, batching coherent planning artifacts when appropriate rather than creating a commit for every resolved term;
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

- `references/SPEC_FORMAT.md` — final implementation spec format and GitHub handoff metadata adaptation.
- `references/CONTEXT_FORMAT.md` — domain glossary format.
- `references/ADR_FORMAT.md` — ADR format and threshold.
