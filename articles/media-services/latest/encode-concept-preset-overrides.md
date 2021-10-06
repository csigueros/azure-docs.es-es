---
title: Uso de invalidaciones preestablecidas para cambiar la configuración de transformación en el envío de trabajos
description: En este artículo se explica cómo usar invalidaciones preestablecidas para ajustar la configuración de transformación en una instancia por trabajo.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: conceptual
ms.date: 09/20/2021
ms.author: johndeu
ms.custom: seodec18
ms.openlocfilehash: 3b748aa9e6b8d9f79572b05d10258839aabfa8f7
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/24/2021
ms.locfileid: "128699620"
---
# <a name="using-preset-overrides-to-control-per-job-settings"></a>Uso de invalidaciones preestablecidas para controlar la configuración por trabajo

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

## <a name="transforms-and-jobs-overview"></a>Información general sobre transformaciones y trabajos

Para codificar con Media Services v3, debe crear una [transformación](/rest/api/media/transforms) y un [trabajo](/rest/api/media/jobs). La transformación define la receta para la configuración de la codificación y las salidas, y el trabajo es una instancia de la receta. Para obtener más información, consulte [Transformaciones y trabajos](transform-jobs-concept.md).

Al codificar o usar análisis con Media Services, puede definir valores preestablecidos personalizados en una transformación para definir qué configuración usar. A veces es necesario invalidar la configuración en una transformación por trabajo para evitar tener que crear una transformación personalizada para cada escenario. Para invalidar cualquier configuración del valor preestablecido de transformación, puede usar la propiedad de invalidación preestablecida del [recurso de salida del trabajo](/dotnet/api/microsoft.azure.management.media.models.joboutputasset) antes de enviar el trabajo a la transformación.

## <a name="preset-overrides"></a>Invalidaciones preestablecidas

Las invalidaciones preestablecidas permiten pasar un valor preestablecido personalizado que invalidará la configuración proporcionada a un objeto de transformación después de que se creara por primera vez.  Esta propiedad está disponible en el [recurso de salida del trabajo](/dotnet/api/microsoft.azure.management.media.models.joboutputasset) al enviar un nuevo trabajo a una transformación.

Esto puede servir para situaciones en las que es necesario invalidar algunas propiedades de las transformaciones definidas personalizadas o una propiedad en un valor preestablecido integrado. Por ejemplo, considere el escenario en el que ha creado una transformación personalizada que usa el [valor preestablecido integrado del analizador de audio](/rest/api/media/transforms/create-or-update#audioanalyzerpreset), pero inicialmente ha configurado ese valor preestablecido para usar la configuración de idioma de audio "en-us" para inglés.  Esto daría lugar a una transformación en la que cada trabajo enviado se enviaría al motor de transcripción de voz a texto solo en inglés de Estados Unidos. Todos los trabajos enviados a esa transformación se bloquearían en la configuración de idioma "en-us". Podría evitar este escenario si tiene una transformación definida para todos los idiomas, pero eso sería mucho más difícil de administrar y podría alcanzar las limitaciones de cuota de transformación en su cuenta.
Para solucionar mejor este escenario, use una invalidación preestablecida en el recurso de salida del trabajo antes de enviar el trabajo a la transformación.  A continuación, puede definir una única transformación de "Transcripción de audio" y pasar la configuración de idioma necesaria por trabajo.

La invalidación de valores preestablecidos proporciona una manera de pasar una nueva definición de valores preestablecidos personalizados con cada trabajo enviado a la transformación. Esta propiedad está disponible en la entidad de [salida del trabajo](/dotnet/api/microsoft.azure.management.media.models.joboutput) en todas las versiones del SDK basadas en la versión 2021-06-01 de la API.

Como referencia, consulte la propiedad [presetOverride](https://github.com/Azure/azure-rest-api-specs/blob/ce90f9b45945c73b8f38649ee6ead390ff6efe7b/specification/mediaservices/resource-manager/Microsoft.Media/stable/2021-06-01/Encoding.json#L1960) en la entidad de salida del trabajo en la documentación de REST.

> [!NOTE]
> Solo puede usar invalidaciones preestablecidas para invalidar la configuración de un valor preestablecido definido en la transformación.  No se puede cambiar de un valor preestablecido específico a otro tipo. Por ejemplo, si se intenta invalidar una transformación creada con el valor preestablecido de codificación para contenido integrado para usar otro valor preestablecido como el analizador de audio, se produce un mensaje de error.


## <a name="example-of-preset-override-in-net"></a>Ejemplo de invalidación de valores preestablecidos en .NET

Hay disponible un ejemplo completo con el SDK de .NET para Media Services que muestra cómo usar la invalidación preestablecida con una transformación básica del analizador de audio en GitHub.
Consulte el ejemplo [Analizar un archivo multimedia con un analizador de audio preestablecido](https://github.com/Azure-Samples/media-services-v3-dotnet/tree/main/AudioAnalytics/AudioAnalyzer) para obtener más información sobre cómo usar la propiedad de invalidación preestablecida de la salida del trabajo.

## <a name="sample-code-of-preset-override-in-net"></a>Código de ejemplo de invalidación de valores preestablecidos en .NET

[!code-csharp[Main](../../../media-services-v3-dotnet/AudioAnalytics/AudioAnalyzer/program.cs#PresetOverride)]

## <a name="ask-questions-give-feedback-get-updates"></a>Formule preguntas, realice comentarios y obtenga actualizaciones

Consulte el artículo [Comunidad de Azure Media Services](media-services-community.md) para ver diferentes formas de formular preguntas, enviar comentarios y obtener actualizaciones de Media Services.

## <a name="next-steps"></a>Pasos siguientes

* [Carga, codificación y transmisión con Media Services](stream-files-tutorial-with-api.md).
* [Codificación desde una dirección URL de HTTPS con valores preestablecidos integrados](job-input-from-http-how-to.md).
* [Codificación de un archivo local con valores preestablecidos integrados](job-input-from-local-file-how-to.md).
* [Compilación de un valor preestablecido personalizado para sus requisitos específicos de escenario o dispositivo](transform-custom-presets-how-to.md).
