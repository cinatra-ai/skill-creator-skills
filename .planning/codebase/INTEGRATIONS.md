# External Integrations

**Analysis Date:** 2026-06-09

## APIs & External Services

**Cinatra Platform:**
- Cinatra Marketplace — extension submission and promotion pipeline
  - SDK/Client: reusable workflow `cinatra-ai/.github/.github/workflows/reusable-extension-release.yml@main`
  - Auth: `CINATRA_MARKETPLACE_VENDOR_TOKEN` (GitHub org secret, inherited via `secrets: inherit` in `.github/workflows/release.yml`)
  - Flow: GitHub Release → MCP proxy (`extension-submit-for-review`) → approve → promotion saga → `registry.cinatra.ai`

**Cinatra Skills Catalog:**
- This package is registered into the Cinatra skills catalog at workspace level
- Capability `skill.prefill-generation` is consumed by the Cinatra host monorepo at authoring time to generate one-line example prompts for new skills

## Data Storage

**Databases:**
- Not applicable — no database used

**File Storage:**
- Local filesystem only — skill definition lives in `skills/skill-prefill-generation/SKILL.md`

**Caching:**
- Not applicable

## Authentication & Identity

**Auth Provider:**
- GitHub Actions OIDC — `id-token: write` permission granted in `.github/workflows/release.yml` for build-provenance attestation
- `CINATRA_MARKETPLACE_VENDOR_TOKEN` — org-level secret for marketplace submission (not present in this repo; provided by the `cinatra-ai` GitHub org)

## Monitoring & Observability

**Error Tracking:**
- Not applicable — no runtime service

**Logs:**
- GitHub Actions job logs only

## CI/CD & Deployment

**Hosting:**
- Cinatra Marketplace / `registry.cinatra.ai` (extension registry)

**CI Pipeline:**
- GitHub Actions
  - `.github/workflows/ci.yml` — runs on push/PR to `main`: classifies repo (source mirror vs standalone), conditionally installs, typechecks, tests, and runs `npm pack --dry-run` + kind-specific gate
  - `.github/workflows/release.yml` — triggers on GitHub Release publish or manual `workflow_dispatch`; delegates entirely to `cinatra-ai/.github` reusable workflow

## Environment Configuration

**Required env vars:**
- None at runtime (content-only extension)
- `CINATRA_MARKETPLACE_VENDOR_TOKEN` — required in the `cinatra-ai` GitHub org secrets for release publishing

**Secrets location:**
- GitHub org-level secrets (`cinatra-ai` organization); none stored in this repository

## Webhooks & Callbacks

**Incoming:**
- Not applicable

**Outgoing:**
- Not applicable — no webhook endpoints

---

*Integration audit: 2026-06-09*
