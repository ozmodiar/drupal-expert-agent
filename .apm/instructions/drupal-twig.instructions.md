---
description: "Drupal Twig template conventions. Use when writing or reviewing Twig templates in a Drupal project."
applyTo: "**/*.html.twig"
---

# Drupal Twig Standards

## XSS Safety

- Never use `|raw` unless explicitly required and approved.
- All variables are auto-escaped by default. Do not bypass this.

## Template Structure

- Always print `{{ attributes }}` on the outermost HTML element:

    <div{{ attributes }}>
      {{ content }}
    </div>

- Use `{{ content }}` to render all fields. Do not access fields manually unless you need to reorder or conditionally render them:

    {# Good #}
    {{ content }}

    {# Acceptable — when reordering or conditionally rendering #}
    {{ content.field_image }}
    {{ content|without('field_image') }}

    {# Wrong — bypasses formatters and access checks #}
    {{ node.field_image.value }}

## Translation

- Use `{% trans %}` for translatable strings:

    {% trans %}Submit your application{% endtrans %}

- Use `|t` for simple strings in attributes or variables:

    {{ 'Read more'|t }}

- Never hardcode user-facing strings without translation.

## Libraries

- Attach libraries in the template, not in preprocess:

    {{ attach_library('mymodule/my-library') }}

## Drupal Twig Functions and Filters

- `{{ url('route_name') }}` — generate an absolute URL for a route.
- `{{ path('route_name') }}` — generate a relative path for a route.
- `{{ create_attribute() }}` — build HTML attributes safely.
- `{{ link(text, url) }}` — generate an anchor element.
- `|default('value')` — provide fallback for optional variables:

    {% set variant = variant|default('primary') %}

## Accessibility

- Use semantic HTML elements (`<nav>`, `<main>`, `<article>`, `<section>`, `<button>`).
- Maintain heading hierarchy (`<h1>`–`<h6>`); do not skip levels.
- Provide `alt` text for images; use `alt=""` for decorative images.
- Add `aria-expanded`, `aria-controls`, `aria-live` where appropriate.
- See `drupal-a11y.instructions.md` for full accessibility standards.

## Conditional Rendering

- Use `content.field_name` (not `node.field_name`) to check if a field has output:

    {% if content.field_subtitle|render|trim %}
      <h2>{{ content.field_subtitle }}</h2>
    {% endif %}
