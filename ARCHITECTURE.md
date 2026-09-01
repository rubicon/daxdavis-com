# Architecture

## Current state

This repository holds no application source. A framework has not been selected,
so there is no build pipeline, no rendering model, and no deploy target yet.

What exists today is repository infrastructure: dual licensing, release
automation, commit and pull-request policy enforcement, and dependency updates.

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
release-please-config.json    Release automation configuration
.release-please-manifest.json Current released version
LICENSE                       MIT, for source code
LICENSE-CONTENT               CC BY-NC-ND 4.0, for site content
CLAUDE.md                     Canonical agent instructions (AGENTS.md points here)
```

## Design constraints already fixed

**Two licenses, not one.** Code and content have different reuse profiles, so
they carry different terms and live in separate files. Any new file is one or
the other; when it is ambiguous, the MIT boundary stops at anything a reader
would recognize as the site's writing or visual identity.

**GitHub is the canonical host, and the only one.** Cloudflare Pages, Vercel, and
Netlify all connect to a GitHub repository directly, so hosting the source here
is what makes a one-step deploy possible. `origin` points at GitHub and there is
no mirror to keep in sync.

**Release automation signs its own commits.** `main` requires signed commits, and
release-please commits through a GitHub App token so its API-created commits are
verified rather than bypassing the requirement with an admin merge.

## What this document owes the next change

Framework selection is the next architectural decision, and it warrants a
decision record under `docs/adr/` rather than a paragraph here: alternatives will
be considered and rejected, and the rejected ones will otherwise be re-proposed.
This file then gains the rendering model, the content source, the build
pipeline, and the deploy path, and links the record instead of restating it.
