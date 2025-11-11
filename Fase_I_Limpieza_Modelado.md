# FASE I: DOCUMENTACIÓN DE ETL Y ARQUITECTURA SQL

Este documento detalla la transformación de datos brutos y la implementación del Data Mart para el análisis de Madurez Digital.

---

## 1. DICCIONARIO DE DATOS Y CONTEXTO DE LA DATA BRUTA

El análisis se basa en el dataset `RUTADIGITAL2021al2024.csv` de **Registro de MYPES que culminaron el Test de Digitalización en la plataforma RUTA DIGITAL**.

| Variable | Descripción | Tipo de Dato (Origen) |
| :--- | :--- | :--- |
| **ANIO** | Año al que corresponde la descarga. | Numérico |
| **CIIU** | Código de actividad económica (SUNAT). | Alfanumérico |
| **TIPO** | Tipo de personal: Natural o Jurídica. | Texto |
| **DEPARTAMENTO** | Ubicación geográfica de la empresa. | Texto |
| **NIVEL DE DIGITALIZACION** | Indica el nivel de digitalización de la empresa. | Texto |
| **DIG_GENERAL** | Nivel alcanzado en el Test General (en porcentaje). | Numérico (6) |
| **GESTION_EMPRESARIAL** | Nivel alcanzado en Test Empresarial (en porcentaje). | Numérico (6) |
| **COMERCIO_ELECTRONICO** | Nivel alcanzado en el Test de Comercio Electrónico. | Numérico (6) |
| **FECHA_REGISTRO** | Fecha de registro en el aplicativo Ruta Digital. | Numérico (8) |
| **FECHA_FIN** | Fecha de fin del llenado del test. | Numérico (8) |
| **TIEMPO_DEMORA_MINUTOS** | Tiempo que demoró en el llenado del test. | Numérico (6) |

## 2. LÓGICA DE LIMPIEZA Y TRANSFORMACIÓN (ETL)

El proceso de Extracción, Transformación y Carga (ETL) se enfocó en garantizar la integridad del 100% de los registros mediante correcciones de calidad de dato en SQL.

La extracción de los datos del dataset `RUTADIGITAL2021al2024.csv` se realiza a través de `BULK INSERT` hacia una tabla staging denominada `DatosBrutos_RutaDigital` previamente creada para recibir los datos.

```sql
--CREACION DE LA TABLA STAGING PARA RECEPCION DE DATOS DE RUTADIGITAL2021al2024.csv

CREATE TABLE DatosBrutos_RutaDigital(
ANIO VARCHAR(4),
CIIU VARCHAR(10),
DESCRIPCION_CIIU VARCHAR (255),
RAZON_SOCIAL_ANONIMIZADA VARCHAR (100),
TIPO VARCHAR(100),
DEPARTAMENTO VARCHAR(100),
PROVINCIA VARCHAR(50),
DISTRITO VARCHAR(50),
UBIGEO VARCHAR(100),
NIVEL_DIGITALIZACION VARCHAR(50),

DIG_GENERAL VARCHAR(10),
GESTION_EMPRESARIAL VARCHAR(10),
COMERCIO_ELECTRONICO VARCHAR(10),
ANALISIS_DE_DATOS VARCHAR(10),
MARKETING_DIGITAL VARCHAR(10),
MEDIOS_DE_PAGO VARCHAR(10),
FINANZAS VARCHAR(10),

FECHA_INICIO VARCHAR(8),
FECHA_FIN VARCHAR(8),
TIEMPO_DEMORA_MINUTOS INT,
FECHA_REGISTRO  VARCHAR(8),
AUT_PUBLICIDAD VARCHAR(20),
FECHA_PUB_PNDA VARCHAR(50)
)

--CARGA DE DATOS DESDE CSV (RUTADIGITAL2021al2024.csv)

BULK INSERT DatosBrutos_RutaDigital 
FROM 'C:\Users\Antonio\Mi unidad\Antonio\Proyectos\Portafolio\RutaDigital\RUTADIGITAL2021al2024.csv'
WITH
(
    FIELDTERMINATOR = '|',   -- Delimitador: pipe
    ROWTERMINATOR = '0x0a',  -- Carácter de nueva línea (estándar para la mayoría de archivos)
    FIRSTROW = 2             -- La primera fila es encabezado
);
GO
```
El proceso de ETL se centralizó en un **Stored Procedure (SP) de carga** `sp_CargarDataMart`, el cual ejecuta el **diagnóstico de datos brutos** y aplica las correcciones antes de la inserción a las tablas normalizadas.

### 2.1 Corrección de Inconsistencias

Se realiza un análisis de los datos de la tabla `DatosBrutos_RutaDigital` ya poblada y se anotan las observaciones:

### 2.1.1 Diagnóstico de Dimensiones Categóricas (Texto)

### Diagnóstico en `DIMENSIÓN NIVEL_DIGITALIZACION`
```sql
---- B. DIMENSIÓN NIVEL_DIGITALIZACION
	SELECT 
		NIVEL_DIGITALIZACION, 
		COUNT(*) AS Frecuencia
	FROM DatosBrutos_RutaDigital
	GROUP BY NIVEL_DIGITALIZACION
	ORDER BY Frecuencia DESC;

	--Nota: Corregir B+ísico por Básico
```
<img width="290" height="152" alt="image" src="https://github.com/user-attachments/assets/aaba9323-4486-4acf-b611-e0b2464c9186" />

* Corrección en el `sp_CargarDataMart`: Se usó REPLACE para normalizar el texto.
  
```sql
(...)
INSERT INTO dim_NivelDigitalizacion(NivelDigitalizacion)
		SELECT DISTINCT 
			UPPER(TRIM(REPLACE(NIVEL_DIGITALIZACION,'+í','á')))
		FROM
		DatosBrutos_RutaDigital
		WHERE TRIM(NIVEL_DIGITALIZACION) IS NOT NULL AND TRIM(NIVEL_DIGITALIZACION) <> ''
(...)
```

### Diagnóstico en `DIMENSIÓN CIIU`

```sql
---- C. DIMENSIÓN CIIU (Clave y Descripción)
	SELECT 
		CIIU, 
		DESCRIPCION_CIIU, 
		COUNT(*) AS Frecuencia
	FROM DatosBrutos_RutaDigital
	GROUP BY CIIU, DESCRIPCION_CIIU
	ORDER BY CIIU DESC;

	SELECT 
    'Valores Nulos (NULL)' AS Tipo_Vacio,
    COUNT(*) AS Frecuencia
	FROM DatosBrutos_RutaDigital
	WHERE 
		CIIU IS NULL
	
	--Nota: 3 CIIU vacíos
```
<img width="302" height="85" alt="image" src="https://github.com/user-attachments/assets/d966053a-8f49-4a4d-9d5d-3d92210f8e00" />

* Corrección en el `sp_CargarDataMart`: Se implementa la Clave Subrogada por Defecto (-1) para manejar los valores desconocidos.
```sql
(...)
SET IDENTITY_INSERT dim_CIIU ON;
		INSERT INTO dim_CIIU (ID_CIIU, CIIU, Descripcion_CIIU)
		VALUES (-1, 'N/A', 'DESCONOCIDO O NO APLICA'); -- 
		SET IDENTITY_INSERT dim_CIIU OFF;

		INSERT INTO dim_CIIU(CIIU,Descripcion_CIIU)
		SELECT DISTINCT 
		UPPER(TRIM(CIIU)),
		UPPER(TRIM(DESCRIPCION_CIIU))
		FROM
		DatosBrutos_RutaDigital T1
		WHERE TRIM(CIIU) IS NOT NULL AND TRIM(T1.CIIU) <> '';
(...)
```
### Diagnóstico en `DIMENSIÓN UBICACIÓN`
```sql
SELECT 
		DISTINCT PROVINCIA, 
		COUNT(*) AS Cantidad 
	FROM DatosBrutos_RutaDigital
	GROUP BY PROVINCIA
	ORDER BY PROVINCIA;

	--Nota: Corregir CA+æETE por CAÑETE, FERRE+æAFE por FERREÑAFE, MARA+æON por MARAÑON,
	-- DATEM DEL MARA+æON por DATEM DEL MARAÑON
```
<img width="245" height="263" alt="image" src="https://github.com/user-attachments/assets/e8fbbf40-09ba-44e0-a40d-e70c991fa039" />

```sql
SELECT 
		DISTINCT DISTRITO, 
		COUNT(*) AS Cantidad 
	FROM DatosBrutos_RutaDigital
	GROUP BY DISTRITO
	ORDER BY DISTRITO;

	--Nota: Corregir BA+æOS por BAÑOS, BRE+æA por BREÑA, CORONEL CASTA+æEDA por  CORONEL CASTAÑEDA
	--ENCA+æADA por ENCAÑADA, FERRE+æAFE por FERREÑAFE, I+æAPARI por IÑAPARI, LA TINGUI+æA por LA TINGUIÑA,
	-- LOS BA+æOS DEL INCA por LOS BAÑOS DEL INCA, NU+æOA por NUÑOA, PARI+æAS por PARIÑAS SA+æA por SAÑA,
	-- SAN VICENTE DE CA+æETE por SAN VICENTE DE CAÑETE
```
<img width="245" height="265" alt="image" src="https://github.com/user-attachments/assets/8d6620ea-37dd-447a-9f82-110f734fc8d1" />

* Corrección en el `sp_CargarDataMart`: Se usó REPLACE anidado para corregir caracteres especiales durante la inserción.
```sql
(...)
INSERT INTO dim_Ubicacion (Ubigeo,Departamento,Provincia,Distrito)
		SELECT DISTINCT 
		UPPER(TRIM(UBIGEO)),
		UPPER(TRIM(DEPARTAMENTO)),
		UPPER(TRIM(REPLACE(PROVINCIA,'+æ','Ñ'))),
		UPPER(TRIM(REPLACE(DISTRITO,'+æ','Ñ')))
		FROM
		DatosBrutos_RutaDigital
(...)
```
### 2.1.2 Diagnóstico de Faltantes (NULLs / Cadenas Vacías)
```sql
SELECT 
		SUM(CASE WHEN TRIM(TIPO) = '' OR TIPO IS NULL THEN 1 ELSE 0 END) AS Faltantes_TIPO,
		SUM(CASE WHEN TRIM(UBIGEO) = '' OR UBIGEO IS NULL THEN 1 ELSE 0 END) AS Faltantes_UBIGEO,
		SUM(CASE WHEN FECHA_REGISTRO IS NULL THEN 1 ELSE 0 END) AS Faltantes_FECHA,
		SUM(CASE WHEN TIEMPO_DEMORA_MINUTOS IS NULL THEN 1 ELSE 0 END) AS Faltantes_TIEMPO_DEMORA
	FROM DatosBrutos_RutaDigital;
	-- Nota: sin observaciones
```
### 2.1.3 Diagnóstico de Formato y Outliers (Fechas y Scores)

### Diagnóstico de fechas inválidas (Para DIM_TIEMPO)
```sql
SELECT 
		DISTINCT FECHA_REGISTRO, 
		COUNT(*) AS Frecuencia
	FROM DatosBrutos_RutaDigital
	WHERE TRY_CONVERT(DATE, FECHA_REGISTRO) IS NULL -- Busca valores que NO se pueden convertir a fecha
	GROUP BY FECHA_REGISTRO;
	
	--Nota: sin observaciones
```
### Diagnóstico de scores (Outliers)
```sql
SELECT 
		GESTION_EMPRESARIAL, 
		TRY_CAST(REPLACE(GESTION_EMPRESARIAL, ',', '.') AS FLOAT) AS Score_Porcentaje
	FROM DatosBrutos_RutaDigital
	WHERE
    TRY_CAST(REPLACE(GESTION_EMPRESARIAL, ',', '.') AS FLOAT) > 100 -- Buscamos valores que superan el 100% (escala 0-10000)
    OR TRY_CAST(REPLACE(GESTION_EMPRESARIAL, ',', '.') AS FLOAT) < 0;
-- Nota: valores > 100
```
<img width="330" height="77" alt="image" src="https://github.com/user-attachments/assets/0274a471-0dce-48c8-9f5a-4ce85c7ec6e5" />

```sql
SELECT 
		COMERCIO_ELECTRONICO, 
		TRY_CAST(REPLACE(COMERCIO_ELECTRONICO, ',', '.') AS FLOAT) AS Score_Porcentaje
	FROM DatosBrutos_RutaDigital
	WHERE
    TRY_CAST(REPLACE(COMERCIO_ELECTRONICO, ',', '.') AS FLOAT) > 100 -- Buscamos valores que superan el 100% (escala 0-10000)
    OR TRY_CAST(REPLACE(COMERCIO_ELECTRONICO, ',', '.') AS FLOAT) < 0;
	
	-- Nota: valores > 100
```
<img width="322" height="87" alt="image" src="https://github.com/user-attachments/assets/f96533bc-824c-4f1e-8e13-1c2e811fb8cf" />

```sql
	SELECT 
		ANALISIS_DE_DATOS, 
		TRY_CAST(REPLACE(ANALISIS_DE_DATOS, ',', '.') AS FLOAT) AS Score_Porcentaje
	FROM DatosBrutos_RutaDigital
	WHERE
    TRY_CAST(REPLACE(ANALISIS_DE_DATOS, ',', '.') AS FLOAT) > 100 -- Buscamos valores que superan el 100% (escala 0-10000)
    OR TRY_CAST(REPLACE(ANALISIS_DE_DATOS, ',', '.') AS FLOAT) < 0;
	
	-- Nota: valores > 100
```
<img width="297" height="61" alt="image" src="https://github.com/user-attachments/assets/c8de6e8a-4431-4812-8132-92579579294c" />

```sql
SELECT 
		MEDIOS_DE_PAGO, 
		TRY_CAST(REPLACE(MEDIOS_DE_PAGO, ',', '.') AS FLOAT) AS Score_Porcentaje
	FROM DatosBrutos_RutaDigital
	WHERE
    TRY_CAST(REPLACE(MEDIOS_DE_PAGO, ',', '.') AS FLOAT) > 100 -- Buscamos valores que superan el 100% (escala 0-10000)
    OR TRY_CAST(REPLACE(MEDIOS_DE_PAGO, ',', '.') AS FLOAT) < 0;
	
	-- Nota: valores > 100
```
<img width="320" height="77" alt="image" src="https://github.com/user-attachments/assets/5913bc06-dbd8-4060-b33a-86ea07851e66" />

* Corrección en el `sp_CargarDataMart`: Se aplicó REPLACE para decimales y la estructura CASE WHEN para limitar los valores entre 0.00 y 100.00.
```sql
(...)
--Scores normalizados
			CASE
				WHEN TRY_CONVERT(DECIMAL(5,2), REPLACE(T1.GESTION_EMPRESARIAL,',', '.')) > 100.00 THEN 100.00
				WHEN TRY_CONVERT(DECIMAL(5,2), REPLACE(T1.GESTION_EMPRESARIAL,',', '.')) < 0.00 THEN 0.00
				ELSE TRY_CONVERT(DECIMAL(5,2), REPLACE(T1.GESTION_EMPRESARIAL,',', '.'))
			END,
			
			CASE
				WHEN TRY_CONVERT(DECIMAL(5,2), REPLACE(T1.COMERCIO_ELECTRONICO,',', '.')) > 100.00 THEN 100.00
				WHEN TRY_CONVERT(DECIMAL(5,2), REPLACE(T1.COMERCIO_ELECTRONICO,',', '.')) < 0.00 THEN 0.00
				ELSE TRY_CONVERT(DECIMAL(5,2), REPLACE(T1.COMERCIO_ELECTRONICO,',', '.'))
			END,
            
			CASE
				WHEN TRY_CONVERT(DECIMAL(5,2), REPLACE(T1.ANALISIS_DE_DATOS,',', '.')) > 100.00 THEN 100.00
				WHEN TRY_CONVERT(DECIMAL(5,2), REPLACE(T1.ANALISIS_DE_DATOS,',', '.')) < 0.00 THEN 0.00
				ELSE TRY_CONVERT(DECIMAL(5,2), REPLACE(T1.ANALISIS_DE_DATOS,',', '.'))
			END,

			CASE
				WHEN TRY_CONVERT(DECIMAL(5,2), REPLACE(T1.MEDIOS_DE_PAGO,',', '.')) > 100.00 THEN 100.00
				WHEN TRY_CONVERT(DECIMAL(5,2), REPLACE(T1.MEDIOS_DE_PAGO,',', '.')) < 0.00 THEN 0.00
				ELSE TRY_CONVERT(DECIMAL(5,2), REPLACE(T1.MEDIOS_DE_PAGO,',', '.'))
			END,
(...)
```

Luego de identificar las inconsistencias y determinar correcciones, se procede a ejecutar el `sp_CargarDataMart` donde se aplica la limpieza y carga de datos a la tabla `hechos_Digitalizacion` normalizada.

Se visualiza la carga exitosa de todos los registros:
```sql
SELECT COUNT(*) AS TotalRegistros
FROM hechos_Digitalizacion;
GO
```
<img width="235" height="80" alt="image" src="https://github.com/user-attachments/assets/358035be-afce-4326-b0ea-17ea39381ead" />

* 

## 3. FIN DEL PROCESO ETL Y VISUALIZACIÓN DEL MODELO DIMENSIONAL

La ejecución exitosa del `sp_CargarDataMart` finaliza con la inserción de los **datos normalizados y corregidos** a la tabla **`hechos_Digitalizacion`**. El Data Mart está completo y **listo para ser cargado y consumido por Power BI** para la creación de la capa semántica (Fase II: Ingeniería DAX).

* **Resultado del ETL:** Se garantiza que la tabla de hechos contiene 13,944 registros limpios, con las correcciones de formato y outliers aplicadas.
* **Modelo:** Se implementó un modelo que combina el **Esquema Estrella** (SQL) con la estructura de **Copo de Nieve**(Power BI)  para el tiempo, optimizado para la agregación de métricas.

<img width="988" height="576" alt="image" src="https://github.com/user-attachments/assets/05698686-31c5-4098-bb7a-c6632e4b2984" />
