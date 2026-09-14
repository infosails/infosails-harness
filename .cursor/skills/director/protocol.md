# Protocolo Orquesta ↔ Lead de proceso

El **Director de Orquesta** no conoce agentes internos de ningún proceso.
Solo habla con el **Lead** de cada proceso (Discovery Lead, Bug Lead, …).

```text
Usuario
   ↕
Director de Orquesta          ← memory/director-state.json
   ↕  (briefs / reportes)
Lead del proceso               ← memory/processes/<proceso>/state.json
   ↕
Agentes internos del proceso   ← solo el Lead los conoce y orquesta
```

## Activación (Orquesta → Lead)

La Orquesta escribe el brief y cede el control al skill del Lead.

Archivo: `memory/processes/<proceso>/inbox-from-orchestra.json`

```json
{
  "version": 1,
  "at": "ISO-8601",
  "from": "orchestra-director",
  "to": "<proceso>-lead",
  "command": "start | resume | abort",
  "user_intent": "frase del usuario",
  "constraints": [],
  "focus_artifact_id": null,
  "focus_path": null
}
```

La Orquesta **no** lista agentes internos ni pasos internos.

## Reportes (Lead → Orquesta)

El Lead escribe el outbox. La Orquesta solo lee esto (no el state interno).

Archivo: `memory/processes/<proceso>/outbox-to-orchestra.json`

```json
{
  "version": 1,
  "at": "ISO-8601",
  "from": "<proceso>-lead",
  "to": "orchestra-director",
  "event": "status | complete | blocked | aborted",
  "process": "<proceso>",
  "summary": "una frase para el humano/orquesta",
  "progress_pct": 0,
  "artifact": {
    "type": "blueprint | bug | onboard | design | build_report | deploy_report | null",
    "id": null,
    "path": null
  },
  "next_hint": "design | build | null",
  "blocked_reason": null
}
```

### Eventos

| event | Significado | Qué hace la Orquesta |
|-------|-------------|----------------------|
| `status` | Avance intermedio (opcional) | Puede mostrar `summary` al usuario; no cambia de fase |
| `complete` | Proceso terminó OK | Cierra proceso, registra artefacto, propone siguiente Lead |
| `blocked` | No puede seguir | Marca blocked; pregunta al usuario |
| `aborted` | Cancelado | Vuelve a idle |

## Estado interno del proceso

`memory/processes/<proceso>/state.json` — **solo el Lead** lo lee/escribe.

La Orquesta **tiene prohibido**:
- Leer agentes internos
- Activar agentes internos
- Editar `state.json` del proceso

`memory/costs/` lo escribe el hook de Cursor (`stop`); la Orquesta solo lee.  
`memory/lessons/` lo escriben los Leads (calidad); la Orquesta no lo lee ni lo edita.

## Cadena de mando

1. Usuario ↔ Orquesta  
2. Orquesta ↔ Lead (brief / outbox)  
3. Lead ↔ agentes internos (**subagentes en paralelo** cuando aplique)

Si un agente interno “termina”, avisa al **Lead**, no a la Orquesta.
El Lead agrega y reporta hacia arriba.

## Subagentes (Leads)

Todo Lead **ejecuta el frontier** de su grafo (`state.graph` / `graph.yaml`): subagentes en paralelo cuando hay varios nodos ready.
Guía: [../_shared/lead-subagents.md](../_shared/lead-subagents.md) · [../_shared/graph.md](../_shared/graph.md).

La Orquesta **no** lista, lanza ni espera subagentes: solo lee el outbox del Lead.

Tras `complete`, la Orquesta registra el artefacto con `derived_from`: el `focus.artifact_id` previo si es distinto del nuevo id (BP→DS→BR→DR).

`memory/lessons/` lo escriben los Leads cuando bloquean por calidad aguas arriba. La Orquesta no lo edita.
