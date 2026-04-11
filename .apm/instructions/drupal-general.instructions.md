---
description: "Drupal 10.3+ project guidelines, anti-patterns, and best practices. Always loaded for any Drupal project."
applyTo: "**"
---

# Drupal Project Guidelines

## Technology Stack

- Drupal 10.3+ / 11
- PHP 8.1+ (PHP 8.3+ recommended)
- Drush 12 / 13
- DDEV

## Build & Tools

- Use `ddev composer` for all Composer operations.
- Never edit `composer.lock` manually.
- Use `ddev drush generate` to scaffold modules, controllers, forms, plugins, and services instead of creating files by hand.
- Use `ddev drush field:create` for adding fields to content types.
- Run `ddev drush cex -y` after making configuration changes via CLI.

## Pre-Commit Checks

Run these locally before committing:

    ddev exec ./vendor/bin/phpcs -p --colors modules/custom/
    ddev exec ./vendor/bin/phpstan analyze modules/custom/
    ddev exec ./vendor/bin/drupal-check modules/custom/

Use `phpcbf` to auto-fix coding standard violations:

    ddev exec ./vendor/bin/phpcbf modules/custom/

## Best Practices

- Before implementing functionality, check if a contrib module or Drupal Recipe (10.3+) already solves it.
- Prefer existing services over creating new ones.
- Prefer existing patterns in the codebase over introducing new ones.
- Do not introduce new patterns unless they already exist in the codebase.
- Do not refactor unrelated code.
- Do not optimize prematurely.

## Anti-Patterns (Hard Rules)

Avoid these unless explicitly required:

- Using `\Drupal::service()` in classes
- Writing business logic in controllers or hooks
- Skipping access checks
- Returning raw user input
- Creating non-final classes without reason
- Duplicating logic instead of extracting services

## When in Doubt

- Do not guess — ask for clarification.
- Inspect the codebase before implementing anything.
- Prefer consistency over "cleaner" abstractions.

## Documentation

- DDEV: https://docs.ddev.com/
- Composer: https://getcomposer.org/doc/
- Drupal user guide: https://www.drupal.org/docs/user_guide/en/index.html
- Drupal developer docs: https://www.drupal.org/docs/develop

## Module Directory Structure

Canonical layout for a custom module:

    my_module/
    ├── my_module.info.yml
    ├── my_module.module             # Procedural hooks only (keep thin)
    ├── my_module.services.yml
    ├── my_module.routing.yml
    ├── my_module.permissions.yml
    ├── my_module.libraries.yml
    ├── config/
    │   ├── install/                 # Default config
    │   ├── optional/                # Optional config (extra dependencies)
    │   └── schema/                  # Config schema (required for custom config)
    ├── src/
    │   ├── Controller/
    │   ├── Form/
    │   ├── Plugin/
    │   │   ├── Block/
    │   │   └── Field/
    │   ├── Service/
    │   ├── EventSubscriber/
    │   └── Hook/                    # OOP hooks (Drupal 11+)
    ├── templates/                   # Twig templates
    └── tests/
        └── src/
            ├── Unit/
            ├── Kernel/
            └── Functional/

## Final Advice

AI agents don't fail because they lack knowledge.
They fail because they try to be *too clever*.

> **Match the existing codebase. Do not try to improve it unless explicitly asked.**
