---
title: Uso de etiquetas de servicio
titleSuffix: Azure SignalR Service
description: Uso de etiquetas de servicio para permitir el tráfico saliente a Azure SignalR Service
services: signalr
author: ArchangelSDY
ms.service: signalr
ms.topic: article
ms.date: 05/06/2020
ms.author: dayshen
ms.openlocfilehash: fdb87dab5f2dc105da28ca097f81ff83c0dce3b3
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/13/2021
ms.locfileid: "121735642"
---
# <a name="use-service-tags-for-azure-signalr-service"></a>Uso de etiquetas de servicio para Azure SignalR Service

Puede usar [etiquetas de servicio](../virtual-network/network-security-groups-overview.md#service-tags) para Azure SignalR Service al configurar [Grupo de seguridad de red](../virtual-network/network-security-groups-overview.md#network-security-groups). Permite definir una regla de seguridad de red de entrada/salida para los puntos de conexión de Azure SignalR Service sin necesidad de codificar direcciones IP.

Azure SignalR Service administra estas etiquetas de servicio. No puede crear su propia etiqueta de servicio ni modificar una etiqueta existente. Microsoft administra estos prefijos de direcciones para que coincidan con la etiqueta de servicio y actualiza automáticamente dicha etiqueta a medida que las direcciones cambian.

> [!Note]
> A partir del 15 de agosto de 2021, Azure SignalR Service admite la etiqueta de servicio bidireccional para el tráfico entrante y saliente.

## <a name="use-service-tag-on-portal"></a>Uso de la etiqueta de servicio en el portal

### <a name="configure-outbound-traffic"></a>Configuración del tráfico de salida

Puede permitir el tráfico saliente a Azure SignalR Service si agrega una nueva regla de seguridad de red de salida:

1. Vaya al grupo de seguridad de red.

1. Haga clic en el menú configuración denominado **Reglas de seguridad de salida**.

1. Haga clic en el botón **+ Agregar** en la parte superior.

1. Elija **Etiqueta de servicio** en **Destino**.

1. Elija **AzureSignalR** en **Etiqueta de servicio de destino**.

1. Rellene **443** en **Intervalos de puertos de destino**.

    ![Creación de una regla de seguridad de salida](media/howto-service-tags/portal-add-outbound-security-rule.png)

1. Ajuste otros campos según sus necesidades.

1. Haga clic en **Agregar**.

### <a name="configure-inbound-traffic"></a>Configuración del tráfico entrante

Si tiene elementos ascendentes, también puede permitir el tráfico entrante desde Azure SignalR Service agregando una nueva regla de seguridad de red de entrada:

1. Vaya al grupo de seguridad de red.

1. Haga clic en el menú configuración denominado **Reglas de seguridad de entrada**.

1. Haga clic en el botón **+ Agregar** en la parte superior.

1. Elija **Etiqueta de servicio** en **Origen**.

1. Elija **AzureSignalR** en **Etiqueta de servicio de origen**.

1. Rellene **\* *_ en _* Intervalos de puertos de origen**.

   :::image type="content" alt-text="Creación de una regla de seguridad de entrada" source="media/howto-service-tags/portal-add-inbound-security-rule.png" :::


1. Ajuste otros campos según sus necesidades.

1. Haga clic en **Agregar**.

## <a name="next-steps"></a>Pasos siguientes

- [Grupos de seguridad de red: etiquetas de servicio](../virtual-network/network-security-groups-overview.md#security-rules)