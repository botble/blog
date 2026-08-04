---
title: "How to Build Claude Code Skills for Botble CMS Development"
description: "Learn how to create custom Claude Code skills that teach AI your Botble CMS coding conventions, plugin architecture, and development workflows — turning Claude Code into a Botble expert."
categories:
  - Tutorials
  - Development
tags:
  - claude-code
  - ai
  - development
  - botble
  - productivity
  - tools
image: news/claude-code-og-image.jpg
status: published
is_featured: true
---

# How to Build Claude Code Skills for Botble CMS Development

Claude Code is a powerful AI coding assistant, but out of the box it knows nothing specific about Botble CMS — our plugin architecture, form builders, table builders, hook system, or Envato marketplace requirements. It will generate generic Laravel code that doesn't follow Botble conventions.

The solution? **Claude Code Skills** — custom instruction files that teach Claude your project's specific patterns and rules. In this guide, we'll show you how to build skills that transform Claude Code into a Botble CMS expert for your development workflow.

## Prerequisites: The Botble AI Assistant Guide

Before building skills, you should know that Botble CMS ships with a comprehensive [AI Assistant Guide](https://docs.botble.com/cms/ai-assistant-guide.html). This guide is designed for any AI system (Claude, GPT, Copilot) and covers:

- **Project architecture**: Core modules, packages, plugins, and themes structure
- **Tech stack**: Laravel 13+, PHP 8.3+, Vue.js 3, Bootstrap 5, jQuery, Tabler UI
- **Naming conventions**: kebab-case files, PascalCase classes, camelCase methods, snake_case variables
- **Botble's custom Enum class**: Uses `Botble\Base\Supports\Enum` (not PHP 8.1 native enums) — the `$value` property is **protected** and direct `===` comparison always returns `false` (the most common silent bug)
- **Form builder**: Modern FieldOptions syntax with 25+ typed field classes including `TextField`, `EditorField`, `MediaImageField`, `SelectField`, `OnOffField`, `RepeaterField`, and more
- **Table builder**: Typed column classes (`IdColumn`, `NameColumn`, `FormattedColumn`, `StatusColumn`) with customization methods
- **Hook system**: `add_action()`, `add_filter()`, `do_action()`, `apply_filters()` with common hooks reference
- **Security rules**: `BaseHelper::clean()` for XSS, CSRF in AJAX, `@json()` for JS contexts, cookie allowlist validation
- **Translation system**: `trans()` for plugins (never `__()`), `__()` for themes only, 42+ supported languages
- **RvMedia**: Always use `RvMedia::getImageUrl()` for images — never raw paths
- **Ecommerce patterns**: Product hierarchy, order lifecycle, payment gateway integration (6-step pattern), 80+ hooks
- **API development**: Sanctum auth, custom headers (`X-CURRENCY`, `X-LANGUAGE`), guest cart with optional auth
- **Code review severity guide**: Critical/High/Medium/Low issue classification
- **20+ common pitfalls**: From enum access to badge classes to Google Fonts handling

This guide is your **foundation**. Copy the relevant sections into your skills to give Claude Code deep Botble knowledge from day one.

## What Are Claude Code Skills?

Skills are markdown files with structured instructions that Claude Code loads when activated. They act as "expert modules" — giving the AI deep knowledge about specific tools, patterns, and workflows.

When you type a slash command like `/botble-plugin`, Claude Code reads the skill file and suddenly understands how to scaffold a Botble plugin with the correct directory structure, service providers, repositories, forms, tables, and routes.

Official documentation: [Claude Code Skills](https://code.claude.com/docs/en/skills)

Community skills repository: [github.com/anthropics/skills](https://github.com/anthropics/skills)

## Skill File Structure

Every skill lives in your `~/.claude/skills/` directory (global) or `.claude/skills/` (per-project). Each skill has a `SKILL.md` file with YAML frontmatter:

```markdown
---
name: botble-plugin
description: Scaffold and develop Botble CMS plugins
trigger: when working with Botble plugin architecture
---

# Botble Plugin Development

## Plugin Directory Structure

Every Botble plugin follows this structure:

platform/plugins/your-plugin/
├── config/
│   ├── general.php
│   └── permissions.php
├── database/migrations/
├── resources/
│   ├── lang/en/{plugin}.php
│   ├── views/
│   ├── js/
│   └── sass/
├── routes/
│   ├── web.php
│   └── api.php (optional)
├── src/
│   ├── Enums/
│   ├── Forms/
│   ├── Http/
│   │   ├── Controllers/
│   │   └── Requests/
│   ├── Models/
│   ├── Providers/
│   ├── Repositories/
│   ├── Services/
│   ├── Tables/
│   └── Plugin.php
└── plugin.json

## Rules

- Always extend `Botble\Base\Models\BaseModel`, never plain `Model`
- Always use `foreignId()` for foreign key columns in migrations
- ID parameters must be typed as `int|string` for UUID support
- Register permissions in `config/permissions.php`
- `casts()` is a method (not property) in Laravel 12+
- `removed()` must drop ALL tables and clean up ALL settings
```

## Skills We Recommend Building for Botble

Here are the categories of skills that will dramatically improve your Botble development with Claude Code. For each one, we highlight what to include and show concrete examples based on the patterns from the [AI Assistant Guide](https://docs.botble.com/cms/ai-assistant-guide.html).

### 1. Plugin Development Skill

This is the most impactful skill to build. Document everything Claude needs to know about creating Botble plugins.

**What to include:**
- Plugin directory structure and `plugin.json` configuration
- How to create models extending `BaseModel`
- Form builder patterns using modern FieldOptions syntax
- Table builder patterns with typed column classes
- Service provider registration with `DashboardMenu`, routes, views, translations
- Repository interface and implementation patterns
- Route registration with `AdminHelper::registerRoutes()` and route model binding
- Permission configuration
- Hook registration with `add_action()` and `add_filter()`
- Plugin lifecycle: `activate()`, `deactivate()`, `remove()`
- Controller response helpers: `withCreatedSuccessMessage()`, `withUpdatedSuccessMessage()`
- Artisan scaffolding: `cms:make:model`, `cms:make:form`, `cms:make:table`, etc.
- Key registrations: `SlugHelper`, `SeoHelper`, `LanguageAdvancedManager`

**Example — Form Builder (modern FieldOptions syntax):**

```php
use Botble\Base\Forms\FormAbstract;
use Botble\Base\Forms\Fields\TextField;
use Botble\Base\Forms\Fields\SelectField;
use Botble\Base\Forms\Fields\EditorField;
use Botble\Base\Forms\Fields\MediaImageField;
use Botble\Base\Forms\FieldOptions\NameFieldOption;
use Botble\Base\Forms\FieldOptions\StatusFieldOption;

class YourForm extends FormAbstract
{
    public function setup(): void
    {
        $this
            ->setupModel(new YourModel())
            ->setValidatorClass(YourRequest::class)
            ->add('name', TextField::class, NameFieldOption::make()->required())
            ->add('description', EditorField::class,
                EditorFieldOption::make()
                    ->label(trans('core/base::forms.description'))
            )
            ->add('image', MediaImageField::class,
                MediaImageFieldOption::make()
                    ->label(trans('core/base::forms.image'))
            )
            ->add('status', SelectField::class, StatusFieldOption::make());
    }
}
```

**Example — Multi-Column Forms:**

```php
$this
    ->columns(12)
    ->add('name', TextField::class,
        NameFieldOption::make()->colspan(8)->required()->toArray()
    )
    ->add('status', SelectField::class,
        StatusFieldOption::make()->colspan(4)->toArray()
    );
```

**Example — Table Builder with typed columns:**

```php
use Botble\Table\Columns\IdColumn;
use Botble\Table\Columns\NameColumn;
use Botble\Table\Columns\StatusColumn;
use Botble\Table\Columns\CreatedAtColumn;
use Botble\Table\Columns\FormattedColumn;

$this->model(YourModel::class)
    ->addColumns([
        IdColumn::make(),
        NameColumn::make()->route('your-plugin.edit'),
        FormattedColumn::make('price')
            ->formatted(fn ($value) => format_price($value)),
        StatusColumn::make(),
        CreatedAtColumn::make(),
    ]);
```

**Example — Service Provider:**

```php
class MyPluginServiceProvider extends ServiceProvider
{
    public function boot(): void
    {
        $this
            ->setNamespace('plugins/my-plugin')
            ->loadAndPublishConfigurations(['permissions'])
            ->loadAndPublishViews()
            ->loadAndPublishTranslations()
            ->loadRoutes()
            ->loadMigrations()
            ->publishAssets();

        DashboardMenu::default()->beforeRetrieving(function (): void {
            DashboardMenu::make()
                ->registerItem([
                    'id' => 'cms-plugins-my-plugin',
                    'priority' => 5,
                    'name' => 'My Plugin',
                    'icon' => 'ti ti-box',
                    'url' => route('my-plugin.index'),
                    'permissions' => ['my-plugin.index'],
                ]);
        });
    }
}
```

### 2. Theme Development Skill

Document Botble's theme system so Claude can build shortcodes, widgets, and layouts correctly.

**What to include:**
- Theme directory structure (assets, config.php, functions, lang, layouts, partials, views, widgets)
- `theme.json` configuration
- `config.php` events — `beforeRenderTheme` for asset registration
- Shortcode registration, frontend views, and admin config forms
- Shortcode admin field types: `text`, `textarea`, `image`, `select`, `onOff`, `number`, `color`, `tabs`
- Widget creation patterns (frontend + backend templates)
- Theme facade: `Theme::scope()`, `Theme::partial()`, `theme_option()`
- Built-in getters: `Theme::getSiteTitle()`, `Theme::getLogo()`, `Theme::getSiteCopyright()`
- **RvMedia**: Always use `RvMedia::getImageUrl()` for images, never raw paths
- Theme support helpers: `ThemeSupport::registerSocialLinks()`, `registerPreloader()`, etc.
- Child theme creation with `'inherit' => 'parent-theme'`
- Frontend routes with `Theme::registerRoutes()`
- View size limit: max ~150 lines per Blade file — split into partials
- No DB queries in header/footer — use View Composers
- Slider unique IDs for multiple shortcode instances

**TailwindCSS v4 themes** (if applicable):
- All config in CSS (no `tailwind.config.js`)
- `@source`, `@custom-variant dark`, `@theme` directives
- OKLCH color system
- Dark mode via `.dark` class on `<html>`

### 3. Botble Conventions Skill (Auto-Activated)

This is the skill that should **always be active** when working in a Botble project. Pull key rules directly from the [AI Assistant Guide](https://docs.botble.com/cms/ai-assistant-guide.html):

```markdown
---
name: botble-conventions
description: Core Botble CMS coding conventions and patterns
trigger: auto when editing files in platform/ directory
---

## Critical: Botble Enum Usage

Botble uses a custom Enum class (`Botble\Base\Supports\Enum`),
NOT PHP 8.1 native enums. The `$value` property is protected.
Direct `===` comparison between enum instance and constant is
always false (object vs string) — most common silent bug.

### Correct:
- `$model->status->getValue()` — get the value
- `(string) $model->status` — cast to string
- `$model->status->label()` — get display label
- `BaseStatusEnum::PUBLISHED()` — create enum instance
- `$model->status->getValue() === BaseStatusEnum::PUBLISHED` — compare

### Wrong:
- `$model->status->value` — protected property, will throw error
- `$model->status === BaseStatusEnum::PUBLISHED` — always false

### NOT needed when:
- `$request->input('field')` — already a raw string
- `->where('status', SomeEnum::VALUE)` — Laravel handles bindings

## Models
- Always extend `BaseModel`, never plain `Model`
- `casts()` is a method in Laravel 12+

## Eloquent
- Always use `Model::query()` — never DB facade
- Always eager load: `->with(['relation'])` to prevent N+1
- ID parameters typed as `int|string` for UUID support
- Use `$model->getKey()` instead of `$model->id` when type matters

## Translations
- Plugins: `trans('plugins/blog::posts.create')` — NEVER `__()`
- Themes: `__('Home')` — JSON flat key-value only
- NEVER convert string translations to arrays
- Always escape apostrophes: `l\'exemple`

## Security
- `BaseHelper::clean($html)` for all unescaped output
- `{{ $var }}` auto-escapes (default)
- `@json($data)` for JS embedding
- CSRF header: `X-CSRF-TOKEN` meta tag
- Cookie: use `request()->cookie()` with allowlist validation

## Images
- Always use `RvMedia::getImageUrl($path)` — never raw paths
- With preset: `RvMedia::getImageUrl($path, 'thumb')`

## Frontend Rules
- No CDN assets — bundle locally via npm
- jQuery `.on()` only — no `.click()`, `.bind()`, `.hover()`
- No inline JS/CSS — no `onclick=`, `style=`
- No dead code — delete unused, never comment out
- Google Fonts: use `BaseHelper::googleFonts()` proxy

## Formatting
- Run `./vendor/bin/pint` before every commit
```

### 4. Code Quality & Review Skill

Build a skill based on the severity guide from the AI Assistant Guide. This ensures Claude writes code that passes both internal review and Envato marketplace review:

```markdown
## Critical Issues (Must Fix)
- Enum comparisons without `getValue()`
- XSS: `{!! !!}` without `BaseHelper::clean()`
- CDN assets (googleapis, jsdelivr, unpkg, cdnjs)
- SQL injection, CSRF missing

## High Issues
- Models not extending `BaseModel`
- Inline JS/CSS
- Dead code
- DRY violations
- View files >150 lines, controllers >200 lines

## Medium Issues
- ID params not `int|string`
- Hardcoded strings (not translatable)
- `bigInteger` instead of `foreignId` in migrations
- Cookie access without allowlist
- N+1 queries

## Low Issues
- Missing `query()` builder
- `setInterval` without cleanup
- Noisy comments

## Envato Marketplace Rules
- No CDN assets — all libraries bundled locally
- No hardcoded license checks
- All strings translatable via `trans()` helper

## Botble-Specific Patterns
- Badge classes: always pair `bg-{color}` with `text-{color}-fg`
- Use `FormattedColumn::make()` for `getValueUsing()` / `renderUsing()`
- Product eager loading: always include `images` field with `image`
```

### 5. Database Seeder Skill

Teach Claude to generate realistic seeders following the architecture from the AI Assistant Guide:

```markdown
## Core Rules
- NEVER use `fake()` / Faker for user-visible content
- Use real product names, prices, descriptions
- Support multi-language via JSON translation files
- Real images from `database/seeders/files/`, not placeholder URLs
- Use `Arr::random()` for variety

## When fake() IS Acceptable
- Passwords: `bcrypt('12345678')`
- Random selection: `Arr::random($realProducts)`
- Random counts: `rand(3, 8)`
- Random booleans: `rand(0, 1)` for `is_featured`

## Architecture
database/seeders/
├── DatabaseSeeder.php
├── TranslationSeeder.php
├── contents/           # HTML content files
├── files/              # Seed images/media
├── translations/
│   └── {locale}/
│       ├── ec_products.json
│       └── {model}-content.html
└── Themes/
    └── Main/           # Per-variant seeders
```

### 6. REST API Skill

Document Botble's API patterns for mobile app integration:

**What to include:**
- API controller extending `BaseApiController`
- JSON Resource transformation
- Response format: `{"error": false, "data": {...}, "message": null}`
- Use `$this->httpResponse()->setData(...)->toApiResponse()`
- Sanctum authentication with `auth:sanctum` middleware
- Optional auth for guest features: `api.optional.auth` middleware
- Custom headers: `X-API-KEY`, `X-CURRENCY`, `X-LANGUAGE`, `X-API-IP`
- Rate limiting with `ThrottleRequests::using('name')`
- Guest cart pattern with `$customerId ?? Str::uuid()`
- Route file structure: `routes/api.php`

### 7. Translation Skill

Botble supports 42+ languages. A translation skill helps Claude handle i18n correctly:

**What to include:**
- PHP translation files for plugins: `resources/lang/{locale}/*.php`
- JSON translation files for themes: `lang/{locale}.json`
- **Critical**: Never use `__()` in plugins — it won't resolve namespaces
- **Critical**: Never convert string translations to arrays
- Apostrophe escaping: `L\'utilisateur n\'existe pas`
- Flat string keys only, not nested arrays
- Preserve `:placeholders` exactly
- Supported languages: `ar`, `bg`, `bn`, `cs`, `da`, `de`, `el`, `es`, `fa`, `fi`, `fr`, `he`, `hi`, `hu`, `id`, `it`, `ja`, `ko`, `lt`, `lv`, `ms`, `nb`, `nl`, `pl`, `pt`, `pt-BR`, `ro`, `ru`, `sk`, `sl`, `sr`, `sv`, `th`, `tr`, `uk`, `vi`, `zh`, `zh-TW`

### 8. Ecommerce Skill

If you work with Botble's ecommerce system, document these patterns:

**What to include:**
- Product hierarchy: `Product` → `ProductVariation` → `ProductVariationItem`
- Order lifecycle: `PENDING → PROCESSING → COMPLETED/CANCELED`
- Key enums: `OrderStatusEnum`, `PaymentStatusEnum`, `ShippingStatusEnum`, `ProductTypeEnum`, `StockStatusEnum`
- Payment gateway integration (6-step pattern): constants → hook filters → payment service → webhook → routes → settings form
- `EcommerceHelper` facade methods
- Ecommerce hooks (80+): `ACTION_AFTER_ORDER_STATUS_COMPLETED`, `PAYMENT_FILTER_AFTER_POST_CHECKOUT`, etc.
- Infinite scroll pattern with `bb-infinite-products-grid`

### 9. Testing Skill

Document your PHPUnit testing patterns:

```php
class YourPluginTest extends TestCase
{
    use RefreshDatabase;

    public function test_can_create_model(): void
    {
        $data = [
            'name' => 'Test Item',
            'status' => YourStatusEnum::PUBLISHED,
        ];

        $response = $this->loginAs()->post(
            route('your-plugin.create'),
            $data
        );

        $response->assertRedirect();
        $this->assertDatabaseHas('your_table', ['name' => 'Test Item']);
    }
}
```

**Rules to document:**
- Always use `RefreshDatabase` trait
- Test both success and failure scenarios
- Test permission-restricted routes
- Never mock the database — use real database calls
- Use `$this->loginAs()` for authenticated test requests
- Pre-commit verification: `php -l`, `./vendor/bin/pint`, `php artisan test`

## Building Your First Botble Skill: Step by Step

### Step 1: Start with the AI Assistant Guide

Read the [Botble AI Assistant Guide](https://docs.botble.com/cms/ai-assistant-guide.html) thoroughly. It contains the conventions and patterns that every skill should reference. Copy the quick reference section as a baseline:

```php
// Enum value access
$model->status->getValue()           // Get value
$model->status->label()              // Get label
(string) $model->status              // Cast to string
BaseStatusEnum::PUBLISHED            // Constant value
BaseStatusEnum::PUBLISHED()          // Enum instance

// Translations
trans('plugins/name::file.key')      // Admin (plugins/packages/core)
__('theme.key')                      // Frontend (themes only)

// Eloquent
Model::query()->where(...)->get()    // Always use query()
->with(['relation'])                 // Eager load

// Forms
NameFieldOption::make()->required()  // Field options
StatusFieldOption::make()            // Status select

// Tables
FormattedColumn::make('col')         // For custom render
    ->formatted(fn ($v) => ...)

// Security
BaseHelper::clean($html)             // Sanitize HTML
@json($data)                         // Safe JS embedding

// Images
RvMedia::getImageUrl($path)          // Always use for images
RvMedia::getImageUrl($path, 'thumb') // With size preset

// Routes
Route::get('{id}', ...)->wherePrimaryKey()  // Support int + UUID

// Response
$this->httpResponse()->withCreatedSuccessMessage()
```

### Step 2: Create the Skill Directory

```bash
mkdir -p ~/.claude/skills/botble-plugin
```

### Step 3: Write the SKILL.md File

Start with the most common patterns you repeat. Open `~/.claude/skills/botble-plugin/SKILL.md` and document:

1. **Directory structure** — what files go where
2. **Base classes** — which classes to extend (`BaseModel`, `FormAbstract`, `TableAbstract`)
3. **Common patterns** — code snippets for forms, tables, models, service providers
4. **Rules** — things Claude must always do or never do
5. **Examples** — real code from your existing plugins

### Step 4: Add Code References

Include reference files alongside SKILL.md for complex patterns:

```
~/.claude/skills/botble-plugin/
├── SKILL.md
├── references/
│   ├── example-model.php
│   ├── example-form.php
│   ├── example-table.php
│   └── example-provider.php
```

Reference these in your SKILL.md:

```markdown
## Model Example

See `references/example-model.php` for a complete model implementation.
```

Pro tip: Study the [FriendsOfBotble](https://github.com/FriendsOfBotble) repositories on GitHub for well-structured plugin examples to use as references. Examples include: fob-comment, fob-wishlist, fob-compare, fob-faq, and more.

### Step 5: Test and Iterate

Activate your skill in Claude Code and try common tasks:

```
/botble-plugin

Create a new plugin called "product-reviews" with:
- A Review model with rating, comment, product_id, customer_id
- Admin CRUD interface
- Status enum (pending, approved, rejected)
```

If Claude gets something wrong, add a rule to your skill to prevent it next time. Skills improve through iteration — every correction becomes a permanent rule.

## Common Pitfalls to Include in Your Skills

The [AI Assistant Guide](https://docs.botble.com/cms/ai-assistant-guide.html) documents 20+ common mistakes. Make sure your skills address these:

| Pitfall | What to Put in Your Skill |
|---------|--------------------------|
| Accessing `$enum->value` directly | "Use `$enum->getValue()` — Botble enums have protected `$value`" |
| `$model->status === Enum::VALUE` | "Always false — use `$model->status->getValue() === Enum::VALUE`" |
| Using DB facade for queries | "Always use `Model::query()` instead of `DB::table()`" |
| Hardcoding integer IDs | "Type ID parameters as `int\|string` for UUID support" |
| Using `__()` in plugins | "Use `trans('plugins/name::file.key')` — `__()` won't resolve namespaces" |
| Nested array translations | "Use flat string keys — arrays cause 'Array to string conversion'" |
| Unescaped apostrophes | "Escape with `\'` in single-quoted translation strings" |
| Missing eager loading | "Always add `->with(['relation'])` to prevent N+1 queries" |
| `Column::make()` with callbacks | "Use `FormattedColumn::make()` for custom rendering" |
| `->with('product:id,name,image')` | "Include `images` field too — image accessor depends on it" |
| `bg-green` badge without `-fg` | "Always add `text-green-fg` — text invisible without it" |
| CDN Google Fonts | "Use `BaseHelper::googleFonts()` proxy or `Theme::typography()`" |
| Raw `<img src="{{ $model->image }}">` | "Use `RvMedia::getImageUrl()` — always" |
| `removed()` without cleanup | "Must drop ALL tables and settings in `removed()`" |
| `$_COOKIE` direct access | "Use `request()->cookie()` with allowlist validation" |
| Skipping code formatting | "Run `./vendor/bin/pint` before every commit" |

## Tips for Effective Skills

### Keep Rules Specific and Actionable

Bad: "Write good code"

Good: "Always use `foreignId()` instead of `unsignedBigInteger()` for foreign key columns in migrations"

### Include Real Code Examples

Claude learns best from concrete examples. Copy patterns from your actual codebase rather than writing abstract descriptions.

### Document the "Why" Behind Rules

```markdown
## Rule: Use FormattedColumn for custom rendering

Always use `FormattedColumn::make()` (not `Column::make()`) when using
`getValueUsing()` or `renderUsing()`.

**Why:** Base `Column` class silently ignores these callbacks. Your custom
rendering will appear to work in development but produce empty columns.
```

### Create Auto-Activated Skills

For rules that should always apply when working in Botble code, use trigger conditions:

```markdown
---
name: botble-conventions
trigger: auto when editing files in platform/ directory
---
```

This way Claude automatically loads the skill without you typing a slash command.

### Chain Skills for Workflows

Design skills that work together in sequence:

```
Plan → Build → Seed → Test → Review
```

Each skill handles one phase. The output of one feeds into the next, creating a complete development pipeline.

## What This Looks Like in Practice

Once you've built skills for Botble development, here's what a typical workflow looks like:

1. **You say**: "Create a wishlist plugin for the ecommerce system"
2. **Claude Code** (with your plugin skill active):
   - Generates the correct directory structure under `platform/plugins/`
   - Creates model extending `BaseModel` with proper `casts()` method
   - Builds form using modern FieldOptions syntax with `setValidatorClass()`
   - Creates table with typed column classes and `FormattedColumn` for custom rendering
   - Registers routes with `AdminHelper::registerRoutes()` and `wherePrimaryKey()`
   - Sets up service provider with `DashboardMenu`, `SlugHelper`, `SeoHelper`
   - Writes migration with `foreignId()` columns
   - Adds translatable strings with correct `trans()` syntax
   - Uses `BaseHelper::clean()` for output, `RvMedia::getImageUrl()` for images
   - Implements `removed()` that drops all tables
   - Follows every Botble convention automatically

Without skills, you'd spend time correcting Claude's output — `$enum->value` instead of `$enum->getValue()`, `Model` instead of `BaseModel`, raw image paths instead of `RvMedia`, `__()` instead of `trans()` in plugins. With skills, it gets it right the first time.

## Going Further

### Explore Community Skills

The [Anthropic Skills Repository](https://github.com/anthropics/skills) contains community-contributed skills for various frameworks. Browse it for inspiration and patterns you can adapt for Botble.

### Combine with CLAUDE.md

While skills are activated on demand, `CLAUDE.md` files in your project root provide always-on instructions. Use CLAUDE.md for project-wide rules and skills for task-specific knowledge:

- **CLAUDE.md**: "This is a Botble CMS project. PHP 8.3+. Run `./vendor/bin/pint` before commits."
- **Skills**: Detailed plugin architecture, form builder patterns, field types, etc.

### Use Hooks for Automation

Claude Code supports [hooks](https://code.claude.com/docs/en/hooks) — shell commands that run automatically before/after certain actions. Combine with skills for powerful automation:

```json
{
  "hooks": {
    "postToolUse": [
      {
        "tool": "write",
        "command": "./vendor/bin/pint $FILE",
        "description": "Auto-format PHP files after writing"
      }
    ]
  }
}
```

### Use Artisan Commands

Botble provides CLI commands that your skills can reference:

```bash
# Scaffold
php artisan cms:plugin:create my-plugin
php artisan cms:theme:create my-theme
php artisan cms:make:model Name --no-interaction
php artisan cms:make:form Name --no-interaction
php artisan cms:make:table Name --no-interaction
php artisan cms:make:controller Name --no-interaction
php artisan cms:make:request Name --no-interaction
php artisan cms:make:route Name --no-interaction

# Management
php artisan cms:plugin:activate my-plugin
php artisan cms:plugin:list
php artisan cms:theme:activate my-theme
php artisan cms:publish:assets

# Quality
./vendor/bin/pint                    # Format PHP code
php artisan test                     # Run tests
./vendor/bin/phpstan analyse         # Static analysis
npm run dev|prod|watch               # Build assets
```

## Conclusion

Claude Code skills bridge the gap between a general-purpose AI assistant and a Botble CMS expert. By combining the official [AI Assistant Guide](https://docs.botble.com/cms/ai-assistant-guide.html) with custom skill files, you teach Claude to write code that fits your project from the start — no corrections needed.

Start with one skill (plugin development is the highest impact), test it with real tasks, and expand from there. The investment pays off quickly: every future plugin, theme, or API you build with Claude Code will automatically follow your standards.

**Resources:**
- [Botble AI Assistant Guide](https://docs.botble.com/cms/ai-assistant-guide.html) — the foundation for all Botble skills
- [Claude Code Skills Documentation](https://code.claude.com/docs/en/skills) — how to create and manage skills
- [Anthropic Skills Repository](https://github.com/anthropics/skills) — community skills for inspiration
- [Botble CMS Documentation](https://docs.botble.com) — complete platform documentation
- [FriendsOfBotble](https://github.com/FriendsOfBotble) — example plugins to use as references

---

*The patterns described here work for any Laravel-based CMS or framework — adapt them to your own project's architecture and conventions.*
