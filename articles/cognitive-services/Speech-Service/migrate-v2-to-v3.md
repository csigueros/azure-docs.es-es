---
title: 'Migración de la versión v2 a v3 de API REST: servicio de voz'
titleSuffix: Azure Cognitive Services
description: Este documento ayuda a los desarrolladores a migrar código de la versión v2 a v3 de la API REST de conversión de voz en texto de los servicios de voz.
services: cognitive-services
author: bexxx
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 02/12/2020
ms.author: rbeckers
ms.custom: devx-track-csharp
ms.openlocfilehash: e8e55202163a447ca2d7d08999c953e619bc027b
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/13/2021
ms.locfileid: "121746576"
---
# <a name="migrate-code-from-v20-to-v30-of-the-rest-api"></a>Migración de código de la versión v2.0 a v3.0 de API REST

En comparación con v2, la versión v3 de la API REST de los servicios de voz para la conversión de voz en texto es más confiable, más fácil de usar y más coherente con las API de servicios similares. La mayoría de los equipos pueden migrar de v2 a v3 en un día o dos.

## <a name="forward-compatibility"></a>Compatibilidad con versiones posteriores

Todas las entidades de v2 también se pueden encontrar en la versión v3 de la API con la misma identidad. Cuando el esquema de un resultado cambia (por ejemplo, las transcripciones), el resultado de una operación Get en la versión v3 de la API usa el esquema v3. El resultado de una operación Get en la versión v2 de la API usa el mismo esquema v2. Las entidades recién creadas en v3 **no** están disponibles en las respuestas de las API de v2. 

## <a name="migration-steps"></a>Pasos de migración

Esta es una lista resumida de los elementos que debe tener en cuenta al preparar la migración. Los detalles se encuentran en los vínculos individuales. En función del uso actual de la API, es posible que no se apliquen todos los pasos que se enumeran aquí. Solo algunos cambios requieren cambios no triviales en el código de llamada. La mayoría de los cambios solo requieren un cambio en los nombres de los elementos. 

Cambios generales: 

1. [Cambiar el nombre de host](#host-name-changes)

1. [Cambiar el identificador de la propiedad a self en el código de cliente](#identity-of-an-entity) 

1. [Cambiar el código para iterar en colecciones de entidades](#working-with-collections-of-entities)

1. [Cambiar el nombre de la propiedad a displayName en el código de cliente](#name-of-an-entity)

1. [Ajustar la recuperación de los metadatos de las entidades referenciadas](#accessing-referenced-entities)

1. Si usa la transcripción por lotes: 

    * [Ajustar el código para crear transcripciones por lotes](#creating-transcriptions) 

    * [Adaptar el código al nuevo esquema de resultados de transcripción](#format-of-v3-transcription-results)

    * [Ajustar el código para el modo de recuperación de los resultados](#getting-the-content-of-entities-and-the-results)

1. Si usa las API de entrenamiento y pruebas del modelo personalizado: 

    * [Aplicar modificaciones al entrenamiento del modelo personalizado](#customizing-models)

    * [Cambiar el modo en que se recuperan los modelos base y personalizado](#retrieving-base-and-custom-models)

    * [Cambiar el nombre del segmento de línea de accuracytests a evaluations en el código de cliente](#accuracy-tests)

1. Si usa API de punto de conexión:

    * [Cambiar el modo en que se recuperan los registros del punto de conexión](#retrieving-endpoint-logs)

1. Otros cambios secundarios: 

    * [Pasar todas las propiedades personalizadas como customProperties en lugar de propiedades en las solicitudes POST](#using-custom-properties)

    * [Leer la ubicación desde el encabezado de respuesta en lugar de Operation-Location](#response-headers)

## <a name="breaking-changes"></a>Últimos cambios

### <a name="host-name-changes"></a>Cambios de los nombres de host

Los nombres de host del punto de conexión han cambiado de `{region}.cris.ai` a `{region}.api.cognitive.microsoft.com`. Las rutas de acceso a los nuevos puntos de conexión ya no contienen `api/` porque forma parte del nombre de host. En el [documento de Swagger](https://westus.dev.cognitive.microsoft.com/docs/services/speech-to-text-api-v3-0) se enumeran las regiones y rutas de acceso válidas.
>[!IMPORTANT]
>Cambie el nombre de host de `{region}.cris.ai` a `{region}.api.cognitive.microsoft.com` donde "region" es la región de la suscripción de voz. Quite también `api/` de cualquier ruta de acceso en el código de cliente.

### <a name="identity-of-an-entity"></a>Identidad de una entidad

Ahora la propiedad `id` es `self`. En la versión v2, un usuario de la API tenía que saber cómo crear las rutas de acceso de la API. Se trataba de un trabajo no extensible e innecesario del usuario. La propiedad `id` (UUID) se reemplaza por `self` (cadena), que es la ubicación de la entidad (URL). El valor sigue siendo único entre todas las entidades. Si `id` se almacena como una cadena en el código, un cambio de nombre es suficiente para admitir el nuevo esquema. Ahora puede usar el contenido de `self` como dirección URL para las llamadas REST de `GET`, `PATCH` y `DELETE` de la entidad.

Si la entidad tiene una funcionalidad adicional disponible a través de otras rutas de acceso, se enumeran en `links`. En el ejemplo siguiente de transcripción se muestra un método independiente para el contenido `GET` de la transcripción:
>[!IMPORTANT]
>Cambie el nombre de la propiedad `id` por `self` en el código de cliente. Cambie el tipo de `uuid` a `string` si es necesario. 

**Transcripción v2:**

```json
{
    "id": "9891c965-bb32-4880-b14b-6d44efb158f3",
    "createdDateTime": "2019-01-07T11:34:12Z",
    "lastActionDateTime": "2019-01-07T11:36:07Z",
    "status": "Succeeded",
    "locale": "en-US",
    "name": "Transcription using locale en-US"
}
```

**Transcripción v3:**

```json
{
    "self": "https://westus.api.cognitive.microsoft.com/speechtotext/v3.0/transcriptions/9891c965-bb32-4880-b14b-6d44efb158f3",
    "createdDateTime": "2019-01-07T11:34:12Z",
    "lastActionDateTime": "2019-01-07T11:36:07Z",
    "status": "Succeeded",
    "locale": "en-US", 
    "displayName": "Transcription using locale en-US",
    "links": {
      "files": "https://westus.api.cognitive.microsoft.com/speechtotext/v3.0/transcriptions/9891c965-bb32-4880-b14b-6d44efb158f3/files"
    }
}
```

Dependiendo de la implementación del código, puede que no sea suficiente para cambiar el nombre de la propiedad. Se recomienda usar los valores devueltos `self` y `links` como las direcciones URL de destino de las llamadas REST, en lugar de generar rutas de acceso en el cliente. Mediante el uso de las direcciones URL devueltas, puede garantizar que los cambios futuros en las rutas de acceso no interrumpan el código de cliente.

### <a name="working-with-collections-of-entities"></a>Trabajo con colecciones de entidades

Anteriormente, la API v2 devolvía todas las entidades disponibles en un resultado. Para permitir un control más preciso sobre el tamaño de respuesta esperado en v3, se paginan todos los resultados de la colección. Tiene control sobre el recuento de entidades devueltas y el desplazamiento inicial de la página. Este comportamiento facilita la predicción del tiempo de ejecución del procesador de respuesta.

La forma básica de la respuesta es la misma para todas las colecciones:

```json
{
    "values": [
        {     
        }
    ],
    "@nextLink": "https://{region}.api.cognitive.microsoft.com/speechtotext/v3.0/{collection}?skip=100&top=100"
}
```

La propiedad `values` contiene un subconjunto de las entidades disponibles de la colección. El recuento y el desplazamiento se pueden controlar mediante los parámetros de consulta `skip` y `top`. Cuando `@nextLink` no es `null`, hay más datos disponibles y el siguiente lote de datos se puede recuperar mediante una operación Get en `$.@nextLink`.

Este cambio requiere llamar a la operación `GET` de la colección en un bucle hasta que se devuelvan todos los elementos.

>[!IMPORTANT]
>Cuando la respuesta de una solicitud GET a `speechtotext/v3.0/{collection}` contiene un valor en `$.@nextLink`, continúe emitiendo `GETs` en `$.@nextLink` hasta que no se haya establecido `$.@nextLink` para recuperar todos los elementos de esa colección.

### <a name="creating-transcriptions"></a>Creación de transcripciones

Puede encontrar una descripción detallada sobre cómo crear lotes de transcripciones en [Instrucciones para la transcripción por lotes](./batch-transcription.md).

La API de transcripción v3 le permite establecer opciones específicas de transcripción explícitamente. Ahora, todas las propiedades de configuración (opcionales) se pueden establecer en la propiedad `properties`.
La versión v3 también admite varios archivos de entrada, por lo que requiere una lista de direcciones URL en lugar de una sola dirección URL, a diferencia de la versión v2. Ahora el nombre de la propiedad v2 `recordingsUrl` es `contentUrls` en v3. En la versión v3 se ha quitado la funcionalidad de análisis de opiniones en las transcripciones. Consulte [Text Analytics](https://azure.microsoft.com/services/cognitive-services/text-analytics/) de Microsoft Cognitive Services para ver las opciones de análisis de opiniones.

La nueva propiedad `timeToLive` en `properties` puede ayudar a eliminar las entidades completadas existentes. `timeToLive` especifica una duración después de la cual se eliminará automáticamente una entidad completada. Establézcala en un valor alto (por ejemplo, `PT12H`) cuando se realice un seguimiento de las entidades o cuando estas se consuman y se eliminen de forma continua y, por lo tanto, se procesen mucho antes de que pasen 12 horas.

**Cuerpo de la solicitud POST de transcripción v2:**

```json
{
  "locale": "en-US",
  "name": "Transcription using locale en-US",
  "recordingsUrl": "https://contoso.com/mystoragelocation",
  "properties": {
    "AddDiarization": "False",
    "AddWordLevelTimestamps": "False",
    "PunctuationMode": "DictatedAndAutomatic",
    "ProfanityFilterMode": "Masked"
  }
}
```

**Cuerpo de la solicitud POST de transcripción v3:**

```json
{
  "locale": "en-US",
  "displayName": "Transcription using locale en-US",
  "contentUrls": [
    "https://contoso.com/mystoragelocation",
    "https://contoso.com/myotherstoragelocation"
  ],
  "properties": {
    "diarizationEnabled": false,
    "wordLevelTimestampsEnabled": false,
    "punctuationMode": "DictatedAndAutomatic",
    "profanityFilterMode": "Masked"
  }
}
```
>[!IMPORTANT]
>Cambie el nombre de la propiedad `recordingsUrl` a `contentUrls` y pase una matriz de direcciones URL en lugar de una sola dirección URL. Pase la configuración de `diarizationEnabled` o `wordLevelTimestampsEnabled` como `bool` en lugar de `string`.

### <a name="format-of-v3-transcription-results"></a>Formato de los resultados de la transcripción v3

El esquema de los resultados de la transcripción ha cambiado ligeramente para alinearse con transcripciones creadas por puntos de conexión en tiempo real. Busque una descripción detallada del nuevo formato en [Instrucciones para la transcripción por lotes](./batch-transcription.md). El esquema del resultado se publica en nuestro [repositorio de ejemplo de GitHub](https://aka.ms/csspeech/samples) en `samples/batch/transcriptionresult_v3.schema.json`.

Ahora, los nombres de propiedad tienen mayúsculas y minúsculas y los valores de `channel` y `speaker` usan tipos enteros. El formato de las duraciones usa la estructura descrita en ISO 8601, que coincide con el formato de duración usado en otras API de Azure.

Ejemplo de un resultado de la transcripción v3. Las diferencias se describen en los comentarios.

```json
{
  "source": "...",                      // (new in v3) was AudioFileName / AudioFileUrl
  "timestamp": "2020-06-16T09:30:21Z",  // (new in v3)
  "durationInTicks": 41200000,          // (new in v3) was AudioLengthInSeconds
  "duration": "PT4.12S",                // (new in v3)
  "combinedRecognizedPhrases": [        // (new in v3) was CombinedResults
    {
      "channel": 0,                     // (new in v3) was ChannelNumber
      "lexical": "hello world",
      "itn": "hello world",
      "maskedITN": "hello world",
      "display": "Hello world."
    }
  ],
  "recognizedPhrases": [                // (new in v3) was SegmentResults
    {
      "recognitionStatus": "Success",   // 
      "channel": 0,                     // (new in v3) was ChannelNumber
      "offset": "PT0.07S",              // (new in v3) new format, was OffsetInSeconds
      "duration": "PT1.59S",            // (new in v3) new format, was DurationInSeconds
      "offsetInTicks": 700000.0,        // (new in v3) was Offset
      "durationInTicks": 15900000.0,    // (new in v3) was Duration

      // possible transcriptions of the current phrase with confidences
      "nBest": [
        {
          "confidence": 0.898652852,phrase
          "speaker": 1,
          "lexical": "hello world",
          "itn": "hello world",
          "maskedITN": "hello world",
          "display": "Hello world.",

          "words": [
            {
              "word": "hello",
              "offset": "PT0.09S",
              "duration": "PT0.48S",
              "offsetInTicks": 900000.0,
              "durationInTicks": 4800000.0,
              "confidence": 0.987572
            },
            {
              "word": "world",
              "offset": "PT0.59S",
              "duration": "PT0.16S",
              "offsetInTicks": 5900000.0,
              "durationInTicks": 1600000.0,
              "confidence": 0.906032
            }
          ]
        }
      ]
    }
  ]
}
```
>[!IMPORTANT]
>Deserialice el resultado de la transcripción en el nuevo tipo como se mostró anteriormente. En lugar de un único archivo por canal de audio, distinga los canales comprobando el valor de propiedad de `channel` para cada elemento en `recognizedPhrases`. Ahora hay un único archivo de resultados para cada archivo de entrada.


### <a name="getting-the-content-of-entities-and-the-results"></a>Obtención del contenido de las entidades y los resultados

En v2, los vínculos a los archivos de entrada o de resultados se han insertado con el resto de los metadatos de la entidad. Como mejora en la versión v3, hay una separación clara entre los metadatos de la entidad (devueltos por una operación Get en `$.self`) y los detalles y las credenciales para tener acceso a los archivos de resultados. Esta separación ayuda a proteger los datos de los clientes y permite un control preciso sobre la duración de la validez de las credenciales.

En la versión v3, `links` incluye una subpropiedad llamada `files` en caso de que la entidad exponga datos (conjuntos de datos, transcripciones, puntos de conexión o evaluaciones). Una operación Get en `$.links.files` devolverá una lista de archivos y una dirección URL de SAS para tener acceso al contenido de cada archivo. Para controlar la duración de la validez de las direcciones URL de SAS, el parámetro de consulta `sasValidityInSeconds` se puede usar para especificar la duración.

**Transcripción v2:**

```json
{
  "id": "9891c965-bb32-4880-b14b-6d44efb158f3",
  "status": "Succeeded",
  "reportFileUrl": "https://contoso.com/report.txt?st=2018-02-09T18%3A07%3A00Z&se=2018-02-10T18%3A07%3A00Z&sp=rl&sv=2017-04-17&sr=b&sig=6c044930-3926-4be4-be76-f728327c53b5",
  "resultsUrls": {
    "channel_0": "https://contoso.com/audiofile1.wav?st=2018-02-09T18%3A07%3A00Z&se=2018-02-10T18%3A07%3A00Z&sp=rl&sv=2017-04-17&sr=b&sig=6c044930-3926-4be4-be76-f72832e6600c",
    "channel_1": "https://contoso.com/audiofile2.wav?st=2018-02-09T18%3A07%3A00Z&se=2018-02-10T18%3A07%3A00Z&sp=rl&sv=2017-04-17&sr=b&sig=3e0163f1-0029-4d4a-988d-3fba7d7c53b5"
  }
}
```

**Transcripción v3:**

```json
{
    "self": "https://westus.api.cognitive.microsoft.com/speechtotext/v3.0/transcriptions/9891c965-bb32-4880-b14b-6d44efb158f3",
    "links": {
      "files": "https://westus.api.cognitive.microsoft.com/speechtotext/v3.0/transcriptions/9891c965-bb32-4880-b14b-6d44efb158f3/files"
    } 
}
```

**Una operación Get en `$.links.files` daría como resultado:**

```json
{
  "values": [
    {
      "self": "https://westus.api.cognitive.microsoft.com/speechtotext/v3.0/transcriptions/9891c965-bb32-4880-b14b-6d44efb158f3/files/f23e54f5-ed74-4c31-9730-2f1a3ef83ce8",
      "name": "Name",
      "kind": "Transcription",
      "properties": {
        "size": 200
      },
      "createdDateTime": "2020-01-13T08:00:00Z",
      "links": {
        "contentUrl": "https://customspeech-usw.blob.core.windows.net/artifacts/mywavefile1.wav.json?st=2018-02-09T18%3A07%3A00Z&se=2018-02-10T18%3A07%3A00Z&sp=rl&sv=2017-04-17&sr=b&sig=e05d8d56-9675-448b-820c-4318ae64c8d5"
      }
    },
    {
      "self": "https://westus.api.cognitive.microsoft.com/speechtotext/v3.0/transcriptions/9891c965-bb32-4880-b14b-6d44efb158f3/files/28bc946b-c251-4a86-84f6-ea0f0a2373ef",
      "name": "Name",
      "kind": "TranscriptionReport",
      "properties": {
        "size": 200
      },
      "createdDateTime": "2020-01-13T08:00:00Z",
      "links": {
        "contentUrl": "https://customspeech-usw.blob.core.windows.net/artifacts/report.json?st=2018-02-09T18%3A07%3A00Z&se=2018-02-10T18%3A07%3A00Z&sp=rl&sv=2017-04-17&sr=b&sig=e05d8d56-9675-448b-820c-4318ae64c8d5"
      }
    }
  ],
  "@nextLink": "https://westus.api.cognitive.microsoft.com/speechtotext/v3.0/transcriptions/9891c965-bb32-4880-b14b-6d44efb158f3/files?skip=2&top=2"
}
```

La propiedad `kind` indica el formato del contenido del archivo. En el caso de las transcripciones, los archivos de tipo `TranscriptionReport` son el resumen del trabajo, y los archivos de tipo `Transcription` son el resultado del propio trabajo.

>[!IMPORTANT]
>Para obtener los resultados de las operaciones, use una solicitud `GET` en `/speechtotext/v3.0/{collection}/{id}/files`, ya no se incluyen en las respuestas de `GET` en `/speechtotext/v3.0/{collection}/{id}` o `/speechtotext/v3.0/{collection}`.

### <a name="customizing-models"></a>Personalización de los modelos

Antes de la versión v3, existía una distinción entre un _modelo acústico_ y un _modelo de lenguaje_ al entrenar un modelo. Esta distinción provocaba la necesidad de especificar varios modelos al crear puntos de conexión o transcripciones. Para simplificar este proceso para un autor de llamada, se han eliminado las diferencias y se ha hecho todo lo que depende del contenido de los conjuntos de datos que se usan para el entrenamiento del modelo. Con este cambio, la creación del modelo ya admite conjuntos de datos mixtos (datos de idioma y datos acústicos). Ahora los puntos de conexión y las transcripciones requieren un solo modelo.

Con este cambio, se ha quitado la necesidad de un valor `kind` en la operación `POST` y la matriz de `datasets[]` puede contener varios conjuntos de datos del mismo tipo o mezclados.

Para mejorar los resultados de un modelo entrenado, los datos acústicos se usan internamente de forma automática durante el entrenamiento de los idiomas. En general, los modelos creados a través de la API v3 proporcionan resultados más precisos que los modelos creados con la API v2.

>[!IMPORTANT]
>Para personalizar la parte del modelo acústico y de lenguaje, pase todos los conjuntos de valores de idioma y acústicos en `datasets[]` de la solicitud POST a `/speechtotext/v3.0/models`. Esto creará un único modelo con las dos partes personalizadas.

### <a name="retrieving-base-and-custom-models"></a>Recuperación de modelos base y personalizados

Para simplificar la obtención de los modelos disponibles, la versión v3 ha separado las colecciones de "modelos base" de los "modelos personalizados" propiedad del cliente. Las dos rutas son `GET /speechtotext/v3.0/models/base` y `GET /speechtotext/v3.0/models/`.

En la versión v2, todos los modelos se devolvieron juntos en una sola respuesta.

>[!IMPORTANT]
>Para obtener una lista de los modelos base proporcionados para la personalización, use `GET` en `/speechtotext/v3.0/models/base`. Puede encontrar sus propios modelos personalizados con una solicitud `GET` en `/speechtotext/v3.0/models`.

### <a name="name-of-an-entity"></a>Nombre de una entidad

Ahora la propiedad `name` es `displayName`. Esto es coherente con otras API de Azure para no indicar las propiedades de la identidad. El valor de esta propiedad no debe ser único y se puede cambiar después de la creación de la entidad con una operación `PATCH`.

**Transcripción v2:**

```json
{
    "name": "Transcription using locale en-US"
}
```

**Transcripción v3:**

```json
{
    "displayName": "Transcription using locale en-US"
}
```

>[!IMPORTANT]
>Cambie el nombre de la propiedad `name` por `displayName` en el código de cliente.

### <a name="accessing-referenced-entities"></a>Acceso a las entidades con referencia

En la versión v2, las entidades con referencia siempre estaban insertadas, por ejemplo, en los modelos utilizados de un punto de conexión. El anidamiento de entidades dio como resultado respuestas grandes y los consumidores raramente consumieron el contenido anidado. Para reducir el tamaño de la respuesta y mejorar el rendimiento, las entidades con referencia ya no se insertan en la respuesta. En su lugar, aparece una referencia a la otra entidad y se puede usar directamente para una operación `GET` posterior (también es una dirección URL), siguiendo el mismo patrón que el vínculo de `self`.

**Transcripción v2:**

```json
{
  "id": "9891c965-bb32-4880-b14b-6d44efb158f3",
  "models": [
    {
      "id": "827712a5-f942-4997-91c3-7c6cde35600b",
      "modelKind": "Language",
      "lastActionDateTime": "2019-01-07T11:36:07Z",
      "status": "Running",
      "createdDateTime": "2019-01-07T11:34:12Z",
      "locale": "en-US",
      "name": "Acoustic model",
      "description": "Example for an acoustic model",
      "datasets": [
        {
          "id": "702d913a-8ba6-4f66-ad5c-897400b081fb",
          "dataImportKind": "Language",
          "lastActionDateTime": "2019-01-07T11:36:07Z",
          "status": "Succeeded",
          "createdDateTime": "2019-01-07T11:34:12Z",
          "locale": "en-US",
          "name": "Language dataset",
        }
      ]
    },
  ]
}
```

**Transcripción v3:**

```json
{
  "self": "https://westus.api.cognitive.microsoft.com/speechtotext/v3.0/transcriptions/9891c965-bb32-4880-b14b-6d44efb158f3",
  "model": {
    "self": "https://westus.api.cognitive.microsoft.com/speechtotext/v3.0/models/021a72d0-54c4-43d3-8254-27336ead9037"
  }
}
```

Si necesita consumir los detalles de un modelo con referencia tal como se muestra en el ejemplo anterior, solo tiene que emitir una operación Get en `$.model.self`.

>[!IMPORTANT]
>Para recuperar los metadatos de las entidades a las que se hace referencia, emita una solicitud GET en `$.{referencedEntity}.self`; por ejemplo, para recuperar el modelo de una transcripción, realice una solicitud `GET` en `$.model.self`.


### <a name="retrieving-endpoint-logs"></a>Recuperación de registros de punto de conexión

La versión v2 del servicio admitía los resultados del punto de conexión de registro. Para recuperar los resultados de un punto de conexión con v2, debía crear una "exportación de datos" que representase una instantánea de los resultados definidos por un intervalo de tiempo. El proceso de exportación por lotes de los datos era inflexible. La versión v3 de la API proporciona acceso a cada archivo individual y permite la iteración a través de ellos.

**Ejecución correcta de un punto de conexión v3:**

```json
{
  "self": "https://westus.api.cognitive.microsoft.com/speechtotext/v3.0/endpoints/afa0669c-a01e-4693-ae3a-93baf40f26d6",
  "links": {
    "logs": "https://westus.api.cognitive.microsoft.com/speechtotext/v3.0/endpoints/afa0669c-a01e-4693-ae3a-93baf40f26d6/files/logs" 
  }
}
```

**Respuesta de `$.links.logs` de Get:**

```json
{
  "values": [
    {
      "self": "https://westus.api.cognitive.microsoft.com/speechtotext/v3.0/endpoints/6d72ad7e-f286-4a6f-b81b-a0532ca6bcaa/files/logs/2019-09-20_080000_3b5f4628-e225-439d-bd27-8804f9eed13f.wav",
      "name": "2019-09-20_080000_3b5f4628-e225-439d-bd27-8804f9eed13f.wav",
      "kind": "Audio",
      "properties": {
        "size": 12345
      },
      "createdDateTime": "2020-01-13T08:00:00Z",
      "links": {
        "contentUrl": "https://customspeech-usw.blob.core.windows.net/artifacts/2019-09-20_080000_3b5f4628-e225-439d-bd27-8804f9eed13f.wav?st=2018-02-09T18%3A07%3A00Z&se=2018-02-10T18%3A07%3A00Z&sp=rl&sv=2017-04-17&sr=b&sig=e05d8d56-9675-448b-820c-4318ae64c8d5"
      }
    }    
  ],
  "@nextLink": "https://westus.api.cognitive.microsoft.com/speechtotext/v3.0/endpoints/afa0669c-a01e-4693-ae3a-93baf40f26d6/files/logs?top=2&SkipToken=2!188!MDAwMDk1ITZhMjhiMDllLTg0MDYtNDViMi1hMGRkLWFlNzRlOGRhZWJkNi8yMDIwLTA0LTAxLzEyNDY0M182MzI5NGRkMi1mZGYzLTRhZmEtOTA0NC1mODU5ZTcxOWJiYzYud2F2ITAwMDAyOCE5OTk5LTEyLTMxVDIzOjU5OjU5Ljk5OTk5OTlaIQ--"
}
```

La paginación de los registros de punto de conexión funciona de forma similar a las demás colecciones, salvo que no se puede especificar ningún desplazamiento. Debido a la gran cantidad de datos disponibles, el servidor determina la paginación.

En la versión v3, cada registro de punto de conexión se puede eliminar individualmente mediante la emisión de una operación `DELETE` en el `self` de un archivo o mediante `DELETE` en `$.links.logs`. Para especificar una fecha de finalización, el parámetro de consulta `endDate` se puede agregar a la solicitud.

>[!IMPORTANT]
>En lugar de crear exportaciones de registros en `/api/speechtotext/v2.0/endpoints/{id}/data`, use `/v3.0/endpoints/{id}/files/logs/` para acceder a los archivos de registro de forma individual. 

### <a name="using-custom-properties"></a>Uso de propiedades personalizadas

Para separar las propiedades personalizadas de las propiedades de configuración opcionales, todas las propiedades con nombre explícito se encuentran en la propiedad `properties`, y todas las propiedades definidas por los autores de llamada se encuentran en la propiedad `customProperties`.

**Entidad de transcripción v2:**

```json
{
  "properties": {
    "customerDefinedKey": "value",
    "diarizationEnabled": "False",
    "wordLevelTimestampsEnabled": "False"
  }
}
```

**Entidad de transcripción v3:**

```json
{
  "properties": {
    "diarizationEnabled": false,
    "wordLevelTimestampsEnabled": false 
  },
  "customProperties": {
    "customerDefinedKey": "value"
  }
}
```

Este cambio también le permite usar tipos correctos en todas las propiedades con nombre explícito en `properties` (por ejemplo, booleano en lugar de cadena).

>[!IMPORTANT]
>Pase todas las propiedades personalizadas como `customProperties` en lugar de `properties` en las solicitudes `POST`.

### <a name="response-headers"></a>Encabezados de respuesta

La versión v3 ya no devuelve el encabezado `Operation-Location` junto con el encabezado `Location` en las solicitudes de `POST`. El valor de ambos encabezados de v2 era el mismo. Ahora solo se devuelve `Location`.

Dado que ahora Azure API Management (APIM) administra la nueva versión de API, los encabezados relacionados con la limitación `X-RateLimit-Limit`, `X-RateLimit-Remaining` y `X-RateLimit-Reset` no se incluyen en los encabezados de respuesta.

>[!IMPORTANT]
>Lea la ubicación del encabezado de respuesta `Location` en lugar de `Operation-Location`. En el caso de un código de respuesta 429, lea el valor del encabezado `Retry-After` en lugar de `X-RateLimit-Limit`, `X-RateLimit-Remaining` o `X-RateLimit-Reset`.


### <a name="accuracy-tests"></a>Pruebas de precisión

Ahora las pruebas de precisión se denominan evaluaciones, porque el nuevo nombre describe mejor lo que representan. Las nuevas rutas de acceso son: `https://{region}.api.cognitive.microsoft.com/speechtotext/v3.0/evaluations`.

>[!IMPORTANT]
>Cambie el nombre del segmento de ruta de acceso `accuracytests` a `evaluations` en el código de cliente.


## <a name="next-steps"></a>Pasos siguientes

Examine todas las características de estas API REST de uso frecuente proporcionadas por los servicios de voz:

* [Speech-to-text REST API](rest-speech-to-text.md)
* [Documento de Swagger](https://westus.dev.cognitive.microsoft.com/docs/services/speech-to-text-api-v3-0) para la versión v3 de la API REST
* Para ver el código de ejemplo para realizar transcripciones por lotes, consulte el [repositorio de ejemplo de GitHub](https://aka.ms/csspeech/samples) en el subdirectorio `samples/batch`.
