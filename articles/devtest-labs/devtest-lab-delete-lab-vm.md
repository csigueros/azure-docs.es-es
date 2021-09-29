---
title: Eliminación de un laboratorio o máquina virtual de un laboratorio
description: En este artículo se muestra cómo eliminar un laboratorio o eliminar una VM en un laboratorio mediante Azure Portal (Azure DevTest Labs).
ms.topic: how-to
ms.date: 01/24/2020
ms.openlocfilehash: af8a1691bbd0f34647b7e52a8f05b7acffb86d2b
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/24/2021
ms.locfileid: "128616372"
---
# <a name="delete-a-lab-or-vm-in-a-lab-in-azure-devtest-labs"></a>Eliminación de un laboratorio o de una máquina virtual de un laboratorio en Azure DevTest Labs
Este artículo muestra cómo eliminar un laboratorio o una máquina virtual de un laboratorio.

## <a name="delete-a-lab"></a>Eliminación de un laboratorio
Cuando se elimina una instancia de DevTest Labs de un grupo de recursos, el servicio DevTest Labs realiza las acciones siguientes: 

- Todos los recursos que fueron creados automáticamente en el momento de creación de laboratorio se eliminan automáticamente. No se elimina el grupo de recursos. Si hubiera creado manualmente recursos en este grupo de recursos, el servicio no los elimina. 
- Todas las máquinas virtuales del laboratorio y los grupos de recursos asociados con estas máquinas virtuales se eliminan automáticamente. Cuando se crea una máquina virtual en un laboratorio, el servicio crea los recursos (disco, interfaz de red, dirección IP pública, etc.) para la máquina virtual en un grupo de recursos independiente. Sin embargo, si crea manualmente algún recurso adicional en estos grupos de recursos, el servicio DevTest Labs no elimina esos recursos y el grupo de recursos. 

Para eliminar un laboratorio, realice las siguientes acciones: 

1. Inicie sesión en [Azure Portal](https://portal.azure.com).
2. Seleccione **Todos los recursos** en el menú de la izquierda, seleccione **DevTest Labs** para el tipo de servicio y seleccione el laboratorio.

    ![Selección del laboratorio](media/devtest-lab-delete-lab-vm/select-lab.png)
3. En la página **DevTest Lab**, haga clic en **Eliminar** en la barra de herramientas. 

    ![Botón Eliminar](media/devtest-lab-delete-lab-vm/delete-button.png)
4. En la página **Confirmación**, escriba el **Nombre** del laboratorio y seleccione **Eliminar**. 

    ![Confirm](media/devtest-lab-delete-lab-vm/confirm-delete.png)
5. Para ver el estado de la operación, seleccione el icono de **Notificaciones** (Campana). 

    ![Notificaciones](media/devtest-lab-delete-lab-vm/delete-status.png)

 
## <a name="delete-a-vm-in-a-lab"></a>Eliminación de una máquina virtual de un laboratorio
Si se elimina una máquina virtual de un laboratorio, se eliminan algunos de los recursos (no todos) que se crearon en el momento de creación del laboratorio. No se eliminan los recursos siguientes: 

-   El almacén de claves del grupo de recursos principal
-   El conjunto de disponibilidad, el equilibrador de carga y la dirección IP pública del grupo de recursos de la máquina virtual. Estos recursos se comparten por varias máquinas virtuales de un grupo de recursos. 

La máquina virtual, la interfaz de red y el disco asociado a la máquina virtual se eliminan. 

Para eliminar una máquina virtual de un laboratorio, realice las siguientes acciones: 

1. Inicie sesión en [Azure Portal](https://portal.azure.com).
2. Seleccione **Todos los recursos** en el menú de la izquierda, seleccione **DevTest Labs** para el tipo de servicio y seleccione el laboratorio.

    ![Selección del laboratorio](media/devtest-lab-delete-lab-vm/select-lab.png)
3. Seleccione **... (puntos suspensivos)** para la máquina virtual en la lista de máquinas virtuales y seleccione **Eliminar**. 

    ![Eliminación de la máquina virtual en el menú](media/devtest-lab-delete-lab-vm/delete-vm-menu-in-list.png)
4. En el cuadro de diálogo **Confirmación**, seleccione **Aceptar**. 
5. Para ver el estado de la operación, seleccione el icono de **Notificaciones** (Campana). 

Para eliminar una máquina virtual desde la **página de la máquina virtual**, seleccione **Eliminar** desde la barra de herramientas, como se muestra en la siguiente imagen:

![Eliminación de una máquina virtual desde la página de la máquina virtual](media/devtest-lab-delete-lab-vm/delete-from-vm-page.png) 


## <a name="next-steps"></a>Pasos siguientes
Si desea crear un laboratorio, consulte los artículos siguientes: 

- [Creación de un laboratorio](devtest-lab-create-lab.md)
- [Adición de una máquina virtual al laboratorio](devtest-lab-add-vm.md)