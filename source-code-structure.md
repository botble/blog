---
title: "Understanding Botble CMS Source Code Structure"
description: "A comprehensive guide to Botble CMS modular architecture and directory organization for developers"
categories:
  - Documentation
  - Development
tags:
  - botble-cms
  - laravel
  - architecture
  - source-code
image: news/get-started-with-laravel-6-400x277.png
status: published
is_featured: true
---

# Understanding Botble CMS Source Code Structure

Botble CMS distributes its full source code without encryption, utilizing a modular architecture pattern in Laravel that separates the application into distinct modules, each handling specific functionality.

## Main Directory Organization

The primary source code resides in the `/platform` directory, which contains all core modules, plugins, and themes. This directory is organized into several key sections:

- **core**: Houses the CMS's foundational modules (ACL, base, dashboard, media, settings, table)
- **packages**: Contains reusable packages deployable across projects (menu, page, SEO, theme, widget)
- **plugins**: Feature-specific plugins extending CMS functionality (blog, analytics, contact, gallery)
- **themes**: Frontend themes for the CMS

## Module Structure Example

Using the Blog plugin as an illustration, here's how a typical module is organized:

```
platform/plugins/blog/
├── config/                 # Configuration files
├── database/
│   └── migrations/         # Database schema changes
├── resources/
│   ├── lang/               # Translation files
│   ├── views/              # Blade templates
│   ├── js/                 # JavaScript files
│   └── sass/               # SCSS stylesheets
├── routes/
│   └── web.php             # Route definitions
├── src/
│   ├── Commands/           # Artisan commands
│   ├── Forms/              # Form builders
│   ├── Http/
│   │   ├── Controllers/    # Request handlers
│   │   └── Requests/       # Form validation
│   ├── Models/             # Eloquent models
│   ├── Providers/          # Service providers
│   ├── Repositories/       # Data access layer
│   ├── Services/           # Business logic
│   └── Tables/             # DataTable definitions
└── plugin.json             # Plugin metadata
```

Each module adheres to this consistent structure, facilitating easier code navigation and understanding.

## Core Modules

The `platform/core` directory contains essential modules:

| Module | Purpose |
|--------|---------|
| ACL | Access Control List - user roles and permissions |
| Base | Foundation classes, helpers, and base controllers |
| Dashboard | Admin panel dashboard widgets and layout |
| Media | File upload and media management |
| Settings | System configuration management |
| Table | DataTables integration for admin listings |

## Frontend Themes

Frontend themes are organized in the `platform/themes` directory. Each theme operates as an independent directory containing:

- **views/**: Blade templates for pages
- **assets/**: CSS, JavaScript, and images
- **functions/**: Theme-specific PHP functions
- **config.php**: Theme configuration
- **theme.json**: Theme metadata

## Architecture Benefits

The modular design provides several key advantages:

1. **Separation of Concerns**: Each module handles distinct features without overlap
2. **Reusability**: Modules function across different projects seamlessly
3. **Maintainability**: Changes to one module don't cascade to others
4. **Scalability**: New features integrate as separate modules without modifying existing code
5. **Testability**: Modules undergo isolated testing for better quality assurance

## Extension Capabilities

Developers can extend Botble CMS by creating new plugins and themes. The process is straightforward:

### Creating a Plugin

```bash
# Use the artisan command to scaffold a new plugin
php artisan cms:plugin:create my-plugin
```

### Activating a Plugin

```bash
php artisan cms:plugin:activate my-plugin
```

### Creating a Theme

Themes can be created by duplicating an existing theme directory and modifying the `theme.json` metadata file.

## Best Practices

When working with Botble CMS source code:

1. **Follow naming conventions**: Use kebab-case for files, PascalCase for classes
2. **Use the repository pattern**: Access data through repository interfaces
3. **Leverage hooks**: Use actions and filters for extensibility
4. **Keep modules independent**: Avoid tight coupling between plugins
5. **Run Pint**: Format code with `./vendor/bin/pint` before committing

## Conclusion

Understanding the source code structure is essential for effective development with Botble CMS. The modular architecture makes it easy to extend functionality while maintaining clean, organized code.

For more information, visit the [official documentation](https://docs.botble.com/cms).
