# Technology Stack

**Analysis Date:** 2026-06-09

## Languages

**Primary:**
- TypeScript (ES2023 target) - Configured via `tsconfig.json`; no `.ts` source files committed yet (content-only extension, CI confirms no tracked TS)

**Secondary:**
- YAML - Skill definition frontmatter in `skills/skill-prefill-generation/SKILL.md`
- JSON - Package manifest and tsconfig

## Runtime

**Environment:**
- Node.js 24 (pinned in `.github/workflows/ci.yml` via `actions/setup-node@v4`)

**Package Manager:**
- pnpm (via corepack) — see `.npmrc` for `auto-install-peers=false`
- Lockfile: not committed (CI uses `--no-frozen-lockfile`)

## Frameworks

**Core:**
- None — this is a content-only Cinatra skill extension; no application framework is used

**Testing:**
- Not applicable — no test files or test runner configured

**Build/Dev:**
- TypeScript compiler (`tsc`) — configured in `tsconfig.json`; no build script defined in `package.json` (CI runs `tsc` ephemerally via `npx` if needed)
- GitHub Actions — CI/CD via `.github/workflows/ci.yml` and `.github/workflows/release.yml`

## Key Dependencies

**Critical:**
- None declared — `package.json` has no `dependencies`, `devDependencies`, or `peerDependencies`

**Infrastructure:**
- Cinatra platform (host monorepo) — this is a "source mirror" extension; `@cinatra-ai/*` packages are resolved by the monorepo workspace at integration time, not declared here

## Configuration

**Environment:**
- No `.env` files detected
- No runtime environment variables required by this content-only skill

**Build:**
- `tsconfig.json` — standalone strict TypeScript config targeting `src/**/*.ts`, outputs to `dist/`, `ES2023` target, `ESNext` modules, `bundler` module resolution

**Package:**
- `package.json` — declares `cinatra.apiVersion: cinatra.ai/v1`, `kind: skill`, capability `skill.prefill-generation` mapped to `skill-prefill-generation`
- `.npmrc` — sets `auto-install-peers=false`

## Platform Requirements

**Development:**
- Node.js 24+, pnpm (via corepack)
- Cinatra monorepo workspace for full typecheck/test (standalone install is skipped as a source mirror)

**Production:**
- Deployed via Cinatra Marketplace: GitHub Release triggers reusable workflow `cinatra-ai/.github/.github/workflows/reusable-extension-release.yml@main`
- Requires `CINATRA_MARKETPLACE_VENDOR_TOKEN` org secret and `cinatra-ai/.github` org infra

---

*Stack analysis: 2026-06-09*
