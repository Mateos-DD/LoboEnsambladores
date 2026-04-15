# Datos filtrados ENOE — Documentación

Esta carpeta contiene (o contenía localmente) los archivos CSV filtrados
generados a partir de los datos crudos de la ENOE.

**Los archivos no están en el repositorio** por su tamaño (~5–15 MB por
trimestre). La carpeta existe en la estructura del repo únicamente como
referencia de dónde deben colocarse para reproducir el análisis.

---

## Cómo se generan estos archivos

Los archivos de esta carpeta son producidos por `scripts/01_filtrado.ipynb`
a partir de los crudos en `datos/DATOS MASIVOS ENOE/`.

Cada archivo filtrado corresponde a un trimestre y sigue el formato:

```
ENOE_T{trim}{YY}.csv
```

Ejemplos: `ENOE_T125.csv` (2025 T1), `ENOE_T420.csv` (2020 T4).

---

## Para qué se usan

Estos archivos son la entrada del notebook `scripts/02_calculo.ipynb`.

El `02_calculo` los lee todos, los une en un solo DataFrame y calcula
las métricas del proyecto aplicando **mediana ponderada con `fac_tri`**
para obtener estimaciones representativas de la población mexicana.

---

## Qué produce el 02_calculo con estos archivos

Al ejecutar `02_calculo.ipynb` sobre los filtrados, se generan 4 archivos
CSV en `datos/analisis/` que **sí están en el repositorio**:

| Archivo | Contenido |
|---------|-----------|
| `serie_nacional.csv` | Ingreso mediano por hora por año y sexo (2005–2025) |
| `brecha_sector_anio.csv` | Brecha salarial porcentual por sector y año |
| `brecha_educ_sector_{año}.csv` | Brecha por nivel educativo y sector (último año) |
| `informalidad_contexto_{año}.csv` | Porcentaje de informalidad por sector y sexo |

Opcionalmente, si se ejecutan las secciones 8 y 9 del `02_calculo`,
también se generan:

| Archivo | Contenido |
|---------|-----------|
| `brecha_sector_ocupacion_{año}.csv` | Tabla extendida: población, media y mediana por sector × ocupación × educación × sexo |
| `brecha_estructural_{año}.csv` | Brecha dentro del mismo cargo y escolaridad (análisis de discriminación estructural) |

---

## Reproducibilidad

A partir de los archivos en `datos/analisis/`, el notebook
`scripts/03_graficas.ipynb` es **completamente reproducible** sin necesidad
de tener los crudos ni los filtrados en disco.

El `03_graficas` solo lee los CSV de `datos/analisis/` y genera las
visualizaciones interactivas en `dashboard/` con Plotly.

El flujo completo es:

```
DATOS MASIVOS ENOE/   →   01_filtrado.ipynb   →   filtrado/
filtrado/             →   02_calculo.ipynb    →   analisis/   ← está en el repo
analisis/             →   03_graficas.ipynb   →   dashboard/  ← está en el repo
```

Los primeros dos pasos requieren los datos pesados localmente.
El tercer paso es reproducible para cualquier persona que clone el repositorio.
