# InfoSails Agentic Harness (kit)

Kit instalable para **desarrollo agentico**. Este repo **no** guarda la memoria de tus productos.

## Cómo queda el harness

### 1. Kit vs proyecto

```text
┌─────────────────────────────────────┐
│  KIT (este repo, 1 vez en la máquina)│
│  agentes · templates · scripts      │
│  infosails-init                     │
└─────────────────┬───────────────────┘
                  │ instancia
                  ▼
┌─────────────────────────────────────┐
│  PROYECTO (repo del producto)       │
│  código + memory/ + .cursor/skills  │
└─────────────────────────────────────┘
```

```text
~/infosails-harness/              ← kit
~/Projects/mi-app/                ← proyecto
├── harness.project.yaml
├── memory/                       ← memoria del producto
└── .cursor/skills/               ← agentes (copia del kit)
```

### 2. Jerarquía de agentes

```text
                         ┌──────────┐
                         │  Usuario │
                         └────┬─────┘
                              │
                              ▼
                   ┌──────────────────────┐
                   │ Director de Orquesta │
                   │ habla SOLO con Leads │
                   └──────────┬───────────┘
                              │
           ┌──────────────────┼──────────────────┬──────────────────┐
           ▼                  ▼                  ▼                  ▼
   ┌───────────────┐  ┌───────────────┐  ┌───────────────┐  ┌───────────────┐
   │ Discovery Lead│  │   Bug Lead    │  │  Design Lead  │  │  Build Lead   │
   │               │  │               │  │  (arquitecto) │  │   (planned)   │
   └───────┬───────┘  └───────┬───────┘  └───────┬───────┘  └───────────────┘
           │                  │                  │
     interviewer/scribe  triager/scribe   surveyor → architect → scribe
```

También planned: **Build Lead**, **Deploy Lead**.

### 3. Pipeline (quién sigue a quién)

```text
Historia (blueprint)
  Discovery Lead ──► Design Lead ──► Build Lead ──► Deploy Lead
                         active         active         planned

Build: TDD · cobertura ≥85% · mutación · WPs en paralelo · e2e si aplica

Bug
  Bug Lead ────────────────────────► Build Lead ──► Deploy Lead
```

La Orquesta es quien **activa** y **delega** de un Lead al siguiente.

### 4. Dónde se escribe el status

```text
Orquesta                         Lead                         Agentes internos
────────                         ────                         ────────────────
director-state.json   ◄──outbox──  outbox-to-orchestra.json
                      ──inbox──►   state.json          ◄── status por agente
                                   (privado del Lead)
```

| Nivel | Archivo (en el **proyecto**) |
|-------|------------------------------|
| Pipeline global | `memory/director-state.json` |
| Lead → Orquesta | `memory/processes/<proceso>/outbox-to-orchestra.json` |
| Orquesta → Lead | `memory/processes/<proceso>/inbox-from-orchestra.json` |
| Agentes del proceso | `memory/processes/<proceso>/state.json` |

### 5. Memoria del proyecto

```text
memory/
├── director-state.json      ← Orquesta
├── backlog.json
├── blueprints/              ← historias (Discovery)
├── bugs/
├── architecture/            ← estado de arquitectura del producto
│   ├── landscape.md|json    ← stack, módulos, fronteras
│   └── adrs/                ← decisiones (ADR)
├── designs/                 ← Design Packages (input de Build)
├── builds/                  ← Build Reports (TDD / cov / mutación / e2e)
└── processes/
    ├── discovery|bug|design|build/
```

Futuro en la misma memoria: `adrs/`, `runs/`, …

### 6. Flujo de una historia (ejemplo)

```text
1. Usuario: "hola" / "nueva feature X"
2. Orquesta → menú / activa Discovery Lead (escribe inbox)
3. Discovery Lead → interviewer (clarifica) → scribe (escribe blueprint)
4. Lead escribe outbox event=complete + path del blueprint
5. Orquesta lee outbox → actualiza director-state → propone Design Lead
6. Design → Build (TDD, cov≥85%, mutación, e2e) → Deploy (cuando exista)
```

### 7. Probar en este mismo repo (playground)

Sin instalar ni crear otro proyecto:

```text
infosails-harness/           ← kit (Cursor abierto aquí)
└── playground/              ← proyecto simulado
    └── memory/              ← aquí se escriben las pruebas
```

```bash
./scripts/playground          # crear / refrescar
./scripts/playground --reset  # limpio
```

Luego en el chat: `hola` o `probar playground`.  
La Orquesta usa `playground/memory/`. Skills van por symlink al kit.

Para un producto real sigue siendo `infosails-init`.

---

## Instalar el kit (una vez)

```bash
cd /ruta/a/infosails-harness
./scripts/install
source ~/.zshrc   # o la rc que use tu shell
```

Queda el comando `infosails-init` y `INFOSAILS_HARNESS_HOME`.

## Partir un proyecto nuevo

```bash
infosails-init mi-app --path ~/Projects/mi-app
cd ~/Projects/mi-app
# Abrir esta carpeta en Cursor → escribir: hola
```

Adoptar un repo ya existente:

```bash
infosails-init . --into ~/Projects/app-existente
```

Eso crea en el proyecto: `memory/`, agentes en `.cursor/skills/`, `harness.project.yaml`, templates.

## Procesos activos hoy

| Proceso | Lead | Escribe |
|---------|------|---------|
| Discovery | Discovery Lead | `memory/blueprints/` |
| Bug | Bug Lead | `memory/bugs/` |
| Design | Design Lead | `memory/designs/` + `memory/architecture/` |
| Build | Build Lead | código + `memory/builds/` |
| Deploy | — | planned |
