# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Quick Reference

| Command | Action |
|---|---|
| `pnpm install` | Install dependencies |
| `pnpm run dev` | Start dev server (http://localhost:4321) |
| `pnpm run build` | Type-check (`astro check`), build to `dist/`, generate Pagefind search index |
| `pnpm run preview` | Preview production build locally |
| `pnpm run sync` | Generate Astro TypeScript types |
| `pnpm run lint` | ESLint (flat config) |
| `pnpm run format` | Prettier auto-format |
| `pnpm run format:check` | Prettier dry-run check |

There are no test suites — this project relies on type-checking and manual verification.

## Architecture

This is **AstroPaper** — an Astro v5 static blog using Tailwind CSS v4, Shiki syntax highlighting, and Pagefind search. No client-side framework (React/Vue/Svelte). All interactivity is vanilla JS inside `<script>` tags.

### Content Layer

All blog posts live as Markdown in `src/data/blog/`. Astro's Content Layer API (defined in `src/content.config.ts`) loads them via `glob` with a Zod schema. Only `.md` files are loaded; files/directories prefixed with `_` (underscore) are hidden from routes.

Key detail: a post's URL path mirrors its filesystem path. E.g., `src/data/blog/_releases/my-post.md` → `/posts/my-post` (the `_releases` segment is stripped). `src/data/blog/2025/hello.md` → `/posts/2025/hello`.

Frontmatter fields: `author`, `pubDatetime` (required), `modDatetime`, `title` (required), `featured`, `draft`, `tags` (defaults to `["others"]`), `ogImage`, `description` (required), `canonicalURL`, `hideEditPost`, `timezone`.

Draft posts and future-dated posts (with a 15-minute margin) are hidden in production builds but visible in dev.

### Routing (file-based)

| Route | File |
|---|---|
| `/` | `src/pages/index.astro` |
| `/posts` / `/posts/2` | `src/pages/posts/[...page].astro` |
| `/posts/:slug` | `src/pages/posts/[...slug]/index.astro` |
| `/posts/:slug/index.png` | `src/pages/posts/[...slug]/index.png.ts` |
| `/archives` | `src/pages/archives/index.astro` |
| `/tags` | `src/pages/tags/index.astro` |
| `/tags/:tag` | `src/pages/tags/[tag]/[...page].astro` |
| `/search` | `src/pages/search.astro` |
| `/about` | `src/pages/about.md` |
| `/rss.xml` | `src/pages/rss.xml.ts` |
| `/og.png` | `src/pages/og.png.ts` |

### Component Hierarchy

`Layout.astro` is the root HTML shell (head, meta, JSON-LD structured data, theme script, fonts). Inner layouts extend it:
- `Main.astro` — generic page wrapper (breadcrumb + title + main slot)
- `PostDetails.astro` — full blog post with tags, prev/next nav, share links, OG image, code copy buttons
- `AboutLayout.astro` — about page wrapper

Navigation: `Header.astro` provides the global nav. `Breadcrumb.astro` shows Home » Posts » ... path. `BackButton.astro` uses `sessionStorage.getItem("backUrl")` for view-transition-aware back navigation.

### Key Utilities (`src/utils/`)

- `postFilter.ts` — filters out drafts and future-dated posts in production
- `getSortedPosts.ts` — sorts by `modDatetime` descending (falls back to `pubDatetime`)
- `getPostsByTag.ts` — filters posts by slugified tag
- `getUniqueTags.ts` — deduplicates and alphabetically sorts all tags
- `getPath.ts` — resolves a post's content ID + file path to its public URL, handling `_`-prefixed path segments
- `slugify.ts` — URL-safe slug generation
- `og-templates/postOg.tsx` — Satori JSX template for per-post OG images

### Styling

Tailwind CSS v4 (Vite plugin). Theme colors defined as CSS custom properties in `global.css`. Dark mode via `data-theme="dark"` attribute on `<html>`. Custom variant `@custom-variant dark (&:where([data-theme=dark], [data-theme=dark] *))`. JetBrains Mono is the primary font. Animated notebook grid background via CSS keyframes.

### Configuration

`src/config.ts` exports a single `SITE` object controlling: website URL, author, timezone (`Asia/Shanghai`), posts per page, draft/scheduling behavior, archives visibility, edit-post link, dynamic OG images, and more.

`src/constants.ts` exports `SOCIALS` (header/footer social links) and `SHARE_LINKS` (post sharing buttons). Each entry references an SVG icon from `src/assets/icons/`.

### Import Alias

`@/` maps to `./src/` (configured in `tsconfig.json` paths).

### Shiki Transformers

The project ships a custom transformer at `src/utils/transformers/fileName.js` that reads `file="filename"` metadata from fenced code blocks and renders a styled filename label above the block.

### Deployment

Targets Cloudflare Pages. Docker image uses a multi-stage build (`node:lts` + pnpm for build, `nginx:alpine` for serving on port 80).
