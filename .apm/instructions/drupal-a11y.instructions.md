---
description: "Web accessibility standards for Drupal templates and styles. Use when writing or reviewing Twig templates, components, or CSS."
applyTo: "**/*.html.twig, **/components/**/*.twig, **/*.css"
---

# Accessibility Standards

Conform to WCAG 2.2 Level AA. Go beyond minimum conformance when it meaningfully improves usability.

## Core Principles

- Prefer native HTML elements and attributes over ARIA.
- Use ARIA only when native semantics are insufficient. Do not add ARIA to elements where native semantics already work.
- Ensure correct accessible **name, role, value, states, and properties**.
- All interactive elements must be keyboard operable with clearly visible focus and no keyboard traps.

## Structure and Semantics

- Use landmarks: `<header>`, `<nav>`, `<main>`, `<footer>`.
- Use headings to introduce sections. Do not skip heading levels.
- Prefer one `<h1>` for the page topic, typically the first heading inside `<main>`.
- Set a descriptive `<title>` (pattern: "Page — Section — Site").

## Keyboard and Focus

- All interactive elements must be keyboard operable.
- Tab order must follow reading order and be predictable.
- Focus must always be visible.
- Hidden content must not be focusable (`hidden`, `display: none`, `visibility: hidden`).
- Content hidden with `aria-hidden="true"` must not contain focusable descendants.
- Static content must not be tabbable. Exception: use `tabindex="-1"` for programmatic focus targets.

### Skip Link

Provide a skip link as the first focusable element:

    <header>
      <a href="#main-content" class="sr-only">Skip to main content</a>
    </header>
    <main id="main-content" tabindex="-1">
      <h1>Page title</h1>
    </main>

Screen-reader-only utility:

    .sr-only:not(:focus):not(:active) {
      clip: rect(0 0 0 0);
      clip-path: inset(50%);
      height: 1px;
      overflow: hidden;
      position: absolute;
      white-space: nowrap;
      width: 1px;
    }

### Composite Widgets

For components with arrow-key navigation (tabs, listbox, menu, grid, date picker):

- Provide one tab stop for the composite container.
- Manage internal focus with roving `tabindex` or `aria-activedescendant`.

Roving tabindex: one item has `tabindex="0"`, all others `-1`. Arrow keys swap and call `.focus()`.

`aria-activedescendant`: container has `tabindex="0"` and `aria-activedescendant="IDREF"`. Arrow keys update the IDREF.

## Contrast and Color

- Text contrast: at least 4.5:1 (large text ≥24px regular or ≥18.66px bold: 3:1).
- Focus indicators and UI component boundaries: at least 3:1 against adjacent colors.
- Never rely on color alone to convey information (error, success, required, selected). Provide text and/or icons with accessible names.
- Do not invent arbitrary colors. Use project-approved design tokens.
- Avoid alpha transparency for text and key UI affordances — contrast becomes background-dependent.
- Ensure contrast for all states: default, hover, active, focus, visited, disabled.

## High Contrast / Forced Colors

- Never override OS accessibility settings.
- The UI must adapt to Forced Colors mode automatically.
- Use system color keywords (`ButtonText`, `CanvasText`, `Canvas`) inside `@media (forced-colors: active)`.
- Do not use `forced-color-adjust: none` unless absolutely necessary.
- Icons must adapt to text color — use `currentColor` for SVG fill/stroke.

    @media (forced-colors: active) {
      .button {
        border: 2px solid ButtonBorder;
      }
    }

    /* Transparent outline for focus styles using box-shadow */
    .button:focus {
      box-shadow: 0 0 4px 3px rgba(90, 50, 200, 0.7);
      outline: 2px solid transparent;
    }

## Reflow

Content must fit within 320px wide containers without requiring horizontal scrolling to read.

- Use responsive layout primitives (`flex`, `grid`) with fluid sizing.
- Avoid fixed widths that force two-dimensional scrolling at 320px.
- Multi-column layouts must stack into a single column at narrow widths.
- Media and containers must not overflow the viewport (`max-width: 100%`).
- Handle long strings with `overflow-wrap: anywhere`.
- Exception: data tables, maps, diagrams may allow component-level horizontal scrolling.

## Controls and Labels

- Every interactive element must have a visible label that does not disappear on input.
- The accessible name must contain the visible label text (for voice access).
- If multiple controls share the same visible label (e.g., multiple "Remove" buttons), add context via `aria-label` that includes the visible text (e.g., "Remove item: Socks").

## Forms

- Every form control must have a programmatic label. Prefer `<label for="...">`.
- Associate help text with `aria-describedby`.
- Indicate required fields visually and with `aria-required="true"`.
- Use `aria-invalid="true"` for invalid fields; remove when valid.
- Associate inline errors with the field via `aria-describedby`.
- On submit with errors, focus the first invalid control.
- Do not disable submit buttons solely to prevent submission.

## Graphics and Images

- Informative images: provide meaningful `alt` text.
- Informative SVGs: use `role="img"` with `aria-label` or `aria-labelledby`.
- Decorative images: `alt=""`.
- Decorative SVGs/icons: `aria-hidden="true"`.

## Navigation and Menus

- Use `<nav>` with lists and links for navigation.
- Do not use `role="menu"` / `role="menubar"` for site navigation.
- For expandable navigation: use `<button>` elements to toggle, with `aria-expanded` to indicate state.
- `Escape` may close open sub-navigations.

## Tables

- Use `<table>` for tabular data.
- Use `<th>` for column and row headers.
- For interactive data grids: use `role="grid"` with proper row/cell nesting and arrow-key navigation.

## Verification Checklist

Before finalizing, verify:

1. Landmarks, headings, and one `<h1>` for the page topic.
2. Keyboard operable controls, visible focus, predictable tab order, no traps.
3. Skip link present and functional.
4. Visible labels present and included in accessible names.
5. Form labels, required indicators, error handling with `aria-invalid` + `aria-describedby`.
6. Text contrast ≥4.5:1, UI boundaries ≥3:1, color not the only cue.
7. Forced Colors mode does not break the UI.
8. Content reflows at 320px without horizontal scrolling for reading.
9. Informative images have alternatives; decorative images are hidden.
10. Tables use `<th>` headers; interactive grids have proper structure.
