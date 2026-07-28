# Cinatra Skill Prefill Generation Skill

When a skill is added to the catalog, this bundle writes the short example prompt shown beside it in the UI — the one-tap "try saying…" hint that lets users immediately understand what kind of question or command activates it. Install it on any workspace where new skills are authored or re-indexed.

**Install:** Install `@cinatra-ai/skill-prefill-generation-skill` in your Cinatra instance. No credentials or external accounts are required — the bundle operates entirely within the Cinatra skill runtime.

**Usage:** You do not invoke this skill yourself. The prefill pipeline resolves it through the `skill.prefill-generation` capability, hands it a SKILL.md document, and takes back a single plain-text example prompt of at most 20 words with no quotes or markdown formatting. That output is the hint displayed beside the skill in the catalog.

**Configuration:** None.

**Development:** Clone the repository and run `node extension-kind-gate.mjs --package-root .` to validate the manifest. The bundle lives in `skills/skill-prefill-generation/`.

**Troubleshooting:** If catalog entries show no example prompt, the pipeline could not resolve the capability — confirm this package is installed and active in the workspace.

## Works with

- Cinatra workspace skill catalog

## Capabilities

- Pre-fill a clean one-line example prompt for every newly authored skill
- Backfill missing example prompts when re-indexing the skill catalog
- Keep the catalog's hints uniformly short — at most 20 words, no quotes or markdown
- Make skill discovery feel consistent across hand-authored and generated examples
