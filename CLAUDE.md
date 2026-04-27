# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project Overview

This is a personal social media content portfolio — a collection of plain-text posts and professional materials for LinkedIn and Substack, organized by platform.

## Structure

```
social/
├── linkedin/
│   ├── about_me.txt              — Professional bio / LinkedIn profile context
│   ├── posts/                    — Individual LinkedIn posts (numbered by publish order)
│   └── suggestion/workbench.txt  — Staging area / ideas for future posts
├── substack/
│   ├── profile.txt               — Substack profile / newsletter description
│   ├── notes/                    — Short Substack notes (numbered, plain text)
│   ├── posts/                    — Full Substack articles (.md or .txt)
│   └── suggestion/gbb.txt        — Staging area / ideas for future posts
└── todo                          — Plain-text task list for content planning
```

## Conventions

### LinkedIn
- Posts live in `linkedin/posts/` and are numbered sequentially with a short topic slug (e.g. `001_webout.txt`, `034_learn_adblock.txt`)
- Numbering reflects publish order, not subject grouping
- Post categories visible in slugs: `learn_*` (tutorials/tips), `tidbit_*` (short observations), `skf_*` / `aigear_*` / `memobowl_*` (project updates), `vibecoding_*`
- `linkedin/suggestion/workbench.txt` holds raw links and ideas before they become posts

### Substack
- Full articles live in `substack/posts/` — numbered sequentially, mostly `.md` (some `.txt`)
- Short notes live in `substack/notes/` — numbered sequentially, plain text
- `substack/suggestion/gbb.txt` holds topic ideas for future articles
- `substack/profile.txt` contains the newsletter bio

### General
- All post files are plain text (or Markdown); no special formatting requirements beyond what the platform expects
- `todo` (root-level file, no extension) is a plain-text task list in Czech for content planning tasks
