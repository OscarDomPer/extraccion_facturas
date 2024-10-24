<div align="center">

# Extracción automática de texto estructurado de PDFs

</div>

<br>
<div align="center">

  <img src="https://github.com/OscarDomPer/extraccion_facturas/blob/main/imaxes/imaxe01.png?raw=true">
  
</div>

<br>

## Tecnologías usadas

**Lenguajes:**
![Python](https://img.shields.io/badge/-Python-3776AB?style=flat&logo=python&logoColor=white)

**Librerías:**
![Pandas](https://img.shields.io/badge/-Pandas-150458?style=flat&logo=pandas&logoColor=white)
![PyMuPDF](https://img.shields.io/badge/-PyMuPDF-00599C?style=flat&logo=fitz&logoColor=white)
![Transformers](https://img.shields.io/badge/-Transformers-FF6F00?style=flat&logo=huggingface&logoColor=white)
![Torch](https://img.shields.io/badge/-Torch-EE4C2C?style=flat&logo=pytorch&logoColor=white)
![BERT](https://img.shields.io/badge/-BERT-181717?style=flat&logo=bert&logoColor=white)

**Herramientas:**
![Hugging Face](https://img.shields.io/badge/-Hugging%20Face-FFD700?style=flat&logo=huggingface&logoColor=white)

## Introducción

Este proyecto consiste en extraer algunos campos determinados de información de facturas eléctricas ficiticias. Estas
facturas se presentan en distintos formatos, todas ellas en archivos PDF, pero con un orden y disposición diferente de los
campos. Sin embargo, la información de las facturas, aun con variaciones, es esencialmente la misma, y algunos de estos
campos comunes deben ser obtenidos de ellas.
Para ello se hará fine-tuning de un modelo BERT y se evaluarán sus resultados con un conjunto de facturas que el modelo no habrá visto.

<br>
<br>


****
## BERT


BERT (Bidirectional Encoder Representation from Transformers) es un modelo desarrollado por Google en 2018, actualmente está integrado en muchos productos de Google (Búsquedas, Translate, Gmail…).BERT tiene capacidad para captar el contexto bidireccional en una oración, lo que significa que puede entender el significado de una palabra en relación con las palabras que la rodean.  Esta competencia en la comprensión contextual lo hace especialmente adecuado para la tarea al que lo vamos a destinar.

Inicialmente BERT se pre-entrenó en inglés, posteriormente se desarrolló un modelo multilingüe. Y al ser ‘open source’ se ha usado su arquitectura para pre-entrenarlo con muchas lenguas. El modelo elegido es: 'dccuchile/bert-base-spanish-wwm-cased' conocido coloquialmente como BETO.

<div align="center">
  
<img src="https://github.com/OscarDomPer/extraccion_facturas/blob/main/imaxes/imaxe_1.png?raw=true">

  
</div>


<br>
El proceso de entrenamiento de BERT tiene dos etapas, el pre-entrenamiento, del que ya hemos hablado donde se entrena con una gran cantidad de datos no etiquetados y fine-tuning, donde se añaden capas nuevas según la tarea a realizar y se entrena el modelo con datos etiquetados. En este caso BERT se va a usar para token classification que consiste en asignar etiquetas a cada token en una secuencia de texto.  

Dentro de la clasificación de tokens hay varias tareas según la naturaleza de las etiquetas.
La usada en este proyecto y probablemente la más común es NER (Named Entity Recognition) que consiste en identificar y clasificar entidades en categorías predefinidas. Hay otras tareas como POS (Part-of-speech tagging) que clasifica los tokens gramaticalmente.


<div align="center">
  
<img src="https://github.com/OscarDomPer/extraccion_facturas/blob/main/imaxes/imaxe12.png?raw=true">

  
</div>
  <br>
  
****
## Creación del dataset y de las etiquetas NER

Para la extracción del texto de las facturas se ha utilizado la la librería PyMuPDF, que destaca por la buena calidad de su documentación. El texto extraído se guardó en carpetas individuales para posteriormente crear un dataframe de Pandas con las siguientes columnas: ‘id’ que es el nombre de las facturas, ‘texto’ el texto de la factura convertido en en lista de palabras, ‘ner_tags’ que de momento está vacía y ‘json’ con el json correspondiente a cada factura.

Antes de proceder a la creación de etiquetas se eliminaron las factura en las que había alguna coincidencia entre el nombre de la localidad y la provincia, tanto para cliente como para comercializadora, ya que eran difíciles de etiquetar, podían confundir al modelo y no eran muchas.


<div align="center">
  
<img src="https://github.com/OscarDomPer/extraccion_facturas/blob/main/imaxes/imaxe_2.png?raw=true">

  
</div>

<div align="center">
  
<img src="https://github.com/OscarDomPer/extraccion_facturas/blob/main/imaxes/imaxe_4.png?raw=true">

  
</div>
  <br>
<br>
Para saber si las etiquetas se han creado correctamente se recorre el camino inverso: Se buscan las etiquetas en la columna ‘NER_tags’ y a partir de sus índices se extrae la información de la columna texto.

Los resultados se almacenan en un diccionario con el mismo aspecto que el json original.

Son necesarios una serie de procesamientos, como por ejemplo, quedarnos una sola secuencia (ya que un mismo campo puede aparecer varias veces en la factura) o poner todas las fechas en formato "%d.%m.%Y".

Una vez conseguido se compara con el json original para evaluar el éxito de la tarea.



<div align="center">
  
<img src="https://github.com/OscarDomPer/extraccion_facturas/blob/main/imaxes/imaxe_5.png?raw=true">

  
</div>
  <br>

****
## Entrenamiento del modelo.
Las facturas proporcionadas para el reto se dividen en cuatro tipos.
Dado que el objetivo último del reto es conseguir un sistema que generalice bien, las facturas tipo 1 son reservadas como conjunto de prueba. Mientras que el resto se usan para entrenar el modelo. Los resultados para el conjunto validación (20% de los tres tipos restantes elegidos aleatoriamente), son buenos. Pero los del conjunto de prueba (las facturas que el modelo nunca ha visto) son mediocres.

Para comprender mejor las causas de este pobre desempeño se repitió el proceso para cada uno de los tipos de facturas. Siendo los resultados igualmente mediocres para todos los tipos excepto el tipo 3. Algo que llama la atención puesto que el tipo 3 y el 2 son muy parecidos entre sí. excepto por la distribución del texto.


<div align="center">
  
<img src="https://github.com/OscarDomPer/extraccion_facturas/blob/main/imaxes/imaxe_6.png?raw=true">

  
</div>

Las facturas proporcionadas para el reto se dividen en cuatro tipos.
Dado que el objetivo último del reto es conseguir un sistema que generalice bien, las facturas tipo 1 son reservadas como conjunto de prueba. Mientras que el resto se usan para entrenar el modelo. Los resultados para el conjunto validación (20% de los tres tipos restantes elegidos aleatoriamente), son buenos. Pero los del conjunto de prueba (las facturas que el modelo nunca ha visto) son mediocres.

Para comprender mejor las causas de este pobre desempeño se repitió el proceso para cada uno de los tipos de facturas. Siendo los resultados igualmente mediocres para todos los tipos excepto el tipo 3. Algo que llama la atención puesto que el tipo 3 y el 2 son muy parecidos entre sí. excepto por la distribución del texto.

<div align="center">
  
<img src="https://github.com/OscarDomPer/extraccion_facturas/blob/main/imaxes/imaxe_7.png?raw=true">

  
</div>
Las métricas de los conjuntos de pruebas de los distintos tipos de facturas sugerían que el modelo estaba siendo demasiado rígido con la estructura de las facturas. 

La manera de probar esta hipótesis y de paso mejorar el rendimiento del modelo fue realizar un aumento de datos.

El texto de cada factura se  separó por párrafos, y usando la librería Random se agrupan los párrafos en grupos de 1 a 5 párrafos, para posteriormente barajarlos  y crear una copia de la factura reordenada, doblando el tamaño del dataset.

Los resultados del conjunto de prueba después de entrenar el modelo con el dataset aumentado son mucho mejores.


<div align="center">
  
<img src="https://github.com/OscarDomPer/extraccion_facturas/blob/main/imaxes/imaxe_8.png?raw=true">

  
</div>

****
## Creación del json.
Una vez comprobado que el aumento de datos mejoraba la regularización del modelo. Se vuelve a entrenar esta vez con los cuatro tipos de facturas.
Si bien el modelo  ya ha realizado las predicciones. En el reto se pide como resultado un json con la misma estructura que los proporcionados para el entrenamiento.

Se usa la misma lógica que para comprobar las etiquetas, solo que en esta ocasión la información extraída está bajo los efectos de la tokenización, debiendo ser reconstruida. Los resultados son buenos, pero no tan buenos como podrían sugerir las métricas de evaluación. El modelo muestra dificultades para clasificar correctamente tokens de naturaleza numérica.



<div align="center">
  
<img src="https://github.com/OscarDomPer/extraccion_facturas/blob/main/imaxes/imaxe_9.png?raw=true">

  
</div>

En un intento de mejorar el desempeño de BERT con valores numéricos. Se pasó de etiquetar únicamente el valor numérico a etiquetarlo junto con el símbolo del Euro o las abreviaturas de Kilovatio o Kilovatio/hora. Mejorando algo los resultados. Otra aproximación fue entrenar el modelo solo con valores numéricos, pero esta no dio los resultados esperados.

Se complementan las predicciones de BERT con expresiones regulares. Se pasan estas reglas por todo el dataset y los resultados se almacenan en un nueva columna llamada ‘reglas’, creando un segundo diccionario con las mismas claves que se se usa para actualizar el resultante de las predicciones sólo si, el valor para ese campo está vacío.
<div align="center">
  
<img src="https://github.com/OscarDomPer/extraccion_facturas/blob/main/imaxes/imaxe_10.png?raw=true">

  
</div>








