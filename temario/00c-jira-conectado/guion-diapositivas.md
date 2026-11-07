# Guión de diapositivas — Tema 00c: Jira conectado

> Mismo orden intercalado que `contenido.md`. Cada diapositiva de concepto lleva 3-5 bullets de
> repaso (definición corta + por qué importa + ejemplo/dato concreto + gotcha si aplica), no un
> párrafo largo. Cada diapositiva de "acción" se dicta con la máquina del alumno abierta.

## Diapositiva 1 — Portada

**Título en pantalla:** "Tema 00c — Jira conectado"

**Guión:**
- Hoy conectamos tu asistente con Jira: `acli` como camino principal, Atlassian MCP de respaldo.
- Antes preparamos el terreno: MCP, `.env`, Bun, `bun run claude` y Context7.
- La configuración del repo llega con `git pull`; ustedes instalan herramientas y completan su
  `.env`.
- Al final de la clase su asistente lee el proyecto Jira de práctica y saben preparar el Jira de
  su empresa (campos, statuses, IDs en `.claude/jira-fields.json`) para crear historias en el
  00d.

## Diapositiva 2 — ¿Qué es un MCP?

**Guión:**
- MCP = Model Context Protocol: le da al asistente acceso directo a herramientas y datos
  externos.
- El asistente CONSULTA el MCP directamente, sin que vos copies/pegues información a mano.
- Cada MCP expone acciones específicas — ej: leer un ticket, correr una query de solo lectura.
- Corre como proceso aparte: lo configurás una vez, el asistente lo usa cuando lo necesita.

## Diapositiva 3 — MCP vs CLI

**Guión:**
- CLI: el asistente EJECUTA un programa como comando de terminal (`acli`, `git`).
- MCP: el asistente CONSULTA un servidor directamente, sin pasar por la terminal.
- Regla del curso: CLI siempre primero (Atlassian CLI antes que Atlassian MCP, Playwright CLI
  antes que Playwright MCP).
- El MCP es respaldo/fallback — no reemplaza al CLI como camino principal.

## Diapositiva 4 — Acción: Context7 CLI + skill

**Guión:**
- Primer caso real de "CLI primero": Context7 trae documentación actualizada de librerías.
- No es un MCP: el setup instala la skill `find-docs`, que le enseña al asistente a correr
  comandos `ctx7` en la terminal.
- Skill del repo (`.claude/skills/`): viaja con el proyecto. Skill global
  (`~/.claude/skills/find-docs/`): solo en tu máquina, sirve en cualquier proyecto.
- Puente con el 00b: `.agents/skills/` es el estándar abierto (Codex y otros); Claude Code usa
  `.claude/skills/`, y el curso usa Claude Code.
- La skill trabaja en 2 pasos: `library` convierte el nombre de la librería en un ID
  (`react` → `/facebook/react`) y `docs` trae la documentación de ese ID.
- Instalación: `npx ctx7 setup --cli --claude`. Se loguean en Context7 en el
  navegador; la API key la genera y guarda el comando.
- Verificación: `npx ctx7 library react "..."` en la terminal, y después pedirle docs al
  asistente y confirmar que corre `ctx7`.

## Diapositiva 5 — ¿Qué es `.env`?

**Guión:**
- Archivo de variables de entorno: ahí van API Tokens, URLs de bases de datos, credenciales.
- Nunca se escriben credenciales en el código ni en archivos versionados.
- `.env` va en `.gitignore`: no se sube al repo.
- `.env.example` SÍ se versiona: mismo formato, sin valores reales.

## Diapositiva 6 — Acción: instalar Bun

**Guión:**
- Runtime y gestor de paquetes de JavaScript/TypeScript, como Node.js + npm pero más rápido.
- Dentro del repo, todo con Bun: `bun install` y `bun run claude` (próxima diapositiva). npm
  queda para herramientas globales (Claude Code, Playwright CLI).
- Lo usa el conversor de la skill `acli`: `md-to-adf.ts`.
- Jira guarda los campos largos en ADF (JSON), no en Markdown: ustedes escriben Markdown y el
  conversor traduce. Ejemplo en pantalla: un AC en bloque `gherkin` → `codeBlock` de ADF.
- Verificación: `bun --version`. Si no responde, cerrar y abrir la terminal.

## Diapositiva 7 — Acción: bun run claude

**Guión:**
- Problema: ni el asistente ni las skills leen `.env` solos.
- Las skills corren comandos con variables (`curl -u "$ATLASSIAN_EMAIL:$ATLASSIAN_API_TOKEN"`);
  los `${...}` de `.mcp.json` se completan con lo que tiene Claude al arrancar. Sin variables,
  fallan.
- `package.json`: scripts y dependencias del repo, llegó con el `git pull`. El script `claude`
  abre Claude con tu `.env`.
- Acción: `bun install` (una vez: instala `dotenv-cli` en `node_modules/`, ignorada por git) y
  `bun run claude`.
- Gotcha: `bun run` carga `.env` solo para programas de Bun; por eso el script usa `dotenv-cli`.
- Gotcha: `claude` a secas arranca sin variables. Cambiaste `.env` → salir y volver a abrir.
- En un repo profesional: `.env` + `.env.example` + `${...}` es el patrón estándar.

## Diapositiva 8 — ¿Qué es Jira? + repaso Scrum

**Guión:**
- La herramienta de tickets que ya usan en su trabajo: el curso no la enseña desde cero, la
  conecta a su asistente.
- Repaso de lo que van a crear en el 00d, con un ejemplo por tipo:
  - Sprint: período fijo, ≈2 semanas ("Sprint 14: 6 al 20 de octubre").
  - Épica: módulo grande ("Carrito y pagos").
  - Historia: necesidad de un usuario que entra en un sprint ("Carrito | Aplicar cupón").
  - Tarea: trabajo técnico ("Actualizar la librería de pagos").
  - Bug: comportamiento no esperado ("El total no descuenta el cupón").

## Diapositiva 9 — ¿Qué es un API Token?

**Guión:**
- Clave de acceso que reemplaza usuario+contraseña para que una herramienta se autentique.
- Se genera desde la cuenta del servicio — para Atlassian: `id.atlassian.com`.
- Se puede revocar sin cambiar tu contraseña real, si se filtra.
- Va en `.env` — nunca se pega directo en un comando ni se comparte por chat.

## Diapositiva 10 — Acción: instalar `acli`

**Guión:**
- CLI oficial de Atlassian: lee y actualiza tickets desde la terminal. Camino principal.
- Generen su API Token en `id.atlassian.com`, con la cuenta invitada al sitio de práctica.
- Instalen `acli` y `jq` siguiendo la guía. `jq` filtra el JSON de `acli`: lo usan hoy con el
  ticket modelo.
- Login: `acli jira auth login`, modo interactivo, eligiendo API token. Gotcha: en la terminal
  `echo "$ATLASSIAN_API_TOKEN"` sale vacío, porque solo `bun run claude` carga `.env`.
- Después completan las 3 variables `ATLASSIAN_*` en su `.env`.
- Verificación: `acli jira workitem search --jql "project = CLAVE"` lista tickets.

## Diapositiva 11 — Acción: Atlassian MCP

**Guión:**
- Es el respaldo de `acli` — mismo acceso a Jira, vía MCP en vez de CLI.
- Corre con `uv`: lo instalan con la guía, sin aprender Python.
- `.mcp.json` llegó con el repo: el bloque `atlassian` usa `${ATLASSIAN_*}`, referencias a su
  `.env`, no credenciales. Reusa el mismo API Token.
- Abren `bun run claude`, aceptan los MCP del proyecto y `/mcp` muestra `atlassian` connected.
- Verificación: pídanle al asistente el mismo ticket vía MCP y comparen contra `acli`.

## Diapositiva 12 — ¿Qué es un custom field y un workflow?

**Guión:**
- Un proyecto Jira se puede configurar, no solo usar: campos propios y estados propios.
- Custom field: campo que agrega el equipo, no viene de fábrica (ej. "Criterios de aceptación").
- Workflow: la secuencia de estados de un ticket. La de Story en el curso: `BACKLOG` →
  `REFINAMIENTO` → ... → `CERRADA`.
- En team-managed lo configuran ustedes, sin ser admin de toda la instancia.

## Diapositiva 13 — Acción manual: crear campos y statuses

**Guión:**
- En el proyecto de práctica ya está hecho: lo muestro en pantalla. Ustedes lo repiten en el
  Jira de su empresa con el Paso 9 de la guía.
- Team-managed: Configuración del proyecto → Tipos de work item → Story.
- 4 campos tipo Párrafo con los nombres exactos de `jira-required.yaml`: Criterios de
  aceptación, Reglas de negocio, Alcance, Fuera de alcance.
- Por qué Párrafo: un campo de una línea corta a los 255 caracteres; tres escenarios de AC no
  entran. Párrafo se guarda en ADF: por eso Bun.
- Statuses de Story y Épica; como mínimo `BACKLOG` y `REFINAMIENTO`, que usa el 00d.
- Gotcha: renombrar "Por hacer" a `BACKLOG` (status inicial) y permitir llegar a `REFINAMIENTO`
  desde cualquier status; si no, el 00d no puede mover la historia.
- Story Points ya viene con la plantilla Scrum/Kanban: solo confirmar.
- Company-managed: los campos los crea un admin; la guía dice qué pedirle.
- Campos de Bug y de pruebas (ATP/ATR): Tema 02.

## Diapositiva 14 — Gotcha: `acli` necesita el ID numérico

**Guión:**
- `acli` (y la API de Jira) identifica cada custom field por ID numérico
  (`customfield_NNNNN`), no por su nombre visible.
- No existe `acli jira field list`: no hay forma de listar campos por nombre.
- No hay name-addressing: nunca se le pasa el nombre, solo el ID.
- Estrategia sin código: un ticket modelo con cada campo lleno, que después se inspecciona.

## Diapositiva 15 — Acción: descubrir IDs con el ticket modelo

**Guión:**
- El ticket modelo: una historia con los 4 campos llenos, cada uno con un texto reconocible. Hoy
  usan el del proyecto de práctica; en su empresa crean el suyo.
- `acli` no devuelve nombres: un campo vacío no aparece, uno lleno se reconoce por su contenido.
- Comando: `acli jira workitem view CLAVE-N --fields "*all" --json | jq '...'`.
- Gotcha: sin `--fields "*all"` la vista default no trae ningún `customfield_`.
- Alternativas sin código: URL del admin al editar el campo, o `curl .../rest/api/3/field`.

## Diapositiva 16 — Acción: guardar IDs en `jira-fields.json`

**Guión:**
- Cada `customfield_NNNNN` va en su clave de `.claude/jira-fields.json`
  (`acceptance_criteria`, `business_rules_specification`, `scope`, `out_of_scope`).
- La skill que crea historias (00d) lee los IDs de ahí, nunca los escribe fijos.
- La clave del proyecto va en `CLAUDE.md`, sección `## Proyecto Jira`.
- Hoy: el repo ya trae los IDs y la clave de práctica; verifican que coinciden con el ticket
  modelo. En su empresa: los cargan en su repo de trabajo, no en el clon del curso.
- Verificación: `jq -r 'to_entries[] | "\(.key): \(.value.id)"' .claude/jira-fields.json`
  muestra 4 IDs, ninguno vacío.

## Diapositiva 17 — Gotcha: editar un custom field ya creado

**Guión:**
- `acli jira workitem edit` rechaza cualquier custom field: error duro, exit 1.
- Los custom fields solo se llenan en `create`, nunca en `edit`.
- Para corregir uno en un ticket existente: REST `PUT` con `curl` y las variables
  `ATLASSIAN_EMAIL` / `ATLASSIAN_API_TOKEN`.
- Por eso `bun run claude`: sin variables, el `curl` sale vacío. Se usa en el 00d.

## Diapositiva 18 — Cierre y checklist

**Guión:**
- Explican qué es un MCP y en qué se diferencia de un CLI.
- Abren Claude con `bun run claude`, con sus variables cargadas.
- Tienen Context7 y Bun instalados; `acli` y Atlassian MCP conectados con su API Token.
- Verificaron los IDs de `.claude/jira-fields.json` contra el ticket modelo de práctica.
- Saben crear campos Párrafo, statuses y ticket modelo en el Jira de su empresa.
- Prueba de punta a punta: con la skill `acli`, el asistente empareja los campos del ticket
  modelo con `jira-fields.json`.
- La próxima clase: crear historias con contexto manual (Tema 00d).
