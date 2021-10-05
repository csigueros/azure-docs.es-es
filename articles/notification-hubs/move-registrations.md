---
title: Traslado de los recursos de Azure Notification Hubs de una región a otra
description: Obtenga información sobre cómo trasladar los recursos de Azure Notification Hubs a una región de Azure diferente.
author: sethmanheim
ms.author: sethm
ms.service: notification-hubs
ms.topic: how-to
ms.date: 09/07/2021
ms.custom: template-how-to
ms.openlocfilehash: 0de15ab1f16ab5bc234ad6430eae5f65e608753e
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/24/2021
ms.locfileid: "128594636"
---
# <a name="move-resources-between-azure-regions"></a>Traslado de recursos entre regiones de Azure

Obtenga información sobre cómo trasladar los recursos de Azure Notification Hubs a una región de Azure diferente. En un nivel alto, el proceso es el siguiente:

1. Cree un espacio de nombres de destino con un nombre diferente.
1. Exporte los registros del espacio de nombres anterior.
1. Importe los registros en el nuevo espacio de nombres de la región deseada.

## <a name="overview"></a>Información general

En algunos escenarios, es posible que tenga que trasladar recursos de servicio entre regiones de Azure por diversos motivos empresariales. Es posible que se trasladen a una región disponible recientemente. También es posible que desee implementar características o servicios disponibles solo en una región específica, realizar el traslado debido a requisitos internos de cumplimiento o por una directiva, o bien resolver problemas de capacidad.

Los nombres del espacio de nombres de Azure Notification Hubs son únicos, y los registros dependen del centro, por lo que, para realizar este traslado, debe crear un centro en la región deseada y, a continuación, mover los registros junto con todos los demás datos pertinentes al espacio de nombres recién creado.

## <a name="create-a-notification-hubs-namespace-with-a-different-name"></a>Creación de un espacio de nombres de Notification Hubs con un nombre distinto

Siga estos pasos para crear un espacio de nombres de Notification Hubs. Rellene toda la información necesaria en la pestaña **Aspectos básicos**, incluida la región de destino deseada para el espacio de nombres.

[!INCLUDE [notification-hubs-portal-create-new-hub](../../includes/notification-hubs-portal-create-new-hub.md)]

Una vez creado el espacio de nombres, asegúrese de establecer las credenciales PNS en el nuevo espacio de nombres y de crear directivas equivalentes en el nuevo espacio de nombres.

## <a name="exportimport-registrations"></a>Exportación e importación de registros

Una vez creado el espacio de nombres en la región a la que desea trasladar el recurso, exporte todos los registros de forma masiva e impórtelos en el nuevo espacio de nombres. Para ello, vea [Exportación e importación de registros de Azure Notification Hubs en masa](export-modify-registrations-bulk.md).

## <a name="delete-the-previous-namespace-optional"></a>Eliminación del espacio de nombres anterior (opcional)

Después de completar la exportación del registro del espacio de nombres anterior al nuevo espacio de nombres, si lo desea, puede eliminar el espacio de nombres anterior.

1. Vaya al espacio de nombres existente en la región anterior.

2. Haga clic en **Eliminar** y, después, vuelva a escribir el nombre del espacio de nombres en el panel **Eliminar espacio de nombres**.

3. Haga clic en **Eliminar** en la parte inferior del panel **Eliminar espacio de nombres**.

## <a name="next-steps"></a>Pasos siguientes

Los siguientes artículos son ejemplos de otros servicios que tienen un artículo sobre el traslado de región.

- [Traslado de grupos de seguridad de red a otra región](/azure/virtual-network/move-across-regions-nsg-portal)
- [Traslado de direcciones IP públicas a otra región](/azure/virtual-network/move-across-regions-publicip-portal)
- [Traslado de una cuenta de almacenamiento a otra región](/azure/storage/common/storage-account-move?toc=%2Fazure%2Fstorage%2Fblobs%2Ftoc.json&tabs=azure-portal)
- [Traslado de recursos entre regiones (desde un grupo de recursos)](/azure/resource-mover/move-region-within-resource-group#:~:text=1%20In%20the%20Azure%20portal%2C%20open%20the%20relevant,you%20want%20to%20move.%20...%20More%20items...%20)
