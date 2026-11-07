# Operaciones en Jira: tabla de ruteo de herramientas

> **Propósito**: tabla central que decide qué herramienta resuelve cada operación en Jira. Todas
> las referencias de esta skill nombran la operación sin volver a
> decidir la herramienta ni filtrar sintaxis.
> **Cuándo usarla**: cada vez que un paso de un workflow necesita leer o escribir en Jira. Buscá
> la operación en la tabla y cargá la skill de la herramienta para el cómo exacto.
> **Referencias que la acompañan**:
>
> - `dependency-linking.md`: operaciones de creación de links.
> - `jira-publishing-gotchas.md`: bugs de ADF que afectan las escrituras de texto enriquecido.
> - `description-custom-field-dedup.md`: qué contenido va en cada lugar antes de escribir.

---

## Resolución de herramientas

El ruteo sigue la tabla "Resolución de herramientas" de `CLAUDE.md`. Para Jira,
`[ISSUE_TRACKER_TOOL]` se resuelve en tres niveles:

| Nivel | Capa | Fuente |
|---|---|---|
| Principal | skill `acli` | `.claude/skills/acli/SKILL.md`: CLI local, menor costo de tokens. |
| Respaldo | Atlassian MCP | Bloque `atlassian` de `.mcp.json`. Se usa cuando `acli` no está disponible, no está logueado o no cubre la operación. |
| Último recurso | REST `PUT/POST /rest/api/3/...` | HTTP directo con `ATLASSIAN_EMAIL` / `ATLASSIAN_API_TOKEN` (cargados por `bun run claude`). Solo si fallan los dos niveles anteriores o si la skill `acli` indica ese camino. |

**Regla dura**: este archivo describe solo el ruteo. La sintaxis exacta, las formas JSON y los
flags viven en la skill de la herramienta. Para `acli` (incluido el REST `PUT` para editar campos
de texto enriquecido), ver `.claude/skills/acli/SKILL.md`, sección "Publicar texto enriquecido
(ADF)".

---

## Tabla de operaciones

Cada fila es una operación y el nivel recomendado. Elegí el nivel más a la izquierda que cumpla
las precondiciones de la sección siguiente.

| Operación | Principal | Respaldo | Último recurso |
|---|---|---|---|
| Crear issue (Épica / Historia / Bug) | `acli`: `workitem create` | Atlassian MCP | REST `POST /rest/api/3/issue` |
| Actualizar la descripción de un issue existente | `acli`: `workitem edit` (descripción) | Atlassian MCP (solo `description` convierte Markdown) | REST `PUT /rest/api/3/issue/{KEY}` con ADF |
| Actualizar un custom field ADF existente | **`acli` no puede editar custom fields de issues existentes**: escalar directo | Atlassian MCP, una llamada por campo (`jira-publishing-gotchas.md` Gotcha 2) | REST `PUT /rest/api/3/issue/{KEY}` por campo (receta de la skill `acli`) |
| Transicionar el status | `acli`: `workitem transition` | Atlassian MCP | REST `POST /rest/api/3/issue/{KEY}/transitions` |
| Comentar un issue (Notas técnicas, Preguntas abiertas) | `acli`: `workitem comment create` | Atlassian MCP | REST `POST /rest/api/3/issue/{KEY}/comment` con ADF |
| Crear un link (Blocks / Relates) | `acli`: `workitem link create` | Atlassian MCP | REST `POST /rest/api/3/issueLink` |
| Leer un ticket con custom fields | `acli`: `workitem view` (todos los campos) | Atlassian MCP | REST `GET /rest/api/3/issue/{KEY}` |
| Buscar tickets (JQL) | `acli`: `workitem search --jql` | Atlassian MCP | REST `POST /rest/api/3/search/jql` |

Las celdas describen **qué es** la operación, no **cómo correrla**. Para la forma literal del
comando, cargá la skill de la herramienta.

---

## Precondiciones por operación

Antes de cada operación, cumplí sus precondiciones. Saltarlas produce corrupción silenciosa.

- **Escrituras de texto enriquecido (descripción, custom fields ADF, comentarios)**: tienen que
  ir en ADF. Ver `jira-publishing-gotchas.md` (marcas `code` + `strong` → HTTP 400; MCP con varios
  campos en una llamada).
- **Escrituras de custom fields**: el ID sale de `.claude/jira-fields.json` por clave. Nunca
  escribas el ID numérico fijo en la skill ni en lo que publicás.
- **Creación de links**: tipo y dirección según `dependency-linking.md`.
- **Transiciones de status**: los nombres salen de `.claude/jira-required.yaml`. Si el status no
  existe o no es alcanzable, reportalo y dejá el issue donde está.
- **Búsquedas**: pedí paginación explícita o acotá el JQL. `acli` trunca en silencio a la
  primera página si no se pide paginar.

---

## Reglas de degradación

Cuando el nivel principal no está disponible o falla:

1. **Pasá al nivel siguiente** de la fila. No saltees niveles.
2. **Informá la degradación** al usuario: `nivel: principal | respaldo | último recurso` y una
   línea de motivo (por ejemplo, `acli` no logueado, MCP no configurado).
3. **Avisá la pérdida de significado** cuando el cambio de nivel la produce. Ejemplo: un link
   que debía ser `Blocks` y termina como `Relates` pierde la dirección.
4. **Frená solo ante fallas duras**, no por degradación de nivel. Falla dura: los tres niveles
   agotados, o una escritura que no se puede reintentar sin riesgo.

---

## No se maneja acá: la asignación a un sprint es MANUAL

Asignar issues a un **sprint** de Jira no se automatiza: `acli` no puede agregar un issue a un
sprint (`JRACLOUD-97107`), y entre sprints suele haber una espera que decide una persona. La
asignación se hace a mano desde el tablero.

---

## Lo que este archivo NO contiene

Flags de comandos, formas JSON, estructura de ADF, cursores de paginación, configuración de
autenticación ni significado de códigos de error: todo eso vive en la skill de la herramienta.
Apenas la pregunta pasa a ser "¿qué flag uso?", cargá la skill `acli`.
