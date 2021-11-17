---
title: Uso de etiquetas de servicio
titleSuffix: Azure Web PubSub service
description: Uso de etiquetas de servicio para permitir el tráfico saliente al servicio Azure Web PubSub
author: ArchangelSDY
ms.service: azure-web-pubsub
ms.topic: article
ms.date: 10/21/2021
ms.author: dayshen
ms.openlocfilehash: 2422fb24fd3608d035fe374bd08dcae49321f4f2
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/22/2021
ms.locfileid: "130270180"
---
# <a name="use-service-tags-for-azure-web-pubsub-service"></a>Uso de etiquetas de servicio para el servicio Azure Web PubSub

Puede usar [etiquetas de servicio](../virtual-network/network-security-groups-overview.md#service-tags) para el servicio Azure Web PubSub al configurar [Grupo de seguridad de red](../virtual-network/network-security-groups-overview.md#network-security-groups). Las etiquetas de servicio permiten definir reglas de seguridad de red de entrada o salida para los puntos de conexión del servicio Azure Web PubSub sin necesidad de codificar las direcciones IP.

El servicio Azure Web PubSub administra estas etiquetas de servicio. No puede crear su propia etiqueta de servicio ni modificar una etiqueta existente. Microsoft administra estos prefijos de direcciones para que coincidan con la etiqueta de servicio y actualiza automáticamente dicha etiqueta a medida que las direcciones cambian.

> [!Note]
> A partir del 15 de agosto de 2021, el servicio Azure Web PubSub admite etiquetas de servicio bidireccionales para el tráfico de entrada y salida.

## <a name="use-service-tag-via-azure-cli"></a>Uso de las etiquetas de servicio mediante la CLI de Azure

### <a name="configure-outbound-traffic"></a>Configuración del tráfico de salida

Puede permitir el tráfico de salida al servicio Azure Web PubSub agregando una nueva regla de seguridad de red de salida:

```azurecli-interactive
az network nsg rule create -n <rule-name> --nsg-name <nsg-name> -g <resource-group> --priority 100 --direction Outbound --destination-address-prefixes AzureWebPubSub
```

### <a name="configure-inbound-traffic"></a>Configuración del tráfico entrante

Si usa el [controlador de eventos](concept-service-internals.md#event_handler), también puede permitir el tráfico de entrada desde el servicio Azure Web PubSub agregando una nueva regla de seguridad de red de entrada:

```azurecli-interactive
az network nsg rule create -n <rule-name> --nsg-name <nsg-name> -g <resource-group> --priority 100 --direction Inbound --source-address-prefixes AzureWebPubSub
```

## <a name="next-steps"></a>Pasos siguientes

- [Grupos de seguridad de red: etiquetas de servicio](../virtual-network/network-security-groups-overview.md#security-rules)