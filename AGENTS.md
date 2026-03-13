# Agent Guidelines for airstream.ing

This is a Jekyll-based academic website (al-folio theme). This document provides build commands, code style guidelines, and development workflow for coding agents.

## Quick Links

- **Tech Stack & Build Info:** [`.github/copilot-instructions.md`](.github/copilot-instructions.md)
- **Git Workflow:** [`.github/GIT_WORKFLOW.md`](.github/GIT_WORKFLOW.md)
- **Troubleshooting:** [TROUBLESHOOTING.md](TROUBLESHOOTING.md)

## Build & Development Commands

### Docker (Recommended)

```bash
# Initial setup and start dev server
docker compose pull && docker compose up

# Site runs at http://localhost:8080

# Rebuild after changing dependencies or Dockerfile
docker compose up --build

# Stop containers and free port 8080
docker compose down

# Force complete rebuild
docker compose up --build --force-recreate
```

### Local Development (Ruby/Jekyll - Legacy)

```bash
bundle install
pip install jupyter nbconvert
bundle exec jekyll serve --port 4000
# Visit http://localhost:4000
```

### Code Quality

```bash
# Install prettier (first time only)
npm install --save-dev prettier @shopify/prettier-plugin-liquid

# Format all files (MANDATORY before commit)
npx prettier . --write

# Verify formatting without modifying files
npx prettier . --check
```

### Production Build

```bash
# Set JEKYLL_ENV=production for CSS/JS minification
JEKYLL_ENV=production bundle exec jekyll build
```

## Pre-Commit Checklist

Before every commit, you **must** run these steps:

1. **Format Code:** `npx prettier . --write`
2. **Build Locally & Verify:** `docker compose up --build`
3. **Verify site at http://localhost:8080** - Check navigation, pages, images, dark mode

## Project Structure

| Directory | Purpose |
|-----------|---------|
| `_bibliography/` | BibTeX bibliography (papers.bib) |
| `_config.yml` | Primary site configuration |
| `_data/` | YAML data files (socials, coauthors, cv, citations) |
| `_includes/` | Reusable Liquid template components |
| `_layouts/` | Page layout templates |
| `_news/` | News/announcement entries |
| `_pages/` | Static pages (about, cv, publications, projects) |
| `_posts/` | Blog posts (format: YYYY-MM-DD-title.md) |
| `_projects/` | Project showcase entries |
| `_sass/` | SCSS stylesheets |
| `_scripts/` | JavaScript files |
| `_teachings/` | Course and teaching entries |
| `assets/img/` | Images, profile pictures |

## Code Style Guidelines

### General Principles

- **Use Prettier** - All code must be formatted with Prettier before commit. This is enforced by CI.
- **Keep it consistent** - Follow existing patterns in the codebase.
- **Test locally** - Always verify changes with `docker compose up` before committing.

### Liquid Templates

- 2-space indentation
- Use whitespace control: `{{- -}}` to strip surrounding whitespace
- Put whitespace between Liquid tags: `{% if condition %}` not `{%if condition%}`
- Quote string attributes: `<a href="{{ url }}">`
- Comment format: `{% comment %} comment text {% endcomment %}`

Example:
```liquid
{%- if page.title -%}
  <title>{{ page.title | escape }}</title>
{%- endif -%}
```

### YAML Configuration

- Quote strings with special characters: `title: "My: Cool Site"`
- 2-space indentation
- Use lowercase keys: `baseurl:` not `baseUrl:`
- Boolean values: lowercase `true`/`false`

### JavaScript

- ES6+ syntax, 2-space indentation, use semicolons
- Prefer `const` over `let`, avoid `var`
- Use template literals: `` `string ${variable}` ``

### CSS/SCSS

- Use SCSS syntax, 2-space indentation
- Follow BEM-like naming: `.block__element--modifier`
- Use CSS variables for colors and spacing

### Markdown Content

- Use frontmatter at the top:
```yaml
---
layout: post
title: Post Title
date: YYYY-MM-DD
categories: category-name
---
```
- Use ATX-style headers: `## Header`
- Use fenced code blocks: ``` ```javascript ```

## Common Pitfalls

| Problem | Solution |
|---------|----------|
| YAML special chars error | Quote strings: `title: "My: Cool Site"` |
| CSS/JS not loading | Check `url` + `baseurl` in `_config.yml` |
| Port 8080 in use | `docker compose down` then `docker compose up` |
| ImageMagick missing | `sudo apt-get install imagemagick` (Linux) |

## CI/CD Pipeline

GitHub Actions run on push/PR: **deploy.yml**, **prettier.yml** (mandatory), **broken-links.yml**, **axe.yml**, **codeql.yml**

## Important Notes

- **Trust these instructions** - They document the tested, working build process
- **Always use Docker** for local development to match CI/CD
- **Prettier is mandatory** - PRs will fail CI if not formatted
- **Set JEKYLL_ENV=production** for production builds
