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

✅ Correct:

    /**
     * {@inheritdoc}
     */
    #[\Override]
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

✅ Preferred:

    // In src/Hook/MyModuleHooks.php
    namespace Drupal\mymodule\Hook;

    use Drupal\Core\Hook\Attribute\Hook;

    final class MyModuleHooks {

      #[Hook('cron')]
      public function cron(): void {
        // Logic.
      }

    }

> OOP hook classes live in `src/Hook/` of the module.

❌ Only when necessary:

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

When assembling `AccessResult` or other cacheable objects, always use `addCacheableDependency()` to merge dependencies:

    $build = [];
    $renderer = \Drupal::service('renderer');
    $renderer->addCacheableDependency($build, $entity);

---

## Security Rules

### Input Handling

✅ Correct:

    $value = $request->get('id');
    $id = (int) $value;

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
