---
applyTo: "**/*.astro,src/pages/**,src/layouts/**,src/components/**"
---

# Astro Project Guidelines

Astro-specific conventions and project rules. Design principles are in design.instructions.md; XP workflow in xp.instructions.md; TypeScript conventions in typescript.instructions.md.

## Project Context

This is an Astro 5.x blog with TinaCMS and MDX. Posts live in `posts/` as `.mdx` files.

## Component Design

- Astro components (`.astro`) for static/server-rendered content
- Use `<script>` tags sparingly; prefer static rendering
- Client-side interactivity via `client:*` directives only when needed
- Keep components small and focused — one responsibility per component
- Favor composition: build complex pages from simple, reusable components

## Posts

- All posts must be `.mdx` (not `.md`)
- Required frontmatter: `title`, `slug`, `description`, `added` (ISO date or readable string)
- Optional: `tags` (array of strings)

## Development

- Dev server: `http://localhost:4321` via `make dev` or `pnpm dev`
- Tina env vars required: `TINACLIENTID`, `TINATOKEN`, `TINASEARCH`
- Check existing server before starting another — don't switch ports

## Build & Verify

- `make build` — Full production build
- `make lint` — ESLint
- `make typecheck` — TypeScript strict
- `make verify` — Run verification script

## Common Gotchas

- RSS feed requires all frontmatter fields; missing fields break the build
- Inline scripts use `<script is:inline>` to avoid esbuild bundling issues
- TinaCMS runs first in dev/build; missing env vars cause immediate failure
