# Datos crudos ENOE — Documentación

Esta carpeta contiene (o contenía localmente) los archivos crudos de la
Encuesta Nacional de Ocupación y Empleo (ENOE) descargados del INEGI.

**Los archivos no están en el repositorio** por su tamaño (~150 MB por trimestre,
~12 GB en total). La carpeta existe en la estructura del repo únicamente como
referencia de dónde deben colocarse para reproducir el pipeline.

---

## Fuente

| Periodo | Encuesta | URL de descarga |
|---------|----------|-----------------|
| 2005 T1 – 2020 T1 | ENOE | https://www.inegi.org.mx/programas/enoe/15ymas/#microdatos |
| 2020 T2 | ETOE (encuesta telefónica, COVID-19) | https://www.inegi.org.mx/programas/enoe/15ymas/#microdatos |
| 2020 T3 – 2022 T4 | ENOEN (nueva edición post-COVID) | https://www.inegi.org.mx/programas/enoe/15ymas/#microdatos |
| 2023 T1 – 2025 T4 | ENOE (retoma operación regular) | https://www.inegi.org.mx/programas/enoe/15ymas/#microdatos |

Tabla específica utilizada: **SDEM (Sociodemográfico)**, cuestionario básico.

---

## Formatos de nombre de los archivos CSV

El INEGI ha cambiado el formato del nombre según el año y el tipo de encuesta.
Los tres formatos que aparecen en esta carpeta son:

| Formato | Ejemplo | Periodo |
|---------|---------|---------|
| `SDEMT{trim}{YY}.csv` | `SDEMT419.csv` | 2005–2020 T1 (carpeta compartida) |
| `ENOE_SDEMT{trim}{YY}.csv` | `ENOE_SDEMT120.csv` | Variante con prefijo |
| `ENOEN_SDEMT{trim}{YY}.csv` | `ENOEN_SDEMT422.csv` | 2020 T3 – 2022 T4 |

Donde `{trim}` es el número de trimestre (1–4) e `{YY}` son los dos últimos
dígitos del año (ej. `25` para 2025).

---

## Cómo se procesan estos archivos

El notebook `scripts/01_filtrado.ipynb` lee todos los archivos `*SDEMT*.csv`
de esta carpeta, aplica los siguientes filtros y transformaciones, y guarda
los resultados en `datos/filtrado/`:

### Filtro principal
Solo se conservan registros con `clase2 == 1` (población ocupada).
Los registros de desocupados, disponibles e inactivos se descartan.

### Columnas extraídas y normalizadas

| Columna original | Nombre normalizado | Descripción |
|------------------|--------------------|-------------|
| `ent` / `cve_ent` | `cve_ent` | Clave de entidad federativa (el nombre cambió en 2023) |
| `fac` / `fac_tri` | `fac_tri` | Factor de expansión trimestral (el nombre cambió en agosto 2021) |
| `sex` | `sex` | Sexo (1 = Hombre, 2 = Mujer) |
| `eda` | `eda` | Edad en años |
| `c_ocu11c` | `c_ocu11c` | Grupo de ocupación (11 categorías) |
| `rama_est2` | `rama_est2` | Sector de actividad económica (11 sectores) |
| `niv_ins` | `niv_ins` | Nivel de instrucción (1–5) |
| `anios_esc` | `anios_esc` | Años de escolaridad acumulados |
| `hrsocup` | `hrsocup` | Horas trabajadas en la semana |
| `ingocup` | `ingocup` | Ingreso mensual en pesos |
| `ing_x_hrs` | `ing_x_hrs` | Ingreso por hora (calculado por el INEGI) |
| `emp_ppal` | `emp_ppal` | Tipo de empleo (1 = Formal, 2 = Informal) |

### Columnas calculadas que se agregan al filtrado

| Columna nueva | Cómo se calcula |
|---------------|-----------------|
| `ingreso_hora` | `ing_x_hrs` cuando es > 0; si no, `ingocup ÷ (hrsocup × 4.33)` |
| `salario_semanal` | `ingocup ÷ 4.33` |
| `estado` | Nombre de entidad federativa (de cve_ent con catálogo INEGI) |
| `sexo` | Etiqueta de texto de sex ("Hombre" / "Mujer") |
| `sector` | Nombre del sector (de rama_est2 con catálogo INEGI) |
| `nivel_educ` | Etiqueta de texto de niv_ins |
| `ocupacion` | Nombre del grupo de ocupación (de c_ocu11c con catálogo INEGI) |
| `formalidad` | Etiqueta de texto de emp_ppal ("Formal" / "Informal") |

---

## Nota sobre el factor de expansión

El campo `fac_tri` indica cuántas personas de la población total representa
cada registro de la muestra. Sin aplicarlo, los cálculos solo describen los
~400,000 encuestados por trimestre, no los ~55 millones de ocupados en México.

El INEGI renombró este campo el 26 de agosto de 2021:
- `fac` → archivos anteriores a 2021 T2
- `fac_tri` → archivos de 2021 T2 en adelante

El notebook `01_filtrado` detecta automáticamente cuál de los dos nombres
existe en cada archivo y los normaliza todos como `fac_tri`.
