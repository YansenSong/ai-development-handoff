# CONTEXT.md Format

`CONTEXT.md` is a domain glossary. It must not contain implementation details.

## Structure

```md
# {Context Name}

{One or two sentences describing the context and why it exists.}

## Language

**Order**:
A business commitment representing a customer's request for fulfillment.
_Avoid_: Purchase, transaction

**Invoice**:
A request for payment sent to a customer after delivery.
_Avoid_: Bill, payment request
```

## Rules

- Be opinionated: choose one canonical term when synonyms exist and list discouraged alternatives under `_Avoid_`.
- Keep each definition to one or two sentences.
- Define what a concept **is**, not how the code implements it.
- Include only concepts specific to this project's domain/context.
- Do not add generic programming concepts merely because the code uses them.
- Group terms under subheadings when natural clusters emerge.

## Single vs multi-context repositories

For most repositories, use one root `CONTEXT.md`.

For multiple bounded contexts, use a root `CONTEXT-MAP.md` that points to context-specific `CONTEXT.md` files and records their relationships.

Example:

```md
# Context Map

## Contexts

- [Ordering](./src/ordering/CONTEXT.md): receives and tracks customer orders
- [Billing](./src/billing/CONTEXT.md): generates invoices and processes payments

## Relationships

- **Ordering → Billing**: Ordering emits an event consumed by Billing.
```

Create context files lazily when there is durable vocabulary worth recording.
