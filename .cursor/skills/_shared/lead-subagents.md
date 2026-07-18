# Subagentes de Lead (todos los procesos)

Aplica a **Discovery Lead**, **Bug Lead**, **Design Lead**, **Build Lead** (y futuros Leads).

Los agentes expertos del roster **no** son solo roles que el Lead “encarna” en serie.  
El Lead **puede y debe** lanzarlos como **subagentes** (Task / subagent de Cursor) y **en paralelo** cuando no haya dependencia ni conflicto de escritura.

La Orquesta **no** ve ni activa subagentes. Solo el Lead.

## Modelo

```text
Orquesta
   ↕  inbox / outbox
Lead del proceso
   ↕  Task / subagent (1..N en paralelo)
Agentes expertos (roster del proceso)
```

| Quién | Qué hace |
|-------|----------|
| Lead | Planifica, lanza subagentes, sintetiza, actualiza `state.agents.*`, habla con el usuario, escribe outbox |
| Subagente | Ejecuta **un** rol del roster con prompt acotado; devuelve resultado al Lead |
| Orquesta | Solo Lead ↔ outbox; ignora internos |

## Cuándo paralelo

Lanza **varios** subagentes en el **mismo turno** si:

1. No dependen del output del otro, **o**
2. El grafo del plan marca `parallel: true`, **y**
3. No escriben el mismo archivo / mismo artefacto a la vez.

Ejemplos:

| Proceso | Paralelo típico |
|---------|-----------------|
| Discovery | Investigación de usuarios + tipo de app; scouts de inventario ∥ preparación; **no** dos interviewers con el usuario a la vez |
| Bug | Recolección de contexto en repo ∥ borrador de guardrails si el síntoma ya está claro |
| Design | Tras surveyor: `architect` ∥ `ui-designer` (si el BP ya define pantallas) |
| Build | Varios `tdd-dev` (WPs); luego `sast` ∥ `mutation` ∥ `e2e` |

## Cuándo serie / encarnar

- Conversación con el usuario (interviewer, triager, confirmaciones).
- Gates que necesitan el resultado anterior (coverage tras todos los WPs; scribe al final).
- Un solo escritor canónico del artefacto final (`scribe`).

**Fallback:** si no hay herramienta de subagente disponible, el Lead **encarna** el rol en serie — mismo contrato de estado y outbox.

## Cómo lanzar un subagente

1. Marcar en `memory/processes/<proceso>/state.json`:
   - `current_agent` / `agents.<id>.status` = `running`
   - `note` breve (ej. “subagent WP-02”)
2. Lanzar Task/subagent con prompt que incluya:
   - **Rol exacto** del roster (`architect`, `ui-designer`, `tdd-dev`, …)
   - Ruta al skill / doc del agente (`ui-designer.md`, `interview.md`, `standards.md`, …)
   - `PROJECT_ROOT` y paths de memoria a leer
   - **Entregable** concreto (qué devolver al Lead; qué archivos puede tocar)
   - **Prohibiciones**: no editar `director-state.json`; no escribir outbox a Orquesta; no inventar fuera del brief
3. Si hay N independientes → **N llamadas en paralelo** en el mismo mensaje.
4. Al volver: sintetizar, marcar `done` | `blocked`, resolver conflictos, seguir el grafo.
5. Solo el Lead escribe `outbox-to-orchestra.json`.

### Plantilla de prompt (subagente)

```text
Eres el agente interno "<id>" del proceso <discovery|bug|design|build>.
Skill/guía: <path>
PROJECT_ROOT: <path>
Lee: <lista>
Escribe solo: <lista o "devolver texto al Lead sin persistir">
Objetivo: <una frase>
Criterio de done: <checklist corta>
No hables con la Orquesta. No edites director-state.json.
Al terminar: resume hallazgos + paths tocados + blockers.
```

## Estado

Actualizar siempre `state.agents.*` aunque el trabajo lo haga un subagente:

| status | Significado |
|--------|-------------|
| `idle` | No arrancado |
| `running` | Subagente o Lead en ese rol |
| `done` | Entregable aceptado por el Lead |
| `blocked` | Falta input / secreto / decisión |
| `skipped` | No aplica (ej. ui sin pantallas) |

Varias instancias del mismo rol (ej. `tdd-dev`): usar `note` o claves `tdd-dev:WP-01` en `working`.

## Reglas duras

1. **Maximizar paralelismo** entre expertos independientes — no serializar por costumbre.
2. Subagentes **reportan al Lead**, nunca a la Orquesta.
3. Un solo dueño del artefacto final por proceso (`scribe` del Lead).
4. No dos writers en el mismo path sin coordinación.
5. Confirmaciones humanas: las hace el **Lead** (o el agente conversacional), no N subagentes a la vez.
6. Playground / kit: misma regla; documentar si un subagente no pudo correr.

## Referencias por proceso

| Lead | Roster |
|------|--------|
| Discovery | [discovery/agents.md](../discovery/agents.md) |
| Bug | [bug/agents.md](../bug/agents.md) |
| Design | [design/agents.md](../design/agents.md) |
| Build | [build/agents.md](../build/agents.md) |
