# Changelog

## [1.2.0] — 2026-04-11

### Added

**drupal-php.instructions.md**
- Translation guidance: `$this->t()`, `StringTranslationTrait`, placeholder types (`@`, `%`, `:`).
- Database query safety: parameterized queries via the abstraction layer with correct/incorrect examples.
- Cache tag conventions: standard naming patterns (`node:123`, `node_list`, `user:456`, `config:*`, `taxonomy_term_list`).
- Deprecated APIs table: `drupal_set_message()`, `entity_load()`, `db_select()`, etc. with modern replacements.

**drupal-general.instructions.md**
- Research-first principle: check for contrib modules or Drupal Recipes (10.3+) before writing custom code.
- Drush scaffolding workflow: prefer `ddev drush generate` and `ddev drush field:create` over hand-crafting files.
- Pre-commit checks: `phpcs`, `phpstan`, and `drupal-check` commands to run locally before committing.
- Module directory structure: canonical layout reference for custom modules.

## [1.1.0] — 2026-04-11

### Changed

- Broadened supported stack from Drupal 11 / PHP 8.3+ / Drush 13 to Drupal 10.3+ / PHP 8.1+ / Drush 12+.
- `#[\Override]` guidance now conditional on PHP version (8.3+ only).
- OOP hook attributes marked as Drupal 11+ only; procedural hooks documented as required for Drupal 10.
- Updated description and README to reflect Drupal 10.3+ support.

## [1.0.0] — 2026-04-10

### Added

- `drupal-general.instructions.md` — project guidelines, anti-patterns, and tooling.
- `drupal-php.instructions.md` — PHP coding standards, DI, caching, security.
- `drupal-testing.instructions.md` — PHPUnit testing conventions.
- `drupal-twig.instructions.md` — Twig template standards and XSS safety.
- `drupal-yaml.instructions.md` — routing, services, links, and config schema.
- APM package configuration (`apm.yml`).
