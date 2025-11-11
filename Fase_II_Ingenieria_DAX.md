# FASE II: INGENIERÍA DAX Y MODELADO BI

Esta documentación describe el proceso de construcción de la capa semántica (DAX) en Power BI, esencial para las métricas, tendencias y el comportamiento dinámico del dashboard.

---

## 1. CONSTRUCCIÓN Y CONTROL DEL TIEMPO

### A. Creación de la Dimensión Maestra (Dim_Calendario)

Se implementó una tabla calculada para establecer el rango de análisis explícito y controlar el flujo de filtro en el esquema Copo de Nieve, solucionando la ambigüedad de años incompletos o futuros (2025).

```dax
Dim_Calendario = 
ADDCOLUMNS(
    CALENDAR(
        DATE(2021, 1, 1),      // Límite Inferior Fijo
        DATE(2024, 12, 31)     // Límite Superior Fijo (Aislando el año 2025)
    ),
    "Año", YEAR([Date]),
    "Mes Nombre", FORMAT([Date], "mmmm"),
    "Número Mes", MONTH([Date])
)
````

### B. Creación de Jerarquía y Corrección de Usabilidad

* Jerarquía de Fechas: Se creó una jerarquía dentro del modelo (`Año -> Mes Nombre`) para facilitar el análisis Drill Down/Up en el Gráfico Combinado de Tendencias.
* Corrección de Orden: Se aplicó la propiedad "Ordenar por Columna" (Sort by Column) a la columna `Mes Nombre`, utilizando la columna numérica Número Mes para garantizar el orden cronológico.
-----

## 2. CREACIÓN DE MÉTRICAS (KPIs)

Se generaron **9 métricas DAX** explícitas (medidas) para el análisis de rendimiento y diagnóstico.

### A. Métricas de Rendimiento, Alcance y Auditoría

| Métrica | Propósito Analítico | Tipo de Cálculo |
| :--- | :--- | :--- |
| `[Avg Score General]` | Rendimiento General del Test | `AVERAGE(DIG_GENERAL)` |
| `[Total MYPES]` | Alcance del Programa (Base para el Gráfico Combinado) | `COUNTROWS(hechos_Digitalizacion)` |
| `[Avg Duración (Minutos)]` | KPI de Auditoría | `AVERAGE(TIEMPO_DEMORA_MINUTOS)` |

### B. Métricas de Diagnóstico (Brecha Digital)

Estas 6 métricas alimentan el Gráfico de Brecha, permitiendo la comparación de debilidades específicas por dominio.

  * **Implementación:** Se implementó la fórmula `AVERAGE('hechos_Digitalizacion'[COLUMNA_DOMINIO])` para las **6 columnas de score individual** (GESTION\_EMPRESARIAL, COMERCIO\_ELECTRONICO, ANÁLISIS\_DE\_DATOS, MARKETING\_DIGITAL, MEDIOS\_DE\_PAGO, FINANZAS).

-----

## 3. MODELADO Y RELACIONES

Se estableció el flujo de filtro sobre el modelo de datos, confirmando el esquema **Copo de Nieve** para las tablas de tiempo.

  * **Flujo de Filtro:** Se vinculó la dimensión maestra creada (`Dim_Calendario`) a la tabla intermedia (`Dim_Tiempo`) y, finalmente, a la tabla de hechos.

  <img width="1177" height="738" alt="image" src="https://github.com/user-attachments/assets/dd840cbf-8ae8-4a87-b43d-4d71b935b318" />
