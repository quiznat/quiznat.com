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
- Project-specific accent colors (`--accent-design`, `--accent-game`, etc.)

Theme switching uses `data-theme` attribute on `<html>` with automatic detection via `prefers-color-scheme`.

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
| Builder_X.com.md | Twitter/X | Process-forward, iterative, shows momentum |
| Product_Template.md | Game sites | Warm, emotion-first, frames features as feelings |
| Product_It's_a_Match!.md | match.quiznat.com | Cheerful guide, targets children/families |

**Key principle from Synergy.md**: "Nothing should collapse into anything else." Each surface maintains its distinct voice.

When writing content for this site (quiznat.com), use the Canonical Voice: state principles, document systems, avoid hype and personal anecdotes.

## Project Links

The landing page links to five external projects:
- design.quiznat.com (Design Studio)
- tiletown.quiznat.com (Tiletown)
- match.quiznat.com (It's a Match!)
- blockblast.quiznat.com (Block Blast)
- santa.quiznat.com (Santa's Workshop)
