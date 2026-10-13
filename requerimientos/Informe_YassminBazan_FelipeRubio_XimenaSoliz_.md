---
# Informe Técnico: Clasificación de Imágenes con Redes Neuronales Convolucionales (CNN) vs Perceptrón Multicapa (MLP)

**Asignatura:** Técnicas Avanzadas de Machine Learning
**Fecha:** 13 Octubre 2026
**Integrantes:** Yassmin Bazan, Felipe Rubio, Ximena Soliz.

---

## a. Documentación del proceso desarrollado

### Variante del Problema y Conjunto de Datos
Para esta evaluación, hemos decidido mantener el enfoque en la clasificación de paisajes, trabajando con un conjunto de datos compuesto por **6 clases de paisajes**. El objetivo es mantener una línea base justa que nos permita comparar el rendimiento de extracción espacial de la CNN frente al modelo MLP clásico desarrollado en la evaluación anterior.

### Preparación de las Imágenes
El pipeline de datos consistió en los siguientes pasos:
1. **Redimensionamiento:** Las imágenes fueron estandarizadas a una resolución de `150x150` píxeles con 3 canales de color (RGB) para mantener el formato matricial (2D) nativo de la CNN.
2. **Normalización:** Se aplicó una capa de `Rescaling(1./255)` para llevar los valores de los píxeles del rango [0, 255] a [0.0, 1.0], facilitando la convergencia del modelo.
3. **Codificación y Partición:** Se utilizó `label_mode='categorical'` (One-Hot Encoding) para las etiquetas. Los datos se dividieron rigurosamente en conjuntos de entrenamiento, validación y prueba.

## b. Justificación de las decisiones adoptadas

### Arquitectura de la Red Neuronal Convolucional (CNN)
Para superar las limitaciones detectadas en el modelo MLP, el cual perdía la jerarquía espacial y el contexto al aplanar prematuramente la imagen en un vector 1D, se implementó una arquitectura basada en Redes Neuronales Convolucionales (CNN).
Las decisiones técnicas que fundamentan este diseño son:
La red fue diseñada siguiendo una jerarquía visual progresiva.

*   **Capas convolucionales (`Conv2D`):** Se utilizaron filtros pequeños de `(3x3)` para explorar el detalle local de las imágenes de manera eficiente. La cantidad de filtros aumenta en profundidad (32 -> 64 -> 128) para que la red aprenda desde características básicas (bordes, colores) en las primeras capas, hasta texturas y objetos complejos en las capas profundas.
*   **Capas de submuestreo (`MaxPooling2D`):** Se intercalaron ventanas de `(2x2)` para reducir la dimensionalidad espacial a la mitad en cada bloque. Esto permite extraer las características más fuertes, hacer que la red sea invariante a pequeñas traslaciones y reducir drásticamente la carga computacional antes de llegar a la capa plana.
*   **Función de activación ReLU:** Implementada en todas las capas ocultas para introducir la no-linealidad necesaria para el reconocimiento de patrones complejos, evitando el desvanecimiento del gradiente.
*   **Clasificador (`Dense` y Softmax):** Una capa densa oculta de 128 neuronas se encarga del razonamiento lógico, seguida de una capa de salida con 6 neuronas (función Softmax) que entrega las probabilidades probabilísticas para nuestras 6 clases de paisajes.

### Ausencia de Técnicas de Regularización
De acuerdo a los objetivos y lineamientos técnicos de esta evaluación, **se omitió deliberadamente el uso de capas de regularización** (como Dropout o penalizaciones L1/L2). El propósito de esta decisión fue evaluar el comportamiento natural y puro de la arquitectura convolucional en la extracción de características, observando su nivel de adaptación a los datos de entrenamiento para establecer una línea base de comparación limpia frente al modelo MLP.

---

## c. Matriz de responsabilidades
| Integrante | Rol Principal | Tareas en Notebook (`.ipynb`) | Tareas en Informe y Presentación | % Contribución | Estado |
| :--- | :--- | :--- | :--- | :---: | :---: |
| **Yassmin Bazán** | Preparación de Datos y Análisis Visual | • Punto 1: Introducción, justificación del problema y variante.<br>• Punto 2: Pipeline de datos (rescaling 150x150x3, split y muestreo visual).<br>• Punto 7: Programación de extracción de Feature Maps y mapas de activación por clase. | • Redacción de Sección *a* (Documentación del proceso).<br>• Revisión estructural del informe técnico.<br>• Preparación de diapositivas de datos en PPT. | 33.3% | **Completado** |
| **Ximena Soliz** | Arquitectura y Entrenamiento CNN | • Punto 3: Diseño de la CNN en Keras (Conv2D + MaxPool2D sin Dropout).<br>• Punto 4: Compilación con Adam y entrenamiento por 10 épocas en GPU.<br>• Generación de curvas de aprendizaje (Loss y Accuracy vs Épocas). | • Redacción de Sección *b* (Justificación de arquitectura y decisiones).<br>• Justificación matemática de los hiperparámetros.<br>• Preparación de diapositivas de arquitectura en PPT. | 33.3% | **Completado** |
| **Felipe Rubio** | Evaluación, Benchmarking y Conclusiones | • Punto 5: Evaluación rigurosa en test (Accuracy, Precision, Recall, F1) y matriz de confusión.<br>• Punto 6: Benchmark comparativo formal CNN vs MLP.<br>• Auditoría de predicciones visuales y análisis técnico de causas de error. | • Redacción de Sección *c* (Matriz de responsabilidades).<br>• Redacción de Sección *d* (Conclusiones finales y propuestas de regularización).<br>• Consolidación del informe y coordinación de PPT. | 33.3% | **Completado** |
---

## d. Conclusiones obtenidas

### 1. Cuadro Comparativo Experimental Consolidado (MLP vs CNN)

| Métrica / Dimensión | Perceptrón Multicapa (MLP - Ev1) | Red Neuronal Convolucional (CNN - Ev2) | Análisis Comparativo |
| :--- | :---: | :---: | :--- |
| **Entrada del Modelo** | Tensor aplanado a vector 1D (67.500 valores) | Tensor matricial 2D/3D (150, 150, 3) | El MLP destruye la estructura espacial; la CNN procesa la imagen en 2D tal como es. |
| **Total de Parámetros** | **8.648.774** | **4.829.126** | **Reducción del 44.2% de parámetros**. La CNN reutiliza filtros pequeños en vez de conectar todo a la fuerza. |
| **Accuracy en Test** | 54.37% | **77.63%** (0.7763) | **Aumento de más de 23 puntos de precisión (+23.26%)** al clasificar 3.000 fotos nuevas. |
| **Precision Ponderada** | 55.00% / 68.00% | **78.73%** (0.7873) | Mayor confiabilidad; el modelo casi no inventa falsos positivos. |
| **Recall Ponderado** | 54.00% / 68.00% | **77.63%** (0.7763) | Gran capacidad para encontrar y reconocer las fotos de cada clase. |
| **F1-Score Ponderado** | 54.00% / 68.00% | **77.71%** (0.7771) | Balance armónico muy sólido entre precisión y cobertura. |
| **Tiempo de Entrenamiento** | 42.4 seg total (Aprox. 4.2s/época en CPU) | **132.77 seg total** (Aprox. 13.3s/época en GPU T4) | La CNN hace más cálculos matemáticos por imagen, pero con GPU corre en poco más de 2 minutos. |
| **Mecanismo de Aprendizaje** | Promedio general de intensidades de píxel | Extracción jerárquica de características | La CNN reconoce un patrón sin importar en qué esquina de la foto aparezca. |

---

### 2. Análisis Detallado de los Resultados

**Comparativa de Desempeño CNN vs MLP:**  
La red convolucional (CNN) superó ampliamente al modelo MLP en todas las métricas. Al evaluar con las 3.000 imágenes de prueba que el modelo nunca había visto, la CNN alcanzó un **Accuracy de 77.63%** y un **F1-Score de 0.7771** (llegando a un pico en validación de 81.33%), superando por más de 23 puntos al MLP de la entrega anterior. En cuanto al tiempo, el MLP tardó 42.4 segundos en CPU (Aprox. 4.2 segundos por época), mientras que la CNN tomó 132.77 segundos en GPU T4 (Aprox. 13.3 segundos por época). Aunque la CNN tarda un poco más porque revisa las fotos con lupa mediante filtros, este ligero tiempo extra es plenamente justificable por el salto drástico en el nivel de precisión.

**Cantidad de Parámetros:**  
Un resultado muy interesante es que la CNN logró ser mucho más inteligente teniendo **casi la mitad de parámetros que el MLP**. El MLP requirió **8.648.774 parámetros**, ya que al estirar la imagen en una sola fila de 67.500 píxeles, cada píxel se conectaba obligatoriamente con cada neurona. La CNN, en cambio, necesitó solo **4.829.126 parámetros** (un 44.2% menos). Esto demuestra la enorme ventaja de las capas convolucionales: en vez de conectar todo a lo bruto, usan pequeños filtros de 3x3 que se van deslizando por la foto reutilizando los mismos números (pesos compartidos), logrando capturar formas y figuras espaciales usando mucha menos memoria.

**Mapas de Características (Feature Maps):**  
Al observar cómo viaja la imagen a través de las capas de la red, se nota una evolución muy clara y lógica:
* **En las primeras capas (`Convolucion_1`):** La red se enfoca en los detalles más básicos y fáciles de ver, como líneas rectas, bordes de objetos, esquinas y cambios fuertes de color (por ejemplo, el corte entre el cielo y la tierra o los bordes de un edificio). Aquí la imagen que procesa la red todavía se parece muchísimo a la foto original.
* **En las últimas capas (`Convolucion_3`):** La imagen se vuelve pequeña y borrosa para el ojo humano, pero es mucho más inteligente para el modelo. Aquí la red ya no busca simples líneas, sino combinaciones complejas: reconoce la textura de las hojas de un árbol, la superficie rugosa de una montaña de roca o el patrón de ventanas de una ciudad. La red pasa de ver "píxeles sueltos" a entender "qué tipo de paisaje hay en la foto".

**Comportamiento de Aprendizaje (Overfitting):**  
Al no utilizar capas de regularización como Dropout de forma intencional (para cumplir con la regla de la rúbrica y no sufrir la penalización del 20%), se produjo un **sobreajuste**. Al principio el modelo aprendió bien (el error de validación bajó a su mejor número de 0.5717 en la época 4 y la precisión llegó a 81.33%). Sin embargo, después de eso las curvas se separaron: mientras el error de entrenamiento siguió bajando hasta casi cero (0.0646 con 98.02% de acierto), la pérdida de validación empeoró y subió hasta 1.2886. Esto confirma que la red terminó memorizándose las fotos del set de entrenamiento con las que practicó, perdiendo su capacidad óptima para acertar en fotos nuevas.

---

### 3. Auditoría de Aciertos y Análisis de Errores

Revisando la matriz de confusión sobre las 3.000 imágenes de prueba:

**¿Dónde acertó más la red?**
* **Bosques (`forest` - 432 aciertos):** Sigue siendo una de las clases más fáciles para el modelo. El color verde intenso y la textura repetitiva de las hojas y ramas hacen que sea casi imposible confundirlo con otra cosa.
* **Mar (`sea` - 414 aciertos):** El modelo reconoce muy bien las líneas de horizonte limpias y las texturas planas características de esta clase.

**¿Por qué se equivocó el modelo? (Causas Técnicas Explicadas de Forma Simple):**
1. **Calles (`street`) confundidas con Edificios (`buildings`) [126 casos]:**  
   *Causa:* Ambas clases pertenecen a la ciudad y comparten los mismos tonos grises de cemento, pavimento y veredas. Muchas fotos de calles muestran asfalto u ortogonalidad compartida con las fachadas, por lo que la red prioriza la textura de las construcciones laterales y se confunde creyendo que la foto es puramente de un edificio.
2. **Glaciares (`glacier`) vs Montañas (`mountain`) [98 glaciares predichos como montaña y 52 montañas como glaciar]:**  
   *Causa:* Las dos clases tienen exactamente la misma forma física (puntas de cerros, laderas empinadas y cielo de fondo) y los mismos colores (roca gris/café con nieve blanca encima). Si una montaña tiene nieve en la punta o un glaciar tiene rocas asomadas, para la red son prácticamente gemelos.
3. **Glaciares (`glacier`) confundidos con Mar (`sea`) [66 casos]:**  
   *Causa:* Los bloques de hielo azul/blanco continuos, especialmente cuando son capturados desde ángulos abiertos o planos, activan los mismos filtros convolucionales que el reflejo de la luz sobre una superficie acuática lisa.
---

### 4. Propuestas de Mejora

Para solucionar el sobreajuste y llevar este modelo a un nivel superior en el futuro, se proponen:
1. **Agregar Dropout:** Poner una capa de `Dropout(0.4)` antes de la capa densa. Esto "apaga" neuronas al azar mientras entrena, obligando al modelo a aprender patrones generales en vez de memorizarse fotos de memoria.
2. **Aumento de Datos (Data Augmentation):** Aplicar giros horizontales y zoom aleatorio a las fotos mientras entrena (`RandomFlip`, `RandomRotation`), para que el modelo vea el mismo paisaje desde muchos ángulos distintos.
3. **Normalización por Lotes (Batch Normalization):** Poner capas de `BatchNormalization` después de cada convolución para que los datos pasen más estables y el entrenamiento sea más parejo.
4. **Parada Temprana (Early Stopping):** Activar un freno automático para que el entrenamiento se detenga solo en cuanto la pérdida de validación empiece a subir (alrededor de la época 3 o 4), quedándose con los mejores pesos del modelo antes de que empiece a memorizar.
