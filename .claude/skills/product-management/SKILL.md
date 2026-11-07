---
name: product-management
description: "Crea y refina épicas e historias de usuario en Jira. Workflow B: agregar una funcionalidad al backlog. C: crear una épica y descomponerla en historias. Entrevista al usuario por lo que necesita para redactar (persona, reglas, alcance, escenarios). Se usa para: 'crear historia', 'crear épica', 'agregar historia al backlog', 'agregar funcionalidad'. No se usa para: implementar una historia, diseñar pantallas ni probar una historia."
license: MIT
compatibility: [claude-code]
phase: management
complementary_categories: [issue-tracker]
metadata:
  kind: workflow
---

# Product Management

Alcance de esta skill (reglas I1-I21; las reservadas quedan marcadas):

- Workflows activos: **B** (agregar funcionalidad) y **C** (crear épica).
- Lo que hace falta para redactar (persona, reglas, alcance, escenarios) se pregunta en una
  entrevista: máximo 5 preguntas por ronda.
- Todo el contenido que se publica en Jira va en español.

## Abstracción de herramientas: regla dura

Esta skill describe la metodología (qué hacer). El cómo lo pone la skill de la herramienta.
Toda operación contra Jira se escribe como `[ISSUE_TRACKER_TOOL]`, que se resuelve en la tabla
"Resolución de herramientas" de `CLAUDE.md` (hoy: `acli`, respaldo Atlassian MCP). Antes de
ejecutar, cargá `.claude/skills/acli/SKILL.md`. Ruteo por operación: `references/jira-operations.md`.

## Antes de cualquier workflow

1. Leé `.claude/jira-fields.json`. Si está vacío algún `id` de los campos de esta skill
   (`acceptance_criteria`, `business_rules_specification`, `scope`, `out_of_scope`): FRENÁ y
   pedile al usuario que lo complete con el ticket modelo
   (`.claude/skills/acli/references/jira-check.md`).
2. Leé la clave del proyecto en la sección `## Proyecto Jira` de `CLAUDE.md`. Si sigue en
   `CLAVE_DEL_PROYECTO`: preguntala y, con OK del usuario, reemplazala ahí.
3. Leé `.claude/jira-required.yaml` (statuses de Story y Épica).

## Formato de título (canónico)

```
{Funcionalidad} | {Acción}
```

- **`{Funcionalidad}`**: 1-3 palabras, sustantivo. Nombre corto de la funcionalidad, más fino que
  la épica. Las historias hermanas de la misma funcionalidad comparten el prefijo. Se acuerda con
  el usuario en la entrevista.
- **` | `**: espacio, pipe, espacio.
- **`{Acción}`**: la cláusula "Quiero…" como verbo en infinitivo. Sin persona ni beneficio.
  Conserva calificadores (`desde el carrito`, `con tarjeta guardada`). Sin punto final, 3-9
  palabras.
- Total del título: hasta ~80 caracteres.

| Título | La descripción (`## Historia de usuario`) contiene |
|---|---|
| `Carrito \| Aplicar cupón de descuento` | Como comprador registrado, quiero aplicar un cupón de descuento, para pagar menos en mi compra |
| `Autenticación \| Iniciar sesión con Google` | Como visitante, quiero iniciar sesión con Google, para entrar sin crear una contraseña |

**Épicas:** frase nominal de módulo, sin pipe ni verbo (`Carrito y pagos`, `Gestión de usuarios`).

## Pre-flight de voz y formato (OBLIGATORIO antes de cada escritura en Jira)

Antes de cualquier `create` o `edit` que toque título, descripción o los campos de
`.claude/jira-fields.json`. Si algo falla, corregí el borrador antes de escribir.

- [ ] **Persona (I19):** el "Como" nombra un rol que el usuario declaró en la entrevista. Genéricos ("el usuario", "el
  sistema") → FRENÁ y preguntá.
- [ ] **Voz (I15):** AC, Alcance y Fuera de alcance sin endpoints, códigos HTTP, tablas o columnas,
  códigos de error internos, nombres de frameworks ni algoritmos.
- [ ] **AC (I17):** cada escenario Gherkin dentro de un bloque ```` ```gherkin ````. Mínimo 3: 1
  camino feliz, 1 error/validación, 1 borde. Datos concretos (`"juan@ejemplo.com"`, no "un email
  válido").
- [ ] **Story Points (I16):** vacío, salvo que el usuario pida estimar en esta sesión.
- [ ] **Sin duplicación (I3):** la descripción no tiene secciones de Criterios de aceptación,
  Alcance ni Fuera de alcance.
- [ ] **Título (I2, I20):** formato `{Funcionalidad} | {Acción}`, sin prefijos de spec ni claves.
- [ ] **No inventar (I5):** cada AC, ítem de alcance y regla sale de lo que dijo el usuario. Sin fuente → el campo queda vacío y se reporta como `gap`.

Después de escribir:

- [ ] Leer de vuelta con la operación "Leer un ticket con custom fields" de
  `references/jira-operations.md` y confirmar que los AC llegaron como `codeBlock`.
- [ ] Si se creó un link, verificar la dirección (`references/dependency-linking.md`).

## Workflows

| Workflow | Cuándo | Referencia |
|---|---|---|
| **B. Agregar funcionalidad** | "creá una historia", "agregá esto al backlog" | `references/add-feature.md` |
| **C. Crear épica** | "creá una épica", o B clasificó la idea como Nivel 2 (o Nivel 3 y se eligió una épica) | `references/epic-creation.md` |

## Hand-offs

- Planificar, ejecutar y reportar pruebas, y crear bugs: fuera de esta skill.
- Implementar la historia: fuera de esta skill.

## Anti-patrones: NUNCA

- **I1.** NUNCA escribas un `customfield_NNNNN` en la skill ni en lo que publicás como texto. Leé el ID de `.claude/jira-fields.json` en el momento.
- **I2.** NUNCA pongas prefijos de spec o claves en el título.
- **I3.** NUNCA copies AC, Alcance o Fuera de alcance en la descripción.
- **I4.** NUNCA dejes que dos historias de la misma épica compartan un ítem literal de Alcance. Avisá `overlap_alert` y preguntá.
- **I5.** NUNCA inventes AC, alcance ni reglas. Fuente válida: lo que dijo el usuario. Sin fuente → `gap`, ese campo queda vacío, seguí con el resto.
- **I6.** NUNCA mandes varios campos ADF en una sola llamada al MCP. Uno por llamada, o pre-convertidos con `md-to-adf.ts`.
- **I7.** NUNCA anides `` `código` `` dentro de `**negrita**` en Markdown que va a ADF.
- **I8.** NUNCA termines de crear historias sin la fase de dependencias (`references/dependency-linking.md`), aunque el resultado sea "sin dependencias".
- **I9.** NUNCA escribas sintaxis de `acli`, MCP o REST en esta skill. Usá `[ISSUE_TRACKER_TOOL]`.
- **I10.** Reservada (terminología de sprints: no aplica a esta skill).
- **I11.** Reservada (secuenciación de sprints: no aplica a esta skill).
- **I12.** NUNCA escribas el nombre de un link type sin resolverlo con `references/dependency-linking.md`.
- **I13.** NUNCA uses `Relates` para dependencias de orden: es simétrico y pierde la dirección.
- **I14.** Reservada (detección de ciclos de secuenciación: no aplica a esta skill).
- **I15.** NUNCA pongas superficie de implementación en AC, Alcance o Fuera de alcance. Heurística: si el criterio sigue siendo cierto aunque cambie la tecnología con la que está hecho el producto, es voz de negocio. Excepción: la persona es un consumidor de la API.
- **I16.** NUNCA completes Story Points por defecto. Solo si el usuario pide estimar: Fibonacci 1, 2, 3, 5, 8; 13 o más → dividir.
- **I17.** NUNCA escribas AC como texto plano. Cada escenario va en un bloque ```` ```gherkin ````.
- **I18.** NUNCA crees ni edites una historia sin la búsqueda activa de dependencias (`references/dependency-linking.md`, "Búsqueda activa").
- **I19.** NUNCA uses actores genéricos en el "Como". La persona sale de la entrevista.
- **I20.** NUNCA uses la oración "Como… quiero… para…" como título.
- **I21.** NUNCA uses para una misma cosa un término distinto del que usó el usuario en la entrevista. Término dudoso → se pregunta.
