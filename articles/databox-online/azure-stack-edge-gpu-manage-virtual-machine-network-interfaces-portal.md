---
title: Administración de interfaces de red de VM en Azure Stack Edge Pro mediante Azure Portal
description: Aprenda a administrar interfaces de red en máquinas virtuales implementadas en un GPU de Azure Stack Edge Pro mediante Azure Portal.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 08/02/2021
ms.author: alkohli
ms.openlocfilehash: 27927f1e5f31243d4d2dcbfd9ea1f4da09b8b544
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/13/2021
ms.locfileid: "121736800"
---
# <a name="use-the-azure-portal-to-manage-network-interfaces-on-the-vms-on-your-azure-stack-edge-pro-gpu"></a>Uso de Azure Portal para administrar interfaces de red en las máquinas virtuales del GPU de Azure Stack Edge Pro

[!INCLUDE [applies-to-GPU-and-pro-r-and-mini-r-skus](../../includes/azure-stack-edge-applies-to-gpu-pro-r-mini-r-sku.md)]

Puede crear y administrar máquinas virtuales en un dispositivo Azure Stack Edge con Azure Portal, plantillas, cmdlets de Azure PowerShell y a través de scripts de Python/la CLI de Azure. En este artículo se explica cómo administrar las interfaces de red en una máquina virtual que se ejecuta en el dispositivo Azure Stack Edge mediante Azure Portal.

Cuando se crea una máquina virtual, se especifica una interfaz de red virtual para su creación. Es posible que quiera agregar una o varias interfaces de red a la máquina virtual después de crearla. También puede que quiera cambiar la configuración predeterminada de una interfaz de red existente.

En este artículo se explica cómo agregar una interfaz de red a una máquina virtual existente, cambiar valores existentes, como el tipo de IP (estática frente a dinámica), y desasociar o eliminar una interfaz existente.

        
## <a name="about-network-interfaces-on-vms"></a>Acerca de las interfaces de red de las máquinas virtuales

Una interfaz de red permite que una máquina virtual (VM) que se ejecuta en el dispositivo Azure Stack Edge Pro se comunique con los recursos locales y de Azure. Cuando se habilita un puerto para la red de proceso en el dispositivo, se crea un conmutador virtual en esa interfaz de red. Este conmutador virtual se usa para implementar cargas de trabajo de proceso, como máquinas virtuales o aplicaciones contenedorizadas, en el dispositivo. 

El dispositivo solo admite un conmutador virtual, pero varias interfaces de red virtual. Cada interfaz de red de la máquina virtual tiene asignada una dirección IP estática o dinámica. Con las direcciones IP asignadas a varias interfaces de red en la máquina virtual, en esta se habilitan determinadas capacidades. Por ejemplo, la máquina virtual puede hospedar varios sitios web o servicios con direcciones IP y certificados SSL diferentes en un único servidor. Una máquina virtual en el dispositivo puede actuar como aplicación virtual de red, como un firewall o un equilibrador de carga. <!--Is it possible to do that on ASE?-->

<!--There is a limit to how many virtual network interfaces can be created on the virtual switch on your device. See the Azure Stack Edge Pro limits article for details.--> 


## <a name="prerequisites"></a>Requisitos previos

Para empezar a administrar máquinas virtuales en el dispositivo mediante Azure Portal, asegúrese de que:

1. Tiene acceso a un dispositivo GPU de Azure Stack Edge Pro activado. Ha habilitado una interfaz de red de proceso en el dispositivo. Esta acción crea un conmutador virtual en esa interfaz de red en la máquina virtual. 
    1. En la interfaz de usuario local del dispositivo, vaya a **Proceso**. Seleccione la interfaz de red que va a usar para crear un conmutador virtual.

        > [!IMPORTANT] 
        > Solo puede configurar un puerto para Proceso.

    1. Habilite Proceso en la interfaz de red. Azure Stack Edge Pro GPU crea y administra un conmutador virtual correspondiente a esa interfaz de red.

1. Tiene al menos una máquina virtual implementada en el dispositivo. Para crear esta máquina virtual, vea las instrucciones de [Implementación de máquinas virtuales en el dispositivo Azure Stack Edge Pro con GPU a través de Azure Portal](azure-stack-edge-gpu-deploy-virtual-machine-portal.md).

1. La máquina virtual debería estar en estado **Detenido**. Para detener la máquina virtual, vaya a **Máquinas virtuales** y seleccione la máquina virtual que quiere detener. En la página de **Detalles** de la máquina virtual, seleccione **Detener** y luego **Sí** cuando se le pida confirmación. Antes de agregar, editar o eliminar interfaces de red, debe detener la máquina virtual.

    ![Captura de pantalla que muestra el mensaje de confirmación para detener una máquina virtual en Azure Stack Edge.](./media/azure-stack-edge-gpu-manage-virtual-machine-network-interfaces-portal/stop-vm-2.png)


## <a name="add-a-network-interface"></a>Adición de una interfaz de red

Siga estos pasos para agregar una interfaz de red a una máquina virtual implementada en el dispositivo.<!--There's no obvious way to add a new NIC to a VM or to an Edge resource group in the portal. To update these procedures, I need to create my own test VM, which I can start and stop, create a new NIC for, detach a NIC from the stopped VM, etc.-->

1. Vaya a la máquina virtual que ha detenido y seleccione **Redes**.
    
    ![Captura de pantalla que muestra la pestaña Detalles de una máquina virtual. La etiqueta Redes, que abre la hoja Redes de la máquina virtual, está resaltada.](./media/azure-stack-edge-gpu-manage-virtual-machine-network-interfaces-portal/add-nic-1.png)

2. En la hoja **Redes**, en la barra de comandos, seleccione **+ Agregar interfaz de red**.

    ![Captura de pantalla que muestra la hoja Redes de una máquina virtual. El botón Agregar interfaz de red está resaltado.](./media/azure-stack-edge-gpu-manage-virtual-machine-network-interfaces-portal/add-nic-2.png)

3. En la hoja **Agregar interfaz de red**, escriba los siguientes parámetros:

    |Campo    |Descripción  |
    |---------|-------------|
    |Nombre     | Nombre único dentro del grupo de recursos perimetral. El nombre no se puede cambiar una vez creada la interfaz de red. Para administrar varias interfaces de red con facilidad, use las sugerencias proporcionadas en las [convenciones de nomenclatura](/azure/cloud-adoption-framework/ready/azure-best-practices/naming-and-tagging#resource-naming).     |
    |Seleccione un grupo de recursos permietral |Seleccione el grupo de recursos perimetral al que agregar la interfaz de red.|   
    |Virtual network| Red virtual asociada al conmutador virtual que se ha creado en el dispositivo al habilitar el proceso en la interfaz de red. Solo hay una red virtual asociada al dispositivo.         |
    |Subnet     | Subred dentro de la red virtual seleccionada. Este campo se rellena automáticamente con la subred asociada a la interfaz de red en la que se habilitó el proceso.         |
    |Asignación de dirección IP   | Dirección IP estática o dinámica de la interfaz de red. La dirección IP estática debe ser una dirección IP libre disponible del intervalo de subred especificado. Elija dinámica si hay un servidor DHCP en el entorno.        |

    ![Captura de pantalla que muestra la hoja Agregar interfaz de red para una máquina virtual. El botón Add (Agregar) está resaltado.](./media/azure-stack-edge-gpu-manage-virtual-machine-network-interfaces-portal/add-nic-3.png)

4. Verá una notificación de que la creación de la interfaz de red está en curso.

    ![Captura de pantalla que muestra la notificación de que se está creando una interfaz de red.](./media/azure-stack-edge-gpu-manage-virtual-machine-network-interfaces-portal/add-nic-4.png)

5.  Después de que la interfaz de red se haya creado correctamente, la lista de interfaces de red se actualiza para mostrar la interfaz recién creada.

    ![Captura de pantalla de la hoja Redes de una máquina virtual. La entrada de una máquina virtual recién creada está resaltada.](./media/azure-stack-edge-gpu-manage-virtual-machine-network-interfaces-portal/add-nic-5.png)


## <a name="edit-a-network-interface"></a>Edición de una interfaz de red

Siga estos pasos para editar una interfaz de red asociada a una máquina virtual implementada en el dispositivo.

1. Vaya a la máquina virtual que ha detenido y seleccione **Redes** en **Detalles** de la máquina virtual.

1. En la lista de interfaces de red, seleccione la que quiere editar. En el extremo derecho de la interfaz de red seleccionada, seleccione el icono de edición (lápiz).  

    ![Captura de pantalla que muestra la hoja Redes de una máquina virtual. El nombre de una interfaz de red y el icono de lápiz de la entrada están resaltados.](./media/azure-stack-edge-gpu-manage-virtual-machine-network-interfaces-portal/edit-nic-1.png)

1. En la hoja **Editar la interfaz de red** solo puede cambiar la asignación IP de la interfaz de red. El nombre, el grupo de recursos perimetral, la red virtual y la subred asociados a la interfaz de red no se pueden cambiar una vez creada. Cambie la **Asignación IP** a estática y guarde los cambios.

    ![Captura de pantalla de la hoja Editar interfaz de red de una máquina virtual. El área de asignación de direcciones IP y el botón Guardar están resaltados.](./media/azure-stack-edge-gpu-manage-virtual-machine-network-interfaces-portal/edit-nic-2.png)

1. La lista de interfaces de red se actualiza para mostrar la interfaz de red actualizada.


## <a name="detach-a-network-interface"></a>Desasociación de una interfaz de red

Siga estos pasos para desasociar o quitar una interfaz de red asociada a una máquina virtual implementada en el dispositivo.

1. Vaya a la máquina virtual que ha detenido y seleccione **Redes** en **Detalles** de la máquina virtual.

1. En la lista de interfaces de red, seleccione la que quiere editar. En el extremo derecho de la interfaz de red seleccionada, seleccione el icono de desasociación (desconexión).  

    ![Captura de pantalla de la hoja Redes de una máquina virtual. Se resaltan el nombre de una interfaz de red y el icono de desasociacón.](./media/azure-stack-edge-gpu-manage-virtual-machine-network-interfaces-portal/detach-nic-1.png)

1. Verá un mensaje que le pide que confirme que desea desasociar la interfaz de red. Seleccione **Sí**.

    ![Captura de pantalla que muestra la notificación para confirmar que desea desasociar una interfaz de red de una máquina virtual.](./media/azure-stack-edge-gpu-manage-virtual-machine-network-interfaces-portal/detach-nic-2.png)

    Una vez que la interfaz está completamente desasociada, la lista de interfaces de red se actualiza para mostrar las restantes.


## <a name="delete-a-network-interface"></a>Eliminar una interfaz de red

Siga estos pasos para eliminar una interfaz de red que no esté expuesta a una máquina virtual.

1. Vaya a **Máquinas virtuales** y, a continuación, a la página **Recursos**. Seleccione **Redes**.
    
    ![Captura de pantalla que muestra la pestaña Redes en la página Recursos para máquinas virtuales. Las etiquetas del panel Recursos y la pestaña Redes están resaltadas.](./media/azure-stack-edge-gpu-manage-virtual-machine-network-interfaces-portal/delete-nic-1.png)

1. En la hoja **Redes**, seleccione el icono de eliminar (papelera) al lado de la interfaz de red que desea eliminar. El icono de eliminar solo se muestra para las interfaces de red que no están expuestas a una máquina virtual.

    ![Captura de pantalla que muestra la hoja Redes en recursos de una máquina virtual. Se resalta el icono de eliminar de una interfaz de red no expuesta.](./media/azure-stack-edge-gpu-manage-virtual-machine-network-interfaces-portal/delete-nic-2.png)

1. Verá un mensaje que le pide que confirme que desea eliminar la interfaz de red. La operación no se puede revertir. Seleccione **Sí**.

    ![Captura de pantalla de la notificación en la que se le pide que confirme que desea eliminar una interfaz de red seleccionada.](./media/azure-stack-edge-gpu-manage-virtual-machine-network-interfaces-portal/delete-nic-3.png)
  
    Una vez completada la eliminación de la interfaz de red, la interfaz de red se quita de la lista.

## <a name="next-steps"></a>Pasos siguientes

Para obtener información sobre cómo implementar máquinas virtuales en el dispositivo de Azure Stack Edge Pro, consulte [Implementación de máquinas virtuales a través de Azure Portal](azure-stack-edge-gpu-deploy-virtual-machine-portal.md).
