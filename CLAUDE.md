# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project Overview

**2026 Persona Bracket Picker** — A March Madness bracket generator by Hurrdat Marketing that maps quiz answers to marketing personas, then uses persona traits to deterministically generate bracket predictions.

This is a **single-file vanilla SPA** (`index.html`, ~1,346 lines). No framework, no build system, no npm dependencies. All HTML, CSS, and JavaScript live in one file.

## Development

There is no build step, package manager, test suite, or linter. To develop:

- Open `index.html` in a browser (or use a local HTTP server like `python -m http.server`)
- All changes are made directly in `index.html`

External resources loaded at runtime: Google Fonts (Open Sans) and the Hurrdat logo SVG from hurrdatmarketing.com.

## Architecture

### Data Flow

User name + 8 quiz answers → hash-based seed → seeded RNG → persona determination → trait calculation → bracket simulation → rendered results with tab UI

### Key Constants (top of `<script>`)

- `BRACKET` — Full 64-team tournament data (4 regions, 16 seeds each, with records)
- `FIRST_FOUR` — Play-in game matchups
- `R1_MATCHUPS` — First-round matchup data with upset risk scores (1-10) and injury flags
- `PERSONAS` / `PERSONA_TRAITS` — 4 marketing personas with chalk/upset/dominant profiles
- `COMPANIES` — Company matches for each persona track

### Persona System

Quiz answers (values 0-3) tally votes to 4 company tracks. Highest tally determines persona:

| Track | Persona | Company | Dominant Trait |
|-------|---------|---------|---------------|
| 0 | SEO Strategist | Hurrdat Marketing | data (high chalk) |
| 1 | Storyteller | Hurrdat Media | narrative (high upsets) |
| 2 | Reputation Builder | Lukas Partners | brand (very high chalk) |
| 3 | Market Dominator | Canary & Coal | regional (moderate) |

"Boldness" modifier is computed from odd-indexed quiz answers, shifting chalk/upset balance.

### Bracket Algorithm

- **Seeded RNG**: `hashCode()` produces seed from name + answer indices; `seededRandom()` implements LCG for deterministic output
- **`pickWinner()`**: Core logic using seed differential, win percentage, persona traits (chalk vs upset tension), injury flags, and dominant dimension modifiers
- **`simulateRegion()`**: Runs 4 rounds per region, then Final Four and championship

### UI Sections

1. Hero header → 2. Quiz form (name + 8 dropdowns) → 3. Loading overlay (1500ms) → 4. Results (persona card + company match + tabbed regional brackets + Final Four + copy/restart buttons)

### CSS Theme

Primary colors: `--coral: #EC6861`, `--orange: #F47933`, `--blue: #2EA3F2`. Gradient accents coral→orange. Responsive breakpoint at 600px converts 2-column form grid to single column; bracket uses horizontal scroll on mobile.
