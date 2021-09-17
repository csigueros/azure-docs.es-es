---
title: Creación de un almacén de conocimiento mediante REST
titleSuffix: Azure Cognitive Search
description: Use la API REST y Postman para crear un almacén de conocimiento de Azure Cognitive Search y conservar los enriquecimientos de IA del conjunto de aptitudes.
author: HeidiSteen
manager: nitinme
ms.author: heidist
ms.service: cognitive-search
ms.topic: tutorial
ms.date: 09/02/2021
ms.openlocfilehash: acad54727ecb441cce64021ecba395d41820659a
ms.sourcegitcommit: f2d0e1e91a6c345858d3c21b387b15e3b1fa8b4c
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/07/2021
ms.locfileid: "123535276"
---
# <a name="create-a-knowledge-store-using-rest-and-postman"></a>Creación de un almacén de conocimiento mediante REST y Postman

El almacén de conocimiento es una característica de Azure Cognitive Search que envía la salida de un conjunto de aptitudes desde una [canalización de enriquecimiento con IA](cognitive-search-concept-intro.md) hacia Azure Storage para la subsiguiente minería de conocimientos, análisis de datos o procesamiento posterior. Después de completar un almacén de conocimiento, puede usar herramientas como el [Explorador de Storage](knowledge-store-view-storage-explorer.md) o [Power BI](knowledge-store-connect-power-bi.md) para explorar el contenido.

En este artículo, usará la API REST para ingerir, enriquecer y explorar un conjunto de reseñas de clientes sobre estancias en hotel en un almacén de conocimiento de Azure Storage. El resultado final es un almacén de conocimiento incluye contenido de texto original que se extrajo del origen, además de contenido generado por IA que incluye una puntuación de opinión, la extracción de frases clave, la detección del idioma y la traducción de texto de comentarios de clientes que no están en inglés.

Para que el conjunto de datos inicial esté disponible, las reseñas de hotel se importan primero a Azure Blob Storage. Después del procesamiento, los resultados se guardan como almacén de conocimiento en Azure Table Storage.

> [!NOTE]
> En este artículo se supone que se usa la [aplicación de escritorio Postman](https://www.getpostman.com/). El [código fuente](https://github.com/Azure-Samples/azure-search-postman-samples/tree/master/knowledge-store) de este artículo incluye una recopilación de Postman que contiene todas las solicitudes. 

## <a name="create-services-and-load-data"></a>Creación de servicios y carga de datos

Este ejercicio utiliza Azure Cognitive Search, Azure Blob Storage y [Azure Cognitive Services](https://azure.microsoft.com/services/cognitive-services/) para IA. 

Dado que la carga de trabajo es tan pequeña, Cognitive Services se aprovecha en segundo plano para proporcionar el procesamiento gratuito de hasta 20 transacciones al día. Una carga de trabajo pequeña significa que puede omitir la creación o la asociación de un recurso de Cognitive Services.

1. [Descarga de HotelReviews_Free.csv](https://knowledgestoredemo.blob.core.windows.net/hotel-reviews/HotelReviews_Free.csv?sp=r&st=2019-11-04T01:23:53Z&se=2025-11-04T16:00:00Z&spr=https&sv=2019-02-02&sr=b&sig=siQgWOnI%2FDamhwOgxmj11qwBqqtKMaztQKFNqWx00AY%3D). Estos datos son las reseñas del hotel guardadas en un archivo CSV (procede de Kaggle.com) y contiene 19 fragmentos de comentarios de clientes sobre un solo hotel. 

1. [Cree una cuenta de Azure Storage](../storage/common/storage-account-create.md?tabs=azure-portal) o [busque una cuenta existente](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Storage%2storageAccounts/). Usará Azure Storage tanto para el contenido sin procesar que se va a importar como para el almacén de información que es el resultado final.

   Elija el tipo de cuenta **StorageV2 (uso general V2)** .

1. En el recurso de Azure Storage, use el **Explorador de Storage** para crear un contenedor de blobs denominado **hotel-reviews**.

1. Seleccione **Cargar** en la parte superior de la página para cargar el archivo **HotelReviews-Free.csv** que descargó del paso anterior.

   :::image type="content" source="media/knowledge-store-create-portal/blob-container-storage-explorer.png" alt-text="Captura de pantalla del Explorador de Storage con el archivo cargado y el panel de navegación izquierdo" border="true":::

1. Casi ha terminado con este recurso, pero antes de salir de estas páginas, seleccione **Claves de acceso** en el panel de navegación izquierdo para obtener una cadena de conexión para poder recuperar estos datos mediante el indexador.

1. En **Claves de acceso**, seleccione **Mostrar claves** en la parte superior de la página para mostrar las cadenas de conexión y, luego, copie la cadena de conexión para key1 o key2.

   Una cadena de conexión tiene el formato siguiente: `DefaultEndpointsProtocol=https;AccountName=<YOUR-ACCOUNT-NAME>;AccountKey=<YOUR-ACCOUNT-KEY>;EndpointSuffix=core.windows.net`

## <a name="configure-requests"></a>Configuración de solicitudes

1. Descargue [azure-search-postman-samples](https://github.com/Azure-Samples/azure-search-postman-samples) de GitHub y descomprima el archivo. Hay varias colecciones en el repositorio. Usará la colección de la carpeta knowledge-store.

1. En Postman, seleccione **File** (Archivo) > **Import** (Importar) para importar el archivo KnowledgeStore.postman_collection.json.

1. Seleccione la pestaña **Recopilaciones** el botón **...** (puntos suspensivos).

1. Seleccione **Editar**.

   ![Aplicación Postman que muestra la navegación](media/knowledge-store-create-rest/postman-edit-menu.png "Ir al menú de edición de Postman")

1. En el cuadro de diálogo **Edit** (Editar), seleccione la pestaña **Variables** (Variables). 

En la pestaña **Variables** (Variables) puede agregar valores para los intercambios de Postman cada vez que encuentre una variable específica entre llaves dobles. Por ejemplo, Postman reemplaza el símbolo `{{admin-key}}` por el valor actual que estableciera para `admin-key`. Postman realiza esta sustitución en las direcciones URL, los encabezados, el cuerpo de la solicitud, etc. 

Las variables se definen para los servicios de Azure, las conexiones de servicio y los nombres de objeto. Reemplace los valores de marcador de posición de servicios y conexiones por valores reales para su servicio de búsqueda y cuenta de almacenamiento. También puede encontrar estos valores en Azure Portal.

+ Para obtener los valores de `search-service-name` y `search-service-admin-key`, vaya al servicio Azure Cognitive Search en el portal y copie los valores de las páginas **Información general** y **Claves**.

+ Para obtener los valores de `storage-account-name` y `storage-account-connection-string`, compruebe la página **Claves de acceso**.

![Pestaña de variables de la aplicación Postman](media/knowledge-store-create-rest/postman-variables-window.png "Ventana de variables de Postman")

| Variable    | Dónde obtenerla |
|-------------|-----------------|
| `admin-key` | En la página **Claves** del servicio Azure Cognitive Search.  |
| `api-version` | Déjela como **2020-06-30**. |
| `datasource-name` | Déjela como **hotel-reviews-ds**. | 
| `indexer-name` | Déjela como **hotel-reviews-ixr**. | 
| `index-name` | Déjela como **hotel-reviews-ix**. | 
| `search-service-name` | Nombre del servicio Azure Cognitive Search. Si la dirección URL es `https://mySearchService.search.windows.net`, el valor que debe especificar es `mySearchService`. | 
| `skillset-name` | Déjela como **hotel-reviews-ss**. | 
| `storage-account-name` | El nombre de la cuenta de Azure Storage. | 
| `storage-connection-string` | En la cuenta de almacenamiento, en la pestaña **Claves de acceso**, seleccione **Mostrar clave** de la parte superior de la página, y, luego, copie **key1** > **Cadena de conexión**. | 
| `storage-container-name` | Déjela como **hotel-reviews**. | 

### <a name="review-the-request-collection-in-postman"></a>Revisión de la colección de solicitudes en Postman

Los almacenes de conocimiento se definen en conjuntos de aptitudes que, a su vez, se adjuntan a los indexadores. La creación de un almacén de conocimiento requiere que cree todos los objetos ascendentes, incluido un índice, un origen de datos, un conjunto de aptitudes y un indexador. Aunque un índice no está relacionado con un almacén de conocimiento, un indexador lo requiere para su ejecución, por lo que creará uno como requisito previo del indexador.

Al crear un almacén de conocimiento, tiene que emitir cuatro solicitudes HTTP: 

+ **Solicitud PUT para crear el índice**: Este índice contendrá datos de búsqueda devueltos en las solicitudes de consulta emitidas al servicio de búsqueda.

+ **Solicitud POST para crear el origen de datos**: Este origen de datos brinda información de conexión al indexador. El indexador se conecta a la cuenta de Azure Storage para recuperar los datos de ejemplo.

+ **Solicitud PUT para crear el conjunto de aptitudes**: El conjunto de aptitudes especifica los enriquecimientos que se aplican a los datos. También especifica la estructura del almacén de conocimiento.

+ **Solicitud PUT para crear el indexador**: Al ejecutar el indexador se leen los datos, se aplica el conjunto de aptitudes, se crea el almacén de conocimiento en Azure Storage y se guarda el resultado. Debe ejecutar esta solicitud en último lugar.

Las variables que configuró anteriormente se usan en los encabezados y en la dirección URL. En la captura de pantalla siguiente para la solicitud Crear índice se muestra dónde aparecen estas variables en la solicitud.

![Captura de pantalla que muestra la interfaz de Postman para los encabezados](media/knowledge-store-create-rest/postman-headers-ui.png)

> [!Note]
> Todas las solicitudes del conjunto de recopilación `api-key` y encabezados `Content-type`, que son obligatorios. Si Postman reconoce una variable, esta aparece en texto naranja, como `{{admin-key}}` en la captura de pantalla anterior. Si la variable está mal escrita, aparece en texto rojo.
>

## <a name="create-an-index"></a>Creación de un índice

Use [Create Index (REST API)](/rest/api/searchservice/create-index) (Crear índice [API REST]) para crear un índice de búsqueda en el servicio de búsqueda. Un índice de búsqueda no está relacionado con un almacén de conocimiento, pero el indexador requiere que se cree uno. El índice de búsqueda tendrá el mismo contenido que el almacén de conocimiento. Si busca un enfoque alternativo para explorar el contenido, puede consultar este índice mediante el envío de solicitudes de consulta al servicio de búsqueda. 

Emita una solicitud PUT a `https://{{search-service-name}}.search.windows.net/indexes/{{index-name}}?api-version={{api-version}}` para crear el índice. El esquema del índice se proporciona en el cuerpo de la solicitud.

```JSON
{
    "name": "{{index-name}}",
    "fields": [
        { "name": "name", "type": "Edm.String", "filterable": false, "sortable": false, "facetable": false },
        { "name": "reviews_date", "type": "Edm.DateTimeOffset", "searchable": false, "filterable": false, "sortable": false, "facetable": false },
        { "name": "reviews_rating", "type": "Edm.String", "searchable": false, "filterable": false, "sortable": false, "facetable": false },
        { "name": "reviews_text", "type": "Edm.String", "filterable": false,  "sortable": false, "facetable": false },
        { "name": "reviews_title", "type": "Edm.String", "searchable": false, "filterable": false, "sortable": false, "facetable": false },
        { "name": "reviews_username", "type": "Edm.String", "searchable": false, "filterable": false, "sortable": false, "facetable": false },
        { "name": "AzureSearch_DocumentKey", "type": "Edm.String", "searchable": false, "filterable": false, "sortable": false, "facetable": false, "key": true },
        { "name": "language", "type": "Edm.String", "filterable": true, "sortable": false, "facetable": true },
        { "name": "translated_text", "type": "Edm.String", "filterable": false, "sortable": false, "facetable": false },
        { "name": "sentiment", "type": "Collection(Edm.String)", "searchable": false, "filterable": true, "retrievable": true, "sortable": false, "facetable": true },
        { "name": "keyphrases", "type": "Collection(Edm.String)", "filterable": true, "sortable": false, "facetable": true }
    ]
}
```

Esta definición del índice es una combinación de los datos que le gustaría presentar al usuario. Incluye campos asignados directamente al archivo CSV (nombre del hotel, fecha de la reseña, etc.) y campos creados por el conjunto de aptitudes (opinión, frases clave, idioma y texto traducido).

Seleccione **Enviar** para emitir la solicitud PUT.

Debería ver el estado `201 - Created`. Si ve un estado diferente, en el panel **Body** (Cuerpo), busque una respuesta JSON que contenga un mensaje de error. Si usa un servicio de búsqueda gratuito, asegúrese de tener espacio para los nuevos objetos (el servicio gratuito tiene un máximo de tres).

En este momento, el índice se ha creado, pero no está cargado. La importación de documentos se produce más adelante, cuando se ejecuta el indexador. 

## <a name="create-a-data-source"></a>Creación de un origen de datos

A continuación, conecte Azure Cognitive Search con los datos de hotel que almacenó en Blob Storage. Para crear el origen de datos, envíe una solicitud POST [Create Data Source](/rest/api/searchservice/create-data-source) (Crear origen de datos) a `https://{{search-service-name}}.search.windows.net/datasources?api-version={{api-version}}`. 

En Postman, vaya a la solicitud **Create Datasource** (Crear origen de datos) y al panel **Body** (Cuerpo). Debería ver el código siguiente:

```json
{
  "name" : "{{datasource-name}}",
  "description" : "Demo files to demonstrate knowledge store capabilities.",
  "type" : "azureblob",
  "credentials" : { "connectionString" : "{{storage-connection-string}}" },
  "container" : { "name" : "{{storage-container-name}}" }
}
```

Seleccione **Enviar** para emitir la solicitud POST. 

## <a name="create-a-skillset"></a>Creación de un conjunto de aptitudes 

En el siguiente paso se crea el conjunto de aptitudes, que especifica las mejoras que se van a aplicar y el almacén de conocimiento donde se almacenarán los resultados. Esta solicitud envía una solicitud PUT [Create Skillset](/rest/api/searchservice/create-skillset) (Crear conjunto de aptitudes) a `https://{{search-service-name}}.search.windows.net/skillsets/{{skillset-name}}?api-version={{api-version}}`.

Hay dos grandes objetos de nivel superior: `skills` y `knowledgeStore`:

+ "skills" es el conjunto de aptitudes. Cada objeto del objeto `skills` es un servicio de enriquecimiento. Cada servicio de enriquecimiento tiene `inputs` y `outputs`. Observe cómo `LanguageDetectionSkill` tiene una salida `targetName` de `Language`. El valor de este nodo se usa en la mayoría de las demás aptitudes como entrada. El origen es `document/Language`. Esta capacidad de utilizar la salida de un nodo como entrada de otro es incluso más evidente en `ShaperSkill`, que especifica cómo fluyen los datos en las tablas del almacén de conocimiento.

+ "knowledgeStore" incluye la cadena de conexión a la cuenta de almacenamiento y una serie de proyecciones. Cada elemento de proyección de la matriz de proyecciones da como resultado una tabla en Azure Storage. Las columnas de la tabla incluirán columnas generadas que se usan para la vinculación cruzada y los campos de contenido. También incluirán campos de contenido creados durante el enriquecimiento.

  Las proyecciones aceptan formas de datos como entrada. Puede indicar las formas a través de una aptitud de conformador, asignar la salida de la aptitud a una proyección, o definir formas insertadas en cada elemento de proyección. 

Para generar el conjunto de aptitudes, seleccione el botón **Send** (Enviar) de Postman para colocar la solicitud PUT:

```json
{
    "name": "{{skillset-name}}",
    "description": "Skillset to detect language, translate text, extract key phrases, and score sentiment",
    "skills": [ 
        {
            "@odata.type": "#Microsoft.Skills.Text.SplitSkill", 
            "context": "/document/reviews_text", "textSplitMode": "pages", "maximumPageLength": 5000,
            "inputs": [ 
                { "name": "text", "source": "/document/reviews_text" }
            ],
            "outputs": [
                { "name": "textItems", "targetName": "pages" }
            ]
        },
        {
            "@odata.type": "#Microsoft.Skills.Text.V3.SentimentSkill",
            "context": "/document/reviews_text/pages/*",
            "inputs": [
                { "name": "text", "source": "/document/reviews_text/pages/*" },
                { "name": "languageCode", "source": "/document/language" }
            ],
            "outputs": [
                { "name": "sentiment", "targetName": "sentiment" }
            ]
        },
        {
            "@odata.type": "#Microsoft.Skills.Text.LanguageDetectionSkill",
            "context": "/document",
            "inputs": [
                { "name": "text", "source": "/document/reviews_text" }
            ],
            "outputs": [
                { "name": "languageCode", "targetName": "language" }
            ]
        },
        {
            "@odata.type": "#Microsoft.Skills.Text.TranslationSkill",
            "context": "/document/reviews_text/pages/*",
            "defaultFromLanguageCode": null,
            "defaultToLanguageCode": "en",
            "inputs": [
                { "name": "text", "source": "/document/reviews_text/pages/*" }
            ],
            "outputs": [
                { "name": "translatedText", "targetName": "translated_text" }
            ]
        },
        {
            "@odata.type": "#Microsoft.Skills.Text.KeyPhraseExtractionSkill",
            "context": "/document/reviews_text/pages/*",
            "inputs": [
                { "name": "text",  "source": "/document/reviews_text/pages/*" },
                { "name": "languageCode",  "source": "/document/language" }
            ],
            "outputs": [
                { "name": "keyPhrases" , "targetName": "keyphrases" }
            ]
        },
        {
            "@odata.type": "#Microsoft.Skills.Util.ShaperSkill",
            "context": "/document",
            "inputs": [
                { "name": "name",  "source": "/document/name" },
                { "name": "reviews_date",  "source": "/document/reviews_date" },
                { "name": "reviews_rating",  "source": "/document/reviews_rating" },
                { "name": "reviews_text",  "source": "/document/reviews_text" },
                { "name": "reviews_title",  "source": "/document/reviews_title" },
                { "name": "reviews_username",  "source": "/document/reviews_username" },
                { "name": "AzureSearch_DocumentKey",  "source": "/document/AzureSearch_DocumentKey" },
                {
                "name": "pages",
                "sourceContext": "/document/reviews_text/pages/*",
                "inputs": [
                    {
                    "name": "languageCode",
                    "source": "/document/language"
                    },
                    {
                    "name": "translatedText",
                    "source": "/document/reviews_text/pages/*/translated_text"
                    },
                    { 
                    "name": "sentiment",
                    "source": "/document/reviews_text/pages/*/sentiment"
                    },
                    {
                    "name": "keyPhrases",
                    "source": "/document/reviews_text/pages/*/keyphrases/*"
                    },
                    {
                    "name": "Page",
                    "source": "/document/reviews_text/pages/*"
                    }
                ]
                }
            ],
            "outputs": [
                { "name": "output" , "targetName": "tableprojection" }
            ]
        }
    ],
    "knowledgeStore": {
        "storageConnectionString": "{{storage-connection-string}}",
        "projections": [
            {
                "tables": [
                    { "tableName": "hotelReviews1Document", "generatedKeyName": "Documentid", "source": "/document/tableprojection" },
                    { "tableName": "hotelReviews2Pages", "generatedKeyName": "Pagesid", "source": "/document/tableprojection/pages/*" },
                    { "tableName": "hotelReviews3KeyPhrases", "generatedKeyName": "KeyPhrasesid", "source": "/document/tableprojection/pages/*/keyPhrases/*" }
                ],
                "objects": []
            },
            {
                "tables": [
                    { 
                        "tableName": "hotelReviews4InlineProjectionDocument", "generatedKeyName": "Documentid", "sourceContext": "/document",
                        "inputs": [
                            { "name": "name", "source": "/document/name"},
                            { "name": "reviews_date", "source": "/document/reviews_date"},
                            { "name": "reviews_rating", "source": "/document/reviews_rating"},
                            { "name": "reviews_username", "source": "/document/reviews_username"},
                            { "name": "reviews_title", "source": "/document/reviews_title"},
                            { "name": "reviews_text", "source": "/document/reviews_text"},
                            { "name": "AzureSearch_DocumentKey", "source": "/document/AzureSearch_DocumentKey" }
                        ]
                    },
                    { 
                        "tableName": "hotelReviews5InlineProjectionPages", "generatedKeyName": "Pagesid", "sourceContext": "/document/reviews_text/pages/*",
                        "inputs": [
                            { "name": "Sentiment", "source": "/document/reviews_text/pages/*/sentiment"},
                            { "name": "LanguageCode", "source": "/document/language"},
                            { "name": "Keyphrases", "source": "/document/reviews_text/pages/*/keyphrases"},
                            { "name": "TranslatedText", "source": "/document/reviews_text/pages/*/translated_text"},
                            { "name": "Page", "source": "/document/reviews_text/pages/*" }
                        ]
                    },
                    { 
                        "tableName": "hotelReviews6InlineProjectionKeyPhrases", "generatedKeyName": "kpidv2", "sourceContext": "/document/reviews_text/pages/*/keyphrases/*",
                        "inputs": [
                            { "name": "Keyphrases", "source": "/document/reviews_text/pages/*/keyphrases/*" }
                        ]
                    }
                ],
                "objects": []
            }
        ]
    }
}
```

## <a name="create-an-indexer"></a>Creación de un indexador

El último paso es la solicitud [Create Indexer](/rest/api/searchservice/create-indexer) (Crear indexador). Este lee los datos y activa el conjunto de aptitudes. La definición del indexador hace referencia a otros recursos que ya ha creado: el origen de datos, el índice y el conjunto de aptitudes. 

+ El objeto `parameters/configuration` controla cómo ingiere el indexador los datos. En este caso, los datos de entrada se encuentran en un único archivo .csv con una línea de encabezado y valores separados por comas. 

+ Las asignaciones de campos crean "AzureSearch_DocumentKey", que es un identificador único para cada documento generado por el indexador de blobs (según la ruta de acceso del almacenamiento de metadatos). 

+ Las asignaciones de campos de salida especifican cómo se asignan los campos enriquecidos a los campos de un índice de búsqueda. Las asignaciones de campos de salida no se usan en almacenes de conocimiento (los almacenes de conocimiento usan formas y proyecciones para expresar las estructuras de datos físicas).

Seleccione **Send** (Enviar) en Postman para crear y ejecutar el indexador. La importación de datos, la ejecución del conjunto de aptitudes y la creación del almacén de conocimiento tienen lugar en este paso.

```json
{
    "name": "{{indexer-name}}",
    "dataSourceName": "{{datasource-name}}",
    "skillsetName": "{{skillset-name}}",
    "targetIndexName": "{{index-name}}",
    "parameters": {
        "configuration": {
            "dataToExtract": "contentAndMetadata",
            "parsingMode": "delimitedText",
            "firstLineContainsHeaders": true,
            "delimitedTextDelimiter": ","
        }
    },
    "fieldMappings": [
        {
            "sourceFieldName": "AzureSearch_DocumentKey",
            "targetFieldName": "AzureSearch_DocumentKey",
            "mappingFunction": { "name": "base64Encode" }
        }
    ],
    "outputFieldMappings": [
        { "sourceFieldName": "/document/reviews_text/pages/*/Keyphrases/*", "targetFieldName": "Keyphrases" },
        { "sourceFieldName": "/document/Language", "targetFieldName": "Language" },
        { "sourceFieldName": "/document/reviews_text/pages/*/Sentiment", "targetFieldName": "Sentiment" }
    ]
}
```

## <a name="check-status"></a>Comprobar estado

Después de enviar cada solicitud, el servicio de búsqueda debe responder con un mensaje de confirmación 201. Si recibe errores, vuelva a comprobar las variables y asegúrese de que el servicio de búsqueda tenga espacio para el nuevo índice, el indexador, el origen de datos y el conjunto de aptitudes (el nivel gratis se limita a tres de cada uno).

En Azure Portal, vaya a la página **Información general** del servicio Azure Cognitive Search. Seleccione la pestaña **Indexadores** y **hotel-reviews-ixr**. En un minuto o dos, el estado debería avanzar de "En curso" a "Correcto" sin errores ni advertencias.

## <a name="check-tables-in-storage-explorer"></a>Comprobación de las tablas en el Explorador de Storage

En Azure Portal, cambie a la cuenta de Azure Storage y use el **Explorador de Storage** para ver las nuevas tablas. Debería ver seis tablas, una para cada proyección definida en el conjunto de aptitudes.

Cada tabla se genera con los identificadores necesarios para la vinculación cruzada de las tablas en las consultas. Al abrir una tabla, desplácese más allá de estos campos para ver los campos de contenido que ha agregado la canalización.

   :::image type="content" source="media/knowledge-store-create-rest/knowledge-store-tables.png" alt-text="Captura de pantalla de las tablas del almacén de conocimiento en el Explorador de Storage" border="true":::

En este tutorial, el almacén de conocimiento se compone de varias tablas que muestran diferentes maneras de formar y estructurar una tabla. El primer conjunto de tres muestra tablas que usan la salida de una aptitud de conformador para determinar las columnas y filas. El segundo conjunto muestra comandos de modelado insertados, introducidos dentro de la propia proyección. Puede usar cualquiera de los enfoques para lograr el mismo resultado.

| Tabla | Descripción |
|-------|-------------|
| hotelReviews1Document | Contiene campos que trasladan desde el archivo CSV, como reviews_date y reviews_text. |
| hotelReviews2Pages | Contiene campos enriquecidos creados por el conjunto de aptitudes, como la puntuación de opinión y el texto traducido. |
| hotelReviews3KeyPhrases | Contiene una larga lista con solo las frases clave. |
| hotelReviews4InlineProjectionDocument | Alternativa a la primera tabla, mediante el modelado insertado, en lugar de la aptitud de conformador para dar forma a los datos de la proyección. |
| hotelReviews5InlineProjectionPages | Alternativa a la segunda tabla, mediante el modelado insertado. |
| hotelreviews6InlineProjectionKeyPhrases | Alternativa a la tercera tabla, mediante el modelado insertado. |

## <a name="clean-up"></a>Limpieza

Al trabajar en una suscripción propia, se recomienda identificar al final de un proyecto si aún necesita los recursos creados. Los recursos que se dejan en ejecución pueden costarle mucho dinero. Puede eliminar los recursos de forma individual o eliminar el grupo de recursos para eliminar todo el conjunto de recursos.

Puede encontrar y administrar recursos en el portal, mediante el vínculo **Todos los recursos** o **Grupos de recursos** en el panel de navegación izquierdo.

Si está usando un servicio gratuito, recuerde que está limitado a tres índices, indexadores y orígenes de datos. Puede eliminar elementos individuales en el portal para mantenerse por debajo del límite.

> [!TIP]
> Si desea repetir este ejercicio o realizar otro tutorial de enriquecimiento de inteligencia artificial, elimine el indexador **hotel-reviews-idxr** y los objetos relacionados para volver a crearlos. La eliminación del indexador restablece el contador de transacciones diarias gratis a cero.

## <a name="next-steps"></a>Pasos siguientes

Ahora que ha enriquecido los datos con Cognitive Services y ha proyectado los resultados en un almacén de conocimiento, puede usar el Explorador de Storage u otras aplicaciones para explorar el conjunto de datos enriquecido.

Para aprender a explorar este almacén de conocimiento con el Explorador de Storage, consulte el siguiente tutorial:

> [!div class="nextstepaction"]
> [Visualización con el Explorador de Storage](knowledge-store-view-storage-explorer.md)