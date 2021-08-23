---
title: 'Tutorial sobre conexión de redes virtuales con emparejamiento de VNet: Azure Portal'
description: En este tutorial, aprenderá a conectar las redes virtuales con emparejamiento de redes virtuales mediante Azure Portal.
services: virtual-network
documentationcenter: virtual-network
author: KumudD
ms.service: virtual-network
ms.devlang: azurecli
ms.topic: tutorial
ms.tgt_pltfrm: virtual-network
ms.workload: infrastructure
ms.date: 07/06/2021
ms.author: kumud
ms.custom: ''
ms.openlocfilehash: ca8829a5f71dabfe33f013c354e083cae08e4566
ms.sourcegitcommit: beff1803eeb28b60482560eee8967122653bc19c
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 07/07/2021
ms.locfileid: "113430760"
---
# <a name="tutorial-connect-virtual-networks-with-virtual-network-peering-using-the-azure-portal"></a>Tutorial: Conexión de redes virtuales con emparejamiento de redes virtuales usando Azure Portal

Puede conectar redes virtuales entre sí con el emparejamiento de redes virtuales. Estas redes virtuales pueden estar en la misma región o en regiones diferentes (también conocidas como emparejamiento de VNET global). Una vez que las redes virtuales están emparejadas, los recursos de ambas se pueden comunicar entre sí con el mismo ancho de banda y la misma latencia que si estuvieran en la misma red virtual. En este tutorial aprenderá a:

> [!div class="checklist"]
> * Crear dos redes virtuales
> * Conectar dos redes virtuales con el emparejamiento de redes virtuales
> * Implementar una máquina virtual (VM) en cada red virtual
> * Comunicarse entre máquinas virtuales

Si lo prefiere, puede completar este tutorial con la [CLI de Azure](tutorial-connect-virtual-networks-cli.md) o [Azure PowerShell](tutorial-connect-virtual-networks-powershell.md).

## <a name="prerequisites"></a>Prerrequisitos

Antes de empezar, debe tener una cuenta de Azure con una suscripción activa. En caso de no tener una, puede [crear una cuenta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="create-virtual-networks"></a>Creación de redes virtuales

1. Inicie sesión en [Azure Portal](https://portal.azure.com).

1. En Azure Portal, seleccione **+ Crear un recurso**.

1. Busque **Red virtual** y seleccione **Crear**.

    :::image type="content" source="./media/tutorial-connect-virtual-networks-portal/create-vnet.png" alt-text="Captura de pantalla de la creación de un recurso para la red virtual":::.

1. En la pestaña **Fundamentos**, escriba o seleccione la siguiente información y acepte los valores predeterminados en las siguientes opciones:

    |Configuración|Value|
    |---|---|
    |Suscripción| Seleccione su suscripción.|
    |Resource group| Haga clic en **Crear nuevo** y escriba *myResourceGroup*.|
    |Region| Seleccione **Este de EE. UU**.|
    |Nombre|myVirtualNetwork1|

    :::image type="content" source="./media/tutorial-connect-virtual-networks-portal/create-basic-tab.png" alt-text="Captura de pantalla de la pestaña &quot;Introducción a la creación de una red virtual&quot;":::.

1. En la pestaña **Direcciones IP**, escriba *10.0.0.0/16* en el campo **Espacio de direcciones**. Haga clic en el botón **Agregar subred** que aparece a continuación y escriba *Subnet1* en **Nombre de subred** y *10.0.0.0/24* en **Intervalo de direcciones de subred**.

    :::image type="content" source="./media/tutorial-connect-virtual-networks-portal/ip-addresses-tab.png" alt-text="Captura de pantalla de la pestaña &quot;Direcciones IP para crear una red virtual&quot;":::.

1. Seleccione **Revisar y crear** y luego **Crear**.
   
1. Repita los pasos del 1 al 5 para crear una segunda red virtual con la siguiente configuración:

    | Valor | Value |
    | --- | --- |
    | Nombre | myVirtualNetwork2 |
    | Espacio de direcciones | 10.1.0.0/16 |
    | Grupo de recursos | Seleccione **Usar existente** y después seleccione **myResourceGroup**.|
    | Nombre de subred | Subnet2 |
    | Intervalo de direcciones de subred | 10.1.0.0/24 |

## <a name="peer-virtual-networks"></a>Emparejamiento de redes virtuales

1. En el cuadro de búsqueda que se encuentra en la parte superior de Azure Portal, busque *myVirtualNetwork1*. Cuando la opción **myVirtualNetwork1** aparezca en los resultados de la búsqueda, selecciónela.

    :::image type="content" source="./media/tutorial-connect-virtual-networks-portal/search-vnet.png" alt-text="Captura de pantalla de la búsqueda de myVirtualNetwork1":::.

1. Seleccione **Emparejamientos** en **Configuración** y, luego, seleccione **+ Agregar**, como se muestra en la imagen siguiente:

    :::image type="content" source="./media/tutorial-connect-virtual-networks-portal/create-peering.png" alt-text="Captura de pantalla de la creación de emparejamientos para myVirtualNetwork1":::.
    
1. Escriba o seleccione la siguiente información, acepte los valores predeterminados para el resto de la configuración y luego seleccione **Agregar**.

    | Valor | Value |
    | --- | --- |
    | Nombre de vínculo de emparejamiento a esta red virtual | Nombre del emparejamiento de myVirtualNetwork1 a la red virtual remota.  *myVirtualNetwork1-myVirtualNetwork2* se usa para esta conexión.|
    | Nombre de vínculo de emparejamiento a esta red virtual remota |  Nombre del emparejamiento de myVirtualNetwork1 a la red virtual remota. *myVirtualNetwork2-myVirtualNetwork1* se usa para esta conexión. |
    | Suscripción | Seleccione su suscripción.|
    | Virtual network  | Puede seleccionar una red virtuales de la misma región o de otra. En el menú desplegable, seleccione *myVirtualNetwork2* |

    :::image type="content" source="./media/tutorial-connect-virtual-networks-portal/peering-settings-bidirectional.png" alt-text="Captura de pantalla de la configuración del emparejamiento de red virtual" lightbox="./media/tutorial-connect-virtual-networks-portal/peering-settings-bidirectional-expanded.png":::.

    El **ESTADO DE EMPAREJAMIENTO** es *Conectado*, como se muestra en la siguiente imagen:

    :::image type="content" source="./media/tutorial-connect-virtual-networks-portal/peering-status-connected.png" alt-text="Captura de pantalla de la configuración del emparejamiento de red virtual":::.

    Si no ve un estado *Conectado*, seleccione el botón **Actualizar**.

## <a name="create-virtual-machines"></a>Creación de máquinas virtuales

Cree una máquina virtual en cada red virtual para que puedan probar la comunicación entre ellas.

### <a name="create-the-first-vm"></a>Creación de la primera máquina virtual

1. En Azure Portal, seleccione **+ Crear un recurso**.

1. Seleccione **Proceso** y, luego, **Crear** en *Máquina virtual*.

    :::image type="content" source="./media/tutorial-connect-virtual-networks-portal/create-vm.png" alt-text="Captura de pantalla de la creación de un recurso para máquinas virtuales":::.

1. Escriba o seleccione la siguiente información en la pestaña **Aspectos básicos**, acepte los valores predeterminados para el resto de la configuración y luego seleccione **Crear**:

    | Valor | Value |
    | --- | --- |
    | Grupo de recursos| Seleccione **Usar existente** y después seleccione **myResourceGroup**. |
    | Nombre | myVm1 |
    | Location | Seleccione **Este de EE. UU**. |
    | Imagen | Seleccione una imagen de sistema operativo. Para esta máquina virtual se selecciona *Windows Server 2019 Datacenter - Gen1*. |
    | Tamaño | Seleccione un tamaño de máquina virtual. Para esta máquina virtual se selecciona *Standard_D2s_v3*. |
    | Nombre de usuario | Especifique un nombre de usuario. Se ha elegido el nombre de usuario *azure* para este ejemplo. |
    | Contraseña | Escriba una contraseña de su elección. La contraseña debe tener al menos 12 caracteres de largo y cumplir con los [requisitos de complejidad definidos](../virtual-machines/windows/faq.yml?toc=%2fazure%2fvirtual-network%2ftoc.json#what-are-the-password-requirements-when-creating-a-vm-). |
   
    :::image type="content" source="./media/tutorial-connect-virtual-networks-portal/create-vm-basic-tab.png" alt-text="Captura de pantalla de la configuración de pestañas básica de la máquina virtual" lightbox="./media/tutorial-connect-virtual-networks-portal/create-vm-basic-tab-expanded.png":::.

1. En la pestaña **Redes**, seleccione los siguientes valores:

    | Valor | Value |
    | --- | --- |
    | Virtual network | myVirtualNetwork1: si no está ya seleccionado, seleccione **Red virtual** y, luego, **myVirtualNetwork1**. |
    | Subnet | Subnet1: si aún no está seleccionado, seleccione **Subred** y, después, **Subnet1**. |
    | Puertos de entrada públicos | *Permitir los puertos seleccionados* |
    | Selección de puertos de entrada | *RDP (3389)* |

    :::image type="content" source="./media/tutorial-connect-virtual-networks-portal/create-vm-networking-tab.png" alt-text="Captura de pantalla de la configuración de pestañas de redes de la máquina virtual" lightbox="./media/tutorial-connect-virtual-networks-portal/create-vm-networking-tab-expanded.png":::.

1. Seleccione **Revisar y crear** y, a continuación, **Crear** para iniciar la implementación de la máquina virtual.

### <a name="create-the-second-vm"></a>Creación de la segunda máquina virtual

Repita los pasos del 1 al 6 para crear una segunda máquina virtual con los cambios siguientes:

| Valor | Value |
| --- | --- |
| Nombre | myVm2 |
| Virtual network | myVirtualNetwork2 |

Las máquinas virtuales tardan unos minutos en crearse. No siga con los pasos restantes hasta que se creen ambas máquinas virtuales.

[!INCLUDE [ephemeral-ip-note.md](../../includes/ephemeral-ip-note.md)]

## <a name="communicate-between-vms"></a>Comunicarse entre máquinas virtuales

1. En el cuadro de búsqueda que aparece en la parte superior del portal, escriba *myVm1*. Seleccione **MyVm1** cuando aparezca en los resultados de búsqueda.
    
    :::image type="content" source="./media/tutorial-connect-virtual-networks-portal/search-vm.png" alt-text="Captura de pantalla de la búsqueda de myVm1":::.

1. Para conectarse a la máquina virtual, seleccione **Conectar** y **RDP** en el menú desplegable. Seleccione **Descargar archivo RDP** para descargar el archivo de escritorio remoto.

    :::image type="content" source="./media/tutorial-connect-virtual-networks-portal/connect-to-virtual-machine.png" alt-text="Captura de pantalla del botón Conectarse a la máquina virtual":::. 

1. Para conectarse a la máquina virtual, abra el archivo RDP descargado. Cuando se le pida, seleccione **Conectar**.

    :::image type="content" source="./media/tutorial-connect-virtual-networks-portal/rdp-connect.png" alt-text="Captura de pantalla de la pantalla de conexión para escritorio remoto":::.

1. Escriba el nombre de usuario y la contraseña que especificó al crear la máquina virtual (puede que deba seleccionar **Más opciones** y luego **Usar una cuenta diferente**, para especificar las credenciales que escribió cuando creó la máquina virtual). A continuación, seleccione **Aceptar**.

    :::image type="content" source="./media/tutorial-connect-virtual-networks-portal/rdp-credentials.png" alt-text="Captura de pantalla de la pantalla de credenciales de RDP":::.

1. Puede recibir una advertencia de certificado durante el proceso de inicio de sesión. Seleccione **Sí** para continuar con la conexión.

1. En un paso posterior, se usa ping para comunicarse con la máquina virtual *myVm1* desde la máquina virtual *myVm2*. Ping usa el Protocolo de mensajes de control de Internet (ICMP) que, de forma predeterminada, se deniega a través del Firewall de Windows. En la máquina virtual *myVm1*, habilite IMCP a través del Firewall de Windows para que pueda hacer ping en esta máquina virtual desde *myVm2* en un paso posterior, mediante PowerShell:

    ```powershell
    New-NetFirewallRule –DisplayName "Allow ICMPv4-In" –Protocol ICMPv4
    ```

    Aunque en este tutorial se usa ping para comunicarse entre máquinas virtuales, no se recomienda permitir que ICMP atraviese el Firewall de Windows en implementaciones de producción.

1. Para conectar con la máquina virtual *myVm2*, escriba el siguiente comando desde el símbolo del sistema en la máquina virtual *myVm1*:

    ```
    mstsc /v:10.1.0.4
    ```
    
1. Puesto que ha habilitado ping en *myVm1*, ahora puede hacer ping a él por dirección IP:

    ```
    ping 10.0.0.4
    ```
    
    :::image type="content" source="./media/tutorial-connect-virtual-networks-portal/myvm2-ping-myvm1.png" alt-text="Captura de pantalla de myVM2 haciendo ping a myVM1":::.

1. Desconecte las sesiones RDP para ambos *myVm1* y *myVm2*.

## <a name="clean-up-resources"></a>Limpieza de recursos

Cuando ya no sea necesario, elimine el grupo de recursos y todos los recursos que contiene: 

1. Escriba *myResourceGroup* en el cuadro **Buscar** que se encuentra en la parte superior del portal. Seleccione **myResourceGroup** cuando lo vea en los resultados de la búsqueda.

1. Seleccione **Eliminar grupo de recursos**.

1. Escriba *myResourceGroup* para **ESCRIBA EL NOMBRE DEL GRUPO DE RECURSOS:** y seleccione **Eliminar**.

    :::image type="content" source="./media/tutorial-connect-virtual-networks-portal/delete-resource-group.png" alt-text="Captura de pantalla de la página de eliminación de un grupo de recursos":::.

## <a name="next-steps"></a>Pasos siguientes

> [!div class="nextstepaction"]
> [Más información acerca del emparejamiento de red virtual](virtual-network-peering-overview.md).
