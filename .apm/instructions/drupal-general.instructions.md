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

## Best Practices

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

## Final Advice

AI agents don't fail because they lack knowledge.
They fail because they try to be *too clever*.

> **Match the existing codebase. Do not try to improve it unless explicitly asked.**
