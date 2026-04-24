# HackODS UNAM 2026

## Equipo: Lobo Ensambladores

| Nombre | Institución | Rol |
|---|---|---|
| Aparicio Guerrero Andrea | Estudiante de Ciencia de datos — UPIIAP, IPN | Análisis de datos y visualización |
| Mateos Coleptor Kevin | Estudiante de Matemáticas Aplicadas — FCFM, BUAP | Procesamiento y limpieza de datos |
| Rodríguez López Luis Guillermo | Doctorante en Ciencias de la Computación — INAOE | Storytelling y desarrollo del tablero |

---

## ODS Elegido

**ODS 8 — Trabajo decente y crecimiento económico**

Promover el crecimiento económico inclusivo y sostenible, el empleo pleno y productivo, y el trabajo decente para todas las personas.

---

## Pregunta central

> **¿En qué sectores económicos persiste la brecha salarial de género en México y qué papel juega el nivel educativo?**

---

## Descripción del proyecto

### Justificación

En México, las mujeres ganan en promedio entre 6% y 20% menos por hora que los hombres con características laborales equivalentes, dependiendo de si se mide solo el empleo formal o la economía total. Esta diferencia no desaparece al controlar por escolaridad: en varios sectores, las mujeres con educación superior siguen percibiendo ingresos menores que sus pares masculinos con el mismo nivel de estudios.

Este proyecto analiza la evolución de esa brecha entre 2005 y 2025 usando microdatos de la ENOE, identificando en qué sectores es más pronunciada, si la pandemia de 2020 aceleró o revirtió tendencias, y qué relación guarda con el nivel educativo en cada rama de actividad.

### Pertinencia con el ODS 8

El análisis responde directamente a las metas **8.5** (lograr el empleo pleno y productivo con igualdad de remuneración por trabajo de igual valor) y **8.3** (promover políticas que apoyen la formalización y el trabajo decente). La brecha salarial de género es uno de los indicadores más directos del avance de la meta 8.5.

### Audiencia

Interesados en política laboral, organizaciones de la sociedad civil enfocadas en equidad de género, y estudiantes que quieren entender en qué sectores productivos la desigualdad salarial es mayor.

---

## Metadatos de los datos

| Fuente | Dataset | URL | Fecha de descarga | Licencia |
|---|---|---|---|---|
| INEGI | ENOE — tabla SDEM, cuestionario básico | [Microdatos ENOE](https://www.inegi.org.mx/programas/enoe/15ymas/#microdatos) | Abril 2026 | [Libre con atribución](https://www.inegi.org.mx/inegi/terminos.html) |
| INEGI | ENOEN — tabla SDEM (2020-T3 a 2022-T4) | [Microdatos ENOE](https://www.inegi.org.mx/programas/enoe/15ymas/#microdatos) | Abril 2026 | Libre con atribución |

**Variables utilizadas:** `cve_ent` (entidad), `sex` (sexo), `c_ocu11c` (grupo de ocupación), `cs_p13_1` (nivel escolar), `hrsocup` (horas trabajadas), `ingocup` (ingreso mensual), `ing_x_hrs` (ingreso por hora), `emp_ppal` (tipo de empleo), `clase2` (condición de ocupación).

### Justificación del indicador `ing_x_hrs`

El ingreso por hora trabajada es preferible al ingreso mensual para comparar brechas porque neutraliza las diferencias de jornada entre hombres y mujeres. Esas diferencias de jornada son en sí mismas consecuencia del trabajo doméstico no remunerado, no una característica inherente del empleo femenino.

---

## Estructura del repositorio

```
LoboEnsambladores/
├── datos/
│   ├── analisis/                         # CSVs generados por 02_calculo.ipynb
│   │   ├── datos_limpios_por_genero.csv  # Dataset principal (estándar OCDE n≥30)
│   │   ├── datos_limpios_general.csv     # Dataset comparativo (sin filtro de muestra)
│   │   ├── brecha_educ_sector_2025.csv
│   │   ├── brecha_estado_sexo_2025.csv
│   │   ├── brecha_estructural_2025.csv
│   │   ├── brecha_sector_anio.csv
│   │   ├── brecha_sector_ocupacion_2025.csv
│   │   ├── informalidad_contexto_2025.csv
│   │   ├── serie_nacional.csv
│   │   └── serie_nacional_formal.csv
│   └── filtrado/                         # CSVs intermedios (ENOE_T*.csv)
├── scripts/
│   ├── 01_filtrado.ipynb    # Lectura y extracción de columnas clave
│   ├── 02_calculo.ipynb     # Indicadores derivados y agregaciones anuales
│   └── 03_graficas.ipynb    # Visualizaciones con Plotly (análisis exploratorio)
│   └── 04_posteior.ipynb    # Visualizaciones con Plotly (análisis exploratorio)
│   └── 04_tablero.ipynb    # Visualizaciones con Plotly (análisis exploratorio)
│   └── 05_graficas.ipynb    # Visualizaciones con Plotly (análisis exploratorio)
├── dashboard/
│   └── index.qmd            # Tablero narrativo final (Quarto dashboard)
├── docs/
│   └── index.html           # Tablero renderizado (GitHub Pages)
├── ai-log.md
├── README.md
├── LICENSE                  # Creative Commons BY-SA 4.0
├── pyproject.toml
└── uv.lock
```

---

## Cómo reproducir el análisis

### Requisitos previos

- Python 3.12+
- [uv](https://docs.astral.sh/uv/) (gestor de entornos Python)
- [Quarto](https://quarto.org/docs/get-started/) 1.6+

### Pasos

**1. Clonar el repositorio**

```bash
git clone https://github.com/Mateos-DD/LoboEnsambladores.git
cd LoboEnsambladores
```

**2. Instalar dependencias**

```bash
uv sync
```

**3. Descargar los datos crudos**

Los datos crudos de la ENOE se obtienen directamente del [portal del INEGI](https://www.inegi.org.mx/programas/enoe/15ymas/#microdatos). Los archivos filtrados y procesados están en `datos/filtrado/`.

**4. Ejecutar los scripts en orden**

```bash
# 1. Filtrado inicial
uv run jupyter nbconvert --to notebook --execute scripts/01_filtrado.ipynb

# 2. Cálculo de indicadores
uv run jupyter nbconvert --to notebook --execute scripts/02_calculo.ipynb

# 3. Visualizaciones exploratorias (opcional)
uv run jupyter nbconvert --to notebook --execute scripts/03_graficas.ipynb
```

**5. Renderizar el tablero**

```bash
uv run quarto render dashboard/index.qmd --output-dir docs
```

**6. Previsualizar localmente**

```bash
uv run quarto preview dashboard/index.qmd
```

---

## Decisiones metodológicas clave

**Filtrado de muestra mínima (estándar OCDE):** el dataset principal (`df_cut_si.csv`) aplica un filtro de muestra mínima de 30 personas por celda (estado × sector × ocupación × nivel educativo × sexo), siguiendo el estándar OCDE/OIT para estimaciones confiables en encuestas muestrales. Esto cambia el diagnóstico: la brecha en educación superior pasa de 5.7% (sin filtro) a 7.5% (con filtro).

**Mediana ponderada:** se usa la mediana ponderada por factor de expansión (`fac_tri`) en lugar de la media, para resistir la influencia de salarios extremos en cada subgrupo.

**Uso de IA:** el desarrollo de este proyecto incluyó el uso de Claude (Anthropic) como asistente para análisis de datos, generación de código y estructuración narrativa. Todos los datos y análisis provienen de fuentes oficiales (INEGI, ENOE). Ver `ai-log.md` para el detalle.

---

## Licencia

Este proyecto se distribuye bajo [Creative Commons Attribution-ShareAlike 4.0 International (CC BY-SA 4.0)](https://creativecommons.org/licenses/by-sa/4.0/).
