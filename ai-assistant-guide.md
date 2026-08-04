---
title: "AI Assistant Guide for Botble CMS Development"
description: "Learn how to configure AI assistants like Claude, GPT, and Copilot for consistent, high-quality code generation in Botble CMS projects"
categories:
  - Documentation
  - Development
tags:
  - ai-assistant
  - claude
  - gpt
  - botble-cms
  - code-generation
image: news/get-started-with-laravel-6-400x277.png
status: published
is_featured: true
---

# AI Assistant Guide for Botble CMS Development

This guide provides instructions for configuring AI assistants (Claude, GPT, Copilot, and others) to work effectively with Botble CMS codebases, ensuring consistent and high-quality code generation.

## Why Use an AI Assistant Guide?

When working with AI code assistants, providing context about your project's architecture, conventions, and patterns helps generate code that:

- Follows your existing coding standards
- Uses the correct design patterns
- Integrates seamlessly with your codebase
- Avoids common pitfalls and anti-patterns

## Project Architecture Overview

Botble CMS uses a modular Laravel structure organized in the `/platform` directory:

| Directory | Purpose |
|-----------|---------|
| `core/` | Foundation modules (ACL, base, dashboard, media, settings, table) |
| `packages/` | Reusable packages (menu, page, SEO, theme, widget) |
| `plugins/` | Feature plugins (blog, ecommerce, contact, gallery) |
| `themes/` | Frontend presentation layer |

### Tech Stack

- **Backend**: Laravel 13+, PHP 8.3+
- **Frontend**: Vue.js 3, Bootstrap 5, jQuery
- **Build Tools**: Laravel Mix, npm workspaces
- **Database**: MySQL (SQLite for tests)
- **UI Framework**: Tabler UI

## Naming Conventions

Consistent naming is crucial for maintainability. Here are the conventions used in Botble CMS:

| Type | Convention | Example |
|------|-----------|---------|
| Files | kebab-case | `product-controller.php` |
| Classes/Enums | PascalCase | `ProductController` |
| Methods | camelCase | `getProductList()` |
| Variables | snake_case | `$product_name` |
| Constants | SCREAMING_SNAKE_CASE | `PUBLISHED` |

## Critical: Botble Enum Usage

One of the most important things AI assistants need to understand is that **Botble uses a custom Enum class, NOT PHP 8.1 native enums**.

### Common Mistake

```php
// WRONG - This will throw an error!
$model->status->value
// "Cannot access protected property"
```

### Correct Approaches

```php
// Using getValue() method
$status = $model->status->getValue();

// String casting
$status = (string) $model->status;

// Using equals() for comparisons
if ($model->status->equals(BaseStatusEnum::PUBLISHED())) {
    // ...
}

// Using static method for enum instance
$enum = BaseStatusEnum::PUBLISHED();
```

## Database Conventions

### Standard Model Fields

Most models include these common fields:

- `id` - Primary key (supports both integer and UUID)
- `name` or `title` - Display name
- `slug` - URL-friendly identifier
- `status` - Uses `BaseStatusEnum`
- `created_at`, `updated_at` - Timestamps
- `author_id`, `author_type` - Polymorphic author relationship

### ID Type Support

Botble CMS supports both integer IDs and UUIDs. Always use union types:

```php
// Correct - supports both ID types
public function show(int|string $id): Response

// Incorrect - only supports integers
public function show(int $id): Response
```

## Eloquent Best Practices

### Always Use query() Method

```php
// Correct
User::query()->where('status', 'published')->get();

// Avoid
User::where('status', 'published')->get();
```

### Prevent N+1 Queries

```php
// Correct - eager loading
$posts = Post::query()
    ->with(['categories', 'tags', 'author'])
    ->get();

// Avoid - causes N+1 queries
$posts = Post::all();
foreach ($posts as $post) {
    echo $post->author->name; // Extra query each iteration!
}
```

## Translation System

Botble CMS uses different translation functions depending on context:

### Admin Panel

```php
trans('plugins/blog::posts.create')
trans('core/base::forms.save')
```

### Frontend/Theme

```php
__('Read more')
__('theme.welcome_message')
```

### Translation File Location

```
platform/{core|packages|plugins}/*/resources/lang/{locale}/*.php
```

### Best Practices

- Never convert string translations to arrays
- Always escape apostrophes in single-quoted strings
- Use flat keys instead of nested arrays

## Form Builder

Botble CMS provides a powerful form builder with FieldOptions:

```php
$this
    ->add('name', TextField::class, NameFieldOption::make()->required())
    ->add('status', SelectField::class, StatusFieldOption::make())
    ->add('content', EditorField::class, ContentFieldOption::make()->allowedShortcodes())
    ->add('image', MediaImageField::class, MediaImageFieldOption::make()->label('Featured Image'));
```

### Available Field Types

- `TextField`, `NumberField`, `HiddenField`
- `SelectField`, `RadioField`, `CheckboxField`
- `EditorField`, `TextareaField`
- `MediaImageField`, `MediaImagesField`, `FileField`
- `DatePickerField`, `TimePickerField`, `ColorField`
- `RepeaterField`, `TagField`, `TreeCategoryField`

## Hooks System

Botble CMS uses WordPress-style hooks for extensibility.

### Actions

Execute code at specific points:

```php
// Register an action
add_action('BASE_ACTION_AFTER_CREATE_CONTENT', function ($screen, $request, $model) {
    // Do something after content is created
}, 20);

// Trigger an action
do_action('BASE_ACTION_AFTER_CREATE_CONTENT', SCREEN_NAME, $request, $model);
```

### Filters

Modify values before they're used:

```php
// Register a filter
add_filter('BASE_FILTER_BEFORE_RENDER_FORM', function ($form, $data) {
    // Modify form before rendering
    return $form;
}, 20);

// Apply a filter
$form = apply_filters('BASE_FILTER_BEFORE_RENDER_FORM', $form, $data);
```

## Security Best Practices

### XSS Prevention in Blade Templates

```php
// For HTML contexts - use BaseHelper::clean()
{!! BaseHelper::clean($userContent) !!}

// For JavaScript contexts - use @json directive
<script>
var data = @json($variable);
</script>

// Default auto-escaping (safe for most cases)
{{ $variable }}
```

### CSRF in AJAX Requests

```javascript
headers: {
    'X-CSRF-TOKEN': $('meta[name="csrf-token"]').attr('content')
}
```

## Code Quality Commands

Before committing code, always run these quality checks:

```bash
# Format PHP code
./vendor/bin/pint

# Static analysis
./vendor/bin/phpstan analyse --level=5

# Run tests
php artisan test
```

## Setting Up Your AI Assistant

To configure your AI assistant for Botble CMS development:

1. **Create a CLAUDE.md file** (or equivalent for your AI tool) in your project root
2. **Include architecture information** about your specific project
3. **Document custom conventions** your team follows
4. **List common patterns** used in your codebase
5. **Reference this guide** for standard Botble CMS conventions

## Conclusion

By providing proper context to AI assistants, you can significantly improve code generation quality and reduce the time spent on corrections. The key points to remember are:

- Use the custom Enum class correctly (not PHP 8.1 enums)
- Follow naming conventions consistently
- Use `query()` for Eloquent operations
- Apply proper security practices
- Run quality checks before committing

For more detailed information, visit the [official Botble CMS documentation](https://docs.botble.com/cms).
