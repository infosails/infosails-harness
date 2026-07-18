# [CÓDIGO-ID] Feature Blueprint: [Nombre Corto de la Funcionalidad]

> **Epic/Contexto:** [Nombre del módulo o épica mayor]
> **Status:** Ready for Build
> **Creado por:** Discovery Lead

## 1. El Problema (Contexto de Usuario)
<!-- Qué duele, contexto, por qué ahora. 3–6 bullets densos. Sin vaguedades. -->
* 
* 
* 

## 2. Usuarios (investigación)
<!-- Obligatoria. Perfiles para Design/UI/Build. No “el usuario” genérico. -->

### Perfil primario
| Campo | Valor |
|-------|-------|
| Rol / persona | |
| Objetivos en esta historia | |
| Contexto de uso | |
| Frecuencia / criticidad | |
| Habilidad digital | novato \| intermedio \| experto |
| Dispositivo / canal | |
| Permisos | |
| Dolores UX actuales | |

### Perfiles secundarios (si aplica)
| Rol | Objetivo | Notas |
|-----|----------|-------|
| | | |

### No-usuarios (quién no usa esto)
* 

## 3. Tipo de aplicación
<!-- Investigar landscape/código o proponer + confirmar. -->

* **Tipo:** backoffice \| app producto web \| portal cliente \| marketing/landing \| móvil/PWA \| API/headless \| embedded \| job/CLI \| híbrido: …
* **Canal primario de esta historia:**
* **¿Nueva superficie o extensión?** nueva \| extensión de …
* **Justificación / evidencia:**
* **Alternativas consideradas:** (si se propuso)
* **Implicaciones** (densidad UI, auth, e2e, design system):

## 4. Inventario de existencia
<!-- Qué ya hay en el producto vs qué se construye. Evita que Build reimplemente. -->

### Ya existe (reutilizar — no reimplementar)
* 

### Parcial (existe; esta historia completa el delta)
* [delta]  <!-- qué falta exactamente -->

### Nuevo (no existe; construir en esta historia)
* 

## 5. Soluciones exteriores e integraciones
<!-- Si no hay exterior: "Ninguna — 100% nuestro". Si hay: un bloque por proveedor + matriz de tipos. -->

* **¿Hay solución exterior?** sí / no
* **Proveedor(es):** …

### Matriz de tipos (por proveedor: [nombre])
| Tipo | ¿Esta historia? | Detalle |
|------|-----------------|--------|
| API síncrona | SÍ / NO / N/A | |
| Webhooks | SÍ / NO / N/A | |
| Redirect / callback | SÍ / NO / N/A | |
| SDK / librería oficial | SÍ / NO / N/A | |
| UI hosted / embed | SÍ / NO / N/A | |
| Batch / archivos | SÍ / NO / N/A | |
| Eventos / cola / bus | SÍ / NO / N/A | |
| Email / SMS / push | SÍ / NO / N/A | |
| Datos compartidos / sync | SÍ / NO / N/A | |
| Auth entre sistemas (keys, OAuth client, mTLS) | SÍ / NO / N/A | |
| Consola admin vs in-app | SÍ / NO / N/A | |
| Entornos (sandbox/stg/prod) | SÍ / NO / N/A | |
| Datos y privacidad (PII) | SÍ / NO / N/A | |
| Fallos y degradación | SÍ / NO / N/A | |
| Límites / cuotas | SÍ / NO / N/A | |
| Operación (tenant, rotación secretos) | SÍ / NO / N/A | |

## 6. El Core (Alcance Técnico Requerido)
<!-- Solo NUEVO + delta de PARCIAL. Cada ítem con tag y observable. Incluir integraciones SÍ de §5. -->
* [ ] **[NUEVO]** 
* [ ] **[PARCIAL]**  <!-- falta: … | ya hay: … -->
* [ ] **[NUEVO]** 
* [ ] **[PARCIAL]** 
* [ ] **[NUEVO]** 

## 7. Límites y Exclusiones (Out of Scope / Guardrails)
<!-- Incluir NO reimplementar lo YA_EXISTE y NO asumir tipos de integración no elegidos. Preferir ≥5. -->
* **NO** reimplementar 
* **NO** asumir integración por 
* **NO** 
* **NO** 
* **NO** 

## 8. Criterios de Aceptación (Casos de Prueba Gherkin)
<!-- ≥3 escenarios: feliz, error, borde/alterno. Si hay exterior, incluir fallo/degradación del proveedor cuando aplique. Nombrar el rol del perfil en Dado que. -->

### Escenario 1: [Caso de éxito]
* **Dado que** [rol/perfil + estado inicial concreto]
* **Cuando** [acción o evento concreto]
* **Entonces** [resultado observable concreto]

### Escenario 2: [Caso de error]
* **Dado que** 
* **Cuando** 
* **Entonces** 

### Escenario 3: [Caso borde o alterno]
* **Dado que** 
* **Cuando** 
* **Entonces** 
