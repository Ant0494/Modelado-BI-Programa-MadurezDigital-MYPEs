## **🚀 MONITOR DE MADUREZ DIGITAL: DESEMPEÑO Y ALCANCE DEL PROGRAMA RUTA DIGITAL (2021-2024)**

> **Objetivo:** Implementar una solución integral de Business Intelligence para **analizar los resultados del Test de Madurez Digital** y diagnosticar el nivel de digitalización de las MYPES del programa Ruta Digital.

---

## 1. 🔍 CONTEXTO ESTRATÉGICO Y MECÁNICA DEL DIAGNÓSTICO 🇵🇪

### 1.1. Importancia del Programa Ruta Digital y el Test de Madurez

Este proyecto se fundamenta en los resultados del **Test de Diagnóstico de Madurez Digital**, una herramienta clave del programa **Ruta Digital** del Ministerio de la Producción (PRODUCE).

* **¿Qué es el Test?** Es un instrumento diseñado por PRODUCE para evaluar la situación actual de las MYPES peruanas en **6 dominios digitales críticos** (Ej: Gestión, Finanzas, Comercio Electrónico).
* **Propósito y Objetivos de PRODUCE:** El objetivo es doble: 1) Proporcionar a la MYPE una hoja de ruta para su transformación digital. 2) Dotar a PRODUCE de datos cuantitativos para **justificar y planificar estratégicamente** las acciones de capacitación y fomento, **focalizando la ayuda** donde es más necesaria.
* **Contexto del Dato:** La base de datos contiene **13,944 resultados** de MYPES (2021-2024), siendo la fuente directa para este diagnóstico.

### 1.2. Justificación del Monitoreo

El dashboard es un sistema de **diagnóstico dinámico** que permite:

1.  **Diagnóstico de Brecha (KPIs Ejecutivos):** Identificar el dominio tecnológico más débil (el que arroja el *score* más bajo) para un sector (`CIIU`) o región específica.
2.  **Focalización:** Permite determinar el nivel de las MYPES y potenciar sus capacidades con recursos de capacitación dirigidos.

---

## 2. 🗺️ FLUJO Y ARQUITECTURA DEL PROYECTO

El flujo de trabajo demuestra el dominio del ciclo de BI completo (ETL, Modelado y Visualización).

| Fase | Enfoque Principal | Habilidades Clave | Archivo de Documentación |
| :--- | :--- | :--- | :--- |
| **FASE I** | **ETL y Modelado SQL** | Arquitectura Dimensional (Copo de Nieve), Calidad de Datos. | **[Fase_I_Limpieza_Modelado](Fase_I_Limpieza_Modelado.md)** y **[Script SQL](RutaDigital_DataMart_SQL.sql)** |
| **FASE II** | **Ingeniería DAX y BI** | Creación de **9 métricas** clave, Solución de Ambigüedades Temporales. | **[Fase_II_Ingenieria_DAX](Fase_II_Ingenieria_DAX.md)** |
| **FASE III** | **Diseño Ejecutivo (UX)** | Scorecard Consolidado de una página, Navegación Dinámica. | **[Fase_III_Diseno_Ejecutivo](Fase_III_Diseno_Ejecutivo.md)** y **[Dashboard Final](Dashboard_RutaDigitalvf.pbix)** |

---

## 3. 🛠️ DESGLOSE TÉCNICO DE IMPLEMENTACIÓN

### [FASE I: ETL y Modelado SQL]

* **Modelo Dimensional:** Implementación del **Esquema Estrella** en SQL, creando las tablas de Hechos y Dimensiones (Dim_Ubicacion, Dim_CIIU, etc.) optimizadas para consultas BI.
* **Calidad de Datos:** Se implementaron procedimientos de validación y limpieza (`TRIM/UPPER`) en el Stored Procedure para asegurar la **carga y consistencia del 100%** de los 13,944 registros en el Data Mart.
* **Normalización:** Uso de `REPLACE(',', '.')` dentro del SP para corregir la ambigüedad del formato decimal en los *scores* y garantizar cálculos precisos.

### [FASE II: Ingeniería DAX y BI]

* **Control Temporal (DAX):** Creación de la tabla maestra `Dim_Calendario` con la función `CALENDAR(...)`. El modelo adquiere la estructura **Copo de Nieve** al vincular esta dimensión (creada en DAX) a la `Dim_Tiempo` (cargada desde SQL).
* **Métricas Esenciales:** Creación de **9 métricas DAX** (incluyendo las 6 métricas de Dominio) para el diagnóstico de Brecha.
* **Usabilidad (UX Fix):** Se aplicó **"Ordenar por Columna"** para corregir el orden alfabético de los meses.

### [FASE III: Diseño Ejecutivo (UX)]

* **Diseño Final:** Scorecard Consolidado de una sola página, con diseño de **alto contraste** y una jerarquía visual clara.
* **Mapa de Formas:** Implementación de geometría GeoJSON personalizada para el análisis de saturación de color por departamento.
* **Navegación Dinámica:** El **Gráfico Combinado** fue configurado para **Drill Down** (navegación de **Año a Mes**).

---

## 🎥 DEMOSTRACIÓN DE INTERACCIÓN

La siguiente sección muestra la navegación dinámica  y el filtrado interactivo del Monitor Ejecutivo.

https://github.com/user-attachments/assets/7c1d9126-ad66-49d6-a012-1c3bdc408e84


## 4. 📦 ARTEFACTOS Y RECOMENDACIONES FINALES

| Archivo/Elemento | Contenido | Valor que Demuestra |
| :--- | :--- | :--- |
| **`RutaDigital_Dashboard.pbix`** | Archivo Power BI final. | Habilidad en **Diseño Ejecutivo** y **Modelado DAX**. |
| **`RutaDigital_DataMart_SQL.sql`** | Script SQL completo (DDL y Procedimiento de Carga). | Dominio de **ETL y Arquitectura Dimensional**. |
| **`Fase_I_Limpieza_Modelado.md`** | **Documentación del ETL** y **Esquema Dimensional**. | Muestra la metodología de limpieza y el diseño del modelo. |
| **`Fase_II_Ingenieria_DAX.md`** | Documentación de la lógica DAX. | Evidencia del **pensamiento analítico** y la construcción de métricas. |
| **`Fase_III_Diseno_Ejecutivo.md`** | **Documentación de Diseño y UX**. | Muestra la implementación de visuales avanzados (GeoJSON). |
| **`Dashboard_RutaDigital.png`** | Captura del Dashboard Consolidado. | Evidencia la **habilidad de diseño y UX**. |
| **`Modelo_Datos_CopoNieve.png`** | Captura de la **Vista de Modelo** en Power BI. | Muestra la correcta implementación de las **relaciones (1:\*)** y el esquema Copo de Nieve. |

