# ajch_ai_usecases

This repo is a promoted content vertical — the canonical source for Aarya's enterprise AI use-case library. It is designed to be usable standalone, without `ajch_platform` checked out alongside it.

## Content changes go through the pipeline — never edit `content/` directly

Any addition or edit to `content/usecases/` (case files, `index.json`, `_source-intel.json`) **must** go through the agent pipeline defined in `.claude/agents/`:

**Usecase Lead → Usecase Writer → AppSec Engineer (Security Gate, pre- and post-build) → Usecase Publisher**

- If asked to add, edit, or fix a use case, invoke **Usecase Lead** — do not write to `content/usecases/` directly, even for a "quick" or "small" change.
- Usecase Lead never writes files itself; it orchestrates Usecase Writer (drafts, no file I/O), AppSec Engineer (hard gate, must `PASS ✓` before and after any write), and Usecase Publisher (the only role with `Write`/`Edit` access, scoped to `content/usecases/` only).
- The shared pipeline contract is documented once in `.claude/skills/vertical-pipeline/SKILL.md` — read it before authoring or modifying any of the four agent files.

This is backed by `.github/workflows/validate-content.yml`, which runs `scripts/validate-content.mjs` (including the use-case schema check) on every PR — but that's a post-hoc catch, not a substitute for routing through the pipeline in the first place.

## Agent files are synced from `ajch_platform`, not hand-edited here

`.claude/agents/{appsec-engineer,usecase-lead,usecase-writer,usecase-publisher}.md` and `.claude/skills/vertical-pipeline/SKILL.md` are canonical in `ajch_platform` and mechanically synced into this repo via `node scripts/sync-vertical-agents.mjs usecases <path-to-this-repo>` (run from `ajch_platform`). Don't hand-edit them here — changes will be overwritten on the next sync and this repo will drift from the canonical pipeline definition again.

## Scope

- Use-case content lives in this repo only — `ajch_platform` tracks it through a manifest pin (`content-manifest.json`) and never edits it directly.
- See `README.md` for the full publishing model and validation details.
