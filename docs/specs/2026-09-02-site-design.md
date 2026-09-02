# Site design spec

Status: draft, pending maintainer agreement
Date: 2026-09-02
Author: Dax Davis

Decides what the site *is*. The stack it runs on is decided separately in
[`docs/adr/0001`](../adr/0001-site-framework-and-content-model.md) and
[`docs/adr/0002`](../adr/0002-hosting-and-deploy-target.md); this document does
not restate either.

## 0. What the site is for

The site is a **proof-led executive positioning platform**. It exists to answer
one hiring question:

> When marketing is fragmented, growth is under pressure, technology and AI are
> producing more activity than value, and the organization needs an executive who
> can integrate strategy, people, data and execution, is Dax Davis the leader to
> consider?

It must answer, quickly: what kind of executive this is, what situations he is
good at fixing, at what scale he has operated, what changed because he was there,
why that is relevant now, and how to make contact.

**The failure mode this spec is written against** is a beautifully engineered,
scrupulously sourced archive of the past that never makes the argument for the
future. Verification discipline is necessary and is not sufficient. A schema can
reject an unsupported award; it cannot make a hiring case.

## 1. Positioning

### Primary identity

**Marketing and Growth Transformation Executive.**

### Working thesis

> Dax Davis turns fragmented marketing capabilities into accountable growth
> systems, connecting brand, demand, digital, data, AI, technology and teams.

This is a strategic direction, not final home page copy. The exact line is
written jointly and is listed as unresolved in section 8.

### The situation he owns

Organizations with fragmented marketing capability, growth pressure, underused
technology and data, complex customer acquisition, or distributed operations
that need an executive to integrate the system. That includes disconnected brand
and performance teams, MarTech sprawl, weak attribution, immature marketing
operations, multi-location growth, AI experimentation without operating
discipline, and functions that need building or modernizing.

### Three pillars

Every substantial item on the site supports at least one:

1. **Enterprise growth.** Customer insight, brand, acquisition, retention,
   revenue, commercial strategy.
2. **Marketing transformation.** Digital, data and analytics, MarTech, AI,
   automation, workflow, measurement.
3. **Leadership and scale.** Team building, operating cadence, alignment with
   finance, sales, operations and technology, change leadership, budget and
   business accountability.

### What the umbrella is not

Not "AI marketer", not "digital marketing executive", not "MarTech leader", not
"performance marketer", not "fractional CMO", not "consultant who also wants an
executive job". Each may be a facet; none is the frame.

**Do not use "CMO-level".** It invites the reader to evaluate a title gap rather
than executive capability. Use "Marketing Executive" or "Growth and Marketing
Transformation Executive", and target CMO roles directly.

### AI is a modifier, not the noun

AI appears as a leadership and operating-model capability: identifying
high-value use cases, redesigning workflow, integrating with existing MarTech
and data, building adoption, governing risk and quality, and connecting AI to
revenue, cost, customer experience or productivity.

"AI-enabled marketing transformation" is the register. "AI marketing expert" is
weaker. "AI visionary" is disqualifying.

## 2. Audience

Ranked. When they conflict, this order decides.

| Priority | Audience | What they need |
| ---: | --- | --- |
| 1 | Executive recruiters, CEOs, CHROs, hiring executives, owners, investors | Executive fit, scope, impact, leadership credibility |
| 2 | Warm referrals and credibility checks | Fast confirmation that the reputation is real |
| 3 | Advisory and interim prospects | Evidence of relevant judgment and current capability |
| 4 | Peers, media, podcast hosts, practitioners | A distinctive point of view |
| Separate track | Board and governance | Governance experience, P&L breadth, risk oversight |

**Board service is a separate track, not a co-headliner.** It is a different
selection process with different evidence requirements, and it needs its own
assets: a board bio, a skills matrix, governance and committee relevance. Those
belong on a later, secondary page. Mixing them into the primary site distorts
the executive argument before the board-specific evidence exists.

Consequences of this ordering: no offers-led structure, no lead-capture funnel,
and evidence leads over claims about capability.

## 3. Content model

Four collections. Three ship at launch.

### 3.1 `roles`

Employers and engagements. The canonical career timeline.

| Field | Type | Notes |
| --- | --- | --- |
| `company` | string | |
| `start` / `end` | string | `YYYY` or `YYYY-MM`; omit `end` for current |
| `title` | string | Exact title held |
| `mandate` | string | What he was brought in to change |
| `scope` | object | Team, budget, P&L, geographic or organizational reach |
| `themes` | array | Which pillars this role evidences |
| `body` | MDX | Optional |

### 3.2 `impact`

**The most important addition, and the one the site is actually for.** Selected
transformations organized by *problem*, not chronology. Three to six at launch.

| Field | Type | Notes |
| --- | --- | --- |
| `title` | string | Names the problem, not the project |
| `business_context` | string | What the organization was facing |
| `problem` | string | The specific failure being solved |
| `mandate` | string | What he was asked to own |
| `actions` | array | What he actually did |
| `outcomes` | array | What changed, with figures where publishable |
| `scope` | object | Team, budget, reach |
| `attribution` | enum | `direct`, `executive_owner`, `team`, `contextual` |
| `related_role_ids` | array, optional | Links to `roles` |
| `themes` | array | Pillars |
| `evidence_ids` | array | References into the private evidence ledger |
| `disclosure` | enum | `public`, `anonymized`, `private`, `omit` |

`attribution` matters more than it looks. Claiming a team outcome as personal is
the fastest way to lose a reference check.

### 3.3 `recognition`

Awards and press. Third-party validation, including coverage of companies and
products he was responsible for. Not authored writing.

| Field | Type | Notes |
| --- | --- | --- |
| `display_name` | string | |
| `count` | integer | Defaults to 1. Collapses repeats |
| `awarding_body` | string | |
| `year` | string | Optional |
| `employer` / `client` | string | Optional |
| `categories` | array | For collapsed entries |
| `related_role_ids` | array, optional | |
| `evidence_status` | enum | See 3.5 |
| `disclosure` | enum | See 3.5 |
| `evidence_ids` | array, min 1 | References, not free strings |

**Counts, not repeats.** Six 2006 Davey Silvers become one entry with `count: 6`
and the categories listed.

### 3.4 `insights`

Ships only when two or three substantive pieces exist. Not at launch. An empty
section reads as abandoned.

RSS ships **with** `insights`, not before it.

### 3.5 The evidence gate, corrected

The original gate was a good instinct with three real defects, all of which are
fixed here.

**`resume_eligible: true` was circular.** An item was permitted because it
asserted it was permitted. Removed.

**`Confirmed (per Dax)` is not a public standard.** Self-attestation is
appropriate inside a private career corpus. Recognition, by definition, wants
outside provenance. It no longer qualifies for public render.

**Free-text provenance proved only that someone typed something.** Replaced by
references into a private evidence ledger.

```ts
evidence_status: z.enum([
  'verified_primary',    // primary source held
  'verified_secondary',  // credible secondary source
  'self_attested',       // private corpus only
  'unverified',
])

disclosure: z.enum(['public', 'anonymized', 'private', 'omit'])
```

Public render requires:

```ts
evidence_status in ['verified_primary', 'verified_secondary']
  && disclosure in ['public', 'anonymized']
```

Anything else fails the production build rather than rendering.

**And the honest limit:** this checks that a claim is *sourced*, not that it is
*true, fairly attributed, in context, or safe to publish*. Those are editorial
judgments and stay human. The gate is a floor, not a certificate.

**Private evidence stays private.** Internal document references, confidential
URLs and evidence metadata do not reach the public repository or the generated
site. Structure: a private evidence ledger, curated public content, a build check
that public claims reference eligible evidence, and human sign-off before launch.

### 3.6 Relations

Collections render as independent pages but may reference each other via optional
`related_role_ids`. This supersedes the earlier decision that they must be fully
standalone. Independent rendering does not require isolated data, and isolation
was forcing company names and context to be duplicated across three places.

## 4. Page inventory

Primary navigation:

| Page | Job |
| --- | --- |
| Home | Makes the hiring argument |
| Executive Profile | Identity, leadership philosophy, career arc, the situations he leads best. **Not** the full timeline |
| Selected Impact | Three to six transformations, organized by problem |
| Experience | Canonical timeline and role detail pages |
| Contact | Direct, low friction |

Secondary navigation or footer: Recognition and Press, Advisory, Executive
Résumé, LinkedIn. `Insights` joins the primary nav when it exists.

Two changes from the previous version worth stating. **"Profile" and "Record"
were two doors into the same room**; visitors would click both and find the same
career information. Profile now carries identity and philosophy, Experience
carries the timeline. And **"Record" is replaced by "Experience"** because
recruiters should not have to decode navigation labels.

### Home page structure

1. Hero: name, executive descriptor, one value proposition, real portrait,
   *View Selected Impact* and *Contact*.
2. Executive proof bar: three verified facts establishing level and relevance.
3. Selected Impact: three stories as problem, action, result.
4. How value is created: the three pillars.
5. Career arc: concise, significant roles and environments.
6. Third-party proof: a small selection of recognition, press, endorsements.
7. Current focus: openness to permanent executive leadership, select interim or
   advisory mandates.
8. Contact.

## 5. Conversion path

Reordered. A public calendar is not the right primary action for a recruiter or
CEO: it asks for a high-commitment decision before first contact, and it carries
consulting associations regardless of button copy.

**Home page:** *View Selected Impact* → *Contact* → *Download Executive Résumé*.

**Contact page:** direct email or a very short form, then LinkedIn, then calendar
as an optional convenience.

**Publish a professional email address.** A recruiter should not have to complete
a form or book a meeting to send two paragraphs. This supersedes the earlier "no
public email" decision.

**Executive Résumé**, not CV, for the US corporate market. Ungated, dated with
month and year, ATS-friendly, Dallas-Fort Worth rather than a street address,
refreshed whenever experience or positioning changes. A board bio is a separate
later artifact.

## 6. Visual direction

Dark-first with equally complete light mode, per the token structure in
[`0001`](../adr/0001-site-framework-and-content-model.md).

The register is **contemporary editorial authority**. Not archival newspaper
atmosphere. The site should feel current, intelligent and grounded, and must not
read as an exhibition about early interactive advertising.

Keep: dark-first, warm charcoal rather than pure black, a single restrained warm
accent, editorial typography, limited motion, `prefers-reduced-motion`, premium
human photography.

- **Type.** Libre Franklin for display, Source Serif 4 for reading. DM Mono used
  sparingly for dates, metrics and labels only.
- **Accent.** The warm burnt orange is provisional, not settled. It must be
  tested against portrait photography and skin tone, both modes, contrast
  requirements, and any mark before it is fixed.
- **No public confidence marks.** Confidence is editorial metadata, not audience
  content. A visitor sees the claim, who recognized it, the year, the context and
  a source. They should not have to interpret a private epistemology.
- **No visible blanks.** A missing figure is omitted, or it fails the production
  build when required for a featured item. A blank does not read as principled
  honesty; it reads as unfinished.
- **Mark.** The name is the primary brand. A mark, if used, is confined to
  favicon, footer, section marker, or social avatar, and never competes with the
  name in the hero. The existing DD monogram is rejected. Whether the buffalo
  mark reads as executive authority rather than western or outdoor lifestyle is
  unresolved.

### Portrait

**Commission a real session.** A credibility-first site built on documented
evidence must not present an invented face as its primary human signal. AI may
assist with retouching, background, colour, crops and derivative formats. It does
not manufacture the foundational portrait.

Shot list: close head-and-shoulders; seated three-quarter; standing
environmental; direct and off-camera variants; dark and light backgrounds;
horizontal negative-space compositions for the hero; a few less formal frames.

The existing 2025 session is standing full-length only, on light grey, at
roughly 2007x3000. Tight crops from it are usable at real resolution as an
interim.

## 7. Launch scope

**In:** Home, Executive Profile, Selected Impact, Experience, Contact, Executive
Résumé, a limited Recognition and Press selection, Advisory, dark and light
modes, sitemap, structured data.

**Out:** Insights, RSS, newsletter, search, tags, component library, any AI
feature, the board page.

### Recognition is supporting proof, not the centre

A large volume of recognition from 1999 to 2015 can demonstrate sustained
achievement. It can equally suggest the most interesting work is behind him.
Feature only the strongest and most relevant items; frame the older material as
early digital innovation; lead with recent executive impact and scope; keep the
full archive available to anyone who wants to dig.

### Advisory: subordinate, and truthful

Advisory is secondary to the executive identity, factual, selective, free of
packages, pricing and conversion language, and clear about availability. It must
not imply active engagements that do not exist. The earlier framing, that it
appear only as "what he is currently doing", was too absolute and risks a truth
problem if there are no current engagements.

Dax Davis Consulting is the operating entity, not a competing personal brand.

## 8. Unresolved

None of this blocks scaffolding. All of it blocks launch.

| Item | Why it matters |
| --- | --- |
| Exact positioning line | Joint work. Must follow from target roles and evidence |
| Team, budget and P&L scope | Establishes executive level. Not yet supplied |
| Publishability of employer and client metrics | Truthful is not the same as disclosure-safe |
| Current consulting activity | Decides whether Advisory can describe active work |
| The three strongest impact stories | These drive the home page |
| Usable executive endorsements | Two or three, specific and permissioned |
| Relevance of older recognition | Prevents a backward-looking site |
| Buffalo mark fit | Whether it reads as executive authority |
| Board evidence strength | Decides whether the board track appears publicly at all |
| Portrait session | Not yet commissioned |
| Prior brand direction | A Claude Design project exists, not yet reviewed |

## 9. Human evidence

The plan is strong on awards and metrics and thin on how he leads. Executives are
hired on whether other executives believe they can work with them under
pressure.

Add one restrained section on how he leads, decides, and works across functions,
derived from performance feedback, recommendations and references rather than
invented in a branding exercise. Two or three specific, permissioned executive
endorsements. No rotating carousel.

## 10. Execution order

The site must not become a well-engineered reason to postpone the search.

1. **Positioning.** Lock identity, target role families, situations solved,
   pillars, target company characteristics, advisory status.
2. **Evidence selection.** Ten strongest verified proof points, three to six
   impact stories, publishable scope figures, endorsements, confidentiality
   constraints.
3. **Career materials.** Résumé, variants, LinkedIn headline and About, recruiter
   brief, interview narrative. **Outreach begins here, not after launch.**
4. **Site MVP.** Per section 7.
5. **Authority.** Insights, endorsements, speaking, updated evidence.
6. **Board track.** Separate thesis, bio, skills matrix, network.

## 11. What this obliges next

Issue #9 predates this spec and bakes in schemas and page templates that were
never decided. **Do not rewrite it yet.** Sections 1 through 5 have to settle
first, because the content model and page inventory both changed materially in
this revision.

## 12. Provenance of this revision

The first draft of this spec was reviewed and found to be overoptimized for
documenting the past and underoptimized for arguing the future. That review drove
sections 0, 1, 2, 3.2, 3.5, 3.6, 4, 5 and 10.

Four decisions recorded in the first draft were reversed deliberately and are
noted here so they are not re-proposed as oversights: collections may now relate
to each other; email precedes the calendar in the conversion path and a public
address is published; board service moves to a separate track; and the downloadable
document is an Executive Résumé rather than a CV.

Market claims cited in that review about CMO hiring patterns, board appointment
rates and enterprise AI adoption were not independently verified for this spec.
The strategic conclusions here do not depend on the specific figures.
