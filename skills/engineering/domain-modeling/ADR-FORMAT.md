# ADR Format

ADRs live in `docs/adr/` and use sequential numbering: `0001-slug.md`, `0002-slug.md`, etc.

Create the `docs/adr/` directory lazily: only when the first ADR is needed.

## Template

```md
# {Short title of the decision}

## Context

{What's the situation? What forces or constraints are driving this?}

## Decision

{What we decided, and why it wins given the context and the alternatives below.}

## Alternatives Considered

{What options were on the table, including the ones we rejected, and why each one was rejected or found wanting.}

## Consequences

{What becomes easier or harder as a result of this decision? Include downsides accepted, not just benefits. This is what future readers most need to know.}
```

That's the order the finished document reads in, but it's not the order to *write* it in. Work out Context and Alternatives Considered first, in that order, before drafting Decision. Don't write Decision first and backfill the other two to justify it: think through the situation and the real options honestly, then let the decision fall out of that, and only then slot it into its place above Alternatives Considered in the final file. If the trade-off doesn't survive being written out honestly, that's a sign the decision needs rethinking, not that the section is optional. Consequences comes last, once the decision is settled, since it's a description of what follows from it.

## Optional sections

Only include this when it adds genuine value. Most ADRs won't need it.

- **Status** frontmatter (`proposed | accepted | deprecated | superseded by ADR-NNNN`): useful when decisions are revisited

## Numbering

Scan `docs/adr/` for the highest existing number and increment by one.

## When to offer an ADR

All three of these must be true:

1. **Hard to reverse**: the cost of changing your mind later is meaningful
2. **Surprising without context**: a future reader will look at the code and wonder "why on earth did they do it this way?"
3. **The result of a real trade-off**: there were genuine alternatives and you picked one for specific reasons

If a decision is easy to reverse, skip it: you'll just reverse it. If it's not surprising, nobody will wonder why. If there was no real alternative, there's nothing to record beyond "we did the obvious thing."

### What qualifies

- **Architectural shape.** "We're using a monorepo." "The write model is event-sourced, the read model is projected into Postgres."
- **Integration patterns between contexts.** "Ordering and Billing communicate via domain events, not synchronous HTTP."
- **Technology choices that carry lock-in.** Database, message bus, auth provider, deployment target. Not every library: just the ones that would take a quarter to swap out.
- **Boundary and scope decisions.** "Customer data is owned by the Customer context; other contexts reference it by ID only." The explicit no-s are as valuable as the yes-s.
- **Deliberate deviations from the obvious path.** "We're using manual SQL instead of an ORM because X." Anything where a reasonable reader would assume the opposite. These stop the next engineer from "fixing" something that was deliberate.
- **Constraints not visible in the code.** "We can't use AWS because of compliance requirements." "Response times must be under 200ms because of the partner API contract."
- **Rejected alternatives when the rejection is non-obvious.** If you considered GraphQL and picked REST for subtle reasons, record it; otherwise someone will suggest GraphQL again in six months.
