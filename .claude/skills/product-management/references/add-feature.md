# Workflow B: Agregar una funcionalidad

> Tomar una idea o funcionalidad nueva, decidir su tamaño y, si es una historia, crearla en Jira
> lista para refinar. Si es más grande, deriva al workflow C (`epic-creation.md`).

Actuá como Product Owner y analista: el usuario trae la idea, vos la ordenás y la escribís.

---

## Entradas: leelas primero, en este orden

1. `.claude/jira-fields.json`: IDs de los custom fields de esta skill. Si falta alguno, frená
   (ver "Antes de cualquier workflow" en `SKILL.md`).
2. `.claude/jira-required.yaml`: statuses de Story y Épica.
3. Clave del proyecto: sección `## Proyecto Jira` de `CLAUDE.md`. No la escribas fija acá.
4. Las épicas existentes en Jira (búsqueda de épicas del proyecto con `[ISSUE_TRACKER_TOOL]`).

---

## Campos de la historia

Los IDs numéricos de Jira (`customfield_NNNNN`) cambian en cada instancia y NO viven en esta
skill: cada campo se resuelve en el momento por su clave en `.claude/jira-fields.json` (I1).

**Descripción** (solo esto, nada más):

- `## Historia de usuario`: Como [persona], quiero [acción], para [beneficio].
- `## Reglas de negocio`: solo si no entran en su campo (tablas, listas largas).

**Notas técnicas:** si el usuario aporta pistas para quien implemente, van como comentario del
ticket con encabezado `## Notas técnicas` (Paso 5), nunca en la descripción.

**Custom fields:**

| Clave | Contenido |
|---|---|
| `acceptance_criteria` | Mínimo 3 escenarios (1 camino feliz + error/borde), cada uno en un bloque ```` ```gherkin ```` (I17). Observable por la persona, sin endpoints, códigos HTTP, tablas ni frameworks (I15). |
| `scope` | Solo lo que entra: capacidades que gana la persona. |
| `out_of_scope` | Exclusiones explícitas, sin mezclar con `scope`. |
| `business_rules_specification` | Reglas del dominio (límites, permisos por rol). Opcional. No algoritmos internos. |
| Story Points | Vacío, salvo que el usuario pida estimar en esta sesión (I16). |

AC, Alcance y Fuera de alcance viven **solo** en sus custom fields, nunca en la descripción (I3).
Contrato completo: `description-custom-field-dedup.md`.

**Título:** `{Funcionalidad} | {Acción}` (ver "Formato de título" en `SKILL.md`, I2 e I20). La
oración "Como… quiero… para…" nunca es el título.

---

## Paso 1: Contexto

Pedile al usuario que describa la idea con sus palabras y preguntá lo que necesites para redactar
(persona, reglas, alcance, escenarios), máximo 5 preguntas por ronda.

## Paso 2: Clasificar

| Nivel | Señales | Qué hacer |
|---|---|---|
| **1. Historia** | Mejora o extensión de algo que existe, encaja en una épica existente, 1 historia, entra en un sprint | Seguí en este archivo (Paso 3) |
| **2. Épica** | Funcionalidad nueva que no encaja en ninguna épica, 3-8 historias, alcance acotado | Seguí con `epic-creation.md` (workflow C) |
| **3. Varias épicas** | 2 o más épicas, más de ~20 historias, dependencias complejas | FRENÁ (abajo) |

Preguntas para decidir:

1. ¿Cuántas historias necesita? (1 → Nivel 1; 3-8 → Nivel 2; más → Nivel 3)
2. ¿Encaja en una épica existente? (Sí → probablemente Nivel 1)
3. ¿Se puede partir en 2 o más épicas independientes? (Sí → Nivel 3)

Informá la clasificación en dos líneas (nivel y por qué) antes de seguir. No fuerces un nivel:
si la idea es Nivel 3, no la crees de una vez.

**Nivel 3: frenar.** No crees nada. Explicale al usuario por qué es demasiado grande, proponé
cómo partirla en épicas independientes (nombre y una línea de objetivo cada una) y pedile que
elija **una** para empezar. Esa épica sigue por `epic-creation.md`.

## Paso 3: Épica padre

Elegí la épica existente a la que pertenece la historia entre las épicas de Jira.

```markdown
**Épica elegida:** [CLAVE] [título]
**Motivo:** [por qué la historia pertenece a esta épica]
```

## Paso 4: Borrador y OK

Armá el borrador completo (título, descripción, custom fields, épica padre, prioridad), pasá el
pre-flight de voz y formato de `SKILL.md` y mostralo.

Esperá aprobación explícita. Sin OK no se escribe nada en Jira.

## Paso 5: Crear la historia

Con el OK, creá en Jira:

1. Pedile a `[ISSUE_TRACKER_TOOL]` que cree la Story (operación "Crear issue" de
   `jira-operations.md`): proyecto, tipo Story, título, descripción, padre (la épica del Paso 3)
   y prioridad (Alta, Media o Baja).
2. Los textos largos (descripción y custom fields) se escriben en Markdown y se convierten a ADF
   con la receta "Publicar texto enriquecido (ADF)" de la skill `acli`. Antes, leé
   `jira-publishing-gotchas.md`.
3. Capturá la clave asignada (`CLAVE-NUM`).
4. Si hay notas técnicas, publicalas como comentario `## Notas técnicas` (operación "Comentar un
   issue" de `jira-operations.md`).

## Paso 6: Transicionar

Pasá la historia a `REFINAMIENTO` (de `.claude/jira-required.yaml`). Si el workflow del proyecto
no ofrece esa transición desde el status inicial, avisale al usuario y dejala donde está: no
fuerces otra transición.

## Paso 7: Dependencias

Seguí `dependency-linking.md` (búsqueda activa, creación y verificación de links), aunque el
resultado sea "sin dependencias" (I8, I18).

## Paso 8: Leer de vuelta

Leé la historia con todos sus campos (operación "Leer un ticket con custom fields" de
`jira-operations.md`: la vista por defecto no trae custom fields) y confirmá:

- Cada custom field de la tabla tiene contenido (o quedó vacío como `gap` a propósito, I5).
- Los AC llegaron como bloques de código.
- La descripción no repite AC, Alcance ni Fuera de alcance.

Si algo quedó mal, corregilo en Jira con la operación "Actualizar un custom field ADF existente"
de `jira-operations.md` (`acli` no edita custom fields de un ticket existente). No hay copia
local que editar.

---

## Resultado

- Historia creada en Jira con su clave real, colgada de la épica correcta.
- Historia en `REFINAMIENTO`.
- Dependencias publicadas como links (o "sin dependencias").
- Leída de vuelta sin duplicaciones.

No se generan archivos locales de historias: todo vive en Jira.

## Prerrequisitos

- `acli` logueado contra el Jira del proyecto.
- IDs de los campos de esta skill en `.claude/jira-fields.json`.
- Clave del proyecto en `## Proyecto Jira` de `CLAUDE.md`.
- Claude abierto con `bun run claude` (carga las variables de `.env`).
- Bun instalado (para `md-to-adf.ts`).

## Referencias

- `epic-creation.md`: workflow C, para Nivel 2 y para la épica elegida de un Nivel 3.
- `jira-operations.md`: qué herramienta usar para cada operación en Jira.
- `dependency-linking.md`: búsqueda activa y links.
- `description-custom-field-dedup.md`: qué va en la descripción y qué no.
- `jira-publishing-gotchas.md`: trampas del formato ADF.
