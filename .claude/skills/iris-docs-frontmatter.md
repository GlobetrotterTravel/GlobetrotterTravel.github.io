# Iris Documentation Front Matter

This skill describes how to add front matter and formatting to new documentation files in the Iris docs (`/iris/` directory).

## Front Matter Patterns

### Parent Pages (e.g., `iris/fields.md`, `iris/sets.md`)

Parent pages sit directly in the `iris/` folder and have children in subfolders.

```yaml
---
layout: default
title: <Page Title>
parent: Globetrotter Iris
nav_order: <number>
has_children: true
has_toc: false
permalink: /iris/<slug>/
---
```

- `nav_order`: Use multiples of 10 (existing: home=10, facets=20, sets=30, fields=40)
- `permalink`: Must match the pattern `/iris/<slug>/`

### Child Pages (e.g., `iris/fields/create-a-field-definition.md`)

Child pages sit in subfolders and reference their parent.

```yaml
---
layout: default
title: <Page title in sentence case>
parent: <Parent Page Title>
grand_parent: Globetrotter Iris
nav_order: 10
permalink: /iris/<parent-slug>/<page-slug>/
---
```

- `parent`: Must exactly match the parent page's `title` value
- `grand_parent`: Always `Globetrotter Iris`
- `nav_order`: Start at 10, increment by 10 for ordering within the section

## Content Formatting

### After the Front Matter

Always add `{: .no_toc }` immediately after the main heading:

```markdown
# Page Title
{: .no_toc }
```

### Table of Contents (Optional)

If the page has multiple sections, add:

```markdown
## Table of contents
{: .no_toc }

1. TOC
{:toc }
```

### "In this section" Navigation

Parent pages should end with a navigation section linking to children:

```markdown
## In this section

- [Child page title](/iris/<parent-slug>/<child-slug>/): Brief description
```

## Checklist for New Files

1. Add front matter with correct `layout`, `title`, `parent`, `nav_order`, and `permalink`
2. Add `grand_parent: Globetrotter Iris` for child pages
3. Add `has_children: true` and `has_toc: false` for parent pages
4. Add `{: .no_toc }` after the main heading
5. Add "In this section" to parent pages linking to children
6. Use sentence case for titles (e.g., "Create a field definition" not "Create a Field Definition")
7. If the file is in the root of the Iris documentation folder, then add a reference in index.md (## In this guide)