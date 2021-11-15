---
title: Referencia de los componentes del hash de características
titleSuffix: Azure Machine Learning
description: Aprenda a usar el componente Feature Hashing del diseñador Azure Machine Learning para caracterizar datos de texto.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 02/22/2020
ms.openlocfilehash: 172498550cf6de5d57475805fba592d7e43666e9
ms.sourcegitcommit: e41827d894a4aa12cbff62c51393dfc236297e10
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/04/2021
ms.locfileid: "131566110"
---
# <a name="feature-hashing-component-reference"></a>Referencia de los componentes del hash de características

En este artículo se describe un componente incluido en el diseñador de Azure Machine Learning.

Use el componente Feature Hashing para transformar un flujo de texto en inglés en un conjunto de características de enteros. Después, puede pasar este conjunto de características con hash a un algoritmo de aprendizaje automático para entrenar un modelo de análisis de texto.

La función de hash de características que se proporciona en este componente se basa en la plataforma nimbusml. Para más información, consulte [Clase NgramHash](/python/api/nimbusml/nimbusml.feature_extraction.text.extractor.ngramhash?view=nimbusml-py-latest&preserve-view=true).

## <a name="what-is-feature-hashing"></a>¿Qué es Feature Hashing?

Feature Hashing funciona mediante la conversión de tokens únicos en enteros. Funciona exactamente en las cadenas que proporcione como entrada y no realiza ningún análisis lingüístico ni preprocesamiento. 

Por ejemplo, tome un conjunto de oraciones simples como estas, seguido de una puntuación de opinión. Supongamos que desea utilizar este texto para generar un modelo.

|Texto del usuario|Opinión|
|--------------|---------------|
|Me gustaba este libro|3|
|No me gustaba este libro|1|
|Este libro era fantástico|3|
|Me encantan los libros|2|

Internamente, el componente Feature Hashing crea un diccionario de n-gramas. Por ejemplo, la lista de bigramas de este conjunto de datos debería tener el siguiente aspecto:

|Término (bigramas)|Frecuencia|
|------------|---------------|
|Este libro|3|
|Me gustaba|1|
|No me|1|
|Me encanta|1|

Puede controlar el tamaño de los n-gramas mediante la propiedad **N-grams**. Si elige bigramas, también se calcularán los unigramas. El diccionario también incluiría términos individuales como estos:

|Término (unigramas)|Frecuencia|
|------------|---------------|
|libro|3|
|I|3|
|libros|1|
|gustaba|1|

Una vez creado el diccionario, el componente Feature Hashing convierte los términos del diccionario en valores hash. Después calcula si se ha procesado una característica en cada caso. Para cada fila de datos de texto, el componente genera un conjunto de columnas, una columna para cada característica con hash.

Por ejemplo, después de aplicar el algoritmo hash, las columnas de características pueden tener un aspecto similar al siguiente:

|Rating|Característica de hash 1|Característica de hash 2|Característica de hash 3|
|-----|-----|-----|-----|
|4|1|1|0|
|5|0|0|0|

* Si el valor de la columna es 0, la fila no contenía la característica con hash.
* Si el valor es 1, la fila sí contenía la característica.

Feature Hashing le permite representar documentos de texto de longitud variable como vectores numéricos de características de igual longitud para reducir la dimensionalidad. Si intentó usar la columna de texto para el entrenamiento tal cual estaba, esta se tratará como una columna de características categórica con muchos valores distintos.

Las salidas numéricas también permiten usar métodos de aprendizaje automático comunes como la clasificación, la agrupación en clústeres y la recuperación de información. Dado que las operaciones de búsqueda pueden utilizar hashes de enteros en lugar de comparaciones de cadenas, obtener los pesos de las características también es mucho más rápido.

## <a name="configure-the-feature-hashing-component"></a>Configuración del componente Feature Hashing

1.  Agregue el componente Feature Hashing a la canalización en el diseñador.

1. Conecte el conjunto de datos que contiene el texto que desea analizar.

    > [!TIP]
    > Dado que Feature Hashing no realiza operaciones léxicas como lematización o truncamiento, a veces puede obtener mejores resultados si preprocesa el texto antes de aplicar Feature Hashing. 

1. Establezca **Target columns** (Columnas de destino) en las columnas de texto que desea convertir en características con hash. Tenga en cuenta que:

    * Las columnas deben ser del tipo de datos de cadena.
    
    * La elección de varias columnas de texto puede tener un impacto significativo en la dimensionalidad de las características. Por ejemplo, el número de columnas de un hash de 10 bits va desde 1024 para una sola columna hasta 2048 para dos columnas.

1. Utilice **Hashing bitsize** (Tamaño de bits de hash) para especificar el número de bits que se va a usar al crear la tabla hash.
    
    El tamaño de bits predeterminado es 10. Para muchos problemas, este valor es adecuado. Es posible que necesite más espacio para evitar colisiones en función del tamaño del vocabulario de n-gramas del texto de entrenamiento.
    
1. En **N-grams** (N-gramas), escriba un número que defina la longitud máxima de los n-gramas que se van a agregar al diccionario de entrenamiento. Un n-grama es una secuencia de *n* palabras, que se trata como una unidad única.

    Por ejemplo, si escribe 3, se crearán unigramas, bigramas y trigramas.

1. Envíe la canalización.

## <a name="results"></a>Results

Una vez completado el procesamiento, el componente genera un conjunto de datos transformado en el que la columna de texto original se ha convertido en varias columnas. Cada columna representa una característica del texto. Dependiendo de la importancia del diccionario, el conjunto de resultados puede ser grande:

|Nombre de la columna 1|Tipo de columna 2|
|-------------------|-------------------|
|TEXTO DE USUARIO|Columna de datos original|
|OPINIÓN|Columna de datos original|
|TEXTO DE USUARIO: característica de hash 1|Columna de características con hash|
|TEXTO DE USUARIO: característica de hash 2|Columna de características con hash|
|TEXTO DE USUARIO: característica de hash 3|Columna de características con hash|
|TEXTO DE USUARIO: característica de hash 1024|Columna de características con hash|

Después de crear el conjunto de datos transformado, puede usarlo como entrada para el componente Entrenar modelo.
 
## <a name="best-practices"></a>Procedimientos recomendados

Los siguientes procedimientos recomendados pueden ayudarle a sacar el máximo partido al componente Feature Hashing:

* Agregue un componente de preprocesamiento de texto antes de usar Feature Hashing para preprocesar el texto de entrada. 

* Agregue un componente de selección de columnas después del componente Feature Hashing para eliminar columnas de texto del conjunto de datos de salida. No necesita las columnas de texto una vez generadas las características de hash.
    
* Considere la posibilidad de usar estas opciones de preprocesamiento de texto para simplificar los resultados y mejorar la precisión:

    * División de palabras
    * Detención de la eliminación de palabras
    * Normalización de mayúsculas
    * Eliminación de caracteres especiales y de puntuación
    * Raíz  

El conjunto óptimo de métodos de preprocesamiento que se aplican en cualquier solución depende del dominio, del vocabulario y de la necesidad empresarial. Canalice los datos para ver qué métodos de procesamiento de texto son más eficaces.

## <a name="next-steps"></a>Pasos siguientes
            
Consulte el [conjunto de componentes disponibles](component-reference.md) para Azure Machine Learning.
