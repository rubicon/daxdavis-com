---
status: proposed
date: 2026-09-01
decision-makers: Dax Davis
---

# Hosting and deploy target

## Context and Problem Statement

[0001](0001-site-framework-and-content-model.md) selects a statically rendered
Astro site, which can be hosted almost anywhere. That widens rather than narrows
the choice, so the deciding factors are not technical capability but cost
structure, terms of service, failure modes, and which platform the vendor is
actually investing in.

`ARCHITECTURE.md` records that GitHub is the canonical host for the source and
the only one, specifically because the deploy platforms this site would use
connect to a GitHub repository directly. That constraint holds for every option
below, so it does not discriminate between them.

One piece of current state matters. `daxdavis.com` resolves through Namecheap
nameservers (`dns1.registrar-servers.com`, `dns2.registrar-servers.com`) and is
parked. Where a platform is placed relative to DNS therefore has a real cost.

## Considered Options

- Cloudflare Workers with Static Assets
- Cloudflare Pages
- Vercel
- Netlify
- GitHub Pages

## Decision Outcome

Chosen option: **Cloudflare Workers with Static Assets**, because static asset
requests are free and unlimited with no egress billing at all, its terms carry no
usage-class restriction, and it is the platform Cloudflare is directing new
projects to.

This carries a prerequisite. Workers custom domains require an active Cloudflare
zone, so `daxdavis.com` must move its nameservers from Namecheap to Cloudflare
before the domain can be attached. The maintainer has confirmed this is
acceptable. Cloudflare DNS is free and is an improvement over registrar parking
regardless of the hosting choice, so the migration is not a cost incurred purely
to satisfy this decision.

### Why not Cloudflare Pages

This is the option most likely to be re-proposed, because Pages is what most
secondary sources still recommend for a static site on Cloudflare, and because
its name matches the use case better than "Workers" does.

Cloudflare's own Pages landing page now opens with a callout headed "Are you sure
you want to use Pages?", reading: "Workers supports most Pages use cases and
offers a broader feature set. It is Cloudflare's primary platform for building
applications. Start new projects with Workers."

Pages is not deprecated. It has no announced sunset, is explicitly still
supported, and its documentation is actively maintained. The accurate description
is supported but frozen: Cloudflare has stated that Pages will continue to be
supported, but that going forward all of its investment, optimisation, and
feature work is dedicated to improving Workers.

Feature parity favours Workers for this site. Git-connected builds, preview URLs,
rollbacks, and custom domains are supported on both. The remaining Pages
advantages are custom branch aliases and branch deploy controls, which Cloudflare
marks as in progress on the Workers side. The gaps close in the Workers
direction, never the other way.

Pages retains exactly one advantage that would be decisive if it applied: it can
serve a custom domain whose DNS is hosted elsewhere, via a CNAME record. Workers
cannot. That advantage does not rescue Pages here for two reasons. The
CNAME path documented by Cloudflare covers a subdomain, not an apex domain, and
this site needs the apex. And the maintainer is willing to move DNS, which
removes the constraint entirely.

If the domain could not move to Cloudflare, Pages would be the correct choice and
this record would decide the other way.

### Why not Vercel

Vercel's Hobby plan is documented as restricted to non-commercial personal use,
with "advertising the sale of a product or service" named as an example of
commercial usage. **The maintainer has assessed this site as a personal brand
site rather than a commercial one, and considers the Hobby plan available.** That
reading is supported by the binding terms of service, whose section 4 is
disjunctive: use "for your personal or non-commercial use", which either
condition satisfies. The narrower conjunctive phrasing appears in the Fair Use
Guidelines, which the terms of service do not incorporate by reference.

Vercel is therefore not rejected on eligibility. It is rejected because it offers
this site nothing that Cloudflare does not, and carries two costs that Cloudflare
does not.

The first is bandwidth. Cloudflare charges nothing for data transfer at any tier,
and static asset requests are free and unlimited on both free and paid plans.
Vercel's published Hobby guideline is up to 100 GB of Fast Data Transfer per
month, and exceeding a Hobby usage limit generally means waiting until the
30-day window rolls over before the feature is usable again. For a site carrying
images and case studies, an unmetered platform is simply a better fit than a
metered one.

The second is residual terms risk. Even under the maintainer's reading, the same
section 4 permits Vercel to shut down a Hobby deployment "without notice for any
reason or no reason". That is an ordinary free-tier clause and not evidence of
bad faith, but it means the site's availability rests on an interpretation that
Vercel's own support documentation reads the other way. Accepting that risk would
be reasonable in exchange for a benefit. There is no benefit here: Vercel's
strongest advantages are Next.js-specific, and [0001](0001-site-framework-and-content-model.md)
does not select Next.js.

If this site is ever rebuilt on Next.js, this record should be revisited, because
that changes the calculation materially.

### Why not Netlify

Netlify's free tier is credit-based. Credits are consumed by both bandwidth and
production deploys, and on exhaustion Netlify pauses the account's sites, serving
a "Site not available" page in place of the site.

For a personal or hobby project that is an acceptable failure mode. For a site
that is a professional front door, an availability failure triggered by a
consumption meter, on a plan with no spend controls, is the wrong risk to carry
when a genuinely unmetered alternative is free.

### Why not GitHub Pages

GitHub's documentation states that Pages is "not intended for or allowed to be
used as a free web hosting service to run your online business". A brochure site
with no checkout very likely does not run afoul of that, and this is the closest
of the rejected options.

It loses on the same reasoning as Vercel, one step further along. It introduces
an ambiguity about permitted use that has to be reasoned about, in exchange for
nothing, when Cloudflare's self-serve terms contain no comparable usage-class
restriction at all. It remains a credible fallback if Cloudflare is ever
unavailable, so it is rejected rather than dismissed.

## Consequences

Good:

- Running cost is zero and stays zero. Static asset requests are free and
  unlimited, there are no egress or bandwidth charges at any tier, and git-driven
  builds include 3,000 build minutes per month on the free plan. Nothing about
  this site's traffic profile will leave the free tier.
- Terms are clean. Cloudflare's self-serve agreement carries no non-commercial
  restriction, which removes the question entirely rather than answering it.
- The deploy workflow is unchanged from what was assumed during provisioning:
  connect the GitHub repository, get preview URLs per push, and keep rollbacks.
- Moving to Cloudflare DNS is an independent improvement over registrar parking.

Bad, and accepted deliberately:

- **DNS must move before the domain can be attached.** This is a prerequisite,
  not a follow-up. Until the zone is active at Cloudflare, the site can be
  deployed and previewed but cannot serve `daxdavis.com`.
- Cloudflare becomes a single vendor for DNS, CDN, and hosting. The concentration
  is real. It is mitigated by the site being static and portable: the build
  output can be served by any static host, and moving means changing a deploy
  target rather than rewriting anything.
- Custom branch aliases are not yet available on Workers. Preview URLs are
  versioned per deploy, and stable per-branch aliases must be created manually.
  This is a convenience gap, not a blocker, and Cloudflare marks it as in
  progress.
- Workers static assets are capped at 20,000 files per version and 25 MiB per
  file. Neither is close to binding for this site, but a large media library
  would need object storage rather than the asset bundle.

## More Information

Grounded in the following, all checked on 2026-09-01.

- Cloudflare's guidance to start new projects with Workers, quoted above:
  <https://developers.cloudflare.com/pages/>
- Pages-to-Workers feature compatibility matrix, covering git builds, preview
  URLs, rollbacks, custom domains, branch aliases, and branch deploy controls:
  <https://developers.cloudflare.com/workers/static-assets/migration-guides/migrate-from-pages/>
- Free and unlimited static asset requests, and the absence of egress or
  bandwidth charges: <https://developers.cloudflare.com/workers/platform/pricing/>
- Workers free plan limits (100,000 requests per day, 10 ms CPU per invocation)
  and static asset ceilings (20,000 files per version, 25 MiB per file):
  <https://developers.cloudflare.com/workers/platform/limits/>
- Workers Builds free allowance of 3,000 build minutes per month:
  <https://developers.cloudflare.com/workers/ci-cd/builds/limits-and-pricing/>
- Requirement that a Workers custom domain sit on an active Cloudflare zone:
  <https://developers.cloudflare.com/workers/configuration/routing/custom-domains/>
- Pages custom domains on externally hosted DNS, documented for a subdomain via
  CNAME: <https://developers.cloudflare.com/pages/configuration/custom-domains/>
- Vercel Hobby plan usage guidelines and the commercial-usage definition:
  <https://vercel.com/docs/limits/fair-use-guidelines> and
  <https://vercel.com/docs/plans/hobby>
- GitHub Pages permitted-use language:
  <https://docs.github.com/en/pages/getting-started-with-github-pages/github-pages-limits>
- Current DNS delegation for `daxdavis.com`, confirmed by `dig +short NS
  daxdavis.com`, returning `dns1.registrar-servers.com` and
  `dns2.registrar-servers.com`.

Related repository context: `ARCHITECTURE.md` currently names Cloudflare Pages,
Vercel, and Netlify as deploy candidates. That prose predates this record and
should be replaced by a link to it, per the one-home-per-fact rule. Tracked as
follow-up on the issue that produced this record.
