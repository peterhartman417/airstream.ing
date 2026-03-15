# Agent Guidelines for airstream.ing

This is a Jekyll-based academic website (al-folio theme). This document provides build commands, code style guidelines, and development workflow for coding agents.

## Quick Links

- **Tech Stack & Build Info:** [`.github/copilot-instructions.md`](.github/copilot-instructions.md)
- **Git Workflow:** [`.github/GIT_WORKFLOW.md`](.github/GIT_WORKFLOW.md)
- **Troubleshooting:** [TROUBLESHOOTING.md](TROUBLESHOOTING.md)

## Build & Development Commands

### Docker (Recommended - Always Use This)

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

### Code Quality & Testing

```bash
# Install prettier (first time only)
npm install --save-dev prettier @shopify/prettier-plugin-liquid

# Format all files (MANDATORY before commit)
npx prettier . --write

# Verify formatting without modifying files
npx prettier . --check

# Build site for testing
bundle exec jekyll build

# Build site with production settings (CSS/JS minification)
JEKYLL_ENV=production bundle exec jekyll build

# Serve built site locally for testing
bundle exec jekyll serve
```

### Production Build

```bash
# Set JEKYLL_ENV=production for CSS/JS minification
JEKYLL_ENV=production bundle exec jekyll build
```

## Pre-Commit Checklist

Before every commit, you **must** run these steps:

1. **Format Code:** `npx prettier . --write`
2. **Build Locally & Verify:** `bundle exec jekyll build` (no errors)
3. **Verify site at http://localhost:4000** - Check navigation, pages, images, dark mode
4. **For Docker users:** `docker compose up --build` then verify at http://localhost:8080

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
- **Test locally** - Always verify changes with `bundle exec jekyll build` or Docker before committing.

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
- For URLs with special characters, always quote: `url: "https://example.com:8080/path"`

### JavaScript

- ES6+ syntax, 2-space indentation, use semicolons
- Prefer `const` over `let`, avoid `var`
- Use template literals: `` `string ${variable}` ``
- Import format: `import { function } from 'module';`
- Default exports: `export default function Component() {}`
- Named exports: `export function utility() {}`

### CSS/SCSS

- Use SCSS syntax, 2-space indentation
- Follow BEM-like naming: `.block__element--modifier`
- Use CSS variables for colors and spacing defined in `_variables.scss`
- Nesting limit: maximum 3 levels deep
- Use @extend sparingly, prefer mixins for reuse
- Color formatting: use lowercase hex (`#fff` not `#FFFFFF`) or HSL functions

### Markdown Content

- Use frontmatter at top:
```yaml
---
layout: post
title: Post Title
date: YYYY-MM-DD
categories: category-name
---
```
- Use ATX-style headers: `## Header`
- Use fenced code blocks: 
  ```javascript
  // code here
  ```
- Link format: `[text](URL)`
- Image format: `![alt text](image URL)`
- Emphasis: *italic* or **bold**
- Lists: use `-` for unordered, `1.` for ordered
- Blank line between paragraphs and around block elements

## Common Pitfalls

| Problem | Solution |
|---------|----------|
| YAML special chars error | Quote strings: `title: "My: Cool Site"` |
| CSS/JS not loading | Check `url` + `baseurl` in `_config.yml` |
| Port 8080 in use | `docker compose down` then `docker compose up` |
| ImageMagick missing | `sudo apt-get install imagemagick` (Linux) |
| Jekyll build fails silently | Run with `--verbose` flag for detailed output |
| Related posts errors | Add meaningful content to posts or set `related_posts: false` |
| Prettier formatting failures | Run `npx prettier . --write` before committing |
| SCSS compilation errors | Check for missing semicolons or mismatched brackets |

## CI/CD Pipeline

GitHub Actions run on push/PR:
- **deploy.yml** - Main deployment workflow
- **prettier.yml** (mandatory) - Code formatting validation
- **broken-links.yml** - Link validation
- **axe.yml** - Accessibility testing
- **codeql.yml** - Security scanning
- **update-citations.yml** - Automatic citation updates

## Important Notes

- **Trust these instructions** - They document the tested, working build process
- **Always use Docker** for local development to match CI/CD (recommended)
- **Prettier is mandatory** - PRs will fail CI if not formatted
- **Set JEKYLL_ENV=production** for production builds to enable CSS/JS minification
- **ImageMagick is required** for image processing - installed automatically in Docker
- **Clear browser cache** when testing CSS/JS changes
- **Check _config.yml** first for site-wide configuration changes

## Development Workflow

1. Create feature branch from main
2. Make changes following code style guidelines
3. Format all changes with Prettier: `npx prettier . --write`
4. Build and test locally: `bundle exec jekyll build` and `bundle exec jekyll serve`
5. Verify changes in browser at http://localhost:4000
6. Commit changes with descriptive message
7. Push branch and open pull request
8. Address any CI/CD failures before merging