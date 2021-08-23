---
title: 'Inicio rápido: Acceso a retransmisiones mediante el protocolo TURN'
description: Obtenga información sobre cómo recuperar un token STUN/TURN mediante Azure Communication Services
author: shahen
manager: anvalent
services: azure-communication-services
ms.author: shahen
ms.date: 06/30/2021
ms.topic: quickstart
ms.service: azure-communication-services
ms.custom: tracking-python, devx-track-javascript
zone_pivot_groups: acs-js-csharp
ms.openlocfilehash: 88bdfe85dbd2905ff060498003b23909abe7a855
ms.sourcegitcommit: 7d63ce88bfe8188b1ae70c3d006a29068d066287
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 07/22/2021
ms.locfileid: "114471477"
---
# <a name="quickstart-access-turn-relays"></a>Inicio rápido: Acceso a retransmisiones mediante el protocolo TURN

[!INCLUDE [Public Preview Notice](../includes/public-preview-include.md)]

En este inicio rápido se muestra cómo recuperar un token de retransmisión de red para acceder a los servidores TURN de Azure Communication Services.

## <a name="prerequisites"></a>Requisitos previos

- Una cuenta de Azure con una suscripción activa. [Cree su cuenta de forma gratuita](https://azure.microsoft.com/free).
- Un recurso de Azure Communication Services activo, consulte [Creación de un recurso de Communication Services](./create-communication-resource.md) si no tiene uno.

::: zone pivot="programming-language-csharp"
[!INCLUDE [Get a network relay token with .NET](./includes/relay-token-net.md)]
::: zone-end

::: zone pivot="programming-language-javascript"
[!INCLUDE [Get a network relay token with JavaScript](./includes/relay-token-js.md)]
::: zone-end

## <a name="clean-up-resources"></a>Limpieza de recursos

Si quiere limpiar y quitar una suscripción a un recurso de Communication Services, puede eliminar el recurso o grupo de recursos. Al eliminar el grupo de recursos, también se elimina cualquier otro recurso que esté asociado a él. Obtenga más información sobre la [limpieza de recursos](./create-communication-resource.md#clean-up-resources).
