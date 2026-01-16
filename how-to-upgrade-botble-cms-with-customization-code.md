---
title: "How to Upgrade Botble CMS with Customization Code"
description: "Complete guide to upgrading Botble CMS while preserving your custom code modifications"
categories:
  - Documentation
  - Tutorials
tags:
  - upgrade
  - customization
  - best-practices
image: news/get-started-with-laravel-6-400x277.png
status: published
is_featured: false
---

# How to Upgrade Botble CMS with Customization Code

Keeping your Botble CMS up-to-date is essential for security, performance, and new features. This guide covers upgrade methods and best practices for preserving your customizations.

## Upgrade Methods

### Automatic Update (Recommended)

The easiest way to upgrade:

1. Log into admin panel
2. Go to **Platform Administration** → **System Updater**
3. Click **Download & Install Update** if available
4. System handles everything automatically

### Manual Update

For advanced users who need more control:

**Step 1: Download & Extract**
- Get the latest version from CodeCanyon
- Extract the downloaded package

**Step 2: Upload Files**

Replace these directories and files:
- `app`, `database`, `config`, `platform`
- `public/themes`, `public/vendor`, `bootstrap`, `vendor`
- `composer.json`, `composer.lock`, `public/index.php`

**Step 3: Post-Upgrade Tasks**

1. **Clear Cache**: Go to **Platform Administration** → **Cache Management** → **Clear all CMS cache**
2. **Reactivate Plugins**: Navigate to **Plugins** → **Installed Plugins**, deactivate then reactivate all plugins
3. **Update Translations**: Visit **Settings** → **Localization** → **Other Translations** and click the refresh link

## Handling Customization Code

### Minimal Customizations

If you don't make too many changes in source code:

1. Copy your changes to a safe place (backup)
2. Upgrade your site using System Updater
3. Copy your changes back

### Using Git for Comparison

If you know how to use Git, use it to compare our changes with your changes to know which files need to be updated:

```bash
# Compare changes between versions
git diff v7.5.0..v7.6.0 -- platform/

# See what files changed
git diff --name-only v7.5.0..v7.6.0
```

This helps identify conflicts between your modifications and the new version.

## Best Practices for Customization

### Create a Plugin (Recommended)

The best practice to customize our features is making a plugin in `platform/plugins`. Do the same as we did for other plugins - it will help you keep your changes when using System Updater.

**Benefits:**
- Changes are isolated from core code
- Survives upgrades automatically
- Easy to manage and version control
- Can be shared across multiple sites

**Plugin Structure:**

```
platform/plugins/my-customization/
├── config/
├── database/migrations/
├── resources/
│   ├── lang/
│   └── views/
├── routes/
├── src/
│   ├── Http/Controllers/
│   ├── Models/
│   ├── Providers/
│   └── Plugin.php
└── plugin.json
```

### Theme Customization

If you want to customize our theme, you should rename it first. This prevents your customizations from being overwritten during updates.

**Steps:**
1. Rename the theme folder (e.g., `ripple` → `my-theme`)
2. Update theme configuration files
3. Activate the renamed theme

See the detailed guide: [Rename Theme in Botble CMS](https://botble.com/rename-theme-in-botble-cms)

## Summary

| Scenario | Recommended Approach |
|----------|---------------------|
| Minimal changes | Backup → Upgrade → Restore |
| Multiple file changes | Use Git to compare and merge |
| Feature customization | Create a plugin in `platform/plugins` |
| Theme customization | Rename theme first |

## Additional Resources

- [Official Upgrade Documentation](https://docs.botble.com/cms/upgrade.html)
- [Plugin Development Guide](https://docs.botble.com/cms)
- [Theme Renaming Guide](https://botble.com/rename-theme-in-botble-cms)

By following these best practices, you can safely upgrade your Botble CMS while preserving all your custom work.
