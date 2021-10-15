---
title: 'Vínculos privados: Azure Time Series Insights Gen2 | Microsoft Docs'
description: Información general sobre los vínculos privados y la restricción de acceso público en Azure Time Series Insights Gen2.
author: tedvilutis
ms.author: tvilutis
manager: cnovak
ms.workload: big-data
ms.service: time-series-insights
services: time-series-insights
ms.topic: conceptual
ms.date: 09/01/2021
ms.openlocfilehash: 58532e07b80e0084666e6847d5bd62313a51ee76
ms.sourcegitcommit: e8c34354266d00e85364cf07e1e39600f7eb71cd
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/29/2021
ms.locfileid: "129207780"
---
# <a name="private-network-access-with-azure-private-link-preview"></a>Acceso a redes privadas con Azure Private Link (versión preliminar) 

[Azure Private Link](/azure/private-link/private-link-overview)  es un servicio que le permite acceder a recursos de Azure (como  [Azure Event Hubs](/azure/event-hubs/event-hubs-about),  [Azure Storage](/azure/storage/common/storage-introduction) y  [Azure Cosmos DB](/azure/cosmos-db/introduction)) y a los servicios de asociados y clientes hospedados por Azure mediante un punto de conexión privado en la instancia de  [Azure Virtual Network (VNet)](/azure/virtual-network/virtual-networks-overview). 

Del mismo modo, puede usar puntos de conexión privados para la instancia de Time Series Insights a fin de permitir que los clientes ubicados en la red virtual accedan de forma segura a ella mediante Private Link. 

## <a name="about-the-private-endpoints"></a>Acerca de los puntos de conexión privados

El punto de conexión privado usa una dirección IP del espacio de direcciones de la red virtual de Azure. El tráfico de red entre un cliente de la red privada y la instancia de Time Series Insights atraviesa la red virtual y un servicio Private Link en la red troncal de Microsoft, lo que elimina la exposición a la red pública de Internet. Esta es una representación visual del sistema: 

[![DNS de vínculos privados de serie temporal](media/private-links/tsi-dns.png)](media/private-links/tsi-dns.png#lightbox)

Los clientes también pueden bloquear el acceso público al entorno de TSI de forma que solo sea accesible desde la red virtual. La configuración de un punto de conexión privado para la instancia de Time Series Insights le permite protegerla y eliminar la exposición pública, además de evitar la filtración de datos desde la red virtual. 

[![Red de vínculos privados de serie temporal](media/private-links/tsi-network-access.png)](media/private-links/tsi-network-access.png#lightbox)

Una vez que se ha habilitado un punto de conexión privado y se ha restringido el acceso público, el cliente tendrá que usar una dirección diferente para que el explorador de TSI acceda al entorno de TSI. Esa dirección se puede encontrar en la sección Información general de Azure Portal. 

## <a name="next-steps"></a>Pasos siguientes

* Para más información, consulte [Configuración de puntos de conexión privados para un entorno de TSI](./how-to-private-links.md). 