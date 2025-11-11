## **MONITOR DE MADUREZ DIGITAL: DESEMPEÑO Y ALCANCE DEL PROGRAMA RUTA DIGITAL (2021-2024)**

> **Objetivo:** Implementar una solución integral de Business Intelligence para **analizar los resultados del Test de Madurez Digital** y diagnosticar el nivel de digitalización de las MYPES del programa Ruta Digital.

---

## 1. 🔍 CONTEXTO, ANÁLISIS Y VALOR ESTRATÉGICO

### 1.1. Objeto del Análisis y Contexto del Programa

El proyecto se enfoca en el **análisis de la base de datos de los resultados del Test de Madurez Digital** aplicado a las MYPES a nivel nacional que se encuentran dentro del programa **Ruta Digital** (periodo 2021-2024).

* **Fuente:** Portal de **Datos Abiertos del Gobierno del Perú**.
* **Alcance:** Consolidación de **13,944 registros** de evaluaciones limpias, cubriendo el período **2021 a 2024**.

### 1.2. Justificación del Monitoreo

El dashboard es un sistema de **diagnóstico dinámico** que permite:

1.  **Análisis de Resultados:** El foco es determinar el nivel de las MYPES y potenciar sus capacidades, según el resultado del test de diagnóstico.
2.  **Diagnóstico de Brecha:** El Gráfico de Brecha Tecnológica identifica el **dominio más débil** (Ej: Análisis de Datos) para un sector económico específico (`CIIU`).
3.  **Foco Geográfico:** El **Mapa Coroplético** permite la focalización inmediata de los departamentos con menor desempeño.

---

## 2. 🗺️ FLUJO Y ARQUITECTURA DEL PROYECTO

El flujo de trabajo demuestra el dominio del ciclo de BI completo (ETL, Modelado y Visualización).

| Fase | Enfoque Principal | Habilidades Clave | Enlace Rápido |
| :--- | :--- | :--- | :--- |
| **FASE I** | **ETL y Modelado SQL** | Arquitectura Dimensional, Calidad de Datos. | [Ir a Fase I](#fase-i-etl-y-modelado-sql) |
| **FASE II** | **Ingeniería DAX y BI** | Creación de 11 métricas, Solución de Ambigüedades Temporales. | [Ir a Fase II](#fase-ii-ingenieria-dax-y-bi) |
| **FASE III** | **Diseño Ejecutivo (UX)** | Scorecard Consolidado de una página, Navegación Dinámica. | [Ir a Fase III](#fase-iii-diseño-ejecutivo-ux) |

---

## 3. 🛠️ DESGLOSE TÉCNICO DE IMPLEMENTACIÓN

### [FASE I: ETL y Modelado SQL](https://github.com/Modelado-BI-Programa-MadurezDigital-MYPEs/etl)

* **Modelo Dimensional:** Implementación de un **Esquema Copo de Nieve** (`Dim_Calendario` $\rightarrow$ `Dim_Tiempo`) y Estrella (`Dim_Ubicacion`, `Dim_CIIU`, etc.).
* **Calidad de Datos:** Uso de `REPLACE(',', '.')` para corregir la ambigüedad del formato decimal en los *scores*.

### [FASE II: Ingeniería DAX y BI](https://github.com/Modelado-BI-Programa-MadurezDigital-MYPEs/dax)

* **Control Temporal (DAX):** Creación de la tabla maestra `Dim_Calendario` con la función **`CALENDAR(2021, 1, 1), DATE(2024, 12, 31)`** para limitar el rango de análisis y evitar el problema del año 2025.
* **Métricas Esenciales:** Creación de **11 métricas DAX**, incluyendo las **6 métricas de Dominio** para el diagnóstico de Brecha.
* **Usabilidad (UX Fix):** Se aplicó **"Ordenar por Columna"** para corregir el orden alfabético de los meses.

### [FASE III: Diseño Ejecutivo (UX)](https://github.com/Modelado-BI-Programa-MadurezDigital-MYPEs/visualization)

* **Diseño Final:** Scorecard Consolidado de una sola página, con diseño de **alto contraste**.
* **Navegación Dinámica:** El **Gráfico Combinado** fue configurado para **Drill Down** (navegación de **Año a Mes**), activándose al hacer clic en el segmentador.

---

## 4. 📦 ARTEFACTOS Y RECOMENDACIONES FINALES

| Archivo/Elemento | Contenido | Valor que Demuestra |
| :--- | :--- | :--- |
| **`RutaDigital_Dashboard.pbix`** | Archivo Power BI final. | Habilidad en **Diseño Ejecutivo** y **Modelado DAX**. |
| **`RutaDigital_DataMart_SQL.sql`** | Script SQL completo (DDL y Procedimiento de Carga). | Dominio de **ETL y Arquitectura Dimensional**. |
| **`Images/01_Dashboard_Final.png`** | Captura del Dashboard Consolidado (Vista de una sola página). | Evidencia la **habilidad de diseño y UX**. |
| **`Images/02_Modelo_Datos.png`** | Captura de la **Vista de Modelo** en Power BI. | Muestra la correcta implementación de las **relaciones (1:\*)** y el esquema Copo de Nieve. |
| **`Images/03_Brecha_Digital.png`** | Captura del **Gráfico de Brecha** filtrado. | Documenta la **capacidad analítica** para diagnosticar debilidades. |
