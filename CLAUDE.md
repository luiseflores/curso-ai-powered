# CLAUDE.md: Memoria Persistente de la IA

> Este archivo es lo que Claude Code lee automáticamente al arrancar para entender este
> repositorio. Se va actualizando a medida que el curso agrega skills y flujos de trabajo.

## Por qué existe

Un asistente de IA no tiene memoria entre sesiones por default: cada vez que lo abrís, arranca de
cero. `CLAUDE.md` resuelve eso: es el primer archivo que el asistente lee al arrancar, y le da
contexto persistente del proyecto sin que se lo tengas que repetir cada vez.

## Qué es este repositorio

Repo del curso **AI-Powered QA**. Se construye por temas y solo contiene lo que ya se vio en clase
más la configuración del tema en curso. Temas dictados hasta ahora: 00a (terminal, CLI, primer
asistente de IA) y 00b (Git, GitHub, skills). Tema en curso: 00c (Jira conectado).

## Estructura

- `README.md`: descripción del repo y estado actual.
- `CLAUDE.md`: este archivo.
- `.gitignore`: excluye `.env` y `node_modules/`.
- `.gitattributes`: fuerza finales de línea LF en todos los sistemas operativos.
- `package.json` / `bun.lock`: dependencias del repo y script `claude` (`bun run claude` abre Claude
  con las variables de `.env` cargadas).
- `.env.example`: plantilla de variables, sin valores. Tus credenciales van en `.env`.
- `.mcp.json`: MCP del proyecto. Por ahora solo `atlassian`.
- `.claude/skills/acli/`: skill para operar Jira desde la terminal y conversor `md-to-adf.ts`.
- `.claude/jira-fields.json` y `.claude/jira-required.yaml`: ver secciones de abajo.
- `temario/<tema>/`: material de cada clase dictada (`contenido.md`, `guion-diapositivas.md`,
  `deck/index.html`). Sirve de consulta; no es código del proyecto.
- `temario/apuntes/<tema>.md`: apuntes de cada clase, tomados durante la meeting.
- `info-dev/`: `guia-dev-contexto.md` y `contexto-dev/`, material para coordinar con el equipo de
  desarrollo del producto.

## Reglas de trabajo con Git

- En este repo solo se trae lo nuevo: `git pull` al empezar. Los archivos versionados no se
  modifican, ni se hace `commit` o `push`: el contenido lo actualiza el instructor.
- Lo que aplica a tu empresa (clave, IDs de campos) va en tu propio repo de trabajo.
- Nunca versiones `.env` ni credenciales.

## Qué es `.claude/jira-fields.json`

IDs (`customfield_NNNNN`) de los 4 custom fields de Story del Jira de práctica: criterios de
aceptación, reglas de negocio, alcance y fuera de alcance. Se descubren con el ticket modelo.

## Proyecto Jira

Clave del proyecto: `TP`.

Ticket modelo: TP-2

## Qué es `.claude/jira-required.yaml`

Checklist de custom fields y statuses que el proyecto Jira necesita (2 work types: Épica y Story).
Se audita con `acli` + `jq` + `curl`: procedimiento completo en
`.claude/skills/acli/references/jira-check.md`.

## Resolución de herramientas (tags `[TAG_TOOL]`)

Las referencias de las skills usan pseudocódigo `[TAG_TOOL]` para nombrar qué herramienta
corresponde a cada tarea. Resolución vigente en este repo:

| Tag | Para qué | Herramienta |
|---|---|---|
| `[ISSUE_TRACKER_TOOL]` | Leer y escribir tickets de Jira | `acli` (skill `acli`); respaldo: Atlassian MCP |

## Ver también

- `.claude/skills/acli/SKILL.md`: cómo operar Jira desde la terminal y auditar el proyecto contra
  `.claude/jira-required.yaml`.
