---
title: Incorporación de un disco de datos existente a una VM de laboratorio
description: Aprenda a asociar o desasociar un disco de datos de laboratorio a una máquina virtual de laboratorio en Azure DevTest Labs.
ms.topic: how-to
ms.date: 10/26/2021
ms.openlocfilehash: 8afd2fff50a893fb293d7b419c4839c34487c32d
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/03/2021
ms.locfileid: "131453988"
---
# <a name="attach-or-detach-a-lab-data-disk-to-a-lab-virtual-machine-in-azure-devtest-labs"></a>Asociación o desasociación de un disco de datos de laboratorio a una máquina virtual de laboratorio en Azure DevTest Labs

Puede crear y adjuntar un nuevo [disco de datos](../virtual-machines/managed-disks-overview.md) de laboratorio para una máquina virtual (VM) de Azure de laboratorio. Después, el disco de datos se puede desasociar y: eliminar, volver a conectarse o conectarse a otra máquina virtual de laboratorio que posea. Esta funcionalidad es útil para administrar el almacenamiento o el software fuera de cada máquina virtual individual.

En este artículo, aprenderá a adjuntar y desasociar un disco de datos a una máquina virtual de laboratorio.

## <a name="prerequisites"></a>Prerrequisitos

La máquina virtual de laboratorio debe estar en ejecución. El tamaño de la máquina virtual controla cuántos discos de datos puede conectar. Para obtener más información, consulte [Tamaños de máquinas virtuales](../virtual-machines/sizes.md).

## <a name="attach-a-new-data-disk"></a>Acoplamiento de un nuevo disco de datos

Siga estos pasos para crear y asociar un nuevo disco de datos administrado a una máquina virtual en Azure DevTest Labs.

1. Inicie sesión en [Azure Portal](https://portal.azure.com/).

1. Vaya al laboratorio en **DevTest Labs**.

1. Seleccione la máquina virtual en ejecución.

1. En la página de la **máquina virtual**, en **Configuración**, seleccione **Discos**.
 
1. Seleccione **Asociar nuevo**.

    :::image type="content" source="./media/devtest-lab-attach-detach-data-disk/devtest-lab-attach-new.png" alt-text="Captura de pantalla de la asociación de un nuevo disco de datos a una máquina virtual.":::

1. En la página **Asociar nuevo disco**, proporcione la información siguiente: 

    |Propiedad | Descripción |
    |---|---|
    |Nombre|Escriba un nombre único.|
    |Tipo de disco| Seleccione un [tipo de disco](../virtual-machines/disks-types.md) en la lista desplegable.|
    |Tamaño (GiB)|Escriba un tamaño en gigabytes.|

    :::image type="content" source="./media/devtest-lab-attach-detach-data-disk/devtest-lab-attach-new-form.png" alt-text="Captura de pantalla del rellenado del formulario &quot;Asociar nuevo disco&quot;.":::

1. Seleccione **Aceptar**.

1. Volverá a la página de la **máquina virtual**. Puede ver el disco asociado en **Discos de datos**.

    :::image type="content" source="./media/devtest-lab-attach-detach-data-disk/devtest-lab-attached-data-disk.png" alt-text="Captura de pantalla del disco asociado que aparece en Discos de datos.":::

## <a name="detach-a-data-disk"></a>Desacoplar un disco de datos

La desasociación quita el disco de laboratorio de la máquina virtual de laboratorio, pero lo sigue almacenando para su uso posterior.

### <a name="detach-from-the-vms-management-page"></a>Desasociación desde el panel de administración de la máquina virtual

1. Vaya al laboratorio en **DevTest Labs**.

1. Seleccione la máquina virtual en ejecución con un disco de datos asociado.

1. En la página de la **máquina virtual**, en **Configuración**, seleccione **Discos**.

1. En **Discos de datos**, seleccione el disco de datos que quiere desasociar.

    :::image type="content" source="./media/devtest-lab-attach-detach-data-disk/devtest-lab-detach-button.png" alt-text="Captura de pantalla de la selección de discos de datos para una máquina virtual.":::

1. En la página **Disco de datos**, seleccione **Desasociar**.

    :::image type="content" source="./media/devtest-lab-attach-detach-data-disk/devtest-lab-detach-data-disk-2.png" alt-text="Captura de pantalla en la que se muestra el panel de detalles de un disco con la acción &quot;Desasociar&quot; resaltada.":::

1. Seleccione **Aceptar** para confirmar que quiere desasociar el disco de datos. El disco se desasocia y está disponible para asociarse a otra máquina virtual. 

### <a name="detach-from-the-labs-management-page"></a>Desasociación desde la página de administración del laboratorio

1. Vaya al laboratorio en **DevTest Labs**.

1. En **Mi laboratorio**, seleccione **Mis discos de datos**.

1. En el disco que quiera desasociar, seleccione los puntos suspensivos ( **...** ) y después **Desasociar**.

    :::image type="content" source="./media/devtest-lab-attach-detach-data-disk/devtest-lab-detach-data-disk.png" alt-text="Captura de pantalla de la desasociación de un disco de datos.":::

1. Haga clic en **Sí** para confirmar que desea crear desasociarlo.

   > [!NOTE]
   > Si ya se ha desasociado un disco de datos, puede quitarlo de la lista de discos de datos disponibles con **Eliminar**.

## <a name="attach-an-existing-disk"></a>un disco existente

Siga estos pasos para asociar un disco de datos disponible existente a una máquina virtual en ejecución. 

1. Vaya al laboratorio en **DevTest Labs**.

1. Seleccione la máquina virtual en ejecución.

1. En la página de la **máquina virtual**, en **Configuración**, seleccione **Discos**.

1. Seleccione **Adjuntar existente**.

    :::image type="content" source="./media/devtest-lab-attach-detach-data-disk/devtest-lab-attach-existing-button.png" alt-text="Captura de pantalla en la que se muestran las opciones &quot;Discos&quot; y &quot;Adjuntar existente&quot; seleccionadas.":::

1. En la página **Asociar disco existente**, seleccione un disco y después **Aceptar**. Transcurridos unos instantes, el disco de datos se asocia a la máquina virtual y aparece en la lista de **Discos de datos** de esa máquina virtual.

    :::image type="content" source="./media/devtest-lab-attach-detach-data-disk/devtest-lab-attach-existing.png" alt-text="Captura de pantalla de la asociación de un disco de datos existente a una máquina virtual.":::

## <a name="upgrade-an-unmanaged-data-disk"></a>Actualización de un disco de datos no administrado

Si tiene una máquina virtual con discos de datos no administrados, puede convertirla para que use discos administrados. Este proceso convierte el disco del SO y los discos de datos conectados.

En primer lugar, [desasocie el disco de datos](#detach-a-data-disk) de la máquina virtual no administrada. A continuación, [vuelva a asociar el disco](#attach-an-existing-disk) a una máquina virtual administrada para actualizar automáticamente el disco de datos de no administrado a administrado.

## <a name="next-steps"></a>Pasos siguientes

Aprenda a administrar discos de datos para [máquinas virtuales reclamables](devtest-lab-add-claimable-vm.md#unclaim-a-vm).
