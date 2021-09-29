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
ms.subservice: calling
ms.custom: tracking-python, devx-track-javascript
zone_pivot_groups: acs-js-csharp
ms.openlocfilehash: 7e449fa88a080fd9568436c7806767bac52f1861
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/24/2021
ms.locfileid: "128677157"
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
