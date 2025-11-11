# FASE III - DISEÑO EJECUTIVO Y VISUALIZACIÓN

Esta documentación detalla la implementación del front-end del proyecto en Power BI, enfocado en el diseño ejecutivo, la narrativa de datos y la funcionalidad de la interfaz de usuario (UX).

---

## 1. 🎨 ESTRUCTURA, ESTILO Y JERARQUÍA VISUAL

El diseño se centró en la creación de un **Monitor Ejecutivo de una sola página** con un tema limpio y una jerarquía clara para el análisis.

### A. Estructura y Estilo

* **Estructura:** Diseño de **una sola página** (Scorecard Consolidado).
* **Estilo Visual:** Se adoptó un **Tema Claro (o Estándar)**, priorizando la legibilidad y un fondo limpio para destacar los datos.
* **Tarjetas KPI (Header):** Se implementaron tarjetas clave para el pulso inmediato del proyecto:
    1.  `[Total MYPES]` (Alcance Total).
    2.  `[Avg Score General]` (Rendimiento Principal).
    3.  `[Avg Duración (Minutos)]` (Auditoría/Tiempo de Llenado).
  
### B. Segmentación y Usabilidad

* **Segmentadores Clave:** `Año`, `CIIU`, y `Tipo Empresa`.
* **Configuración UX:** Los segmentadores se mantienen en modo de **Selección Única** (excluyendo la selección múltiple) para guiar el análisis hacia un contexto específico a la vez.
  
  <img width="393" height="131" alt="image" src="https://github.com/user-attachments/assets/ccc87056-7607-4c16-9678-dfdbeb52b39a" />

## 2. 🗺️ IMPLEMENTACIÓN DE VISUALES AVANZADOS Y NARRATIVA

Se utilizaron visualizaciones especializadas para mejorar el análisis geoespacial y el diagnóstico de brechas.

### A. Gráfico de Tendencia Dual (Gráfico Combinado - Principal)

Se utilizó un visual combinado de **Columna Agrupada (Barras)** y **Línea** para mostrar dos narrativas simultáneamente en el tiempo:

* **Eje Y1 (Columna):** Métrica de Volumen (`Total MYPES`).
* **Eje Y2 (Línea):** Métrica de Calidad (`Avg Score General`).
* **Función Drill Down:** Se configuró la jerarquía de fechas para que el usuario pueda navegar de la vista **Año** a **Mes** haciendo clic en las columnas.

  <img width="382" height="226" alt="image" src="https://github.com/user-attachments/assets/2577042f-8d50-4b6f-9678-d5daa9195c8a" />

### B. Gráfico de Diagnóstico (Gráfico de Barras y Gráfico Circular)

* **Gráfico de Barras (Brecha Digital):** Muestra los **6 scores de dominio** (Marketing, Finanzas, etc.) uno al lado del otro. Permite identificar la debilidad tecnológica más grande del sector filtrado (la barra más corta).

  <img width="375" height="222" alt="image" src="https://github.com/user-attachments/assets/43afd2e3-9d4d-448a-a83a-2e6d72fa9c5f" />
  
* **Gráfico Circular (Pie Chart):** Se utilizó para mostrar la **Distribución de las MYPES por `Nivel de Digitalización`** (`Inicial`, `Básico`, `Intermedio`, `Avanzado`), cuantificando el peso del problema.
  
  <img width="380" height="222" alt="image" src="https://github.com/user-attachments/assets/1d5ac281-a163-4f18-9a5d-f7f93ccd1523" />

### C. Implementación del Mapa de Formas Personalizado (Foco Geográfico)

Para mapear los resultados por departamento con precisión, se utilizó el visual **Mapa de Formas (`Shape Map`)** con geometría personalizada.

* **Origen GeoJSON:** Se utilizó el archivo GeoJSON (`peru.json`) obtenido a partir del repositorio de [juaneladio/peru-geojson](https://github.com/juaneladio/peru-geojson) y optimizado mediante Mapshaper.org.
* **Visualización:** La saturación de color (heatmap) se configuró sobre la métrica **`[Avg Score General]`**, permitiendo identificar rápidamente los departamentos con menor madurez digital.
* **Función Interactiva:** El mapa se configuró como un filtro secundario.

  <img width="341" height="446" alt="image" src="https://github.com/user-attachments/assets/14a1fd92-d1fb-4a50-b286-cfe797ffa910" />

