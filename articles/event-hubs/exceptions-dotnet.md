---
title: 'Azure Event Hubs: excepciones de .NET'
description: En este artículo se proporciona una lista de las excepciones de mensajería y acciones sugeridas del .NET de Azure Event Hubs.
services: event-hubs
documentationcenter: na
author: spelluru
ms.service: event-hubs
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.custom: seodec18
ms.date: 09/23/2021
ms.author: spelluru
ms.openlocfilehash: e3f37267bcf5c4dd6248cc9498ac0cc84ea91a75
ms.sourcegitcommit: e8c34354266d00e85364cf07e1e39600f7eb71cd
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/29/2021
ms.locfileid: "129210314"
---
# <a name="eventhubsexception---net"></a>EventHubsException: .NET
Se desencadena una excepción **EventHubsException** cuando una operación específica de Event Hubs provoca un problema, incluidos los errores en el servicio y específicos del cliente. 

## <a name="exception-information"></a>Información de la excepción
La excepción incluye la siguiente información contextual para ayudar a comprender el contexto del error y su gravedad relativa. 

- **IsTransient**: identifica si la excepción se considera recuperable o no. En el caso de que se considerase transitorio, ya se ha aplicado la directiva de reintentos adecuada y los reintentos no hayan producido el resultado esperado.
- **Motivo**: proporciona un conjunto de razones conocidas para el error que ayuda a clasificar y aclarar la causa principal. Estas razones están pensadas para permitir la aplicación del filtrado de excepciones y otra lógica cuando la inspección del texto de un mensaje de excepción no sería lo ideal. Algunos motivos de error clave son:
    - **Cliente cerrado**: Se produce cuando un cliente del centro de eventos ya se ha cerrado o desechado. Se recomienda comprobar el código de la aplicación para asegurarse de que los objetos de la biblioteca cliente de Event Hubs se han creado y cerrado en el ámbito previsto.
    - **Tiempo de espera del servicio**: indica que el servicio Event Hubs no respondió a una operación en la cantidad de tiempo prevista. Este problema puede deberse a un problema transitorio del servicio o la red. Es posible que el servicio Event Hubs haya completado correctamente la solicitud; el estado es desconocido. Se recomienda intentar comprobar el estado actual y volver a intentarlo si es necesario.
    - **Cuota superada**: indica que hay demasiadas operaciones de lectura activas para un único grupo de consumidores. Este límite depende del nivel del espacio de nombres de Event Hubs y es posible que sea necesario pasar a un nivel superior. Una alternativa sería crear grupos de consumidores adicionales y garantizar que el número de lecturas de cliente de consumidor de cualquier grupo esté dentro del límite. Para obtener más información, consulte [Cuotas y límites de Azure Event Hubs](event-hubs-quotas.md).
    - **Tamaño de mensaje superado**: datos del evento como un tamaño máximo permitido para un evento individual y un lote de eventos. Incluye los datos del evento, así como los metadatos asociados y la sobrecarga del sistema. Para resolver este error, reduzca el número de eventos enviados en un lote o el tamaño de los datos incluidos en el mensaje. Dado que los límites de tamaño están sujetos a cambios, consulte [Cuotas y límites de Azure Event Hubs](event-hubs-quotas.md) para obtener información específica.
    - **Consumidor desconectado**: El servicio del centro de eventos desconectó un cliente de consumidor de la instancia del centro de eventos. Normalmente, se produce cuando un consumidor con un nivel de propietario superior valida la propiedad sobre un emparejamiento de grupo de consumidores y partición.
    - **Recurso no encontrado**: el servicio Event Hubs no encontró un recurso como un centro de eventos, un grupo de consumidores o una partición. Es posible que se haya eliminado o que exista un problema con el propio servicio Event Hubs.

## <a name="handling-exceptions"></a>Control de excepciones
Puede reaccionar a un motivo de error específico para la excepción **EventHubException** de varias maneras. Una forma consiste en aplicar una cláusula de filtro de excepción como parte del bloque catch.

```csharp
try
{
    // Read events using the consumer client
}
catch (EventHubsException ex) where 
    (ex.Reason == EventHubsException.FailureReason.ConsumerDisconnected)
{
    // Take action based on a consumer being disconnected
}
```

## <a name="next-steps"></a>Pasos siguientes
Hay otras excepciones que se documentan en el [artículo heredado](event-hubs-messaging-exceptions.md). Algunas de ellas solo se aplican a la biblioteca cliente de .NET de Event Hubs heredada.