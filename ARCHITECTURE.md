# Architecture

## Current state

This repository holds no application source yet. The stack is decided and
recorded; the scaffold that implements it has not landed.

What exists today is repository infrastructure (dual licensing, release
automation, commit and pull-request policy enforcement, dependency updates) and
the decision records that fix the stack.

## Layout

```
.editorconfig                 Editor defaults across file types
.github/
  dependabot.yml              github-actions ecosystem updates
  PULL_REQUEST_TEMPLATE.md    PR checklist, carries the ADR line
  workflows/
    commitlint.yaml           Conventional Commits on every PR commit
    pr-policy.yaml            Branch naming and issue linkage
    release-please.yaml       Release PR proposal from main
docs/adr/                     Decision records, indexed by their own README
release-please-config.json    Release automation configuration
.release-please-manifest.json Current released version
LICENSE                       MIT, for source code
LICENSE-CONTENT               CC BY-NC-ND 4.0, for site content
CLAUDE.md                     Canonical agent instructions (AGENTS.md points here)
```

## The stack

Each row links the record that decided it. Those records carry the alternatives
that were considered and rejected, and the reasons. This table carries only what
and where, so the two do not drift.

| Concern | Choice | Record |
| --- | --- | --- |
| Rendering model | Astro 7.x, statically rendered. No adapter, no server runtime. | [0001](docs/adr/0001-site-framework-and-content-model.md) |
| Content source | Content collections in `src/content/`, Markdown and MDX in git, Zod-validated frontmatter. No CMS. | [0001](docs/adr/0001-site-framework-and-content-model.md) |
| Styling | Tailwind CSS 4.x using the CSS-first `@theme` model. No component library at launch. | [0001](docs/adr/0001-site-framework-and-content-model.md) |
| Deploy path | Cloudflare Workers with Static Assets, built from this repository through Workers Builds. | [0002](docs/adr/0002-hosting-and-deploy-target.md) |

## Design constraints already fixed

**Two licenses, not one.** Code and content have different reuse profiles, so
they carry different terms and live in separate files. Any new file is one or
the other; when it is ambiguous, the MIT boundary stops at anything a reader
would recognize as the site's writing or visual identity.

**GitHub is the canonical host, and the only one.** Cloudflare Workers builds
directly from a GitHub repository, so hosting the source here is what makes a
one-step deploy possible. `origin` points at GitHub and there is no mirror to
keep in sync.

**Release automation signs its own commits.** `main` requires signed commits, and
release-please commits through a GitHub App token so its API-created commits are
verified rather than bypassing the requirement with an admin merge.

## What this document owes the next change

The scaffold. When `package.json` and the first source files land, this file
gains the build commands and the layout under `src/`, and the deploy row above
gains the Worker name and its preview-URL behaviour.

Attaching the apex domain depends on a maintainer action outside this
repository: `daxdavis.com` has to move its nameservers to Cloudflare, because a
Workers custom domain requires an active Cloudflare zone. Record 0002 carries
that reasoning and the one case that would reverse the hosting decision.
