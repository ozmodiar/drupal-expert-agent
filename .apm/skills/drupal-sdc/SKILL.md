---
name: drupal-sdc
description: "Scaffold a new Drupal Single Directory Component (SDC) with component.yml, Twig template, and optional CSS/JS."
argument-hint: "Component name in kebab-case, e.g. 'event-card'"
---

# Create SDC Component

Scaffold a new Drupal Single Directory Component following Drupal conventions.

## When to Use

- Creating a new reusable UI component.
- Scaffolding a component from a design spec or wireframe.
- Adding a new SDC to the theme.

## Procedure

1. **Gather requirements**: Ask for the component name (kebab-case), purpose, props, and whether it needs CSS or JavaScript.

2. **Check existing components**: Search the theme's `components/` directory for similar components to extend or reference. Follow existing naming and structural patterns.

3. **Create the component directory**: `components/<name>/`

4. **Create component.yml** — metadata and props schema:
   - Reference: [component.yml template](./references/component-yml-template.md)
   - Include `$schema`, `name`, `status`, `description`.
   - Define `props` using JSON Schema with `type`, `title`, `description`, and `default` where appropriate.
   - Add `slots` for projected content.
   - Add `libraryOverrides` only if CSS/JS files exist.

5. **Create template** — `<name>.twig` (NOT `.html.twig`):
   - Reference: [Twig template patterns](./references/twig-template-patterns.md)
   - Use semantic HTML elements.
   - Set defaults for optional props with `|default()`.
   - Use `|t` for translatable strings.
   - Never use `|raw`.

6. **Create CSS** (optional) — `<name>.css`:
   - Only for styles that cannot be expressed with the project's utility classes.
   - Follow the project's existing CSS conventions.

7. **Create JS** (optional) — `<name>.js`:
   - Follow the project's existing JavaScript patterns.
   - Implement keyboard navigation where applicable.
   - Include cleanup/teardown logic.

8. **Accessibility check**: Review against this checklist:
   - [ ] Semantic HTML elements used (`<button>`, `<nav>`, `<article>`, etc.)
   - [ ] Interactive elements are keyboard accessible
   - [ ] Focus is clearly visible
   - [ ] Images have appropriate alt text
   - [ ] Color contrast meets WCAG AA (4.5:1 text, 3:1 UI components)
   - [ ] ARIA attributes added where native semantics are insufficient
   - [ ] Component works without JavaScript (progressive enhancement)

## Output

The skill produces 2–4 files in `components/<name>/`:

    <name>/
    ├── <name>.component.yml   # Always
    ├── <name>.twig            # Always
    ├── <name>.css             # If custom styles needed
    └── <name>.js              # If interactive behavior needed
