<div align="center">

# Extracción automática de texto estructurado de PDFs

</div>

<br>
<div align="center">

  <img src="https://github.com/OscarDomPer/extraccion_facturas/blob/main/imaxes/imaxe0.png?raw=true">
  
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

<br>
<br>

## Introducción
<br>

Este proyecto consiste en extraer algunos campos determinados de información de facturas eléctricas ficiticias. Estas
facturas se presentan en distintos formatos, todas ellas en archivos PDF, pero con un orden y disposición diferente de los
campos. Sin embargo, la información de las facturas, aun con variaciones, es esencialmente la misma, y algunos de estos
campos comunes deben ser obtenidos de ellas.
Para ello se hará fine-tuning de un modelo BERT y se evaluarán sus resultados con un conjunto de facturas que el modelo no habrá visto.

<br>
<br>
<br>

****
## BERT

<br>
BERT (Bidirectional Encoder Representation from Transformers) es un modelo desarrollado por Google en 2018, actualmente está integrado en muchos productos de Google (Búsquedas, Translate, Gmail…).BERT tiene capacidad para captar el contexto bidireccional en una oración, lo que significa que puede entender el significado de una palabra en relación con las palabras que la rodean.  Esta competencia en la comprensión contextual lo hace especialmente adecuado para la tarea al que lo vamos a destinar.

Inicialmente BERT se pre-entrenó en inglés, posteriormente se desarrolló un modelo multilingüe. Y al ser ‘open source’ se ha usado su arquitectura para pre-entrenarlo con muchas lenguas. El modelo elegido es: 'dccuchile/bert-base-spanish-wwm-cased' conocido coloquialmente como BETO![image](https://github.com/user-attachments/assets/8bc6b7dd-13f3-485e-ac10-2a22b09212f4)

<div align="center">
  
<img src="https://github.com/OscarDomPer/extraccion_facturas/blob/main/imaxes/imaxe_1.png?raw=true">

  
</div>
  <br>

<br>
El proceso de entrenamiento de BERT tiene dos etapas, el pre-entrenamiento, del que ya hemos hablado donde se entrena con una gran cantidad de datos no etiquetados y fine-tuning, donde se añaden capas nuevas según la tarea a realizar y se entrena el modelo con datos etiquetados. En este caso BERT se va a usar para token classification que consiste en asignar etiquetas a cada token en una secuencia de texto.  

Dentro de la clasificación de tokens hay varias tareas según la naturaleza de las etiquetas.
La usada en este proyecto y probablemente la más común es NER (Named Entity Recognition) que consiste en identificar y clasificar entidades en categorías predefinidas. Hay otras tareas como POS (Part-of-speech tagging) que clasifica los tokens gramaticalmente.


<div align="center">
  
<img src="https://github.com/OscarDomPer/extraccion_facturas/blob/main/imaxes/imaxe_2.png?raw=true">

  
</div>
  <br>















