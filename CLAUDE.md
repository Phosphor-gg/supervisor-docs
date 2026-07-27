# Supervisor documentation

Markdown sources for supervisor.gg/docs. The website clones this repo at build
time, traverses `docs/`, renders every page, and generates the sidebar.

## Writing rules

**No em dashes or en dashes. Ever.** Not `—`, not `–`. Use a comma, a colon,
parentheses, or two sentences. Check before committing:

```bash
grep -rn '—\|–' docs/
```

- British spelling, sentence case for headings.
- Second person, imperative for instructions ("Create a key", not "You can
  create a key").
- Short sentences. Docs are scanned, not read.

## Accuracy

Docs are the reference people integrate against, so a wrong value here becomes a
support ticket or a broken integration.

- **Every endpoint, parameter, field name, default, and limit must be checked
  against the source before writing.** The API is defined in
  `supervisor-moderation-api/moderation_api.py` (request and response models)
  and `supervisor-backend/src/routes/` (the public surface). Read it, do not
  recall it.
- **Response examples must match the real response shape**, including which
  fields are optional or omitted. `scores` and `class_probs` only appear when
  the server runs with `EXPOSE_SCORES=1`, so a plain example must not show them.
- **Model versions, label codes, and credit costs are checked against
  `metadata.json` in the models repo and the pricing constants**, not written
  from memory. The 16 label codes and their meanings come from the `/labels`
  endpoint.
- If a documented value is one you could not verify, leave it out rather than
  guess. A missing detail is recoverable; a wrong one is not.
- When behaviour changes in code, the docs change in the same session. Stale
  docs are worse than absent ones.

## Format

- `docs/NN-name.md` becomes `/docs/name`, ordered by `NN`.
- `docs/NN-dir/NN-name.md` becomes `/docs/dir/name` inside a sidebar category.
  The label comes from `_category.json` (`{"title": "..."}`) or Title Case of
  the directory name.
- Frontmatter, quote any value containing a colon:

```yaml
---
title: "Page Title"
description: "One-line description used for SEO meta tags."
---
```

- `slug: index` maps a page to `/docs` itself.
- Fenced code blocks always carry a language tag (`bash`, `json`, `python`,
  `javascript`, `rust`).
- Show a full request and its full response for every endpoint.
- Internal links are root relative: `/docs/integrations/discord`,
  `/glossary/false-positive`.

## Do not

- Document an endpoint that is not deployed.
- Use placeholder values that look real (`sk_prod_abc123` is fine,
  a plausible-looking real key is not).
- Copy request or response examples between pages without re-checking them
  against the current source.
