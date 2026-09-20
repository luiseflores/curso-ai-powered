# Generador del Mapa de Datos de Negocio

> **Orden de generación: 1º de 5.** Generá este documento primero — no depende de ningún otro
> archivo de contexto. Ver `README.md` de esta carpeta para el orden completo.

Genera o actualiza `.context/business/business-data-map.md` — un mapa visual y narrativo de cómo
funciona el sistema.

**Objetivo**: analizá el producto completo (o indicame una ruta/módulo puntual si querés acotar el
análisis).

---

## Qué produce esto

Un único documento que explica **cómo funciona el sistema** a través de:
- Entidades de negocio y sus relaciones (POR QUÉ existen)
- Flujos de negocio para cada funcionalidad importante (trazado de punta a punta)
- Máquinas de estado y sus transiciones
- Procesos automáticos (triggers, cron jobs, webhooks)
- Integraciones externas y su impacto en los datos

Este es el **archivo de contexto más valioso** de los tres — de él depende todo lo demás.

---

## Fuentes (usá TODAS las disponibles)

Agotá cada fuente antes de escribir. No te bases en una sola.

| Fuente | Qué extraer | Herramienta |
|--------|----------------|------|
| Esquema de base de datos | Tablas, columnas, relaciones, constraints, enums | DBHub MCP (o el cliente de DB que tengas) — corré queries de solo lectura contra el ambiente activo |
| Endpoints de API | Rutas, métodos, payloads, niveles de auth | OpenAPI MCP, o leé el spec OpenAPI del proyecto; si no existe, leé los archivos de rutas directamente |
| Código backend | Servicios, lógica de negocio, reglas de validación, triggers | Leé el repositorio de backend — enfocate en servicios, controllers, modelos |
| Código frontend | Páginas, formularios, flujos de usuario, manejo de estado | Leé el repositorio de frontend — enfocate en rutas, páginas, formularios |
| Contexto existente | PRD, SRS, modelo de negocio + glosario de dominio, si ya tenés algo escrito | Cualquier doc previo del proyecto |
| Dependencias del package | Integraciones externas (Stripe, SendGrid, Auth0, etc.) | Leé `package.json`, `requirements.txt`, `Gemfile`, etc. |

**Regla de oro**: sintetizá, no extraigas. La DB está viva — usala para ENTENDER el sistema, no
para volcar `information_schema` en markdown.

---

## Detección de modo

```
¿Existe .context/business/business-data-map.md?
  → NO:  generalo desde cero
  → SÍ:  generá una nueva versión, mostrame el resumen del diff, y
         esperá mi confirmación antes de sobreescribir. NUNCA
         sobreescribas automáticamente.
```

---

## Fases de exploración

### Fase 1 — Entidades de negocio

Para cada entidad descubierta vía DB + código:
- ¿Qué concepto del mundo real representa?
- ¿Por qué existe? ¿Qué problema resuelve?
- ¿Cómo se relaciona con otras entidades, y por qué?

**NO listes columnas.** El schema se puede consultar a demanda. Documentá el significado de
negocio.

### Fase 2 — Flujos de negocio

Para cada funcionalidad importante del sistema:
- Trazá el recorrido completo: `Usuario → API → Lógica → DB → Respuesta`
- ¿Qué endpoints, servicios y tablas participan?
- ¿Qué reglas de negocio aplican?
- ¿Qué efectos colaterales ocurren (emails, webhooks, cambios de estado)?

**Documentá TODOS los flujos importantes.** No te limites a 3.

### Fase 3 — Máquinas de estado

Para entidades con estados de ciclo de vida (pendiente, activo, completado, cancelado...):
- Transiciones válidas y eventos que las disparan
- Consecuencias de cada transición
- Reglas de negocio que restringen las transiciones

### Fase 4 — Procesos automáticos

- **Triggers de DB**: ¿qué se dispara automáticamente en INSERT/UPDATE/DELETE?
- **Cron jobs**: ¿qué corre en un horario programado?
- **Webhooks**: ¿qué eventos externos disparan acciones?

Para cada uno: ¿por qué existe? ¿Qué problema resuelve?

### Fase 5 — Integraciones externas

Para cada servicio de terceros:
- Cómo fluyen los datos hacia adentro/afuera
- Qué flujos de negocio dependen de él
- Comportamiento ante falla (¿qué se rompe si el servicio está caído?)

---

## Estructura de la salida

Escribí `.context/business/business-data-map.md` con esta estructura:

1. **Encabezado visual** — nombre del proyecto + descripción corta en caja ASCII
2. **Resumen ejecutivo** — 2-3 párrafos sobre el propósito de negocio, actores (diagrama ASCII), propuesta de valor
3. **Mapa de entidades** — diagrama ASCII de relaciones + tabla (`Entidad | Rol de negocio | Por qué existe`) + narrativa sobre relaciones clave
4. **Flujos de negocio** — una sección por flujo con: diagrama ASCII de flujo, narrativa numerada, reglas de negocio, rutas de código involucradas
5. **Máquinas de estado** — una sección por entidad con estado, con: diagrama ASCII de estados, tabla de transiciones (`De | A | Evento | Efectos`), reglas de negocio
6. **Procesos automáticos** — tres tablas (triggers, cron jobs, webhooks) cada una con columna "por qué existe"
7. **Integraciones externas** — una sección por servicio con diagrama ASCII de llamada, impacto en los datos, flujos que dependen de él
8. **Discovery gaps** — OBLIGATORIO. Listá todo lo que no pudiste verificar desde código/DB. "No pude verificar X" es mejor que inventar una respuesta.

**Visual primero**: usá diagramas ASCII de forma extensiva. Los diagramas ganan a los párrafos.

Agregá al tope del archivo la línea `> Última verificación: YYYY-MM-DD` (fecha de hoy).

---

## Después de generar

- Si tu proyecto tiene un archivo de memoria para la IA (`AGENTS.md`, `CLAUDE.md`, `README.md` de
  contexto, etc.), agregá ahí una referencia al archivo generado, si todavía no está.
- Si el archivo ya existía: mostrame el resumen del diff y esperá mi confirmación explícita antes
  de sobreescribir.
- Reportá: entidades documentadas, flujos trazados, máquinas de estado encontradas, integraciones
  mapeadas, discovery gaps.

---

Cuando termines con este, seguí con `glossary.md` (2º de 5).
