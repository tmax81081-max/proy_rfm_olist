# proy_rfm_olist
# Leame
Este es un proyecto de segmentación de clientes e identificación de los clientes preferenciales de la tienda Olist
que consta de:

## Ingesta
Sera el bloque de captura de datos desde el repositorio de google sheet, como una simulación de ingesta periódica

## Arquitectura Medallón
Esta es la construcción de la arquitectura medallón, para seguir el pipeline

### Creación de la Base Bronce y su carga
Aquí se carga los datos obtenidos de la base olist para ser preprocesados como una limpieza temporal previa

### Creación de la base Plata Limpieza y la Master table
Aquí se construye la tabla maestra que permitirá realizar el procesamiento de los datos

### Creación de las bases Oro Procesamiento según el modelo de segmentos e identificación de cliente
Aquí se procesan de acuerdo al modelo y se crean las tablas de identificación de segmentos y ademas se identifican los clientes preferencial

## Presentación de Resultados en un dashboard
Aquí se construyen los dashboard que se mostraran como resultados obtenidos e indices de efectividad y métricas adicionales
