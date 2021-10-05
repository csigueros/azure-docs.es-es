---
title: Creación de un host de Bastion con CLI de Azure | Azure Bastion
description: Aprenda a crear y eliminar un host de Bastion mediante la CLI de Azure.
services: bastion
author: cherylmc
ms.service: bastion
ms.topic: how-to
ms.date: 09/22/2021
ms.author: cherylmc
ms.openlocfilehash: d2477c46b79692033baabb5327cda47f6652589d
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/24/2021
ms.locfileid: "128671134"
---
# <a name="create-an-azure-bastion-host-using-azure-cli"></a>Creación de un host de Azure Bastion con CLI de Azure

En este artículo se muestra cómo crear un host de Azure Bastion con CLI de Azure. Una vez que haya aprovisionado el servicio Azure Bastion en la red virtual, ya puede disponer de la completa experiencia de RDP/SSH en todas las máquinas virtuales de la misma red virtual. La implementación de Azure Bastion se realiza por red virtual, no por suscripción o cuenta, ni por máquina virtual.

Opcionalmente, puede crear un host de Azure Bastion mediante los métodos siguientes:
* [Azure Portal](./tutorial-create-host-portal.md)
* [Azure PowerShell](bastion-create-host-powershell.md)

[!INCLUDE [Note about SKU limitations for preview.](../../includes/bastion-preview-sku-note.md)]

## <a name="prerequisites"></a>Requisitos previos

Compruebe que tiene una suscripción a Azure. Si todavía no la tiene, puede activar sus [ventajas como suscriptor de MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details) o registrarse para obtener una [cuenta gratuita](https://azure.microsoft.com/pricing/free-trial).

[!INCLUDE [Cloud Shell CLI](../../includes/vpn-gateway-cloud-shell-cli.md)]

 > [!NOTE]
 > En este momento no se admite el uso de Azure Bastion con las zonas de DNS privado de Azure. Antes de empezar, asegúrese de que la red virtual en la que planea implementar el recurso de Bastion no esté vinculada a una zona de DNS privado.
 >

## <a name="create-a-bastion-host"></a><a name="createhost"></a>Creación de un host de Bastion

Esta sección le ayuda a crear un recurso de Azure Bastion con CLI de Azure.

> [!NOTE]
> Como se muestra en los ejemplos, use el parámetro `--location` con `--resource-group` para cada comando a fin de asegurarse de que los recursos se implementan juntos.

1. Cree una red virtual y una subred Azure Bastion. Debe crear la subred Azure Bastion con el valor de nombre **AzureBastionSubnet**. Este valor permite a Azure saber en qué subred se deben implementar los recursos de Bastion. Esta es diferente de una subred de puerta de enlace de VPN.

   [!INCLUDE [Note about BastionSubnet size.](../../includes/bastion-subnet-size.md)]

   ```azurecli-interactive
   az network vnet create --resource-group MyResourceGroup --name MyVnet --address-prefix 10.0.0.0/16 --subnet-name AzureBastionSubnet --subnet-prefix 10.0.0.0/24 --location northeurope
   ```

2. Cree una dirección IP pública para Azure Bastion. La IP pública es la dirección IP pública del recurso de Bastion en la que se accederá a RDP/SSH (a través del puerto 443). La dirección debe estar en la misma región que el recurso de Bastion que está creando.

   ```azurecli-interactive
   az network public-ip create --resource-group MyResourceGroup --name MyIp --sku Standard --location northeurope
   ```

3. Cree un nuevo recurso Azure Bastion en AzureBastionSubnet de su red virtual. El recurso de Bastion tarda aproximadamente cinco minutos en crearse e implementarse.

   ```azurecli-interactive
   az network bastion create --name MyBastion --public-ip-address MyIp --resource-group MyResourceGroup --vnet-name MyVnet --location northeurope
   ```
## <a name="disassociate-the-vm-public-ip-address"></a>Desasociación de la dirección IP pública de una máquina virtual

Azure Bastion no usa la dirección IP pública para conectarse a la máquina virtual cliente. Si no necesita la dirección IP pública de la máquina virtual, puede desasociar la dirección IP pública mediante los pasos de este artículo: [Desasociación de una dirección IP pública de una máquina virtual de Azure](../virtual-network/remove-public-ip-address-vm.md).

## <a name="next-steps"></a>Pasos siguientes

* Para más información, lea las [P+F sobre Bastion](bastion-faq.md).
* Para usar grupos de seguridad de red con la subred de Azure Bastion, consulte [Trabajo con grupos de seguridad de red](bastion-nsg.md).
