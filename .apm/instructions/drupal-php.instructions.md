---
description: "Drupal PHP coding standards, patterns, and security rules. Use when writing or reviewing PHP code in a Drupal project."
applyTo: "**/*.php"
---

# Drupal PHP Standards

## Coding Standards

- Follow Drupal core coding standards: https://www.drupal.org/docs/develop/standards
- Stay compatible with the PHP version defined in `composer.json`.
- Use strict typing:

    declare(strict_types=1);

- Use parameter and return type hints wherever possible.
- Use `?Type` for nullable types.

---

### Overrides

> `#[\Override]` requires PHP 8.3+. Omit it if the project runs on an earlier PHP version.

✅ Correct (PHP 8.3+):

    /**
     * {@inheritdoc}
     */
    #[\Override]
    public function build(): array {
      return [];
    }

✅ Correct (PHP < 8.3):

    /**
     * {@inheritdoc}
     */
    public function build(): array {
      return [];
    }

❌ Wrong:

    public function build() {
      return [];
    }

> `#[\Override]` provides compile-time safety that the method actually overrides a parent.
> `{@inheritdoc}` is separate — it inherits the parent's documentation. Use both when appropriate.

---

### Dependency Injection

✅ Correct (service class):

    final class ExampleService {

      public function __construct(
        private readonly LoggerInterface $logger,
      ) {}

    }

✅ Preferred (controller/plugin with AutowireTrait):

    use Drupal\Core\DependencyInjection\AutowireTrait;

    final class ExampleController extends ControllerBase {

      use AutowireTrait;

      public function __construct(
        private readonly ExampleService $exampleService,
      ) {
        parent::__construct();
      }

    }

> AutowireTrait eliminates the need for a `create()` method.

✅ Fallback (when AutowireTrait is not sufficient):

    final class ExampleController extends ControllerBase {

      public function __construct(
        private readonly ExampleService $exampleService,
      ) {}

      public static function create(ContainerInterface $container): self {
        return new self(
          $container->get('example.service'),
        );
      }

    }

> Use `create()` only when you need to pass non-service arguments or when autowiring cannot resolve the dependency.

❌ Wrong:

    $service = \Drupal::service('example.service');

> Only allowed in procedural code (`.module` files, hooks) where DI is impossible.

---

### Plugin Definition (Attributes)

✅ Preferred:

    use Drupal\Component\Plugin\Attribute\Plugin;

    #[Plugin(
      id: 'example_plugin',
      label: new TranslatableMarkup('Example')
    )]
    final class ExamplePlugin extends PluginBase {}

❌ Avoid (unless required):

    /**
     * @Plugin(
     *   id = "example_plugin"
     * )
     */

---

### Hooks (Attributes vs Procedural)

✅ Preferred (Drupal 11+ only):

    // In src/Hook/MyModuleHooks.php
    namespace Drupal\mymodule\Hook;

    use Drupal\Core\Hook\Attribute\Hook;

    final class MyModuleHooks {

      #[Hook('cron')]
      public function cron(): void {
        // Logic.
      }

    }

> OOP hook classes live in `src/Hook/` of the module. Hook attributes are **not available in Drupal 10**.

✅ Required for Drupal 10, acceptable in Drupal 11:

    function mymodule_cron() {
      // Logic.
    }

---

### Class Design

✅ Correct:

    final class OrderProcessor {

      public function __construct(
        private readonly PaymentGatewayInterface $gateway,
      ) {}

    }

❌ Wrong:

    class OrderProcessor {

      protected $gateway;

    }

---

## Quality Gates

### SOLID / Single Responsibility

✅ Good:

    final class PriceCalculator {
      public function calculate(float $basePrice): float {
        return $basePrice * 1.21;
      }
    }

❌ Bad:

    class OrderController {
      public function submit() {
        // Calculates price
        // Sends email
        // Writes logs
        // Updates DB
      }
    }

---

### DRY

✅ Good:

    final class Vat {
      public const RATE = 0.21;
    }

❌ Bad:

    $total = $price * 1.21;
    $discount = $price * 1.21;

---

### Library-First Architecture

✅ Good:

    final class ExportService {
      public function export(array $data): string {}
    }

Then used in:
- Controller
- Drush command
- Queue worker

❌ Bad:
Putting logic directly in:
- Controller
- Hook
- Form submit

---

## Caching

Every render array must declare its cacheability. Missing cache metadata causes stale content or cache poisoning.

✅ Correct:

    return [
      '#markup' => $this->t('Welcome'),
      '#cache' => [
        'contexts' => ['user.permissions'],
        'tags' => ['node_list'],
        'max-age' => 3600,
      ],
    ];

❌ Wrong:

    return [
      '#markup' => $this->t('Welcome'),
    ];

> Without `#cache`, Drupal cannot invalidate or vary the output correctly.

Key concepts:
- **Cache contexts**: what the output varies by (e.g., `user`, `url.query_args`, `languages:language_interface`).
- **Cache tags**: what the output depends on (e.g., `node:42`, `config:system.site`).
- **Max-age**: how long the output is valid (`0` = uncacheable, `-1` = permanent).

Common cache tag patterns:
- `node:123` — specific node.
- `node_list` — any node list (invalidated when nodes are added/removed).
- `user:456` — specific user.
- `config:mymodule.settings` — specific configuration object.
- `taxonomy_term:789` — specific term.
- `taxonomy_term_list` — any term list.

When assembling `AccessResult` or other cacheable objects, always use `addCacheableDependency()` to merge dependencies:

    $build = [];
    $renderer = \Drupal::service('renderer');
    $renderer->addCacheableDependency($build, $entity);

---

## Translation

Every user-facing string must go through Drupal's translation API.

- Use `$this->t()` in classes that extend a Drupal base class or use `StringTranslationTrait`.
- Use `new TranslatableMarkup()` in plugin attributes.
- Use `t()` only in procedural code (`.module` files).

Placeholder types:
- `@variable` — sanitized text.
- `%variable` — sanitized and wrapped in `<em>`.
- `:variable` — URL (sanitized).

✅ Correct:

    $this->t('Hello @name', ['@name' => $name]);
    $this->t('Visit :url for details.', [':url' => $url]);

❌ Wrong:

    return ['#markup' => 'Welcome back'];
    return ['#markup' => $this->t("Hello $name")];

> Add `use StringTranslationTrait;` to service classes that need `$this->t()` without extending a base class.

---

## Security Rules

### Input Handling

✅ Correct:

    $value = $request->get('id');
    $id = (int) $value;

---

### Database Queries

Always use the database abstraction layer. Never concatenate user input into SQL.

✅ Correct:

    $query = $this->database->select('node_field_data', 'n');
    $query->fields('n', ['nid', 'title']);
    $query->condition('n.type', $type);
    $results = $query->execute();

❌ Never:

    $this->database->query("SELECT * FROM node WHERE type = '$type'");

---

### Access Checks

✅ Required:

    return AccessResult::allowedIfHasPermission($account, 'access content');

❌ Never:

    return AccessResult::allowed();

---

### Rendering / XSS

✅ Correct:

    return [
      '#markup' => $this->t('Hello @name', ['@name' => $name]),
    ];

❌ Dangerous:

    return [
      '#markup' => "Hello $name",
    ];

---

### Entity Access

✅ Always:

    if (!$entity->access('view')) {
      throw new AccessDeniedHttpException();
    }

---

## Deprecated APIs

Do not use these. Use the modern replacements.

| Deprecated | Replacement |
|---|---|
| `drupal_set_message()` | `\Drupal::messenger()->addMessage()` |
| `entity_load()` | `\Drupal::entityTypeManager()->getStorage()->load()` |
| `db_select()` / `db_query()` | Inject `database` service |
| `format_date()` | Inject `date.formatter` service |
| `drupal_render()` | Inject `renderer` service |
| `\Drupal::l()` | `Link::fromTextAndUrl()` |
| `\Drupal::url()` | `Url::fromRoute()->toString()` |

Run `./vendor/bin/drupal-check modules/custom/` or `phpstan` to detect deprecations automatically.
