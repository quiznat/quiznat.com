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

## Design Principles

These rules govern all visual and interaction decisions. Derived from Dieter Rams, Vercel's Web Interface Guidelines, and UI Skills.

### Motion
- Only animate `transform` and `opacity` (GPU-accelerated properties)
- Never use `transition: all` — explicitly list intended properties
- Honor `prefers-reduced-motion` with a `reduce` media query that disables all transitions and transforms
- Animate only to clarify cause & effect or add deliberate delight

### Shadows & Depth
- Use dual-layer shadows (ambient + directional light) on elevated surfaces
- Tint borders and shadows toward the element's accent hue on colored backgrounds

### Accessibility
- Every interactive element must show a visible `:focus-visible` ring
- Touch targets must be at minimum 44px on mobile
- Never rely on color alone to convey meaning
- Set `color-scheme: dark` on `<html>` so native controls and scrollbars match the theme

### Typography
- Use `font-variant-numeric: tabular-nums` on any aligned numeric content
- Use curly quotes, proper ellipsis (`…`), and non-breaking spaces for units

### Layout
- Adjust optical alignment ±1px when perception beats geometry
- Set explicit `width`/`height` on images to prevent cumulative layout shift
- Concentric border radii: child radius ≤ parent radius, maintaining consistent inset

### Performance
- Prefer CSS over JS for layout and animation
- Lazy-load below-the-fold images; set dimensions to reserve space
- Set `<meta name="theme-color">` to match the page background

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

The landing page features six projects:
- design.quiznat.com (Design Studio)
- match.quiznat.com (It's a Match!)
- tiletown.quiznat.com (Tiletown)
- blockblast.quiznat.com (Block Blast)
- fidchell.quiznat.com (Fidchell)
- Santa's Nordic Workshop (Coming Soon — not linked)
