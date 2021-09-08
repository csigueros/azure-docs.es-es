---
title: Conceptos de conjunto de aptitudes
titleSuffix: Azure Cognitive Search
description: Los conjuntos de aptitudes es donde se crea una canalización de enriquecimiento con inteligencia artificial en Azure Cognitive Search. Aprenda conceptos importantes y detalles sobre la composición del conjunto de aptitudes.
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 08/10/2021
ms.openlocfilehash: c189fd8d77d33a2397e5a83f73dcdda759247a03
ms.sourcegitcommit: f2d0e1e91a6c345858d3c21b387b15e3b1fa8b4c
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/07/2021
ms.locfileid: "123535246"
---
# <a name="skillset-concepts-in-azure-cognitive-search"></a>Conceptos de conjunto de aptitudes en Azure Cognitive Search

Este artículo está destinado a los desarrolladores que necesitan una mejor comprensión de los conceptos y la composición del conjunto de aptitudes, y se supone que están familiarizados con los conceptos y flujos de trabajo de alto nivel del [enriquecimiento con IA](cognitive-search-concept-intro.md).

Un conjunto de aptitudes es un recurso reutilizable en Azure Cognitive Search que se adjunta a [un indexador](search-indexer-overview.md). Contiene una o varias aptitudes, que son operaciones atómicas que llaman a la inteligencia artificial integrada o al procesamiento personalizado externo sobre documentos recuperados de un origen de datos externo.

Desde el inicio del procesamiento del conjunto de aptitudes hasta su conclusión, las aptitudes leen y escriben en un documento enriquecido. Un documento enriquecido es inicialmente solo el contenido sin procesar extraído de un origen de datos, pero con cada ejecución de aptitud, obtiene estructura y esencia. En última instancia, los nodos de un documento enriquecido [se asignan a campos](cognitive-search-output-field-mapping.md) en un índice de búsqueda o [se asignan a proyecciones](knowledge-store-projection-overview.md) en un almacén de conocimiento, de modo que el contenido se pueda enrutar correctamente, donde otras aplicaciones lo consultarán o consumirán.

:::image type="content" source="media/knowledge-store-concept-intro/knowledge-store-concept-intro.svg" alt-text="Canalización con conjunto de aptitudes" border="false":::

## <a name="skillset-definition"></a>Definición del conjunto aptitudes

Un conjunto de aptitudes es una matriz con una o varias *aptitudes* que representan una operación de enriquecimiento atómica, como la traducción de texto, la extracción de frases clave o la realización de reconocimiento óptico de caracteres de un archivo de imagen. Las aptitudes pueden ser las [aptitudes integradas](cognitive-search-predefined-skills.md) de Microsoft o [aptitudes personalizadas](cognitive-search-create-custom-skill-example.md) que contengan modelos o lógica de procesamiento que proporcione. Genera documentos enriquecidos que se consumen durante la indexación o se proyectan en un almacén de conocimiento.

Las aptitudes tienen un tipo, un contexto y entradas y salidas que a menudo están encadenadas entre sí. En el ejemplo siguiente se muestran dos [aptitudes integradas](cognitive-search-predefined-skills.md) que funcionan juntas e introduce parte de la terminología de la definición del conjunto de aptitudes. 

+ La aptitud n.º 1 es una [aptitud de división del texto](cognitive-search-skill-textsplit.md) que acepta el contenido del campo de origen "reviews_text" como entrada y divide el contenido en "pages" de 5000 caracteres como salida. Dividir texto grande en fragmentos más pequeños puede producir mejores resultados durante el procesamiento del lenguaje natural.

+ La aptitud n.º 2 es una [aptitud de detección de opinión](cognitive-search-skill-sentiment.md) que acepta "pages" como entrada y genera un nuevo campo denominado "sentiment" como salida que contiene los resultados del análisis de opiniones.

```json
{
    "skills": [
        {
            "@odata.type": "#Microsoft.Skills.Text.SplitSkill",
            "name": "#1",
            "description": null,
            "context": "/document/reviews_text",
            "defaultLanguageCode": "en",
            "textSplitMode": "pages",
            "maximumPageLength": 5000,
            "inputs": [
                {
                    "name": "text",
                    "source": "/document/reviews_text"
                }
            ],
            "outputs": [
                {
                    "name": "textItems",
                    "targetName": "pages"
                }
            ]
        },
        {
            "@odata.type": "#Microsoft.Skills.Text.SentimentSkill",
            "name": "#2",
            "description": null,
            "context": "/document/reviews_text/pages/*",
            "defaultLanguageCode": "en",
            "inputs": [
                {
                    "name": "text",
                    "source": "/document/reviews_text/pages/*",
                }
            ],
            "outputs": [
                {
                    "name": "score",
                    "targetName": "Sentiment"
                }
            ]
        }
. . . 
}
```

Los puntos clave que hay que tener en cuenta sobre el ejemplo anterior son que las entradas y salidas son pares nombre-valor, que puede hacer coincidir las salidas de una aptitud con las entradas de las aptitudes de nivel inferior y que todas las aptitudes tienen contexto que determina dónde se produce el procesamiento en el árbol de enriquecimiento.

Para obtener más información sobre cómo se formulan las entradas y salidas, consulte [Procedimiento para hacer referencia a anotaciones](cognitive-search-concept-annotations-syntax.md).

## <a name="enrichment-tree"></a>Árbol de enriquecimiento

Un documento enriquecido es una estructura de datos temporal similar a un árbol creada durante la ejecución del conjunto de aptitudes que recopila todos los cambios introducidos a través de aptitudes y los representa en una jerarquía de nodos direccionables. Los nodos también incluyen los campos no enriquecidos que se pasan textualmente desde el origen de datos externo. Existe un documento enriquecido durante la ejecución del conjunto de aptitudes, pero se puede almacenar en caché o conservarse en un almacén de conocimiento. 

Inicialmente, un documento enriquecido es simplemente el contenido extraído de un origen de datos durante el [*descifrado de documentos*](search-indexer-overview.md#document-cracking), donde el texto y las imágenes se extraen del origen y están disponibles para el análisis de idioma o imagen. 

El contenido inicial es el *nodo raíz*. Normalmente es un documento completo o una imagen normalizada. La forma en que se articula en un árbol de enriquecimiento varía según cada tipo de origen de datos. En la tabla siguiente se muestra el estado de un documento que entra en la canalización de enriquecimiento para varios orígenes de datos compatibles:

|Origen de datos\Modo de análisis|Valor predeterminado|JSON, líneas JSON y CSV|
|---|---|---|
|Blob Storage|/document/content<br>/document/normalized_images/*<br>…|/document/{key1}<br>/document/{key2}<br>…|
|Azure SQL|/document/{column1}<br>/document/{column2}<br>…|N/D |
|Cosmos DB|/document/{key1}<br>/document/{key2}<br>…|N/D|

A medida que se ejecutan las aptitudes, la salida se agrega al árbol de enriquecimiento como nodos nuevos. Estos nodos se pueden usar como entradas para las aptitudes de nivel inferior y, finalmente, se proyectarán en un almacén de conocimiento o se asignarán a campos de índice. Las aptitudes que crean contenido, como las cadenas traducidas, escribirán su salida en el documento enriquecido. Del mismo modo, las aptitudes que consumen la salida de las aptitudes de nivel superior leerán desde el documento enriquecido para obtener las entradas necesarias. 

:::image type="content" source="media/cognitive-search-working-with-skillsets/skillset-def-enrichment-tree.png" alt-text="Aptitudes de lectura y escritura desde el árbol de enriquecimiento" border="false":::

Un árbol de enriquecimiento consta de contenido extraído y metadatos extraídos del origen, además de los nodos nuevos creados por una aptitud, como `translated_text` de la [aptitud de traducción de texto](cognitive-search-skill-text-translation.md), `locations` de la [aptitud de reconocimiento de entidades](cognitive-search-skill-entity-recognition-v3.md) o `keyPhrases` de la [aptitud de extracción de frases clave](cognitive-search-skill-keyphrases.md). Aunque puede [visualizar y trabajar con un árbol de enriquecimiento](cognitive-search-debug-session.md) a través de un editor visual, se trata principalmente de una estructura interna. 

Los enriquecimientos no son mutables: una vez creados, los nodos no se pueden editar. A medida que los conjuntos de aptitudes se vuelven más complejos, también lo hará el árbol de enriquecimiento, pero no todos los nodos del árbol de enriquecimiento deben asignarse al índice o al almacén de conocimiento. Puede conservar de forma selectiva solo un subconjunto de las salidas de enriquecimiento para que solo mantenga lo que piensa usar.

Dado que las entradas y salidas de una aptitud leen y escriben en árboles de enriquecimiento, una de las tareas que se realizarán como parte del diseño del conjunto de aptitudes es crear [asignaciones de campos de salida](cognitive-search-output-field-mapping.md) que muevan el contenido fuera del árbol de enriquecimiento y a un campo de un índice de búsqueda. Del mismo modo, si va a crear un almacén de conocimiento, puede asignar salidas a [formas](knowledge-store-projection-shape.md) asignadas a proyecciones.

> [!NOTE]
> El formato de árbol de enriquecimiento permite que la canalización de enriquecimiento adjunte metadatos incluso a tipos de datos primitivos. Los metadatos no serán un objeto JSON válido, pero se pueden proyectar en un formato JSON válido en definiciones de proyección en un almacén de conocimiento. Para obtener más información, consulte [Aptitud de conformador](cognitive-search-skill-shaper.md).

## <a name="context"></a>Context

Cada aptitud tiene un contexto, que puede ser todo el documento (`/document`) o un nodo inferior en el árbol (`/document/countries/`). Un contexto determina:

+ El número de veces que se ejecuta la aptitud, en un solo valor (una vez por campo, por documento) o para los valores de contexto de la colección de tipos, donde agregar un elemento `/*` genera una invocación de aptitud, una vez para cada instancia de la colección. 

+ La declaración de salida o dónde se agregan las salidas de la aptitud en el árbol de enriquecimiento. Las salidas siempre se agregan al árbol como elementos secundarios del nodo de contexto.

+ La forma de las entradas. En el caso de colecciones de varios niveles, el establecimiento del contexto en la colección primaria afectará a la forma de la entrada de la aptitud. Por ejemplo, si tiene un árbol de enriquecimiento con una lista de países o regiones, cada uno de ellos enriquecido con una lista de estados que contiene una lista de códigos postales, la forma en que establezca el contexto determinará cómo se interpretará la entrada.

|Context|Entrada|Forma de la entrada|Invocación de la aptitud|
|-------|-----|--------------|----------------|
|`/document/countries/*` |`/document/countries/*/states/*/zipcodes/*` |Lista de todos los códigos postales del país o región |Una vez por país o región |
|`/document/countries/*/states/*` |`/document/countries/*/states/*/zipcodes/*` |Lista de todos los códigos postales del estado | Una vez por combinación de país o región y estado|

## <a name="enrichment-example"></a>Ejemplo de enriquecimiento

Con el [conjunto de aptitudes de reseñas de hoteles](https://github.com/Azure-Samples/azure-search-sample-data/blob/master/hotelreviews/HotelReviews_skillset.json) como punto de referencia, en este ejemplo se explica cómo evoluciona un [árbol de enriquecimiento](cognitive-search-working-with-skillsets.md#enrichment-tree) a través de la ejecución de aptitudes mediante diagramas conceptuales.

Este ejemplo también muestra lo siguiente:

+ Cómo funcionan el contexto y las entradas de una aptitud para determinar el número de veces que se ejecuta una aptitud.
+ Qué forma tiene la entrada según el contexto.

En este ejemplo, los campos de origen de un archivo CSV incluyen reseñas de clientes sobre hoteles ("reviews_text") y clasificaciones ("reviews_rating"). El indexador agrega campos de metadatos desde Blob Storage y las aptitudes agregan texto traducido, puntuaciones de opiniones y detección de frases clave.

En el ejemplo de reseñas de hotel, un elemento "document" dentro del proceso de enriquecimiento representa una sola reseña de hotel.

> [!TIP]
> Puede crear un índice de búsqueda y un almacén de conocimiento para estos datos en [Azure Portal](knowledge-store-create-portal.md) o a través de [Postman y las API REST](knowledge-store-create-rest.md). También puede usar [sesiones de depuración](cognitive-search-debug-session.md) para obtener información sobre la composición del conjunto de aptitudes, las dependencias y los efectos en un árbol de enriquecimiento. Las imágenes de este artículo se extraen de sesiones de depuración.

Conceptualmente, el árbol de enriquecimiento inicial tiene el siguiente aspecto:

![Árbol de enriquecimiento después del descifrado de documentos](media/cognitive-search-working-with-skillsets/enrichment-tree-doc-cracking.png "Árbol de enriquecimiento después del descifrado de documentos y antes de la ejecución de la aptitud")

El nodo raíz de todos los enriquecimientos es `"/document"`. Al trabajar con indexadores de blobs, el nodo `"/document"` tendrá nodos secundarios de `"/document/content"` y `"/document/normalized_images"`. Cuando se trabaja con datos CSV, como en este ejemplo, los nombres de columna se asignan a los nodos situados debajo de `"/document"`.

### <a name="skill-1-split-skill"></a>Aptitud 1: aptitud de división

Cuando el contenido de origen se compone de grandes fragmentos de texto, resulta útil dividirlo en componentes más pequeños para una mayor precisión en la detección del idioma, la opinión y las frases clave. Hay dos intervalos disponibles: páginas y oraciones. Una página consta de aproximadamente 5000 caracteres.

Una aptitud de división de texto suele ir primero en un conjunto de aptitudes.

```json
"@odata.type": "#Microsoft.Skills.Text.SplitSkill",
"name": "#1",
"description": null,
"context": "/document/reviews_text",
"defaultLanguageCode": "en",
"textSplitMode": "pages",
"maximumPageLength": 5000,
"inputs": [
{
    "name": "text",
    "source": "/document/reviews_text"
}
],
"outputs": [
{
    "name": "textItems",
    "targetName": "pages"
}
```

Con el contexto de aptitud de `"/document/reviews_text"`, la aptitud de división se ejecutará una vez para `reviews_text`. La salida de la aptitud es una lista donde `reviews_text` se fragmenta en segmentos de 5000 caracteres. La salida de la aptitud de división se denomina `pages` y se agrega al árbol de enriquecimiento. La característica `targetName` permite cambiar el nombre de una salida de aptitud antes de agregarla al árbol de enriquecimiento.

El árbol enriquecimiento tiene ahora un nuevo nodo colocado en el contexto de la aptitud. Este nodo está disponible para cualquier aptitud, proyección o asignación de campos de salida. 
 
![Árbol de enriquecimiento después de la aptitud 1](media/cognitive-search-working-with-skillsets/enrichment-tree-skill1.png "Árbol de enriquecimiento después de la ejecución de la aptitud 1")

Para acceder a cualquiera de los enriquecimientos agregados a un nodo mediante una aptitud, se necesita la ruta de acceso completa para el enriquecimiento. Por ejemplo, si desea utilizar el texto del nodo ```pages``` como una entrada a otra aptitud, deberá especificarlo como ```"/document/reviews_text/pages/*"```. Para obtener más información sobre las rutas de acceso, consulte [Anotaciones de referencia](cognitive-search-concept-annotations-syntax.md).

### <a name="skill-2-language-detection"></a>Aptitud 2: detección de idioma

Los documentos de reseñas de hoteles incluyen comentarios de los clientes realizados en varios idiomas. La aptitud de detección de idiomas determina el idioma que se usó. A continuación, el resultado se pasará a la extracción de frases clave y a la detección de opiniones (no se muestra), teniendo en cuenta el idioma durante la detección de la opinión y las frases.

Aunque la aptitud de detección de idioma es la tercera (aptitud 3) definida en el conjunto de aptitudes, es la siguiente aptitud que se ejecuta. Dado que no se bloquea al no requerir entradas, se ejecutará en paralelo con la aptitud anterior. Al igual que la aptitud de división que la precedía, la aptitud de detección de idioma también se invoca una vez para cada documento. El árbol de enriquecimiento tiene ahora un nuevo nodo para Language.

 ![Árbol de enriquecimiento después de la aptitud 2](media/cognitive-search-working-with-skillsets/enrichment-tree-skill2.png "Árbol de enriquecimiento después de la ejecución de la aptitud 2")

### <a name="skills-3-and-4-sentiment-analysis-and-key-phrase-detection"></a>Aptitudes 3 y 4: análisis de opiniones y detección de frases clave

Los comentarios de los clientes reflejan una gran variedad de experiencias positivas y negativas. La aptitud de análisis de opiniones analiza los comentarios y asigna una puntuación a lo largo de una continuación de números negativos a positivos, o neutral si la opinión no está determinada. En paralelo al análisis de opiniones, la detección de frases clave identifica y extrae palabras y frases cortas que aparecen como consecuencia.

Dado el contexto de `/document/reviews_text/pages/*`, tanto las aptitudes de análisis de opiniones como de frases clave se invocará una vez para cada uno de los elementos de la colección `pages`. La salida de la aptitud será un nodo bajo el elemento de página asociado. 

Ahora deberá poder observar el resto de las aptitudes del conjunto y visualizar cómo seguirá creciendo el árbol de enriquecimientos con la ejecución de cada una de las aptitudes. Algunas aptitudes, como la aptitud de combinación y la aptitud de conformador, también crean nuevos nodos, pero solo usan datos de los nodos existentes y no crean nuevos enriquecimientos netos.

![Árbol de enriquecimiento después de todas las aptitudes](media/cognitive-search-working-with-skillsets/enrichment-tree-final.png "Árbol de enriquecimiento después de todas las aptitudes")

Los colores de los conectores en el árbol anterior indican que los enriquecimientos se crearon mediante distintas aptitudes y que los nodos deben abordarse de forma individual y no formarán parte del objeto devuelto al seleccionar el nodo primario.

### <a name="skill-5-shaper-skill"></a>Aptitud 5: aptitud de conformador

Si la salida incluye un [almacén de conocimiento](knowledge-store-concept-intro.md), agregue una [aptitud de conformador](cognitive-search-skill-shaper.md) como último paso. La aptitud de conformador crea formas de datos de nodos en un árbol de enriquecimiento. Por ejemplo, es posible que desee consolidar varios nodos en una sola forma. Después, puede proyectar esta forma como una tabla (los nodos se convierten en las columnas de una tabla), pasando la forma por nombre a una proyección de tabla.

Trabajar con la aptitud de conformador resulta sencillo porque se centra en dar forma a una aptitud. Como alternativa, puede optar por dar forma en línea dentro de proyecciones individuales. La aptitud de conformador no agrega ni resta de un árbol de enriquecimiento, por lo que no se visualiza. En su lugar, puede pensar en una aptitud de conformador como el medio por el que vuelve a articular el árbol de enriquecimiento que ya tiene. Conceptualmente, esto es similar a la creación de vistas de tablas en una base de datos.

```json
{
  "@odata.type": "#Microsoft.Skills.Util.ShaperSkill",
  "name": "#5",
  "description": null,
  "context": "/document",
  "inputs": [
    {
      "name": "name",
      "source": "/document/name"
    },
    {
      "name": "reviews_date",
      "source": "/document/reviews_date"
    },
    {
      "name": "reviews_rating",
      "source": "/document/reviews_rating"
    },
    {
      "name": "reviews_text",
      "source": "/document/reviews_text"
    },
    {
      "name": "reviews_title",
      "source": "/document/reviews_title"
    },
    {
      "name": "AzureSearch_DocumentKey",
      "source": "/document/AzureSearch_DocumentKey"
    },
    {
      "name": "pages",
      "sourceContext": "/document/reviews_text/pages/*",
      "inputs": [
        {
          "name": "Sentiment",
          "source": "/document/reviews_text/pages/*/Sentiment"
        },
        {
          "name": "LanguageCode",
          "source": "/document/Language"
        },
        {
          "name": "Page",
          "source": "/document/reviews_text/pages/*"
        },
        {
          "name": "keyphrase",
          "sourceContext": "/document/reviews_text/pages/*/Keyphrases/*",
          "inputs": [
            {
              "name": "Keyphrases",
              "source": "/document/reviews_text/pages/*/Keyphrases/*"
            }
          ]
        }
      ]
    }
  ],
  "outputs": [
    {
      "name": "output",
      "targetName": "tableprojection"
    }
  ]
}
```

## <a name="next-steps"></a>Pasos siguientes

Con una introducción y un ejemplo a sus espaldas, pruebe a crear su primer conjunto de aptitudes mediante [aptitudes integradas](cognitive-search-predefined-skills.md).

> [!div class="nextstepaction"]
> [Cree su primer conjunto de aptitudes](cognitive-search-defining-skillset.md).
