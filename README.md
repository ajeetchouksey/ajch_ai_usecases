# ajch_ai_usecases

This repository is the canonical content source for Aarya's enterprise AI use-case library — real-world architecture patterns (RAG, HITL approval gates, multi-agent research, structured extraction, etc.) shown as concrete, industry-specific implementations. The platform consumes this repository via a pinned Git SHA through `content-manifest.json` in the main app repo (`ajeetchouksey/ajch_platform`), so content is versioned and reviewable before it goes live — the same model already used for `ajch_aaryaai_blogs` and `ajch_skillup`.

## What this repo contains

- `content/usecases/index.json` — catalog index: verticals (industries), architectural patterns, and `featuredIds` — powers the `/usecases` platform page
- `content/usecases/_source-intel.json` — the source-of-truth intel file: full detail (tech stack, failure modes, integrations, Mermaid diagrams) for every featured use case
- `content/usecases/cases/*.json` — one file per published use case, each extending its `_source-intel.json` entry with `relatedUseCases` cross-links; this is what `/usecases/{id}` renders
- `scripts/validate-content.mjs` — schema validator (canonical copy, synced from `ajch_platform`) — includes a `validateUsecase()` branch checking the full case-JSON schema (id, title, vertical, patterns, failureModes, etc.), not just generic JSON validity
- `scripts/add-uc-diagrams.py`, `scripts/expand-uc-content.py`, `scripts/gen-uc-case-files.py` — one-off authoring tools used to seed/expand this content, ported here from `ajch_platform` since they only ever operated on this vertical's own files
- `.github/workflows/validate-content.yml` — automated schema validation on PR/push

## Publishing model

This vertical has a dedicated content-authoring pipeline, matching the pattern used by Blog (Content Lead → Tech Writer → Release Engineer): **Usecase Lead → Usecase Writer → AppSec Engineer (Security Gate) → Usecase Publisher**, defined in `.claude/agents/`. The shared pipeline contract lives in `.claude/skills/vertical-pipeline/SKILL.md`.

**Content files are never written directly** — any addition or edit to `content/usecases/` should go through Usecase Lead as the entry point; see `CLAUDE.md` at this repo's root. The one-off scripts under `scripts/` remain available for bulk/offline authoring, but routine case authoring should use the agent pipeline.

1. Ask Usecase Lead to draft one or more cases (it delegates research → Usecase Writer, validation → AppSec Engineer, and the actual file write + `index.json`/`_source-intel.json` update → Usecase Publisher).
2. Validate locally (below) — the agent pipeline runs this automatically as part of the Security Gate, but it's also safe to re-run by hand.
3. Open a PR, get it reviewed, merge to `main`.
4. From `ajch_platform`, promote the new SHA into `content-manifest.json` (via its `promote-content.yml` workflow dispatch, or `node scripts/sync-vertical-repo.mjs usecases ajeetchouksey/ajch_ai_usecases <sha>`).

Agent definitions in `.claude/agents/` and the pipeline skill in `.claude/skills/` are kept in sync with `ajch_platform`'s canonical copies via `node scripts/sync-vertical-agents.mjs usecases <path>`, run from `ajch_platform` — not edited by hand in this repo.

## Validation

CI runs `scripts/validate-content.mjs` against every changed file under `content/usecases/` on PR/push.

Local validation:

```bash
node scripts/validate-content.mjs \
  content/usecases/index.json \
  content/usecases/_source-intel.json \
  content/usecases/cases/*.json
```

This checks JSON validity plus the case schema (required fields, kebab-case `id`, minimum `failureModes` count) via `validateUsecase()`.

## File ownership and scope

- Use-case content lives in this repo only.
- `ajch_platform` tracks the published version through a manifest pin (`content-manifest.json`) — it never edits use-case content directly.
- Don't bypass validation before merge.

## Related references

- `ajch_platform`'s `platform_refactor.md` — the vertical-split initiative this repo is part of
- `ajch_platform`'s `content-manifest.json` — the promotion pin
- `ajch_skillup`, `ajch_aaryaai_blogs` — sibling vertical repos following the same pattern
- `.github/workflows/validate-content.yml`
- `scripts/validate-content.mjs`
