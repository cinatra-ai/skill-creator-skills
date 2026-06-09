# Coding Conventions

**Analysis Date:** 2026-06-09

## Repository Nature

This is a content-only Cinatra skill extension. It contains no TypeScript source files (no `src/` directory). The repository ships a single skill defined entirely in a YAML-frontmatter Markdown file (`skills/skill-prefill-generation/SKILL.md`). Conventions therefore apply to skill authoring patterns, package manifest structure, and CI configuration rather than application code.

## Naming Patterns

**Skill directories:**
- kebab-case directory names under `skills/`: `skills/skill-prefill-generation/`
- Directory name matches the `name:` field in the skill's SKILL.md frontmatter

**Skill files:**
- Each skill lives in its own subdirectory under `skills/`
- Skill definition file is always named `SKILL.md` (uppercase)

**Package naming:**
- npm scope: `@cinatra-ai/` prefix
- Package name: `@cinatra-ai/skill-creator-skills` — uses the bundle name, not a single skill name

**Capability keys:**
- Dot-namespaced lowercase: `skill.prefill-generation` (in `package.json` `cinatra.capabilities`)
- Capability value matches the skill directory name: `"skill.prefill-generation": "skill-prefill-generation"`

## SKILL.md Authoring

**Structure:**
```yaml
---
name: skill-prefill-generation
description: Internal system prompt for generating a short example user prompt (prefill text) for a skill, given its SKILL.md content.
---

[Skill system prompt body — plain prose, max ~20 words output directive for this skill]
```

**Rules (observed from `skills/skill-prefill-generation/SKILL.md`):**
- Frontmatter: `name` and `description` fields are required
- `name` must match the containing directory name
- `description` is a single sentence explaining the skill's internal purpose
- Body is a direct system prompt in plain prose — no markdown headers, no bullet lists
- Output constraints ("Return ONLY…", "max 20 words", "no quotes, no markdown") are stated explicitly in the body

## package.json Conventions

**Required top-level fields:**
- `name`: scoped npm package name (`@cinatra-ai/<bundle-name>`)
- `version`: semver
- `license`: `"Apache-2.0"`
- `description`: one-sentence summary
- `type`: `"module"` (ESM)

**`cinatra` manifest block:**
```json
{
  "cinatra": {
    "apiVersion": "cinatra.ai/v1",
    "kind": "skill",
    "dependencies": [],
    "capabilities": {
      "<dot.namespaced.key>": "<skill-directory-name>"
    }
  }
}
```

**Dependency rules (enforced by CI):**
- First-party `@cinatra-ai/*` packages MUST NOT appear in `dependencies`, `devDependencies`, or `optionalDependencies`
- First-party packages that are peers MUST be listed in `peerDependencies` AND marked `peerDependenciesMeta.<pkg>.optional: true`
- This repo currently declares zero dependencies of any kind

## TypeScript Configuration

`tsconfig.json` is present for future TypeScript sources. Key settings:
- `target`: `"ES2023"`
- `module`: `"ESNext"`, `moduleResolution`: `"bundler"`
- `strict`: `true`, `noImplicitAny`: `false`
- `verbatimModuleSyntax`: `true`
- `isolatedModules`: `true`
- `outDir`: `"dist"`, `rootDir`: `"src"`

No TypeScript source files currently exist. The config is a forward-compatibility scaffold.

## Code Style

**Formatting:**
- No Prettier or ESLint config files detected — not applicable for a content-only repo

**Linting:**
- Not detected

**Import Organization:**
- Not applicable (no source files)

## Error Handling

- Not applicable (no source files)
- CI enforces structural correctness via inline Node.js validation scripts in `.github/workflows/ci.yml`

## Logging

- Not applicable (no source files)

## Comments

**CI workflow files:**
- Block comments at the top of each workflow file explain the purpose and non-obvious branching logic (`.github/workflows/ci.yml`, `.github/workflows/release.yml`)
- Inline comments explain every conditional branch in the CI job steps
- Convention: explain the "why" (e.g., why a step is skipped for source mirrors) not just the "what"

## Package Manager

- `pnpm` via corepack (`corepack enable`, `corepack pnpm ...`)
- `.npmrc` sets `auto-install-peers=false`
- No lockfile committed (CI uses `--no-frozen-lockfile`)

---

*Convention analysis: 2026-06-09*
