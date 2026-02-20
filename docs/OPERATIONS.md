Operations note
===============

This repository stores content (content/published/ and content/drafts/) and audit artifacts for the HistoryOfJazz site.

Key operational responsibilities

- OpenClaw (server) — orchestrator
  - OpenClaw running on the configured server is responsible for generating and verifying posts on the schedule configured in the OpenClaw cron jobs.
  - When OpenClaw produces a validated draft (per pipeline rules), it will commit the markdown file to the develop branch and push it to this repository. It will also write audit artifacts under audit/{post-slug}/.
  - OpenClaw is the authority for content generation; the repository is an archive and review surface.

- GitHub Actions — deploy only
  - GitHub Actions are used only for site build & deployment. When you merge develop → main, the existing GitHub Action on main will build and deploy the site (e.g., to Vercel or Netlify) using the site/ or app/ sources.
  - Content generation is intentionally not performed inside GitHub Actions to keep generation and orchestration centralized on the OpenClaw server.

Manual publishing flow

1. OpenClaw commits a validated draft to develop (or a draft to content/drafts/ if verification fails).
2. You review develop and merge develop → main when ready to publish. Merging triggers the GitHub Action to deploy the site.
3. If you need to revert a published post, revert the merge/commit on main and re-deploy.

Secrets & safety

- API keys and credentials are stored in the server environment and in GitHub Actions secrets for deployment only. Do not commit secrets to the repo.
- OpenClaw will include audit artifacts (prompt, raw output, verification notes) for traceability.

Contact

If you need the OpenClaw cron configuration changed or want to pause automatic generation, update the OpenClaw cron job or tell the operator to disable the scheduled job.

