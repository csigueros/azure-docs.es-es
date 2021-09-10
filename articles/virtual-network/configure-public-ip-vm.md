---
title: Administración de una dirección IP pública con una máquina virtual de Azure
titleSuffix: Azure Virtual Network
description: Conozca las formas en que se usa una dirección IP pública con Azure Virtual Machines y cómo cambiar la configuración.
author: asudbring
ms.author: allensu
ms.service: virtual-network
ms.subservice: ip-services
ms.topic: how-to
ms.date: 06/28/2021
ms.custom: template-how-to
ms.openlocfilehash: 2271786f2f69e6894637ad3d7dd67f8415943a11
ms.sourcegitcommit: beff1803eeb28b60482560eee8967122653bc19c
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 07/07/2021
ms.locfileid: "113439513"
---
# <a name="manage-a-public-ip-address-with-an-azure-virtual-machine"></a>Administración de una dirección IP pública con una máquina virtual de Azure

Las direcciones IP públicas están disponibles en dos SKU: estándar y básica. La selección de la SKU determina las características de la dirección IP. La SKU determina los recursos a los que se puede asociar la dirección IP.  

Azure Virtual Machines es el servicio de proceso principal de Azure. Los clientes pueden crear máquinas virtuales Linux o Windows. Se puede asignar una dirección IP pública a una máquina virtual para las conexiones entrantes a la máquina virtual. 

Una máquina virtual no requiere una dirección IP pública para su configuración.

En este artículo, aprenderá a crear una máquina virtual de Azure mediante una dirección IP pública existente en su suscripción. Aprenderá a agregar una dirección IP pública a una máquina virtual. Cambiará la dirección IP. Por último, aprenderá a quitar la IP pública.

## <a name="prerequisites"></a>Requisitos previos

- Una cuenta de Azure con una suscripción activa. [cree una de forma gratuita](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).
- Dos IP públicas de SKU estándar en la suscripción. La dirección IP no puede estar asociada a ningún recurso. Para más información sobre cómo crear una IP pública de SKU estándar, consulte [Creación de una dirección IP pública: Azure Portal](create-public-ip-portal.md).
    - Para seguir los ejemplos de este artículo, asigne a las nuevas direcciones IP públicas los nombres **myStandardPublicIP-1** y **myStandardPublicIP-2**.
- Una dirección IP pública de SKU estándar con la preferencia de enrutamiento de **Internet** en la suscripción. Para más información sobre cómo crear una IP pública con la preferencia de enrutamiento de **Internet**, consulte [Configuración de la preferencia de enrutamiento para una dirección IP pública mediante Azure Portal](./routing-preference-portal.md).
    - Para seguir el ejemplo de este artículo, asigne a la nueva dirección IP pública el nombre **myStandardPublicIP-3**.
## <a name="create-virtual-machine-existing-public-ip"></a>Creación de una máquina virtual con una IP pública existente

En esta sección, creará una máquina virtual. Seleccionará la dirección IP que creó en los requisitos previos como IP pública de la máquina virtual.

1. Inicie sesión en [Azure Portal](https://portal.azure.com).

2. En el cuadro de búsqueda que aparece en la parte superior del portal, escriba **Máquina virtual**.

3. En los resultados de la búsqueda, seleccione **Máquinas virtuales**.

4. Seleccione **+ Agregar** y, luego, **+ Máquina virtual**.

5. En **Crear una máquina virtual**, escriba o seleccione esta información:

    | Configuración | Value |
    | ------- | ----- |
    | **Detalles del proyecto** |   |
    | Suscripción | Seleccione su suscripción. |
    | Resource group | Seleccione **Crear nuevo**. </br> Escriba **myResourceGroupVM**. </br> Seleccione **Aceptar**. |
    | **Detalles de instancia** |   |
    | Nombre de la máquina virtual | Escriba **myVM**. |
    | Region | Seleccione **(EE. UU.) Oeste de EE. UU. 2**. | 
    | Opciones de disponibilidad | Seleccione **No se requiere redundancia de la infraestructura**. |
    | Imagen | Seleccione **Windows Server 2019 Datacenter - Gen1**. |
    | Instancia de Azure Spot  | Deje esta casilla desactivada, tal y como está de forma predeterminada. |
    | Size | Seleccione un tamaño para la máquina virtual. |
    | **Cuenta de administrador** |   |
    | Nombre de usuario | Especifique un nombre de usuario. |
    | Contraseña | Escriba una contraseña. |
    | Confirmar contraseña | Confirme la contraseña. |
    | **Reglas de puerto de entrada** |   |
    | Puertos de entrada públicos | Deje el valor predeterminado **Permitir los puertos seleccionados**. |
    | Selección de puertos de entrada | Deje el valor predeterminado, **RDP (3389)** . |

6. Seleccione la pestaña **Redes** o elija **Siguiente: Discos**, luego, **Siguiente: Redes**.

7. En la pestaña **Redes**, escriba o seleccione la siguiente información:

    | Configuración | Valor |
    | ------- | ----- |
    | **Interfaz de red** |   |
    | Virtual network | Deje el valor predeterminado de **(new) myResourceGroupVM-vnet**. |
    | Subred | Deje el valor predeterminado de **(new) default (10.1.0.0/24)** . |
    | Dirección IP pública | Seleccione **myStandardPublicIP-1**. |
    | Grupo de seguridad de red de NIC | Deje el valor predeterminado de **Básico**. |
    | Puertos de entrada públicos | Deje el valor predeterminado **Permitir los puertos seleccionados**. |
    | Selección de puertos de entrada | Deje el valor predeterminado, **RDP (3389)** . |

6. Seleccione la pestaña **Revisar y crear** o seleccione el botón **Revisar y crear**.

7. Seleccione **Crear**.

> [!NOTE]
> Esta es una implementación sencilla de una máquina virtual de Azure. En el caso de una configuración e instalación avanzadas, consulte [Inicio rápido: Creación de una máquina virtual Windows en Azure Portal](../virtual-machines/windows/quick-create-portal.md).
>
> Para más información sobre Azure Virtual Machines, consulte [Máquinas virtuales Windows en Azure](../virtual-machines/windows/overview.md).

## <a name="change-public-ip-address"></a>Cambio de la dirección IP pública

En esta sección, cambiará la dirección IP pública asociada a la configuración de IP pública predeterminada de la máquina virtual.

1. En el cuadro de búsqueda que aparece en la parte superior del portal, escriba **Máquina virtual**.

2. En los resultados de la búsqueda, seleccione **Máquinas virtuales**.

3. En **Máquinas virtuales**, seleccione **myVM**.

4. En **Configuración**, en **myVM**, seleccione **Redes**.

5. En **Redes**, seleccione el valor de **Inetrfaz de red** de la máquina virtual. El nombre de la NIC tendrá como prefijo el nombre de la máquina virtual y finalizará con un número aleatorio.  En este ejemplo, es **myvm793**.

    :::image type="content" source="./media/configure-public-ip-vm/network-interface.png" alt-text="Selección de la interfaz de red." border="true":::

6. En la hoja **Configuración** de la interfaz de red, seleccione **Configuraciones IP**.

7. En **Configuraciones IP**, seleccione **ipconfig1**.  

    :::image type="content" source="./media/configure-public-ip-vm/change-ipconfig.png" alt-text="Selección de ipconfig para cambiar la dirección IP." border="true":::

1. En **Dirección IP pública** de **ipconfig1**, seleccione **myStandardPublicIP-2**.

7. Seleccione **Guardar**.

## <a name="add-public-ip-configuration"></a>Adición de una configuración de IP pública

En esta sección, agregará una configuración de IP pública a la máquina virtual. 

Para más información sobre cómo agregar varias direcciones IP, consulte [Asignación de varias direcciones IP a máquinas virtuales mediante Azure Portal](./virtual-network-multiple-ip-addresses-portal.md). 

Para más información sobre el uso de ambos tipos de preferencias de enrutamiento, consulte [Configuración de ambas opciones de preferencia de enrutamiento para una máquina virtual](./routing-preference-mixed-network-adapter-portal.md).

1. En el cuadro de búsqueda que aparece en la parte superior del portal, escriba **Máquina virtual**.

2. En los resultados de la búsqueda, seleccione **Máquinas virtuales**.

3. En **Máquinas virtuales**, seleccione **myVM**.

4. En **Configuración**, en **myVM**, seleccione **Redes**.

5. En **Redes**, seleccione el valor de **Interfaz de red** de la máquina virtual. El nombre de la NIC tendrá como prefijo el nombre de la máquina virtual y finalizará con un número aleatorio.  En este ejemplo, es **myvm793**.

    :::image type="content" source="./media/configure-public-ip-vm/network-interface.png" alt-text="Selección de la interfaz de red." border="true":::

6. En la hoja **Configuración** de la interfaz de red, seleccione **Configuraciones IP**.

7. En **Configuraciones IP**, seleccione **+ Agregar**.

8. En **Nombre**, escriba **ipconfig2**.

9. En **Dirección IP pública**, seleccione **Asociar**.

10. En **Dirección IP pública**, seleccione **myStandardPublicIP-3**.

11. Seleccione **Aceptar**.

## <a name="remove-public-ip-address-association"></a>Eliminación de la asociación de la dirección IP pública

En esta sección, quitará la dirección IP pública de la interfaz de red. Después de este proceso, la máquina virtual no estará disponible para las conexiones externas.

1. En el cuadro de búsqueda que aparece en la parte superior del portal, escriba **Máquina virtual**.

2. En los resultados de la búsqueda, seleccione **Máquinas virtuales**.

3. En **Máquinas virtuales**, seleccione **myVM**.

4. En **Configuración**, en **myVM**, seleccione **Redes**.

5. En **Redes**, seleccione el valor de **Interfaz de red** de la máquina virtual. El nombre de la NIC tendrá como prefijo el nombre de la máquina virtual y finalizará con un número aleatorio.  En este ejemplo, es **myvm793**.

    :::image type="content" source="./media/configure-public-ip-vm/network-interface.png" alt-text="Selección de la interfaz de red." border="true":::

6. En la hoja **Configuración** de la interfaz de red, seleccione **Configuraciones IP**.

7. En **Configuraciones IP**, seleccione **ipconfig1**.  

    :::image type="content" source="./media/configure-public-ip-vm/change-ipconfig.png" alt-text="Selección de ipconfig para cambiar la dirección IP." border="true":::

8. En **Configuración de dirección IP pública**, seleccione **Desasociar**.

9. Seleccione **Guardar**.

## <a name="next-steps"></a>Pasos siguientes

En este artículo, ha aprendido a crear una máquina virtual y a usar una dirección IP pública existente. Ha cambiado la IP pública de la configuración IP predeterminada. Por último, ha agregado una configuración de IP pública al firewall con la preferencia de enrutamiento de Internet.

- Para más información sobre las IP públicas en Azure, consulte [Direcciones IP públicas](public-ip-addresses.md).
