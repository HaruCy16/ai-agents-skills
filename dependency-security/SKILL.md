---
name: dependency-security
description: Audit and harden third-party dependencies and the software supply chain for JavaScript/TypeScript and other web projects. Use this skill whenever the user adds or updates packages, runs npm/pnpm/yarn audit, asks about vulnerable or outdated dependencies, lockfiles, Dependabot/Renovate, typosquatting, or installing an unfamiliar package, even if they don't say "supply chain."
---

# Dependency Security

Every dependency is code you ship but did not write. Minimize, pin, verify, and monitor.

## Before adding a package
1. Does the platform or an existing dependency already do this? Prefer built-ins.
2. Check: maintainers, last release date, weekly downloads, open security advisories, repo activity, license.
3. Verify the exact name. Typosquats (`lodash` vs `1odash`, `colors` vs `colour`) are common.
4. Check install scripts (`preinstall`/`postinstall`) in `package.json`; they run arbitrary code.
5. Prefer small, focused packages from known maintainers over large abandoned ones.

## Pin and lock
- Always commit the lockfile (`package-lock.json`, `pnpm-lock.yaml`, `yarn.lock`).
- In CI use `npm ci` / `pnpm install --frozen-lockfile`, never a plain install.
- Avoid `latest` and wide ranges for production dependencies.
- Prefer waiting about two weeks after a new release before upgrading, since malicious or broken releases are usually caught in that window.

## Audit
```bash
npm audit --omit=dev          # production deps only
npm outdated
npx osv-scanner --lockfile=package-lock.json   # if available
```
Triage by reachability: is the vulnerable function actually used, and is the dependency in production or dev only? Report **fix now / schedule / accept with reason**.

## Automate
- Enable Dependabot or Renovate with grouped, scheduled PRs.
- Run audit in CI and fail on high/critical in production deps.
- Enable GitHub secret scanning and push protection.

## Hardening
- Use `npm config set ignore-scripts true` for untrusted installs, then rebuild only what is needed.
- Scope registry tokens, use 2FA on publishing accounts, publish with provenance when you maintain packages.
- Remove unused dependencies (`npx depcheck`).
- Subresource Integrity (`integrity=` + `crossorigin`) for any CDN scripts.

## Output format
List: package, current → fixed version, severity, advisory ID, reachable (yes/no/unknown), recommended action. Provide the exact command to apply each fix.
