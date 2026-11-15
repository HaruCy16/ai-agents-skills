---
name: deployment-cicd
description: Set up and review deployment pipelines and hosting for web apps: GitHub Actions CI/CD, environment variables and secrets, Docker basics, preview and production environments, zero-downtime releases, rollbacks, monitoring, and logging. Use this skill whenever the user deploys an app, writes a workflow file, Dockerfile, or nginx config, asks about Vercel/Netlify/Railway/VPS hosting, env vars, or "how do I ship this," even if they don't say CI/CD.
---

# Deployment and CI/CD

Automate everything repeatable. Every merge to main should be deployable; every deploy should be reversible.

## Pick the hosting model
| Situation | Option |
|---|---|
| Static site / JAMstack / Next.js | Vercel, Netlify, Cloudflare Pages |
| Full-stack app, simple ops | Railway, Render, Fly.io |
| Full control / cost-sensitive | VPS (Docker + Caddy/nginx), Linux hardened |
| Scale/enterprise | Managed containers/Kubernetes (only when justified) |

## Environments
`local` → `preview` (per PR) → `staging` → `production`. Config differs by environment variables only; the same build artifact is promoted.

## Environment variables and secrets
- Never commit `.env`; commit `.env.example` with fake values.
- Store secrets in the platform's secret store / GitHub Secrets / a vault.
- Client-exposed vars (`NEXT_PUBLIC_*`, `VITE_*`) must contain nothing secret.
- Validate env at startup with a schema (fail fast).
- Rotate secrets on suspected exposure.

## GitHub Actions baseline
```yaml
name: CI
on:
  pull_request:
  push:
    branches: [main]

permissions:
  contents: read

jobs:
  test:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      - uses: actions/setup-node@v4
        with: { node-version: 22, cache: npm }
      - run: npm ci
      - run: npm run lint
      - run: npm run typecheck
      - run: npm test -- --run
      - run: npm run build
```
- Least-privilege `permissions`; pin third-party actions to a commit SHA for sensitive pipelines.
- Cache dependencies; run jobs in parallel; add a deploy job gated on `main` and passing tests.
- Protect `main`: required checks and reviews.

## Docker (Node example)
```dockerfile
FROM node:22-alpine AS build
WORKDIR /app
COPY package*.json ./
RUN npm ci
COPY . .
RUN npm run build

FROM node:22-alpine
WORKDIR /app
ENV NODE_ENV=production
COPY --from=build /app/dist ./dist
COPY --from=build /app/package*.json ./
RUN npm ci --omit=dev
USER node
EXPOSE 3000
HEALTHCHECK CMD wget -qO- http://localhost:3000/health || exit 1
CMD ["node", "dist/server.js"]
```
Multi-stage, non-root user, `.dockerignore` (exclude `node_modules`, `.git`, `.env`), small base image.

## Release safety
- Database migrations run before the new code needs them and stay backward-compatible (see `database-design`).
- Strategies: rolling, blue/green, or canary; feature flags for risky changes.
- Keep the previous release available for one-command rollback.
- Health check endpoint and smoke test after deploy.

## VPS hardening (if self-hosting)
SSH keys only, disable root login, firewall (ufw: 22/80/443), automatic security updates, fail2ban, TLS via Let's Encrypt, regular backups with a tested restore.

## Observability
- Structured logs with request IDs; error tracking (Sentry); uptime checks; basic metrics (latency, error rate, saturation); alerts to a channel someone watches.

## Output
Deliver ready-to-use files (workflow YAML, Dockerfile, `.env.example`, config) and a short "first deploy" and "rollback" runbook.
