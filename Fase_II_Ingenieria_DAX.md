# DOCUMENTACIÓN TÉCNICA: FASE II - INGENIERÍA DAX Y MODELADO BI

Esta documentación describe el proceso de construcción de la capa semántica (DAX) en Power BI, esencial para las métricas, tendencias y el comportamiento dinámico del dashboard.

---

## 1. CONSTRUCCIÓN Y CONTROL DEL TIEMPO

### A. Creación de la Dimensión Maestra (Dim_Calendario)

Se implementó una tabla calculada para controlar el rango de análisis (2021-2024), evitando la ambigüedad de años sin datos reales.

* **Fórmula:** Se usó `CALENDAR(DATE(2021, 1, 1), DATE(2024, 12, 31))` para acotar el rango de forma explícita.
* **Relación:** Se estableció la jerarquía Copo de Nieve: `Dim_Calendario[Date]` $\rightarrow$ `Dim_Tiempo[Fecha]`.

### B. Corrección de Usabilidad (Orden de Meses)

Se corrigió la ordenación incorrecta del eje temporal en las visualizaciones:

* **Acción:** Se aplicó la propiedad **"Ordenar por Columna"** (Sort by Column) a la columna `Mes Nombre`, utilizando la columna numérica `Número Mes` para asegurar el orden cronológico.

## 2. CREACIÓN DE MÉTRICAS (KPIs)

Se generaron **11 métricas DAX** explícitas para el análisis de rendimiento y diagnóstico.

| Métrica | Propósito Analítico | Tipo de Cálculo |
| :--- | :--- | :--- |
| `[Avg Score General]` | Rendimiento General del Test | `AVERAGE(DIG_GENERAL)` |
| `[Total MYPES]` | Alcance del Programa | `COUNTROWS('hechos_Digitalizacion')` |
| `[Avg Duración (Minutos)]` | KPI de Auditoría | `AVERAGE(TIEMPO_DEMORA_MINUTOS)` |
| `[Avg Score por Dominio] (x6)` | Diagnóstico de Brecha Tecnológica | `AVERAGE(SCORE_DEL_DOMINIO)` |

## 3. MODELADO Y RELACIONES

Se establecieron las relaciones del **Esquema Copo de Nieve** (`Snowflake Schema`):

* **Flujo de Hechos:** `Dim_Tiempo[ID_Tiempo]` (1) se relaciona con `hechos_Digitalizacion[ID_Tiempo_Registro]` (*).
* **Flujo de Atributos:** El filtro fluye de la dimensión maestra (`Dim_Calendario`) a la tabla de hechos a través de la dimensión intermedia (`Dim_Tiempo`).

  <img width="1177" height="738" alt="image" src="https://github.com/user-attachments/assets/dd840cbf-8ae8-4a87-b43d-4d71b935b318" />
