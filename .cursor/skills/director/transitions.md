# Pipeline y transiciones (Director)

El Director es dueño de `memory/director-state.json`. Ningún otro agente lo edita.

## Pipeline por tipo de artefacto

### Blueprint (historia)

```text
in_discovery → discovery_done → in_design → design_done → in_build → build_done → in_deploy → done
```

| Stage | next_process | Notas |
|-------|--------------|-------|
| `in_discovery` | — | Discovery running |
| `discovery_done` | `design` | Blueprint listo; Director ofrece Design |
| `in_design` | — | Design running |
| `design_done` | `build` | Director ofrece Build |
| `in_build` | — | Build running |
| `build_done` | `deploy` | Director ofrece Deploy |
| `in_deploy` | — | Deploy running |
| `done` | `null` | Cerrado |
| `blocked` | — | Requiere humano |

### Bug

```text
in_bug → bug_done → in_build → build_done → in_deploy → done
```

`bug_done` → `next_process: build` (salta Design salvo que el usuario pida lo contrario).

## Disponibilidad de procesos

Si `processes.<id>.availability === "planned"`:
- El Director **registra** que el artefacto espera ese proceso.
- **No** finge ejecutarlo.
- Informa al usuario y deja `session` en idle con focus en el artefacto.

**Build está `active`:** tras `design_done` (o `bug_done`) puede delegar al Build Lead.

**Design está `active`:** tras `discovery_done` puede delegar al Design Lead.

Cuando un proceso pase de `planned` → `active` en el kit, el Director podrá `delegate`.

## Eventos de history

| event | Cuándo |
|-------|--------|
| `activate` | Usuario elige / Director enciende un proceso |
| `complete` | El proceso termina y devuelve control |
| `delegate` | Director pasa el focus al siguiente proceso |
| `resume` | Continúa un `active_process` dejado a medias |
| `block` | Algo queda bloqueado |
| `idle` | Vuelve al menú sin focus |

## Sync con backlog

Tras `complete` de Discovery/Bug/Design/Build, el Director:

1. Lee el artefacto (`blueprints` | `bugs` | `designs` | `builds`)
2. Upsert en `artifacts`
3. Asegura entrada en `memory/backlog.json`
4. Propone la siguiente transición
