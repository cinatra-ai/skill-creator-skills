# Codebase Structure

**Analysis Date:** 2026-06-09

## Directory Layout

```
skill-creator-skills/          # repo root
├── .github/
│   └── workflows/
│       ├── ci.yml             # Build, validate, pack dry-run on push/PR
│       └── release.yml        # Publish to Cinatra Marketplace on GitHub Release
├── .planning/
│   └── codebase/              # GSD mapping documents (this directory)
├── skills/
│   └── skill-prefill-generation/
│       └── SKILL.md           # The single skill: system prompt for prefill generation
├── LICENSE                    # Apache-2.0
├── README.md                  # Human-readable overview
├── package.json               # npm manifest + cinatra extension registration
└── tsconfig.json              # Standalone strict TS config (no src/ currently)
```

## Directory Purposes

**`skills/`:**
- Purpose: Contains one subdirectory per skill registered in this bundle
- Contains: One directory per skill, each with a `SKILL.md` defining the skill's identity and system prompt
- Key files: `skills/skill-prefill-generation/SKILL.md`

**`skills/skill-prefill-generation/`:**
- Purpose: Implements the `skill.prefill-generation` capability
- Contains: A single `SKILL.md` with YAML frontmatter + natural-language system prompt
- Key files: `skills/skill-prefill-generation/SKILL.md`

**`.github/workflows/`:**
- Purpose: CI/CD automation for validation and marketplace release
- Contains: `ci.yml` (build gate), `release.yml` (publish trigger)
- Key files: `.github/workflows/ci.yml`, `.github/workflows/release.yml`

**`.planning/codebase/`:**
- Purpose: GSD codebase mapping output consumed by planning and execution agents
- Contains: ARCHITECTURE.md, STRUCTURE.md (and other focus documents as generated)
- Generated: Yes (by GSD mapper agents)
- Committed: Yes

## Key File Locations

**Entry Points:**
- `package.json`: Extension manifest — declares `cinatra.kind: skill`, registers capabilities map
- `skills/skill-prefill-generation/SKILL.md`: The skill's system prompt — this is the functional entry point at runtime

**Configuration:**
- `package.json`: Package identity, version, license, cinatra manifest block
- `tsconfig.json`: TypeScript compiler options (strict, ESNext, `src/` rootDir, `dist/` outDir)
- `.npmrc`: npm registry configuration (existence confirmed; contents not read)

**Core Logic:**
- `skills/skill-prefill-generation/SKILL.md`: The entire runtime behavior of this extension — a natural-language prompt

**CI/CD:**
- `.github/workflows/ci.yml`: Classify repo type, conditionally install/typecheck/test, pack dry-run, kind-gates
- `.github/workflows/release.yml`: Thin trigger delegating to `cinatra-ai/.github` reusable release workflow

## Naming Conventions

**Files:**
- Skill definition files: `SKILL.md` (uppercase, always at the root of each skill directory)
- Workflow files: `kebab-case.yml` (e.g., `ci.yml`, `release.yml`)
- Config files: standard names (`package.json`, `tsconfig.json`, `.npmrc`)

**Directories:**
- Skill directories: `kebab-case` prefixed with `skill-` (e.g., `skill-prefill-generation`)
- Top-level grouping: `skills/` (plural, lowercase)

**Capability Keys:**
- Format: `skill.<kebab-name>` (e.g., `skill.prefill-generation`)
- Maps to: directory name under `skills/` (e.g., `skill-prefill-generation`)

## Where to Add New Code

**New Skill:**
- Create: `skills/skill-<name>/SKILL.md` with YAML frontmatter (`name`, `description`) and system prompt body
- Register: Add entry to `package.json` → `cinatra.capabilities`: `"skill.<name>": "skill-<name>"`
- No other files required for a content-only skill

**New TypeScript Source (if needed in future):**
- Create `src/` directory at repo root
- Place all `.ts`/`.tsx` files under `src/`
- Add a `typecheck` script to `package.json` (`"typecheck": "tsc --noEmit"`) so CI picks it up
- Output goes to `dist/` per `tsconfig.json`

**Shared Utilities:**
- Not applicable currently — no `src/` directory exists and no shared code is needed for prompt-only skills

## Special Directories

**`dist/`:**
- Purpose: TypeScript compilation output (per `tsconfig.json` `outDir`)
- Generated: Yes (by `tsc`)
- Committed: No (not present; no `src/` to compile)

**`.planning/`:**
- Purpose: GSD planning and codebase mapping artifacts
- Generated: Yes (by GSD agents)
- Committed: Yes

---

*Structure analysis: 2026-06-09*
