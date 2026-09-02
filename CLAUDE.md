# CLAUDE.md

Agent instructions for `rubicon/daxdavis-com`. This file carries only what is
specific to this repository. The general repository process, code practices, and
verification standards live in the maintainer's own policy files and are not
restated here.

`AGENTS.md` is a pointer stub to this file. Keep it that way.

## What this repository is

The complete source for daxdavis.com, the personal site of Dax Davis.

## Host

**GitHub is canonical for this repository.** `origin` points at GitHub and there
is no mirror anywhere else. Do not add one, and do not try to keep this
repository in sync across two hosts. Cloudflare Workers builds directly from a
GitHub repository, which is the whole reason the source lives here.

`gh` calls in this repository must strip the restricted PAT from the
environment, because it does not carry the scopes this repo needs:

```bash
env -u GITHUB_TOKEN -u GH_TOKEN gh <command>
```

The `gh` login is `rubicon`.

## Licensing boundary

Two licenses, and every file falls under exactly one:

- **MIT** (`LICENSE`) covers source code: build configuration, components,
  scripts, tooling.
- **CC BY-NC-ND 4.0** (`LICENSE-CONTENT`) covers site content: prose, copy,
  imagery, visual design.

When adding a file, decide which side it is on. If a reader would recognize it
as the site's writing or visual identity, it is content. Do not relicense either
side, and do not merge the two files.

## Stack

Decided and recorded. Do not re-litigate it in a pull request; changing any of
it takes a superseding decision record, not an edit.

| Layer | Choice | Record |
| --- | --- | --- |
| Framework | Astro 7.x, static output | [0001](docs/adr/0001-site-framework-and-content-model.md) |
| Content | Content collections plus MDX in git, no CMS | [0001](docs/adr/0001-site-framework-and-content-model.md) |
| Styling | Tailwind CSS 4.x, CSS-first `@theme`, no component library | [0001](docs/adr/0001-site-framework-and-content-model.md) |
| Hosting | Cloudflare Workers with Static Assets | [0002](docs/adr/0002-hosting-and-deploy-target.md) |

Scaffolding the site is tracked as its own issue. Two Astro 7 settings have to be
made deliberately at that point, because a later maintainer cannot re-derive
either from the repository. Set `markdown.processor` to `unified` if the remark
and rehype ecosystem is wanted, since v7 no longer ships it by default and
changing it after articles exist means reworking them. And do not build on
`@astrojs/db`, which v7 removed.

`package.json` arrives with that scaffold. The same change has to switch
`release-please-config.json` from the `simple` release type to `node`, because
`simple` tracks a `version.txt` while `node` tracks the version inside
`package.json`. Adding a manifest without that switch leaves the package version
unmanaged, and it fails quietly. Until then the commitlint workflow installs its
own dependencies at runtime.

## Enforcement lives in config, not in prose

Do not describe these rules in documentation; change the file that enforces them.

| Rule | Enforced by |
| --- | --- |
| Conventional Commits | `commitlint.config.mjs`, `.github/workflows/commitlint.yaml` |
| `dev/<issue>-<slug>` branches, issue linkage | `.github/workflows/pr-policy.yaml` |
| Version bumps and changelog | `release-please-config.json`, `.github/workflows/release-please.yaml` |
| Editor defaults | `.editorconfig` |
| Action updates | `.github/dependabot.yml` |
| Protected `main` | Branch protection settings on GitHub |

## Release automation

release-please proposes releases from `main`. It commits through the
`rubicon-release-please` GitHub App so its commits are verified, which is what
lets `main` require signed commits without an admin bypass. App credentials are
pulled at runtime from 1Password.

**1Password references are pinned by item UUID, never by title.** A title-pinned
reference breaks silently the moment the item is renamed. If you add a new
reference, resolve the UUID first and pin that.

The first release is forced to `0.1.0` via `release-as` in
`release-please-config.json`, because release-please's first-release special case
would otherwise propose `1.0.0`. **Remove that override once the release PR
confirms the version**, or it silently pins every later release too.

## Attribution

All work in this repository is attributed to Dax Davis alone. Never add
`Co-Authored-By` trailers naming an AI, "Generated with" lines, or any other
AI-authorship marker to commits, pull requests, issues, or files.
