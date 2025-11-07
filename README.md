Procesamiento y Transformación de Datos para Análisis de Retención
🎯 Descripción del Proyecto
Este proyecto documenta el proceso de preparación y transformación de un dataset de clientes (Churn_Clientes.csv) con el objetivo de generar un conjunto de datos limpio y estructurado para el análisis de retención en un dashboard interactivo (Power BI).

El objetivo clave fue asegurar la integridad y la calidad de los datos para facilitar la exploración descriptiva de las tasas de abandono (Churn).

🛠️ Metodología de TransformaciónLa transformación del dataset original se realizó a través de un riguroso proceso enfocado en la limpieza de datos y la creación de una métrica binaria clave.
El resultado es el archivo processed_retention.csv, que se utiliza como fuente para la visualización.1. Limpieza de DatosManejo de Valores Nulos: Se identificaron y trataron los valores ausentes en las columnas clave, optando por [Indicar el método, ej: imputación con la media/mediana] o la eliminación de filas con datos inconsistentes.
Corrección de Tipos de Datos: Se ajustaron los tipos de datos de las columnas para asegurar la correcta interpretación (ej. convertir columnas numéricas a formato float o integer).
Identificación de Atípicos: Se revisaron y manejaron los valores atípicos (outliers) para mitigar su impacto en las métricas descriptivas.2. Transformación de la Variable ChurnLa variable original de retención se transformó en una métrica binaria (0/1) para simplificar la segmentación y el cálculo de la tasa de abandono en el dashboard.Columna OriginalValor Original (Ejemplo)Columna FinalValor Binario[Nombre original de Churn][Ej: 'Sí abandona']Churn_Binario1 (Abandono)[Nombre original de Churn][Ej: 'No abandona']Churn_Binario0 (Retenido)🔑 Propósito: Esta transformación permite calcular de forma directa y eficiente la Tasa de Churn y la Tasa de Retención como promedios sencillos en la herramienta de visualización (Power BI).

💾 Estructura del Dataset FinalEl conjunto de datos final y limpio se encuentra en data/processed_retention.csv.
A continuación, se detallan las nuevas columnas y la estructura de las métricas clave.
Columnas Enriquecidas 
El archivo final incluye las columnas originales limpias más las siguientes columnas de transformación clave:
Columna Descripción Tipo de Dato Notas de Transformación Churn_Binario Indicador binario (1=Abandono, 0=Retenido).Integer (0 ó 1)Derivada de la columna original [Churn].
