# Grafo de ejecución (Leads)

Fuente de verdad del **qué corre ahora**. No es un runtime aparte: el runtime sigue siendo Task/subagent de Cursor. El grafo vive en `memory/processes/<proceso>/state.json` → `graph`.

Template del kit: `.cursor/skills/<proceso>/graph.yaml`.  
Al `start`, si `graph.nodes` está vacío o falta, copiá el YAML al state.  
Al `resume`, **no** recopies el template: seguí el grafo persistido.

Guía de lanzamiento: [lead-subagents.md](lead-subagents.md).  
Crítico: [critic.md](critic.md). Lecciones: [lessons.md](lessons.md).

## Modelo

```text
nodo  = una instancia de rol (surveyor, tdd-dev:WP-02, committer:web)
arista = "B no arranca hasta que A esté done | skipped"
writes = paths que ese nodo puede tocar
```

`graph` es la **única fuente**. `agents.<role>` no se edita a mano: se **reconstruye** después de cada cambio de nodos.

```text
roles = unique(node.role)
para cada role:
  statuses = nodos con ese role
  agents[role].status = running si alguno running
                      sino blocked si alguno blocked
                      sino done si alguno done (resto skipped)
                      sino skipped si todos skipped
                      sino idle
  agents[role].role = texto del roster
borrar agents[key] si key no está en roles
si queda un node.role sin agents[key] → inválido (falta reconstruir)
```

Si `agents` y `graph` no calzan: **gana el grafo**, reconstruí `agents`, no al revés.  
`current_agent` es compatibilidad. Con varios `running`, `null`. **No** orquestes desde ahí.

## Frontier (cada turno)

Un nodo está **ready** si:

1. `status` es `idle`
2. Todo predecesor (aristas `from → este`) está `done` o `skipped`
3. Ningún path de `writes[]` se solapa con un nodo `running` (prefijo o igualdad)

Lanzá **todos** los ready en el mismo turno:

- `kind: human` → el Lead encarna (conversación). Nunca dos human a la vez.
- `kind: gate` o `serial` → un Task (o encarnar). Varios serial ready sin overlap de writes → paralelo igual.
- `kind: parallel` → Task. Es el caso típico de N instancias.

`optional: true` e innecesario → `skipped` **antes** de calcular el frontier (si no, el sucesor espera para siempre).

Al volver: validar el reporte ([lead-subagents.md](lead-subagents.md) § Contrato).  
`done` solo si el JSON calza el schema y `paths_touched` ⊆ `writes[]` (o vacío).  
`retry` / Task caído / JSON inválido → dejar el nodo `idle` y relanzar **ese** id (máx. 1 retry). **No** escribás lección ni diary en `memory/`.  
`blocked` de un nodo no invalida hermanos `done`.

## Instanciar (planner / surveyor)

El template trae el esqueleto. Quien parte trabajo **agrega nodos y reescribe aristas** antes de marcarse `done`:

| Quién | Qué agrega | Qué rewirea |
|-------|------------|-------------|
| Build `planner` | `tdd-dev:<WP-id>` por paquete | Quita `planner → coverage-gate`. Poné `planner → cada WP raíz` y `WP → coverage-gate`. WPs con `depends_on` se encadenan entre sí. |
| Deploy `surveyor` | `committer:<repo_id>` por git de producto; `publisher:<app_id>` por target Vercel | Quita `surveyor → gate:git` si hay committers (`surveyor → committer:* → gate:git`). Quita `gate:git → gate:vercel` si hay publishers (`gate:git → publisher:* → gate:vercel`). Sin Vercel: `gate:vercel` = `skipped`. |
| Design `surveyor` | nada | Si no hay UI: `ui-designer` = `skipped`. |

Cada instancia: `role` del roster, `id` único, `writes[]` concretos.

## `writes[]` y colisión

- Vacío = no persistir; devolver texto al Lead.
- Solape: un path es prefijo del otro (`../web` y `../web/src`).
- Un solo `scribe` escribe el artefacto canónico del proceso.
- Home vs producto: nunca el mismo nodo; `committer:home` va **después** de `scribe`.

## Progreso

```text
progress_pct = floor(100 * (done + skipped) / count(nodes))
```

Usalo en outbox `status` / `complete`. No inventes porcentajes.

## Rewind (mismo proceso)

Si `critic` falla: los nodos responsables vuelven a `idle` o `running`; `critic` vuelve a `idle`. **No** agregues aristas hacia atrás.

Si el hueco es de un proceso **anterior**: outbox `blocked` + escribí una lección ([lessons.md](lessons.md)). No marques `complete`.

## Ciclos

Prohibido. El grafo del proceso es un DAG. Rewind cambia status, no aristas.
