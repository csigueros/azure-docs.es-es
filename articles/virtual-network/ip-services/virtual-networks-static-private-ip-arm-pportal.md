---
title: Creación de una máquina virtual con una dirección IP privada estática mediante Azure Portal
description: Obtenga información sobre cómo crear una máquina virtual con una dirección IP privada estática mediante Azure Portal.
author: asudbring
ms.author: allensu
ms.service: virtual-network
ms.subservice: ip-services
ms.topic: how-to
ms.date: 10/01/2021
ms.custom: template-how-to
ms.openlocfilehash: eec4e9e07f19130732219c0932f66a1cfa0fa2b7
ms.sourcegitcommit: 87de14fe9fdee75ea64f30ebb516cf7edad0cf87
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/01/2021
ms.locfileid: "129367712"
---
# <a name="create-a-virtual-machine-with-a-static-private-ip-address-using-the-azure-portal"></a>Creación de una máquina virtual con una dirección IP privada estática mediante Azure Portal

A una máquina virtual se le asigna automáticamente una dirección IP privada de un intervalo que el usuario especifique. Este intervalo se basa en la subred en la que se implementa la máquina virtual. La máquina virtual conserva la dirección hasta que se elimina. Azure asigna dinámicamente la siguiente dirección IP privada disponible desde la subred en la que se crea una máquina virtual. Asigne una dirección IP estática a la máquina virtual si quiere disponer de una dirección IP específica en la subred.

## <a name="prerequisites"></a>Requisitos previos

- Una cuenta de Azure con una suscripción activa. [Cree una cuenta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="create-virtual-machine"></a>Crear máquina virtual

Siga estos pasos para crear una máquina virtual, una red virtual y una subred.

1. Inicie sesión en [Azure Portal](https://portal.azure.com).

2. En el cuadro de búsqueda que aparece en la parte superior del portal, escriba **Máquina virtual**. En los resultados de la búsqueda, seleccione **Máquinas virtuales**.

3. En **Máquinas virtuales**, seleccione **+ Crear** y, a continuación, **+ Máquina virtual**.

4. En **Crear una máquina virtual**, escriba o seleccione esta información:

    | Configuración | Value |
    | ------- | ----- |
    | **Detalles del proyecto** |   |
    | Subscription | Seleccione su suscripción. |
    | Resource group | Seleccione **Crear nuevo**. </br> Escriba **myResourceGroup** en **Nombre**. </br> Seleccione **Aceptar**. |
    | **Detalles de instancia** |   |
    | Nombre de la máquina virtual | Escriba **myVM**. |
    | Region | Seleccione **(EE. UU.) Este de EE. UU. 2**. |
    | Opciones de disponibilidad | Seleccione **No se requiere redundancia de la infraestructura**. |
    | Imagen | Seleccione **Windows Server 2019 Datacenter - Gen2**. |
    | Instancia de Azure Spot | Deje la opción desactivada. |
    | Size | Seleccione un tamaño. |
    | **Cuenta de administrador** |   |
    | Nombre de usuario | Especifique un nombre de usuario. |
    | Contraseña | Escriba una contraseña. |
    | Confirmación de la contraseña | Vuelva a escribir la contraseña. |
    | Puertos de entrada públicos | Seleccione **Permitir los puertos seleccionados**. |
    | Selección de puertos de entrada | Seleccione **RDP (3389)** . |

    :::image type="content" source="./media/virtual-networks-static-private-ip-arm-pportal/create-vm.png" alt-text="Captura de pantalla de la creación de una máquina virtual.":::

    > [!WARNING]
    > Se selecciona el puerto 3389 para permitir el acceso remoto a la máquina virtual Windows Server desde Internet. No se recomienda abrir el puerto 3389 desde Internet para administrar las cargas de trabajo de producción. </br> Para obtener acceso seguro a las máquinas virtuales de Azure, consulte **[¿Qué es Azure Bastion?](../../bastion/bastion-overview.md)** .

3. Seleccione la pestaña **Redes** o seleccione **Siguiente: Discos** y, después, **Siguiente: Redes**.
  
4. En la pestaña Redes, seleccione o escriba:

    | Configuración | Valor |
    |-|-|
    | **Interfaz de red** |  |
    | Virtual network | Acepte el nombre de red predeterminado. |
    | Subnet | Acepte la configuración de subred predeterminada. |
    | Dirección IP pública | Acepte la configuración de IP pública predeterminada. |
    | Grupo de seguridad de red de NIC | Seleccione **Básica**. |
    | Puertos de entrada públicos | Seleccione **Permitir los puertos seleccionados**. |
    | Selección de puertos de entrada | Seleccione **RDP (3389)** . |

5. Seleccione **Revisar + crear**. 
  
6. Revise la configuración y, a continuación, seleccione **Crear**.

[!INCLUDE [ephemeral-ip-note.md](../../../includes/ephemeral-ip-note.md)]

## <a name="change-private-ip-address-to-static"></a>Cambio de la dirección IP privada a estática

En esta sección, cambiará la dirección IP privada de **dinámica** a **estática** para la máquina virtual que creó anteriormente.

1. En el cuadro de búsqueda que aparece en la parte superior del portal, escriba **Máquina virtual**. En los resultados de la búsqueda, seleccione **Máquinas virtuales**.

2. En **Máquinas virtuales**, seleccione **myVM**.

3. En **Configuración**, en **myVM**, seleccione **Redes**.

4. En **Redes**, seleccione el nombre de la interfaz de red junto a **Interfaz de red**. En este ejemplo, el nombre de la NIC es **myvm472**.

    :::image type="content" source="./media/virtual-networks-static-private-ip-arm-pportal/select-nic.png" alt-text="Captura de pantalla de selección de la interfaz de red.":::

5. En las propiedades de interfaz de red, seleccione **Configuraciones de IP** en **Configuración**.

6. En la página **Configuraciones IP**, seleccione **ipconfig1**.

    :::image type="content" source="./media/virtual-networks-static-private-ip-arm-pportal/select-ip-configuration.png" alt-text="Captura de pantalla de selección de la configuración de IP.":::

7. En **Asignación**, seleccione **Estática**. Seleccione **Guardar**.

    :::image type="content" source="./media/virtual-networks-static-private-ip-arm-pportal/select-static-assignment.png" alt-text="Captura de pantalla de la selección de la asignación estática.":::

    > [!NOTE]
    > Si después de seleccionar **Guardar** se da cuenta de que la asignación sigue establecida en **Dinámica**, significa que la dirección IP que ha escrito sigue estando en uso. Pruebe con otra dirección IP.

Para cambiar la dirección IP de nuevo a dinámica, establezca la asignación de la dirección IP privada en **Dinámica** y, a continuación, seleccione **Guardar**.

> [!WARNING]
> Desde dentro del sistema operativo de una VM, no debe asignar estáticamente la IP *privada* asignada a la VM de Azure. Realice la asignación estática de una dirección IP privada solo cuando sea necesario, por ejemplo al [asignar muchas direcciones IP a las VM](virtual-network-multiple-ip-addresses-portal.md). 
>
>Si establece manualmente la dirección IP privada en el sistema operativo, asegúrese de que coincide con la dirección IP privada asignada a la [interfaz de red](virtual-network-network-interface-addresses.md#change-ip-address-settings) de Azure. De lo contrario, puede perder la conectividad a la VM. Más información sobre la configuración de la [dirección IP privada](virtual-network-network-interface-addresses.md#private).

## <a name="clean-up-resources"></a>Limpieza de recursos

Cuando ya no sea necesario, elimine el grupo de recursos y todos los recursos que contiene:

1. Escriba **myResourceGroup** en el cuadro **Buscar** que se encuentra en la parte superior del portal. Seleccione **myResourceGroup** cuando lo vea en los resultados de la búsqueda.

2. Seleccione **Eliminar grupo de recursos**.

3. Escriba **myResourceGroup** para **ESCRIBA EL NOMBRE DEL GRUPO DE RECURSOS:** y seleccione **Eliminar**.

## <a name="next-steps"></a>Pasos siguientes

- Más información sobre [direcciones IP públicas estáticas](public-ip-addresses.md#ip-address-assignment).

- Obtenga más información acerca de las [direcciones IP públicas](public-ip-addresses.md#public-ip-addresses) en Azure.

- Obtenga más información acerca de toda la [configuración de direcciones IP públicas](virtual-network-public-ip-address.md#create-a-public-ip-address).

- Obtener más información acerca de las [direcciones IP privadas](private-ip-addresses.md) y la asignación de una [dirección IP privada estática](virtual-network-network-interface-addresses.md#add-ip-addresses) a una máquina virtual de Azure.

