# ADR Format

Use ADRs only for durable decisions that are hard to reverse, surprising without context, and the result of a real trade-off.

ADRs normally live in `docs/adr/` and use sequential numbering:

```text
docs/adr/0001-short-slug.md
```

Create the directory lazily when the first ADR is justified.

## Minimal format

```md
# {Short title of the decision}

{1–3 sentences describing the context, what was decided, and why.}
```

That is enough for most ADRs.

## Optional sections

Add these only when they provide real value:

- **Status**: `proposed | accepted | deprecated | superseded by ADR-NNNN`
- **Considered Options**
- **Consequences**

## Good ADR candidates

- architectural shape with meaningful lock-in;
- integration pattern between contexts;
- major technology choices that are expensive to replace;
- ownership or scope boundaries;
- deliberate deviations from the obvious approach;
- constraints not visible in code;
- non-obvious rejection of a plausible alternative.

Do not create ADRs for routine, reversible, or obvious implementation details.
