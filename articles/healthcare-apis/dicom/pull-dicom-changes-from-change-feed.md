---
title: Incorpore cambios de DICOM mediante la fuente de cambios
description: En esta guía paso a paso se explica cómo incorporar cambios de DICOM mediante la fuente de cambios DICOM para Azure Healthcare APIs.
author: stevewohl
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: how-to
ms.date: 08/04/2021
ms.author: aersoy
ms.openlocfilehash: fa0237a57f5ebb8df0a69fa715a36d963ea0748f
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 08/13/2021
ms.locfileid: "121780063"
---
# <a name="pull-dicom-changes-using-the-change-feed"></a>Incorpore cambios de DICOM mediante la fuente de cambios

> [!IMPORTANT]
> Azure Healthcare APIs se encuentra actualmente en VERSIÓN PRELIMINAR. Los [Términos de uso complementarios para las versiones preliminares de Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) incluyen términos legales adicionales que se aplican a las características de Azure que se encuentran en la versión beta, en versión preliminar o que todavía no se han publicado con disponibilidad general.

La fuente de cambios de DICOM ofrece a los clientes la posibilidad de revisar el historial del servicio de DICOM y actuar en los eventos de creación y eliminación del servicio. En esta guía paso a paso se describe cómo consumir la fuente de cambios.

Se accede a la fuente de cambios mediante las API de REST. Estas API junto con el uso de ejemplo de la fuente de cambios se documentan en [Información general de la fuente de cambios DICOM](dicom-change-feed-overview.md). La versión de la API de REST debe especificarse explícitamente en la dirección URL de la solicitud, como se indica en la [Documentación del servicio de versiones de API para DICOM](api-versioning-dicom-service.md).

## <a name="consume-change-feed"></a>Consumo de fuente de cambios

En el siguiente ejemplo de código de C# se muestra cómo consumir la fuente de cambios mediante el paquete cliente DICOM.

```csharp
const int limit = 10;
 
using HttpClient httpClient = new HttpClient { BaseAddress = new Uri("<URL>") };
using CancellationTokenSource tokenSource = new CancellationTokenSource();
 
int read;
List<ChangeFeedEntry> entries = new List<ChangeFeedEntry>();
DicomWebClient client = new DicomWebClient(httpClient);
do
{
    read = 0;
    DicomWebAsyncEnumerableResponse<ChangeFeedEntry> result = await client.GetChangeFeed(
        $"?offset={entries.Count}&limit={limit}&includeMetadata={true}",
        tokenSource.Token);
 
    await foreach (ChangeFeedEntry entry in result)
    {
        read++;
        entries.Add(entry);
    }
} while (read > 0);
```

Para ver y acceder al ejemplo de código **ChangeFeedRetrieveService.cs**, consulte [Consumo de fuente de cambios](https://github.com/microsoft/dicom-server/blob/main/converter/dicom-cast/src/Microsoft.Health.DicomCast.Core/Features/DicomWeb/Service/ChangeFeedRetrieveService.cs).

## <a name="next-steps"></a>Pasos siguientes

En esta guía paso a paso se describe cómo consumir la fuente de cambios. La fuente de cambios permite supervisar el historial del servicio DICOM. Para obtener información sobre el servicio DICOM, consulte

>[!div class="nextstepaction"]
>[Información general del servicio DICOM](dicom-services-overview.md)
