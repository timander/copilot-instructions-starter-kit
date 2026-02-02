---
applyTo: "**/*.astro,src/pages/**,src/layouts/**,src/components/**"
---

# Astro Project Guidelines

## Project Context
This is an Astro 5.x blog with TinaCMS and MDX. Posts live in `posts/` as `.mdx` files.

## Component Style
- Astro components (`.astro`) for static/server-rendered content
- Use `<script>` tags sparingly; prefer static rendering
- Client-side interactivity via `client:*` directives only when needed

## Posts
- All posts must be `.mdx` (not `.md`)
- Required frontmatter: `title`, `slug`, `description`, `added` (ISO date or readable string)
- Optional: `tags` (array of strings)

## Development
- Dev server runs on `http://localhost:4321` via `make dev` or `pnpm dev`
- Tina env vars required: `TINACLIENTID`, `TINATOKEN`, `TINASEARCH`
- Check existing server before starting another—don't switch ports

## Build & Verify
- `make build` — Full production build
- `make lint` — ESLint
- `make typecheck` — TypeScript strict
- `make verify` — Run verification script

## Common Gotchas
- RSS feed requires all frontmatter fields; missing fields break the build
- Inline scripts use `<script is:inline>` to avoid esbuild bundling issues
- TinaCMS runs first in dev/build; missing env vars cause immediate failure
