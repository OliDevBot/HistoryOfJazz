Project: HistoryOfJazz — Fully-automated, low-friction AI blog (learning project)

1) Purpose
- Automatically publish short, well-sourced posts about jazz (roots, movements, artists, Spanish/international stories).
- Keep the system simple and inexpensive; accept occasional minor inaccuracies during early learning.

2) Core decision (model usage)
- Use Claude Opus 4.6 for content authoring and automated quality verification (generation + fact-check).
- Use github-copilot/gpt-5-mini for auxiliary tasks (formatting, metadata, lightweight helpers) to reduce cost.

3) Minimum scope (MVP)
- Publish cadence: 2 posts/week.
- Post format (per publish):
  - Title, date (UTC), slug, tags, excerpt (≤160 chars)
  - Body: ~300–600 words
  - Sources: 2 explicit URLs (required)
  - Confidence score (0–1) produced by Claude’s verification step
- Pipeline (simple):
  1. Generate draft with Claude Opus 4.6 (content + 2 source URLs).
  2. Have Claude verify the draft against its cited sources and return a confidence score and short verification note.
  3. If confidence >= 0.75 → publish (commit markdown to content/published/).
     If 0.6 ≤ confidence < 0.75 → retry generation once.
     If confidence < 0.6 → save to content/drafts/ (manual review).
  4. After publish, store prompt, raw output, sources and verification note in an audit folder (local to repo or object store).
- Duplicate check: basic similarity check to avoid near-duplicates (simple hashing or token overlap is fine for now).

4) Safety & minimal policy
- Do not publish personal data of private individuals or obvious defamatory claims.
- Require explicit source URLs for historical claims.
- Limit direct quoted text (short quotes only, with attribution).

5) Non-functional (lightweight)
- Keep cost control in mind: prefer GPT-5-mini for supporting steps; use Claude only for generation + verification.
- No analytics, advanced observability, or escalation rules required initially — this is a learning project.
- Artifact retention: keep last 90 days of audit artifacts (simple file rotation is fine).

6) Acceptance criteria (simple)
- The pipeline can generate, verify, and publish a single test post end-to-end to content/published/ using the develop branch and the configured secrets.
- The published post contains:
  - the content markdown,
  - at least 2 source URLs,
  - a verification note and confidence score saved in audit/{post-slug}/.
- You can manually revert a publish by reverting the commit (manual rollback is acceptable for now).

7) Configurable thresholds (config/pipeline.yaml)
- publish_if_confidence: 0.75
- retry_if_confidence: 0.6
- retries: 1
- artifact_retention_days: 90

8) Next steps after you confirm
- I will commit this simplified REQUIREMENTS.md to develop.
- Then I’ll implement generator + verify pipeline in develop in staging mode (one test run that writes to content/published/ and audit/) for your review.
- After you’re happy, we can enable scheduled runs and let it run automatically.
