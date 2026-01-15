# Blog Content Guide

This directory contains markdown files for blog posts. Use the `cms:blog:create-post-from-markdown` command to publish them.

## Quick Start

```bash
php artisan cms:blog:create-post-from-markdown path/to/your-post.md
```

## Markdown File Format

Every post must have YAML front matter at the top:

```markdown
---
title: "Your Post Title"
description: "A brief description for SEO (optional)"
categories:
  - Category One
  - Category Two
tags:
  - tag1
  - tag2
image: news/your-image.png
status: published
is_featured: false
---

Your markdown content starts here...
```

## Front Matter Fields

| Field | Required | Default | Description |
|-------|----------|---------|-------------|
| `title` | Yes | - | Post title |
| `description` | No | null | SEO description |
| `categories` | No | [] | List of category names (auto-created if missing) |
| `tags` | No | [] | List of tag names (auto-created if missing) |
| `image` | No | null | Featured image path in storage |
| `status` | No | published | `published`, `draft`, or `pending` |
| `is_featured` | No | false | Feature the post on homepage |
| `author_id` | No | 1 | Author user ID |

## Example Post

```markdown
---
title: "Getting Started with Laravel"
description: "Learn the basics of Laravel framework"
categories:
  - Tutorials
  - PHP
tags:
  - laravel
  - php
  - beginner
image: news/laravel-tutorial.png
status: published
is_featured: true
---

# Getting Started with Laravel

Laravel is a powerful PHP framework...

## Installation

\`\`\`bash
composer create-project laravel/laravel my-app
\`\`\`

## Features

- Elegant syntax
- Powerful ORM
- Built-in authentication
```

## Publishing Commands

```bash
# From local file
php artisan cms:blog:create-post-from-markdown ../blog-content/my-post.md

# From URL
php artisan cms:blog:create-post-from-markdown https://raw.githubusercontent.com/user/repo/post.md
```

## Duplicate Handling

If a post with the same title exists, you'll be prompted to:
- **Update existing post** - Overwrites content
- **Create new post** - Creates duplicate
- **Cancel** - Abort operation

## Tips

1. **Images**: Upload images to `storage/app/public/` first, then reference the path
2. **Categories/Tags**: They're auto-created if they don't exist
3. **Code blocks**: Use triple backticks with language identifier
4. **Tables**: Standard markdown tables are supported
