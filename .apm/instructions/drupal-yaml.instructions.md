---
description: "Drupal YAML file conventions for routing, services, links, and config schema. Use when writing or reviewing Drupal YAML definition files."
applyTo: "**/*.routing.yml,**/*.services.yml,**/*.links.*.yml,**/*.schema.yml,**/*.info.yml"
---

# Drupal YAML Standards

## Routing (`*.routing.yml`)

    # Controller route
    mymodule.example:
      path: '/admin/example'
      defaults:
        _controller: '\Drupal\mymodule\Controller\ExampleController::build'
        _title: 'Example'
      requirements:
        _permission: 'access content'

    # Form route
    mymodule.settings:
      path: '/admin/config/mymodule'
      defaults:
        _form: '\Drupal\mymodule\Form\SettingsForm'
        _title: 'Settings'
      requirements:
        _permission: 'administer site configuration'

    # Entity form route
    mymodule.entity_edit:
      path: '/admin/example/{example}/edit'
      defaults:
        _entity_form: 'example.edit'
        _title: 'Edit example'
      requirements:
        _entity_access: 'example.update'

Rules:
- Always specify `requirements` — never leave a route without access control.
- Use `_permission`, `_role`, `_entity_access`, or `_custom_access`.
- Use full class paths with namespace (starting with `\Drupal\`).

## Services (`*.services.yml`)

    services:
      mymodule.example_service:
        class: Drupal\mymodule\ExampleService
        autowire: true

    # When autowire is not sufficient
      mymodule.other_service:
        class: Drupal\mymodule\OtherService
        arguments: ['@entity_type.manager', '@logger.factory']

    # Event subscriber
      mymodule.event_subscriber:
        class: Drupal\mymodule\EventSubscriber\ExampleSubscriber
        autowire: true
        tags:
          - { name: event_subscriber }

Rules:
- Prefer `autowire: true` over explicit `arguments`.
- Service IDs use the format `mymodule.service_name`.
- Use `@service_id` for service references in arguments.
- Use `'%parameter%'` for container parameters.

## Menu Links (`*.links.menu.yml`)

    mymodule.admin:
      title: 'My Module'
      route_name: mymodule.settings
      parent: system.admin_config
      weight: 0

## Local Tasks / Tabs (`*.links.task.yml`)

    mymodule.settings_tab:
      title: 'Settings'
      route_name: mymodule.settings
      base_route: mymodule.settings

## Local Actions (`*.links.action.yml`)

    mymodule.add:
      title: 'Add example'
      route_name: mymodule.add
      appears_on:
        - mymodule.list

## Config Schema (`*.schema.yml`)

Every config entity and settings form must have a matching schema:

    mymodule.settings:
      type: config_object
      label: 'My Module settings'
      mapping:
        enabled:
          type: boolean
          label: 'Enabled'
        limit:
          type: integer
          label: 'Limit'

Rules:
- Config schema is **required** — config without schema breaks translations and validation.
- Schema file lives in `config/schema/mymodule.schema.yml`.
- Schema key must match the config object name.
