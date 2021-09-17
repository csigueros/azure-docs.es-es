---
title: Conceptos del almacén de conocimiento
titleSuffix: Azure Cognitive Search
description: Envíe documentos enriquecidos a Azure Storage. En esta solución podrá cambiar ver y consumir documentos enriquecidos en Azure Cognitive Search y otras aplicaciones, además de cambiarle la forma.
author: HeidiSteen
manager: nitinme
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 09/02/2021
ms.openlocfilehash: 07a4d0b7b932b92307b77420df1a21027df1efb0
ms.sourcegitcommit: f2d0e1e91a6c345858d3c21b387b15e3b1fa8b4c
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/07/2021
ms.locfileid: "123538064"
---
# <a name="knowledge-store-in-azure-cognitive-search"></a>Almacén de conocimiento en Azure Cognitive Search

El almacén de conocimiento es una característica de Azure Cognitive Search que envía la salida de una [canalización de enriquecimiento con IA](cognitive-search-concept-intro.md) a tablas y blobs en Azure Storage para realizar análisis independientes o procesos de bajada.

Si ha usado aptitudes cognitivas en el pasado, ya sabe que los *conjuntos de aptitudes* mueven un documento a través de una secuencia de enriquecimientos que invocan transformaciones atómicas, como el reconocimiento de entidades o la traducción de texto. El resultado puede ser un índice de búsqueda o proyecciones en un almacén de conocimientos. Las dos salidas, el índice de búsqueda y el almacén de conocimiento son productos de la misma canalización mutuamente excluyentes. Aunque se obtienen de las mismas entradas, se genera una salida estructurada y almacenada que se emplea en aplicaciones diferentes.

:::image type="content" source="media/knowledge-store-concept-intro/knowledge-store-concept-intro.svg" alt-text="Canalización con conjunto de aptitudes" border="false":::

Físicamente, un almacén de conocimiento es [Azure Storage](../storage/common/storage-account-overview.md), ya sea Azure Table Storage, Azure Blob Storage o ambos. Cualquier herramienta o proceso que pueda conectarse a Azure Storage puede consumir el contenido de un almacén de conocimiento.

Visto a través del Explorador de Storage, un almacén de conocimiento es simplemente una colección de tablas, objetos o archivos. En el ejemplo siguiente se muestra un almacén de conocimiento compuesto por tres tablas con campos que se arrastran desde el origen de datos o se crean mediante enriquecimientos (vea "sentiment score" y "translated_text").

:::image type="content" source="media/knowledge-store-concept-intro/kstore-in-storage-explorer.png" alt-text="Lectura y escritura de aptitudes desde el árbol de enriquecimiento" border="true":::

## <a name="benefits-of-knowledge-store"></a>Ventajas de Knowledge Store

Las principales ventajas de un almacén de conocimiento son dos: el acceso flexible al contenido y la capacidad de dar forma a los datos.

A diferencia de un índice de búsqueda al que solo se puede acceder a través de consultas de Cognitive Search, cualquier herramienta, aplicación o proceso que admita conexiones a Azure Storage puede acceder a un almacén de conocimiento. Esta flexibilidad abre nuevos escenarios para consumir el contenido analizado y enriquecido generado por una canalización de enriquecimiento.

El mismo conjunto de aptitudes que enriquece los datos también se puede usar para dar forma a los datos. Algunas herramientas como Power BI funcionan mejor con tablas, mientras que una carga de trabajo de ciencia de datos podría requerir una estructura de datos compleja en un formato de blob. La adición de una [aptitud de Conformador](cognitive-search-skill-shaper.md) a un conjunto de aptitudes le proporciona control sobre la forma de los datos. A continuación, puede pasar estas formas a proyecciones, ya sean tablas o blobs, para crear estructuras de datos físicos que se alineen con el uso previsto de los datos.

En el vídeo siguiente se explican estas ventajas y mucho más.

> [!VIDEO https://www.youtube.com/embed/XWzLBP8iWqg?version=3]

## <a name="knowledge-store-definition"></a>Definición del almacén de conocimiento

Un almacén de conocimiento se define dentro de una definición de conjunto de aptitudes y consta de dos componentes: 

+ una cadena de conexión a Azure Storage

+ [**Proyecciones**](knowledge-store-projection-overview.md) que determinan si el almacén de conocimiento consta de tablas, objetos o archivos. 

El elemento proyecciones es una matriz. Puede crear varios conjuntos de combinaciones de tabla-objeto-archivo dentro de un almacén de conocimiento.

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

+ `files` proyecta los archivos de imagen en Blob Storage. Un elemento `file` es una imagen extraída de un documento, que se transfiere sin cambios al almacenamiento de blobs. Aunque se denomina "archivos", se muestra en Blob Storage, no en el almacenamiento de archivos.

## <a name="create-a-knowledge-store"></a>Crear un almacén de conocimientos

Para crear el almacén de conocimiento, use el portal o una API. Necesitará [Azure Storage](../storage/index.yml), un [conjunto de aptitudes](cognitive-search-working-with-skillsets.md) y un [indexador](search-indexer-overview.md). Dado que los indexadores requieren un índice de búsqueda, también deberá proporcionar una definición de índice.

Opte por el enfoque del portal para obtener la ruta más rápida hacia un almacén de conocimiento terminado. O bien, elija la API de REST para comprender mejor cómo se definen y se relacionan los objetos.

### <a name="azure-portal"></a>[**Azure Portal**](#tab/kstore-portal)

[**Cree su primer almacén de conocimiento en cuatro pasos**](knowledge-store-create-portal.md) mediante el asistente para **importación de datos**.

1. Definición de un origen de datos

1. Defina el conjunto de aptitudes y especifique un almacén de conocimiento.

1. Defina un esquema de índice. El asistente lo necesita y puede inferir uno automáticamente.

1. Ejecute el asistente. La extracción, el enriquecimiento y el almacenamiento se producen en este último paso.

El asistente automatiza las tareas que, de lo contrario, tendría que controlar manualmente. Específicamente, tanto el modelado como las proyecciones (definiciones de estructuras de datos físicas en Azure Storage) se crean automáticamente. 

### <a name="rest"></a>[**REST**](#tab/kstore-rest)

El tutorial [**Create your first knowledge store using Postman**](knowledge-store-create-rest.md) (Cree su primer almacén de conocimiento mediante Postman) le guía por los objetos y las solicitudes que pertenecen a esta [colección del almacén de conocimiento](https://github.com/Azure-Samples/azure-search-postman-samples/tree/master/knowledge-store).

La versión de la API de REST `2020-06-30` se puede usar para crear un almacén de conocimiento mediante adiciones a un conjunto de aptitudes.

+ [Creación de un conjunto de aptitudes (api-version=2020-06-30)](/rest/api/searchservice/create-skillset)
+ [Actualización de un conjunto de aptitudes (api-version=2020-06-30)](/rest/api/searchservice/update-skillset)

Dentro del conjunto de aptitudes:

+ Especificación de las proyecciones que desea que se creen en Azure Storage (tablas, objetos, archivos)
+ Inclusión de una aptitud Conformador en el conjunto de aptitudes para determinar el esquema y el contenido de la proyección
+ Asignación de la forma con nombre a una proyección

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
