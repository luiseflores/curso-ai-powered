# AGENTS.md: Memoria Persistente de la IA

> Este archivo es lo que tu asistente de IA (Claude Code, Codex u otro) lee automáticamente al
> arrancar para entender este repositorio. Se va actualizando a medida que el curso agrega
> skills y flujos de trabajo.

## Por qué existe

Un asistente de IA no tiene memoria entre sesiones por default: cada vez que lo abrís, arranca de
cero. `AGENTS.md` (y su alias `CLAUDE.md`, si tu asistente es Claude Code) resuelve eso — es el
primer archivo que el asistente lee al arrancar, y le da contexto persistente del proyecto sin que
se lo tengas que repetir cada vez.

## Qué es este repositorio

Repo de trabajo del curso **AI-Powered QA**. Es un clon de un repositorio de GitHub: ya tiene
`.git` y remoto `origin`. Se construye por temas y solo contiene lo que el alumno ya vio en clase.
Temas dictados hasta ahora: 00a (terminal, CLI, primer asistente de IA) y 00b (Git, GitHub, skills).

## Estructura

- `README.md`: descripción del repo y estado actual.
- `AGENTS.md` / `CLAUDE.md`: este archivo (`CLAUDE.md` solo importa a `AGENTS.md`).
- `.gitignore`: excluye `.env` y `node_modules/`.
- `temario/<tema>/`: material de cada clase dictada (`contenido.md`, `guion-diapositivas.md`,
  `deck/index.html`). Sirve de consulta; no es código del proyecto.
- `temario/apuntes/<tema>.md`: apuntes de cada clase, tomados durante la meeting.
- `info-dev/`: `guia-dev-contexto.md` y `contexto-dev/`, material para coordinar con el equipo de
  desarrollo del producto.

## Reglas de trabajo con Git

- Flujo diario: `git fetch` → `git status` → `git pull` al empezar; `git add .` →
  `git commit -m "mensaje"` → `git push` al terminar.
- Nunca versiones `.env` ni credenciales.

## Qué va a tener más adelante

Todavía no hay skills ni flujos de trabajo definidos en este repo — eso llega en los próximos
temas del curso.
