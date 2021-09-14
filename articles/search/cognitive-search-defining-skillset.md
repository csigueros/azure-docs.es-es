---
title: Creación de un conjunto de aptitudes
titleSuffix: Azure Cognitive Search
description: Defina la extracción de datos, el procesamiento de lenguaje natural o los pasos de análisis de imágenes para enriquecer y extraer información estructurada de los datos para su uso en Azure Cognitive Search.
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 08/15/2021
ms.openlocfilehash: 4425a4b7c29bc0f4bc237c021610087c933631d8
ms.sourcegitcommit: 40866facf800a09574f97cc486b5f64fced67eb2
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/30/2021
ms.locfileid: "123224029"
---
# <a name="create-a-skillset-in-azure-cognitive-search"></a>Creación de un conjunto de aptitudes en Azure Cognitive Search

![Fases del indexador](media/cognitive-search-defining-skillset/indexer-stages-skillset.png "fases del indexador")

Un conjunto de aptitudes define las operaciones que extraen y enriquecen los datos para que se puedan buscar. Un conjunto de aptitudes se ejecuta después del descifrado de documentos, cuando se extrae el contenido de texto e imagen de los documentos de origen, y después de que se asignen (opcionalmente) los campos de dicho documento a los campos de destino de un índice o un almacén de conocimiento.

En este artículo, aprenderá los pasos para crear un conjunto de aptitudes. Como referencia, en este artículo se usa [Create Skillset (API REST)](/rest/api/searchservice/create-skillset). 

Algunas reglas de uso para los conjuntos de aptitudes son las siguientes:

+ Un conjunto de aptitudes es un recurso de nivel superior, lo que significa que muchos indexadores pueden crearlo una vez y hacer referencia a él.
+ Un conjunto de aptitudes debe contener al menos una aptitud.
+ Un conjunto de aptitudes puede repetir las aptitudes del mismo tipo (por ejemplo, varias aptitudes de conformador).

Recuerde que los indexadores dirigen la ejecución del conjunto de aptitudes, lo que significa que también tendrá que crear un [indexador](search-howto-create-indexers.md), un [origen de datos](search-data-sources-gallery.md) y un [índice de búsqueda](search-what-is-an-index.md) para poder probar el conjunto de aptitudes.

> [!TIP]
> Habilite el [almacenamiento en caché de enriquecimiento](cognitive-search-incremental-indexing-conceptual.md) para reutilizar el contenido que ya ha procesado y reducir el costo de desarrollo.

## <a name="skillset-definition"></a>Definición del conjunto aptitudes

Comience con la estructura básica. En la [API REST](/rest/api/searchservice/create-skillset), se ha creado un conjunto de aptitudes en JSON con las secciones siguientes:

```json
{
   "name":"skillset-template",
   "description":"A description makes the skillset self-documenting (comments aren't allowed in JSON itself)",
   "skills":[
      
   ],
   "cognitiveServices":{
      "@odata.type":"#Microsoft.Azure.Search.CognitiveServicesByKey",
      "description":"A Cognitive Services resource in the same region as Azure Cognitive Search",
      "key":"<Your-Cognitive-Services-Multiservice-Key>"
   },
   "knowledgeStore":{
      "storageConnectionString":"<Your-Azure-Storage-Connection-String>",
      "projections":[
         {
            "tables":[ ],
            "objects":[ ],
            "files":[ ]
         }
      ]
    },
    "encryptionKey":{ }
}
```

Después del nombre y la descripción, un conjunto de aptitudes tiene cuatro propiedades principales:

+ Una matriz `skills`: una [colección de aptitudes](cognitive-search-predefined-skills.md) sin ordenar para la que el servicio de búsqueda determina la secuencia de ejecución en función de las entradas necesarias para cada aptitud. Si las aptitudes son independientes, se ejecutarán en paralelo. Las aptitudes pueden ser utilitarias (como dividir texto), transformacionales (basadas en la inteligencia artificial de Cognitive Services) o aptitudes personalizadas que proporcione. En la sección siguiente se proporciona un ejemplo de una matriz de aptitudes.

+ `cognitiveServices` se usa para las [aptitudes facturables](cognitive-search-predefined-skills.md) que llaman a las API de Cognitive Services. Quite esta sección si no va a usar aptitudes facturables ni búsqueda de entidades personalizada. En caso contrario, [asocie un recurso](cognitive-search-attach-cognitive-services.md).

+ `knowledgeStore` (opcional): especifica una cuenta de Azure Storage y la configuración para proyectar la salida del conjunto de aptitudes en tablas, blobs y archivos de Azure Storage. Quite esta sección si no la necesita; de lo contrario, [especifique un almacén de conocimiento](knowledge-store-create-rest.md).

+ `encryptionKey` (opcional): especifica una instancia de Azure Key Vault y las [claves administradas por el cliente](search-security-manage-encryption-keys.md) que se usan para cifrar contenido confidencial en una definición de conjunto de aptitudes. Quite esta propiedad si no va a usar el cifrado administrado por el cliente.

## <a name="add-a-skills-array"></a>Adición de una matriz de aptitudes

Dentro de una definición de conjunto de aptitudes, la matriz de aptitudes especifica las aptitudes que se ejecutarán. En el ejemplo siguiente se presenta su composición mostrando dos [aptitudes integradas](cognitive-search-predefined-skills.md) no relacionadas. Observe que cada aptitud tiene un tipo, un contexto y entradas y salidas. 

```json
"skills":[
  {
    "@odata.type": "#Microsoft.Skills.Text.V3.EntityRecognitionSkill",
    "context": "/document",
    "categories": [ "Organization" ],
    "defaultLanguageCode": "en",
    "inputs": [
      {
        "name": "text",
        "source": "/document/content"
      }
    ],
    "outputs": [
      {
        "name": "organizations",
        "targetName": "orgs"
      }
    ]
  },
  {
    "@odata.type": "#Microsoft.Skills.Text.SentimentSkill",
    "context": "/document",
    "inputs": [
      {
        "name": "text",
        "source": "/document/content"
      }
    ],
    "outputs": [
      {
        "name": "score",
        "targetName": "mySentiment"
      }
    ]
  }
]
```

> [!NOTE]
> Puede crear conjuntos de aptitudes complejos con bucles y ramificaciones mediante la [aptitud condicional](cognitive-search-skill-conditional.md) para crear las expresiones. La sintaxis está basada en la notación de ruta del [puntero JSON](https://tools.ietf.org/html/rfc6901) con algunas modificaciones para identificar los nodos del árbol de enriquecimiento. `"/"` atraviesa un nivel inferior en el árbol y `"*"` actúa como un operador for-each en el contexto. En los numerosos ejemplos de este artículo se muestra la sintaxis. 

### <a name="how-built-in-skills-are-structured"></a>Estructuración de las aptitudes integradas

Cada aptitud es única en términos de sus valores de entrada y los parámetros que acepta. En la [documentación de cada aptitud](cognitive-search-predefined-skills.md) se describen todos los parámetros y propiedades de una aptitud determinada. Aunque hay diferencias, la mayoría de las aptitudes comparten un conjunto común y tienen un patrón similar. Para ilustrar varios puntos, la [aptitud Reconocimiento de entidades](cognitive-search-skill-entity-recognition-v3.md) proporciona un ejemplo:

```json
{
  "@odata.type": "#Microsoft.Skills.Text.V3.EntityRecognitionSkill",
  "context": "/document",
  "categories": [ "Organization" ],
  "defaultLanguageCode": "en",
  "inputs": [
    {
      "name": "text",
      "source": "/document/content"
    }
  ],
  "outputs": [
    {
      "name": "organizations",
      "targetName": "orgs"
    }
  ]
}
```

Los parámetros comunes incluyen "odata.type", "inputs" y "outputs". Los demás parámetros, es decir, "categories" y "defaultLanguageCode", son ejemplos de parámetros específicos de Reconocimiento de entidades. 

+ **"odata.type"** identifica de manera única cada aptitud. Puede encontrar el tipo en la [documentación de referencia de aptitudes](cognitive-search-predefined-skills.md).

+ **"context"** es un nodo de un árbol de enriquecimiento y representa el nivel en el que tienen lugar las operaciones. Todas las aptitudes tienen esta propiedad. Si el campo "context" no se establece explícitamente, el contexto predeterminado es `"/document"`. En el ejemplo, el contexto es todo el documento, lo que significa que a la aptitud de reconocimiento de entidades se le llama una vez por documento.

  El contexto también determina dónde se generan también las salidas en el árbol de enriquecimiento. En este ejemplo, la aptitud devuelve una propiedad denominada `"organizations"`, capturada como `orgs`, que se agrega como un nodo secundario de `"/document"`. En las aptitudes de nivel inferior, la ruta de acceso a este nodo de enriquecimiento recién creado es `"/document/orgs"`. Para un documento determinado, el valor de `"/document/orgs"` es una matriz de las organizaciones extraídas del texto (por ejemplo, `["Microsoft", "LinkedIn"]`). Para más información sobre la sintaxis de ruta de acceso, consulte [Referencia a las anotaciones en un conjunto de aptitudes de Azure Cognitive Search](cognitive-search-concept-annotations-syntax.md).

+ **"inputs"** especifica el origen de los datos de entrada y cómo se usarán. En el caso del reconocimiento de entidades, una de las entradas es `"text"`, que es el contenido que se va a analizar para las entidades. El contenido se origina en el nodo `"/document/content"` en un árbol de enriquecimiento. En un árbol de enriquecimiento, `"/document"` es el nodo raíz. En los documentos recuperados mediante un indexador de blobs de Azure, el campo `content` de cada documento es un campo estándar creado por el indexador. 

+ **"outputs"** representa la salida de la aptitud. Cada aptitud está diseñada para emitir tipos específicos de salida, a los que se hace referencia por el nombre en el conjunto de aptitudes. En el caso del reconocimiento de entidades, `"organizations"` es una de las salidas que admite. La documentación de cada aptitud describe las salidas que puede generar.

Salidas existen únicamente durante el procesamiento. Para encadenar esta salida a la entrada de una aptitud de bajada, haga referencia a la salida como `"/document/orgs"`. Para enviar la salida a un campo de un índice de búsqueda, [cree una asignación de campos de salida](cognitive-search-output-field-mapping.md) en un indexador. Para enviar la salida a un almacén de conocimiento, [cree una proyección](knowledge-store-projection-overview.md).

Las salidas de una aptitud pueden estar en conflicto con las salidas de otra aptitud. Si tiene varias aptitudes que devuelven la misma salida, use `"targetName"` para eliminar la ambigüedad con los nombres de las rutas de acceso del nodo de enriquecimiento.

Algunas situaciones requieren que se haga referencia a cada elemento de una matriz por separado. Por ejemplo, imagine que quiere pasar *cada elemento* de `"/document/orgs"` por separado a otra aptitud. Para ello, agregue un asterisco a la ruta de acceso: `"/document/orgs/*"`. 

La segunda aptitud en el análisis de sentimiento sigue el mismo patrón que el primer enriquecedor. Toma `"/document/content"` como entrada y devuelve una puntuación de opinión para cada instancia del contenido. Puesto que no estableció el campo "context" explícitamente, la salida (mySentiment) ahora es un elemento secundario de `"/document"`.

```json
{
  "@odata.type": "#Microsoft.Skills.Text.SentimentSkill",
  "inputs": [
    {
      "name": "text",
      "source": "/document/content"
    }
  ],
  "outputs": [
    {
      "name": "score",
      "targetName": "mySentiment"
    }
  ]
}
```

## <a name="adding-a-custom-skill"></a>Incorporación de una aptitud personalizada

Aquí tiene un ejemplo de una [aptitud personalizada](cognitive-search-custom-skill-web-api.md). El URI apunta a una función de Azure, que, a su vez, invoca el modelo o la transformación que se proporciona. Para más información, consulte [Definición de una interfaz personalizada](cognitive-search-custom-skill-interface.md).

Aunque la aptitud personalizada ejecuta código externo a la canalización, en una matriz de aptitudes, es simplemente otra aptitud. Al igual que las aptitudes integradas, tiene un tipo, un contexto y entradas y salidas. También lee y escribe en un árbol de enriquecimiento, igual que lo hacen las aptitudes integradas. Tenga en cuenta que el campo "context" está establecido en `"/document/orgs/*"` con un asterisco, lo que significa que se llama al paso de enriquecimiento *para cada* organización bajo `"/document/orgs"`.

La salida, en este caso una descripción de la compañía, se genera para cada organización identificada. Cuando se hace referencia a la descripción en un paso de bajada (por ejemplo, en la extracción de frases clave), se usaría la ruta de acceso `"/document/orgs/*/companyDescription"` para hacerlo. 

```json
{
  "@odata.type": "#Microsoft.Skills.Custom.WebApiSkill",
  "description": "This skill calls an Azure function, which in turn calls custom code",
  "uri": "https://indexer-e2e-webskill.azurewebsites.net/api/InvokeCode?code=foo",
  "httpHeaders": {
      "Ocp-Apim-Subscription-Key": "foobar"
  },
  "context": "/document/orgs/*",
  "inputs": [
    {
      "name": "query",
      "source": "/document/orgs/*"
    }
  ],
  "outputs": [
    {
      "name": "description",
      "targetName": "companyDescription"
    }
  ]
}
```

## <a name="send-output-to-an-index"></a>Envío de la salida a un índice

A medida que se ejecuta cada aptitud, su salida se agrega en forma de nodos al árbol de enriquecimiento de un documento. Los documentos enriquecidos existen en la canalización en forma de estructuras de datos temporales. Para crear una estructura de datos permanente y obtener una visibilidad completa sobre lo que realmente genera una aptitud, deberá enviar la salida a un índice de búsqueda o a un [almacén de conocimiento](knowledge-store-concept-intro.md).

En las primeras fases de la evaluación del conjunto de aptitudes, querrá comprobar los resultados preliminares con un esfuerzo mínimo. Se recomienda el índice de búsqueda porque es más sencillo de configurar. Para cada salida de aptitud, [defina una asignación de campos de salida](cognitive-search-output-field-mapping.md) en el indexador y un campo en el índice.

:::image type="content" source="media/cognitive-search-defining-skillset/skillset-indexer-index-combo.png" alt-text="Diagrama de objetos que muestra cómo se define una entidad de personas en la salida de la aptitud, la asignación de campos del indexador y el campo de índice.":::

Después de ejecutar el indexador, puede usar el [Explorador de búsqueda](search-explorer.md) para devolver documentos del índice y comprobar el contenido de cada campo a fin de determinar lo que ha detectado o creado el conjunto de aptitudes.

En el ejemplo siguiente se muestran los resultados de una aptitud de reconocimiento de entidades que detectó personas, ubicaciones, organizaciones y otras entidades en un fragmento de texto. Ver los resultados en el Explorador de búsqueda puede ayudarle a determinar si una aptitud agrega valor a la solución.

:::image type="content" source="media/cognitive-search-defining-skillset/doc-in-search-explorer.png" alt-text="Captura de pantalla de un documento en el Explorador de búsqueda.":::

## <a name="tips-for-a-first-skillset"></a>Sugerencias para un primer conjunto de aptitudes

+ Ensamblar un ejemplo representativo del contenido en Blob Storage u otro origen de datos de indexador admitido y ejecutar el asistente para **importación de datos** para crear el conjunto de aptitudes, el índice, el indizador y el objeto de origen de datos. 

  El asistente automatiza varios pasos que pueden ser difíciles por primera vez, incluida la definición de los campos de un índice, la definición de asignaciones de salida en un indizador y proyecciones en un almacén de conocimiento si se usa uno. Para algunas aptitudes, como OCR o análisis de imágenes, el asistente agregará aptitudes de utilidad que combinan el contenido de imagen y texto que se separó durante el descifrar el documento.

+ Como alternativa, puede importar colecciones Postman de aptitudes que proporcionan ejemplos completos de todas las definiciones de objeto necesarias para evaluar una aptitud, desde el conjunto de aptitudes a un índice que puede consultar para ver los resultados de una transformación.

## <a name="next-steps"></a>Pasos siguientes

Los campos de contexto y origen de entrada son rutas de acceso a los nodos de un árbol de enriquecimiento. Como paso siguiente, conozca mejor la sintaxis para configurar rutas de acceso a los nodos de un árbol de enriquecimiento.

> [!div class="nextstepaction"]
> [Referencia a las anotaciones de un conjunto de aptitudes](cognitive-search-concept-annotations-syntax.md)
