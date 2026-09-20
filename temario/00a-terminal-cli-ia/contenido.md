# Contenido — Tema 00a: Terminal, CLI y primer asistente IA

> Orden intercalado: cada concepto va seguido de la acción práctica que lo pone en uso. Fuente
> práctica: `docs/cursos/ai-powered-qa/guia-instalacion.md`, Sección A (recortada — sin Git, VS
> Code ni Context7 MCP, que quedan para un tema futuro).

## 1. Terminal

**Qué es:** programa que te deja escribir comandos de texto en vez de clickear en una interfaz
gráfica. Es el lugar donde vas a trabajar el resto del curso.

**Acción — instalar Warp:** seguir `guia-instalacion.md`, sub-sección "Terminal". Linux: instalar
el `.deb` y abrir desde el menú de aplicaciones. Windows: elegir una de las 3 opciones
documentadas ahí (PowerShell/Windows Terminal nativo, Warp sin WSL2, o Warp + WSL2).

**Verificación:** abrir Warp y confirmar que aparece una línea de comandos funcional.

## 2. CLI (Command Line Interface)

**Qué es:** un programa con el que interactuás solo con comandos de texto (sin ventanas ni
botones), que corre DENTRO de la terminal. El asistente de IA que vas a usar en este curso es un
CLI.

**Acción — instalar Node.js:** el runtime que necesita el CLI del asistente. Seguir
`guia-instalacion.md`, sub-sección "Git, Node.js y VS Code" (solo la parte de Node.js LTS).
Verificar con `node --version` y `npm --version`.

**Acción — elegir e instalar tu runner de IA:** Claude Code CLI **o** Codex CLI, uno solo (son
intercambiables para este curso). Seguir `guia-instalacion.md`, sub-sección "Runner de IA — elegí
uno".

**Verificación práctica:** dentro del CLI elegido, escribir un mensaje simple ("hola") y
confirmar que responde. (Esto es lo mismo que la guía de instalación llama "smoke test" — acá
todavía no usamos ese término formalmente, porque en el glosario del curso "smoke test" se define
recién cuando se suma MCP, más adelante.)

## 3. Token

**Qué es:** la unidad mínima de texto que procesa un modelo de IA — no es exactamente una
palabra: puede ser una palabra completa, un pedazo de palabra, o un signo de puntuación. Cada
mensaje que mandás y cada respuesta que recibís se mide en tokens.

**Por qué importa:** el consumo del asistente (créditos, límites) se mide en tokens, no en
"mensajes" ni en "minutos de uso".

## 4. Ventana de contexto

**Qué es:** la cantidad máxima de tokens que el asistente puede "tener en la cabeza" al mismo
tiempo. Tu conversación completa — tus mensajes, los archivos que leyó, sus respuestas — tiene
que entrar ahí.

**Por qué importa:** una conversación muy larga puede llenar la ventana de contexto; el asistente
puede perder detalle de lo que se dijo al principio de la sesión.

## 5. Créditos por sesión (5hrs) vs. créditos semanales

**Qué es:** dos modelos de consumo distintos según el plan/proveedor — créditos que se resetean
cada bloque de 5 horas de uso activo, vs. créditos que se resetean una vez por semana.

**Por qué importa:** saber cuál modelo tiene tu plan (Claude Pro, o ChatGPT Plus/Codex) evita
quedarte sin crédito a mitad de una tarea real de trabajo.

## 6. CLAUDE.md / AGENTS.md (memoria de proyecto)

**Qué es:** un archivo de texto en la raíz de tu carpeta de trabajo que el CLI lee
automáticamente al arrancar — instrucciones persistentes para que el asistente sepa cómo trabajar
en ESE proyecto (idioma, convenciones, contexto), sin que se las repitas en cada mensaje.

**Acción:** crear tu propio `CLAUDE.md` (o `AGENTS.md`, según el runner que elegiste) en la
carpeta de trabajo del curso, con 2-3 líneas simples (ej: en qué idioma querés que te responda,
qué es esta carpeta).

**Verificación:** reiniciar el CLI dentro de esa carpeta y confirmar que respeta lo que pusiste
en el archivo (ej: si pediste que responda en español, comprobar que lo hace).

## 7. README.md (buena práctica de repositorio)

**Qué es:** archivo de texto en la raíz de un repositorio que explica de qué se trata — lo
primero que lee cualquier persona (o vos mismo, más adelante) al entrar a la carpeta. Esta
carpeta de trabajo se va a convertir en un repositorio git más adelante en el curso, así que
arrancamos el hábito desde ahora.

**Acción:** crear tu propio `README.md` con un título y 1-2 líneas describiendo para qué es la
carpeta.

## Cierre del tema

Al terminar este tema, el alumno tiene: Warp instalado, Node.js instalado, un runner de IA
(Claude Code CLI o Codex CLI) instalado y logueado, y su carpeta de trabajo con `CLAUDE.md` (o
`AGENTS.md`) + `README.md` propios.
