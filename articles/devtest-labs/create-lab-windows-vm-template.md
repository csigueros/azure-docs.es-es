---
title: Cree un laboratorio en Azure DevTest Labs mediante una plantilla de Azure Resource Manager
description: En este inicio rápido, creará un laboratorio en Azure DevTest Labs mediante una plantilla de Azure Resource Manager (plantilla de ARM). Los administradores de laboratorio configuran laboratorios, crean máquinas virtuales en los laboratorios y configuran las directivas.
ms.topic: quickstart
ms.custom: subject-armqs
ms.date: 10/15/2021
ms.openlocfilehash: 0cdc7db0f29a09040b7d40507cfad840b75f0c91
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/22/2021
ms.locfileid: "130246964"
---
# <a name="quickstart-use-an-arm-template-to-create-a-lab-in-devtest-labs"></a>Inicio rápido: Uso de una plantilla de ARM para crear un laboratorio en DevTest Labs

En este inicio rápido, usará una plantilla de Azure Resource Manager (plantilla de ARM) para crear un laboratorio en Azure DevTest Labs. El laboratorio contiene una máquina virtual (VM) Datacenter de Windows Server 2019.

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

En este inicio rápido, lleve a cabo las acciones siguientes:

> [!div class="checklist"]
> * Revise la plantilla.
> * Implemente la plantilla.
> * Comprobación de la implementación.
> * Limpieza de recursos.

## <a name="prerequisites"></a>Prerrequisitos

Si no tiene una suscripción a Azure, cree una [cuenta gratuita](https://azure.microsoft.com/free/) antes de empezar.

## <a name="review-the-template"></a>Revisión de la plantilla

En este inicio rápido se usa la plantilla [Creación de un laboratorio Azure DevTest Labs con una máquina virtual de Windows Server](https://azure.microsoft.com/resources/templates/dtl-create-lab-windows-vm/). La plantilla define los recursos siguientes:

- [Microsoft.DevTestLab/labs](/azure/templates/microsoft.devtestlab/labs)
- [Microsoft.DevTestLab labs/virtualnetworks](/azure/templates/microsoft.devtestlab/labs/virtualnetworks)
- [Microsoft.DevTestLab labs/virtualmachines](/azure/templates/microsoft.devtestlab/labs/virtualmachines)

:::code language="json" source="~/quickstart-templates/quickstarts/microsoft.devtestlab/dtl-create-lab-windows-vm/azuredeploy.json":::

Para ver más ejemplos de plantillas ARM, consulte [Plantillas de inicio rápido de Azure](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Devtestlab).

## <a name="deploy-the-template"></a>Implementación de la plantilla

1. Seleccione el siguiente botón **Implementar en Azure** para usar la plantilla de ARM. La plantilla abre la pantalla de creación del laboratorio en Azure Portal:

   [![Implementación en Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fquickstarts%2Fmicrosoft.devtestlab%2Fdtl-create-lab-windows-vm%2Fazuredeploy.json)

1. En la pantalla **Creación de un laboratorio en Azure DevTest Labs con una máquina virtual de Windows Server**, rellene los siguientes elementos:

   - **Grupo de recursos**: cree un grupo de recursos nuevo para facilitar la limpieza más adelante.
   - **Región**: seleccione la ubicación del grupo de recursos.
   - En **Nombre del laboratorio**: escriba un nombre para la instancia de laboratorio nueva.
   - **Nombre de la máquina virtual**: escriba un nombre para crear la máquina virtual. 
   - **Nombre de usuario**: escriba un nombre para el usuario que puede acceder a la máquina virtual. 
   - **Contraseña**: escriba una contraseña para el usuario.

1. Seleccione **Revisar y crear** y, una vez superada la validación, seleccione **Crear**.

   :::image type="content" source="./media/create-lab-windows-vm-template/deploy-template-page.png" alt-text="Captura de pantalla de la página Crear un laboratorio.":::

## <a name="validate-the-deployment"></a>Validación de la implementación
1. Durante la implementación, puede seleccionar **Notificaciones** en la parte superior de la pantalla y seleccionar **Implementación en curso** para ver la página **Información general** de la implementación. La implementación, especialmente la creación de una máquina virtual, tarda un tiempo.

   :::image type="content" source="./media/create-lab-windows-vm-template/deployment-notification.png" alt-text="Captura de pantalla que muestra la implementación en curso.":::

1. Una vez completada la implementación, seleccione **Ir al grupo de recursos** para ir al grupo de recursos del laboratorio.

   :::image type="content" source="./media/create-lab-windows-vm-template/navigate-resource-group.png" alt-text="Captura de pantalla que muestra la implementación completada y el botón Ir al grupo de recursos.":::

1. En la página **Grupo de recursos** se enumeran los recursos del grupo de recursos, incluido el laboratorio y los recursos que dependen de este, como redes virtuales y máquinas virtuales. Seleccione el recurso de **DevTest Lab** para ir a la página de **Información general** del laboratorio.

1. En la página de **Información general**, seleccione **Máquinas virtuales reclamables**.

   :::image type="content" source="./media/create-lab-windows-vm-template/lab-home-page.png" alt-text="Captura de pantalla que muestra la página de información general del laboratorio con el vínculo de máquinas virtuales reclamables.":::

1. En la página **Máquinas virtuales reclamables**, seleccione los puntos suspensivos **Más opciones** junto a la máquina virtual y seleccione **Reclamar máquina**.

   :::image type="content" source="./media/create-lab-windows-vm-template/claim-vm.png" alt-text="Captura de pantalla que muestra la página Máquinas virtuales reclamables con la opción Reclamar máquina.":::

1. En la página de **información general** del laboratorio, confirme que la máquina virtual aparece en **Mis máquinas virtuales** con el estado **Iniciando**. Espere a que el estado cambie a **Ejecución**.

   :::image type="content" source="./media/create-lab-windows-vm-template/lab-vm.png" alt-text="Captura de pantalla que muestra la página de información general con la máquina virtual en la lista.":::

## <a name="clean-up-resources"></a>Limpieza de recursos

Elimine los recursos de inicio rápido para evitar cargos por ejecutar el laboratorio y la máquina virtual en Azure. Si tiene previsto consultar el siguiente tutorial para acceder a la máquina virtual en el laboratorio, puede limpiar los recursos después de finalizar ese tutorial.

No se puede eliminar un grupo de recursos que contenga un laboratorio. En primer lugar, elimine el laboratorio para poder eliminar el grupo de recursos.

1. En la página principal del laboratorio, seleccione **Eliminar** en la barra de herramientas.

   :::image type="content" source="./media/create-lab-windows-vm-template/delete-lab-button.png" alt-text="Captura de pantalla que muestra el botón Eliminar en la página principal del laboratorio.":::

1. En la página Confirmación, escriba el nombre del laboratorio y seleccione **Eliminar**.

1. Durante la eliminación, puede seleccionar **Notificaciones** en la parte superior de la pantalla para ver el progreso. La eliminación del laboratorio tarda un tiempo.

1. Después de haber eliminado el laboratorio, seleccione el **Grupo de recursos** en la página principal del laboratorio. Si la página principal del laboratorio ya no está disponible, busque **Grupos de recursos** en el cuadro de búsqueda de Azure y, a continuación, seleccione el grupo de recursos que contenía el laboratorio.

   :::image type="content" source="./media/create-lab-windows-vm-template/confirm-lab-deletion.png" alt-text="Captura de pantalla que muestra la confirmación de eliminación en las notificaciones.":::

1. En la página **Grupo de recursos**, seleccione **Eliminar grupo de recursos**. En la página de confirmación, escriba el nombre del grupo de recursos y seleccione **Eliminar**. Puede comprobar las notificaciones para confirmar que se ha eliminado el grupo de recursos.
 
   :::image type="content" source="./media/create-lab-windows-vm-template/delete-resource-group-button.png" alt-text="Captura de pantalla que muestra el botón Eliminar grupo de recursos.":::

## <a name="next-steps"></a>Pasos siguientes
En este inicio rápido, ha creado un laboratorio que tiene una máquina virtual. Para información acerca de cómo acceder al laboratorio y a la máquina virtual, avance hasta el tutorial siguiente:

> [!div class="nextstepaction"]
> [Tutorial: Acceso al laboratorio](tutorial-use-custom-lab.md)
