---
name: accessibility
description: Build and audit accessible web interfaces to WCAG 2.2 AA: semantic HTML, keyboard navigation, focus management, ARIA, color contrast, forms, images, motion, and screen reader support. Use this skill whenever the user builds UI components, forms, modals, menus, tabs, or asks about a11y, WCAG, screen readers, contrast, or "making it usable for everyone," and apply it proactively when reviewing any frontend code.
---

# Accessibility (WCAG 2.2 AA)

Accessible by default is cheaper than retrofitting. Start with semantic HTML; add ARIA only when HTML cannot express the behavior.

## Semantics first
- Use `<header> <nav> <main> <footer> <section> <article>`, one `<h1>`, headings in order.
- Interactive elements: `<button>` for actions, `<a href>` for navigation. Never a clickable `<div>`.
- Lists as `<ul>/<ol>`, data as `<table>` with `<th scope>` and `<caption>`.

## Keyboard
- Everything operable via keyboard; logical tab order (DOM order, avoid positive `tabindex`).
- Visible focus indicator (≥ 3:1 contrast). Never `outline: none` without a replacement.
- Modals/dialogs: move focus in, trap it, close on `Esc`, restore focus to the trigger. Prefer native `<dialog>`.
- Provide a "Skip to content" link.

## Forms
- Every input has a `<label for>` (placeholder is not a label).
- Group related controls with `<fieldset><legend>`.
- Errors: text message tied via `aria-describedby`, `aria-invalid="true"`, not color alone; move focus to the first error or an error summary.
- Use correct `type` and `autocomplete` values.

## Images and media
- Informative images: meaningful `alt`. Decorative: `alt=""`.
- Video: captions; audio: transcripts. No autoplay with sound.

## Color and visuals
- Text contrast ≥ 4.5:1 (≥ 3:1 for large text and UI components).
- Never convey meaning by color alone; add text or icons.
- Support zoom to 200% and reflow at 320px without horizontal scroll.
- Respect `prefers-reduced-motion` and `prefers-color-scheme`.

## ARIA rules of thumb
1. No ARIA is better than bad ARIA.
2. Don't override native semantics.
3. All custom widgets need role, name, state, and keyboard support per the ARIA Authoring Practices.
4. Use `aria-live="polite"` for dynamic status updates.
5. `aria-label` for icon-only buttons.

## Targets
- Pointer targets ≥ 24×24 CSS px minimum (aim for 44×44).

## Testing
- Keyboard-only pass; screen reader spot check (NVDA/VoiceOver); axe DevTools or Lighthouse; `eslint-plugin-jsx-a11y`; `@axe-core/playwright` in CI.

## Output
For audits: list **issue, WCAG criterion, location, fix (code)**, ordered by impact. For new components: deliver code that already meets the above and note the keyboard interactions supported.
