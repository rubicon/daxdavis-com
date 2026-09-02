# Site design spec

Status: draft, pending maintainer agreement
Date: 2026-09-02
Author: Dax Davis

Decides what the site *is*. The stack it runs on is decided separately in
[`docs/adr/0001`](../adr/0001-site-framework-and-content-model.md) and
[`docs/adr/0002`](../adr/0002-hosting-and-deploy-target.md); this document does
not restate either.

## 1. Audience and purpose

The site has four plausible audiences. They want different things, and when they
conflict this order decides:

1. **Executive and board.** Retained recruiters, hiring committees, boards
   assessing Dax for CMO and VP roles and advisory seats. This audience wins.
2. **Credibility check.** People who already have his name, from a meeting, an
   introduction, a referral, verifying he is what he appears to be.
3. **Peers and practitioners.** Marketing operators interested in how he thinks.
4. **Consulting inbound.** Explicitly last.

The consequences of that ordering are not cosmetic:

- No hard sell, no offers-led structure, no lead-capture funnel.
- Evidence leads. Scope, scale, outcomes, and third-party recognition carry the
  argument, not claims about capability.
- The consulting practice appears as **what he is currently doing**, never as
  something on offer. One page at most, no rate card, no engagement pitch. A
  services pitch reads as "between things" to a board, which is the exact
  impression an executive-first site exists to prevent.

## 2. Content model

Two collections. Both stand alone; there is no relation between them. This was a
deliberate call, not an omission.

### 2.1 `roles`

One entry per company or engagement.

| Field | Type | Notes |
| --- | --- | --- |
| `company` | string | Display name |
| `start` / `end` | string | `YYYY` or `YYYY-MM`; `end` omitted means current |
| `title` | string | Role as held |
| `mandate` | string | What he was brought in to change |
| `scope` | object | Team size, budget owned, remit. Optional per field |
| `outcomes` | array | Each with a claim and, where citable, a figure |
| `citations` | array | Source label plus URL or document reference |
| `featured` | boolean | Surfaces on the home page |
| `body` | MDX | Optional long form |

Named companies and real figures are publishable, which is what makes this
collection load-bearing rather than decorative.

### 2.2 `recognition`

Awards and press. Third-party evidence, including articles about companies and
products he was responsible for. Not authored writing.

| Field | Type | Notes |
| --- | --- | --- |
| `display_name` | string | Exact string to show |
| `count` | integer | Defaults to 1. Collapses repeats |
| `awarding_body` | string | |
| `year` | string | Optional; some entries have none |
| `employer` / `client` | string | Optional |
| `categories` | array | For collapsed entries, the distinct categories won |
| `confidence` | enum | `Confirmed` or `Confirmed (per Dax)` only |
| `resume_eligible` | literal `true` | |
| `provenance` | array, min 1 | Source URLs or document references |

**Counts, not repeats.** Six 2006 Davey Silvers become one entry with
`count: 6` and the three categories listed. The record stays exact; the page
stays readable.

### 2.3 The build gate

The source ledger already distinguishes what he did from what happened near him,
and records what could not be substantiated. The site enforces that rather than
trusting it:

```ts
recognition: defineCollection({
  schema: z.object({
    display_name:    z.string(),
    count:           z.number().int().positive().default(1),
    confidence:      z.enum(['Confirmed', 'Confirmed (per Dax)']),
    resume_eligible: z.literal(true),
    provenance:      z.array(z.string()).min(1),
  })
})
```

Anything at `Probable` or `Needs Verification` fails the build rather than
rendering. Company awards he was not responsible for never enter the collection.
The site becomes structurally incapable of overclaiming, which for a board
audience is the property worth having.

### 2.4 Not a collection

No `writing` collection at launch. Nothing is published yet, and an empty or
one-post section reads as abandoned. The IA below works without it and gains it
later without restructuring.

## 3. Page inventory

| Page | Job |
| --- | --- |
| Home | Makes the argument. Positioning line, strongest proof, paths to depth |
| Profile | Career narrative. Hand-written intro above the generated role timeline |
| Record | The `roles` collection, with per-entry detail pages |
| Recognition | The `recognition` collection, grouped and counted |
| Advisory | The consulting practice, subordinate, one page, no pitch |
| Contact | The conversion path below |

Added later, without restructuring: `Writing`, once two or three real pieces
exist.

A maintained CV PDF is downloadable and ungated from Profile and Contact. It is
a separate artifact kept in sync by hand; generating it from site content was
considered and set aside as build complexity that is not yet earned.

## 4. Conversion path

Ranked, and the ranking is deliberate:

1. **Calendar booking** (Cal.com), primary.
2. **Short form**, for anyone who would rather write first.
3. **LinkedIn**, where executive search actually happens.

No public email address.

**The copy carries the risk, not the mechanism.** A booking link can invert the
dynamic with a board member. "Find a time to talk" reads as a peer; "Book your
free strategy session" reads as a funnel. Same link.

## 5. Visual direction

Dark-first with full light-mode support, per the token structure in
[`0001`](../adr/0001-site-framework-and-content-model.md).

The direction is **atmospheric and editorial**, not a flat grid. It comes from
what the evidence actually is: press clippings from 1999 to 2015 and juried
awards. Newspaper-of-record authority. A site that shows its work.

- **Ground:** deep cool charcoal, not pure black. Warm off-white in light mode.
- **Accent:** a single warm burnt orange, used sparingly. Deliberately not the
  default blue or purple.
- **Atmosphere:** one warm low light source, one cool high wash, and a low-opacity
  grain overlay. Depth and texture rather than flat fill.
- **Type:** Libre Franklin for display, on the Franklin Gothic newspaper lineage.
  Source Serif 4 for reading. DM Mono for ledger data, years, and confidence
  labels.
- **Confidence is encoded by shape, never by colour alone.** Filled, half, and
  open marks. Colour-only encoding fails accessibility and collides with the
  accent.

**Not black and white.** Evaluated and rejected.

Motion is restrained: one orchestrated load sequence, `prefers-reduced-motion`
respected. No scroll-driven animation until Firefox ships `animation-timeline`.

## 6. Launch scope

In: home, profile, record, recognition, advisory, contact, CV download, dark and
light modes, sitemap, RSS, structured data.

Out: writing section, newsletter, search, tags, component library, any AI
feature. Each has a named trigger for reconsideration rather than a vague
"later".

## 7. Unresolved

Honest list. None of these blocks scaffolding; all of them block launch.

| Item | State |
| --- | --- |
| Positioning line | **Joint work.** Not written. Blocks the home page |
| Revenue influenced | Not supplied. Renders as a marked blank until it is |
| Team and budget owned | Not supplied. Same treatment |
| Portrait | No usable asset. Existing shoot is standing full-length only; the seated pose requires either a reshoot or generation from reference photos |
| Buffalo mark | Commissioned design, cleared for use. Placement and role undecided |
| Prior brand direction | A Claude Design project exists and has not been reviewed. Export needed |

## 8. Consequences

**Good.** The site cannot overclaim, because the schema forbids it. Content is
portable and outlives any vendor. Adding a role updates every surface that shows
one. The IA works today and gains a writing section later without a rewrite.

**Accepted costs.** The profile timeline reads as generated rather than authored,
mitigated by a hand-written intro. Editing means an editor and a commit until
Keystatic is adopted. Two headline figures render as visible blanks until
supplied, which is deliberate: a visible gap is more honest than an invented
number, and more likely to get filled.

## 9. What this obliges next

Issue #9 was written before this spec existed and bakes in schemas and templates
that were never decided. It must be rewritten against sections 2 and 3 before
scaffolding starts.
