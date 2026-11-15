---
name: seo-and-metadata
description: Implement technical SEO and metadata for websites: title/description tags, Open Graph and Twitter cards, canonical URLs, structured data (JSON-LD), sitemap, robots.txt, semantic markup, URL structure, internationalization, and indexability checks. Use this skill whenever the user builds a public-facing page, landing page, blog, portfolio, or e-commerce site, asks about ranking, Google, link previews, or social sharing, even if they don't say "SEO."
---

# SEO and Metadata

Search engines reward fast, crawlable, well-structured pages with useful content. Technical SEO makes sure nothing blocks that.

## Per-page essentials (in `<head>`)
```html
<title>Primary Keyword: Brand | ≤ 60 chars</title>
<meta name="description" content="Compelling summary with a reason to click. 120–160 chars.">
<link rel="canonical" href="https://example.com/page">
<meta name="viewport" content="width=device-width, initial-scale=1">
<html lang="en">
```
- Unique title and description per page; one `<h1>` matching intent.
- `robots` meta (`noindex`) only for pages that must not appear (admin, thank-you, staging).

## Social previews
```html
<meta property="og:type" content="website">
<meta property="og:title" content="...">
<meta property="og:description" content="...">
<meta property="og:url" content="https://example.com/page">
<meta property="og:image" content="https://example.com/og/page.jpg"> <!-- 1200×630 -->
<meta name="twitter:card" content="summary_large_image">
```
Use absolute URLs for images; verify with the platforms' share debuggers.

## Structured data (JSON-LD)
Add types that match real content: `Organization`, `WebSite`, `BreadcrumbList`, `Article`, `Product`, `FAQPage`, `LocalBusiness`.
```html
<script type="application/ld+json">
{ "@context": "https://schema.org", "@type": "Organization",
  "name": "Brand", "url": "https://example.com", "logo": "https://example.com/logo.png" }
</script>
```
Never mark up content that isn't visible on the page. Validate with Google's Rich Results Test.

## Crawlability and indexing
- `robots.txt` at root with a `Sitemap:` line; `sitemap.xml` listing canonical, indexable URLs (auto-generate).
- Clean, descriptive, lowercase, hyphenated URLs; stable (301 redirect when they change).
- Internal links with descriptive anchor text; no orphan pages.
- Render important content in HTML (SSR/SSG), not only after client-side JS.
- One canonical version (https, preferred host); fix duplicate content with `rel=canonical`.
- Return real `404`/`410` status codes, not soft 404s.

## Page experience
- Core Web Vitals matter (see `web-performance`); mobile-first responsive; HTTPS.
- Images: descriptive filenames, `alt` text (see `accessibility`), modern formats, dimensions set.

## International
`<link rel="alternate" hreflang="x">` sets with a self-reference and `x-default`.

## Framework notes
- Next.js: `generateMetadata`, `app/sitemap.ts`, `app/robots.ts`.
- Astro/SvelteKit/Nuxt: use their head/meta APIs and sitemap integrations.
- SPA: prerender or SSR any page that must rank.

## Content
Match search intent, use natural keywords in title/headings/first paragraph, publish original and useful content; avoid keyword stuffing.

## Output
Deliver the exact tags/files for the user's framework, a checklist of what to verify (Search Console, share debuggers, Lighthouse SEO), and notes on any content gaps found.
