# Architecture Landscape

> **Producto:** {{PROJECT_NAME}}
> **Actualizado:** {{CREATED_AT}}
> **Mantenedor:** Design Lead
> **Nubes org:** Vercel + GCP (suites completas; elegir por necesidad, no por capa)

## 1. Resumen
<!-- Una frase: qué es el sistema. -->


## 2. Stack actual
| Capa | Tecnología | Notas |
|------|------------|-------|
| Frontend | — | UI: kit a elegir (shadcn, MUI, InfoSails, custom, none) |
| Backend | — | Puede vivir en Vercel y/o GCP |
| Datos | — | Según servicio elegido (Vercel o GCP) |
| Design system | — (sin default) | Spec: CSF / Storybook / docs — completar en Design |
| Infra / deploy | Vercel + GCP | Ver §3 |

El **design system lo elige el producto** (Design pregunta si está vacío). Una vez documentado, las historias siguientes heredan. Cambiar de kit → ADR.


## 3. Infraestructura de trabajo
<!-- Nubes permitidas: Vercel y GCP con toda su suite. Sin repartir front/back por nube. -->

### Nubes
| Nube | Alcance | Estado |
|------|---------|--------|
| **Vercel** | Suite completa (app, functions, storage, DB, AI, cron, etc. según producto) | active |
| **GCP** | Suite completa (compute, datos, storage, colas, secrets, observabilidad, etc.) | active |

Design elige **Vercel, GCP o ambos** según el problema — no por “esto es front” o “esto es back”.

### Repositorios (topología)
<!-- Design Lead define cuántos repos hay, monorepo vs multi-repo, y los crea/scaffold si faltan. -->
| Repo ID | Nombre / URL | Rol | Tipo | Estado |
|---------|--------------|-----|------|--------|
| | | app \| api \| packages \| infra \| docs \| other | mono \| multi | planned \| active |

* Estrategia actual: monorepo | multi-repo | híbrido
* Quién crea repos nuevos: **Design Lead** (confirmar con humano si es org remota)
* Build **no** inventa repos: solo trabaja en los listados aquí / en el Design Package

### Recursos (ejemplos; completar por proyecto)
| Recurso | Proveedor (Vercel \| GCP \| ambos) | Entornos | Estado | Notas |
|---------|------------------------------------|----------|--------|-------|
| App / hosting | | | planned \| active | |
| Compute / functions | | | | |
| Base de datos | | | | |
| Cache / cola | | | | |
| Object storage | | | | |
| CI/CD | | | | |
| Secrets / config | | | | |
| Observabilidad | | | | |
| Dominio / CDN | | | | |
| Otros | | | | |

### Restricciones de infra
* **Nubes permitidas:** solo **Vercel** y **GCP** (toda la suite de cada una).
* **NO** introducir AWS, Azure u otro cloud sin ADR Accepted de excepción.
* **NO** forzar front→Vercel / back→GCP: decidir por capacidad, costo, latencia, team.
* **Arquitectura interna:** hexagonal; **microservicios solo si es absolutamente necesario** (ADR).
* Regiones / compliance:
* Lo que NO se puede provisionar sin aprobación:

## 3b. Estilo de arquitectura (directiva org)
<!-- Design Lead: hexagonal siempre; microservicios solo si es absolutamente necesario. -->
* Estilo interno: **hexagonal** (puertos y adaptadores)
* Default deployable: **modular monolith** (o pocos artefactos)
* Microservicios: **solo con ADR** y justificación de necesidad absoluta
* Ver: `.cursor/skills/design/architecture-principles.md`

## 4. Módulos / bounded contexts
| ID | Nombre | Responsabilidad | Repo/ruta | Estado |
|----|--------|-----------------|-----------|--------|
| | | | | planned \| active \| legacy |

## 5. Fronteras e integraciones
* Internas:
* Externas (APIs, colas, terceros):

## 5b. Diagramas (Mermaid)
<!-- Architect: mantener actualizados. Guía: .cursor/skills/design/diagrams.md -->

### Componentes del sistema
```mermaid
flowchart TB
  %% módulos, adapters, externos, nubes
```

### Secuencia — flujo crítico
```mermaid
sequenceDiagram
  %% actor → adapters → application → domain → externos
  %% Si aún no hay flujo: dejar nota "pendiente primera historia"
```

## 6. Datos y ownership
| Dato / agregado | Dueño (módulo) | Store (Vercel y/o GCP) |
|-----------------|----------------|------------------------|
| | | |

## 7. Cross-cutting
* Auth / tenancy:
* Observabilidad:
* Config / feature flags:

## 8. Restricciones vigentes
* Nubes = Vercel + GCP, suites completas (ver §3)
* Hexagonal + anti-microservicio por defecto (ver §3b)
* 

## 9. Huecos conocidos
* 

## 10. Capacidades construidas (as-built)
| Capacidad / componente | Módulo | Estado | Notas / última historia |
|------------------------|--------|--------|-------------------------|
| | | active \| partial \| deprecated | BP-… / DS-… |

## 11. Historial de cambios de arquitectura
* 
