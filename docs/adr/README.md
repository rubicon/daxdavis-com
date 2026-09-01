# Decision records

Decision records for daxdavis.com, in the [MADR 4.0.0](https://adr.github.io/madr/)
minimal format. Since MADR 3.0.0 the acronym means "Markdown Any Decision
Records", so these cover any decision meeting the trigger below, not only
architectural ones.

## Index

| Record | Title | Status |
| --- | --- | --- |
| [0001](0001-site-framework-and-content-model.md) | Site framework and content model | proposed |
| [0002](0002-hosting-and-deploy-target.md) | Hosting and deploy target | proposed |

## When to write one

Write a record when any of these fires:

1. **Investigation cost.** The decision was established by reading source,
   running an experiment, or reverse-engineering an external contract, and a
   future maintainer cannot re-derive it from the code alone.
2. **Rejected alternative.** A plausible alternative was considered and
   rejected. Absent a record it gets re-proposed and re-litigated.
3. **External constraint.** An outside contract (host API, platform limit,
   licence term) forces a shape that looks arbitrary in isolation.
4. **Reversal.** A previously recorded decision is being superseded.

The first three are judgment calls, so these always count regardless of how the
judgment lands: a change to a public API or external contract; adding or
removing a required tool, host, or platform dependency; rejecting an alternative
named in an issue, PR, or review; a change to repository process; superseding an
existing record.

If none fire, the code plus `ARCHITECTURE.md` is enough. A decision that is
obvious from reading the code is not a record.

## Conventions

One decision per file, named `NNNN-kebab-slug.md`, four-digit and zero-padded.
Numbers are monotonic and never reused.

Status is one of `proposed`, `accepted`, `rejected`, `deprecated`, or
`superseded by NNNN`. The maintainer sets status at merge.

Records are decision-immutable. Once accepted, the decision, its alternatives,
and its consequences change only through a new record that supersedes this one.
Editorial fixes (typos, dead links, formatting, corrected citations) are allowed
and encouraged. A fix that changes a factual claim is logged in a `Corrections`
block at the end of the record with the date, what changed, and why. Preserving
the reversal is the point of the format.

One home per fact. `ARCHITECTURE.md` carries what and where; records carry why,
alternatives, and consequences. Where both touch the same subject,
`ARCHITECTURE.md` links to the record rather than restating it.

The repository is canonical. External notes, agent memory, and chat summaries may
link here, but are never the source of record.
