# Afterloop Alpha — Claude Code Project Rules

## Overview

Alpha downloads page for Afterloop, deployed to **Cloudflare Pages + Access**. Single-page static site that auto-detects the visitor's platform and presents the correct download links.

The plugin repo's `release.yml` triggers deployment here via `repository_dispatch` on prerelease tags.

> Universal practices (commits, branches, worktrees, quality philosophy, workflow) are in `~/.claude/CLAUDE.md` and apply automatically.

## Quick Start

```bash
mise install          # Node 22
npm install           # prettier
```

## Build & Deploy

This is a static site — no build step locally. CI handles everything:

- **`deploy.yml`** resolves the release tag, downloads assets from the plugin repo, substitutes template placeholders in `index.html`, and deploys to Cloudflare Pages
- **Manual re-deploy:** `gh workflow run deploy.yml` (uses latest release) or `gh workflow run deploy.yml -f tag=v0.1.9-alpha.1`

### Template Substitution

`index.html` contains placeholders replaced at deploy time by CI:

| Placeholder | Replaced with |
|---|---|
| `__HAS_RELEASE__` | `true` or `false` |
| `__RELEASE_TAG__` | e.g. `v0.1.9-alpha.1` |
| `__RELEASE_DATE__` | e.g. `2026-04-01` |
| `__RELEASE_FILE_ARM64__` | ARM64 DMG filename |
| `__RELEASE_FILE_X86_64__` | Intel DMG filename |
| `__RELEASE_FILE_WIN64__` | Windows EXE filename |

When editing `index.html`, keep these placeholders intact — they are literal strings in the source, not JS variables.

## Lint

```bash
npx prettier --check .       # format check
npx prettier --write .       # auto-fix
```

## Conventions

### Commit Scopes

- `page` — `index.html` and front-end changes
- `ci` — GitHub Actions workflows
- `infra` — Cloudflare configuration, Access policies

## Secrets

| Secret | Purpose |
|---|---|
| `PLUGIN_REPO_TOKEN` | Fine-grained PAT with `contents:read` on the plugin repo — for downloading release assets |
| `CLOUDFLARE_API_TOKEN` | Cloudflare API token scoped to Pages |
| `CLOUDFLARE_ACCOUNT_ID` | Cloudflare account identifier |

Cross-repo dispatch uses fine-grained PATs that expire annually — see `docs/secrets-runbook.md` in the plugin repo for rotation procedures.

## Infrastructure Notes

- **Cloudflare Access does not automatically protect `*.pages.dev` domains** when configured as a Zero Trust self-hosted application — the built-in Pages access toggle (Workers & Pages -> project Settings -> Access Policy) must be enabled separately
- The page auto-detects platform via `navigator.userAgentData` (Chromium) with Safari fallback to ARM64 (most common new Mac)
