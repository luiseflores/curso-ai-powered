# Vincular dependencias

> Refleja las dependencias entre historias como links reales de Jira. Se corre después de crear
> historias (workflows B y C), aunque el resultado sea "sin
> dependencias".

## Búsqueda activa (I18)

1. Listá las historias de la misma épica con `[ISSUE_TRACKER_TOOL]` (JQL
   `parent = <CLAVE-ÉPICA>`).
2. Preguntale al usuario: "¿Alguna de estas historias tiene que estar terminada antes que la
   nueva, o la nueva bloquea a alguna?". Lo que confirma el usuario es la única fuente válida.
3. Descartá el ruido: prerrequisitos globales ("tiene que existir el login", "tiene que haber base
   de datos") no son dependencias entre historias. Heurística: si desaparece al reordenar el
   trabajo, es ruido.
4. Mostrá la matriz (desde, hasta, tipo) y esperá confirmación antes de crear links.

## Tipos de link

El proyecto no tiene un link type `Dependencies` dedicado. Se usan los built-in de Jira:

| Necesidad | Tipo | Nota |
|---|---|---|
| Dependencia dura ("A no puede empezar sin B") | `Blocks` (B blocks A) | Built-in de Jira |
| Relación informativa | `Relates` | Simétrico: pierde la dirección. Nunca para orden (I13); si se usa, avisá la degradación |

## Procedimiento

1. Por cada dependencia confirmada, creá el link con `[ISSUE_TRACKER_TOOL]`, un link por llamada.
   La dirección de los links en `acli` es contraintuitiva: seguí la receta `link create` de
   `.claude/skills/acli/references/workitem.md`.
2. **Verificá la dirección (OBLIGATORIO)** listando los links del ticket que bloquea. Si quedó al
   revés: borrá el link y recrealo según esa receta.
3. Mostrá la matriz final.

## Plantilla de salida

```markdown
## Matriz de dependencias: <CLAVE-ÉPICA>

| Desde | Hasta | Tipo | Dirección verificada |
|---|---|---|---|
| CLAVE-12 | CLAVE-13 | Blocks | sí |

### Avisos
- ninguno (o: "se usó Relates en vez de Blocks: se pierde la dirección")
```

Sin dependencias: reportá "Sin dependencias".

## Reglas duras

- NUNCA inventes dependencias: solo las que confirmó el usuario.
- NUNCA uses `Relates` para orden.
- NUNCA crees varios links sin verificar la dirección de cada uno.
