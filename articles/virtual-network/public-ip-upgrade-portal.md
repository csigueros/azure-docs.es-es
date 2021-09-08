---
title: 'Actualización de una dirección IP pública: Azure Portal'
description: En este artículo, aprenderá a actualizar la dirección IP pública de una SKU básica mediante Azure Portal.
author: asudbring
ms.author: allensu
ms.service: virtual-network
ms.subservice: ip-services
ms.topic: how-to
ms.date: 05/20/2021
ms.custom: template-how-to
ms.openlocfilehash: 2799c71d98fb200aeb5722f2685fb4649045ec9d
ms.sourcegitcommit: beff1803eeb28b60482560eee8967122653bc19c
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 07/07/2021
ms.locfileid: "113439421"
---
# <a name="upgrade-a-public-ip-address-using-the-azure-portal"></a>Actualización de una dirección IP pública mediante Azure Portal

Las direcciones IP públicas de Azure se crean con una SKU básica o estándar. La SKU determina su funcionalidad, lo que incluye el método de asignación, la compatibilidad con características y los recursos a los que se pueden asociar. 

En este artículo, aprenderá a actualizar en Azure Portal la dirección IP pública de una SKU básica estática a una SKU estándar.

## <a name="prerequisites"></a>Requisitos previos

* Una cuenta de Azure con una suscripción activa. [cree una de forma gratuita](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).
* La dirección IP pública de una SKU básica estática en la suscripción. Para más información, consulte el artículo sobre la [creación de una dirección IP pública desde Azure Portal](create-public-ip-portal.md#create-a-basic-sku-public-ip-address).

## <a name="upgrade-public-ip-address"></a>Actualización de una dirección IP pública

En esta sección, iniciará sesión en Azure Portal y actualizará la dirección IP pública de una SKU básica estática a la SKU estándar.

1. Inicie sesión en [Azure Portal](https://portal.azure.com).

2. En el cuadro de búsqueda que aparece en la parte superior del portal, escriba **IP pública**.

3. En los resultados de la búsqueda, escriba **Direcciones IP públicas**.

4. En **Direcciones IP públicas**, seleccione **myBasicPublicIP** o la dirección IP que desea actualizar.

5. Seleccione el banner de actualización en la parte superior de la sección de información general de **myBasicPublicIP**.

    :::image type="content" source="./media/public-ip-upgrade-portal/upgrade-ip-portal.png" alt-text="Actualización de una dirección IP básica en Azure Portal" border="true":::

    > [!NOTE]
    > La dirección IP pública básica que va a actualizar debe tener el tipo de asignación estática. Recibirá la advertencia de que la IP no se puede actualizar si intenta actualizar una dirección IP asignada dinámicamente.

6.  Active la casilla **I acknowledge** (Confirmo). Seleccione **Actualizar**.

    > [!WARNING]
    > No se puede revertir la actualización de una IP pública básica a una SKU estándar. Las IP públicas actualizadas de una SKU básica a estándar siguen sin tener [zonas de disponibilidad](../availability-zones/az-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json#availability-zones) garantizadas.

## <a name="verify-upgrade"></a>Comprobación de la actualización

En esta sección, comprobará que la dirección IP pública es ahora la SKU estándar.

1. Inicie sesión en [Azure Portal](https://portal.azure.com).

2. En el cuadro de búsqueda que aparece en la parte superior del portal, escriba **IP pública**.

3. En los resultados de la búsqueda, escriba **Direcciones IP públicas**.

4. En **Direcciones IP públicas**, seleccione **myBasicPublicIP** o la dirección IP que ha actualizado.

5. Compruebe que la SKU aparece como **Estándar** en la sección **Información general**.

    :::image type="content" source="./media/public-ip-upgrade-portal/verify-upgrade-ip.png" alt-text="Compruebe que la dirección IP pública es una SKU estándar." border="true":::

## <a name="next-steps"></a>Pasos siguientes

En este artículo, actualizará una dirección IP pública de SKU básica a una SKU estándar.

Para más información sobre direcciones IP públicas, consulte:

- [Direcciones IP públicas en Azure](public-ip-addresses.md)
- [Creación de una dirección IP pública: Azure Portal](create-public-ip-portal.md)

