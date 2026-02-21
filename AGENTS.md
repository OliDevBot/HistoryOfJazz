# AGENTS.md — AI Agent Instructions for HistoryOfJazz

_This file contains everything an AI agent needs to autonomously generate, verify, translate, and publish posts for this blog. If you're an AI agent taking over this project, read this file completely before doing anything._

---

## 1. Project Overview

**HistoryOfJazz** is an AI-generated, human-curated blog exploring the history of jazz — from Congo Square to the present day. Posts are researched, written, and verified by an AI agent, then reviewed by a human editor before publication.

- **Site:** Hugo static site, deployed via GitHub Pages
- **Repo:** `OliDevBot/HistoryOfJazz` (public)
- **Branch model:** `develop` (agent commits here) → `main` (human merges to publish)
- **Languages:** English (primary) + Spanish translation for each post

---

## 2. Schedule

| Day       | Time (UTC) | Task                          |
|-----------|-----------|-------------------------------|
| Tuesday   | 10:00     | Generate new post (EN + ES)   |
| Friday    | 10:00     | Generate new post (EN + ES)   |

---

## 3. Content Pipeline

### Step 1: Check for Duplicates

Before writing, scan all existing posts in `content/published/` to avoid covering the same topic twice.

```bash
ls content/published/*.md | grep -v '.es.md'
```

Read the titles and slugs. Choose a topic that hasn't been covered yet.

### Step 2: Research and Write (English)

Write a well-researched post about a jazz history topic.

**Requirements:**
- **Length:** ~800–1,500 words (target ~1,400 for substantial posts)
- **Sources:** Minimum 2 explicit, verifiable URLs cited in the post
- **Tone:** Engaging, narrative-driven, educational — not academic or dry
- **Structure:** Introduction → multiple sections with `##` headers → "Why It Matters" conclusion
- **Inline citations:** Use `[1]`, `[2]`, `[3]` markers referencing the `sources` list in frontmatter
- **No AI disclaimers** in the body text

**Topic progression (suggested chronological order):**
1. ✅ Origins in New Orleans (Congo Square, brass bands, Buddy Bolden, Storyville)
2. ✅ Jazz moves to Chicago (Great Migration, King Oliver, Armstrong, Hot Five)
3. The Harlem Renaissance and New York jazz scene
4. The Swing Era and big bands
5. Bebop revolution (Charlie Parker, Dizzy Gillespie, Thelonious Monk)
6. Cool jazz and the West Coast sound
7. Hard bop and the return to roots
8. Modal jazz and Kind of Blue
9. Free jazz (Ornette Coleman, John Coltrane's later work)
10. Jazz fusion and electric Miles
11. Latin jazz and Afro-Cuban connections
12. Jazz in Europe
13. Jazz vocals (Billie Holiday, Ella Fitzgerald, Sarah Vaughan)
14. Modern jazz and the current scene

Feel free to cover sub-topics, specific artists, specific albums, cultural movements, or regional scenes. The list above is a guide, not a strict sequence.

### Step 3: Verify the Post

After writing, self-verify the post against the cited sources:
- Are the historical claims supported by the sources?
- Are dates, names, and events accurate?
- Produce a **confidence score** (0.0–1.0)

**Thresholds:**
| Confidence    | Action                                                      |
|---------------|-------------------------------------------------------------|
| ≥ 0.75        | ✅ Publish — commit to `content/published/`                  |
| 0.60 – 0.74   | 🔄 Retry once — regenerate and re-verify                    |
| < 0.60        | ❌ Draft only — save to `content/drafts/` for human review   |

### Step 4: Save Audit Artifacts

For every generated post, create an audit directory:

```
audit/<slug>-<timestamp>/
├── prompt.txt          # The prompt used to generate the post
├── raw_output.txt      # The raw model output (first few lines or summary)
├── verify_note.txt     # Verification notes (what was checked, any concerns)
├── confidence.txt      # The confidence score (just the number, e.g. "0.85")
└── sources.json        # Array of source URLs used
```

**Timestamp format:** `YYYYMMDDTHHMMSSZ` (UTC)

### Step 5: Translate to Spanish

Translate the English post to Spanish. This is a **quality translation**, not machine-translated output:
- Natural, fluent Spanish prose
- Preserve the narrative tone and structure
- Keep all frontmatter fields but translate `title`, `excerpt`, and `tags`
- Keep `slug`, `date`, `sources`, and `confidence` identical to the English version
- Save as the same filename with `.es.md` extension

### Step 6: Commit and Push

Commit both files (EN + ES) plus audit artifacts to the `develop` branch.

**⚠️ CRITICAL: Never use `git add .` or `git add -A`**

Always add specific files only:

```bash
# Add the posts
git add content/published/<timestamp>-<slug>.md
git add content/published/<timestamp>-<slug>.es.md

# Add audit artifacts
git add audit/<slug>-<timestamp>/

# Commit with a descriptive message
git commit -m "post: <short title description>"

# Push to develop
git push origin develop
```

**Why:** The repo is public. Never commit secrets, API keys, environment files, or personal data.

---

## 4. File Naming Convention

### Posts
```
content/published/<YYYYMMDDTHHMMSSZ>-<slug>.md       # English
content/published/<YYYYMMDDTHHMMSSZ>-<slug>.es.md     # Spanish
```

Example:
```
content/published/20260219T111433Z-origins-of-jazz-new-orleans.md
content/published/20260219T111433Z-origins-of-jazz-new-orleans.es.md
```

### Audit artifacts
```
audit/<slug>-<YYYYMMDDTHHMMSSZ>/
```

---

## 5. Post Frontmatter Schema

```yaml
---
title: "Full Title of the Post"
date: "2026-02-19T11:55:00Z"          # UTC ISO-8601
slug: "url-friendly-slug"
tags: [history, new-orleans, origins]  # Lowercase, relevant keywords
excerpt: "One-sentence summary, max 160 characters."
sources:
  - "https://example.com/source-1"
  - "https://example.com/source-2"
confidence: 0.85                       # 0.0–1.0
---
```

**Spanish frontmatter** is identical except:
- `title` → translated
- `excerpt` → translated
- `tags` → translated (e.g., `history` → `historia`)

---

## 6. Model Recommendations

| Task                    | Recommended Model                   |
|-------------------------|-------------------------------------|
| Post generation         | Claude Opus 4.6 (or equivalent)     |
| Post verification       | Claude Opus 4.6 (or equivalent)     |
| Spanish translation     | Claude Opus 4.6 (or equivalent)     |
| Lightweight helpers     | GPT-5 Mini (or equivalent, cheaper) |

Use the strongest available model for content generation and translation. Use cheaper models only for metadata extraction, formatting, or other trivial tasks.

---

## 7. Repository Structure

```
HistoryOfJazz/
├── content/
│   ├── published/          # Final posts (EN + ES), ready for site build
│   ├── drafts/             # Low-confidence posts for human review
│   └── README.md           # Content guidelines
├── audit/                  # Per-post audit artifacts
├── site/                   # Hugo site
│   ├── config.toml         # Site config (title, menu, i18n)
│   ├── content/
│   │   ├── _index.md       # Home page
│   │   ├── _index.es.md    # Home page (ES)
│   │   └── blog/           # Blog posts (copied here at build time)
│   ├── layouts/            # Hugo templates
│   └── static/css/         # Styles
├── docs/
│   ├── REQUIREMENTS.md     # Project requirements and pipeline spec
│   └── OPERATIONS.md       # Operational notes
├── .github/workflows/
│   └── gh-pages.yml        # GitHub Action: build + deploy on push to main
├── README.md               # Human-readable project overview
└── AGENTS.md               # ← You are here
```

---

## 8. Deployment

The agent does **not** deploy. Deployment is handled automatically:

1. Agent commits to `develop`
2. Human reviews and merges `develop` → `main`
3. GitHub Action on `main` builds Hugo site and deploys to GitHub Pages
4. The Action copies `content/published/*.md` into `site/content/blog/` before building

---

## 9. Safety Rules

- **Never commit secrets, API keys, `.env` files, or personal data**
- **Never use `git add .`** — always add specific files
- **Never push directly to `main`** — always use `develop`
- **No PII** of private persons in posts
- **No defamatory claims** — stick to well-documented history
- **Limit direct quotations** — short quoted snippets only, with attribution
- **Verify `.gitignore`** covers sensitive files before committing

---

## 10. Quality Checklist

Before committing a post, verify:

- [ ] Title is engaging and descriptive
- [ ] Post is ~800–1,500 words
- [ ] At least 2 source URLs are cited and accessible
- [ ] Inline citation markers `[1]`, `[2]`, etc. match frontmatter `sources` list
- [ ] Confidence score ≥ 0.75
- [ ] Audit artifacts are saved in `audit/<slug>-<timestamp>/`
- [ ] Spanish translation reads naturally (not machine-translated feel)
- [ ] Spanish frontmatter fields are translated (title, excerpt, tags)
- [ ] Filename follows convention: `<timestamp>-<slug>.md` / `.es.md`
- [ ] No duplicate topic in `content/published/`
- [ ] Committed only specific files (no `git add .`)

---

## 11. Troubleshooting

**Post rejected (confidence < 0.75):**
Retry once with a different angle or better sources. If still low, save to `content/drafts/` and notify the human.

**Git push fails:**
Check that you're on the `develop` branch and have push access. Run `git status` and `git remote -v` to debug.

**Duplicate topic:**
Read all existing post titles in `content/published/` before generating. If overlap is found, pick a different topic.

**Translation quality issues:**
Use the strongest available model for translation. Avoid models known for stiff, literal translations.

---

## 12. Example: Full Pipeline Run

```bash
# 1. Pull latest
cd /path/to/historyofjazz
git checkout develop
git pull origin develop

# 2. Check existing posts
ls content/published/*.md | grep -v '.es.md'

# 3. Generate post (agent does this internally)
#    → writes content/published/20260225T100000Z-bebop-revolution.md
#    → writes audit/bebop-revolution-20260225T100000Z/*

# 4. Verify confidence ≥ 0.75 → proceed

# 5. Translate to Spanish
#    → writes content/published/20260225T100000Z-bebop-revolution.es.md

# 6. Commit and push
git add content/published/20260225T100000Z-bebop-revolution.md
git add content/published/20260225T100000Z-bebop-revolution.es.md
git add audit/bebop-revolution-20260225T100000Z/
git commit -m "post: the bebop revolution — Parker, Gillespie, and the birth of modern jazz"
git push origin develop
```

---

_This file is the single source of truth for any AI agent operating this blog. If the process changes, update this file first._
