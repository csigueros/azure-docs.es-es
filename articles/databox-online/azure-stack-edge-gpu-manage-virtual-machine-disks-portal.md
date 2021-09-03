---
title: Administración de discos de máquinas virtuales en Azure Stack Edge Pro GPU, Pro R y Mini R a través de Azure Portal
description: Obtenga información sobre cómo administrar discos, incluido cómo agregar, desasociar y eliminar discos de datos en máquinas virtuales implementadas en Azure Stack Edge Pro GPU, Azure Stack Edge Pro R y Azure Stack Edge Mini R mediante Azure Portal.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 08/02/2021
ms.author: alkohli
Customer intent: As an IT admin, I need to understand how to manage disks on a VM running on an Azure Stack Edge Pro device so that I can use it to run applications using Edge compute before sending it to Azure.
ms.openlocfilehash: 8b9be47fb87c626bb5e6f2f20054f6cf249a57d0
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/13/2021
ms.locfileid: "121732965"
---
# <a name="use-the-azure-portal-to-manage-disks-on-the-vms-on-your-azure-stack-edge-pro-gpu"></a>Uso de Azure Portal para administrar discos en las máquinas virtuales de Azure Stack Edge Pro GPU

[!INCLUDE [applies-to-GPU-and-pro-r-and-mini-r-skus](../../includes/azure-stack-edge-applies-to-gpu-pro-r-mini-r-sku.md)]

Puede aprovisionar discos en las máquinas virtuales (VM) implementadas en el dispositivo Azure Stack Edge Pro mediante Azure Portal. Los discos se aprovisionan en el dispositivo a través de la instancia local de Azure Resource Manager y consumen la capacidad del dispositivo. Las operaciones, como agregar, desasociar y eliminar un disco, se pueden realizar en Azure Portal, que a su vez realiza llamadas a la instancia local de Azure Resource Manager para aprovisionar el almacenamiento. 

En este artículo se explica cómo agregar, desasociar, quitar y eliminar discos de datos en una máquina virtual existente, así como la forma de cambiar el tamaño de la propia máquina virtual, mediante Azure Portal.

        
## <a name="about-disks-on-vms"></a>Acerca de los discos en máquinas virtuales

La máquina virtual puede tener un disco de sistema operativo y un disco de datos. Cada máquina virtual implementada en el dispositivo tiene un disco de sistema operativo asociado. Ese disco tiene un sistema operativo instalado previamente, que se seleccionó cuando se creó la máquina virtual. Este disco contiene el volumen de arranque.

> [!NOTE]
> No se puede cambiar el tamaño del disco del sistema operativo de la máquina virtual implementada en el dispositivo. El tamaño del disco del sistema operativo viene determinado por el tamaño de la máquina virtual seleccionado.

Por otro lado, un disco de datos es un disco administrado asociado a la máquina virtual que se ejecuta en el dispositivo. Un disco de datos se utiliza para almacenar los datos de aplicación. Los discos de datos suelen ser unidades SCSI. El tamaño de la máquina virtual determina el número de discos que se pueden asociar a una máquina virtual. De forma predeterminada, se usa el almacenamiento prémium para hospedar los discos.

A veces, una máquina virtual implementada en el dispositivo puede contener un disco temporal. El disco temporal proporciona almacenamiento a corto plazo para aplicaciones y procesos, y está destinado únicamente a almacenar datos como archivos de paginación o de intercambio. Los datos del disco temporal pueden perderse durante un evento de mantenimiento o cuando vuelva a implementar una máquina virtual. Durante un reinicio estándar correcto de la máquina virtual, se conservarán los datos de la unidad temporal. 


## <a name="prerequisites"></a>Requisitos previos

Antes de empezar a crear y a administrar discos en las máquinas virtuales que se ejecutan en el dispositivo a través de Azure Portal, debe asegurarse de que:


1. Tiene acceso a un dispositivo Azure Stack Edge Pro GPU activado. También ha habilitado una interfaz de red para el proceso en el dispositivo. Esta acción crea un conmutador virtual en la interfaz de red de la máquina virtual. 
    1. En la interfaz de usuario local del dispositivo, vaya a **Proceso**. Seleccione la interfaz de red que va a usar para crear un conmutador virtual.

        > [!IMPORTANT] 
        > Solo puede configurar un puerto para Proceso.

    1. Habilite Proceso en la interfaz de red. Azure Stack Edge Pro GPU crea y administra un conmutador virtual correspondiente a esa interfaz de red.

1. Tiene al menos una máquina virtual implementada en el dispositivo. Para crear esta máquina virtual, consulte las instrucciones en [Implementación de la máquina virtual en Azure Stack Edge Pro a través de Azure Portal](azure-stack-edge-gpu-deploy-virtual-machine-portal.md).


## <a name="add-a-data-disk"></a>Agregar un disco de datos

Siga estos pasos para agregar un disco a una máquina virtual implementada en el dispositivo.

1. Vaya a la máquina virtual a la que quiere agregar un disco de datos y seleccione **Discos** en los **Detalles** de la máquina virtual.
    
    ![Captura de pantalla de la configuración de Discos en la pestaña Detalles de una máquina virtual de Azure Stack Edge. La etiqueta Discos está resaltada.](./media/azure-stack-edge-gpu-manage-virtual-machine-disks-portal/add-data-disk-1.png)

1. En la hoja **Discos**, en **Discos de datos**, seleccione **Crear y adjuntar un nuevo disco**.

    ![Captura de pantalla de la hoja Discos en la vista Información general de Virtual Machines. La opción Crear y adjuntar un nuevo disco, en Discos de datos, está resaltada.](./media/azure-stack-edge-gpu-manage-virtual-machine-disks-portal/add-data-disk-2.png)

1. En la hoja **Crear un disco**, escriba los parámetros siguientes:

    
    |Campo  |Descripción  |
    |---------|---------|
    |Nombre     | Nombre único dentro del grupo de recursos. El nombre no se puede cambiar después de crear el disco de datos.     |
    |Grupo de recursos de Edge |Escriba el grupo de recursos de Edge en el que se va a almacenar el nuevo disco.|
    |Size| El tamaño del disco de datos en GiB. El tamaño máximo de un disco de datos viene determinado por el tamaño de la máquina virtual que haya seleccionado. Al aprovisionar un disco, también debe tener en cuenta el espacio real en el dispositivo y las otras cargas de trabajo de máquina virtual que se ejecutan y que consumen capacidad.  |         

    ![Captura de pantalla de la hoja Crear un disco para una máquina virtual. El botón Aceptar está resaltado.](./media/azure-stack-edge-gpu-manage-virtual-machine-disks-portal/add-data-disk-3.png)

    Seleccione **Aceptar** y continúe.

1. En la vista **Discos**, verá una entrada correspondiente al nuevo disco. Acepte el valor predeterminado o asigne un número de unidad lógica (LUN) válido al disco y seleccione **Guardar**. Un LUN es un identificador único de un disco SCSI. Para obtener más información, consulte [¿Qué es un número de unidad lógica?](../virtual-machines/linux/azure-to-guest-disk-mapping.md#what-is-a-lun).

    ![Captura de pantalla de la vista Discos para máquinas virtuales después de agregar un disco nuevo. El número de disco del nuevo disco y el botón Guardar están resaltados.](./media/azure-stack-edge-gpu-manage-virtual-machine-disks-portal/add-data-disk-4.png)

1. Verá una notificación de que la creación del disco está en curso. Una vez creado correctamente el disco, se actualiza la máquina virtual. 

    ![Captura de pantalla que muestra la notificación de que la creación del disco está en curso.](./media/azure-stack-edge-gpu-manage-virtual-machine-disks-portal/add-data-disk-5.png)

1. Vuelva a la página **Detalles** de la máquina virtual. La lista de discos se actualiza para mostrar el disco de datos recién creado.

    ![Captura de pantalla de la pestaña Detalles de una máquina virtual. Un disco de máquina virtual recién creado está resaltado en el área Discos.](./media/azure-stack-edge-gpu-manage-virtual-machine-disks-portal/add-data-disk-6.png)


## <a name="change-a-data-disk"></a>Cambio de un disco de datos

Siga estos pasos para cambiar un disco asociado a una máquina virtual implementada en el dispositivo.

1. Vaya a la máquina virtual que tiene el disco de datos que desea modificar y seleccione **Discos** en los **Detalles** de la máquina virtual.

1. En la lista de discos de datos, seleccione el disco que quiere cambiar. En el extremo derecho del disco seleccionado, seleccione el icono de edición (lápiz).  

    ![Captura pantalla de Discos en una máquina virtual. La sección Discos de datos y el icono del lápiz, que se usa para editar un disco de datos, están resaltados.](./media/azure-stack-edge-gpu-manage-virtual-machine-disks-portal/edit-data-disk-1.png)

1. En la hoja **Change disk** (Cambiar disco), solo puede cambiar el tamaño del disco. Una vez creado, no se puede cambiar el nombre de un disco. Modifique el **Tamaño** del disco y guarde los cambios.

    ![Captura de pantalla de la hoja Cambiar disco para una máquina virtual. La opción Tamaño y el botón Aceptar están resaltados.](./media/azure-stack-edge-gpu-manage-virtual-machine-disks-portal/edit-data-disk-2.png)

    > [!NOTE]
    > Solo puede expandir un disco de datos. No puede reducir el disco.

1. En la vista **Discos**, se actualiza la lista de discos para mostrar el disco actualizado.


## <a name="attach-an-existing-disk"></a>un disco existente

Siga estos pasos para asociar un disco existente a la máquina virtual implementada en el dispositivo.

1. Vaya a la máquina virtual a la que quiere adjuntar el disco existente y seleccione **Discos** en los **Detalles** de la máquina virtual.

1. En la hoja **Discos**, en **Discos de datos**, seleccione **Asociar disco existente**.

    ![Captura de pantalla de la hoja Discos de una máquina virtual de Azure Stack Edge. La opción "Asociar un disco existente" está resaltada.](./media/azure-stack-edge-gpu-manage-virtual-machine-disks-portal/attach-existing-data-disk-1.png)

1. Acepte el LUN predeterminado o asigne un LUN válido. Elija un disco de datos existente en la lista desplegable. Seleccione **Guardar**.

    ![Captura de pantalla de la hoja Discos de una máquina virtual de Azure Stack Edge después de agregar un disco existente. El número LUN y el nombre del disco están resaltados, al igual que el botón Guardar.](./media/azure-stack-edge-gpu-manage-virtual-machine-disks-portal/attach-existing-data-disk-2.png)

    Seleccione **Guardar** y continúe.

1. Verá una notificación de que la máquina virtual está actualizada. Una vez actualizada la máquina virtual, vuelva a la página **Detalles** de la máquina virtual. Actualice la página para ver el disco recién conectado en la lista de discos de datos.

    ![Captura de pantalla en la que se muestra la sección Discos de datos del panel Detalles de una máquina virtual. La entrada de un disco de datos está resalta.](./media/azure-stack-edge-gpu-manage-virtual-machine-disks-portal/list-data-disks-2.png)


## <a name="detach-a-data-disk"></a>Desacoplar un disco de datos

Siga estos pasos para desasociar o quitar un disco de datos asociado a una máquina virtual implementada en el dispositivo.

> [!NOTE]
> - Puede quitar un disco de datos mientras se ejecuta la máquina virtual. Asegúrese de que no haya nada que use activamente el disco antes de desasociarlo de la máquina virtual.
> - Si se desasocia un disco, no se elimina automáticamente. Siga los pasos de la sección [Eliminar un disco de datos](#delete-a-data-disk) que encontrará más adelante.

1. Vaya a la máquina virtual de la que quiere desasociar un disco de datos y seleccione **Discos** en los **Detalles** de la máquina virtual.

    ![Captura de pantalla en la que se muestra la sección Discos de datos del panel Detalles de una máquina virtual. Las entradas de disco en Discos de datos están resaltadas.](./media/azure-stack-edge-gpu-manage-virtual-machine-disks-portal/list-data-disks-1.png)

1. En la lista de discos, seleccione el disco que quiere desasociar. En el extremo derecho del disco seleccionado, seleccione el icono de desasociación ("X"). Se desasociará el disco seleccionado. Seleccione **Guardar**.

    ![Captura de pantalla de la vista Discos de una máquina virtual. El icono X de un disco no conectado que se puede desasociar y el botón Guardar están resaltados.](./media/azure-stack-edge-gpu-manage-virtual-machine-disks-portal/detach-data-disk-1.png)

1. Una vez desasociado el disco, se actualiza la máquina virtual. Actualice la página para ver la lista actualizada de discos de datos.

    ![Captura de pantalla de la vista Discos actualizada de una máquina virtual después de desasociar un disco de datos. La lista de discos de datos está resaltada.](./media/azure-stack-edge-gpu-manage-virtual-machine-disks-portal/list-data-disks-2.png)


## <a name="delete-a-data-disk"></a>Eliminar un disco de datos

Siga estos pasos para eliminar un disco de datos que no está conectado a una máquina virtual implementada en el dispositivo.

> [!NOTE]
> Antes de eliminar un disco de datos, debe [desasociarlo de la máquina virtual](#detach-a-data-disk) si el disco está en uso.

1. Vaya a **Máquinas virtuales** en el dispositivo y, a continuación, al panel **Recursos**. Seleccione **Discos**.
 
    ![Captura de pantalla que muestra la pestaña Discos en la hoja Recursos de las máquinas virtuales. La etiqueta Recursos y la pestaña Discos están resaltadas.](./media/azure-stack-edge-gpu-manage-virtual-machine-disks-portal/delete-disk-1.png)

1. En la lista de discos, seleccione el disco que quiere eliminar. En el extremo derecho del disco seleccionado, seleccione el icono de eliminación (papelera de reciclaje).

    ![Captura de pantalla que muestra la pestaña Discos en la hoja Recursos de las máquinas virtuales. Aparece resaltado un disco de datos con un icono de papelera que indica que se puede eliminar.](./media/azure-stack-edge-gpu-manage-virtual-machine-disks-portal/delete-disk-2.png)

    Si no ve el icono de eliminación, puede seleccionar el nombre de la máquina virtual en la columna **VM asociada** y [desasociar el disco de la máquina virtual](#detach-a-data-disk).

1. Verá un mensaje que le pide que confirme si desea eliminar el disco. La operación no se puede revertir. Seleccione **Sí**.

    ![Captura de pantalla que muestra la notificación que le pide que confirme si desea eliminar un disco de datos.](./media/azure-stack-edge-gpu-manage-virtual-machine-disks-portal/delete-disk-3.png)

    Una vez completada la eliminación, el disco se quita de la lista.


## <a name="next-steps"></a>Pasos siguientes

Para obtener información sobre cómo implementar máquinas virtuales en el dispositivo de Azure Stack Edge Pro, consulte [Implementación de máquinas virtuales a través de Azure Portal](azure-stack-edge-gpu-deploy-virtual-machine-portal.md).
