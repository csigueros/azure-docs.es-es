---
title: Inicio rápido de las API de grabación de llamadas con Azure Communication Services
titleSuffix: An Azure Communication Services quickstart document
description: Proporciona un ejemplo de inicio rápido para las API de grabación de llamadas.
author: ravithanneeru
manager: GrantMeStrength
services: azure-communication-services
ms.author: jken
ms.date: 06/30/2021
ms.topic: quickstart
ms.service: azure-communication-services
zone_pivot_groups: acs-csharp-java
ms.openlocfilehash: 4a190e67b04adc858045d32159ad83e430699d30
ms.sourcegitcommit: 2eac9bd319fb8b3a1080518c73ee337123286fa2
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/31/2021
ms.locfileid: "123258995"
---
# <a name="call-recording-api-quickstart"></a>Inicio rápido de las API de grabación de llamadas

[!INCLUDE [Public Preview](../../includes/public-preview-include-document.md)]

Este inicio rápido le permite empezar a grabar llamadas de voz y vídeo. En este inicio rápido se da por supuesto que ya ha utilizado el [SDK del cliente de llamadas](get-started-with-video-calling.md) para crear la experiencia de llamadas del usuario final. Con las **API y SDK del servidor de llamadas** puede habilitar y administrar las grabaciones. 

::: zone pivot="programming-language-csharp"
[!INCLUDE [Build Call Recording server sample with C#](./includes/call-recording-samples/recording-server-csharp.md)]
::: zone-end

::: zone pivot="programming-language-java"
[!INCLUDE [Build Call Recording server sample with Java](./includes/call-recording-samples/recording-server-java.md)]
::: zone-end

## <a name="clean-up-resources"></a>Limpieza de recursos

Si quiere limpiar y quitar una suscripción a Communication Services, puede eliminar el recurso o grupo de recursos. Al eliminar el grupo de recursos, también se elimina cualquier otro recurso que esté asociado a él. Obtenga más información sobre la [limpieza de recursos](../create-communication-resource.md#clean-up-resources).

## <a name="next-steps"></a>Pasos siguientes

Para más información, consulte los siguientes artículos.

- Consulte nuestro [ejemplo de elementos principales de una llamada](../../samples/calling-hero-sample.md).
- Más información sobre la [llamada a las funcionalidades de SDK](./calling-client-samples.md)
- Más información sobre [cómo funciona la llamada](../../concepts/voice-video-calling/about-call-types.md)
