# Supervisor documentation

Markdown sources for [supervisor.gg/docs](https://supervisor.gg/docs).

The website builds its docs from this repo: a pre-build step clones it,
traverses `docs/`, renders every page to HTML, and generates the sidebar,
so changes here go live on the next website build with no code edits.

## Conventions

- `docs/NN-name.md` becomes a page at `/docs/name`, ordered by `NN`.
- `docs/NN-dir/NN-name.md` becomes `/docs/dir/name` inside a sidebar
  category. The category label comes from `_category.json`
  (`{"title": "..."}`) in the directory, or Title Case of the name.
- Every page starts with frontmatter:

```yaml
---
title: Page Title
description: One-line description used for SEO meta tags.
---
```

- `slug: index` in frontmatter maps a page to `/docs` itself.
- Standard markdown plus tables and fenced code blocks with language tags.
