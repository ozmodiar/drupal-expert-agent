# Drupal Expert Agent

AI agent configuration for Drupal 10.3+ projects — coding standards, security rules, caching patterns, and best practices.

## What's included

| File | Scope | Description |
|------|-------|-------------|
| `drupal-general.instructions.md` | `**` | Project guidelines, anti-patterns, and tooling |
| `drupal-php.instructions.md` | `**/*.php` | PHP coding standards, DI, caching, security |
| `drupal-testing.instructions.md` | `**/tests/**` | PHPUnit testing conventions |
| `drupal-twig.instructions.md` | `**/*.html.twig` | Twig template standards and XSS safety |
| `drupal-yaml.instructions.md` | `**/*.routing.yml`, `**/*.services.yml`, etc. | Routing, services, links, and config schema |
| `drupal-a11y.instructions.md` | `**/*.html.twig`, `**/components/**/*.twig`, `**/*.css` | WCAG 2.2 AA accessibility standards |
| `drupal-sdc.instructions.md` | `**/*.component.yml`, `**/components/**/*.twig` | Single Directory Components conventions |

### Skills

| Skill | Description |
|-------|-------------|
| `drupal-sdc` | Scaffold a new SDC component (component.yml, Twig template, optional CSS/JS) |

## Stack

- Drupal 10.3+ / 11
- PHP 8.1+ (PHP 8.3+ recommended)
- Drush 12 / 13
- DDEV

## Installation

Requires [Agent Package Manager (APM)](https://github.com/microsoft/apm).

```sh
apm install ozmodiar/drupal-expert-agent
```

The instruction files are scoped via `applyTo` patterns and activate automatically for matching files in your project.

## License

MIT
