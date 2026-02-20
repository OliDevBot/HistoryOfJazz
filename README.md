# History of Jazz 🎷

An AI-generated, human-curated blog exploring the history of jazz — from Congo Square to the present day.

Posts are researched, written, and verified by [OpenClaw](https://openclaw.ai) using Claude Opus 4.6, then reviewed by a human editor before publication.

## Site Structure

```
HistoryOfJazz/
├── content/
│   └── published/              # Markdown posts (.md + .es.md translations)
├── audit/                      # Per-post audit artifacts (sources, confidence, prompts)
├── site/                       # Hugo site
│   ├── config.toml             # Site config (title, menu, i18n)
│   ├── content/
│   │   ├── _index.md           # Home page content
│   │   └── blog/_index.md     # Blog listing intro
│   ├── layouts/
│   │   ├── _default/
│   │   │   └── baseof.html    # Base template (header, nav, footer)
│   │   ├── index.html          # Home: intro + latest 5 posts
│   │   └── blog/
│   │       ├── list.html       # Blog: full post archive
│   │       └── single.html     # Post: article + sources + prev/next
│   └── static/css/style.css    # Minimal responsive theme
├── .github/workflows/
│   └── gh-pages.yml            # Build Hugo + deploy to GitHub Pages
└── README.md
```

## How It Works

1. **OpenClaw** generates a new post (2×/week, Tue & Fri) using Claude Opus 4.6
2. Each post is fact-checked against primary sources, scored for confidence (≥0.75 to publish), and audit artifacts are saved alongside
3. A Spanish translation is generated automatically
4. Everything is committed to the `develop` branch
5. A human reviews and merges `develop` → `main`
6. Push to `main` triggers a GitHub Action that builds the Hugo site and deploys to GitHub Pages

## Publishing Flow

```
OpenClaw (Claude Opus 4.6)
    │
    ▼
  Research → Write → Verify → Translate (ES)
    │
    ▼
  Commit to develop branch
    │
    ▼
  Human review & merge → main
    │
    ▼
  GitHub Action → Hugo build → GitHub Pages
```

## Local Preview

Install [Hugo](https://gohugo.io/) and run:

```bash
# Copy posts into Hugo content dir
cp content/published/*.md site/content/blog/

# Start dev server
hugo server -s site
```

## Stack

- **Content engine:** [OpenClaw](https://openclaw.ai) + Claude Opus 4.6
- **Site generator:** [Hugo](https://gohugo.io/)
- **Hosting:** GitHub Pages
- **Translations:** GPT-5 Mini via OpenClaw

## License

Content © 2026. All rights reserved.
