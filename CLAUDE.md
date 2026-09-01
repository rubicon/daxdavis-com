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
repository in sync across two hosts. The deploy platforms this site will use
connect to GitHub directly, which is the whole reason it lives here.

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

## Framework

**No framework has been chosen.** Do not scaffold one, do not add a static-site
generator, and do not configure a deploy target on your own initiative. That
decision is the maintainer's and is pending. When it lands it warrants a
decision record under `docs/adr/`, because alternatives will be rejected and
would otherwise be re-proposed.

Until then, `package.json` and lockfiles are deliberately absent. The commitlint
workflow installs its own dependencies at runtime precisely so the repository
does not have to commit to a package manager before that decision is made.

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
