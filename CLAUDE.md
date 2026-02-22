# CLAUDE.md

This file tells Claude Code to follow the design system documented in `AGENTS.md`.

---

## Quick Reference

This project is a collection of standalone HTML developer tools.
Every tool is a **single vanilla HTML file** — no build step, no framework.

### Design System Rules (non-negotiable)

1. **Colors** — Always use semantic CSS tokens from `shared/tokens.css`. Never hardcode hex/rgb/hsl outside that file.
2. **Fonts** — DM Sans for UI text, JetBrains Mono for code. No other fonts.
3. **Shared CSS load order** — `tokens.css` → `base.css` → `components.css` → tool `<style>`.
4. **No `!important`** outside `@layer reset`.
5. **No inline `style=""` for static values** — static styles belong in CSS.
6. **Dark only** — `color-scheme: dark`. Never change this.
7. **No responsive breakpoints** (`@media` queries, `prefers-reduced-motion`, ARIA roles).

### Required `<head>` block for every tool

```html
<meta name="color-scheme" content="dark">
<link rel="preconnect" href="https://fonts.googleapis.com">
<link rel="preconnect" href="https://fonts.gstatic.com" crossorigin>
<link href="https://fonts.googleapis.com/css2?family=DM+Sans:ital,opsz,wght@0,9..40,300..700;1,9..40,300..700&family=JetBrains+Mono:ital,wght@0,400;0,500;0,600;1,400&display=swap" rel="stylesheet">
<link rel="stylesheet" href="shared/tokens.css">
<link rel="stylesheet" href="shared/base.css">
<link rel="stylesheet" href="shared/components.css">
<style>/* tool-specific overrides only */</style>
```

### Key Tokens

```css
var(--color-bg)            /* main canvas */
var(--color-bg-panel)      /* sidebar/panel */
var(--color-bg-toolbar)    /* top bar */
var(--color-bg-hover)      /* hover state */
var(--color-text)          /* primary text */
var(--color-text-secondary)/* labels, captions */
var(--color-text-muted)    /* placeholders */
var(--color-border)        /* default border */
var(--color-border-focus)  /* focus ring */
var(--color-accent)        /* primary blue */
var(--color-success)       /* teal green */
var(--color-error)         /* red */
var(--radius-control)      /* inputs, buttons */
var(--radius-card)         /* cards, panels */
var(--font-ui)             /* DM Sans */
var(--font-mono)           /* JetBrains Mono */
```

For full component classes, token inventory, and layer architecture — see `AGENTS.md`.
