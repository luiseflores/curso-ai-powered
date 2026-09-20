# Generador del Mapa de Funcionalidades de Negocio

> **Orden de generación: 4º de 5.** Generá este documento después de `data.md`, `glossary.md` y
> `personas.md`. Aprovechá que `business-data-map.md` ya existe para verificar CRUD, y que
> `domain-glossary.md`/`business-personas-map.md` ya existen para nombrar entidades y roles de
> forma consistente en vez de re-derivarlos. Ver `README.md` de esta carpeta para el orden
> completo.

Genera o actualiza `.context/business/business-feature-map.md` — un inventario completo de cada
funcionalidad del sistema.

**Objetivo**: analizá el producto completo (o indicame una ruta/módulo puntual si querés acotar el
análisis).

---

## Qué produce esto

Un único documento que cataloga **cada funcionalidad** del sistema con:
- Identificación de la funcionalidad, estado y madurez
- Matriz CRUD por entidad
- Inventario de endpoints de API agrupados por dominio
- Inventario de componentes de UI (formularios, vistas, acciones)
- Integraciones de terceros y feature flags
- Matriz de cobertura de pruebas por funcionalidad y evaluación de riesgo
- Discovery gaps (funcionalidades planeadas, en curso, o sin documentar)

Este es el complemento **centrado en capacidades** de `business-data-map.md` (centrado en datos).
Juntos dan una comprensión completa de qué hace el sistema y cómo.

---

## Fuentes (usá TODAS las disponibles)

Agotá cada fuente. No te bases solo en código — cruzá referencias con DB, API y docs existentes.

| Fuente | Qué extraer | Herramienta |
|--------|----------------|------|
| Rutas / endpoints de API | Funcionalidades expuestas vía HTTP — cada endpoint es una capacidad | Leé archivos de rutas, o el spec OpenAPI si existe |
| Rutas y páginas de frontend | Funcionalidades cara al usuario — cada página/formulario es una operación de usuario | Leé el repositorio de frontend — enfocate en rutas, páginas, formularios, modales, dashboards |
| Esquema de base de datos | Entidades que respaldan funcionalidades — capacidades CRUD por entidad | DBHub MCP (o tu cliente de DB) — queries de solo lectura para estructura de tablas y relaciones |
| Servicios backend | Lógica de negocio, validación, procesamiento | Leé el repositorio de backend — enfocate en servicios, controllers, handlers |
| Dependencias del package | Integraciones de terceros (pagos, email, auth, analytics) | Leé `package.json`, `requirements.txt`, `Gemfile`, etc. |
| Feature flags / variables de entorno | Funcionalidades deshabilitadas o experimentales | Buscá `FEATURE_`, `isEnabled`, `feature.*flag` en el código y en `.env.example` |
| Contexto existente | `business-data-map.md`, `domain-glossary.md`, `business-personas-map.md`, PRD, SRS | Leé directo |
| Historial de git (reciente) | Funcionalidades agregadas o cambiadas recientemente | `git log --oneline -30` para patrones de actividad |

**Regla de oro**: una funcionalidad es cualquier **capacidad que el sistema ofrece** — endpoints de
API, acciones de UI, procesos en background, integraciones. Si un usuario o el sistema puede
HACERLO, es una funcionalidad.

---

## Detección de modo

```
¿Existe .context/business/business-feature-map.md?
  → NO:  generalo desde cero
  → SÍ:  generá una nueva versión, mostrame el resumen del diff, y
         esperá mi confirmación antes de sobreescribir. NUNCA
         sobreescribas automáticamente.
```

---

## Si falta `business-data-map.md`

Este documento produce valor igual sin él — avisame ("la matriz CRUD y las entidades van a
depender solo del escaneo directo de DB/código, sin la narrativa de negocio detrás"), seguí
adelante, y registrá la limitación en §Discovery Gaps. Pero si podés, generá primero `data.md`
(ver `README.md` de esta carpeta).

---

## Fases de descubrimiento

### Fase 1 — Descubrimiento de funcionalidades vía API

Para cada ruta/endpoint de API encontrado:
- Método HTTP + path + propósito
- Requisito de auth (público, autenticado, admin)
- Forma de request/response (breve — no vuelques el schema completo)
- A qué entidad/dominio pertenece

Agrupá endpoints por dominio (usuarios, productos, órdenes, pagos, etc.).

### Fase 2 — Descubrimiento de funcionalidades vía UI

Para cada página, formulario, modal, dashboard encontrado:
- ¿Qué acción de usuario habilita?
- ¿Qué datos muestra o recolecta?
- ¿Qué endpoints de API llama?
- ¿Qué roles de usuario pueden acceder? (citá las personas de `business-personas-map.md` en vez de
  nombrar roles sueltos)

Buscá: componentes de formulario, componentes de modal/dialog, widgets de dashboard, vistas de
tabla/listado, botones de acción.

### Fase 3 — Descubrimiento de integraciones

Para cada servicio de terceros encontrado en las dependencias:
- ¿Qué funcionalidad habilita? (pagos, email, auth, storage, monitoreo)
- ¿Qué endpoints/componentes de UI lo usan?
- ¿Está activo, deshabilitado, o planeado?

### Fase 4 — Feature flags y funcionalidades en curso

Buscá:
- Variables de entorno con prefijos `FEATURE_`, `ENABLE_`, `BETA_`
- Comentarios de código con `TODO`, `FIXME`, `WIP`, `HACK`
- Handlers de ruta vacíos o stub (funcionalidades planeadas)
- Feature flags deshabilitados

### Fase 5 — Referencias cruzadas con business-data-map

Si `.context/business/business-data-map.md` existe:
- Verificá que cada entidad del data-map tenga funcionalidades CRUD correspondientes
- Verificá que cada flujo de negocio mapee a al menos una funcionalidad
- Marcá entidades sin funcionalidades (¿datos huérfanos?)
- Marcá funcionalidades sin entidades (¿falta persistencia?)

---

## Estructura de la salida

Escribí `.context/business/business-feature-map.md` con:

### 1. Resumen de inventario

```markdown
| Categoría  | Funcionalidades | Estado         |
|------------|-----------------|----------------|
| Core       | [cantidad]      | Estable        |
| Secundaria | [cantidad]      | Estable        |
| Beta       | [cantidad]      | En prueba      |
| Planeada   | [cantidad]      | En desarrollo  |
```

### 2. Catálogo de funcionalidades (por dominio)

Una sección por dominio. Cada funcionalidad:

```markdown
#### Funcionalidad: [Nombre]

| Aspecto           | Valor                      |
|-------------------|----------------------------|
| **ID**            | FEAT-NNN                   |
| **Estado**        | Estable / Beta / Planeada  |
| **Endpoints**     | [lista]                    |
| **UI**            | [componentes/páginas]      |
| **Usuarios**      | [quién puede usarla]       |
| **Dependencias**  | [servicios, integraciones] |
| **Evidencia**     | [ruta de código]           |

**Capacidades:**
- [x] Capacidad implementada
- [ ] Capacidad faltante o planeada
```

### 3. Matriz CRUD

```markdown
| Entidad | Create | Read | Update | Delete | Evidencia    |
|---------|--------|------|--------|--------|--------------|
| Usuario | ✅     | ✅   | ✅     | ⚠️ Soft | api/users/  |
```

Leyenda: ✅ Completo, ⚠️ Parcial/condicional, ❌ No disponible

### 4. Inventario de endpoints de API

Agrupado por dominio. Cada uno: `Método | Endpoint | Propósito | Auth`.

### 5. Inventario de componentes de UI

Tablas para: Formularios, Dashboards/Vistas, Acciones (modales, diálogos, confirmaciones).

### 6. Integraciones de terceros

Tabla: `Servicio | Propósito | Package | Estado | Funcionalidades que lo usan`.

### 7. Feature flags y funcionalidades en curso

Tabla: `Flag | Descripción | Default | Ambiente`.
Tabla: `Funcionalidad planeada | Evidencia (TODOs, stubs) | Estado estimado`.

### 8. Relevancia para QA

**Matriz de cobertura de pruebas por funcionalidad:**
```markdown
| Feature ID | Unit | Integración | E2E | Estado         |
|------------|------|-------------|-----|----------------|
| FEAT-001   | ✅   | ✅          | ⚠️  | Falta E2E      |
```

**Funcionalidades de alto riesgo** (priorizar testing):
```markdown
| Funcionalidad | Riesgo | Razón                 |
|---------------|--------|-----------------------|
| Pagos         | ALTO   | Impacto en ingresos   |
| Auth          | ALTO   | Seguridad             |
```

### 9. Discovery gaps

OBLIGATORIO. Listá funcionalidades que:
- No se pudieron verificar desde el código
- Aparecen parcialmente implementadas
- Tienen dueño o propósito poco claro
- Necesitan aclaración del equipo

Agregá al tope del archivo la línea `> Última verificación: YYYY-MM-DD` (fecha de hoy).

---

## Después de generar

- Referenciá cruzado con `business-data-map.md` si existe — anotá discrepancias.
- Si tu proyecto tiene un archivo de memoria para la IA (`AGENTS.md`, `CLAUDE.md`, `README.md` de
  contexto, etc.), agregá ahí una referencia al archivo generado, si todavía no está.
- Si el archivo ya existía: mostrame el resumen del diff y esperá mi confirmación explícita antes
  de sobreescribir.
- Reportá: total de funcionalidades, funcionalidades por estado, cobertura CRUD, integraciones
  encontradas, discovery gaps.

---

Cuando termines con este, seguí con `api.md` (5º de 5).
