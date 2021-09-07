---
title: Conceptos del almacén de conocimiento
titleSuffix: Azure Cognitive Search
description: Envíe documentos enriquecidos a Azure Storage. En esta solución podrá cambiar ver y consumir documentos enriquecidos en Azure Cognitive Search y otras aplicaciones, además de cambiarle la forma.
author: HeidiSteen
manager: nitinme
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 08/10/2021
ms.openlocfilehash: baa4a78e23b83fa7c138e71b92dba12ba23a3ab6
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/13/2021
ms.locfileid: "121736317"
---
# <a name="knowledge-store-in-azure-cognitive-search"></a>Almacén de conocimiento en Azure Cognitive Search

El almacén de conocimiento es una característica de Azure Cognitive Search que conserva la salida de una [canalización de enriquecimiento con IA](cognitive-search-concept-intro.md) en Azure Storage para realizar análisis independientes o procesos de bajada. Un *documento enriquecido* es la salida de una canalización creada a partir del contenido que se ha extraído, estructurado y analizado mediante procesos de inteligencia artificial. En una canalización estándar de inteligencia artificial, los documentos enriquecidos son transitorios, solo se usan durante la indexación y después se descartan. La creación de un almacén de conocimiento conserva los documentos enriquecidos para su inspección o para otros escenarios de minería de conocimiento.

Si ha usado aptitudes cognitivas anteriormente, ya sabe que los *conjuntos de aptitudes* se usan para mover un documento por una secuencia de enriquecimientos. El resultado puede ser un índice de búsqueda o proyecciones en un almacén de conocimientos. Las dos salidas, el índice de búsqueda y el almacén de conocimiento son productos de la misma canalización. Aunque se obtienen de las mismas entradas, se genera una salida estructurada y almacenada, que se emplea de aplicaciones diferentes.

:::image type="content" source="media/knowledge-store-concept-intro/knowledge-store-concept-intro.svg" alt-text="Canalización con conjunto de aptitudes" border="false":::

<!-- previous version of the architecture diagram
:::image type="content" source="media/knowledge-store-concept-intro/annotationstore_sans_internalcache.png" alt-text="Knowledge store in pipeline diagram" border="false"::: -->

Físicamente, un almacén de conocimiento es [Azure Storage](../storage/common/storage-account-overview.md), ya sea Azure Table Storage, Azure Blob Storage o ambos. Cualquier herramienta o proceso que pueda conectarse a Azure Storage puede consumir el contenido de un almacén de conocimiento.

Visto a través del Explorador de Storage, un almacén de conocimiento es simplemente una colección de tablas, objetos o archivos. En el ejemplo siguiente se muestra un almacén de conocimiento compuesto por tres tablas con campos que se arrastran desde el origen de datos o se crean mediante enriquecimientos (vea "sentiment score" y "translated_text").

:::image type="content" source="media/knowledge-store-concept-intro/kstore-in-storage-explorer.png" alt-text="Lectura y escritura de aptitudes desde el árbol de enriquecimiento" border="true":::

## <a name="benefits-of-knowledge-store"></a>Ventajas de Knowledge Store

Un almacén de conocimiento proporciona estructura, contexto y contenido real, recopilados a partir de archivos de datos no estructurados y semiestructurados, como blobs, archivos de imagen que se han sometido a análisis o, incluso, datos estructurados cuya forma ha cambiado. En una [guía paso a paso](knowledge-store-create-rest.md), puede ver de primera mano cómo un documento JSON denso se particiona en subestructuras, se reconstituye en nuevas estructuras y, además, se pone a disposición para procesos de nivel inferior, como cargas de trabajo de ciencia de datos y aprendizaje automático.

Aunque es útil ver lo que puede producir una canalización de enriquecimiento con IA, la verdadera ventaja del almacén de conocimiento radica en la capacidad de remodelar los datos. Puede comenzar con un conjunto de aptitudes básico y, luego, iterarlo para agregar niveles crecientes de estructura que luego se pueden combinar en nuevas estructuras, y que pueden consumirse en otras aplicaciones, además de Azure Cognitive Search.

Entre las ventajas de Knowledge Store, se incluyen las siguientes:

+ Consumir documentos enriquecidos en [herramientas de informes y análisis](#tools-and-apps), además de buscar. Power BI con Power Query es una opción atractiva, pero cualquier herramienta o aplicación que se pueda conectar a Azure Storage puede beneficiarse de un almacén de conocimiento que cree.

+ Refinar una canalización de indexación de IA durante la depuración de pasos y definiciones de conjuntos de aptitudes. Un almacén de conocimientos le muestra el producto de la definición de un conjunto de aptitudes en una canalización de indexación de IA. Puede usar estos resultados para diseñar un mejor conjunto de aptitudes, ya que puede ver exactamente la apariencia de los enriquecimientos. Puede usar el [Explorador de Storage](../vs-azure-tools-storage-manage-with-storage-explorer.md?tabs=windows) en Azure Storage para ver el contenido de un almacén de conocimiento.

+ Cambiar la forma de los datos. El remodelado se codifica en conjuntos de aptitudes, pero la aptitud [Conformador](cognitive-search-skill-shaper.md) de Azure Cognitive Search proporciona control explícito y la capacidad de crear varias formas. El modelado le permite definir una proyección que se alinee con el uso previsto de los datos, al tiempo que conserva las relaciones.

> [!VIDEO https://www.youtube.com/embed/XWzLBP8iWqg?version=3]

## <a name="knowledge-store-definition"></a>Definición del almacén de conocimiento

Durante la ejecución del indexador, los almacenes de conocimiento se crean en la [cuenta de Azure Storage](../storage/common/storage-account-overview.md), mediante Azure Table Storage, Azure Blob Storage o ambos. 

Las estructuras de datos Azure Storage se especifican a través del elemento `projections` de una definición `knowledgeStore` en un conjunto de aptitudes. Las [proyecciones](knowledge-store-projection-overview.md) se pueden articular como tablas, objetos o archivos, y puede tener varios conjuntos (o *grupos de proyecciones*) para crear varias estructuras de datos para distintos propósitos.

```json
"knowledgeStore":{
   "storageConnectionString":"<YOUR-AZURE-STORAGE-ACCOUNT-CONNECTION-STRING>",
   "projections":[
      {
         "tables":[ ],
         "objects":[ ],
         "files":[ ]
      }
   }
```

El tipo de proyección que especifique en esta estructura determina el tipo de almacenamiento que usará el almacén de conocimiento.

+ `tables` proyecta el contenido enriquecido en Table Storage. Defina una proyección de tabla cuando necesite estructuras de informes tabulares para escribir entradas en herramientas analíticas o realizar exportaciones de tramas de datos en otros almacenes de datos. Puede especificar varios elementos `tables` en el mismo grupo de proyección para obtener un subconjunto o una sección transversal de documentos enriquecidos. Dentro del mismo grupo de proyección, las relaciones entre las tablas se conservan para que pueda trabajar con todas ellas.

+ `objects` proyecta el documento JSON en Blob Storage. La representación física de un elemento `object` es una estructura JSON jerárquica que representa un documento enriquecido.

+ `files` proyecta los archivos de imagen en Blob Storage. Un elemento `file` es una imagen extraída de un documento, que se transfiere sin cambios al almacenamiento de blobs.

## <a name="create-a-knowledge-store"></a>Crear un almacén de conocimientos

Para crear el almacén de conocimiento, use el portal o una API. Necesitará [Azure Storage](../storage/index.yml), un [conjunto de aptitudes](cognitive-search-working-with-skillsets.md) y un [indexador](search-indexer-overview.md). Dado que los indexadores requieren un índice de búsqueda, también deberá proporcionar una definición de índice.

### <a name="azure-portal"></a>[**Azure Portal**](#tab/kstore-portal)

[Cree su primer almacén de conocimiento en cuatro pasos](knowledge-store-connect-power-bi.md) mediante el asistente para **importación de datos**. El asistente le guiará en las tareas siguientes:

1. Seleccione un origen de datos compatible que proporcione contenido sin procesar.

1. Especifique el enriquecimiento: asocie un recurso de Cognitive Services, seleccione las aptitudes y especifique un almacén de conocimiento. En este paso, elegirá una cuenta Azure Storage y decidirá si desea crear objetos, tablas o ambas cosas.

1. Cree un esquema de índice. El asistente lo necesita y puede inferir uno automáticamente.

1. Ejecute el asistente. La extracción, el enriquecimiento y el almacenamiento se producen en este último paso.

Cuando se usa el asistente, se controlan internamente varias tareas adicionales que, de lo contrario, tendría que controlar en el código. Tanto el modelado como las proyecciones (definiciones de estructuras de datos físicas en Azure Storage) se crean automáticamente. Al crear manualmente un almacén de conocimiento, el código deberá cubrir estos pasos.

### <a name="rest"></a>[**REST**](#tab/kstore-rest)

La versión de la API de REST `2020-06-30` se puede usar para crear un almacén de conocimiento mediante adiciones a un conjunto de aptitudes.

+ [Creación de un conjunto de aptitudes (api-version=2020-06-30)](/rest/api/searchservice/create-skillset)
+ [Actualización de un conjunto de aptitudes (api-version=2020-06-30)](/rest/api/searchservice/update-skillset)

Un elemento `knowledgeStore` se define dentro de un [conjunto de aptitudes](cognitive-search-working-with-skillsets.md) que, a su vez, se invoca mediante un [indizador](search-indexer-overview.md). Durante el enriquecimiento, Azure Cognitive Search crea un espacio en la cuenta de Azure Storage y proyecta los documentos enriquecidos como blobs o en tablas, según la configuración.

Tareas que el código tendrá que controlar:

+ Especificación de las proyecciones que desea que se creen en Azure Storage (tablas, objetos, archivos)
+ Inclusión de una aptitud Conformador en el conjunto de aptitudes para determinar el esquema y el contenido de la proyección
+ Asignación de la forma con nombre a una proyección

Una manera fácil de explorar es [crear el primer almacén de conocimiento mediante Postman](knowledge-store-create-rest.md).

### <a name="net-sdk"></a>[**SDK de .NET**](#tab/kstore-dotnet)

Para los desarrolladores de .NET, use la [clase KnowledgeStore](/dotnet/api/azure.search.documents.indexes.models.knowledgestore) en la biblioteca cliente Azure.Search.Documents.

---

<a name="tools-and-apps"></a>

## <a name="connect-with-apps"></a>Conexión con aplicaciones

Una vez que el enriquecimiento existe en el almacenamiento, puede usarse cualquier herramienta o tecnología que se conecte a Azure Blob o Table Storage para explorar, analizar o consumir el contenido. La lista siguiente es un comienzo:

+ El [Explorador de Storage](knowledge-store-view-storage-explorer.md) para ver la estructura y el contenido de los documentos enriquecidos. Considere esto como la herramienta de base de referencia para ver el contenido del almacén de conocimientos.

+ [Power BI](knowledge-store-connect-power-bi.md) para crear informes y realizar análisis. 

+ [Azure Data Factory](../data-factory/index.yml) para manipulación adicional.

## <a name="content-lifecycle"></a>Ciclo de vida del contenido

Cada vez que se ejecutan el indexador y el conjunto de aptitudes, el almacén de conocimiento se actualiza si el conjunto de aptitudes o los datos de origen subyacentes han cambiado. Los cambios que recoge el indexador se propagan a través del proceso de enriquecimiento a las proyecciones del almacén de conocimiento, lo que garantiza que los datos proyectados son una representación actual del contenido en el origen de datos de origen. 

> [!Note]
> Aunque puede editar los datos en las proyecciones, las modificaciones se sobrescribirán en la invocación de la canalización siguiente, suponiendo que se actualice el documento en los datos de origen. 

### <a name="changes-in-source-data"></a>Cambios en los datos de origen

En el caso de los orígenes de datos que admiten el seguimiento de cambios, un indexador procesará documentos nuevos y modificados, y omitirá los documentos existentes que ya se han procesado. La información de marca de tiempo varía según el origen de datos, pero en un contenedor de blobs, el indexador examina la fecha `lastmodified` para determinar qué blobs se deben ingerir.

### <a name="changes-to-a-skillset"></a>Cambios en un conjunto de aptitudes

Si va a realizar cambios en un conjunto de aptitudes, debe [habilitar el almacenamiento en caché de documentos enriquecidos](cognitive-search-incremental-indexing-conceptual.md) para reutilizar los enriquecimientos existentes siempre que sea posible.

Sin el almacenamiento en caché incremental, el indexador siempre procesará los documentos por orden de límite máximo, sin retroceder. En el caso de los blobs, el indexador procesaría los blobs ordenados por `lastModified`, independientemente de los cambios en la configuración del indexador o en el conjunto de aptitudes. Si cambia un conjunto de aptitudes, los documentos procesados previamente no se actualizan para reflejar el nuevo conjunto de aptitudes. Los documentos procesados después del cambio del conjunto de aptitudes usarán el nuevo conjunto de aptitudes, lo que provocará que los documentos de índice sean una combinación de conjuntos de aptitudes antiguos y nuevos.

Con el almacenamiento en caché incremental y después de una actualización del conjunto de aptitudes, el indexador reutilizará los enriquecimientos que no se hayan visto afectados por el cambio del conjunto de aptitudes. Los enriquecimientos ascendentes se extraen de la memoria caché, al igual que los enriquecimientos independientes y aislados de la aptitud modificada.

### <a name="deletions"></a>Eliminaciones

Aunque un indexador crea y actualiza estructuras y contenido en Azure Storage, no los elimina. Las proyecciones siguen existiendo incluso cuando se elimina el indexador o el conjunto de aptitudes. Como propietario de la cuenta de almacenamiento, debe eliminar una proyección si ya no es necesaria. 

## <a name="next-steps"></a>Pasos siguientes

El almacén de conocimiento ofrece persistencia de documentos enriquecidos, lo cual resulta útil al diseñar un conjunto de aptitudes o durante la creación de nuevas estructuras y contenido para su consumo por parte de cualquier aplicación cliente capaz de acceder a una cuenta de Azure Storage.

El enfoque más sencillo para crear documentos enriquecidos es [mediante el portal](knowledge-store-create-portal.md), pero también puede usar Postman y la API de REST, que resulta más útil si desea obtener conclusiones sobre cómo se crean objetos y se hace referencia a estos mediante programación.

> [!div class="nextstepaction"]
> [Creación de un almacén de conocimiento mediante Postman y REST](knowledge-store-create-rest.md)

O bien, observe más detenidamente las [proyecciones](knowledge-store-projection-overview.md). Para ver un ejemplo que muestre los conceptos de proyecciones avanzadas, como la segmentación, el modelado insertado y las relaciones, empiece con [Definición de proyecciones en un almacén de conocimiento](knowledge-store-projections-examples.md).
