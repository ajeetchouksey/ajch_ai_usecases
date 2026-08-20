# ajch_ai_usecases

This repository is the canonical content source for Aarya's enterprise AI use-case library — real-world architecture patterns (RAG, HITL approval gates, multi-agent research, structured extraction, etc.) shown as concrete, industry-specific implementations. The platform consumes this repository via a pinned Git SHA through `content-manifest.json` in the main app repo (`ajeetchouksey/ajch_platform`), so content is versioned and reviewable before it goes live — the same model already used for `ajch_aaryaai_blogs` and `ajch_skillup`.

## What this repo contains

- `content/usecases/index.json` — catalog index: verticals (industries), architectural patterns, and `featuredIds` — powers the `/usecases` platform page
- `content/usecases/_source-intel.json` — the source-of-truth intel file: full detail (tech stack, failure modes, integrations, Mermaid diagrams) for every featured use case
- `content/usecases/cases/*.json` — one file per published use case, each extending its `_source-intel.json` entry with `relatedUseCases` cross-links; this is what `/usecases/{id}` renders
- `scripts/validate-content.mjs` — schema validator (canonical copy, synced from `ajch_platform`) — generic JSON-validity check; no usecases-specific schema branch exists yet
- `scripts/add-uc-diagrams.py`, `scripts/expand-uc-content.py`, `scripts/gen-uc-case-files.py` — one-off authoring tools used to seed/expand this content, ported here from `ajch_platform` since they only ever operated on this vertical's own files
- `.github/workflows/validate-content.yml` — automated schema validation on PR/push

## Publishing model

There is no dedicated content-authoring agent for this vertical yet — unlike Blog (Content Lead → Tech Writer → Release Engineer) or SkillUp (Curriculum Engineer → Assessment/Docs/Scenario Engineer), use cases are currently authored by hand or via the one-off scripts above. `ajch_platform`'s refactor plan (`platform_refactor.md`, Track B step 15) flags adapting the `content-lead` pattern into a lightweight use-case-writer agent as future work — not required for this repo to function as a vertical.

Until that exists:

1. Add or edit a case under `content/usecases/cases/`, and update `content/usecases/index.json`'s `verticals[].count` / `patterns[].count` / `featuredIds` and `content/usecases/_source-intel.json` as needed.
2. Validate locally (below).
3. Open a PR, get it reviewed, merge to `main`.
4. From `ajch_platform`, promote the new SHA into `content-manifest.json` (via its `promote-content.yml` workflow dispatch, or `node scripts/sync-vertical-repo.mjs usecases ajeetchouksey/ajch_ai_usecases <sha>`).

## Validation

CI runs `scripts/validate-content.mjs` against every changed file under `content/usecases/` on PR/push.

Local validation:

```bash
node scripts/validate-content.mjs \
  content/usecases/index.json \
  content/usecases/_source-intel.json \
  content/usecases/cases/*.json
```

This checks JSON validity. (Use-case files don't yet have a dedicated schema branch in the validator the way MCQ questions or blog posts do — file-reference/shape checks can be added here if this vertical grows a stricter schema later.)

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
