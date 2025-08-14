# Telecom X — Análisis de Evasión de Clientes (Churn)

Este repositorio/notebook contiene un flujo **ETL + EDA** para analizar la evasión de clientes en **Telecom X**.  
El objetivo es **entender patrones de churn** y proponer acciones de retención basadas en datos.

---

## 🧭 Contenido

- **Objetivo**: medir y explicar la evasión (churn) y sus factores asociados.
- **Alcance**: extracción de datos, transformación/limpieza, análisis exploratorio, visualizaciones e informe final.
- **Stack**: Python (Google Colab), `pandas`, `numpy`, `matplotlib`.

---

## 📂 Estructura del Notebook

1. **EXTRACCIÓN**
   - Montaje de Google Drive.
   - Lectura del dataset en formato **JSON**.
   - **Aplanado** de campos anidados con `pd.json_normalize`.

2. **TRANSFORMACIÓN**
   - Tipificación: `customerID` como string; campos numéricos a `float/int`.
   - **Imputación mínima** de `account.Charges.Total`:
     - Si `tenure = 0` ⇒ Total = 0.
     - Si `tenure > 0` ⇒ Total ≈ Monthly × tenure.
   - Creación de variables:
     - `Churn_bin` (1 = yes, 0 = no).
     - `Cuentas_Diarias` = `account.Charges.Monthly` / 30.4375.
   - Normalización ligera de categóricas (minúsculas/espacios) y `category`.

3. **CARGA Y ANÁLISIS (EDA)**
   - Descriptivos globales y por churn (`describe`, `groupby`).
   - Distribución de churn (conteos y %).
   - **Churn por categoría**: contrato, método de pago, seguridad/soporte.
   - **Numéricas por churn**: `tenure`, `Monthly`, `Total`, `Cuentas_Diarias` (histogramas y boxplots).
   - Ranking rápido de relevancia:
     - Numéricas: |correlación| con churn.
     - Categóricas: **lift** (max − min de la tasa de churn por categoría).

4. **INFORME FINAL**
   - Introducción y contexto del problema.
   - Resumen de limpieza/tratamiento.
   - Hallazgos principales (tablas y gráficos clave).
   - Conclusiones e **insights** accionables.
   - **Recomendaciones** de retención (p. ej., migración a contrato anual, activar seguridad/soporte, foco en tenure bajo + monthly alto).

---

## 🚀 Cómo ejecutar

> Recomendado: **Google Colab** (no requiere instalación local).

1. Abre el notebook en Colab.
2. Ejecuta la celda de **EXTRACCIÓN** para montar Drive y leer el JSON.
3. Ejecuta la sección **TRANSFORMACIÓN** (tipos, imputaciones, columnas derivadas).
4. Corre **CARGA Y ANÁLISIS** para ver métricas y gráficos.
5. Ejecuta **INFORME FINAL** para generar el reporte dentro del notebook.

---

## 📦 Dependencias

- Python 3.10+
- `pandas` 2.x
- `numpy` 1.26+
- `matplotlib` 3.8+

En Colab ya vienen instaladas; si trabajas localmente:

```bash
pip install -U pandas numpy matplotlib

🗃️ Datos

Fuente: archivo TelecomX_Data.json en Google Drive.

Estructura: diccionarios anidados por cliente (customer, phone, internet, account…).

Clave primaria: customerID.

🧪 Salidas esperadas

Tablas:

describe() global y por Churn_bin.

Tasa de churn por categoría (contrato, método de pago, seguridad, soporte).

Gráficos:

Distribución global de churn (barras).

Barras de % churn por categoría y barras apiladas (permanecen vs churn).

Histogramas superpuestos y boxplots para numéricas por churn.

Informe Final con conclusiones y recomendaciones.

🧹 Limpieza y criterios

Conversión robusta a numérico con errors='coerce' (texto/espacios ⇒ NaN).

Imputación explicable de account.Charges.Total.

Estandarización de categóricas (minúsculas/strip) para evitar categorías duplicadas.

Verificación de duplicados por customerID (esperado: 0).

⚠️ Problemas comunes y soluciones

ValueError: could not convert string to float: ' '
Convierte columnas numéricas con:

df[col] = pd.to_numeric(df[col], errors='coerce')


KeyError: 'Churn_bin'
Asegura crearla antes del EDA:

df['Churn_bin'] = df['Churn'].astype(str).str.strip().str.lower().eq('yes').astype(int)


JSON anidado (solo 5–6 columnas tipo dict)
Aplana con:

df = pd.json_normalize(df.to_dict('records'), sep='.')


Nulos en account.Charges.Total
Aplica la imputación mínima (0 si tenure=0; si no, Monthly * tenure).

✅ Buenas prácticas del proyecto

Mantener el orden EXTRACCIÓN → TRANSFORMACIÓN → ANÁLISIS → INFORME.

Documentar decisiones (imputaciones, supuestos, filtros).

Guardar versión transformada en Parquet para reutilizar:

df.to_parquet('TelecomX_Data_prepared.parquet', index=False)

🔍 Qué se aprendió

Cómo aplanar JSON y tipar datos correctamente.

Diseño de una métrica diaria a partir de montos mensuales.

Identificación de factores de churn (contrato mensual, método de pago, tenure bajo, servicios de seguridad/soporte).

Elaboración de un informe final reproducible dentro del notebook.

📬 Contacto

Si tienes dudas o sugerencias, abre un issue o envía un mensaje.
¡Gracias por revisar este proyecto!
