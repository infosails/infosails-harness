# Memoria del proyecto

Resultado de procesos + arquitectura + builds. El chat no es memoria.

## Contenido

| Ruta | Qué / quién |
|------|-------------|
| `director-state.json` | Orquesta |
| `processes/<proceso>/` | Lead: state (incl. **grafo**) + inbox/outbox |
| `backlog.json` | Cola unificada |
| `blueprints/` | Historias (Discovery; Onboard escribe Done) |
| `bugs/` | Defectos (Bug) |
| `architecture/` | Landscape + ADRs (Design; Onboard seed) |
| `designs/` | Design Packages (Design → Build) |
| `builds/` | Build Reports (gates TDD/cov/mutación/e2e) |
| `onboard/` | Informes de hidratación (Onboard) |
| `deploys/` | Deploy Reports (producto + home en `main`) |
| `lessons/` | Calidad rechazada aguas arriba (Lead que bloquea) |
| `trackers/` | Proyección Linear (Orquesta; no es SoT) |
| `costs/` | Tokens por turno (hook Cursor; la Orquesta lee) |

## Arquitectura vs diseño vs build

```text
architecture/  ← mapa vivo del producto
designs/DS-…   ← cómo construir ESTA historia
builds/BR-…    ← evidencia de que se construyó con calidad
deploys/DR-…   ← SHA en main + URL Vercel prod
```
