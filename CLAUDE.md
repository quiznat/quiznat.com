# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project Overview

Quiznat.com is a static HTML/CSS portfolio and landing page for strategy games and design projects. No build system, no dependencies, no backend - just static files served via GitHub Pages.

## Development

**No build tools required.** Edit HTML/CSS directly and push to main branch for deployment.

- Main page: `index.html`
- Styles: `styles.css` (design tokens-based architecture)
- Preview locally: Open `index.html` in a browser

## Architecture

### CSS Design System

All styling uses CSS custom properties (design tokens) defined at the top of `styles.css`:
- Color palette with light/dark theme support (`--color-*`)
- Spacing scale from 4px to 96px (`--space-*`)
- Typography scale (`--text-*`, `--font-*`)

Theme switching uses `data-theme` attribute on `<html>` with automatic detection via `prefers-color-scheme`.

### Card Color Sequence

Project cards use a **5-color cycling palette** that matches the hero gradient. Colors are assigned automatically by position using `nth-child` selectors—no color classes needed on individual cards.

| Position | Color | Variable |
|----------|-------|----------|
| 1, 6, 11... | Purple | `--color-design` |
| 2, 7, 12... | Green | `--color-game` |
| 3, 8, 13... | Yellow | `--color-casual` |
| 4, 9, 14... | Cyan | `--color-incremental` |
| 5, 10, 15... | Red | `--color-gamejam` |

To add a new card: just add a `.project-card` element. The color is determined by its position in the grid.

### Visual Effects

Decorative elements use pseudo-elements extensively:
- Grid overlay: `body::before`
- Centerline accent: `body::after`
- Card hover effects: Multi-layer `::before` and `::after` on `.project-card`
- Status badge animations: `@keyframes pulse` for "Live" indicators

### Responsive Breakpoints

- Desktop: default
- Tablet: 768px
- Mobile: 480px

## Brand Voice

The `Communication_System/` directory defines distinct voices for different surfaces:

| File | Surface | Character |
|------|---------|-----------|
| Canon.md | quiznat.com | Impersonal, precise, minimal, structural |
| Builder_X.com.md | Twitter/X (@Quiznat) | Process-forward, iterative, shows momentum |
| Product_Template.md | Game sites | Warm, emotion-first, frames features as feelings |
| Product_It's_a_Match!.md | match.quiznat.com | Cheerful guide, targets children/families |
| Synergy.md | Reference | How voices relate ("Nothing should collapse into anything else") |

**Key principle from Synergy.md**: "Nothing should collapse into anything else." Each surface maintains its distinct voice.

When writing content for this site (quiznat.com), use the Canonical Voice: state principles, document systems, avoid hype and personal anecdotes.

## Project Links

The landing page links to six external projects:
- design.quiznat.com (Design Studio)
- match.quiznat.com (It's a Match!)
- tiletown.quiznat.com (Tiletown)
- blockblast.quiznat.com (Block Blast)
- fidchell.quiznat.com (Fidchell)
- santa.quiznat.com (Santa's Workshop)
