---
author: baanders
description: incluir para la nota de latencia de la API de consulta
ms.service: digital-twins
ms.topic: include
ms.date: 09/21/2021
ms.author: baanders
ms.openlocfilehash: 2e538b6f0197f408be649bd76cb4a15fbe22b4bb
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/24/2021
ms.locfileid: "128557691"
---
>[!NOTE]
>Después de realizar un cambio en los datos del gráfico, puede haber una latencia de hasta 10 segundos antes de que los cambios se reflejen en las consultas. 
>
>La [API de DigitalTwins](../articles/digital-twins/concepts-apis-sdks.md#overview-data-plane-apis) refleja los cambios inmediatamente, por lo que si necesita una respuesta instantánea, use una solicitud de API ([DigitalTwins GetById](/rest/api/digital-twins/dataplane/twins/digitaltwins_getbyid)) o una llamada SDK ([GetDigitalTwin](/dotnet/api/azure.digitaltwins.core.digitaltwinsclient.getdigitaltwin?view=azure-dotnet&preserve-view=true)) para obtener datos gemelos en lugar de una consulta.