# Tools

A collection of standalone developer utility tools. Each tool is a single vanilla HTML file — no build step, no framework, no bundler. Tools are served directly from disk or a static host.

## Structure

```
tools/
├── index.html          # Tool directory / landing page
├── tools/              # Individual tool files
└── shared/
    ├── tokens.css      # Design system CSS custom properties (VSCode Dark Modern palette)
    ├── base.css        # CSS reset, layer stack, element defaults, utilities
    └── components.css  # Reusable UI component classes
```

## Design System

All tools share a unified design system built on the **VSCode Dark Modern** color palette. Typography uses **DM Sans** for UI text and **JetBrains Mono** for code. Colors are defined as CSS custom properties in `shared/tokens.css` — no hardcoded hex values in tool files.

Every tool links the shared CSS in order: `tokens.css` → `base.css` → `components.css`, then adds tool-specific styles inline.

## Adding a Tool

1. Create a new `.html` file in `tools/`
2. Link the shared CSS from `../shared/`
3. Follow the design system rules in `AGENTS.md`
4. Add a card to `index.html`

No build process required.
