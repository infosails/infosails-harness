# Fase: Discovery (Lead)

**Status:** active  
**Lead:** Discovery Lead  
**Skill:** `.cursor/skills/discovery/SKILL.md`  
**Output:** `memory/blueprints/`  
**Estado interno:** `memory/processes/discovery/state.json`

## Jerarquía

La Orquesta activa al **Discovery Lead**.  
El Lead activa `interviewer` (periodista iterativo) → `scribe` (blueprint detallado) y reporta outbox `complete`.

Guía de entrevista: `.cursor/skills/discovery/interview.md`.

## Agentes internos

Ver `.cursor/skills/discovery/agents.md`.
