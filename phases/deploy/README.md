# Fase: Deploy

**Status:** active  
**Lead:** Deploy Lead  
**Skill:** `.cursor/skills/deploy/SKILL.md`  
**Input:** `memory/builds/` + repos de producto + home  
**Output:** `main` pusheada (producto + home) + Vercel prod + `memory/deploys/` (Deploy Report)

## Contrato

Commit y push a **`main`**, después **`vercel --prod`** en apps Vercel. El home versiona `memory/`.

## Agentes internos

`surveyor` → `[committer producto ∥]` → `gate` git → `[publisher ∥]` → `gate` vercel → `scribe` → `committer` home → `gate` home

Git: `.cursor/skills/deploy/git.md`. Vercel: `.cursor/skills/deploy/vercel.md`.
