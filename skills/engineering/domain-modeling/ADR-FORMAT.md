# ADR Format

ADRs live in `docs/adr/` and use sequential numbering: `0001-slug.md`, `0002-slug.md`, etc.

Create the `docs/adr/` directory lazily: only when the first ADR is needed.

## Template

```md
---
proposed_date: YYYY-MM-DD
supersedes: []   # e.g. ["0042"], if this ADR supersedes one or more earlier ones
tags: []         # e.g. [database, security]
---

# {Short title of the decision}

## Context

{Situation: the stable state of things before this came up.}

{Complication: what changed or what tension makes the status quo no longer tenable, implying the question this ADR has to answer.}

## Decision

{Answer: what we decided, and why it wins given the context and the alternatives below.}

## Alternatives Considered

{What options were on the table, including the ones we rejected, and why each one was rejected or found wanting.}

## Consequences

{What becomes easier or harder as a result of this decision? Include downsides accepted, not just benefits. This is what future readers most need to know.}
```

Context and Decision follow SCQA (Situation, Complication, Question, Answer): the two Context paragraphs are the Situation and the Complication, the Question is the thing that Complication implies but stays unwritten, and Decision is the Answer to it. A Context that doesn't leave an obvious question hanging isn't done yet.

That's the order the finished document reads in, but it's not the order to *write* it in. Work out Context and Alternatives Considered first, in that order, before drafting Decision. Don't write Decision first and backfill the other two to justify it: think through the situation and the real options honestly, then let the decision fall out of that, and only then slot it into its place above Alternatives Considered in the final file. If the trade-off doesn't survive being written out honestly, that's a sign the decision needs rethinking, not that the section is optional. Consequences comes last, once the decision is settled, since it's a description of what follows from it.

The frontmatter starts with `proposed_date` set to today. Leave `supersedes` and `tags` as empty arrays unless there's a real earlier ADR being reversed or an obvious tag to add; don't invent tags for the sake of filling the field.

## Superseding an ADR

If the new ADR's `supersedes` list isn't empty, go back and edit each ADR it names: add a `superseded_by` field to that file's frontmatter, pointing at the new ADR's number.

```md
superseded_by: ["0051"]   # set once a later ADR supersedes this one
```

Don't include `superseded_by` in a fresh ADR's own frontmatter: an ADR can't know at birth that it will one day be superseded, so the field only ever gets added later, by whichever ADR does the superseding.

## Status

There's no `status` field: status is derived from where the ADR lives, not stored. While it only exists on an open PR, it's implicitly proposed. Once that PR merges to `main`, it's accepted. Once another ADR names it in `supersedes` (and it picks up `superseded_by`), it's superseded. This only works if ADRs go through PR review. If one ever lands by a direct commit to `main`, it skips the proposed state entirely and is accepted from the moment it exists.

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
