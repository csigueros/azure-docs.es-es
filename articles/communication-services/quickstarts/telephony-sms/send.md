---
title: 'Inicio rápido: envío de un mensaje de texto'
titleSuffix: An Azure Communication Services quickstart
description: Obtenga información acerca de cómo enviar un mensaje de texto mediante Azure Communication Services.
author: probableprime
manager: chpalm
services: azure-communication-services
ms.author: rifox
ms.date: 06/30/2021
ms.topic: quickstart
ms.service: azure-communication-services
ms.custom: tracking-python, devx-track-js
zone_pivot_groups: acs-js-csharp-java-python
ms.openlocfilehash: 1ace517eefe9b9f579de2975a1809278d5a5afbb
ms.sourcegitcommit: 2eac9bd319fb8b3a1080518c73ee337123286fa2
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/31/2021
ms.locfileid: "123257762"
---
# <a name="quickstart-send-an-sms-message"></a>Inicio rápido: Envío de un mensaje SMS

[!INCLUDE [Regional Availability Notice](../../includes/regional-availability-include.md)]

> [!IMPORTANT]
> Se pueden enviar y recibir SMS de números de teléfono de EE. UU. Los números de teléfono ubicados en otras zonas geográficas no se admiten aún en SMS de Communication Services.
> Para más información, consulte **[Tipos de número de teléfono](../../concepts/telephony-sms/plan-solution.md)** .

::: zone pivot="programming-language-csharp"
[!INCLUDE [Send SMS with .NET SDK](./includes/send-sms-net.md)]
::: zone-end

::: zone pivot="programming-language-javascript"
[!INCLUDE [Send SMS with JavaScript SDK](./includes/send-sms-js.md)]
::: zone-end

::: zone pivot="programming-language-python"
[!INCLUDE [Send SMS with Python SDK](./includes/send-sms-python.md)]
::: zone-end

::: zone pivot="programming-language-java"
[!INCLUDE [Send SMS with Java SDK](./includes/send-sms-java.md)]
::: zone-end

## <a name="troubleshooting"></a>Solución de problemas

Para solucionar problemas relacionados con la entrega de SMS, puede [habilitar los informes de entrega con Event Grid](./handle-sms-events.md) para capturar los detalles de la entrega.

## <a name="clean-up-resources"></a>Limpieza de recursos

Si quiere limpiar y quitar una suscripción a Communication Services, puede eliminar el recurso o grupo de recursos. Al eliminar el grupo de recursos, también se elimina cualquier otro recurso que esté asociado a él. Obtenga más información sobre la [limpieza de recursos](../create-communication-resource.md#clean-up-resources).

## <a name="next-steps"></a>Pasos siguientes

En esta guía de inicio rápido, ha aprendido a enviar mensajes de texto mediante Azure Communication Services.

> [!div class="nextstepaction"]
> [Recepción de eventos de informes de entrega y SMS](./handle-sms-events.md)

> [!div class="nextstepaction"]
> [Tipos de número de teléfono](../../concepts/telephony-sms/plan-solution.md)

> [!div class="nextstepaction"]
> [Más información acerca de SMS](../../concepts/telephony-sms/concepts.md)
