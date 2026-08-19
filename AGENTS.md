# AGENTS.md — Outkast (Lexington Themes)

**Outkast** is an Astro multipage theme for a creative studio or agency: landing sections (hero, services, work, testimonials, blog preview), portfolio **work** case studies, **team** profiles, **services** detail pages, **blog** with tags, pricing variants, contact, legal pages, and `/system/*` reference pages (colors, typography, buttons). Primary use case: **marketing and lead generation** for a design/dev collective or SaaS-adjacent services business.

**Publisher:** [Lexington Themes](https://lexingtonthemes.com/)

## Tech stack (this repo)

| Source | Details |
|--------|---------|
| `package.json` | **Astro** `^6.0.0`; **Tailwind CSS** `^4.1.18` with `@tailwindcss/vite`, `@tailwindcss/forms`, `@tailwindcss/typography`, `tailwind-scrollbar-hide`; **MDX** `@astrojs/mdx` `^5.0.0`; **RSS** `@astrojs/rss` `^4.0.14`; **Sitemap** `@astrojs/sitemap` `^3.6.0`; **`@lexingtonthemes/seo`** `^0.1.0` (used in `Seo.astro`). |
| `astro.config.mjs` | `integrations: [sitemap(), mdx()]`; `vite.plugins: [tailwindcss()]`; `site: "https://yourdomain.com"` (replace for production); Markdown/Shiki `css-variables`, drafts enabled. |
| Aliases | `tsconfig.json`: `@/*` → `src/*`. |

No other `@lexingtonthemes/*` packages appear in `package.json`.

## Folder map

| Path | Role |
|------|------|
| `src/pages/` | File-based routes (see Routing below). |
| `src/layouts/` | `BaseLayout.astro` (shell); `BlogLayout`, `WorkLayout`, `TeamLayout`, `ServicesLayout`, `LegalLayout` for content-driven pages. |
| `src/components/` | `landing/`, `global/` (Navigation, Footer, Search), `blog/`, `work/`, `team/`, `services/`, `ctas/`, `assets/`, **`fundations/`** (head, elements, containers, icons, scripts). |
| `src/content/` | Markdown/MDX for collections (`team`, `work`, `services`, `posts`, `legal`). |
| `src/styles/` | `global.css` — Tailwind v4 `@import`, plugins, `@theme` tokens (fonts, colors, motion). |
| `src/images/` | In this checkout: `brands/*.svg`. Content and pages also reference paths like `src/images/blog/`, `src/images/work/`, `src/images/assets/` — ensure those assets exist locally if you rely on them. |
| `public/` | Not present in this repository. |

## Content collections (`src/content.config.ts`)

Loader pattern for all collections: `glob` from Astro (`astro/loaders`). Image fields use Zod `image()` from the schema callback — pass paths Astro can resolve (see existing entries).

### `team` → `src/content/team/**/*.md`

- **Fields:** `name`, `role`, `intro` (strings); `education`, `experience` (string arrays); `avatar: { url: image(), alt }`.
- **Images:** `avatar.url` must satisfy `image()`.
- **Template:** copy `src/content/team/1.md`.

### `work` → `src/content/work/**/*.md`

- **Fields:** `work` (string, required); `link`, `company`, `year`, `client` (optional strings); `credits` (optional array of `{ name, role }`); `thumbnail: { url: image(), alt }` (required).
- **Images:** `thumbnail.url` must satisfy `image()`.
- **Template:** copy `src/content/work/1.md`.

### `services` → `src/content/services/**/*.md`

- **Fields:** `service`, `description` (strings). No `image()` in schema.
- **Template:** copy `src/content/services/design-systems.md`.

### `posts` → `src/content/posts/**/*.(md|mdx)`

- **Fields:** `title`, `description`, `author` (strings); `pubDate` (date); `image: { url: image(), alt }`; `tags` (string array).
- **Images:** `image.url` must satisfy `image()`.
- **Template:** copy `src/content/posts/1.md`.

### `legal` → `src/content/legal/**/*.md`

- **Fields:** `page` (string — displayed as title), `pubDate` (date).
- **Template:** copy `src/content/legal/privacy.md`.

## Routing conventions

Routes are **file-based** under `src/pages/`. Dynamic routes use **`[...slug]`** with `getStaticPaths` and `entry.id` (filename without extension, including nested paths if you add subfolders).

| Area | URL pattern | Source |
|------|-------------|--------|
| Home | `/` | `index.astro` |
| Blog index | `/blog` | `blog/index.astro` |
| Blog post | `/blog/posts/{id}` | `blog/posts/[...slug].astro` — `params.slug === entry.id` |
| Tags index | `/blog/tags` | `blog/tags/index.astro` |
| Tag filter | `/blog/tags/{tag}` | `blog/tags/[tag].astro` — tag from frontmatter `tags` |
| RSS | `/rss.xml` | `rss.xml.js` — items link to `/blog/posts/${entry.id}` |
| Work | `/work`, `/work/{id}` | `work/index.astro`, `work/[...slug].astro` |
| Team | `/team`, `/team/{id}` | `team/index.astro`, `team/[...slug].astro` |
| Services | `/services`, `/services/{id}` | `services/index.astro`, `services/[...slug].astro` |
| Legal (CMS) | `/legal/{id}` | `legal/[...slug].astro` — e.g. `privacy.md` → `/legal/privacy` |
| Legal (static) | `/legal/about` | `legal/about.astro` (not from collection) |
| Pricing | `/pricing-simple`, `/pricing-full` | dedicated `.astro` files |
| Contact | `/contact` | `contact.astro` |
| System UI ref | `/system/overview`, `/system/colors`, `/system/typography`, `/system/buttons`, `/system/links` | `system/*.astro` |
| 404 | `/404` | `404.astro` |

**Note:** There is no `changelog` content collection in this repo; product changelog for the theme is on Lexington’s site (see README links).

## Customization guide

- **Site URL / canonical domains:** Set `site` in `astro.config.mjs`. RSS (`src/pages/rss.xml.js`) uses `context.site`. Replace placeholder URLs inside `src/components/fundations/head/Seo.astro` (hardcoded `AstroSeo` / meta examples) and anywhere else you still see `yourdomain.com`-style defaults.
- **Brand colors & typography:** `src/styles/global.css` — `@theme` block (`--font-sans`, `--color-base-*`, `--color-accent-*`, animation tokens). Typography stack is wired to **Geist** in `src/components/fundations/head/Fonts.astro`.
- **Navigation / footer:** `src/components/global/navigation/Navigation.astro`, `src/components/global/Footer.astro`.
- **Global shell:** `src/layouts/BaseLayout.astro` imports `global.css`, wraps pages with `BaseHead`, `Navigation`, `main`, `Footer`. **Head:** `src/components/fundations/head/BaseHead.astro` composes `Seo`, `Meta`, `Fonts`, `Favicons`, `FuseJS`, `KeenSlider`.
- **Per-section layouts:** Use the matching layout from `src/layouts/` when adding collection-driven pages (same pattern as existing `[...slug].astro` files).

## Commands

From `README.md` / `package.json` (project root):

| Command | Action |
|---------|--------|
| `npm install` | Install dependencies |
| `npm run dev` | `astro dev` |
| `npm run build` | `astro build` → `./dist/` |
| `npm run preview` | `astro preview` |
| `npm run astro -- ...` | Astro CLI |

Requirements (README): Node.js **18 or 20** (LTS), **npm**.

## Guardrails

- **Do not rename** the `src/components/fundations/` folder; the spelling `fundations` is intentional and used across imports.
- **Avoid widening** Zod schemas in `src/content.config.ts` without updating every page/component that reads `entry.data` for that collection.
- Prefer **minimal diffs** and follow existing patterns (`getCollection` + `render`, passing `frontmatter={entry.data}` into layouts).
- **`image()` fields:** keep `url`/`alt` shapes stable; broken `image()` paths will fail the content pipeline.

## Lexington docs & support (from README)

- Theme specs: https://lexingtonthemes.com/templates/outkast  
- Documentation: https://lexingtonthemes.com/documentation  
- Theme changelog: https://lexingtonthemes.com/changelog/outkast  
- Support: https://lexingtonthemes.com/legal/support/  
- Bundle / shop: https://lexingtonthemes.com  
