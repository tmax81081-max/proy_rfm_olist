# **Documentación del Proyecto: Segmentación de Clientes RFM (Olist)**

Este proyecto implementa una arquitectura de Ingeniería de Datos para el análisis transaccional de clientes (Recencia, Frecuencia, Valor Monetario) de Olist (Brasil), culminando en un modelo de Machine Learning (K-Means) que clasifica a los clientes en segmentos accionables.

El objetivo principal es **transformar el dato transaccional en inteligencia de negocio** para optimizar campañas de marketing y retención.

## **1\. Arquitectura del Proyecto (Capas de Medallón)**

El proyecto sigue la arquitectura de **Capas de Medallón** (Medallion Architecture), asegurando calidad, incrementalidad y gobernanza del dato a través de tres fases principales, ejecutadas mediante **Jobs ETL** secuenciales en Databricks.

### **A. Capa Bronce (Ingesta Bruta)**

| Componente | Descripción | Función |
| :---- | :---- | :---- |
| **Datos Fuente** | Archivos CSV brutos originales (e.g., olist\_orders\_dataset.csv). | Almacenamiento inmutable del dato tal como fue recibido. |
| **Jobs ETL** | Procesos de carga inicial. | Tarea de copiar los archivos fuente a tablas Delta Lake en el formato original. |

### **B. Capa Plata (Limpieza y Feature Engineering)**

| Componente | Descripción | Función |
| :---- | :---- | :---- |
| **Limpieza** | Tratamiento de nulos, duplicados, y conversiones de tipo de dato. | Asegurar que los datos sean consistentes y confiables. |
| **Generación de Features** | Cálculo de las métricas clave: **Recencia (R), Frecuencia (F) y Valor Monetario (M)**. | Transformar transacciones en métricas específicas para el modelado de clientes. |
| **Escalado** | Normalización de las métricas R, F y M (e.g., usando StandardScaler). | Preparar las *features* para el modelo K-Means (Machine Learning). |

### **C. Capa Oro (Consumo y Productos de Datos)**

| Componente | Descripción | Función |
| :---- | :---- | :---- |
| **Modelo K-Means** | Algoritmo de *clustering* no supervisado aplicado a las métricas RFM escaladas. | Identificar automáticamente grupos de clientes con comportamientos similares. |
| **Tablas Curadas** | Vistas y tablas finales listas para el consumo directo. | Servir datos limpios, agregados y modelados para el negocio. |

## **2\. Productos Clave y Resultados**

La principal función de este proyecto es generar una tabla final de clientes con su segmento asignado y una estrategia de negocio recomendada.

### **Modelo de Segmentación**

El proyecto utiliza **K-Means con K=5** clústeres, que ofrecen el mejor equilibrio entre cohesión del grupo (Índice de Silueta \~0.29) y la diferenciación de valor para el negocio.

| Segmento | Descripción del Comportamiento | Estrategia de Negocio |
| :---- | :---- | :---- |
| **💎 Premium** | Alta Frecuencia, Alto Valor, Recencia Baja. | **Retención.** Fomentar la lealtad y obtener *feedback* (Encuestas de Satisfacción). |
| **💰 Ballena** | Alto Valor (M) Dominante, Frecuencia Media. | **Reactivación VIP.** Alto potencial, enviar ofertas de *alto valor*. |
| **👋 Nuevo** | Recencia Alta, Baja Frecuencia/Valor. | **Conversión.** Flujo de *cross-selling* y *up-selling* para asegurar una segunda compra. |
| **😴 Voluminoso** | Frecuencia Alta, Valor Monetario Bajo. | **Monitoreo Logístico.** Optimizar costos de envío; no priorizar marketing. |
| **🔥 Llamarada** | Recencia Baja, Frecuencia Baja. | **Bajo Valor.** Riesgo de *churn*; re-targeting masivo o campañas de bajo costo. |

### **Tablas de Consumo Generadas (Capa Oro)**

Esta capa contiene todas las tablas agregadas, modeladas y optimizadas para el consumo en BI y aplicaciones de negocio.

| Nombre de Tabla | Propósito y Función |
| :---- | :---- |
| **segmentacion\_clientes** | Tabla principal que mapea customer\_id a su segmento\_asignado. Es el *output* directo del modelo K-Means. |
| **resumen\_segmentos** | Agregación de métricas promedio (R, F, M) por clúster. Utilizada para monitoreo del rendimiento y salud de cada grupo. |
| **comparison\_matrix** | Matriz de comparación de rendimiento (ej. A/B testing, evolución de segmentos). |
| **logistics\_analysis** | Análisis detallado de costos y eficiencia de entrega por segmento y región. Clave para la estrategia **Monitoreo Logístico**. |
| **olist\_orders\_gold** | Versión final, limpia y unificada de las transacciones de órdenes de Olist (fuente de verdad). |
| **reporte\_base** | Tabla agregada de alto nivel, ideal para cuadros de mando (dashboards) ejecutivos. |
| **reporte\_final** | Versión final del reporte consolidado, que incluye *join* de todas las métricas. |
| **resumen\_negocio** | Resumen ejecutivo de las métricas clave de negocio (KPIs). |
| **satisfaction\_analysis\_adv** | Análisis avanzado de la satisfacción del cliente (CSAT) basado en los clústeres. |
| **satisfaction\_final** | Datos finales y puntuaciones de satisfacción, listo para BI. |
| **seller\_analysis** | Análisis de rendimiento y geolocalización de vendedores. |
| **top\_5\_geo** | Identificación de las 5 regiones geográficas con mejor/peor rendimiento. |
| **top\_categories** | Categorías de producto más vendidas y su impacto en el valor (M) de los segmentos. |
| **top\_productos** | Listado de productos de mayor demanda para optimización de inventario. |

## **3\. Componentes de Git: Control de Versiones**

El repositorio **proy\_rfm\_olist** se utiliza para versionar todo el código de procesamiento, análisis y modelado del proyecto, garantizando la trazabilidad y la colaboración entre los ingenieros de datos y científicos de datos.

### **Componentes Clave**

| Componente Git | Propósito en el Proyecto | Archivos Incluidos (Ejemplos) |
| :---- | :---- | :---- |
| **main (o master)** | Rama de **Producción**. Contiene el código estable que está ejecutándose en los Jobs de Databricks para actualizar las tablas Gold. | Versiones finales de los *Notebooks* ETL y ML. |
| **develop** | Rama de Integración. Código funcional que ha pasado la revisión de pares (Code Review) y está listo para ser probado en un ambiente Staging. | Código de features y limpieza antes de la aprobación final. |
| **feature/nombre\_tarea** | Ramas de Desarrollo. Son ramas temporales creadas por desarrolladores individuales para implementar una nueva característica o corrección (ej: calcular una métrica RFM diferente). | *Notebooks* en desarrollo, pruebas unitarias (tests/). |
| **dags/ (Jobs)** | Directorio que contiene los scripts de orquestación (ej. Airflow o Azure Data Factory) para definir la secuencia de ejecución de los Jobs ETL. | Scripts de Python o JSON de configuración de Jobs. |
| **src/** | Contiene funciones de Python reutilizables, librerías personalizadas y código modular, no necesariamente notebooks. | rfm\_utils.py (cálculo de RFM), ml\_model.py (entrenamiento de K-Means). |
| **.gitignore** | Lista de archivos y directorios que Git debe ignorar. | Datos grandes (Big Data), resultados de modelos (*pickles*), archivos .ipynb\_checkpoints, logs de ejecución. |
| **README.md** | Documentación de alto nivel del proyecto. | Este archivo, resumiendo arquitectura y resultados. |

### **Flujo de Trabajo (GitFlow Simplificado)**

1. El desarrollador crea una rama **feature/** a partir de develop.  
2. Desarrolla el código (Notebooks, Scripts) localmente o en el *Workspace* de Databricks.  
3. Una vez completa la característica, se abre una **Pull Request (PR)** hacia la rama develop.  
4. Tras la revisión y aprobación, el código se fusiona (merge) a develop.  
5. Una vez que develop es estable, se crea una PR final hacia main para el despliegue a Producción.

## **4\. Tecnologías Utilizadas**

* **Plataforma:** Databricks (para ejecución de Jobs ETL y ML).  
* **Lenguaje:** Python.  
* **Librerías de Datos:** PySpark (ETL), Pandas (Análisis).  
* **Librerías ML:** Scikit-learn (KMeans, silhouette\_score, StandardScaler).  
* **Visualización:** Matplotlib, Seaborn (para gráficos de Codo y Silueta).  
* **Control de Versiones:** Git.