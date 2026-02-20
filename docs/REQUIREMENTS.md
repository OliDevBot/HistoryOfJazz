Project: HistoryOfJazz — OpenClaw‑orchestrated AI blog (learning project)
------------------------------------------------------------------------

1) Purpose
- Produce well‑researched, substantial posts about jazz (roots, movements, artists, Spanish/international perspectives).
- Operate as a low‑friction learning project: accept occasional minor inaccuracies while keeping costs reasonable.

2) Operational principle
- OpenClaw (server) is the content orchestrator: it generates, verifies, and commits drafts to the repository (develop branch).
- GitHub Actions are used only for site build & deployment: merging develop → main triggers the GitHub Action on main which deploys the site.
- Publishing is manual: you (Enrique) merge develop → main when you want the site updated. Auto‑merge is off by default.

3) Model usage
- Use github-copilot/claude-opus-4.6 (via local Copilot auth) for content generation and for verifying drafts against cited sources.
- Use github-copilot/gpt-5-mini for lightweight helpers (formatting, metadata extraction) to reduce costs.

4) Cadence & scope
- Cadence: 2 posts/week (Tue & Fri at 10:00 UTC by default).
- Post format:
  - Frontmatter: title, date (UTC), slug, tags, sources (2+ explicit URLs), excerpt (≤160 chars), confidence (0–1)
  - Body: ~800–1500 words (substantial, well-referenced), with inline citation markers
- Pipeline behavior:
  1. OpenClaw generates a draft with Claude Opus 4.6 (must include 2+ explicit source URLs).
  2. OpenClaw asks Claude to verify the draft against its cited sources and produce a confidence score and a short verification note.
  3. If confidence ≥ publish threshold → OpenClaw commits the markdown and audit artifacts to develop (not to main).
     If retry threshold <= confidence < publish threshold → retry generation once; if still low, write to content/drafts/ for manual review.
     If confidence < retry threshold → save to content/drafts/ and do not stage for merge.
  4. OpenClaw stores audit artifacts (prompt.txt, raw_output.txt, verify_note.txt, confidence.txt, sources.json) under audit/{slug}/.
  5. You merge develop → main to publish; GitHub Actions on main deploy the site.

5) Thresholds & config (config/pipeline.yaml)
- publish_if_confidence: 0.75
- retry_if_confidence: 0.6
- retries: 1
- artifact_retention_days: 90
- cadence: cron expression (default Tue & Fri 10:00 UTC)

6) Safety & minimum policy
- No publishing of PII for private persons; avoid defamatory claims.
- Require explicit source URLs for historical claims.
- Limit direct quotations (short quoted snippets only, with attribution).
- Keep a human review path for low‑confidence outputs.

7) Audit & traceability
- For each committed draft, archive the prompt, raw model output, verification note, confidence, and fetched source snapshots to audit/{slug}/.
- Retain audit artifacts for 90 days; rotate older artifacts.

8) Simplicity & cost control
- Keep pipeline simple for learning: no analytics, no advanced observability initially.
- Prefer gpt-5-mini for helpers; reserve Claude-quality calls for generation & verification.
- Accept occasional minor mistakes early; improve pipeline iteratively.

9) Acceptance criteria (simple)
- The OpenClaw pipeline can generate a validated draft and commit it to develop, with audit artifacts present.
- The draft contains 2+ source URLs and a verification note/confidence in audit/{slug}/.
- Manual merge develop → main successfully triggers the site deploy action.

10) Next steps
- Implement OpenClaw orchestration (cron job + generator + verify) on the server (confirmed).
- Add config/pipeline.yaml, prompt specs, and commit templates to the repo.
- Run a test generation in staging (OpenClaw commits to develop); inspect audit artifacts and the draft.
- After you’re satisfied, continue automated runs on the cadence above.
