---
title: 'Creación de un emparejamiento BGP con un centro virtual (versión preliminar): Azure Portal'
titleSuffix: Azure Virtual WAN
description: Aprenda a crear un emparejamiento BGP con un enrutador de centro de Virtual WAN.
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: conceptual
ms.date: 08/06/2021
ms.author: cherylmc
ms.openlocfilehash: 6c1d845e4f4ad3a61e3131f6451e12070f2af48c
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/13/2021
ms.locfileid: "121781131"
---
# <a name="how-to-create-bgp-peering-with-virtual-hub-preview---azure-portal"></a>Cómo crear un emparejamiento BGP con un centro virtual (versión preliminar): Azure Portal

Este artículo le ayuda a configurar un enrutador de centro de Virtual WAN para su emparejamiento con una aplicación virtual de red (NVA) en la red virtual mediante Azure Portal. El enrutador del centro virtual se aprende las rutas de la NVA en una red virtual de radio que está conectada a un centro de Virtual WAN. El enrutador de centro virtual también anuncia las rutas de red virtual a la NVA. Para más información, consulte [Escenario: emparejamiento BGP con un centro virtual](scenario-bgp-peering-hub.md).

[!INCLUDE [Gated public preview SLA link](../../includes/virtual-wan-gated-public-preview-sla.md)]

:::image type="content" source="./media/create-bgp-peering-hub-portal/diagram.png" alt-text="Diagrama de configuración":::

## <a name="prerequisites"></a>Requisitos previos

> [!IMPORTANT]
> El emparejamiento BGP con la característica de centro de Virtual WAN está actualmente en versión preliminar pública. Si está interesado en probar esta característica, envíe un correo electrónico a **previewbgpwithvhub@microsoft.com** junto con el identificador de recurso de su recurso Virtual WAN. 
>
> Para localizar el id. del recurso, abra Azure Portal, vaya al recurso de Virtual WAN y seleccione **Configuración > Propiedades > Id. del recurso**.<br> Ejemplo: `/subscriptions/<subscriptionID>/resourceGroups/<resourceGroupName>/providers/Microsoft.Network/virtualWans/<virtualWANname>`
>

Antes de comenzar con la configuración, compruebe que se cumplen los criterios siguientes:

[!INCLUDE [Before you begin](../../includes/virtual-wan-before-include.md)]

## <a name="create-a-virtual-wan"></a><a name="openvwan"></a>Creación de una instancia de Virtual WAN

[!INCLUDE [Create a virtual WAN](../../includes/virtual-wan-create-vwan-include.md)]

## <a name="create-a-hub"></a><a name="hub"></a>Creación de un centro de conectividad

Un centro de conectividad es una red virtual que puede contener puertas de enlace para las funcionalidades de sitio a sitio, ExpressRoute o de punto a sitio. Cuando se crea el concentrador, se le cobrará por él, aunque no esté asociado a ningún sitio.

[!INCLUDE [Create a hub](../../includes/virtual-wan-tutorial-s2s-hub-include.md)]

## <a name="connect-the-vnet-to-the-hub"></a><a name="vnet"></a>Conexión de la red virtual al centro de conectividad

En esta sección, creará la conexión entre el centro de conectividad y una red virtual.

[!INCLUDE [Connect a VNet to a hub](../../includes/virtual-wan-connect-vnet-hub-include.md)]

## <a name="configure-a-bgp-peer"></a>Configuración de un par BGP

1.  Abra el [portal de vista previa de Azure](https://aka.ms/azurecortexv2) con [https://aka.ms/azurecortexv2](https://aka.ms/azurecortexv2). El emparejamiento BGP con la característica de centro de Virtual WAN está actualmente en versión preliminar administrada y las páginas de configuración no están disponibles en la versión corriente de Azure Portal.

1.  En la página del portal de la WAN virtual, en la sección **Conectividad**, seleccione **Centros** para ver la lista de centros. Haga clic en un centro para configurar un par BGP.

    :::image type="content" source="./media/create-bgp-peering-hub-portal/hubs.png" alt-text="Captura de pantalla de centros.":::

1.  En la página **Centro virtual**, en la sección **Enrutamiento**, seleccione **Pares BGP** y haga clic en  **+ Agregar** para agregar un par BGP.

    :::image type="content" source="./media/create-bgp-peering-hub-portal/bgp-peers.png" alt-text="3.":::

1.  En la página **Agregar par BGP**, complete todos los campos.

    :::image type="content" source="./media/create-bgp-peering-hub-portal/add-peer.png" alt-text="4.":::

    * **Nombre**: nombre del recurso para identificar un par BGP específico. 
    * **ASN**: el ASN para el par BGP
    * **Dirección IPv4**: la dirección IPv4 del par BGP.
    * **Conexión de red virtual**: elija el id. de conexión que corresponde a la red virtual que hospeda el par BGP.

1.  Haga clic en **Agregar** para completar la configuración del par BGP y ver el par.

    :::image type="content" source="./media/create-bgp-peering-hub-portal/view-peer.png" alt-text="Captura de pantalla del par agregado":::

## <a name="modify-a-bgp-peer"></a>Modificación de un par BGP

1. En el recurso del **centro virtual**, haga clic en **Pares BGP** y seleccione el par BGP. Haga clic en **...** a continuación, haga clic en **Editar**.

    :::image type="content" source="./media/create-bgp-peering-hub-portal/modify.png" alt-text="Captura de pantalla de editar":::

1. Una vez el par BGP se ha modificado, haga clic en **Agregar** para guardar.

## <a name="delete-a-bgp-peer"></a>Eliminación de un par BGP

1. En el recurso del **centro virtual**, haga clic en **Pares BGP** y seleccione el par BGP. Haga clic en **...** después, haga clic en **Eliminar**.

    :::image type="content" source="./media/create-bgp-peering-hub-portal/delete.png" alt-text="Captura de pantalla de la eliminación de un par":::

## <a name="next-steps"></a>Pasos siguientes

* Para más información sobre escenarios BGP, consulte [Escenario: emparejamiento BGP con un centro virtual](scenario-bgp-peering-hub.md).
