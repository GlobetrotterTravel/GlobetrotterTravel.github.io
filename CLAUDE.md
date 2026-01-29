# Claude Code Project Instructions

This is the Globetrotter Iris documentation site, built with Jekyll and the Just the Docs theme.

## Skills

- **[Iris Docs Front Matter](.claude/skills/iris-docs-frontmatter.md)**: Patterns for adding front matter and formatting to documentation files in the `/iris/` directory. Use this when creating or aligning new documentation files.

## Project Structure

- `/iris/` - Main documentation for Globetrotter Iris
  - Parent pages: `home.md`, `facets.md`, `sets.md`, `fields.md`
  - Child pages in subfolders: `home/`, `facets/`, `sets/`, `fields/`

## Key Conventions

- All pages use Jekyll front matter with Just the Docs theme attributes
- Parent pages have `has_children: true` and include "In this section" navigation
- Child pages use `grand_parent: Globetrotter Iris`
- Headings use `{: .no_toc }` to exclude from auto-generated TOC
