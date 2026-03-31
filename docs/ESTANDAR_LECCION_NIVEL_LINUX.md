# Estándar de lección (nivel Linux)

Este documento define la barra de calidad para lecciones en Arborito Library cuando el objetivo es **paridad con el curso de Linux**: capítulos legibles de principio a fin, con sustancia técnica y voz de manual, no listas de instrucciones vacías.

## Referencias de ejemplo

- Español: [`01_Linux_Jr/02_Modulo_2/01_Navegacion.md`](../content/rolling/ES/01_Ingenieria_y_Tecnologia/01_Sistemas_IT/01_Linux/01_Linux_Jr/02_Modulo_2/01_Navegacion.md) — narrativa larga, mapa LPIC, comandos, errores frecuentes.
- Español: [`01_Linux_Jr/01_Modulo_1/03_Instalacion.md`](../content/rolling/ES/01_Ingenieria_y_Tecnologia/01_Sistemas_IT/01_Linux/01_Linux_Jr/01_Modulo_1/03_Instalacion.md) — secciones extensas, tablas, laboratorio.

## Checklist obligatoria

1. **Frontmatter:** `@title`, `@icon`, `@description`, `@order` coherentes con el tema; `@description` útil en el índice (no genérico vacío).
2. **Cuerpo:** título `#` alineado con el `@title` (sin contradicciones).
3. **Sustancia:** definiciones correctas; ejemplos reales (comandos, YAML, HCL, pseudocódigo, tablas, diagramas ASCII). En **idiomas**, la sustancia son explicaciones lingüísticas, tablas léxicas o de gramática, diálogos modelo y contrastes con la L1 — no comandos de sistema. Prohibido sustituir la enseñanza con frases del tipo «desarrolla el tema en tres capas» sin contenido debajo.
4. **Extensión orientativa:** ~150–400+ líneas para temas centrales; temas muy acotados pueden ser algo más cortos, pero **nunca** una sola página de plantilla.
5. **Secciones:** usar `@section:` con títulos descriptivos; numerar subsecciones si el tema lo requiere (`###`, listas).
6. **Voz:** tutorial continuo (como Linux), tono profesional y claro.
7. **Evaluación:** `@quiz:` donde encaje (opcional pero recomendado en temas factuales).
8. **Paridad ES/EN:** cada lección en `rolling/ES/01_Ingenieria_y_Tecnologia/...` debe tener el equivalente en `rolling/EN/01_Engineering/...` (mismo orden y tema; los nombres de archivo pueden diferir). En **Humanidades / idiomas**, el par está en `rolling/EN/08_Humanities_Languages/...` (no en Engineering).

## Qué evitar

- Plantillas rellenadas solo cambiando el título del tema.
- Scripts que generen cuerpos de lección como contenido final sin revisión.
- Enlaces rotos o “pronto ampliaremos” como sustituto de contenido en lecciones marcadas como completas.

## Mantenimiento

Las revisiones mayores de una lección deberían mantener el mismo nivel de detalle; si se acorta, documentar el motivo en el commit o en nota de mantenimiento.
