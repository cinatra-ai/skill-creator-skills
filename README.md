# Skill Creator Skills

Meta-skills for authoring new Cinatra skills. When a skill is added to the catalog, this bundle generates the short example prompt shown beside it in the UI — the one-tap "try saying…" hint that lets users immediately understand what kind of question or command activates it. Install it on any workspace where new skills are authored or re-indexed.

To use, install this extension at the workspace level via the Cinatra marketplace. No credentials or external accounts are required — the bundle operates entirely within the Cinatra skill runtime. The prefill-generation skill reads a SKILL.md document and outputs a single plain-text example prompt of at most 20 words with no quotes or markdown formatting. That output is the hint displayed beside the skill in the catalog.

## Works with

- Cinatra workspace skill catalog

## Capabilities

- Pre-fill a clean one-line example prompt for every newly authored skill
- Backfill missing example prompts when re-indexing the skill catalog
- Keep the catalog's "try saying…" hints uniformly short — at most 20 words, no quotes or markdown
- Make skill discovery feel consistent across hand-authored and generated examples
