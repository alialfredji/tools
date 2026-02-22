# AGENTS.md — Dev Tools Design System

Agent instructions for the `tools` repository.
This file is read by AI coding agents (Copilot, Claude, Cursor, Windsurf, Codex, etc.) on every request.
Keep it concise. Detailed docs live in `shared/` file headers and `DESIGN_SYSTEM_PLAN.md`.

---

## Project

A collection of standalone developer utility tools. Each tool is a **single vanilla HTML file** — no build step, no framework, no bundler. All files are served directly from disk or a static host.

**Tools:**
- `index.html` — tool directory / landing page
- `blur-image.html` — blur sensitive areas of images
- `fake-tweet.html` — generate fake tweet screenshots
- `jsoneditor.html` — JSON editor and formatter
- `markdown-merger.html` — merge multiple markdown files
- `markdown-renderer.html` — markdown + Mermaid live preview
- `pdf-to-markdown.html` — convert PDFs to markdown

---

## Design System

### North Star
**VSCode Dark Modern** palette. Every color in every tool must trace back to a token in `shared/tokens.css`. No hardcoded hex values outside that file.

### Fonts
| Font | Use | Weight |
|------|-----|--------|
| **DM Sans** | All UI text, labels, buttons, body copy | 300–700 |
| **JetBrains Mono** | Code, `<pre>`, `<code>`, monospace inputs | 400, 500, 600 |

Font loading pattern (copy exactly into every tool `<head>`):
```html
<meta name="color-scheme" content="dark">
<link rel="preconnect" href="https://fonts.googleapis.com">
<link rel="preconnect" href="https://fonts.gstatic.com" crossorigin>
<link href="https://fonts.googleapis.com/css2?family=DM+Sans:ital,opsz,wght@0,9..40,300..700;1,9..40,300..700&family=JetBrains+Mono:ital,wght@0,400;0,500;0,600;1,400&display=swap" rel="stylesheet">
```

### Shared CSS Files

| File | Purpose |
|------|---------|
| `shared/tokens.css` | CSS custom property definitions — primitives and semantics |
| `shared/base.css` | `@layer` stack declaration, CSS reset, element defaults, utilities |
| `shared/components.css` | Reusable UI component classes |

**Required `<head>` link order** (after fonts):
```html
<link rel="stylesheet" href="../shared/tokens.css">
<link rel="stylesheet" href="../shared/base.css">
<link rel="stylesheet" href="../shared/components.css">
<style>/* tool-specific overrides only */</style>
```
Adjust the relative path `../shared/` if tool files move.

---

## Rules

These rules are absolute. Do not deviate.

1. **Never hardcode a color hex, rgb(), or hsl() value** outside of `shared/tokens.css`. Always use a semantic token: `var(--color-bg)`, `var(--color-text)`, `var(--color-accent)`, etc.

2. **Never modify primitive tokens** (the `--color-neutral-*`, `--color-blue-*`, etc. raw values in `shared/tokens.css`). To adjust a color, change a semantic token or add a new semantic token.

3. **Never use `!important`** except inside `@layer reset` (where it is already used correctly).

4. **Never use inline `style=""` attributes** for anything beyond one-off dynamic values set by JavaScript (e.g., `element.style.width = computed + 'px'`). Static styles belong in CSS.

5. **Always use semantic tokens** in component and layout CSS, not primitive tokens. Example:
   - ✅ `color: var(--color-text-secondary);`
   - ❌ `color: var(--color-neutral-550);`
   - ❌ `color: #858585;`

6. **Always load the shared CSS in order**: `tokens.css` → `base.css` → `components.css` → tool `<style>`. The `@layer` declaration in `base.css` governs cascade priority for all three files.

7. **Tool-specific CSS goes in an inline `<style>` tag** after the shared `<link>` tags. Do not create per-tool `.css` files unless the tool has >200 lines of unique styles.

8. **Prefer `@layer overrides { }` for tool overrides** when the tool needs to change a shared component's appearance. This keeps cascade intent explicit.

9. **For the `fake-tweet.html` preview area**: the tweet card renders in an isolated `<div>`. Do not attempt to load parent styles into the tweet card — it has its own hardcoded Twitter-accurate styles that must NOT inherit from the design system.

10. **No system-font fallbacks in DM Sans contexts**. The full font stack is defined in `--font-ui`; never use `-apple-system` or `BlinkMacSystemFont` directly in tool CSS.

---

## CSS Layer Stack

```
@layer reset, tokens, base, components, utilities, overrides;
         ↑                                               ↑
      lowest priority                            highest priority
```

Un-layered styles (anything not wrapped in `@layer`) beat all layered styles.
Use this for emergency one-off fixes only.

---

## Token Quick Reference

```css
/* Backgrounds */
var(--color-bg)           /* main canvas */
var(--color-bg-panel)     /* sidebar / panel */
var(--color-bg-toolbar)   /* top bar */
var(--color-bg-hover)     /* hover state */
var(--color-bg-active)    /* selected / active */

/* Text */
var(--color-text)          /* primary body text */
var(--color-text-secondary)/* labels, captions */
var(--color-text-muted)    /* placeholders */
var(--color-text-link)     /* links */
var(--color-text-code)     /* inline code */

/* Borders */
var(--color-border)        /* default border */
var(--color-border-focus)  /* focus ring */

/* Interactive */
var(--color-accent)        /* primary accent (blue) */
var(--color-accent-bg)     /* filled button background */

/* Status */
var(--color-success) var(--color-warning) var(--color-error) var(--color-info)

/* Fonts */
var(--font-ui)             /* DM Sans stack */
var(--font-mono)           /* JetBrains Mono stack */

/* Spacing (4px grid) */
var(--space-1)  /* 4px */   var(--space-2)  /* 8px */
var(--space-3)  /* 12px */  var(--space-4)  /* 16px */
var(--space-5)  /* 20px */  var(--space-6)  /* 24px */
var(--space-8)  /* 32px */  var(--space-10) /* 40px */

/* Radius */
var(--radius-control)  /* inputs, buttons */
var(--radius-card)     /* cards, panels */
var(--radius-badge)    /* tags, pills */
```

---

## Component Classes Quick Reference

```html
<!-- Layout -->
<div class="page">               <!-- full-page wrapper -->
<div class="container">          <!-- max-width centered -->
<div class="split-layout">       <!-- two-pane editor/preview -->
<nav class="toolbar">            <!-- top toolbar bar -->

<!-- Cards / Panels -->
<a href="..." class="card">      <!-- tool link card -->
<div class="panel">              <!-- bordered panel -->
  <div class="panel__header"><h3 class="panel__title">...</h3></div>
  <div class="panel__body">...</div>
</div>

<!-- Buttons -->
<button class="btn">             <!-- primary -->
<button class="btn btn--outline">
<button class="btn btn--ghost">
<button class="btn btn--danger">
<button class="btn btn--sm">     <!-- size modifier -->
<button class="btn btn--icon">   <!-- icon-only square -->

<!-- Form controls -->
<div class="form-group">
  <label class="label">Field</label>
  <input class="input" type="text">
  <p class="field-message">hint</p>
</div>
<input class="input input--mono" type="text">   <!-- monospace input -->
<textarea class="input"></textarea>
<select class="input">...</select>

<!-- Status -->
<span class="badge">default</span>
<span class="badge badge--accent">active</span>
<span class="badge badge--error">error</span>

<!-- Code -->
<div class="code-block">
  <div class="code-block__header">
    <span class="code-block__lang">json</span>
    <button class="btn btn--ghost code-block__copy btn--sm">Copy</button>
  </div>
  <pre><code>...</code></pre>
</div>

<!-- Tabs -->
<div class="tabs">
  <div class="tabs__list" role="tablist">
    <button class="tabs__tab is-active" role="tab">Tab 1</button>
    <button class="tabs__tab" role="tab">Tab 2</button>
  </div>
  <div class="tabs__panel">...</div>
</div>

<!-- Feedback -->
<div class="loading">Loading...</div>
<div class="empty-state">
  <div class="empty-state__icon">📭</div>
  <p class="empty-state__title">Nothing here</p>
</div>

<!-- Utilities -->
class="u-flex u-items-center u-gap-2"
class="u-text-muted u-text-sm u-font-medium"
class="u-truncate"
class="u-sr-only"   <!-- screen-reader only -->
```

---

## What NOT to do

- ❌ Add a new `.css` file per tool (unless the tool genuinely warrants it)
- ❌ Copy-paste the reset or token declarations into a tool's inline `<style>`
- ❌ Use `position: fixed` without `z-index: var(--z-*)` tokens
- ❌ Introduce a new font (stick to DM Sans + JetBrains Mono)
- ❌ Change `color-scheme` to anything other than `dark` — all tools are dark-only
- ❌ Wrap tool-specific `<style>` in `@layer base` or `@layer components` — those are reserved for shared files
- ❌ Reference Twitter/X brand colors (used only inside the `.tweet-card` scope in `fake-tweet.html`)

---

## Detailed Documentation

- Token architecture & naming: see `shared/tokens.css` file header
- Layer stack rationale: see `shared/base.css` file header
- Component API: see `shared/components.css` file header
- Full technical decisions: see `DESIGN_SYSTEM_PLAN.md`
