# 📌Detección de Reseñas Falsas 5 Estrellas Utilizando Modelos de Clasificación No Supervisados

----
## 🧩 Resumen del Proyecto
Este proyecto tiene como objetivo identificar patrones y comportamientos asociados a reseñas falsas en la plataforma de comercio electrónico Mercado Libre. Para ello, se aplicó una combinación de técnicas de procesamiento de texto, extracción de características, análisis de correlación, reducción de dimensionalidad (PCA) y algoritmos de agrupamiento como K-means y clustering jerárquico. Los datos fueron obtenidos usando la API de Mercado Libre, respetando sus restricciones de extracción.

Aunque los métodos no supervisados no lograron una separación perfecta, se observaron agrupaciones con ciertas características comunes (e.g., reseñas cortas con emojis y lenguaje positivo vs. reseñas extensas y detalladas). Estos resultados permiten construir criterios preliminares para avanzar hacia un enfoque supervisado.

---
## 🎯 Motivación
Las reseñas falsas afectan la confianza de los usuarios y distorsionan el posicionamiento de los productos. Los vendedores las utilizan para obtener ventaja competitiva, entregando productos gratuitos o reembolsos a cambio de reseñas positivas. Esta práctica representa un problema ético, económico y tecnológico que requiere herramientas automatizadas de detección para mitigar su impacto.

---

## 🗃️ Descripción del Problema y los Datos
### ⚠️ Problemática
Las plataformas como Mercado Libre no penalizan de manera efectiva estas prácticas, lo que motiva su proliferación. Los usuarios se ven expuestos a una percepción errónea del producto. Por tanto, es crucial construir una métrica confiable que permita detectar la manipulación en las reseñas.

### 📊 Descripción de los Datos
Se extrajeron más de 226,000 productos y 172,000 reseñas de 5 estrellas de la categoría "Adornos y Decoración del Hogar". El dataset incluye variables como número de palabras, likes, dislikes, fecha de compra, número de imágenes, y presencia de palabras prohibidas.

---

## ⚙️ Metodología Aplicada
### 🧹 Procesamiento inicial
* Eliminación de variables innecesarias (reacciones, atributos, variacion_atributos).
* Eliminación de reseñas sin contenido.
* Eliminación de reseñas duplicadas.
* Transformación de las fechas de compra y de creación de la reseña.
* Creación de la Variable de número de caracteres que contiene una reseña.
* Creación de la Variable de número de días que un cliente tardó en hacer la reseña desde que compró el producto.
* Selecciona variables de interés.

### 🔍 Análisis Exploratorio
1. Número de reseñas por valoración
<img width="888" height="557" alt="image" src="https://github.com/user-attachments/assets/38ed89a8-7380-400a-9c42-4cf1a7d0bce8" /><p align="center"><em>Número de reseñas por valoración</em></p>
El 77% de las reseñas de los productos analizados tienen una valoración de 5 estrellas. 

2. Análisis de las variables más relevantes
* Número de palabras por valoración
<img width="810" height="538" alt="image" src="https://github.com/user-attachments/assets/a6cc37e6-99ea-44ed-b448-94cff693aa5e" /><p align="center"><em>Número de palabras por valoración</em></p>
Las reseñas con valoraciones de 1, 2 y 3 estrellas tienden a ser más extensas en comparación con las demás valoraciones, esto puede ser debido a que los usuarios con malas experiencias tienden a expresarse más que usuarios con buenas experiencias.

* Días para crear una reseña
<img width="917" height="609" alt="image" src="https://github.com/user-attachments/assets/7a677f82-9383-4af6-84a0-3340a63fc9ca" /><p align="center"><em>Histograma de días para crear una reseña por valoración</em></p>
El rango de días para crear una reseña oscila entre 0 y 1,000 días. Se puede apreciar una distribución más definida para los productos con una valoración de 3 estrellas, sin embargo parece seguir una tendencia igualitaria para las demás valoraciones.

* Número de caracteres por valoración
<img width="852" height="566" alt="image" src="https://github.com/user-attachments/assets/2cfc1899-b6fb-4e8d-90e7-d23eeaed949d" /><p align="center"><em>Histograma de número de caracteres por valoración</em></p>
El número de caracteres muestra una correlación lineal similar, con reseñas de 1, 2, 3 y 4 estrellas tendiendo a ser más largas que las de 5 estrellas.


3. Procesamiento de Texto

Para el análisis posterior, se seleccionaron únicamente reseñas con una valoración de 5 estrellas. Esto se debe a que se asume que cada valoración requiere un análisis individual, ya que las tendencias de escritura pueden variar para cada una. 
El número total de reseñas de 5 estrellas dentro del dataset es de 172,310. Debido al poder de computación limitado, se tomó una muestra representativa aleatoria de 30,000 reseñas con una valoración de 5 estrellas y se aplicó el siguiente procesamiento:
* Conversión de los documentos a tipo string.
* Eliminación de saltos de línea (\n).
* Aplicación de un autocorrector.
* Conversión a minúsculas.
* Eliminación de stopwords.
* Eliminación de caracteres especiales.
* Aplicación de stemming.


4. Extracción de nuevas variables a partir del texto

Una vez procesados los documentos, se procedió a recopilar nuevas variables con el objetivo de obtener características adicionales de los datos. Las nuevas variables son:
* Número de oraciones por documento.
* Número de errores ortográficos por documento. 
* Número de palabras funcionales (preposiciones, conjunciones, determinantes, adverbios).
* Puntuación de sentimiento por documento.
* Puntuación de diccionario (dic score) por documento.
* Número de adjetivos utilizados.
* Número de sustantivos utilizados.
* Número de verbos utilizados.
* Número de entidades (organizaciones, lugares, fechas).
* Número de palabras únicas.


5. Análisis de correlación 

<img width="882" height="758" alt="image" src="https://github.com/user-attachments/assets/ec769894-b41e-479d-b045-eb411a419264" /><p align="center"><em>Matriz de correlación</em></p>

Las variables calf, sustantivos, palabras_func, verbos, palabras_unicas y num_palabras presentan una correlación lineal superior a 0.80 con otras variables. Por esta razón, se decidió eliminar estas variables para obtener una matriz con la mejor varianza explicada

6. Extracción de Vectores por documento

Con la intención de capturar el contexto y la sintaxis de cada oración, se aplicó el siguiente procedimiento para la obtención de vectores por cada documento:
* Ponderación de palabra con TF-IDF: Esta ponderación mide la frecuencia de una palabra en un conjunto de documentos y le asigna un valor en función de dicha frecuencia. Palabras poco utilizadas tienden a tener un valor más alto a comparación de palabras más utilizadas.
* Extracción de vectores por palabra utilizando el modelo Word2Vec: Esta librería extrae un vector de tamaño “n” para cada palabra, basándose en las palabras que la rodean según el número de ventanas “n” utilizadas. Esto permite agrupar palabras en contextos similares.
* Ponderación de vectores de Word2Vec con TF-IDF por documento: Utilizando la ponderación de TF-IDF y los vectores obtenidos a partir de Word2Vec, se realizó una ponderación por cada palabra utilizada en los documentos, obteniendo así un vector de documento de 100 dimensiones.
* Con los vectores documentos y las características obtenidas a partir del texto se unificaron ambas matrices para obtener una matriz conjunta de 118 características y 30,000 registros.

7. Aplicación de PCA

<img width="946" height="473" alt="image" src="https://github.com/user-attachments/assets/503db5e9-bd11-4be9-aaaf-293985652714" /><p align="center"><em>Varianza Explicada PCA</em></p>

Esta gráfica indica que con 25 componentes principales se puede obtener el 76% de la varianza. Por lo tanto, la matriz para los análisis posteriores tendrá 25 características y 30,000 registros.

8. Aplicación de algoritmos de agrupación.

Posterior a la reducción de dimensionalidad se aplicó una estandarización a los datos debido a las diferentes escalas que se presentan y se trabajaron con los dos siguientes modelos de agrupamiento:
* K-MENAS.
* Clustering Jerárquico.

---

## 📊 Análisis de Resultados

### 🧭 PCA
<img width="754" height="598" alt="image" src="https://github.com/user-attachments/assets/1ae4e24d-ab9e-4106-bb98-ccf15ff659d9" /><p align="center"><em>Gráfico PCA con 0.76% de Varianza Explicada</em></p>

Aunque la matriz haya sido reducida en dimensiones mediante PCA, se puede observar una baja o casi nula separabilidad de los grupos. Esto se debe a que la extracción de características de los textos no ha logrado una separabilidad suficiente para permitir la formación de grupos homogéneos. Esta falta de separabilidad sugiere que las características seleccionadas no capturan adecuadamente las diferencias entre los grupos, lo que dificulta la identificación de patrones claros y la correcta agrupación de las reseñas.

### 📌 K-Means

<img width="946" height="473" alt="image" src="https://github.com/user-attachments/assets/1eca71f1-ba42-4eca-8b0d-9fdf69d7b79e" /><p align="center"><em>Índices Elbow y Silhouette</em></p>

Si se aplica la metodología de agrupación K-MEANS utilizando los índices de "codo" y Silhouette, obtenemos valores bajos en su índice indicando una casi nula separabilidad de los datos. Esto se debe a que el método K-MEANS agrupa los puntos basándose en el "n" número de centroides elegidos y asigna los puntos al centroide más cercano

Es importante destacar que estamos buscando agrupaciones de datos dentro de una misma categoría conforme a sus características. Es razonable pensar que existirá una alta similitud entre estas características, lo que dificulta la diferenciación si no se realiza una extracción de características adecuada.

Además, no buscamos únicamente dos grupos que representen reseñas falsas o verdaderas, sino varios subgrupos que puedan ser clasificados dentro de estas dos categorías. Esta complejidad adicional hace aún más crucial la correcta extracción de características para identificar y separar adecuadamente los diferentes subgrupos.

Con la misma premisa de que en el conjunto de datos esperamos obtener más de 2 agrupaciones diferentes y observamos que el índice de Silhouette al llegar a 14 hay una bajada significativa y se asignan estos número de grupos al modelo de K-MEANS. Al realizar la asignación de etiquetas obtenemos el siguiente gráfico:

<img width="940" height="483" alt="image" src="https://github.com/user-attachments/assets/19cb6e46-504c-446f-90d1-a95a00c328b3" /><p align="center"><em>Gráfico PCA con agrupaciones realizadas por K-Means</em></p>

Esto nos permite visualizar que el algoritmo de K-Means realizó una agrupación en base al número de centroides elegidos. Si visualizamos el tipo de reseñas que se obtuvo con el número de cluster elegidos podemos visualizar lo siguiente:

<img width="946" height="541" alt="image" src="https://github.com/user-attachments/assets/ea4c6508-fd96-4742-887d-51da95002039" /><p align="center"><em>Agrupaciones de reseñas K-Means</em></p>

Aunque las clases se encuentran solapadas el método de K-MEANS logró una agrupación en los datos conforme a sus características presentadas. Por ejemplo el el cluster número 7 podemos observar que los documentos son cortos, utilizan un adjetivo y la utilización de “emojis”, por otro lado el el cluster 4 podemos ver que son textos más largos, que utilizan una correcta utilización de caracteres especiales, habla sobre las características del producto y mencionan detalles más específicos.

### 🌲 Clustering Jerárquico
<img width="660" height="523" alt="image" src="https://github.com/user-attachments/assets/c9815a3b-a8fe-4936-b544-2c02334082cc" /><p align="center"><em>Silhouette Score por diferentes números de clusters</em></p>

Al visualizar el índice de Silhouette y, al igual que con K-means, podemos visualizar que el método no logró una separación correcta. Aunque los índices de separación son más altos que los obtenidos con K-means.
Mayor estructura observada.

Con la misma premisa de que en el conjunto de datos esperamos obtener más de 2 agrupaciones diferentes y deducimos que el número adecuado de clusters es 5, ya que en ese punto se observa una bajada considerable del índice, obtenemos el siguiente gráfico con dos componentes principales. Mejores resultados en distribución de clústeres, especialmente en los extremos.

<img width="760" height="397" alt="image" src="https://github.com/user-attachments/assets/99829090-274d-4009-8c89-2f4206607a56" /><p align="center"><em>Gráfico PCA con agrupaciones realizadas por Clustering Jerárquico</em></p>

En este gráfico, observamos que una gran mayoría de datos se concentran en el centro, mientras que las demás agrupaciones comienzan a formarse a partir de los datos que se encuentran alejados de este centro.

Al observar algunas de las reseñas conforme al cluster obtenido, notamos que los grupos fuera del centro logran tener características muy marcadas. Sin embargo, el método realiza un pésimo trabajo al clasificar las clases centrales.

<img width="752" height="431" alt="image" src="https://github.com/user-attachments/assets/615fef6f-204c-4d1a-9a37-6202bab8e3c5" /><p align="center"><em>Agrupaciones de reseñas Clustering Jerárquico</em></p>

Al visualizar el dendrograma obtenido por esta metodología, podemos deducir que es posible obtener un número mayor de clusters. 

<img width="940" height="527" alt="image" src="https://github.com/user-attachments/assets/c8b5ec85-280e-4661-87ba-4ac34a41f166" /><p align="center"><em>DendogramaClustering Jerárquico</em></p>

Al realizar una comparación de ambos modelos pareciera ser que clustering jerárquico hace un mejor trabajo que k-means, esto puede ser debido a la metodología que utiliza el modelo en empieza con cada data siendo un cluster e iterativamente va uniendo con otros datos.

Aunque los agrupamientos no lograron salir definidos claramente, se pueden extraer las características presentadas y realizar clasificaciones manuales a cada uno de los grupos para posteriormente trabajar con modelos supervisados.

---

## ✅ Conclusiones

A través del uso de diversas técnicas de procesamiento de texto y algoritmos de agrupamiento, se lograron identificar patrones importantes en las reseñas. Sin embargo, para obtener resultados más precisos y efectivos, es necesario realizar los siguientes pasos:

1.	**Definición Clara de Criterios:**** Con los clusters obtenidos a partir de las metodologías utilizadas, podemos empezar a definir criterios claros para identificar qué constituye una reseña falsa y una verdadera. Estos criterios permitirán analizar las agrupaciones y determinar cuáles clases pueden ser encasilladas dentro de estos grupos. Una vez definidos, estos criterios facilitarán la transición a un enfoque supervisado, donde se podrán etiquetar manualmente las reseñas como falsas o verdaderas para entrenar modelos de clasificación.
2.	**Enfoque Supervisado:** El análisis de texto generalmente se maneja mejor con un enfoque supervisado debido a la complejidad de extraer características significativas del texto. Con este enfoque, se pueden entrenar modelos de machine learning para aprender a identificar los contextos y características distintivas de las reseñas falsas y verdaderas. Estos modelos supervisados permitirán una clasificación más precisa de nuevas entradas.
3.	**Profundización en el Procesamiento de Texto:** Es crucial explorar y aplicar técnicas más avanzadas de procesamiento de texto que consideren el contexto, la sintaxis y la semántica de las reseñas. Métodos como el análisis de sentimientos más detallado y la identificación de entidades, pueden proporcionar una comprensión más rica del contenido textual. Estas técnicas avanzadas permitirían una mejor agrupación de las clases y una mayor eficiencia en la clasificación.
4.	**Reducción de Sesgos:** El análisis de texto está sujeto a varios posibles sesgos, que pueden surgir en diferentes etapas del procesamiento. Es esencial identificar y mitigar estos sesgos para garantizar la objetividad y precisión del análisis. Esto incluye revisar y ajustar las técnicas de preprocesamiento, como la tokenización, la eliminación de stopwords y el stemming. Enfocar los esfuerzos en minimizar los sesgos ayudará a obtener resultados más fiables y representativos.

En resumen, aunque se han logrado avances significativos en la identificación de patrones mediante técnicas de procesamiento de texto y agrupamiento, es evidente que se requiere un enfoque más robusto y multifacético para mejorar la precisión y efectividad de la clasificación de reseñas. Al seguir estos pasos, se espera desarrollar un sistema más confiable y preciso para distinguir entre reseñas falsas y verdaderas, mejorando así la transparencia y la confianza en las evaluaciones de productos en plataformas de comercio electrónico.

---

📚 Referencias

* Martínez, L. (2018). Análisis de datos con Python.
* Flach, P. (2012). Máquinas que aprenden.
* Camacho Collados, J., & Pilehvar, M. T. (2020). Embeddings in NLP.
* Jolliffe, I. T. (2002). Principal Component Analysis.


