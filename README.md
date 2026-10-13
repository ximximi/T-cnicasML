# Clasificación de Entornos Inmobiliarios: Análisis Comparativo CNN vs MLP

Este repositorio contiene el código fuente, modelos y documentación correspondientes a la automatización del reconocimiento de entornos en propiedades inmobiliarias mediante imágenes capturadas por drones. El proyecto contrasta de manera experimental el rendimiento de una arquitectura base de Perceptrón Multicapa (MLP) frente a una Red Neuronal Convolucional (CNN).

## Autores
* Yassmin Bazan
* Felipe Rubio
* Ximena Soliz

## Descripción del Dataset
Se utiliza el **Intel Image Classification Dataset**, manteniendo la configuración de 6 clases categóricas para establecer una comparativa directa y consistente con evaluaciones previas:
* `buildings` (edificios)
* `forest` (bosques)
* `glacier` (glaciares)
* `mountain` (montañas)
* `sea` (mar)
* `street` (calles)

**Parámetros de preprocesamiento:**
* **Partición:** 80% Entrenamiento y Validación / 20% Prueba.
* **Resolución de tensores:** 150x150 píxeles a color (150, 150, 3).
* **Normalización:** Reescalado de píxeles al rango [0, 1] (`1./255`).
* **Reproducibilidad:** Semilla aleatoria (Seed) fijada en 29.

## Estructura del Repositorio
* `data/`: Directorio destinado a alojar las particiones del conjunto de datos (`seg_train`, `seg_test`, `seg_pred`). *Nota: Para optimizar el almacenamiento del repositorio, los datos se descargan de manera automatizada durante la ejecución del código.*
* `notebooks/`: Aloja el cuaderno principal de Jupyter (`E2_FelipeRubio_XimenaSoliz_YassminBazan.ipynb`) con el desarrollo estructurado en Python.
* `models/`: Directorio para el volcado y almacenamiento de la arquitectura de la red neuronal exportada y sus respectivos pesos tras finalizar el entrenamiento.
* `images/`: Carpeta que concentra las visualizaciones generadas, incluyendo los Mapas de Características (Feature Maps), curvas de función de pérdida y precisión, y la Matriz de Confusión.
* `informe.md`: Documento técnico principal que detalla el problema de negocio, la justificación matemática de la arquitectura CNN diseñada, la auditoría de errores y las conclusiones finales.

## Requisitos Técnicos e Instalación
El algoritmo requiere un entorno de Python 3 con las siguientes dependencias principales instaladas:
* `tensorflow` (v2.x)
* `numpy`
* `pandas`
* `matplotlib`
* `scikit-learn`

## Instrucciones de Ejecución
1. **Entorno de Trabajo:** Se recomienda cargar el cuaderno alojado en `/notebooks` directamente en **Google Colab**.
2. **Aceleración por Hardware (GPU):** El entrenamiento de la red convolucional requiere una cantidad intensiva de Operaciones de Punto Flotante (FLOPS). Es mandatorio activar la aceleración por hardware para evitar tiempos de cómputo excesivos. En Colab, navegue a *Entorno de ejecución* $\rightarrow$ *Cambiar tipo de entorno de ejecución* $\rightarrow$ Seleccionar **T4 GPU**.
3. **Despliegue:** Ejecute las celdas de forma secuencial. El bloque inicial de "Preparación de Datos" descargará, descomprimirá y estructurará los tensores automáticamente en el directorio virtual antes de iniciar la compilación y el ajuste (fit) del modelo.

