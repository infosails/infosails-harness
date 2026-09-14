# Crítico de calidad (antes de `complete`)

Nodo serial `critic` en el grafo de cada proceso. **No** hay outbox `complete` si `critic` no está `done`.

No habla con la Orquesta. No escribe el artefacto canónico (`scribe` sí). Devuelve al Lead: `pass` | `fail` + checklist.

Skill de orquestación: [graph.md](graph.md) · [lead-subagents.md](lead-subagents.md).

## Dónde vive

Inmediatamente **antes** de `scribe`, salvo Deploy (`critic` revisa SHA/URL tras `gate:vercel`, luego `scribe`).

`kind: gate`. `writes: []` (salvo lección: la escribe el **Lead**, no el subagente critic).

## Prompt

```text
Eres el agente interno "critic" del proceso <proceso>.
Skill/guía: .cursor/skills/_shared/critic.md (sección de ese proceso)
PROJECT_ROOT: <path>
Lee: <artefacto borrador o paths>
Escribe solo: devolver texto al Lead (no persistir)
Objetivo: puntuar el entregable contra el checklist. pass o fail.
Criterio de done: JSON critic según schemas/subagent-report.schema.json ($defs.criticEvidence).
No hables con la Orquesta. No edites director-state.json ni outbox.
```

El Lead **no** marca `critic` `done` si falta `critic.verdict` o algún ítem del checklist. `verdict: fail` → rewind o lección; nunca `complete`.

## Si fail (mismo proceso)

Lead: rewind al nodo que puede corregir (interviewer, triager, architect, planner/tdd-dev, synthesizer). `critic` → `idle`. No `complete`.

## Si fail (proceso anterior)

Lead: outbox `blocked` con razón concreta + lección a `to_process` anterior ([lessons.md](lessons.md)). Ejemplos: Design sin perfiles de usuario → Discovery; Build sin paths/contratos → Design.

## Discovery

Checklist de cierre de [../discovery/interview.md](../discovery/interview.md). Mínimo:

- [ ] ≥1 perfil primario (rol, contexto, dispositivo, habilidad)
- [ ] Tipo de aplicación tipado o propuesto+confirmado
- [ ] Inventario `YA_EXISTE` / `PARCIAL` / `NUEVO` en capacidades relevantes
- [ ] Core atómico; nada `YA_EXISTE` colado como trabajo nuevo
- [ ] ≥3 Gherkin concretos (feliz + error + borde); Dado que nombra el rol
- [ ] Usuario confirmó el borrador
- [ ] Sin `TBD` / `DESCONOCIDO` crítico sin plan

## Bug

- [ ] Síntoma, repro, esperado vs actual
- [ ] Severidad
- [ ] ≥3 guardrails `NO`
- [ ] Criterio de cierre Gherkin
- [ ] No es una feature nueva disfrazada

## Design

Leer BP + landscape + principios ([../design/architecture-principles.md](../design/architecture-principles.md)):

- [ ] BP tiene perfiles y tipo de app (si no → fail hacia Discovery, no inventar)
- [ ] Hexagonal; microservicio solo con ADR
- [ ] Nubes solo Vercel/GCP salvo ADR
- [ ] Mermaid secuencia + componentes
- [ ] UI: mapa a `@infosails/design-system` o `ui-designer` skipped con razón
- [ ] Paths y contratos suficientes para Build
- [ ] Cada escenario Gherkin del BP es **mapeable** (Dado/Cuando/Entonces con datos; no “el sistema funciona”)
- [ ] Guardrails alineados al inventario del BP

## Build

Hay **dos** críticos. Coverage no memoriza calidad; la memoria es el **test que mata mutantes**.

### Por WP (al aceptar `tdd-dev:<id>`)

No es un nodo extra: es el criterio de `done` del nodo. El Lead mira el **diff** de `writes[]` (no el % de cobertura):

```bash
git diff --stat -- <paths del WP>
git diff --name-only -- <paths del WP>
```

- [ ] En el **mismo** cambio hay archivos de test y de producción (`tdd.test_files` no vacío)
- [ ] `red_first: true` (o el Lead vio el test fallar antes del green)
- [ ] `business_asserts` nombra comportamiento de Gherkin/Core, no “la función existe”
- [ ] Nada de `writes[]` fuera del WP

Sin eso el nodo **no** pasa a `done` (retry o rewind). No se escribe lección por un WP flojo: se corrige el nodo.

### Del proceso (nodo `critic`, tras integrator)

Gates de [../build/standards.md](../build/standards.md) **más** relectura de todos los WP:

- [ ] Cada `tdd-dev:*` `done` tiene evidencia `tdd` en el reporte
- [ ] Mutación: los asserts de negocio matan mutantes (score ≥ umbral); no solo line coverage ≥ 85%
- [ ] Complejidad: ninguna función nueva/tocada con CCN > 10
- [ ] SAST sin ERROR/HIGH/CRITICAL in-scope
- [ ] Tabla Gherkin → Playwright PASS o skip justificado
- [ ] No PASS inventado; no secretos en el árbol

Si el DS es impracticable (sin paths, sin contratos, UI sin mapa, Gherkin inejecutable) → fail hacia Design o Discovery + lección.

## Onboard

- [ ] Landscape con evidencia (stack, repos, módulos, as-built)
- [ ] BPs Done solo con rastro en código/docs; Core `YA_EXISTE`
- [ ] No Design Packages ni Build Reports inventados
- [ ] No issues Linear convertidos en BP Done
- [ ] Informe ONBOARD lista qué se creó y qué quedó gap

## Deploy

- [ ] SHA de producto en `origin/main` (o skip documentado)
- [ ] URL Vercel prod o skip documentado
- [ ] Sin `--force`; sin secretos en el DR
- [ ] Home **después** del DR; playground-kit no pusheado
