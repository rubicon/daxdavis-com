---
status: accepted
date: 2026-09-01
decision-makers: Dax Davis
---

# Site framework and content model

## Context and Problem Statement

The repository holds no application source. No framework has been selected, so
there is no build pipeline, no rendering model, and no content model. Everything
else about the site depends on this choice, and the choice is expensive to
reverse once articles and case studies exist in a particular shape.

The site is a personal brand site for a marketing executive. In practice that
means it is content: a home page, a profile, articles, case studies, and a small
number of conversion paths. It is low traffic. It is maintained by one person who
is technically capable but is not a full-time developer, so long-term maintenance
cost matters more than raw capability. It must be fast and crawlable, it is
dark-mode-first with light-mode support, and it needs to still be maintainable in
five years by someone who has not touched it in months.

Two constraints were fixed before this decision. WordPress and Divi are excluded.
Content and source code carry different licences (`LICENSE-CONTENT` is
CC BY-NC-ND 4.0, `LICENSE` is MIT), so the content model has to keep the boundary
between them legible.

## Considered Options

- Astro, statically rendered, with content collections and MDX in git
- Next.js
- SvelteKit or Nuxt
- Framer
- Webflow
- Any framework above, but with a headless CMS (Sanity, Contentful, Storyblok,
  Payload) as the content source instead of git

## Decision Outcome

Chosen option: **Astro, statically rendered, with content collections and MDX in
git**, because its rendering model matches what the site actually is, everything
the site needs is first-party and MIT licensed, and it leaves no vendor between
the author and the content.

Concretely:

- Astro 7.2.x, static output, no adapter and no server runtime.
- Content in `src/content/`, loaded through content collections using the built
  in `glob()` loader, with Zod-validated frontmatter.
- MDX via `@astrojs/mdx` for the small number of places a component earns its
  place inside prose.
- Tailwind CSS 4.3.x using the CSS-first `@theme` model, so design tokens are
  plain custom properties rather than a JavaScript config.
- No component library at launch. Native `<dialog>` and the Popover API cover the
  interactive surface a content site needs.
- No CMS at launch.

### Why not Next.js

Next.js is the strongest alternative and the one most likely to be re-proposed,
so the reason it loses is worth stating precisely. Its differentiator is
request-time rendering: Server Actions, ISR, partial prerendering, and the
caching model built around them. Those are exactly the features that
`output: 'export'` disables. Building this site in Next.js statically means
shipping the framework's complexity and getting none of its capability.

Not exporting statically means running a Node server, which is the real
maintenance surface: a patching obligation, an LTS cadence where the previous
major receives roughly two years of support, and a caching model that has been
reworked repeatedly across recent majors.

There is also a concrete daily cost. A Next.js static export cannot optimise
images without a custom loader pointed at a third-party service, while Astro does
it at build time with Sharp at no cost. For a site whose main output is
illustrated articles and case studies, that alone decides it.

Choosing Astro does not foreclose React. Astro renders React components as
islands on the same pages, so if a genuinely app-shaped need appears later, React
is available exactly where it earns its place without the whole site paying for
it.

### Why not SvelteKit or Nuxt

Both are capable and neither is a mistake. Neither does anything for a content
site that Astro does not, and both put the project further from the React island
that may be wanted later. There is no positive reason to prefer them here.

### Why not Framer or Webflow

Framer is the better-designed of the two and would produce a good result quickly.
It is rejected because it has no export path. Framer's own documentation states
that it does not offer HTML export for self-hosting, and volunteers that Framer
may not be the right solution for anyone who needs source code. For a brand site
intended to outlive several redesigns, that is renting the front door rather than
owning it.

Webflow is the honest runner-up because its lock-in is escapable, but only by
also paying for a Workspace seat on top of a site plan, and even then CMS content
exits as CSV and article pages exit as empty templates that have to be re-wired
by hand.

Both also sell something this project already has. A governed repository and an
agentic coding workflow cover most of the development time a visual builder
saves.

### Why no CMS at launch

Every hosted option surveyed carries a specific disqualifier for this site.
Sanity's free tier permits only public datasets, so unpublished case studies
would be world-queryable. Payload hard-requires Next.js and a database, which
means a server to patch and a bill to pay for a site that is otherwise static.
Storyblok's paid floor is well above the cost of the entire rest of the stack.
Notion keeps articles in a proprietary block format behind a rate-limited API.

Against that, git content costs one thing (a file, some frontmatter, and a
commit) and buys portability, diffability, zero seat cost, and no third party
whose pricing page can change underneath the site. It also keeps the dual-licence
boundary legible, since content lives in identifiable directories rather than in
someone else's database.

When a browser-based editor becomes a felt problem rather than an anticipated
one, Keystatic is the intended answer. It is MIT licensed, `@keystatic/astro`
supports the Astro 7 line, and it commits into this repository, so adopting it
later is additive and removing it again costs nothing.

## Consequences

Good:

- The rendering model matches the content. Static output means no server, no
  runtime, no patching obligation, and a hosting bill that rounds to zero.
- Everything required is first-party and MIT licensed. Content collections,
  MDX, sitemap, RSS, Sharp image optimisation, view transitions, and prefetch all
  ship with the framework rather than arriving as third-party dependencies.
- Content is portable. Markdown and MDX in git can move to any other static site
  generator with rewriting of templates only, not of content.
- No vendor sits between the author and publishing.

Bad, and accepted deliberately:

- **Upgrade cadence is a real tax.** Astro shipped two majors in the twelve
  months before this decision (6.0 on 2026-03-10, 7.0 on 2026-06-22), both with
  breaking changes. Astro 6 removed the legacy content collections API and its
  compatibility flag outright. Budget one upgrade a year and pin the version;
  this is the main ongoing cost of the choice.
- **Editing requires an editor and a commit.** There is no browser-based editing
  until Keystatic is adopted. This is acceptable for a single author and would
  not be for a team.
- Astro's live collections, which is the path a headless CMS would use later, do
  not support MDX or image optimisation. Those are build-time capabilities. If a
  CMS is ever added, the MDX authoring path and the image pipeline stay on the
  git side.

### Two Astro 7 traps to configure on day one

Both were established by reading Astro's upgrade documentation rather than from
the code, and a future maintainer cannot re-derive either from the repository.

1. **Markdown processor.** Astro 7 defaults to a new Rust-based Markdown
   processor and no longer ships `@astrojs/markdown-remark`. Anything that
   depends on the remark and rehype plugin ecosystem (reading time, heading
   anchors, `rel` attributes on external links) requires installing it and
   setting `markdown.processor` to `unified`. Decide this before the first
   article is written, because discovering it later means reworking every one.
2. **`@astrojs/db` is gone.** It was removed in Astro 7. Do not build anything
   on it.

Server islands are also deliberately unused. They require an on-demand adapter
and offer nothing on a static, low-traffic site with no personalisation.

## More Information

Grounded in the following, all checked on 2026-09-01 unless noted.

- Astro release history and current version: <https://astro.build/blog/>, and
  the npm `latest` dist-tag for `astro`, which resolved to 7.2.10 published
  2026-08-31. Astro 7.0 released 2026-06-22; 6.0 released 2026-03-10.
- Removal of the legacy content collections API and its compatibility flag:
  <https://docs.astro.build/en/guides/upgrade-to/v6/>
- Content Layer API, `glob()` and `file()` loaders, and the live-collections
  limitation on MDX and image optimisation:
  <https://docs.astro.build/en/guides/content-collections/>
- Tailwind CSS 4.3 and the CSS-first `@theme` model:
  <https://tailwindcss.com/docs/theme> and
  <https://tailwindcss.com/blog/tailwindcss-v4-3>
- Next.js static export limitations:
  <https://nextjs.org/docs/app/guides/static-exports>
- Framer's lack of an export path: Framer's own help documentation, which states
  that Framer does not offer HTML export for self-hosting.
- Sanity free-tier public-dataset restriction and Payload's Next.js plus database
  requirement: the respective vendors' pricing and documentation pages.
- Keystatic Astro support and licence: `@keystatic/astro` 6.0.0 on npm, declaring
  a peer dependency on Astro `5 || 6 || 7`, MIT licensed.

Related repository context: `ARCHITECTURE.md` records that framework selection
was the next architectural decision and that it warranted a record.

Hosting is recorded separately in
[0002](0002-hosting-and-deploy-target.md), because framework and host are
independently reversible and carry different rejected alternatives.
