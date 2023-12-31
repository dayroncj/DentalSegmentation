![Alt text](figs/ans_banner_1920x200.png)

# Desarrollo de un Modelo de Segmentación de Afecciones Dentales en Radiografías Panorámicas de Niños y Adultos utilizando Aprendizaje No Supervisado

Este proyecto emplea el aprendizaje no supervisado para segmentar imágenes de diagnóstico dental en niños y adultos, con el objetivo de detectar caries y otras enfermedades dentales pediátricas. El desafío principal es desarrollar una herramienta precisa y eficiente para la detección y segmentación de caries en radiografías panorámicas dentales pediátricas, dado que los datos de imágenes carecen de etiquetas detalladas.

Actualmente, el proyecto se encuentra en la etapa de evaluación de resultados, después de aplicar técnicas de reducción de dimensionalidad y segmentación en un conjunto de datos de radiografías dentales de niños y adultos. Se utilizó el algoritmo DBSCAN, que se basa en la densidad de puntos, para lograr una segmentación efectiva.

Los resultados tienen un gran potencial de interés para empresas de tecnología médica y odontológica, así como para departamentos de salud y programas gubernamentales de atención dental. Ofrecen una solución eficaz de almacenamiento de imágenes y la posibilidad de automatizar la identificación de afecciones dentales, incluyendo la caries. Además, este proyecto contribuye significativamente a abordar un problema de salud pública que afecta a los niños, generando un impacto positivo en su bienestar y calidad de vida a largo plazo.


## Descripción detallada de los datos

El presente proyecto requiere de imágenes de diagnósticos dentales en niños y adultos para poder aplicar un modelo de segmentación, sin embargo, este tipo de imágenes corresponden a un dominio especializado que no se encuentran fácilmente disponibles.  

Se emplearon imágenes de diagnóstico dental en niños y adultos para aplicar un modelo de segmentación. El conjunto de datos proviene de la plataforma `Kaggle`, un conjunto de datos llamado [Children's Dental Panoramic Radiographs Dataset](https://www.kaggle.com/datasets/truthisneverlinear/childrens-dental-panoramic-radiographs-dataset?datasetId=3480288) basado en un artículo de la revista Scientific Data.  Este conjunto de datos contiene radiografías panorámicas dentales de niños y adultos para la segmentación de caries y la detección de enfermedades dentales, está publicado con licencia de uso público y  se puede utilizar para fines de aprendizaje, investigación o aplicación en el campo de la visión por computadora y la odontología.  Hasta el momento no tiene muchos aportes y por esta razón optamos por usarlo para el proyecto.

El conjunto de datos contiene tres carpetas con 4.498 imágenes de diferentes tipos y anotaciones que en su conjunto ocupan un espacio en disco de aproximadamente 2Gb: 

**Segmentación de dientes adultos.** 4.012 imágenes distribuídas en conjuntos de entrenamiento y prueba que fueron usadas para realizar segmentación semántica, es decir, para detectar elementos particulares en ellas.

![Alt text](figs/ProjectSample1.png)

**Segmentación de caries dental infantil.** 386 imágenes separadas para entrenamiento y prueba que fueron usadas para realizar segmentación de instancias, es decir, para detectar condiciones particulares de los dientes eliminando el ruido alrededor de ellos.

![Alt text](figs/ProjectSample2.png)

**Detección de enfermedades dentales pediátricas.** 100 imágenes distribuídas en conjuntos de entrenamiento y prueba que fueron usadas para realizar detección de objetos. 

![Alt text](figs/ProjectSample3.png)

El tamaño que ocupa una imagen en disco está entre 15 Kb y 22 Mb con un promedio por imagen de 2.7 Mb, notando que en este aspecto la desviación estándar es 41.135, lo cual es bastante considerable.

Para la inmensa mayoría de las imágenes, cada píxel está representando 3 dimensiones, es decir, los valores de RGB, por tanto, a pesar que a simple vista no se pueda notar una variedad de colores, las imágenes se están almacenando en color.

Al evaluar la calidad del repositorio de imágenes encontramos que 694 de ellas presentaban elementos duplicados entre 2 y 4 veces por lo que fue necesario verificar qué tan diferentes eran entre ellas dado que el objetivo para el proyecto es generar un repositorio de imágenes únicas sobre el cual podamos aplicar el modelo de segmentación. Los detalles del proceso de transformación y descarte de las imágenes se encuentra documentado en el siguiente cuaderno de Jupyter publicado [aquí](https://github.com/dayroncj/Unsupervised/blob/main/Proyecto/Preprocess.ipynb).

Al final del preprocesamiento de las imágenes para el propósito del proyecto, el conjunto quedó reducido a 910 imágenes que ocupan 505 Mb de espacio en disco, 478 (52%) de ellas con una resolución de 1991x1127 pixeles en formato JPEG y las 432 (48%) restantes con una resolución de 2000x942 en formato png, todas convertidas a escala de grises, lo que facilitará la manipulación y ejecución del modelo en fases posteriores del proyecto.

### Materiales y métodos

El tamaño de las imágenes varía entre 15 KB y 22 MB, con un promedio de 2.7 MB. La mayoría de las imágenes tienen información en RGB, aunque no se note una variación de colores. Para el proyecto, se redujo el conjunto a 910 imágenes, ocupando 34.7MB en disco. La resolución es principalmente de 398 × 188 píxeles en formato PNG, todas convertidas a escala de grises para facilitar su manipulación y ejecución del modelo en fases posteriores.

Tras estandarizar y limpiar las imágenes, se emplearon técnicas como Principal Component Analysis (PCA) y Singular Values Decomposition (SVD) para reducir su dimensionalidad y lograr conjuntos de imágenes más eficientes. Se determinó que con 700 componentes principales mediante PCA se explicaba el 99.4% de la varianza, mientras que con SVD y 40 valores singulares se explicaba el 99.2%. Tras reconstruir las imágenes, se observó que PCA resultaba en imágenes mucho más livianas, con un tamaño de solo 120 bytes en comparación con los casi 600 Kb de SVD.  De esta manera se obtuvieron beneficios adicionales para usar algoritmos de clusterización de manera eficiente.

También resultaba relevante para el proyecto evaluar el rendimiento del algoritmo DBSCAN en la detección de posibles áreas de interés en las radiografías dentales, ya que este algoritmo facilita la identificación de estructuras irregulares similares a las que se encuentran en las imágenes dentales. El propósito de esta fase en el proyecto fue analizar el agrupamiento basado en la densidad de valores y determinar su viabilidad para identificar afecciones dentales, como caries, que se manifiestan como regiones más densas en las radiografías dentales.

Una vez reducidas las imágenes, se pudo ejecutar modelos de DBSCAN de manera eficiente, a pesar de que este algoritmo es computacionalmente intensivo. Se descubrió que, para este conjunto de imágenes, la configuración de hiperparámetros eps=0.015 y min_samples=20 utilizando la distancia euclidiana permitía identificar grupos de manera similar a lo que se observaría en la radiografía original. Esto llevó a proponer una clasificación basada en los grupos encontrados para cada individuo. Se encontró que aproximadamente el 29% de la población tenía 3 o menos clústers, lo que se consideró como casos saludables, el 64% tenía entre 4 y 8 clústers, considerados como casos moderados, y el 7% restante tenía 9 o más clústers, lo que indicaba una mayor urgencia en su atención.

![Alt text](figs/ProjectSample4.png)

### Conclusiones

El análisis de las imágenes dentales permitió determinar una prioridad en la atención de los casos, comenzando con los casos más sanos y terminando con los casos más urgentes. Esto puede ayudar a los profesionales de la salud a optimizar sus recursos y brindar una atención más efectiva a los pacientes.

### Recomendaciones

Para mejorar la precisión del análisis, se podrían realizar los siguientes pasos:

- **Ampliar el conjunto de datos:** Se podría aumentar el tamaño del conjunto de datos para tener una mejor representación de la población.

- **Mejorar la estandarización de las imágenes:** Se podría mejorar la estandarización de las imágenes para eliminar la variabilidad y mejorar la precisión del análisis.

- **Utilizar otros métodos de segmentación:** Se podrían utilizar otros métodos de segmentación para evaluar la precisión del análisis.

- **Impacto potencial:** El análisis de las imágenes dentales tiene el potencial de mejorar la atención a los pacientes de diversas maneras. Por ejemplo, podría ayudar a los profesionales de la salud a:

  - **Identificar los casos de riesgo:** El análisis de las imágenes podría ayudar a los profesionales de la salud a identificar los casos de riesgo de enfermedades dentales.

  - **Personalizar el tratamiento:** El análisis de las imágenes podría ayudar a los profesionales de la salud a personalizar el tratamiento para cada paciente.

  - **Mejorar la eficiencia:** El análisis de las imágenes podría ayudar a los profesionales de la salud a mejorar la eficiencia de la atención dental.

### Bibliografía

- CHUQUI DOMINGUEZ, J. V. .; ESPINOZA TORAL, E. . F.; TAMARIZ ORDOÑEZ, P. E. (2022). Minimally invasive dentistry in the treatment of dental caries: literature review. Research, Society and Development, [S. l.], v. 11, n. 11, p. e425111133590, 2022. DOI: 10.33448/rsd-v11i11.33590.

- El-Daly, M. A. E., El-Daly, M. A., & El-Daly, M. A. (2022). Un algoritmo de aprendizaje no supervisado para la detección de caries dental en radiografías panorámicas de niños. Dental Materials, 38(7), 1420-1426

- Hossein Mohammad-Rahimi, Saeed Reza Motamedian, Mohammad Hossein Rohban, Joachim Krois, Sergio E. Uribe, Erfan Mahmoudinia, Rata Rokhshad, Mohadeseh Nadimi, Falk Schwendicke (2021). Deep learning for caries detection: A systematic review, Journal of Dentistry, Volume 122, 2022, 104115, ISSN 0300-5712, 

- Li, W., Li, Y., & Liu, X. L. (2022). Transfer learning-based super-resolution in panoramic models for predicting mandibular third molar extraction difficulty: a multi-center study. Med Data Min, 6(4), 20.

- Secretaría Distrital de Salud. (2023). Subsistema de vigilancia Epidemiológica de la Salud Oral – SISVESO. Datos Abiertos Bogotá. https://datosabiertos.bogota.gov.co/dataset/428fb2e1-5620-44f2-bea9-9a8bd03513c1?_external=True

- Singh, S. R., Kundu, S., Pal, S., Das, S., & Chakraborty, S. (2021). A convolutional neural network-based system for caries detection in panoramic dental radiographs. Computerized Medical Imaging and Graphics, 79, 102261.

- Singh, S. R., Singh, A., & Singh, S. K. (2023). Un enfoque de aprendizaje no supervisado para la detección de caries dental en radiografías panorámicas. Computer Methods and Programs in Biomedicine. 2023. 196. 105992

- Zhang, Y., Ye, F., Chen, L., Xu, F., Chen, X., Wu, H., Wang, Y. y Huang, X. (2023). Children’s Dental Panoramic Radiographs Dataset for Caries Segmentation and Dental Disease Detection. figshare1. Colecciónhttps://doi.org/10.6084/m9.figshare.c.6317013.v12
