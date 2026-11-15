---
name: frontend-architecture
description: Structure and review frontend applications (React, Next.js, Vue, Svelte, vanilla TS): folder layout, component design, state management, data fetching, routing, forms, error handling, and scalability. Use this skill whenever the user starts a new frontend project, asks where to put code, how to manage state, how to split components, SSR vs SPA, or when a codebase feels messy or hard to scale.
---

# Frontend Architecture

Keep it simple, colocated, and easy to delete. Add abstraction only when a second use case appears.

## Choose the rendering model
| Need | Choice |
|---|---|
| SEO, content, fast first load | SSG/SSR (Next.js, Nuxt, SvelteKit, Astro) |
| Highly interactive app behind login | SPA (Vite + React/Vue) |
| Mostly static with islands of interactivity | Astro / islands architecture |

## Folder structure (feature-based)
```
src/
├── app/ or routes/        # routing, layouts, providers
├── features/
│   └── billing/
│       ├── components/
│       ├── hooks/
│       ├── api.ts          # data access for this feature
│       ├── types.ts
│       └── index.ts        # public surface of the feature
├── components/ui/          # shared, presentational, design-system primitives
├── lib/                    # utils, http client, constants
└── styles/
```
Rules: features don't import from each other's internals (only `index.ts`); shared code flows downward (`features` → `components/ui` → `lib`).

## Components
- Small, single-purpose; split when it has >1 reason to change.
- Separate **presentational** (props in, UI out) from **container/logic** (hooks, data).
- Extract logic into hooks/composables; keep JSX/templates declarative.
- Prefer composition (`children`, slots) over deep prop drilling and boolean-prop explosions.

## State: pick the lowest level that works
1. Local component state
2. Lifted state / context (for a subtree)
3. **Server state** → TanStack Query / SWR / framework loaders (cache, retry, invalidation). Do not copy server data into global stores.
4. URL state (filters, tabs, pagination) in query params
5. Global client state (Zustand/Pinia/Redux Toolkit) only for truly app-wide UI state

## Data fetching
- Centralize the HTTP client (base URL, auth header, error normalization).
- Type API responses; validate at the boundary with Zod when data is untrusted.
- Always handle loading, error, empty, and success states.

## Forms
- React Hook Form / Vee-Validate + schema validation (Zod); server validation is still authoritative.

## Errors and resilience
- Error boundaries per route/feature; user-friendly fallbacks; report to Sentry or similar.
- Optimistic updates with rollback where UX benefits.

## Quality
- TypeScript strict mode; ESLint + Prettier; path aliases; Storybook for shared UI.
- Environment config via typed env module; never expose secrets in client bundles (see `web-security`).

## Output
When asked to structure a project, deliver the folder tree, the key conventions in a short list, and starter code for the shared pieces (HTTP client, providers, one example feature). When reviewing, list **problem → why it hurts → refactor**.
