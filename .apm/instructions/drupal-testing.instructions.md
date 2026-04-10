---
description: "Drupal PHPUnit testing conventions. Use when writing, reviewing, or debugging tests."
applyTo: "**/tests/**"
---

# Drupal Testing Standards

- Use PHPUnit.
- Test base classes:
  - `UnitTestCase` — pure logic, no Drupal bootstrap.
  - `KernelTestBase` — Drupal integration (services, database, entity API).
  - `BrowserTestBase` — full browser simulation (forms, pages, access).
- Directory structure:
  - `tests/src/Unit/` for unit tests.
  - `tests/src/Kernel/` for kernel tests.
  - `tests/src/Functional/` for browser tests.
- Add `@group mymodule` annotation to every test class.
