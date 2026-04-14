# Twig Template Patterns

Reference patterns for SDC Twig templates.

## Basic Component

```twig
{% set variant = variant|default('primary') %}

<div class="my-component my-component--{{ variant }}">
  {{ label }}
</div>
```

## Component with Slot

```twig
<article class="card">
  <h3>{{ title }}</h3>
  {% block content %}
    {{ content }}
  {% endblock %}
</article>
```

## Component with Conditional Content

```twig
{% block content %}
  {% if content is not empty %}
    {{ content }}
  {% else %}
    <p>{{ 'No content available.'|t }}</p>
  {% endif %}
{% endblock %}
```

## Key Patterns

- **Defaults**: always `|default()` optional props — `{% set variant = variant|default('primary') %}`.
- **Translation**: use `|t` for user-facing text — `{{ 'Close'|t }}`.
- **Attributes**: use `create_attribute()` for dynamic HTML attributes.
- **Output safety**: never use `|raw` — rely on Twig autoescape.
- **Accessibility**: add `aria-*` attributes, use semantic elements (`<button>`, `<nav>`, `<article>`).
