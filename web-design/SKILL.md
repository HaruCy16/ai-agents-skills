---
name: web-design
description: Design and build visually polished, conversion-focused web interfaces: layout, typography, color, spacing, design tokens, responsive behavior, hierarchy, and UI components. Use this skill whenever the user builds or restyles a landing page, dashboard, portfolio, component, or any frontend, or says things like "make it look better," "clean UI," "modern design," or "improve the layout," even if they don't mention design explicitly.
---

# Web Design

Design is a business tool: every choice should serve clarity, hierarchy, and the page's goal (convert, inform, retain). Avoid generic, template-looking output.

## Before designing
Clarify or infer: audience, primary action (CTA), brand tone, content available, device mix. If the user has a style preference, follow it over these defaults.

## Foundations (define as tokens first)
```css
:root {
  /* spacing scale (4px base) */
  --space-1: .25rem; --space-2: .5rem; --space-3: .75rem; --space-4: 1rem;
  --space-6: 1.5rem; --space-8: 2rem; --space-12: 3rem; --space-16: 4rem;
  /* type scale (1.25 ratio) */
  --text-sm: .8rem; --text-base: 1rem; --text-lg: 1.25rem; --text-xl: 1.563rem; --text-2xl: 1.953rem; --text-3xl: 2.441rem;
  /* color roles, not raw colors */
  --bg: ; --surface: ; --text: ; --text-muted: ; --primary: ; --accent: ; --border: ; --danger: ;
  --radius: .75rem;
}
```
Use roles (`--primary`) rather than hardcoded hex in components. Support dark mode by redefining the tokens under `prefers-color-scheme: dark`.

## Principles
1. **Hierarchy:** one clear focal point per screen. Vary size, weight, and contrast; not everything can be bold.
2. **Typography:** max two typefaces. Body 16 to 18px, line-height 1.5 to 1.7, line length 60 to 75 characters. Pick distinctive, intentional fonts over defaults.
3. **Color:** one primary, one accent, neutrals. Keep contrast ≥ 4.5:1 for text (see `accessibility`). Reserve red/green for status.
4. **Spacing:** use the scale consistently; generous whitespace groups related items and separates sections.
5. **Alignment and grid:** CSS Grid for page layout, Flexbox for components; align to a consistent grid.
6. **Consistency:** reuse components and states (default, hover, focus, active, disabled, loading, error, empty).
7. **Motion:** purposeful and short (150 to 250 ms); respect `prefers-reduced-motion`.

## Responsive
- Mobile-first; breakpoints around 640 / 768 / 1024 / 1280.
- Fluid type and spacing with `clamp()`.
- Touch targets ≥ 44×44px. Test at 360px width.

## Landing page structure (default)
Hero (value prop + CTA) → social proof → benefits → how it works → pricing/offer → FAQ → final CTA → footer.

## Avoid
Stock gradients on everything, centered-everything layouts, five competing colors, tiny low-contrast gray text, walls of text, inconsistent border radii, placeholder lorem ipsum in finished work.

## Output
Deliver working code (HTML/CSS/JS or the user's framework) with tokens defined at the top. State briefly the design decisions made (palette, type, layout rationale) in 3 to 5 lines.
