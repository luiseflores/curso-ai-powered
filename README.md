# curso-ai-powered

Repositorio de trabajo del curso **AI-Powered QA**. Se construye de forma incremental, tema a
tema: **solo contiene lo que ya viste en clase**. Después de cada clase se agregan las piezas
correspondientes a ese tema.

## Estado actual

Temas dictados: **00a** y **00b**.

- **Tema 00a — Terminal, CLI y primer asistente IA:** instalaste Warp, Node.js y un runner de IA
  (Claude Code CLI o Codex CLI). Este repo tiene `README.md` y `CLAUDE.md` (memoria de IA).
- **Tema 00b — Git, GitHub y Skill:** este repo es tu clon de GitHub, ya con `.git` inicializado.
  Trabajás con el flujo diario `git fetch` → `git status` → `git pull` → `git add` →
  `git commit` → `git push`. También viste ingeniería de prompt (Tarea, Contexto, Restricciones,
  Formato) y qué es una skill. El `.gitignore` evita subir `.env` y `node_modules/`.

## Estructura

```
curso-ai-powered/
├── README.md, CLAUDE.md              # este archivo y la memoria de tu asistente de IA
├── .gitignore                        # qué NO versiona Git (.env, node_modules/)
├── temario/                          # material de cada clase ya dictada
│   ├── 00a-terminal-cli-ia/          # contenido.md, guion-diapositivas.md, deck/ (presentación)
│   ├── 00b-git-github-skill/         # ídem
│   └── apuntes/                      # apuntes tomados durante las clases, un .md por tema
└── info-dev/                         # material para coordinar con el equipo de desarrollo
    ├── guia-dev-contexto.md          # guía para el equipo dev
    └── contexto-dev/                 # prompts que el equipo dev usa para generar contexto
```

- Para ver una presentación, abrí `temario/<tema>/deck/index.html` en el navegador.
- Tus apuntes de clase están en `temario/apuntes/<tema>.md`.

## Cómo se actualiza

Cada vez que se dicta una clase se agrega al repo la carpeta de ese tema en `temario/`, sus
apuntes en `temario/apuntes/` y las herramientas o archivos que esa clase habilita. Hacé
`git pull` al empezar cada día para traer lo nuevo.
