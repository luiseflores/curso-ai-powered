# Guión de diapositivas — Tema 00a: Terminal, CLI y primer asistente IA

> Mismo orden intercalado que `contenido.md`. Cada diapositiva de "acción" se dicta con la
> máquina del alumno abierta — pausa para que instale/pruebe antes de seguir.

## Diapositiva 1 — Portada

**Título en pantalla:** "Tema 00a — Terminal, CLI y tu primer asistente de IA"

**Guión:** Hoy dejamos tu máquina lista para trabajar con IA desde la terminal. Vamos a instalar
lo mínimo indispensable y, en el camino, vas a entender qué es cada pieza que estás instalando —
no instalamos "porque sí".

## Diapositiva 2 — ¿Qué es una terminal?

**Guión:** Una terminal es un programa donde escribís comandos de texto en vez de clickear
botones. Es el lugar donde vas a vivir el resto del curso — tanto para hablar con tu asistente de
IA como, más adelante, para git.

## Diapositiva 3 — Acción: instalar Warp

**Guión:** Vamos a instalar Warp, la terminal que usamos en el curso. Abran
`guia-instalacion.md`, sección "Terminal", y sigan los pasos de su sistema operativo. Les doy 3-5
minutos — avisen si algo no corre igual que en la guía.

## Diapositiva 4 — ¿Qué es un CLI?

**Guión:** CLI es "Command Line Interface" — un programa que se maneja solo con comandos de
texto, sin ventanas. Corre DENTRO de la terminal que acaban de instalar. El asistente de IA que
vamos a usar hoy es, justamente, un CLI.

## Diapositiva 5 — Acción: instalar Node.js

**Guión:** El CLI del asistente necesita Node.js para correr. Instálenlo siguiendo la guía
(sección "Git, Node.js y VS Code" — solo la parte de Node.js) y verifiquen con `node --version` y
`npm --version`.

## Diapositiva 6 — Acción: elegí e instalá tu runner de IA

**Guión:** Ahora sí, el CLI del asistente. Elijan UNO: Claude Code CLI o Codex CLI — para este
curso son equivalentes, no hace falta instalar los dos. Sigan la sub-sección "Runner de IA — elegí
uno" de la guía.

## Diapositiva 7 — Verificación: probá que responde

**Guión:** Dentro del CLI que instalaron, escriban "hola" y confirmen que el asistente responde.
Si no arranca, revisamos juntos antes de seguir — esto es la base de todo lo que viene.

## Diapositiva 8 — ¿Qué es un token?

**Guión:** Un token es la unidad mínima de texto que procesa el modelo — no es exactamente una
palabra, puede ser un pedazo de palabra o un signo de puntuación. Todo lo que le mandan y todo lo
que les responde se mide en tokens.

## Diapositiva 9 — ¿Qué es la ventana de contexto?

**Guión:** Es la cantidad máxima de tokens que el asistente puede tener "en la cabeza" a la vez.
Si la conversación es muy larga, puede llenarse — y el asistente empieza a perder detalle de lo
que se dijo al principio.

## Diapositiva 10 — Créditos: sesión de 5hrs vs. semanales

**Guión:** Según el plan que tengan, los créditos se resetean cada bloque de 5 horas de uso
activo, o una vez por semana. Conviene que revisen cuál es el modelo de su plan para no quedarse
sin crédito en medio de una tarea real.

## Diapositiva 11 — ¿Qué es CLAUDE.md / AGENTS.md?

**Guión:** Es un archivo de texto que el CLI lee automáticamente al arrancar en una carpeta —
instrucciones persistentes para que el asistente sepa cómo trabajar en ESE proyecto, sin que se
las tengan que repetir en cada mensaje.

## Diapositiva 12 — Acción: creá tu CLAUDE.md / AGENTS.md

**Guión:** Creen el archivo (el nombre depende del runner que eligieron) en la carpeta de trabajo
del curso, con 2-3 líneas simples — por ejemplo, en qué idioma quieren que les responda.
Reinicien el CLI y pregúntenle algo que dependa de eso para confirmar que lo está leyendo.

## Diapositiva 13 — ¿Qué es README.md?

**Guión:** Es el archivo que explica de qué se trata un repositorio — lo primero que lee
cualquiera al entrar a la carpeta. Esta carpeta se va a convertir en un repositorio git más
adelante en el curso, así que arrancamos el hábito desde ya.

## Diapositiva 14 — Acción: creá tu README.md

**Guión:** Creen un `README.md` con un título y 1-2 líneas contando para qué es esta carpeta.

## Diapositiva 15 — Cierre y checklist

**Guión:** Repasemos: Warp instalado, Node.js instalado, su runner de IA instalado y logueado,
`CLAUDE.md`/`AGENTS.md` propio, `README.md` propio. Con esto ya tienen el punto de partida para
la próxima clase.
