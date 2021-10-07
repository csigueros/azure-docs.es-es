---
title: 'Configuración de la preferencia de enrutamiento para una dirección IP pública: Azure Portal'
description: Información sobre cómo crear una dirección IP pública con una preferencia de enrutamiento de tráfico de Internet
services: virtual-network
documentationcenter: na
author: asudbring
ms.service: virtual-network
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/22/2021
ms.author: allensu
ms.openlocfilehash: 821a8e5f3211f010489717447b733c17ee61c249
ms.sourcegitcommit: 87de14fe9fdee75ea64f30ebb516cf7edad0cf87
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/01/2021
ms.locfileid: "129367583"
---
# <a name="configure-routing-preference-for-a-public-ip-address-using-the-azure-portal"></a>Configuración de la preferencia de enrutamiento para una dirección IP pública mediante Azure Portal

En este artículo se muestra cómo configurar las [preferencias de enrutamiento](routing-preference-overview.md) a través de la red de ISP (opción de **Internet**) para una dirección IP pública. Después de crear la dirección IP pública, puede asociarla a los siguientes recursos de Azure para el tráfico entrante y saliente de Internet:

* Máquina virtual
* Conjunto de escalado de máquina virtual
* Azure Kubernetes Service (AKS)
* Equilibrador de carga accesible desde Internet
* Application Gateway
* Azure Firewall

De forma predeterminada, la preferencia de enrutamiento de la dirección IP pública se establece en la red global de Microsoft para todos los servicios de Azure y puede asociarse a cualquier servicio de Azure.

Si no tiene una suscripción a Azure, cree una [cuenta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) ahora.

## <a name="create-a-public-ip-address-with-a-routing-preference"></a>Creación de una dirección IP pública con preferencia de enrutamiento
1. Inicie sesión en [Azure Portal](https://portal.azure.com/).
2. Seleccione **Crear un recurso**.
3. En el cuadro de búsqueda, escriba *Dirección IP pública*.
3. En los resultados de la búsqueda, escriba **Dirección IP pública**. A continuación, en la página **Dirección IP pública** seleccione **Crear**.
1. En SKU, seleccione **Estándar**.
1. En **Preferencias de enrutamiento**, seleccione **Internet**.

      ![Creación de una dirección IP pública](./media/routing-preference-portal/public-ip-new.png)
1. En la sección **Configuración de dirección IP IPv4**, escriba o seleccione esta información:

    | Configuración | Valor |
    | ------- | ----- |
    | Subscription | Seleccione su suscripción.|
    | Resource group | Seleccione **Crear nuevo**, escriba *RoutingPreferenceResourceGroup* y, a continuación, seleccione **Aceptar**. |
    | Location | Seleccione **Este de EE. UU**.|
    | Zona de disponibilidad | Mantenga el valor predeterminado: **Con redundancia de zona**. |
1. Seleccione **Crear**.

    > [!NOTE]
    > Las direcciones IP públicas se crean con una dirección IPv4 o IPv6. Sin embargo, la preferencia de enrutamiento solo admite IPV4 actualmente.

Puede asociar la dirección IP pública creada anteriormente con una máquina virtual de [Windows](../../virtual-machines/windows/overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json) o [Linux](../../virtual-machines/linux/overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json). Use la sección CLI en la siguiente página del tutorial: [asociación de una dirección IP pública a una máquina virtual](../../virtual-network/associate-public-ip-address-vm.md#azure-cli) para asociar la dirección IP pública a la VM. Asimismo, puede asociar una dirección IP pública que haya creado con anterioridad con una instancia de [Azure Load Balancer](../../load-balancer/load-balancer-overview.md) asignándola a la configuración de **front-end** del equilibrador de carga. La dirección IP pública actúa como dirección IP virtual (VIP) de carga equilibrada.

## <a name="next-steps"></a>Pasos siguientes
- Obtenga más información sobre las [direcciones IP públicas con preferencia de enrutamiento](routing-preference-overview.md).
- [Configuración de la preferencia de enrutamiento de una VM](../../virtual-network/tutorial-routing-preference-virtual-machine-portal.md).
- [Configuración de la preferencia de enrutamiento para una dirección IP pública mediante PowerShell](routing-preference-powershell.md).
- Obtenga más información acerca de las [direcciones IP públicas](public-ip-addresses.md#public-ip-addresses) en Azure.
- Obtenga más información acerca de toda la [configuración de direcciones IP públicas](virtual-network-public-ip-address.md#create-a-public-ip-address).