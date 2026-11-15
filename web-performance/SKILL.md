---
name: web-performance
description: Diagnose and improve web performance: Core Web Vitals (LCP, INP, CLS), bundle size, image/font/script loading, caching, rendering strategy, and database/API latency impact. Use this skill whenever the user says a page or app is slow, asks about Lighthouse scores, page speed, bundle size, lazy loading, caching, or CDN, or builds media-heavy pages, even if they don't mention "performance."
---

# Web Performance

Measure first, optimize the biggest bottleneck, measure again.

## Targets (75th percentile, real users)
- **LCP** ≤ 2.5 s · **INP** ≤ 200 ms · **CLS** ≤ 0.1
- Aim for initial JS < 170 KB gzipped on content pages; fewer, smaller requests.

## Measure
Lighthouse / PageSpeed Insights (lab), Chrome DevTools Performance panel, WebPageTest, `web-vitals` library + RUM for field data. Always test on throttled mobile (4× CPU, Slow 4G).

## LCP (loading)
- Identify the LCP element (usually hero image or heading).
- Preload it: `<link rel="preload" as="image" href="hero.avif" fetchpriority="high">`; never lazy-load the LCP image.
- Serve modern formats (AVIF/WebP), responsive `srcset`/`sizes`, correct dimensions.
- Reduce server response time (TTFB): caching, CDN, edge rendering, faster queries.
- Remove render-blocking CSS/JS; inline critical CSS; `defer` scripts.

## CLS (stability)
- Set `width`/`height` or `aspect-ratio` on images, video, embeds, ads.
- Reserve space for dynamic content; avoid inserting above existing content.
- Fonts: `font-display: swap` + preload key font files + size-adjusted fallbacks.

## INP (responsiveness)
- Break up long tasks (> 50 ms); yield with `scheduler.yield()` / `setTimeout`.
- Debounce/throttle handlers; move heavy work to Web Workers.
- Avoid large re-renders: memoize, virtualize long lists, split state.

## JavaScript
- Code-split by route and heavy components (`import()`), lazy-load below-the-fold.
- Analyze bundles (`vite-bundle-visualizer`, `webpack-bundle-analyzer`); replace heavy libs (moment → date-fns/Intl).
- Tree-shake, avoid barrel-file bloat, ship modern syntax.
- Third-party scripts: load async/deferred, delay non-essential ones until interaction.

## Images, fonts, CSS
- Lazy-load offscreen images (`loading="lazy"`), `decoding="async"`.
- Subset fonts, ≤ 2 families, WOFF2.
- Purge unused CSS; avoid huge frameworks when little is used.

## Caching and delivery
- Hashed static assets: `Cache-Control: public, max-age=31536000, immutable`.
- HTML: short cache or `stale-while-revalidate`.
- Enable Brotli, HTTP/2 or 3, a CDN; use `preconnect` for critical third-party origins.

## Rendering strategy
Static (SSG) > ISR/edge-cached SSR > full SSR > client-only for content pages. Use SPA rendering only where interactivity requires it.

## Backend contributors
- N+1 queries, missing indexes (see `database-design`), unpaginated responses, chatty APIs. Add response compression and caching.

## Output
Report: metric before → likely cause → fix (code) → expected impact. Order by estimated impact vs. effort.
