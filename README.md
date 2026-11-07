# curso-ai-powered

Repositorio de trabajo del curso **AI-Powered QA**. Se construye de forma incremental, tema a
tema: **solo contiene lo que ya viste en clase**. Después de cada clase se agregan las piezas
correspondientes a ese tema.

## Estado actual

Temas dictados: **00a**, **00b**, **00c** y **00d**.

- **Tema 00a — Terminal, CLI y primer asistente IA:** instalaste Warp, Node.js y un runner de IA
  (Claude Code CLI o Codex CLI). Este repo tiene `README.md` y `CLAUDE.md` (memoria de IA).
- **Tema 00b — Git, GitHub y Skill:** este repo es tu clon de GitHub, ya con `.git` inicializado.
  Trabajás con el flujo diario `git fetch` → `git status` → `git pull` → `git add` →
  `git commit` → `git push`. También viste ingeniería de prompt (Tarea, Contexto, Restricciones,
  Formato) y qué es una skill. El `.gitignore` evita subir `.env` y `node_modules/`.
- **Tema 00c — Jira conectado:** tu asistente lee el proyecto Jira de práctica con `acli` (camino
  principal) y Atlassian MCP (respaldo). Instalaste Context7 CLI, Bun, `acli`, `jq` y `uv`, y
  completaste tu `.env` con tu API Token. Abrís Claude con `bun run claude` para que reciba esas
  variables. Este repo suma `package.json`, `.env.example`, `.mcp.json`, la skill `acli` y los IDs
  de los campos de Story del Jira de práctica en `.claude/jira-fields.json`. También tenés la
  receta para preparar el Jira de tu empresa (campos Párrafo, statuses, ticket modelo).
- **Tema 00d — Crear épicas e historias en Jira:** viste la anatomía de una historia
  (título `{Funcionalidad} | {Acción}`, Como/Quiero/Para, campos separados, criterios de
  aceptación en Gherkin en voz de negocio) y la diferencia entre épica e historia. Con la skill
  `product-management` creaste en el Jira de práctica una épica de Toolshop con sus historias y una
  historia extra, respondiendo la entrevista desde la app. Este repo suma esa skill.

## Estructura

```
curso-ai-powered/
├── README.md, CLAUDE.md              # este archivo y la memoria de tu asistente de IA
├── .gitignore                        # qué NO versiona Git (.env, node_modules/)
├── .gitattributes                    # finales de línea LF en todos los sistemas operativos
├── package.json, bun.lock            # dependencias y script `bun run claude`
├── .env.example                      # plantilla de variables (tu copia con valores: .env)
├── .mcp.json                         # MCP del proyecto: atlassian
├── .claude/
│   ├── skills/acli/                  # skill para operar Jira desde la terminal
│   ├── skills/product-management/    # skill para crear épicas e historias en Jira
│   ├── jira-fields.json              # IDs de los 4 campos de Story del Jira de práctica
│   └── jira-required.yaml            # campos y statuses que necesita el proyecto Jira
├── temario/                          # material de cada clase ya dictada
│   ├── 00a-terminal-cli-ia/          # contenido.md, guion-diapositivas.md, deck/ (presentación)
│   ├── 00b-git-github-skill/         # ídem
│   ├── 00c-jira-conectado/           # ídem
│   ├── 00d-creacion-historias/       # ídem
│   └── apuntes/                      # apuntes tomados durante las clases, un .md por tema
└── info-dev/                         # material para coordinar con el equipo de desarrollo
    ├── guia-dev-contexto.md          # guía para el equipo dev
    └── contexto-dev/                 # prompts que el equipo dev usa para generar contexto
```

- Para ver una presentación, abrí `temario/<tema>/deck/index.html` en el navegador.
- Tus apuntes de clase están en `temario/apuntes/<tema>.md`.
- Para trabajar con Jira: `bun install` una vez, completá tu `.env` a partir de `.env.example` y
  abrí Claude con `bun run claude`.

## Cómo se actualiza

Cada vez que se dicta una clase se agrega al repo la carpeta de ese tema en `temario/`, sus
apuntes en `temario/apuntes/` y las herramientas o archivos que esa clase habilita. Hacé
`git pull` al empezar cada día para traer lo nuevo.
