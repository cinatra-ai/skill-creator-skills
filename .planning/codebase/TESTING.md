# Testing Patterns

**Analysis Date:** 2026-06-09

## Repository Nature

This is a content-only Cinatra skill extension with no TypeScript source files and no test files. There is no test framework, no test runner configuration, and no test suite. The repository's quality gate is entirely CI-based structural validation.

## Test Framework

**Runner:** Not applicable — no test files exist.

**Run Commands:**
```bash
# CI runs: corepack pnpm test --if-present
# The --if-present flag means no error is raised when no `test` script is defined in package.json.
# package.json defines no `test` script.
```

## CI-Based Validation (Replaces Unit Tests)

The primary quality gate is the `build` job in `.github/workflows/ci.yml`. It performs structural validation via inline Node.js scripts — no test framework required.

**Validations performed:**

1. **First-party dependency shape check** — Fails with exit code 2 if any `@cinatra-ai/*` or `@cinatra/*` package appears in `dependencies`, `devDependencies`, or `optionalDependencies`. Also fails if first-party peers are not marked `peerDependenciesMeta.<pkg>.optional: true`.

2. **Typecheck** — Skipped for this repo because it declares no first-party peers and has no tracked TypeScript files (the step detects this via `git ls-files '*.ts' '*.tsx' '*.mts' '*.cts'`).

3. **Test step** — Runs `corepack pnpm test --if-present`. Since no `test` script is defined in `package.json`, this is a no-op.

4. **Pack dry-run** — `npm pack --dry-run` validates package shape and publish payload without resolving peers. This is the only content-integrity check for skill files.

**Kind-specific gate:**
- The `kind-gates` job in CI runs after `build` but performs no additional checks for `kind: skill` extensions (only `workflow` and `agent` kinds have extra gates).

## Test File Organization

No test files exist. No test directory structure is present.

## Mocking

Not applicable.

## Fixtures and Factories

Not applicable.

## Coverage

**Requirements:** Not enforced — no test suite exists.

## What to Test When Adding Source Files

If TypeScript sources are added to `src/`, the CI pipeline will automatically:
- Run `corepack pnpm run typecheck` if a `typecheck` script is defined in `package.json`
- Fall back to `npx -y -p typescript tsc --noEmit` if `typescript` is not a local dep
- Run `corepack pnpm test --if-present` which will execute any `test` script added to `package.json`

**Recommended patterns for future tests:**
- Add a test runner (e.g., vitest) as a `devDependency` if `@cinatra-ai/*` packages are not required
- Place test files adjacent to source files as `*.test.ts` or in a `src/__tests__/` directory
- Add a `"test": "vitest run"` script to `package.json`

## Skill Content Validation

No automated validation exists for the content of `skills/*/SKILL.md` files beyond:
- The `npm pack --dry-run` step confirming files are included in the publish payload
- Human review that the skill body follows the constraints stated in README.md (max 20 words output, no quotes, no markdown in generated text)

---

*Testing analysis: 2026-06-09*
