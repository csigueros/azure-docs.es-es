---
title: Acceso a un laboratorio
description: En este tutorial, accederá a un laboratorio en Azure DevTest Labs. Usará una máquina virtual, anulará su reclamación y, a continuación, la reclamará.
ms.topic: tutorial
ms.date: 11/03/2021
ms.author: spelluru
ms.openlocfilehash: d5ace346c8f4b853e862b2e12b8b2adee55bab8c
ms.sourcegitcommit: 96deccc7988fca3218378a92b3ab685a5123fb73
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/04/2021
ms.locfileid: "131578548"
---
# <a name="tutorial-access-a-lab-in-azure-devtest-labs"></a>Tutorial: Acceso a un laboratorio en Azure DevTest Labs

En este tutorial, va a utilizar el laboratorio que se creó en el [Tutorial: Creación de un laboratorio en Azure DevTest Labs](tutorial-create-custom-lab.md).

En este tutorial realizará lo siguiente:

> [!div class="checklist"]
> * Conexión a la máquina virtual de laboratorio
> * Anulación de la reclamación de la máquina virtual de laboratorio
> * Reclamación de la máquina virtual (VM) de laboratorio

Si no tiene una suscripción a Azure, cree una [cuenta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de empezar.

## <a name="prerequisites"></a>Requisitos previos

Un [laboratorio de DevTest Labs con una máquina virtual de Azure](tutorial-create-custom-lab.md).

## <a name="connect-to-the-lab-vm"></a>Conexión a la máquina virtual de laboratorio

1. Inicie sesión en [Azure Portal](https://portal.azure.com).

1. Vaya al laboratorio en **DevTest Labs**.

1. En **Mis máquinas virtuales**, seleccione la máquina virtual.

    :::image type="content" source="./media/tutorial-use-custom-lab/my-virtual-machines.png" alt-text="Captura de pantalla de la máquina virtual en Mis máquinas virtuales.":::

1. En el menú superior, seleccione **Conectar**. A continuación, seleccione el archivo `.rdp`, que se descarga en la máquina.

    :::image type="content" source="./media/tutorial-use-custom-lab/vm-connect.png" alt-text="Captura de pantalla del botón de conexión con la máquina virtual.":::

1. En el cuadro de diálogo **Conexión a Escritorio remoto**, seleccione **Conectar**.

1. En el cuadro de diálogo **Escriba sus credenciales**, escriba la contraseña y, a continuación, seleccione **Aceptar**.

1. Si aparece un cuadro de diálogo con el mensaje **No puede comprobarse la identidad del equipo remoto**, active la casilla **No volver a preguntarme sobre conexiones a este equipo**. A continuación, seleccione **Sí**.

    :::image type="content" source="./media/tutorial-use-custom-lab/remote-computer-verification.png" alt-text="Captura de pantalla de la verificación del equipo remoto.":::

Para ver los pasos para conectarse a una máquina virtual Linux, consulte cómo [conectar con una máquina virtual Linux en Azure](../virtual-machines/linux/use-remote-desktop.md). 

## <a name="unclaim-the-lab-vm"></a>Anulación de la reclamación de la máquina virtual de laboratorio

Una vez que haya terminado con la máquina virtual, anule su reclamación siguiendo estos pasos: 

1. Seleccione la máquina virtual de DevTest Labs con los mismos pasos anteriores.

1. En la página de la **máquina virtual**, seleccione **Anular la reclamación** en el menú superior. 

    :::image type="content" source="./media/tutorial-use-custom-lab/virtual-machine-unclaim.png" alt-text="Captura de pantalla de la opción de anulación de la reclamación.":::

1. La máquina virtual se apaga antes de anular su reclamación. Puede supervisar el estado de esta operación en **Notificaciones**.

1. Cierre la página de la **máquina virtual** para volver a la página **Información general de DevTest Lab**.

1. En **Mi laboratorio**, seleccione **Máquinas virtuales que se pueden reclamar**. La máquina virtual ya está disponible para ser reclamada.

    :::image type="content" source="./media/tutorial-use-custom-lab/claimable-virtual-machines.png" alt-text="Captura de pantalla de las opciones de las máquinas virtuales reclamables.":::

## <a name="claim-a-lab-vm"></a>Reclamación de una máquina virtual de laboratorio

Puede reclamar la máquina virtual de nuevo si necesita usarla.

1. En la lista de **Máquinas virtuales que se pueden reclamar**, seleccione **...** (puntos suspensivos) y seleccione **Reclamar máquina**.

    :::image type="content" source="./media/tutorial-use-custom-lab/claimable-virtual-machines-claimed.png" alt-text="Captura de pantalla de la opción de reclamación.":::

1. Confirme que ve la máquina virtual en la lista **Mis máquinas virtuales**.

    :::image type="content" source="./media/tutorial-use-custom-lab/my-virtual-machines-2.png" alt-text="Captura de pantalla que muestra la máquina virtual devuelta a mis máquinas virtuales.":::

## <a name="clean-up-resources"></a>Limpieza de recursos

Elimine los recursos para evitar cargos por ejecutar el laboratorio y la máquina virtual en Azure. Si tiene previsto consultar el siguiente tutorial para acceder a la máquina virtual del laboratorio, puede limpiar los recursos cuando lo finalice. De lo contrario, siga estos pasos. 

1. Vuelva a la página principal del laboratorio que ha creado.

1. Seleccione **Eliminar** en el menú superior.

   :::image type="content" source="./media/tutorial-use-custom-lab/portal-lab-delete.png" alt-text="Captura de pantalla del botón Eliminar del laboratorio.":::

1. En la página **¿Seguro que quiere eliminarlo?** , escriba el nombre del laboratorio en el cuadro de texto y seleccione **Eliminar**.

1. Durante la eliminación, puede seleccionar **Notificaciones** en la parte superior de la pantalla para ver el progreso. La eliminación del laboratorio tarda un rato. Cuando se haya eliminado el laboratorio, continúe con el paso siguiente.

1. Si creó el laboratorio en un grupo de recursos existente, se habrán quitado todos los recursos del laboratorio. Si creó un grupo de recursos para este tutorial, ahora está vacío y se puede eliminar. No habría sido posible eliminar el grupo de recursos anteriormente mientras el laboratorio todavía pertenecía a él.
    
## <a name="next-steps"></a>Pasos siguientes

En este tutorial, ha aprendido a acceder y usar un laboratorio en Azure DevTest Labs. Para más información sobre el acceso y el uso de máquinas virtuales en un laboratorio, consulte:

> [!div class="nextstepaction"]
> [Procedimiento: Uso de máquinas virtuales en un laboratorio](devtest-lab-add-vm.md)
