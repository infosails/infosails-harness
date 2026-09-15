# Subagentes de Lead (todos los procesos)

Aplica a **Discovery Lead**, **Bug Lead**, **Design Lead**, **Build Lead**, **Onboard Lead**, **Deploy Lead** (y futuros Leads).

Los agentes expertos del roster **no** son solo roles que el Lead “encarna” en serie.  
El Lead los lanza como **subagentes** (Task / subagent de Cursor) según el **grafo de ejecución**.

La Orquesta **no** ve ni activa subagentes. Solo el Lead.

Grafo: [graph.md](graph.md) · Crítico: [critic.md](critic.md) · Lecciones: [lessons.md](lessons.md).

## Modelo

```text
Orquesta
   ↕  inbox / outbox
Lead del proceso
   ↕  graph.frontier → Task / subagent (0..N en paralelo)  o  encarnar (human)
Agentes expertos (nodos instanciados del roster)
```

| Quién | Qué hace |
|-------|----------|
| Lead | Lee lecciones, calcula frontier, lanza subagentes, sintetiza, sincroniza `graph` + `agents.*`, habla con el usuario, escribe outbox |
| Subagente | Ejecuta **un** nodo (`id` + `role`) con prompt acotado a `writes[]`; devuelve resultado al Lead |
| Orquesta | Solo Lead ↔ outbox; ignora internos |

## Cada turno

1. `start`: si `graph` falta o `nodes` vacío → copiar `.cursor/skills/<proceso>/graph.yaml`.
2. Leer `memory/lessons/` con `to_process` = este proceso ([lessons.md](lessons.md)).
3. Opcionales innecesarios → `skipped`.
4. Calcular **frontier** ([graph.md](graph.md)): predecesores `done`/`skipped`, sin colisión de `writes` con `running`.
5. Lanzar **todos** los ready (`human` → Lead encarna, uno a la vez; resto → Task en el mismo mensaje). Marcar `running`.
6. Al volver: **validar** el reporte. Solo entonces `done` / `blocked` / retry.
7. Reconstruir `agents` desde `graph` ([graph.md](graph.md)). `current_agent`: `null` si hay más de un `running`.
8. `progress_pct` = `(done + skipped) / nodes`.
9. `complete` **solo** si `critic` está `done` y `scribe` (si aplica) está `done`.
10. Solo el Lead escribe `outbox-to-orchestra.json`.

**Fallback:** sin herramienta de subagente, el Lead encarna el frontier **en serie** (mismo grafo, un nodo por vez).

## Contrato del subagente

El Task **no** se memoriza en `memory/`. Se endurece aquí. Schema: `schemas/subagent-report.schema.json`.

Sacar `Escribe solo` de `node.writes` (vacío → “devolver texto al Lead sin persistir”).

```text
Eres el agente interno "<id>" (rol <role>) del proceso <discovery|bug|design|build|onboard|deploy>.
Skill/guía: <path>
PROJECT_ROOT: <path>
Lee: <lista>
Escribe solo: <node.writes o "devolver texto al Lead sin persistir">
Objetivo: <una frase>
Criterio de done: <checklist corta>
Al terminar devuelve JSON version=1 node_id role status summary
  + paths_touched + blockers
  + si role=tdd-dev: tdd.{test_files, prod_files, red_first, business_asserts}
  + si role=critic: critic.{verdict, checklist, gap, upstream_process}
No hables con la Orquesta. No edites director-state.json ni outbox.
No relances otros nodos. No toques paths fuera de Escribe solo.
```

### Validar (obligatorio)

1. Parsear el JSON (si viene envuelto en prosa, extraer el objeto).
2. Campos required del schema. `paths_touched` ⊆ `node.writes` (si writes no vacío).
3. `tdd-dev`: `tdd.test_files` no vacío y en el **diff** del WP ([critic.md](critic.md) § Por WP).
4. `critic`: `critic.verdict` presente; `pass` exige todos los `ok: true`.

Si el reporte es inválido o el Task falló: nodo → `idle`, **un** retry. Segundo fallo → `blocked` de ese nodo (pedir al usuario o encarnar). **No** crear `LSN-…`.

`human`: el Lead no fabrica el JSON; aplica el mismo criterio de done del roster.

## Cuándo paralelo / serie

El grafo lo decide. No uses un flag `parallel: true` como plan: el paralelo es “más de un nodo en el frontier”.

Excepciones fijas:

- Un solo `human` a la vez (interviewer, triager, confirmaciones).
- Un solo `scribe` del artefacto canónico.
- `critic` antes de `complete` (y antes de `scribe`, salvo Deploy).

Ejemplos (van en cada `graph.yaml`):

| Proceso | Frontier típico |
|---------|-----------------|
| Discovery | `interviewer` (human) ∥ `user-scout` ∥ `inventory-scout` → `critic` → `scribe` |
| Bug | `triager` (human) ∥ `repro-scout` → `critic` → `scribe` |
| Design | `surveyor` → `architect` ∥ `ui-designer` → `critic` → `scribe` |
| Build | `surveyor` → `planner` → `tdd-dev:*` (según `depends_on`/`writes`) → `coverage-gate` → `sast` ∥ `mutation` ∥ `e2e` ∥ `complexity-gate` → `integrator` → `critic` → `scribe` |
| Onboard | `code-surveyor` ∥ `capability-scout` ∥ `docs-scout` → `synthesizer` → `critic` → `scribe` |
| Deploy | `surveyor` → `committer:<repo>` ∥ … → `gate:git` → `publisher:<app>` ∥ … → `gate:vercel` → `critic` → `scribe` → `committer:home` → `gate:home` |

## Estado de nodo / rol

| status | Significado |
|--------|-------------|
| `idle` | No arrancado; puede entrar al frontier |
| `running` | Subagente o Lead en ese nodo |
| `done` | Entregable aceptado por el Lead |
| `blocked` | Falta input / secreto / decisión / calidad |
| `skipped` | No aplica (ej. ui sin pantallas; scout no lanzado) |

## Reglas duras

1. Ejecutar el **frontier completo** — no serializar por costumbre.
2. Subagentes **reportan al Lead**, nunca a la Orquesta.
3. Un solo dueño del artefacto final (`scribe`).
4. Colisión de `writes[]` → no están ready a la vez.
5. Confirmaciones humanas: un `human`, no N subagentes.
6. Nodo `done` no se relanza (resume / tokens).
7. `agents` se **reconstruye** desde `graph`; si no calzan, gana el grafo.
8. Fallo de Task / reporte inválido → retry del nodo, nunca `memory/lessons/`.
9. Playground / kit: misma regla; documentar si un Task no pudo correr.

## Referencias por proceso

| Lead | Roster | Grafo |
|------|--------|-------|
| Discovery | [discovery/agents.md](../discovery/agents.md) | [discovery/graph.yaml](../discovery/graph.yaml) |
| Bug | [bug/agents.md](../bug/agents.md) | [bug/graph.yaml](../bug/graph.yaml) |
| Design | [design/agents.md](../design/agents.md) | [design/graph.yaml](../design/graph.yaml) |
| Build | [build/agents.md](../build/agents.md) | [build/graph.yaml](../build/graph.yaml) |
| Onboard | [onboard/agents.md](../onboard/agents.md) | [onboard/graph.yaml](../onboard/graph.yaml) |
| Deploy | [deploy/agents.md](../deploy/agents.md) | [deploy/graph.yaml](../deploy/graph.yaml) |
