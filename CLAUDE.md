# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project Overview

MirrorFlow Classic is a communication coaching and training facilitation tool, deployed as a static site at `mirrorflowclassic.theguide.club` via GitHub Pages. It is a **zero-dependency, single-file web application** — all application code, styles, curriculum data, and assets live in `index.html` (~11,000 lines).

## Running the App

No installation or build step required:

```bash
# Open directly in browser
open index.html

# Or serve locally
python -m http.server 8080
# then visit http://localhost:8080
```

There is no package manager, build system, linter, or test runner in this project.

## Architecture

The entire application is an IIFE in `index.html`. Within it, the code is organized into logical sections:

### MFEngine (lines ~700–5273)
The core analysis engine. Key methods:
- `analyzeRead()` — parses received communication for urgency signals, tone, and clarity
- `analyzeWrite()` — evaluates outgoing communication (CTAs, openings, tone)
- `analyzeAudit()` — flags jargon, spelling issues, and quality problems
- `synthesize()` — combines findings into structured output
- `buildMirror()` — simulates how different personas would perceive a given text

Key data structures inside MFEngine: `URGENCY_TOKENS`, `HEDGE_TOKENS`, `PASSIVE_RE`, `POSITIVE_WORDS`, `NEGATIVE_WORDS`, `MISSPELLINGS`.

### Content & Curriculum (lines ~5280–9500)
Embedded educational content:
- `CURRICULUM[]` — 5 training pillars (foundation, conversation, influence, communication, practice)
- `MODULE_LIBRARY[]` — 14 learning modules with metadata
- `TEMPLATES[]` — 15+ communication templates (call scripts, coaching debriefs, session plans)
- `PILLAR_KEYWORDS{}`, `MODULE_LEDES{}`, `MODULE_CONTENT{}` — supporting module data

### UI Modes
Switched via `switchMode()`. The 7 panels are: `landing`, `analyse`, `build`, `facilitate`, `library`, `assess`, `about`. The sidebar controls which panel is active.

### State Management
All persistence is client-side:
- **localStorage** — notes, bookmarks, theme preferences, ignored findings, slides, scores
- Helper pattern: `persistGet(key)` / `persistSet(key, value)` wrap localStorage throughout

### Data Flow
```
User input (textarea or file)
  → MFEngine.run(text)
  → { findings, headline, tryThis, shipReady, evidence, mirror }
  → DOM rendering functions
  → persistSet() to localStorage
```

## Theming

Two CSS themes are defined via CSS custom properties at the top of the `<style>` block:
- `press` — dark editorial theme (default)
- `cream` — light editorial theme

Toggle is handled client-side; preference persists in localStorage.

## Deployment

Pushing to the main branch automatically deploys via GitHub Pages. The `CNAME` file sets the custom domain. There are no CI workflows in `.github/workflows/`.

## Conventions

- **No modules or imports** — everything is a globally-scoped variable or function within the IIFE.
- **DOM manipulation is direct** — `document.getElementById`, `innerHTML` assignments, no virtual DOM.
- **toast()** — use this utility for user-facing notifications (already defined globally).
- **copyText()** — utility for clipboard operations.
- Changes to curriculum or template content are made directly in the `CURRICULUM[]`, `MODULE_LIBRARY[]`, and `TEMPLATES[]` arrays.
- The slide canvas editor uses the HTML5 Canvas API; voice/dictation uses the Web Speech API.
