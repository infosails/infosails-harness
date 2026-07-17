# Fase: Design

**Status:** active  
**Lead:** Design Lead (arquitecto)  
**Skill:** `.cursor/skills/design/SKILL.md`  
**Input:** `memory/blueprints/` + `memory/architecture/` (incl. infra)  
**Output:** `memory/designs/` (+ ADRs / landscape / infra / as-built)

## Objetivo

Traducir cada Feature Blueprint en un **Design Package** (app + validación/propuesta de infra) que Build pueda ejecutar.

## Memoria de arquitectura del producto

| Artefacto | Ruta |
|-----------|------|
| Landscape (estado) | `memory/architecture/landscape.md` + `.json` |
| ADRs | `memory/architecture/adrs/` |

Design **siempre** lee esto primero (agente `surveyor`).

## Salida por historia

| Artefacto | Ruta |
|-----------|-------|
| Design Package | `memory/designs/DS-…md` + `.json` |

Incluye: reuso vs create, contratos, plan de construcción, paths, guardrails, mapeo a Gherkin.

## Agentes internos

`surveyor` → `architect` → `scribe` — ver `.cursor/skills/design/agents.md`
