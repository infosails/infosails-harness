# Fase: Deploy

**Status:** active  
**Lead:** Deploy Lead  
**Skill:** `.cursor/skills/deploy/SKILL.md`  
**Input:** `memory/builds/` + repos de producto + home  
**Output:** `main` pusheada (producto + home) + tag semver + Vercel dev + Vercel prod + `memory/deploys/` (Deploy Report)

## Contrato

Antes de git y de Vercel, preguntar si es **dev y prod** o **solo prod**. Dev (`vercel deploy --yes` sin `--prod`, alias `-dev`) solo si eligieron ambos. **Prod** (tag `vX.Y.Z` y `vercel --prod`) solo con una autorización aparte. El home versiona `memory/` y no se taguea.

## Agentes internos

`surveyor` → ambientes → `[committer producto ∥]` → `gate` git → `[publisher:dev ∥]` → `gate` dev → autorización → `[tagger ∥]` → `gate` tag → `[publisher:prod ∥]` → `gate` prod → `scribe` → `committer` home → `gate` home

Git: `.cursor/skills/deploy/git.md`. Vercel: `.cursor/skills/deploy/vercel.md`.
