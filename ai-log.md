# AI Log - Equipo Lobo Ensambladores

## Herramientas

- **Claude (Anthropic)** — claude.ai, modelo Sonnet

## Filosofía de uso

La IA se usó como herramienta de apoyo técnico, no como generador de ideas o conclusiones. Todas las decisiones sobre pregunta central, selección de datos, enfoque narrativo y hallazgos fueron tomadas por el equipo. La IA ayudó a resolver problemas de código, depurar errores y estructurar documentación. El equipo revisó, validó y adaptó cada salida antes de integrarla al proyecto.

## Registro de uso

---
### 2026-04-03 | Claude (Anthropic) | Resolución de errores de variables entre notebooks

- **Tarea:** Al separar la lógica en tres notebooks independientes, varias celdas de `03_graficas` fallaban con `NameError` porque usaban variables (`ult`, `datos`, `pivot_ext`) que solo existían en memoria del `02_calculo`.
- **Prompt:** "En el 03_graficas me sale NameError: name 'ult' is not defined y NameError: name 'datos' is not defined. El 02 ya corrió y guardó los CSV. ¿Cómo corrijo el 03 para que lea todo desde los archivos y no dependa de variables del 02?"
- **Resultado:** Se reescribieron las celdas afectadas para que leyeran directamente desde `datos/analisis/` usando `Path.glob()` para detectar el año en el nombre del archivo. El `03_graficas` quedó completamente autónomo: solo necesita los CSV en disco, no el kernel del `02`.
- **Decisión:** Se adoptó el patrón de lectura desde archivo en todas las celdas del `03`. Se documentó en el README que los notebooks deben correrse en orden pero no en la misma sesión.


---

### 2026-04-05 | Claude (Anthropic) | Diseño y mejora de las visualizaciones con datos reales

- **Tarea:** Con los 4 CSV de análisis ya calculados (`serie_nacional`, `brecha_sector_anio`, `brecha_educ_sector`, `informalidad_contexto`), se buscó pasar de gráficas de prueba a visualizaciones con narrativa clara para el tablero.
- **Prompt:** "Ya tengo los 4 CSV de análisis. Quiero 4 gráficas: una línea temporal de ingreso por hora, barras horizontales de brecha por sector, un heatmap de brecha por educación y sector, y barras agrupadas con informalidad como anotación contextual. Dame el código Plotly con los colores del proyecto y que los HTML sean ligeros para el repo."
- **Resultado:** Se generaron G1–G4 con paleta consistente (`#378ADD` hombres, `#D4537E` mujeres), área sombreada de brecha en G1, anotación de COVID-2020, heatmap con escala divergente RdBu_r centrada en 0, e informalidad como anotación de texto (no gráfica separada, como recomendó la mentoría). Los HTML se exportaron con `include_plotlyjs='cdn'` para pesar ~20 KB en lugar de 4.4 MB.
- **Decisión:** Se usaron las 4 gráficas. Se ajustó manualmente el rango del eje X en G2 y el orden de sectores en G4 para mejorar la lectura.

---


### 2026-04-11 | Claude (Anthropic) | Corrección del factor de expansión en la pipeline de datos

- **Tarea:** Los archivos crudos de la ENOE usan dos nombres distintos para la columna del factor de expansión (`fac` en archivos hasta 2021-T1 y `fac_tri` en archivos de 2021-T2 en adelante, según las notas de microdatos del INEGI). Al unir todos los trimestres, la columna quedaba con NaN en la mayoría de los registros porque el código no detectaba la variante correcta.
- **Prompt:** "El factor de expansión de algunos archivos se llama `fac` y en otros `fac_tri`. Al hacer el merge todos los registros viejos quedan en NaN. ¿Cómo normalizo esto en el filtrado para que el 02_calculo siempre reciba la columna con el mismo nombre?"
- **Resultado:** Se implementó detección automática en `01_filtrado.ipynb`: el script lee el encabezado del archivo, detecta qué variante existe y renombra siempre a `fac_tri` antes de guardar. También se añadió limpieza de registros con `fac_tri == 0` (viviendas sin entrevista). El `02_calculo` dejó de necesitar lógica de detección.
- **Decisión:** Se adoptó la solución. Se verificó manualmente que los archivos de 2005 y 2025 producían valores consistentes después del cambio.

---

### 2026-04-12 | Claude (Anthropic) | Estructura de documentación y reproducibilidad del repositorio

- **Tarea:** El equipo necesitaba asegurar que el repositorio fuera reproducible para el jurado sin exponer datos pesados (~12 GB de crudos), cumplir la estructura de carpetas de la rúbrica (A4) y documentar metadatos de fuentes (A3).
- **Prompt:** "¿Cómo estructuro el repo para que sea reproducible, no suba los datos crudos a GitHub y el README cumpla con metadatos de fuente, licencia y descripción de variables para la rúbrica del HackODS?"
- **Resultado:** Se definió la estructura final del repo (`datos/analisis/` en el repo, `datos/DATOS MASIVOS ENOE/` y `datos/filtrado/` en `.gitignore`), se escribió el README con tabla de metadatos completa (fuente, URL INEGI, fecha de descarga, licencia, descripción de variables clave), y se agregaron las entradas correctas al `.gitignore` incluyendo `.DS_Store` y carpetas de datos pesados.
- **Decisión:** Se adoptó la estructura propuesta. El equipo verificó con `git status` que ningún archivo de datos pesados quedara en staging antes del push final.

---

### 2026-04-13 | Claude (Anthropic) | Guía de implementación del tablero en Quarto

- **Tarea:** Convertir los notebooks y gráficas Plotly en un tablero navegable con Quarto Dashboard, integrando las 4 gráficas, métricas de resumen, narrativa por sección y estructura coherente con la pregunta central del proyecto.
- **Prompt:** "Tengo las 4 gráficas HTML ya generadas y los 4 CSV de análisis. Quiero un tablero en Quarto con 5 pestañas: contexto con métricas clave, tendencia nacional, brecha por sector, educación y brecha, y conclusión. Que use Python con Plotly, que cargue los CSV con rutas relativas y que se pueda renderizar con uv."
- **Resultado:** Se generó `dashboard/tablero.qmd` con estructura de 5 pestañas, métricas calculadas dinámicamente desde los CSV, las 4 gráficas integradas como celdas Python, callouts de Quarto para resaltar hallazgos clave, y tabla de créditos en la pestaña de conclusión. Se documentó el comando de render con `QUARTO_PYTHON=$(uv run which python) quarto render dashboard/tablero.qmd`.
- **Decisión:** Se adoptó la estructura del tablero. El equipo ajustó los textos de los callouts y la redacción de la conclusión para que reflejara la voz del equipo y no sonara generada automáticamente.

- ---

### 2026-04-14 | Claude (Anthropic) | Recomendación de gráficas adicionales para profundizar el análisis

- **Tarea:** Con las gráficas G1–G4 funcionando, el equipo pidió una recomendación sobre cómo profundizar en la pregunta central más allá de lo ya visualizado.
- **Prompt:** "Ya tenemos las 4 gráficas del tablero. ¿Qué otros cálculos y visualizaciones nos recomiendas para profundizar más en la raíz del problema de la brecha salarial por género?"
- **Resultado:** La IA propuso 4 gráficas adicionales con su justificación conceptual: G5 (evolución histórica de la brecha por sector, para ver qué sectores la cerraron o ampliaron), G6 (scatter de informalidad femenina vs brecha por sector, para verificar si ambas dimensiones correlacionan), G7 (pirámide poblacional por sector con factor de expansión, para dar peso real a los hallazgos) y G8 (brecha estructural por mismo cargo y misma escolaridad, para aislar la discriminación pura de causas laborales). También propuso una tabla extendida con media y mediana ponderadas por `fac_tri` cruzando sector × ocupación × nivel educativo × sexo. El código fue generado como propuesta inicial (bosquejo), no como versión final validada.
- **Decisión:** El equipo incorporó el código al `02_calculo.ipynb` (cálculo de la tabla extendida y `brecha_estructural`) y al `03_graficas.ipynb` (celdas G5–G8) para dejar registro en el repositorio y facilitar el análisis posterior. Al momento de la entrega, estas gráficas no fueron integradas al tablero principal (`tablero.qmd`) porque el equipo se encontraba evaluando cuáles comunicaban mejor los hallazgos sin saturar la narrativa. Quedan disponibles en `dashboard/` como archivos HTML para su revisión y posible incorporación en etapas siguientes del proyecto.
