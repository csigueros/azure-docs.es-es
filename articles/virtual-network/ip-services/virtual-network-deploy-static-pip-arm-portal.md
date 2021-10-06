---
title: 'Creación de una máquina virtual con una dirección IP pública estática: Azure Portal'
description: Obtenga información sobre cómo crear una máquina virtual con una dirección IP pública estática mediante Azure Portal.
services: virtual-network
author: asudbring
ms.service: virtual-network
ms.subservice: ip-services
ms.topic: how-to
ms.workload: infrastructure-services
ms.date: 10/01/2020
ms.author: allensu
ms.openlocfilehash: cebaccb67d2731bf4e669b2ab12102ace3a20fcc
ms.sourcegitcommit: 87de14fe9fdee75ea64f30ebb516cf7edad0cf87
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/01/2021
ms.locfileid: "129367865"
---
# <a name="create-a-virtual-machine-with-a-static-public-ip-address-using-the-azure-portal"></a>Creación de una máquina virtual con una dirección IP pública estática mediante Azure Portal

Una dirección IP pública le permite comunicarse con una máquina virtual desde Internet. 

Asigne una dirección IP pública estática, en lugar de una dirección dinámica, para garantizar que la dirección no cambie nunca.   

## <a name="sign-in-to-azure"></a>Inicio de sesión en Azure

Inicie sesión en [Azure Portal](https://portal.azure.com).

## <a name="create-a-virtual-machine"></a>Creación de una máquina virtual

1. En la parte superior izquierda del portal, seleccione **Crear un recurso** > **Proceso** > **Máquina virtual** o **Máquina virtual** en el cuadro de búsqueda.
   
2. En **Crear una máquina virtual**, escriba o seleccione los valores en la pestaña **Básico**:

    | Configuración | Valor                                          |
    |-----------------------|----------------------------------|
    | **Detalles del proyecto** |  |
    | Subscription | Selección de su suscripción a Azure |
    | Grupo de recursos | Seleccione **Crear nuevo**. </br> En **Nombre**, escriba **myResourceGroup**. </br> Seleccione **Aceptar**. |
    | **Detalles de instancia** |  |
    | Nombre de la máquina virtual | Escriba **myVM**. |
    | Region | Seleccione **Este de EE. UU**. |
    | Opciones de disponibilidad | Seleccione **No se requiere redundancia de la infraestructura** |
    | Imagen | Seleccione **Windows Server 2019 Datacenter - Gen1**. |
    | Instancia de Azure Spot | Seleccione **No**. |
    | Size | Elija el tamaño de la máquina virtual o acepte la configuración predeterminada. |
    | **Cuenta de administrador** |  |
    | Nombre de usuario | Escriba un nombre de usuario. |
    | Contraseña | Escriba una contraseña. |
    | Confirmar contraseña | Vuelva a escribir la contraseña. |
    | Puertos de entrada públicos | Seleccione **Permitir los puertos seleccionados**. |
    | Selección de puertos de entrada | Seleccione **RDP (3389)** . |

    > [!WARNING]
    > Se selecciona el puerto 3389 para permitir el acceso remoto a la máquina virtual Windows Server desde Internet. No se recomienda abrir el puerto 3389 desde Internet para administrar las cargas de trabajo de producción. </br> Para obtener acceso seguro a las máquinas virtuales de Azure, consulte **[¿Qué es Azure Bastion?](../../bastion/bastion-overview.md)** .

3. Seleccione la pestaña **Redes** o seleccione **Siguiente: Discos** y, después, **Siguiente: Redes**.
  
4. En la pestaña Redes, seleccione o escriba:

    | Configuración | Valor |
    |-|-|
    | **Interfaz de red** |  |
    | Virtual network | Acepte el nombre de red predeterminado. |
    | Subnet | Acepte la configuración de subred predeterminada. |
    | Dirección IP pública | Seleccione **Crear nuevo**. </br> En **Crear dirección IP pública**, en nombre, escriba **myPublicIP**. </br> En **SKU**, seleccione **Estándar**. </br> En **Asignación**, seleccione **Estática**. </br> Seleccione **Aceptar**.  |
    | Grupo de seguridad de red de NIC | Seleccione **Básica**. |
    | Puertos de entrada públicos | Seleccione **Permitir los puertos seleccionados**. |
    | Selección de puertos de entrada | Seleccione **RDP (3389)** . |
   
5. Seleccione **Revisar + crear**. 
  
6. Revise la configuración y, a continuación, seleccione **Crear**.

[!INCLUDE [ephemeral-ip-note.md](../../../includes/ephemeral-ip-note.md)]

## <a name="clean-up-resources"></a>Limpieza de recursos

Cuando ya no sea necesario, elimine el grupo de recursos y todos los recursos que contiene:

1. Escriba **myResourceGroup** en el cuadro **Buscar** que se encuentra en la parte superior del portal. Seleccione **myResourceGroup** cuando lo vea en los resultados de la búsqueda.

2. Seleccione **Eliminar grupo de recursos**.

3. Escriba **myResourceGroup** para **ESCRIBA EL NOMBRE DEL GRUPO DE RECURSOS:** y seleccione **Eliminar**.

## <a name="next-steps"></a>Pasos siguientes

Consulte [Incorporación, cambio o eliminación de direcciones IP para una interfaz de red de Azure](virtual-network-network-interface-addresses.md).

* Para cambiar una dirección IP pública de dinámica a estática.

* Trabaje con direcciones IP privadas.

Las direcciones IP públicas tienen un [precio simbólico](https://azure.microsoft.com/pricing/details/ip-addresses). Existe un [límite](../../azure-resource-manager/management/azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits) para el número de direcciones IP públicas que puede usar por suscripción.

La SKU de la dirección IP pública de la máquina virtual debe coincidir con la SKU de IP pública de Azure Load Balancer cuando se agrega a un grupo de back-end. Para más información, consulte [Azure Load Balancer](../../load-balancer/skus.md).

Puede descargar la lista de intervalos (prefijos) para las nubes de Azure [Pública](https://www.microsoft.com/download/details.aspx?id=56519), [Gobierno de Estados Unidos](https://www.microsoft.com/download/details.aspx?id=57063), [China](https://www.microsoft.com/download/details.aspx?id=57062) y [Alemania](https://www.microsoft.com/download/details.aspx?id=57064).

- Más información sobre [direcciones IP públicas estáticas](public-ip-addresses.md#ip-address-assignment).

- Obtenga más información acerca de las [direcciones IP públicas](public-ip-addresses.md#public-ip-addresses) en Azure.

- Obtenga más información acerca de toda la [configuración de direcciones IP públicas](virtual-network-public-ip-address.md#create-a-public-ip-address).

- Obtener más información acerca de las [direcciones IP privadas](private-ip-addresses.md) y la asignación de una [dirección IP privada estática](virtual-network-network-interface-addresses.md#add-ip-addresses) a una máquina virtual de Azure.
