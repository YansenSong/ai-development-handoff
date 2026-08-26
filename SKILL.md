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

Interview the user relentlessly until you reach a shared understanding. Map the unresolved design as a **design tree**: every decision branches into the decisions that hang off it.

Work the tree in **rounds**. The **frontier** is every decision whose prerequisites are already settled: the questions you can ask _now_ without guessing at answers you have not heard yet.

Ask the **whole frontier** in one round. Number each question and give your recommended answer. Then wait for the user's answers before the next round.

Format a round like this:

```text
❓ Q1 — <question title>: <question body, possibly including choices>

➡️ <your recommended answer>

---

❓ Q2 — <question title>: <question body, possibly including choices>

➡️ <your recommended answer>
```

Each round of user answers reshapes the tree: settled decisions push the frontier outward and unblock questions that depended on them. Recompute the frontier and ask the next round.

A question whose answer depends on another question that is still open in the current round belongs to a **later round**, not the current one.

Finding **facts** is ChatGPT's job, never the user's. When a frontier question depends on a fact from the repository or available environment, use GitHub and available tools to find it rather than asking the user. Treat unresolved fact-finding as an unsettled prerequisite: only questions downstream of that fact must wait; ask the rest of the frontier normally.

The **decisions** are the user's. Put each decision to the user and wait for their answer.

The grilling session is done when the frontier is empty: every branch of the design tree has been visited and nothing remains silently assumed.

Do not move on to synthesis until the user confirms that you have reached a shared understanding.

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

Example pattern:

```text
Your glossary defines “cancellation” as X, but you seem to mean Y. Which is it?
```

### Sharpen fuzzy language

When the user uses vague or overloaded terms, propose a precise canonical term and distinguish it from nearby concepts.

Example pattern:

```text
You're saying “account”: do you mean the Customer or the User? Those are different concepts.
```

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

After the grilling frontier is empty and the user has confirmed shared understanding, perform a separate completeness check before switching to synthesis.

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

This gate is **not part of the grilling question-ordering protocol**. Do not use it to impose a fixed priority order or checklist-driven interview on Phase 2.

If this check reveals a material decision that was missed, treat it as a newly discovered branch in the design tree, return to Phase 2, and resume the normal frontier process. After that branch is resolved and the user reconfirms shared understanding, run this gate again.

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

The test seam should normally have been discussed during grilling. If synthesis reveals that a material seam was never agreed, do **not** silently invent it. Return only that unresolved decision to the user, settle it through the normal design-tree/frontier process, then resume synthesis.

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

- `references/SPEC_FORMAT.md` — final implementation spec format.
- `references/CONTEXT_FORMAT.md` — domain glossary format.
- `references/ADR_FORMAT.md` — ADR format and threshold.
