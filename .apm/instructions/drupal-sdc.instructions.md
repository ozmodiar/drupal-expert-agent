---
description: "Drupal Single Directory Components (SDC) conventions. Use when writing or reviewing SDC component files."
applyTo: "**/*.component.yml, **/components/**/*.twig"
---

# Drupal SDC Standards

## File Structure

Each component lives in its own kebab-case directory:

    components/my-component/
    ├── my-component.component.yml   # Required: metadata + props schema
    ├── my-component.twig            # Required: template
    ├── my-component.css             # Optional: component styles
    └── my-component.js              # Optional: component behavior

## Template Naming

- SDC templates: `name.twig` (no `.html.` prefix).
- Drupal override templates: `name.html.twig` (in `templates/` directory, not in `components/`).

## component.yml Schema

Every component requires a `component.yml` with at minimum:

    $schema: https://git.drupalcode.org/project/drupal/-/raw/HEAD/core/assets/schemas/v1/metadata.schema.json
    name: My Component
    status: stable
    description: Brief description of what the component does.

### Props

Define props using JSON Schema:

    props:
      type: object
      required:
        - label
      properties:
        label:
          type: string
          title: Label
          description: Visible text label for the component.
        variant:
          type: string
          title: Variant
          enum:
            - primary
            - secondary
          default: primary
        disabled:
          type: boolean
          title: Disabled
          default: false

### Slots

Use slots for content projection (rendering Drupal field output):

    slots:
      content:
        title: Content
        description: Slot for projected content from Drupal fields or blocks.

### Library Overrides

Declare CSS/JS files attached to the component:

    libraryOverrides:
      css:
        component:
          my-component.css: {}
      js:
        my-component.js: {}

Only add `libraryOverrides` when the CSS or JS files actually exist.

## Template Standards

### Defaults

Always provide defaults for optional props:

    {% set variant = variant|default('primary') %}

### Translation

Use `|t` for all user-facing strings:

    {{ 'Close'|t }}

Use `{% trans %}` for strings with variables:

    {% trans %}Hello {{ name }}{% endtrans %}

### Output Safety

Never use `|raw`. Rely on Twig autoescape.

### Slots with Fallback

    {% block content %}
      {% if content is not empty %}
        {{ content }}
      {% else %}
        <p>{{ 'No content available.'|t }}</p>
      {% endif %}
    {% endblock %}

## Accessibility

- Use semantic HTML elements (`<button>`, `<nav>`, `<article>`, `<section>`).
- Add ARIA attributes where native semantics are insufficient.
- Provide `alt` text for images; use `alt=""` for decorative images.
- Ensure interactive elements have visible labels and keyboard access.
- Maintain heading hierarchy (`<h1>`–`<h6>`); do not skip levels.
- Add `aria-expanded`, `aria-controls`, `aria-live` where appropriate.
- Components should work without JavaScript (progressive enhancement).

## Status Values

Use the `status` field to communicate component maturity:

- `stable` — production-ready, API is fixed.
- `experimental` — usable but API may change.
- `deprecated` — scheduled for removal, do not use in new code.
