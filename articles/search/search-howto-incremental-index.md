---
title: Habilitación del almacenamiento en caché para el enriquecimiento incremental (versión preliminar)
titleSuffix: Azure Cognitive Search
description: Habilite el almacenamiento en caché de contenido enriquecido para su posible reutilización al modificar las aptitudes y proyecciones de nivel inferior en una canalización de enriquecimiento con IA.
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 10/15/2021
ms.openlocfilehash: b905517a2558eb6bc01b4ba218f0ecde7e830ebf
ms.sourcegitcommit: 5361d9fe40d5c00f19409649e5e8fed660ba4800
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/18/2021
ms.locfileid: "130138589"
---
# <a name="enable-caching-for-incremental-enrichment-in-azure-cognitive-search"></a>Habilitación del almacenamiento en caché para el enriquecimiento incremental en Azure Cognitive Search

> [!IMPORTANT] 
> Esta característica se encuentra en versión preliminar pública en los [Términos de uso complementarios](https://azure.microsoft.com/support/legal/preview-supplemental-terms/). La [API REST de versión preliminar](/rest/api/searchservice/index-preview) admite esta característica.

En este artículo se explica cómo agregar almacenamiento en caché a una canalización de enriquecimiento para que pueda modificar los pasos de enriquecimiento de niveles inferiores sin tener que volver a compilarlo todo cada vez. De forma predeterminada, un conjunto de aptitudes no tiene estado y el cambio de cualquier parte de su composición requiere una nueva ejecución completa del indexador. Con el [**enriquecimiento incremental**](cognitive-search-incremental-indexing-conceptual.md), el indexador puede determinar qué partes del árbol del documento deben actualizarse en función de los cambios detectados en las definiciones del conjunto de aptitudes o el indexador. La salida procesada existente se conserva y se reutiliza siempre que sea posible. 

El contenido almacenado en caché se coloca en Azure Storage con la información de la cuenta proporcionada. El contenedor, denominado `ms-az-search-indexercache-<alpha-numerc-string>`, se crea al ejecutar el indexador. Debe considerarse como un componente interno administrado por el servicio de búsqueda y no debe modificarse.

Si no está familiarizado con la configuración de indexadores, comience con la [información general sobre el indexador](search-indexer-overview.md) y, después, continúe con los [conjuntos de aptitudes](cognitive-search-working-with-skillsets.md) para obtener información sobre las canalizaciones de enriquecimiento. Para obtener más información sobre los conceptos clave, vea el tema sobre el [enriquecimiento incremental](cognitive-search-incremental-indexing-conceptual.md).

## <a name="prerequisites"></a>Requisitos previos

Azure Storage se usa para almacenar enriquecimientos almacenados en caché. La cuenta de almacenamiento debe ser [de uso general v2](../storage/common/storage-account-overview.md#types-of-storage-accounts).

Las API de versión preliminar o los SDK de Azure beta son necesarios para habilitar la caché en un indexador. Actualmente, el portal no proporciona ninguna opción para almacenar en caché el enriquecimiento.

## <a name="enable-on-new-indexers"></a>Habilitación en nuevos indizadores

En los nuevos indizadores, agregue la propiedad "cache" en la carga de definición del indexador al llamar al [indizador de creación o actualización (2021-04-30-Preview)](/rest/api/searchservice/preview-api/create-or-update-indexer). También puede usar la versión preliminar anterior de la API: 2020-06-30-Preview.

```https
POST https://[service name].search.windows.net/indexers?api-version=2021-04-30-Preview
    {
        "name": "<YOUR-INDEXER-NAME>",
        "targetIndexName": "<YOUR-INDEX-NAME>",
        "dataSourceName": "<YOUR-DATASOURCE-NAME>",
        "skillsetName": "<YOUR-SKILLSET-NAME>",
        "cache" : {
            "storageConnectionString" : "<YOUR-STORAGE-ACCOUNT-CONNECTION-STRING>",
            "enableReprocessing": true
        },
        "fieldMappings" : [],
        "outputFieldMappings": [],
        "parameters": []
        }
    }
```

## <a name="enable-on-existing-indexers"></a>Habilitación en indizadores existentes

Para los indizadores existentes que ya tienen un conjunto de aptitudes, siga estos pasos para agregar el almacenamiento en caché. Como operación única, restablezca y vuelva a ejecutar el indizador en su totalidad para cargar la memoria caché.

### <a name="step-1-get-the-indexer-definition"></a>Paso 1: Obtención de la definición del indexador

Comience con un indizador de trabajo válido que tenga estos componentes: origen de datos, conjunto de aptitudes, índice. Use un cliente de API para enviar una solicitud [GET Indexer](/rest/api/searchservice/get-indexer) para recuperar el indizador. Cuando se usa la versión preliminar de la API para obtener el indizador, se agrega a la definición una propiedad “cache” definida como null automáticamente.

```http
GET https://[YOUR-SEARCH-SERVICE].search.windows.net/indexers/[YOUR-INDEXER-NAME]?api-version=2021-04-30-Preview
    Content-Type: application/json
    api-key: [YOUR-ADMIN-KEY]
```

### <a name="step-2-set-the-cache-property"></a>Paso 2: Definir la propiedad de caché

En la definición del índice, modifique "cache" para incluir las siguientes propiedades obligatorias y opcionales:

+ (Obligatorio) `storageConnectionString` debe establecerse en una cadena de conexión de Azure Storage.
+ (Opcional) La propiedad booleana `enableReprocessing` (`true` de forma predeterminada) indica que el enriquecimiento incremental está habilitado. Puede establecerla en `false` para suspender el procesamiento incremental mientras se están llevando a cabo otras operaciones con un uso intensivo de recursos, como la indexación de nuevos documentos, y, a continuación, volver a ponerla en `true` más adelante.

```http
POST https://[service name].search.windows.net/indexers?api-version=2021-04-30-Preview
    {
        "name": "<YOUR-INDEXER-NAME>",
        "targetIndexName": "<YOUR-INDEX-NAME>",
        "dataSourceName": "<YOUR-DATASOURCE-NAME>",
        "skillsetName": "<YOUR-SKILLSET-NAME>",
        "cache" : {
            "storageConnectionString" : "<YOUR-STORAGE-ACCOUNT-CONNECTION-STRING>",
            "enableReprocessing": true
        },
        "fieldMappings" : [],
        "outputFieldMappings": [],
        "parameters": []
    }
```

### <a name="step-3-reset-the-indexer"></a>Paso 3: Restablecimiento del indizador

El [restablecimiento del indizador](/rest/api/searchservice/reset-indexer) se requiere al configurar el enriquecimiento incremental de los indizadores existentes para asegurarse de que todos los documentos se encuentran en un estado coherente. Puede usar el portal o un cliente de API para esta tarea.

```https
POST https://[YOUR-SEARCH-SERVICE].search.windows.net/indexers/[YOUR-INDEXER-NAME]/reset?api-version=2021-04-30-Preview
    Content-Type: application/json
    api-key: [YOUR-ADMIN-KEY]
```

### <a name="step-4-save-the-indexer"></a>Paso 4: Guardar el indizador

[Actualice el indizador (2021-04-30-Preview)](/rest/api/searchservice/preview-api/create-or-update-indexer) con una solicitud PUT en que el cuerpo de la solicitud incluya "cache".

```http
PUT https://[YOUR-SEARCH-SERVICE].search.windows.net/indexers/[YOUR-INDEXER-NAME]?api-version=2021-04-30-Preview
    Content-Type: application/json
    api-key: [YOUR-ADMIN-KEY]
    {
        "name" : "<YOUR-INDEXER-NAME>",
        ...
        "cache": {
            "storageConnectionString": "<YOUR-STORAGE-ACCOUNT-CONNECTION-STRING>",
            "enableReprocessing": true
        }
    }
```

Si ahora emite otra solicitud GET en el indexador, la respuesta del servicio incluirá una propiedad `ID` en el objeto de caché. La cadena alfanumérica se anexa al nombre del contenedor que incluirá todos los resultados almacenados en caché y el estado intermedio de cada documento procesado por este indexador. El identificador se usará para asignar el nombre único a la memoria caché en Blob Storage.

```http
    "cache": {
        "ID": "<ALPHA-NUMERIC STRING>",
        "enableReprocessing": true,
        "storageConnectionString": "DefaultEndpointsProtocol=https;AccountName=<YOUR-STORAGE-ACCOUNT>;AccountKey=<YOUR-STORAGE-KEY>;EndpointSuffix=core.windows.net"
    }
```

### <a name="step-5-run-the-indexer"></a>Paso 5: Ejecución del indexador

Para ejecutar el indexador, puede usar el portal o la API. En la lista de indexadores del portal, seleccione el indexador y haga clic en **Ejecutar**. Una ventaja de usar el portal es que puede supervisar el estado del indexador y anotar la duración del trabajo y el número de documentos que se procesan. Las páginas del portal se actualizan cada pocos minutos.

Como alternativa, puede usar REST para [ejecutar el indexador](/rest/api/searchservice/run-indexer):

```http
POST https://[YOUR-SEARCH-SERVICE].search.windows.net/indexers/[YOUR-INDEXER-NAME]/run?api-version=2020-06-30-Preview
Content-Type: application/json
api-key: [YOUR-ADMIN-KEY]
```

> [!NOTE]
> Restablecer y volver a ejecutar el indexador produce una recompilación completa para que se pueda almacenar en caché el contenido. Todos los enriquecimientos cognitivos se volverán a ejecutar en todos los documentos. La reutilización del contenido enriquecido de la memoria caché empieza después de cargar la memoria caché.
>

## <a name="check-for-cached-output"></a>Comprobación de la salida almacenada en caché

Busque la caché en Azure Storage, en Contenedor de blobs. El nombre del contenedor será `ms-az-search-indexercache-<some-alphanumeric-string>`.

Un indizador crea y usa una memoria caché. Su contenido no es apto para lectura humana.

Para comprobar si la memoria caché está operativa, modifique un conjunto de aptitudes y ejecute el indizador. A continuación, compare las métricas antes y después del tiempo de ejecución y los recuentos de documentos. 

Los conjuntos de aptitudes que incluyen el análisis de imágenes y el reconocimiento óptico de caracteres (OCR) de documentos digitalizados son buenos casos de prueba. Si modifica una aptitud de texto de nivel inferior o cualquier aptitud que no esté relacionada con la imagen, el indizador puede recuperar la imagen procesada previamente y el contenido de OCR de la memoria caché para actualizar y procesar solo los cambios relacionados con el texto indicados en las ediciones.  Normalmente, obtendrá menos documentos en el recuento de documentos de ejecución del indizador, tiempos de ejecución más cortos y menos cargos en la factura. 

El [conjunto de archivos](https://github.com/Azure-Samples/azure-search-sample-data/tree/master/ai-enrichment-mixed-media) que se usa en los [tutoriales cog-search-demo](cognitive-search-tutorial-blob.md) es un caso de prueba útil porque contiene 14 archivos de varios formatos JPG, PNG, HTML, DOCX, PPTX y de otros tipos. Cambie `en` a `es` o a otro idioma en la aptitud de traducción de texto para realizar pruebas de concepto del enriquecimiento incremental.

## <a name="common-errors"></a>Errores comunes

El siguiente error se produce si olvida especificar una versión preliminar de la API en la solicitud:

`"The request is invalid. Details: indexer : A resource without a type name was found, but no expected type was specified. To allow entries without type information, the expected type must also be specified when the model is specified."`

También se producirá un error 400 Solicitud incorrecta si falta un requisito del indizador. En el mensaje de error se especificarán las dependencias que faltan.

## <a name="next-steps"></a>Pasos siguientes

El enriquecimiento incremental se aplica a los indexadores que contienen conjuntos de aptitudes, lo que proporciona contenido reutilizable para los índices y los almacenes de conocimiento. Los siguientes vínculos proporcionan más información acerca del almacenamiento en caché y los conjuntos de aptitudes.

+ [Enriquecimiento incremental (ciclo de vida y administración)](cognitive-search-incremental-indexing-conceptual.md)
+ [Conceptos de conjunto de aptitudes y composición](cognitive-search-working-with-skillsets.md)
+ [Creación de un conjunto de aptitudes](cognitive-search-defining-skillset.md)
+ [Tutorial: Uso de REST y AI para generar contenido en el que se pueden realizar búsquedas desde blobs de Azure](cognitive-search-tutorial-blob.md)