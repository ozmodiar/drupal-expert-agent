# Drupal Expert Agent

AI agent configuration for Drupal 11 projects — coding standards, security rules, caching patterns, and best practices.

## What's included

| File | Scope | Description |
|------|-------|-------------|
| `drupal-general.instructions.md` | `**` | Project guidelines, anti-patterns, and tooling |
| `drupal-php.instructions.md` | `**/*.php` | PHP coding standards, DI, caching, security |
| `drupal-testing.instructions.md` | `**/tests/**` | PHPUnit testing conventions |
| `drupal-twig.instructions.md` | `**/*.html.twig` | Twig template standards and XSS safety |
| `drupal-yaml.instructions.md` | `**/*.routing.yml`, `**/*.services.yml`, etc. | Routing, services, links, and config schema |

## Stack

- Drupal 11
- PHP 8.3+
- Drush 13
- DDEV

## Installation

Requires [Agent Package Manager (APM)](https://github.com/microsoft/apm).

```sh
apm install ozmodiar/drupal-expert-agent
```

The instruction files are scoped via `applyTo` patterns and activate automatically for matching files in your project.

## License

MIT
