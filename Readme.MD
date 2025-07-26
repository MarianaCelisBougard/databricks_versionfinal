#  Proyecto ETL SECOP

Este proyecto implementa un flujo completo de extracción, transformación y carga (ETL) sobre los datos abiertos de contratación pública publicados en el portal oficial [datos.gov.co](https://www.datos.gov.co), específicamente desde el dataset del **SECOP II** (Sistema Electrónico de Contratación Pública).

El objetivo principal es construir una base de datos robusta, confiable y estructurada que permita analizar la contratación estatal, identificar patrones, generar alertas o alimentar modelos analíticos.

---

##  Objetivo General

Desarrollar un proceso automatizado en PySpark para:

- Descargar datos del SECOP II de manera eficiente y segura.
- Generar identificadores únicos para los contratos.
- Detectar duplicados y asegurar consistencia estructural.
- Almacenar la información en tablas Delta optimizadas para análisis.

---

##  Estructura del Proceso

### 1.  Configuración Inicial

- Se importa la librería oficial de Socrata (`sodapy`) para conectarse a la API de datos abiertos de Colombia.
- Se utilizan widgets interactivos para seleccionar dinámicamente el `dataset_id` deseado.
- Se configuran los parámetros de autenticación y el número de registros por lote (`limit = 500000`).

---

### 2.  Descarga de Datos por Lotes

- El proceso de extracción se realiza mediante paginación (`offset`) y control de errores con reintentos (`try/except`).
- Cada lote descargado se convierte en un DataFrame de Spark y se almacena en una tabla Delta temporal:  
  **`main.diplomado_datos.secop_restante_tmp`**
- El ciclo se repite hasta agotar los registros del dataset.

---

### 3.  Unificación y Limpieza de Datos

- Se leen los datos previamente cargados desde la tabla `secop_bronze` y se combinan con los nuevos registros descargados (`secop_restante_tmp`).
- Se eliminan registros duplicados usando `dropDuplicates` sobre las columnas clave:  
  `['codigo_proceso', 'nombre_entidad', 'objeto_contrato', 'valor_total', 'fecha_firma']`

---

### 4.  Generación de Identificador Único (`id_sha2`)

- Se crea un nuevo campo `id_sha2` aplicando la función `sha2` (hash SHA-256) sobre la concatenación de campos relevantes

## 📂 Datasets Utilizados

Los siguientes conjuntos de datos públicos fueron utilizados:

- **Contratación Pública (SECOP II):**
  - URL: [https://www.datos.gov.co/resource/rpmr-utcd.csv](https://www.datos.gov.co/resource/rpmr-utcd.csv)
  - Descripción: Contiene información sobre los contratos registrados en el Sistema Electrónico para la Contratación Pública.

- **Matrícula Oficial en Educación (MEN):**
  - URL: [https://www.datos.gov.co/resource/nudc-7mev.csv](https://www.datos.gov.co/resource/nudc-7mev.csv)
  - Descripción: Reporte oficial de matrículas escolares por municipio y nivel educativo.


## 🚀 Instrucciones para Ejecutar el Proyecto

1. Clona este repositorio:
   ```bash
   git clone https://github.com/MarianaCelisBougard/databricks_versionfinal
   cd tu_repositorio
