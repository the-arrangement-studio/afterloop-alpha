---
name: deploy
description: Trigger a deploy of the Afterloop alpha downloads page to Cloudflare Pages
disable-model-invocation: true
---

# Deploy Alpha Page

Trigger the "Deploy Alpha Page" GitHub Actions workflow to deploy the Afterloop alpha downloads page to Cloudflare Pages.

## Usage

- `/deploy` — Deploy using the latest release from the plugin repo
- `/deploy v0.1.9-alpha.1` — Deploy a specific release tag

## Steps

1. If a tag is provided as an argument, trigger with that tag:
   ```bash
   gh workflow run deploy.yml -f tag=<TAG>
   ```

2. If no tag is provided, trigger without inputs (workflow resolves latest release automatically):
   ```bash
   gh workflow run deploy.yml
   ```

3. After triggering, monitor the run:
   ```bash
   gh run list --workflow=deploy.yml --limit=1
   ```

4. Wait a few seconds, then check the run status. Report the run URL and status to the user.
