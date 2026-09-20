# Generador del Glosario de Dominio

> **Orden de generación: 2º de 5.** Generá este documento después de `data.md` — reusa las
> entidades que ya documentaste ahí y las profundiza en terminología, reglas y estados. Ver
> `README.md` de esta carpeta para el orden completo.

Genera o actualiza `.context/business/domain-glossary.md` — el glosario que traduce el lenguaje
técnico del código al lenguaje de negocio.

**Objetivo**: analizá el producto completo (o el mismo recorte que usaste en `data.md`, si acotaste
el análisis).

---

## Qué produce esto

Un único documento que traduce el lenguaje técnico del código al lenguaje de negocio que va a usar
el QA para hablar de ambigüedades en criterios de aceptación:
- Terminología técnica↔negocio por entidad
- Significado de negocio de cada valor de enumeración
- Reglas de negocio con ejemplo Given/When/Then
- Diagrama de estados por entidad con ciclo de vida
- Mapeo de labels de UI a los campos/acciones que representan

Este documento es el que un QA usa para decir "esta AC es ambigua" con fundamento, no por
intuición — cruza cada término dudoso contra la definición canónica acá.

---

## Fuentes (usá TODAS las disponibles)

| Fuente | Qué extraer | Herramienta |
|--------|----------------|------|
| `business-data-map.md` ya generado | Lista de entidades de partida — no las vuelvas a descubrir, profundizalas | Leer directo |
| Esquema de base de datos | Constraints, enums, tipos de columna | DBHub MCP (o tu cliente de DB) |
| Código backend | Reglas de validación, mensajes de error, enums de código | Leé el repositorio de backend — servicios, modelos, constantes |
| Archivos de i18n / traducciones | Labels de UI reales (no el fallback hardcodeado en el componente) | `src/locales/*.json`, `public/locales/**/*.json` si existen |
| Componentes de frontend | Labels de UI cuando no hay i18n | Leé formularios y botones de acción |

**Regla de oro**: cada término del glosario tiene que rastrear a una fuente concreta (archivo,
tabla, enum). Un término sin fuente no entra al glosario — va a Discovery Gaps.

---

## Detección de modo

```
¿Existe .context/business/domain-glossary.md?
  → NO:  generalo desde cero
  → SÍ:  generá una nueva versión, mostrame el resumen del diff, y
         esperá mi confirmación antes de sobreescribir. NUNCA
         sobreescribas automáticamente.
```

---

## Si falta `business-data-map.md`

Este documento pierde calidad sin él — avisame ("voy a re-descubrir entidades desde cero en vez de
profundizar las que ya tenés"), seguí adelante, y registrá la limitación en §Discovery Gaps.
Preferí siempre generar primero `data.md` (ver `README.md` de esta carpeta).

---

## Fases de exploración

### Fase 1 — Terminología técnica↔negocio

Para cada entidad de `business-data-map.md` (o descubierta si el paso anterior no existe):
- ¿Cómo se llama en el código (variable, tabla, clase)?
- ¿Cómo la llama el negocio (la persona que usa el sistema)?
- Ejemplo: `user` en el código, "Cliente" para el negocio.

### Fase 2 — Enumeraciones y constantes

Para cada enum/constante de estado o categoría encontrado en código o DB:
- Valor exacto tal como aparece en código (`PENDING`, `ACTIVE`)
- Qué significa en términos de negocio
- En qué contexto se usa

### Fase 3 — Reglas de negocio

Para cada regla de validación encontrada (código de validación, mensajes de error, constraints de
DB):
- Qué exige la regla
- A qué entidad(es) afecta
- Mensaje de error exacto que ve el usuario
- Un ejemplo Given/When/Then de la regla en acción

### Fase 4 — Estados y transiciones

Para cada entidad con más de 2 estados de ciclo de vida:
- Todos los estados posibles
- Transiciones válidas entre ellos
- Qué evento dispara cada transición

### Fase 5 — Labels de UI

Para cada formulario/acción principal:
- Label que ve el usuario en pantalla
- Campo o acción técnica que representa
- Preferí el archivo de i18n real sobre el texto hardcodeado en el componente, si existe

---

## Estructura de la salida

Escribí `.context/business/domain-glossary.md` con esta estructura:

1. **Entidades núcleo** — una subsección por entidad: tabla `Nombre técnico | Nombre de negocio |
   Descripción | Dónde se encontró`, más relaciones (tiene muchos / pertenece a)
2. **Enumeraciones y constantes** — una tabla por enum: `Valor | Significado de negocio | Contexto
   de uso`, con archivo de origen
3. **Reglas de negocio** — una subsección por regla: Descripción, Entidades afectadas, Mensaje de
   error, Dónde se encontró, ejemplo Given/When/Then
4. **Diagrama de relaciones** — ASCII, mismo estilo que `business-data-map.md`
5. **Mapeo de terminología** — tabla técnico→negocio + tabla de abreviaturas/siglas si existen
6. **Flujos de estado** — un diagrama ASCII de estados por entidad con más de 2 estados
7. **Referencia de labels de UI** — tabla de campos de formulario + tabla de botones de acción
8. **Discovery gaps** — OBLIGATORIO. Términos o reglas que no pudiste verificar.
9. **Guía de uso para QA** — 3-5 bullets de cómo usar este documento al refinar historias o
   diseñar pruebas (ejemplo: "antes de marcar una AC como ambigua, revisá si el término en
   cuestión ya tiene definición acá").

Agregá al tope del archivo la línea `> Última verificación: YYYY-MM-DD` (fecha de hoy).

---

## Después de generar

- Si el archivo ya existía: mostrame el resumen del diff y esperá mi confirmación explícita antes
  de sobreescribir.
- Reportá: entidades documentadas, reglas de negocio encontradas, estados mapeados, discovery
  gaps.

---

Cuando termines con este, seguí con `personas.md` (3º de 5).
