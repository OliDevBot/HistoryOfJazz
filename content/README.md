Content folder - HistoryOfJazz

This folder contains article content and drafts managed by OpenClaw.

Folders

- content/published/
  - Finalized markdown files that have passed automated verification and are ready for human review and merge into main.
  - Filename convention: YYYY-MM-DD-slug.md
    - Example: 2026-02-19-roots-of-jazz.md

- content/drafts/
  - Lower-confidence drafts or works-in-progress that require manual editing or review.
  - Same frontmatter schema as published files.

Frontmatter schema (YAML)

Each markdown file must begin with a YAML frontmatter block containing at least the following keys:

---
title: "Post title"
date: "2026-02-19T10:00:00Z"   # ISO 8601 UTC
slug: "roots-of-jazz"            # kebab-case, unique within repo
tags: [history, new-orleans]
excerpt: "Short summary (max 160 chars)"
sources:
  - "https://example.com/source1"
  - "https://example.com/source2"
confidence: 0.82                 # float between 0.0 and 1.0 (auto-inserted by verification)
---

Body

- The body should be substantial (recommended 800–1500 words for this project) and include inline citation markers where appropriate (e.g., [1], [2]).
- Keep direct quotations short and attribute them to the source.

Audit artifacts

For each published post, OpenClaw will create an audit folder under audit/{slug}/ containing:
- prompt.txt           # the prompt used to generate the post
- raw_output.txt       # raw model output before formatting
- verify_note.txt      # short verification result / notes
- confidence.txt       # numeric confidence value
- sources.json         # fetched source snapshots or metadata

Workflow

- OpenClaw (server) generates and verifies content and commits validated markdown to content/published/ on the develop branch.
- You (Enrique) merge develop → main to publish the site; GitHub Actions on main handle deployment.

