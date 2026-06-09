<!-- refreshed: 2026-06-09 -->
# Architecture

**Analysis Date:** 2026-06-09

## System Overview

```text
┌─────────────────────────────────────────────────────────────┐
│              Cinatra Workspace / Skill Catalog UI            │
│          (consumes skills via cinatra.capabilities map)      │
└──────────────────────────────┬──────────────────────────────┘
                               │  invokes skill by capability key
                               ▼
┌─────────────────────────────────────────────────────────────┐
│         skill-prefill-generation  (the single skill)        │
│         `skills/skill-prefill-generation/SKILL.md`          │
│                                                             │
│  Input:  SKILL.md content for any target skill              │
│  Output: one-line example user prompt (≤20 words, plain     │
│          text, no quotes, no markdown)                      │
└─────────────────────────────────────────────────────────────┘
                               │  registered via
                               ▼
┌─────────────────────────────────────────────────────────────┐
│              package.json  (cinatra manifest)               │
│   `package.json` → cinatra.capabilities map                 │
│   "skill.prefill-generation" → "skill-prefill-generation"   │
└─────────────────────────────────────────────────────────────┘
```

## Component Responsibilities

| Component | Responsibility | File |
|-----------|----------------|------|
| Package manifest | Declares skill bundle identity, version, capability registration | `package.json` |
| skill-prefill-generation | System-prompt skill that generates catalog prefill text for any SKILL.md | `skills/skill-prefill-generation/SKILL.md` |
| CI workflow | Validates package shape, dependency rules, typecheck, pack dry-run | `.github/workflows/ci.yml` |
| Release workflow | Publishes extension to Cinatra Marketplace via reusable org workflow | `.github/workflows/release.yml` |
| TypeScript config | Standalone strict TS config (targets `src/` if TypeScript sources are added) | `tsconfig.json` |

## Pattern Overview

**Overall:** Content-only Cinatra skill extension — no TypeScript sources, no runtime code. The entire "logic" is a natural-language system prompt in a SKILL.md file, registered as a named capability in the package manifest.

**Key Characteristics:**
- Pure prompt-as-code: behavior lives entirely in `skills/skill-prefill-generation/SKILL.md`
- Capability registration maps a string key (`skill.prefill-generation`) to a skill directory name
- No dependencies — `cinatra.dependencies` is an empty array
- The package is a source mirror: host-internal `@cinatra-ai/*` packages are provided by the monorepo, not installed standalone
- CI gate enforces that no first-party packages leak into `dependencies`/`devDependencies`

## Layers

**Manifest Layer:**
- Purpose: Declares the extension as a `cinatra.ai/v1` skill kind, registers capabilities
- Location: `package.json`
- Contains: npm metadata + `cinatra` block with `apiVersion`, `kind`, `dependencies`, `capabilities`
- Depends on: Nothing
- Used by: Cinatra workspace installer, catalog indexer

**Skill Content Layer:**
- Purpose: Provides the natural-language instruction that the Cinatra runtime executes when the capability is invoked
- Location: `skills/skill-prefill-generation/SKILL.md`
- Contains: YAML frontmatter (`name`, `description`) + plain-text system prompt
- Depends on: Nothing (no imports, no code)
- Used by: Cinatra runtime when `skill.prefill-generation` capability is called

**CI/CD Layer:**
- Purpose: Validates package shape and publishes releases
- Location: `.github/workflows/ci.yml`, `.github/workflows/release.yml`
- Contains: GitHub Actions workflows
- Depends on: `cinatra-ai/.github` reusable workflows (org-level, external)
- Used by: GitHub Actions on push/PR/release

## Data Flow

### Skill Invocation Path

1. Cinatra workspace receives a skill catalog indexing request
2. Workspace resolves capability key `skill.prefill-generation` via `package.json` → `cinatra.capabilities`
3. Runtime loads `skills/skill-prefill-generation/SKILL.md` as a system prompt
4. Runtime calls the LLM with the SKILL.md system prompt + target skill's SKILL.md content as user input
5. LLM returns a single short example prompt string (≤20 words, plain text)
6. Catalog stores the string as the "try saying…" prefill hint for the target skill

### CI Validation Path

1. Push or PR triggers `.github/workflows/ci.yml`
2. `Classify repo` step inspects `package.json` for first-party `@cinatra-ai/*` peers
3. Since this repo declares no first-party peers and has no TypeScript sources, the "content-only extension" branch runs
4. `npm pack --dry-run` validates publish payload shape
5. `kind-gates` job runs (no extra gate for `skill` kind)

## Key Abstractions

**Cinatra Skill:**
- Purpose: A named, versioned unit of LLM behavior registered by capability key
- Examples: `skills/skill-prefill-generation/SKILL.md`
- Pattern: YAML frontmatter defines identity; body is the system prompt passed verbatim to the LLM

**Capability Map:**
- Purpose: Connects a stable capability key (used by callers) to the implementing skill directory
- Examples: `package.json` → `cinatra.capabilities`
- Pattern: `"<capability-key>": "<skill-directory-name>"`

## Entry Points

**Capability Registration:**
- Location: `package.json` (`cinatra.capabilities` block)
- Triggers: Cinatra workspace install / skill catalog indexing
- Responsibilities: Maps `skill.prefill-generation` → `skill-prefill-generation` directory

**Skill Execution:**
- Location: `skills/skill-prefill-generation/SKILL.md`
- Triggers: Any Cinatra runtime call to the `skill.prefill-generation` capability
- Responsibilities: Provides system prompt constraining LLM output to a ≤20-word plain-text example prompt

## Architectural Constraints

- **No TypeScript sources:** `tsconfig.json` targets `src/` but no `src/` directory exists; CI skips typecheck for content-only extensions
- **No dependencies:** `cinatra.dependencies: []` — this skill bundle has zero runtime or skill dependencies
- **Source mirror contract:** First-party `@cinatra-ai/*` packages must not appear in `dependencies`/`devDependencies`; CI enforces this with exit code 2
- **Output constraint:** The skill's system prompt enforces ≤20 words, no quotes, no markdown in all generated prefill text
- **Global state:** None — stateless prompt-only extension
- **Circular imports:** Not applicable (no code)

## Anti-Patterns

### Adding TypeScript to this repo without a `src/` directory

**What happens:** `tsconfig.json` points `rootDir` at `src/` which does not exist. A `tsc --noEmit` run would fail with TS18003.
**Why it's wrong:** CI's content-only branch skips typecheck precisely because there are no tracked `.ts` files; adding a `.ts` file outside `src/` would be silently untypechecked.
**Do this instead:** Create `src/` first, place all TypeScript there, and add a `typecheck` script to `package.json` so CI picks it up via the explicit-script branch.

### Putting first-party `@cinatra-ai/*` packages in `dependencies`

**What happens:** CI's classify step exits with code 2 and fails the build.
**Why it's wrong:** These packages are monorepo-internal and not published to any registry; standalone install would fail.
**Do this instead:** Declare them as `peerDependencies` with `peerDependenciesMeta.<pkg>.optional: true`.

## Error Handling

**Strategy:** Not applicable — no runtime code. The skill's system prompt instructs the LLM to return only plain text; no error handling layer exists at the extension level.

**Patterns:**
- CI exits non-zero on dependency shape violations (enforced by inline Node.js script in `.github/workflows/ci.yml`)
- Release is gated behind the org reusable workflow which enforces tag == version

## Cross-Cutting Concerns

**Logging:** Not applicable (no runtime code)
**Validation:** Package shape validated by CI `classify` step; skill output constrained by system-prompt instructions
**Authentication:** Release workflow uses `CINATRA_MARKETPLACE_VENDOR_TOKEN` org secret (external, not stored in this repo)

---

*Architecture analysis: 2026-06-09*
