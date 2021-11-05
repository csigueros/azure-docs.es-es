---
title: 'Tutorial: Translator con Cognitive Services'
description: Aprenda a usar el traductor en Azure Synapse Analytics.
services: synapse-analytics
ms.service: synapse-analytics
ms.subservice: machine-learning
ms.topic: tutorial
ms.reviewer: ''
ms.date: 11/02/2021
author: ruixinxu
ms.author: ruxu
ms.custom: ignite-fall-2021
ms.openlocfilehash: e0ae2aea56e7bfa255daba30c99b49bc66b5b4b5
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/02/2021
ms.locfileid: "131093443"
---
# <a name="tutorial-translator-with-cognitive-service"></a>Tutorial: Translator con Cognitive Services

[Translator](../../cognitive-services/Translator/index.yml) es una instancia de [Azure Cognitive Service](../../cognitive-services/index.yml) que le permite realizar traducciones de idiomas y otras operaciones relacionadas con los idiomas. En este tutorial, aprenderá a usar [Translator](../../cognitive-services/Translator/index.yml) para crear soluciones inteligentes y de varios idiomas en Azure Synapse Analytics.

En este tutorial se muestra cómo usar Translator con [MMLSpark](https://github.com/Azure/mmlspark) para:

> [!div class="checklist"]
> - Traducir texto
> - Transliteración de texto
> - Detectar idioma
> - Dividir la oración
> - Búsqueda en diccionario
> - Ejemplo de diccionario

Si no tiene una suscripción a Azure, [cree una cuenta gratuita antes de empezar](https://azure.microsoft.com/free/).


## <a name="prerequisites"></a>Requisitos previos

- Necesitará un [área de trabajo de Azure Synapse Analytics](../get-started-create-workspace.md) con una cuenta de almacenamiento de Azure Data Lake Storage Gen2 que esté configurada como almacenamiento predeterminado. Asegúrese de que es el *colaborador de datos de Storage Blob* en el sistema de archivos de Data Lake Storage Gen2 con el que trabaja.
- Grupo de Spark en el área de trabajo de Azure Synapse Analytics. Para más información, consulte el artículo sobre [creación de un grupo de Spark en Azure Synapse](../quickstart-create-sql-pool-studio.md).
- Haber completado los pasos de configuración previos que se detallan en este tutorial sobre la [configuración de Cognitive Services en Azure Synapse](tutorial-configure-cognitive-services-synapse.md).

## <a name="get-started"></a>Introducción
Abra Synapse Studio y cree un cuaderno. Para empezar, importe [MMLSpark](https://github.com/Azure/mmlspark). 

```python
import mmlspark
from mmlspark.cognitive import *
from notebookutils import mssparkutils
from pyspark.sql.functions import col, flatten
```

## <a name="configure-translator"></a>Configuración de Translator

Use el traductor vinculado que configuró en los [pasos de configuración previa](tutorial-configure-cognitive-services-synapse.md). 

```python
cognitive_service_name = "<Your linked service for translator>"
```

## <a name="translate-text"></a>Traducción de texto
La operación básica del servicio Translator es traducir texto. 

```python
df = spark.createDataFrame([
  (["Hello, what is your name?", "Bye"],)
], ["text",])

translate = (Translate()
    .setLinkedService(cognitive_service_name)
    .setTextCol("text")
    .setToLanguage(["zh-Hans", "fr"])
    .setOutputCol("translation")
    .setConcurrency(5))

display(translate
      .transform(df)
      .withColumn("translation", flatten(col("translation.translations")))
      .withColumn("translation", col("translation.text"))
      .select("translation"))
```

### <a name="expected-results"></a>Resultados esperados

```json
["你好，你叫什么名字？","Bonjour, quel est votre nom?","再见","Au revoir"]
```

## <a name="transliterate-text"></a>Transliteración de texto

La transliteración es el proceso de convertir una palabra o una frase del guión (alfabeto) de un idioma al de otro en función de su similitud fonética.

```python
transliterateDf =  spark.createDataFrame([
  (["こんにちは", "さようなら"],)
], ["text",])

transliterate = (Transliterate()
    .setLinkedService(cognitive_service_name)
    .setLanguage("ja")
    .setFromScript("Jpan")
    .setToScript("Latn")
    .setTextCol("text")
    .setOutputCol("result"))

display(transliterate
    .transform(transliterateDf)
    .withColumn("text", col("result.text"))
    .withColumn("script", col("result.script"))
    .select("text", "script"))
```

### <a name="expected-results"></a>Resultados esperados

|text|script|
|--|--|
|"["Kon'nichiwa","sayonara"]"|"["Latn","Latn"]"|

## <a name="detect-language"></a>Detección de idioma
Si sabe que va a necesitar una traducción, pero desconoce el idioma del texto que se enviará al servicio Translator, puede usar la operación de detección de idioma. 

```python
detectDf =  spark.createDataFrame([
  (["Hello, what is your name?"],)
], ["text",])

detect = (Detect()
    .setLinkedService(cognitive_service_name)
    .setTextCol("text")
    .setOutputCol("result"))

display(detect
    .transform(detectDf)
    .withColumn("language", col("result.language"))
    .select("language"))
```

### <a name="expected-results"></a>Resultados esperados

```json
"["en"]"
```

## <a name="break-sentence"></a>División de la oración

Identifica el posicionamiento de los límites de las oraciones en un fragmento de texto.

```python
bsDf =  spark.createDataFrame([
  (["Hello, what is your name?"],)
], ["text",])

breakSentence = (BreakSentence()
    .setLinkedService(cognitive_service_name)
    .setTextCol("text")
    .setOutputCol("result"))

display(breakSentence
    .transform(bsDf)
    .withColumn("sentLen", flatten(col("result.sentLen")))
    .select("sentLen"))
```

### <a name="expected-results"></a>Resultados esperados

```json
"[25]"
```

## <a name="dictionary-lookup-alternate-translations"></a>Búsqueda en el diccionario (traducciones alternativas)
Con el punto de conexión, puede obtener traducciones alternativas de una palabra o frase. 

```python
dictDf = spark.createDataFrame([
  (["fly"],)
], ["text",])

dictionaryLookup = (DictionaryLookup()
    .setLinkedService(cognitive_service_name)
    .setFromLanguage("en")
    .setToLanguage("es")
    .setTextCol("text")
    .setOutputCol("result"))

display(dictionaryLookup
    .transform(dictDf)
    .withColumn("translations", flatten(col("result.translations")))
    .withColumn("normalizedTarget", col("translations.normalizedTarget"))
    .select("normalizedTarget"))
```

### <a name="expected-results"></a>Resultados esperados

|normalizedTarget|
|----|
|"["volar","mosca","operan","pilotar","moscas","marcha"]"|

## <a name="dictionary-examples-translations-in-context"></a>Ejemplos de diccionario (traducciones en contexto)

Una vez realizada una búsqueda en el diccionario, se puede pasar tanto el texto de origen como la traducción al punto de conexión de diccionario o de ejemplos para obtener una lista de ejemplos que muestran ambos términos en el contexto de una oración o una frase.

```python
dictDf = spark.createDataFrame([
  ([("fly", "volar")],)
], ["textAndTranslation",])

dictionaryExamples = (DictionaryExamples()
    .setLinkedService(cognitive_service_name)
    .setFromLanguage("en")
    .setToLanguage("es")
    .setTextAndTranslationCol("textAndTranslation")
    .setOutputCol("result"))

display(dictionaryExamples
    .transform(dictDf)
    .withColumn("examples", flatten(col("result.examples")))
    .select("examples"))
```

### <a name="expected-results"></a>Resultados esperados

```json

[{"sourcePrefix":"I mean, for a guy who could ","sourceSuffix":".","targetPrefix":"Quiero decir, para un tipo que podía ","targetTerm":"volar","sourceTerm":"fly","targetSuffix":"."},{"sourcePrefix":"Now it's time to make you ","sourceSuffix":".","targetPrefix":"Ahora es hora de que te haga ","targetTerm":"volar","sourceTerm":"fly","targetSuffix":"."},{"sourcePrefix":"One happy thought will make you ","sourceSuffix":".","targetPrefix":"Uno solo te hará ","targetTerm":"volar","sourceTerm":"fly","targetSuffix":"."},{"sourcePrefix":"They need machines to ","sourceSuffix":".","targetPrefix":"Necesitan máquinas para ","targetTerm":"volar","sourceTerm":"fly","targetSuffix":"."},{"sourcePrefix":"That should really ","sourceSuffix":".","targetPrefix":"Eso realmente debe ","targetTerm":"volar","sourceTerm":"fly","targetSuffix":"."},{"sourcePrefix":"It sure takes longer when you can't ","sourceSuffix":".","targetPrefix":"Lleva más tiempo cuando no puedes ","targetTerm":"volar","sourceTerm":"fly","targetSuffix":"."},{"sourcePrefix":"I have to ","sourceSuffix":" home in the morning.","targetPrefix":"Tengo que ","targetTerm":"volar","sourceTerm":"fly","targetSuffix":" a casa por la mañana."},{"sourcePrefix":"You taught me to ","sourceSuffix":".","targetPrefix":"Me enseñaste a ","targetTerm":"volar","sourceTerm":"fly","targetSuffix":"."},{"sourcePrefix":"I think you should ","sourceSuffix":" with the window closed.","targetPrefix":"Creo que debemos ","targetTerm":"volar","sourceTerm":"fly","targetSuffix":" con la ventana cerrada."},{"sourcePrefix":"They look like they could ","sourceSuffix":".","targetPrefix":"Parece que pudieran ","targetTerm":"volar","sourceTerm":"fly","targetSuffix":"."},{"sourcePrefix":"But you can ","sourceSuffix":", for instance?","targetPrefix":"Que puedes ","targetTerm":"volar","sourceTerm":"fly","targetSuffix":", por ejemplo."},{"sourcePrefix":"At least until her kids can be able to ","sourceSuffix":".","targetPrefix":"Al menos hasta que sus hijos sean capaces de ","targetTerm":"volar","sourceTerm":"fly","targetSuffix":"."},{"sourcePrefix":"I thought you could ","sourceSuffix":".","targetPrefix":"Pensé que podías ","targetTerm":"volar","sourceTerm":"fly","targetSuffix":"."},{"sourcePrefix":"I was wondering what it would be like to ","sourceSuffix":".","targetPrefix":"Me preguntaba cómo sería ","targetTerm":"volar","sourceTerm":"fly","targetSuffix":"."},{"sourcePrefix":"But nobody else can ","sourceSuffix":".","targetPrefix":"Pero nadie puede ","targetTerm":"volar","sourceTerm":"fly","targetSuffix":"."}]
```
## <a name="clean-up-resources"></a>Limpieza de recursos
Para asegurarse de que se cierra la instancia de Spark, finalice todas las sesiones (cuadernos) conectadas. El grupo se cierra cuando se alcanza el **tiempo de inactividad** especificado en el grupo de Apache Spark. También puede seleccionar **finalizar la sesión** en la barra de estado en la parte superior derecha del cuaderno.

![screenshot-showing-stop-session](./media/tutorial-build-applications-use-mmlspark/stop-session.png)

## <a name="next-steps"></a>Pasos siguientes

* [Consulte los cuadernos de ejemplo de Synapse](https://github.com/Azure-Samples/Synapse/tree/main/MachineLearning) 
* [Repositorio de GitHub para MMLSpark](https://github.com/Azure/mmlspark)
