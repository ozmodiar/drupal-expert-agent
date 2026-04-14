# component.yml Template

Reference template for creating SDC component metadata files.

```yaml
$schema: https://git.drupalcode.org/project/drupal/-/raw/HEAD/core/assets/schemas/v1/metadata.schema.json
name: Component Name
status: stable
description: Brief description of what the component does and how it is used.
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
      description: Visual style variant.
      enum:
        - primary
        - secondary
      default: primary
    disabled:
      type: boolean
      title: Disabled
      description: When true, the component is visually and functionally disabled.
      default: false
slots:
  content:
    title: Content
    description: Slot for projected content from Drupal fields or blocks.
libraryOverrides:
  css:
    component:
      component-name.css: {}
  js:
    component-name.js: {}
```

## Field Reference

| Field | Required | Description |
|---|---|---|
| `$schema` | Yes | Always use the Drupal metadata schema URL |
| `name` | Yes | Human-readable component name |
| `status` | Yes | `stable`, `experimental`, or `deprecated` |
| `description` | Yes | Purpose and usage notes |
| `props` | Yes | JSON Schema object with `properties` and `required` |
| `slots` | No | Named slots for content projection |
| `libraryOverrides` | No | CSS/JS files to attach (only if they exist) |

## Prop Types

- `string` — text values, URLs, HTML attributes.
- `boolean` — flags with `default: true/false`.
- `integer` / `number` — numeric values.
- `array` — lists with `items` definition.
- `object` — nested structures with `properties`.
- `enum` — restricted set of allowed values.
