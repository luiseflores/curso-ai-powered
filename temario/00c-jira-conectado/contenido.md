# Contenido — Tema 00c: Jira conectado

> Orden intercalado, igual que en los Temas 00a/00b: cada concepto va seguido de la acción
> práctica que lo pone en uso.
>
> **Instalación vs configuración:** lo que se instala (Context7, Bun, `acli`, `jq`, `uv`) lo
> instala el alumno en su máquina con `guia-instalacion.md`. La configuración del repo
> (`package.json`, `.mcp.json`, `.env.example`, `.gitattributes`, skill `acli`,
> `jira-required.yaml`, `jira-fields.json`, `CLAUDE.md`) le llega con `git pull` al iniciar el
> tema: no la crea a mano. Lo único suyo es el `.env`. En clase se trabaja contra el proyecto
> Jira de práctica del instructor (el alumno entra con su cuenta invitada); la creación de
> campos, statuses y ticket modelo se enseña para que la repita en el Jira de su empresa.

## 1. Qué es un MCP

**Qué es:** un protocolo (Model Context Protocol) que le da al asistente de IA acceso directo a
herramientas y datos externos — bases de datos, APIs, documentación — sin que vos tengas que
copiar y pegar esa información a mano en cada mensaje.

- El asistente CONSULTA un MCP directamente, como si fuera una extensión de sus capacidades.
- Cada MCP expone un conjunto específico de acciones (ej: leer un ticket de Jira, correr una
  query de solo lectura contra una base de datos).
- Un MCP corre como proceso aparte — vos lo configurás una vez, el asistente lo usa cuando lo
  necesita.

## 2. MCP vs CLI

**Diferencia clave:**
- Un CLI es un programa que el asistente EJECUTA como comando de terminal (`acli`, `git`).
- Un MCP es un servidor que el asistente CONSULTA directamente, sin pasar por la terminal.
- En este curso, el CLI siempre va primero: Atlassian CLI antes que Atlassian MCP, Playwright
  CLI antes que Playwright MCP.
- El MCP es el respaldo/fallback — se usa cuando el CLI no está disponible o no cubre algo
  puntual, no reemplaza al CLI como camino principal.

## 3. Acción: Context7 CLI + skill

**Qué es:** documentación técnica actualizada de librerías y frameworks. El asistente la consulta
corriendo comandos `ctx7` en la terminal, guiado por la skill `find-docs` que le enseña cuándo y
cómo usarlos. No es un MCP: es un caso real de la regla "CLI primero".

**Cómo trabaja la skill (2 pasos):**
1. `npx ctx7 library <nombre> "<pregunta>"`: convierte el nombre de la librería en un ID
   (ej: `react` → `/facebook/react`).
2. `npx ctx7 docs <ID> "<pregunta>"`: trae la documentación y ejemplos de código de ese ID.

**Skill del repo vs skill global:**
- **Skill del repo** (`.claude/skills/`): viaja con el proyecto, la ve todo el equipo que clona
  el repo.
- **Skill global** (`~/.claude/skills/`): vive solo en tu máquina y está disponible en cualquier
  proyecto que abras. La de Context7 se instala así, en `~/.claude/skills/find-docs/`.
- En el Tema 00b viste las skills en `.agents/skills/`: es el estándar abierto que leen Codex y
  otros asistentes. Claude Code las lee de `.claude/skills/`, y en este curso usamos Claude Code,
  así que tu repo las trae ahí.

**Acción:** correr el setup siguiendo `guia-instalacion.md`:
- `npx ctx7 setup --cli --claude`

Se abre el navegador para loguearte en Context7 (si no tenés cuenta, la creás en ese paso). El
comando genera tu API key y la guarda solo: no va en tu `.env`.

**Verificación:**
1. En la terminal: `npx ctx7 library react "How to clean up useEffect with async operations"`
   devuelve resultados de React.
2. En tu asistente: pedile documentación de una librería conocida y confirmá que corre un comando
   `ctx7` en pantalla, en vez de responder de memoria.

## 4. Qué es `.env`

**Qué es:** un archivo de variables de entorno: ahí van credenciales y configuración sensible
(API Tokens, URLs de bases de datos) que los MCP necesitan leer.

- Nunca se escriben credenciales directamente en el código ni en archivos versionados por git.
- `.env` normalmente está en `.gitignore`: no se sube al repositorio.
- El archivo `.env.example` sí se versiona: mismo formato, sin valores reales, para que cualquiera
  sepa qué variables necesita completar.

## 5. Acción: instalar Bun

**Qué es:** un runtime de JavaScript/TypeScript, como Node.js pero más rápido, que además es
gestor de paquetes. El curso lo usa para todo lo que corre dentro del repo: instalar dependencias
(`bun install`), abrir Claude (`bun run claude`, §6) y correr el conversor de la skill `acli`
(`md-to-adf.ts`), que pasa tu texto de Markdown al formato que Jira guarda en los campos largos
(ADF). Es una herramienta habitual en repos profesionales de JavaScript/TypeScript.

**npm no desaparece:** sigue sirviendo para instalar herramientas globales (Claude Code en el
00a, Playwright CLI en el 02), y Context7 corre con `npx` por dentro. Dentro del repo, siempre
Bun.

**Acción:** instalar Bun siguiendo `guia-instalacion.md`.

**Verificación:** `bun --version` muestra la versión.

## 6. Acción: `bun run claude` (cómo llegan las variables a Claude)

**Por qué hace falta:** ni tu asistente ni las skills leen `.env` solos.
- Las skills ejecutan comandos que usan variables. Ejemplo: corregir un campo de una historia ya
  creada (§15) corre `curl -u "$ATLASSIAN_EMAIL:$ATLASSIAN_API_TOKEN" ...`; sin esas variables
  cargadas, el comando sale vacío y falla.
- Los `${...}` de `.mcp.json` se completan con las variables que tiene tu asistente al arrancar,
  no con el `.env`.

**Qué es `package.json`:** el archivo donde un repo de JavaScript declara sus scripts (comandos
con nombre) y las dependencias que necesita. Llega con el `git pull` del tema, junto con
`bun.lock` (versiones exactas) y `.gitattributes` (finales de línea LF, para que Windows y Linux
lean igual los archivos). Tiene un script, `claude`, que abre Claude Code con tu `.env` cargado
usando `dotenv-cli`. Hace falta porque `bun run` carga `.env` solo para programas de Bun, no para
`claude`.

**Acción** (siguiendo `guia-instalacion.md`, Tema 00c):
1. `bun install` en la raíz del repo, una sola vez: instala `dotenv-cli` en `node_modules/`
   (carpeta ignorada por git, igual que `.env`). No hay que instalarlo aparte.
2. Abrir Claude siempre con `bun run claude`.

**Comandos de ejemplo:**
```bash
bun install                    # una sola vez
bun run claude                 # abre Claude con tu .env cargado
bun run claude --continue      # argumentos para Claude, a continuación
```

**Gotcha:** `claude` a secas arranca sin tus variables. Si cambiás `.env`, salí de Claude y volvé
a abrirlo con `bun run claude`.

**En un repo profesional:** `.env` (secretos, ignorado por git) + `.env.example` (versionado, sin
valores) + `${...}` en la configuración es el patrón estándar: las credenciales nunca quedan
escritas en archivos que se comparten con el equipo.

## 7. Qué es Jira (y repaso mínimo de Scrum)

**Qué es:** la herramienta de gestión de tickets que ya usás en tu trabajo. Este curso no la
enseña desde cero: la conecta a tu asistente de IA.

Repaso de los tipos de ticket que vas a crear en el Tema 00d:
- **Sprint:** período fijo (normalmente 2 semanas) en el que el equipo se compromete a terminar
  un conjunto de tickets.
- **Épica:** un módulo o capacidad grande que agrupa varias historias (ej. "Carrito y pagos").
- **Historia de usuario:** una necesidad de un usuario, chica como para terminarse en un sprint.
- **Tarea:** trabajo técnico que no es una necesidad directa de un usuario.
- **Bug:** un comportamiento que no es el esperado.

## 8. Qué es un API Token

**Qué es:** una clave de acceso que reemplaza usuario+contraseña para que una herramienta se
autentique contra un servicio, sin exponer tu contraseña real.

- Se genera desde la configuración de la cuenta del servicio (para Atlassian: `id.atlassian.com`).
- Se puede revocar sin cambiar tu contraseña, si se filtra o dejás de necesitarlo.
- Va en tu `.env` — nunca se pega directo en un comando ni se comparte por chat.

## 9. Acción: instalar `acli`

**Qué es:** el CLI oficial de Atlassian — lee y actualiza tickets de Jira desde la terminal.

**Acción:**
1. Generar tu API Token en `id.atlassian.com`, con la cuenta invitada al sitio de práctica.
2. Instalar `acli` y `jq` siguiendo `guia-instalacion.md`. `jq` filtra el JSON que devuelve
   `acli`; lo usás para leer el ticket modelo (§14).
3. Loguear `acli` con tu API Token: `acli jira auth login` (modo interactivo). `--token` lee el
   token de stdin; en la terminal no sirve `echo "$ATLASSIAN_API_TOKEN"`, porque la terminal no
   lee `.env` (solo `bun run claude` lo carga).
4. Completar tu `.env` con `ATLASSIAN_URL`, `ATLASSIAN_EMAIL` y `ATLASSIAN_API_TOKEN`.

**Verificación:** `acli jira workitem search --jql "project = CLAVE" --paginate` lista los
tickets del proyecto de práctica (la clave está en `CLAUDE.md`, sección `## Proyecto Jira`).

## 10. Acción: Atlassian MCP

**Qué es:** el respaldo de `acli` — mismo acceso a Jira, pero vía MCP en vez de CLI, para cuando
`acli` no está disponible.

**Acción:**
1. Instalar `uv` siguiendo `guia-instalacion.md`: es el runtime con el que corre este MCP
   (`uvx mcp-atlassian`). No se enseña Python, solo se instala.
2. Leer el `.mcp.json` que llegó con el repo: el bloque `atlassian` toma `${ATLASSIAN_URL}`,
   `${ATLASSIAN_EMAIL}` y `${ATLASSIAN_API_TOKEN}` de las variables con que arranca Claude. Son
   referencias, no credenciales: por eso el archivo se versiona. Reusa el mismo API Token de
   `acli`, no hace falta otro.
3. Abrir `bun run claude` y aceptar los MCP del proyecto la primera vez.

**Verificación:** `/mcp` muestra `atlassian` **connected**; pedirle al asistente que lea el mismo
ticket vía MCP y comparar que trae la misma información que `acli`.

## 11. Repaso rápido: qué es un custom field y un workflow en Jira

**Qué es:** un proyecto Jira se puede *configurar*, no solo *usar* — tiene campos propios
(custom fields) y estados propios (workflow/statuses) además de los que trae por defecto.

- Custom field: un campo que el equipo agrega a sus tickets (ej. "Ambiente afectado",
  "Prioridad de negocio") — no viene de fábrica en Jira.
- Workflow (statuses): la secuencia de estados por la que pasa un ticket (ej. `To Do` →
  `In Progress` → `In Review` → `Done`) — cada equipo puede definir la suya.
- Esto ya se configura a nivel de proyecto, no requiere ser admin de toda la instancia Jira.

## 12. Acción manual: crear campos y statuses en la UI de Jira

**Qué es:** se crea a mano, en la configuración del proyecto Jira (sin código), lo mínimo para
crear historias en el Tema 00d. En el proyecto de práctica ya está hecho (se muestra en clase);
el alumno lo repite en el Jira de su empresa siguiendo el Paso 9 de `guia-instalacion.md`.

- **4 campos de Story, de tipo Paragraph (multi-línea):** Criterios de aceptación, Reglas de
  negocio, Alcance, Fuera de alcance.
- **Por qué Paragraph:** un campo de una línea corta a los 255 caracteres; tres escenarios de
  criterios de aceptación no entran. Los campos Paragraph Jira los guarda en ADF, y por eso
  instalaste Bun.
- **Statuses de Story y Épica:** los que lista `.claude/jira-required.yaml`. `BACKLOG` tiene que
  ser el status inicial (en team-managed: renombrar "Por hacer" a `BACKLOG`) y `REFINAMIENTO`
  alcanzable desde él: el Tema 00d crea la historia y la pasa a `REFINAMIENTO`.
- Story Points ya viene con las plantillas Scrum/Kanban: solo confirmás que esté habilitado.
- Los campos de Bug y los de pruebas (ATP/ATR) se crean en el Tema 02, cuando se usan.

## 13. Por qué `acli` necesita el ID numérico de cada campo

**Gotcha central:** `acli` (y la API de Jira en general) identifica cada custom field por un ID
numérico (`customfield_NNNNN`), no por su nombre visible.

- No existe `acli jira field list` ni ninguna forma de listar campos por nombre.
- No hay name-addressing: nunca se le puede pasar `--field "Ambiente afectado"` a `acli`, solo
  `--field customfield_10087` (ejemplo de ID).
- La estrategia para conseguir el ID sin escribir código: un **ticket modelo** — un ticket real
  por work type, con cada campo lleno de un valor identificable, que después se inspecciona.

## 14. Acción: descubrir IDs con el ticket modelo

**Comando:**

```bash
acli jira workitem view KEY --fields "*all" --json | jq '.fields | to_entries | map(select(.key|startswith("customfield_")) | select(.value != null and .value != []))'
```

**Nota obligatoria:** sin `--fields "*all"` la vista default de `acli jira workitem view` NO trae
ningún `customfield_` — es el error más probable del alumno en este paso. Otras dos formas de
conseguir el mismo ID, sin código: la URL del admin de Jira al editar el campo
(`EditCustomField!default.jspa?id=NNNNN`, la más simple para un campo recién creado), o
`curl .../rest/api/3/field` para listar todos los campos del site.

**El ticket modelo tiene que tener los 4 campos llenos.** `acli` no devuelve el nombre de los
campos: un campo vacío no aparece, y uno lleno se reconoce por su contenido.

**`.claude/jira-fields.json`:** guarda cada `customfield_NNNNN` en su clave
(`acceptance_criteria`, `business_rules_specification`, `scope`, `out_of_scope`). La skill que
crea historias los lee de ahí. En un repo profesional este archivo suele generarlo un
script que consulta la API de Jira; acá se llena a mano, con la misma forma. La clave del proyecto va
en `CLAUDE.md`, sección `## Proyecto Jira`.

**En clase (proyecto de práctica):** el repo ya trae los IDs y la clave. El alumno corre el
comando contra el ticket modelo de práctica y confirma que cada `customfield_` coincide con un ID
de `jira-fields.json`.

**En su empresa:** crea su ticket modelo, copia los IDs a `jira-fields.json` y la clave a
`CLAUDE.md` en su repo de trabajo (no en el clon del curso, que se actualiza con `git pull`).

**Verificación:** `jq -r 'to_entries[] | "\(.key): \(.value.id)"' .claude/jira-fields.json`
muestra los 4 IDs, ninguno vacío, y coinciden con el ticket modelo.

## 15. Gotcha: editar un custom field ya creado

**Qué es:** `acli jira workitem edit` rechaza cualquier custom field con error duro (exit 1) — la
edición de custom fields solo funciona en `create`, nunca en `edit`.

- El único camino para editar un custom field en un ticket ya existente (ej. corregir un
  criterio de aceptación de una historia ya creada) es REST `PUT` con `curl`, usando las
  mismas credenciales `ATLASSIAN_EMAIL`/`ATLASSIAN_API_TOKEN` ya configuradas.
- Se usa en el Tema 00d si hay que corregir una historia ya creada.

## Cierre del tema

Al terminar este tema, el alumno: explica qué es un MCP y en qué se diferencia de un CLI, abre
Claude con sus variables cargadas (`bun run claude`), tiene Context7 y Bun instalados, conectó
Jira por `acli` y por Atlassian MCP con su API Token, verificó los IDs de `.claude/jira-fields.json`
contra el ticket modelo de práctica, y sabe crear los 4 campos Paragraph, los statuses y el ticket
modelo en el Jira de su empresa. Prueba de punta a punta: el asistente, con la skill `acli`,
empareja los custom fields del ticket modelo con las claves de `jira-fields.json`. En el Tema 00d
usa todo esto para crear su primera épica e historia.
