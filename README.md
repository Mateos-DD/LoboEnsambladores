# HackODS UNAM 2026

## Equipo: Lobo Ensambladores

### Integrantes

| Nombre | Descripción|Rol |
|--------|-----|----|
| Aparicio Guerrero Andrea |Estudiante de Ciencia de datos - UPIIAP, IPN| Análisis de datos y visualización |
|Mateos Coleptor Kevin |Estudiante de Licenciatura en Matemáticas Aplicadas -FCFM, BUAP| Procesamiento y limpieza de datos |
| Rodríguez López Luis Guillermo |Doctorante en Ciencias de la computación - INAOE| Storytelling y desarrollo del tablero |

---

## ODS Elegido

**ODS 8 — Trabajo decente y crecimiento económico**

Promover el crecimiento económico inclusivo y sostenible, el empleo pleno y productivo, y el trabajo decente para todas las personas.

---

## Pregunta central

> **¿En qué sectores económicos persiste una brecha salarial por género en México y qué papel juega el nivel educativo en esta?**

---

## Descripción del proyecto

### Justificación

En México, las mujeres ganan en promedio entre 15% y 20% menos por hora que los hombres con características laborales equivalentes. Esta diferencia no desaparece al controlar por escolaridad: en varios sectores, las mujeres con educación superior siguen percibiendo ingresos menores que sus pares masculinos con el mismo nivel de estudios.

Este proyecto analiza la evolución de esa brecha entre 2005 y 2025, identificando en qué sectores es más pronunciada, si se redujo después de la pandemia de 2020, y qué relación guarda con el nivel educativo de quienes trabajan en cada rama de actividad.

### Pertinencia con el ODS 8

El análisis responde directamente a las metas **8.5** (lograr el empleo pleno y productivo con igualdad de remuneración por trabajo de igual valor) y **8.3** (promover políticas que apoyen la formalización y el trabajo decente). La brecha salarial por género es uno de los indicadores más directos del avance de la meta 8.5 en México.

### Audiencia

Interesados en política laboral de México, organizaciones de la sociedad civil enfocadas en equidad de género, y estudiantes que quieren decidir en que sector productivo iniciar una carrera profesional.

---

## Metadatos de los datos

| Fuente | Dataset | URL | Fecha de descarga | Licencia | Descripción de variables |
|--------|---------|-----|-------------------|----------|--------------------------|
| INEGI | Encuesta Nacional de Ocupación y Empleo (ENOE) — tabla SDEM, cuestionario básico | [ENOE](https://www.inegi.org.mx/programas/enoe/15ymas/#microdatos )| abril 2026 | [Libre con atribución](https://www.inegi.org.mx/inegi/terminos.html )| `ent`/`cve_ent`: entidad federativa · `sex`: sexo · `c_ocu11c`: grupo de ocupación (11 categorías) · `cs_p13_1`: último nivel escolar aprobado · `hrsocup`: horas trabajadas en la semana · `ingocup`: ingreso mensual en pesos · `ing_x_hrs`: ingreso promedio por hora trabajada · `emp_ppal`: tipo de empleo (1=formal, 2=informal) · `clase2`: condición de ocupación (filtro: 1=ocupado) |
| INEGI | ENOEN — tabla SDEM (2020-T3 a 2022-T4) | [ENOE](https://www.inegi.org.mx/programas/enoe/15ymas/#microdatos) | abril 2026 | Libre con atribución | Mismas variables que ENOE. Levantamiento adaptado al periodo post-COVID. |

### Justificación de la selección de datos

La ENOE es la única encuesta de fuerza laboral de México con representatividad nacional, cobertura trimestral continua desde 2005, y desglose por sexo, sector económico y nivel educativo. El indicador `ing_x_hrs` (ingreso por hora trabajada) es preferible al ingreso mensual para comparar brechas porque neutraliza las diferencias de jornada entre hombres y mujeres — diferencias que son en sí mismas una consecuencia del trabajo doméstico no remunerado, no una característica natural del empleo femenino.

El archivo SDEM.csv de la ENOE concentra las variables sociodemográficas y de ocupación necesarias para el análisis, incluyendo edad, sexo, nivel educativo y condición de actividad económica. Además, incorpora campos precodificados que simplifican la clasificación educativa, lo que permite calcular población ocupada, ingresos y brechas salariales sin necesidad de integrar las demás tablas de la ENOE.

---

## Estructura del repositorio

```
LOBOENSAMBLADORES/
├── datos/
│   ├── analisis/            # CSVs que se generan al ejecutar 02_calculo.ipynb            
│   └── filtrado/            # CSVs con columnas clave (ENOE_T*.csv)
├── scripts/
│   ├── 01_filtrado.ipynb      # Lectura y extracción de columnas clave
│   ├── 02_calculo.ipynb       # Indicadores derivados y agregaciones anuales
│   └── 03_graficas.ipynb      # Visualizaciones con Plotly
├── docs/
│    ├──tablero.html        # Tablero narrativo final 
│ 
├── dashboard/
│   └── tablero.qmd # Tablero narrativo final
├── ai-log.md      
├── README.md
├── LICENSE
├── uv.lock
└── pyproject.toml
```

---

## Cómo reproducir el análisis

### 0 Clonar el repositorio en tu computadora

### 1 Instala Python, UV y Quarto
El proyecto está creado con python, UV y Quarto, por lo que se deben de tener instalados previamente. Posteriormente se debe posicionar en la carpeta raiz del proyecto para ejecitar el comando de instalación de las dependencias de uv.
```bash
uv sync
```

### 2 Descarga los datos y almacenalos en el directorio correspondiente

Los datos crudos se obtienen directamente del portal del INEGI en la URL indicada en la tabla de metadatos. Los archivos filtrados y procesados están disponibles en la carpeta `datos/filtrado/`.

### 3 Ejecuta los scripts en el orden correspondiente para generar el tablero

```bash
# Ejecutar en orden
# 1. scripts/01_filtrado.ipynb
# 2. scripts/02_calculo.ipynb
# 3. scripts/03_graficas.ipynb
```

### 4 Reproducir tablero
```bash
uv run quarto preview ./dashboard/tablero.qmd
uv run quarto render
```
