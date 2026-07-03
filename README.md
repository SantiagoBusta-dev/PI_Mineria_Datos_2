# Proyecto Integrador: Segmentación de Usuarios de Streaming (PI_Mineria_Datos_1)

## Información general
Este proyecto integrador aplica minería de datos y aprendizaje no supervisado para analizar el comportamiento y segmentar a los usuarios de una plataforma de streaming en Latinoamérica. Abarca el diagnóstico, la auditoría de calidad de datos crudos, el análisis exploratorio y la reducción de dimensionalidad mediante notebooks de desarrollo, preparando la base analítica para su futura puesta en producción.

* **Desarrollador:** SantiagoBusta-dev
* **Estado de la aplicación:** En desarrollo (Próximamente en Streamlit Cloud)
* **Log de transformaciones:** [Ver registro ETL](logs/pipeline_log.csv)

## Objetivo del proyecto
El objetivo principal es identificar y perfilar los diferentes segmentos de clientes en la plataforma de streaming basándose en sus datos demográficos, patrones de consumo mensual y nivel de interacción con el soporte técnico. Mediante esta segmentación estratégica, el negocio busca optimizar la retención de clientes, diseñar campañas de marketing personalizadas y detectar de forma temprana a los usuarios en riesgo de abandono. El proyecto busca establecer un flujo reproducible que transforme datos crudos e inconsistentes en conocimiento accionable y automatizado para la toma de decisiones.

## Dataset
El conjunto de datos original se encuentra en [data/raw/](data/raw/) y registra información de 8,160 usuarios a través de 8 variables clave: el identificador único (`user_id`), edad (`age`), plan de suscripción (`subscription_plan`), tiempo de reproducción mensual en minutos (`monthly_watch_time_mins`), país de residencia (`country`), género cinematográfico favorito (`favorite_genre`), última fecha de acceso (`last_login_date`) y la cantidad de tickets de soporte técnico abiertos (`customer_support_tickets`). Durante la inspección inicial documentada en el primer archivo de [notebooks/](notebooks/), se detectó que el archivo presentaba múltiples problemas de calidad, incluyendo registros duplicados, valores faltantes distribuidos en tres variables numéricas y categóricas, texto mal formateado con múltiples variantes ortográficas para representar los mismos países o planes, y severas anomalías estadísticas con valores numéricos fuera de los límites lógicos de la operación del negocio.

## Estructura del repositorio
El proyecto está organizado bajo la siguiente estructura formal:
* `data/raw/`: Contiene el dataset original sin modificaciones.
* `data/processed/`: Almacena el dataset final limpio y estandarizado listo para el modelado.
* `notebooks/`: Desarrollo ordenado de las etapas del proyecto desde la inspección inicial hasta las conclusiones.
* `app/`: Directorio reservado para la futura aplicación interactiva en Streamlit (en desarrollo).
* `reports/`: Aloja el espacio para el informe ejecutivo final en formato PDF (en desarrollo).
* `logs/`: Contiene el archivo de traza que audita los cambios en el volumen de datos.
* `requirements.txt`: Declaración de librerías y dependencias necesarias para la ejecución.

## Preparación y calidad de datos
La limpieza de los datos crudos se detalla en los notebooks de calidad y se auditó paso a paso en [logs/pipeline_log.csv](logs/pipeline_log.csv), logrando una tasa final de retención del 90.80% (7,409 filas resultantes). Primero, se imputaron los valores nulos utilizando la mediana (757.4 minutos) para el tiempo de reproducción y la moda para el género cinematográfico preferido. Segundo, se estandarizó la columna de fechas a tipo datetime, lo que derivó en la eliminación de 384 registros con formatos corruptos o inválidos de origen. Tercero, se removieron 131 registros completamente duplicados para evitar sesgos en los modelos analíticos. Cuarto, se normalizó el texto mediante diccionarios específicos, consolidando los planes en 3 categorías únicas, los países en 7 regiones de Latinoamérica y los géneros en español. Finalmente, se aplicaron filtros lógicos para eliminar valores incoherentes, reteniendo usuarios con edades de 0 a 90 años, tiempos de reproducción de 0 a 5,000 minutos y un máximo de 20 tickets de soporte. El dataset depurado se exportó con éxito a [data/processed/](data/processed/).

## Resumen del análisis exploratorio
El análisis exploratorio de datos (EDA) detallado en los cuadernos de trabajo reveló que la base de usuarios es demográficamente equilibrada, con una distribución de edad homogénea concentrada entre los 18 y los 60 años. En contraste, el tiempo de reproducción mensual presenta un marcado sesgo positivo hacia la derecha, donde la gran mayoría de los clientes consume una cantidad moderada de minutos, mientras que un grupo selecto de usuarios intensivos empuja la distribución hacia el límite de los 5,000 minutos. Geográficamente, la comedia y el drama se consolidaron como los géneros preferidos y más vistos en toda Latinoamérica, detectándose como única particularidad local un interés significativamente mayor por el suspenso y los thrillers en los mercados de Argentina y Colombia. Para el posterior modelado analítico, las variables numéricas fueron estandarizadas mediante un escalador estadístico para colocar todas las métricas en la misma escala, y las cualitativas se transformaron a vectores numéricos utilizando codificación One-Hot.

## Reducción de dimensionalidad
Debido al incremento en la cantidad de variables tras la codificación One-Hot, se aplicó la técnica de Análisis de Componentes Principales (PCA) para simplificar el espacio de características. Los resultados matemáticos demostraron que la primera componente principal explica por sí sola el 41% de la variabilidad de los datos, mientras que alsafe combinar las dos primeras componentes se logra capturar más del 72% de la varianza acumulada del comportamiento de los usuarios, justificando el modelado en un plano bidimensional. Al ejecutar el algoritmo de K-Means en este espacio reducido, y validar la estructura mediante el método del codo y el coeficiente de silueta, los datos reflejaron la existencia de tres grupos de clientes bien diferenciados: Usuarios Casuales (bajo consumo y soporte), Usuarios Intensivos y Fieles (alto consumo en minutos) y Usuarios de Alto Riesgo (consumo medio pero la mayor cantidad de reclamos en soporte técnico).

## Visualización interactiva
*Nota: Esta sección se encuentra actualmente en fase de desarrollo.* Próximamente, el comportamiento de los clusters y las distribuciones analizadas en los cuadernos podrán explorarse de forma interactiva mediante una interfaz web en el directorio [app/](app/). La plataforma interactiva permitirá filtrar los patrones del análisis exploratorio por país y evaluar las características operativas de cada grupo de usuarios en tiempo real.

## Cómo ejecutar localmente
Para replicar el análisis de los notebooks en tu entorno local, sigue estos pasos:
1. Clona este repositorio en tu máquina local.
2. Asegúrate de tener instalado Python 3.9 o superior.
3. Instala todas las dependencias obligatorias del proyecto ejecutando en la terminal:
   `pip install -r requirements.txt`
4. Abre tu entorno de Jupyter o VS Code para explorar y ejecutar cronológicamente los archivos dentro de la carpeta [notebooks/](notebooks/).

## Conclusiones
El proyecto integrador demostró la viabilidad de transformar datos crudos y ruidosos en un sistema estructurado de segmentación con un fuerte valor operativo. Las técnicas de PCA y K-Means permitieron organizar la masa de usuarios en tres perfiles comerciales claros, aislando con éxito a un grupo crítico de clientes de alto riesgo que demandan soporte masivo sin generar un consumo alto de minutos. La integración de todo el proceso analítico en los notebooks garantiza un flujo de trabajo transparente y reproducible, permitiendo auditar la calidad de los datos de forma automática en los archivos de log. Esta base de datos limpia y procesada proporciona al equipo técnico y de negocio el cimiento necesario para la construcción inmediata de la interfaz interactiva y el despliegue de estrategias eficientes de retención basadas en datos reales.
