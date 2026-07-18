# Fase: Discovery (Lead)

**Status:** active  
**Lead:** Discovery Lead  
**Skill:** `.cursor/skills/discovery/SKILL.md`  
**Output:** `memory/blueprints/`  
**Estado interno:** `memory/processes/discovery/state.json`

## Jerarquía

La Orquesta activa al **Discovery Lead**.  
El Lead activa `interviewer` / scouts / `scribe` como **subagentes** cuando aplique (paralelo si independientes) y reporta outbox `complete`.

Guía de entrevista: `.cursor/skills/discovery/interview.md`  
(incluye **usuarios**, **tipo de aplicación**, existencia, integraciones exteriores).  
Subagentes: `.cursor/skills/_shared/lead-subagents.md`.

## Agentes internos

Ver `.cursor/skills/discovery/agents.md`.
