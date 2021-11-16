---
title: Cree un laboratorio en Azure DevTest Labs mediante una plantilla de Azure Resource Manager
description: En este inicio rápido, creará un laboratorio en Azure DevTest Labs mediante una plantilla de Azure Resource Manager (plantilla de ARM). Los administradores de laboratorio configuran laboratorios, crean máquinas virtuales en los laboratorios y configuran las directivas.
ms.topic: quickstart
ms.custom: subject-armqs
ms.date: 10/29/2021
ms.openlocfilehash: 7d6a1390c935d68b978146d9eb06e8d2681e50c2
ms.sourcegitcommit: 8946cfadd89ce8830ebfe358145fd37c0dc4d10e
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/05/2021
ms.locfileid: "131847572"
---
# <a name="quickstart-use-an-arm-template-to-create-a-lab-in-devtest-labs"></a>Inicio rápido: Uso de una plantilla de ARM para crear un laboratorio en DevTest Labs

En este inicio rápido se describe cómo usar una plantilla de Azure Resource Manager (plantilla de ARM) para crear un laboratorio en Azure DevTest Labs. El laboratorio contiene una máquina virtual (VM) Datacenter de Windows Server 2019.

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

Si su entorno cumple los requisitos previos y está familiarizado con el uso de plantillas de Resource Manager, seleccione el botón **Implementar en Azure**. La plantilla se abrirá en Azure Portal.

[![Implementación en Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fquickstarts%2Fmicrosoft.devtestlab%2Fdtl-create-lab-windows-vm-claimed%2Fazuredeploy.json)

## <a name="prerequisites"></a>Prerrequisitos

Si no tiene una suscripción a Azure, cree una [cuenta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de empezar.

## <a name="review-the-template"></a>Revisión de la plantilla

La plantilla usada en este inicio rápido forma parte de las [plantillas de inicio rápido de Azure](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Devtestlab).

:::code language="json" source="~/quickstart-templates/quickstarts/microsoft.devtestlab/dtl-create-lab-windows-vm-claimed/azuredeploy.json":::

En la plantilla se definen tres recursos de Azure:

- [Microsoft.DevTestLab/labs](/azure/templates/microsoft.devtestlab/labs): cree un laboratorio de DevTest Labs.
- [Microsoft.DevTestLab labs/virtualnetworks](/azure/templates/microsoft.devtestlab/labs/virtualnetworks): cree una red virtual de DevTest Labs.
- [Microsoft.DevTestLab/labs/virtualMachines](/azure/templates/microsoft.devtestlab/labs/virtualmachines): cree una máquina virtual de DevTest Labs

Para ver más ejemplos de plantillas ARM, consulte [Plantillas de inicio rápido de Azure](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Devtestlab).

## <a name="deploy-the-template"></a>Implementación de la plantilla

1. Seleccione el botón **Implementar en Azure** siguiente para iniciar sesión en Azure y abrir la plantilla de ARM.

   [![Implementación en Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fquickstarts%2Fmicrosoft.devtestlab%2Fdtl-create-lab-windows-vm-claimed%2Fazuredeploy.json)

1. Escriba o seleccione los siguientes valores:

    |Propiedad | Descripción |
    |---|---|
    |Suscripción| En la lista desplegable, seleccione la suscripción de Azure que se va a usar para el laboratorio.|
    |Resource group| En la lista desplegable, seleccione el grupo de recursos existente o seleccione **Crear**.|
    |Region | El valor se rellenará automáticamente con la ubicación usada para el grupo de recursos.|
    |Nombre del laboratorio| Escriba un nombre de laboratorio único para la suscripción.|
    |Location| Déjelo tal cual. |
    |Nombre de la máquina virtual| Escriba un nombre de máquina virtual único para la suscripción.|
    |Tamaño de máquina virtual: | Déjelo tal cual. |
    |Nombre de usuario | Escriba un nombre de usuario para la máquina virtual.|
    |Contraseña| Escriba una contraseña que tenga entre 8 y 123 caracteres.|

   :::image type="content" source="./media/create-lab-windows-vm-template/deploy-template-page.png" alt-text="Captura de pantalla de la página Crear un laboratorio.":::

1. Seleccione **Revisar y crear** y, una vez superada la validación, seleccione **Crear**. A continuación, se le mostrará la página **Información general** de la implementación, donde podrá supervisar el progreso. Los tiempos de implementación variarán en función del hardware, la imagen base y los artefactos seleccionados. El tiempo de implementación de las configuraciones usadas en esta plantilla es de aproximadamente 12 minutos.

## <a name="validate-the-deployment"></a>Validación de la implementación

1. Una vez finalizada la implementación, seleccione **Ir al grupo de recursos** desde la página de **información general** de la plantilla o desde **Notificaciones**.

   :::image type="content" source="./media/create-lab-windows-vm-template/navigate-resource-group.png" alt-text="Captura de pantalla que muestra la implementación completada y el botón Ir al grupo de recursos.":::

1. En la página **Grupo de recursos** se enumeran los recursos del grupo de recursos, incluido el laboratorio y los recursos que dependen de este, como redes virtuales y máquinas virtuales. Seleccione el recurso de **DevTest Lab** para ir a la página de **Información general** del laboratorio.

   :::image type="content" source="./media/create-lab-windows-vm-template/resource-group-overview.png" alt-text="Captura de pantalla de la página de información general del grupo de recursos":::.

1. En la página de **información general** del laboratorio, puede ver la máquina virtual en la sección **Mis máquinas virtuales**.

   :::image type="content" source="./media/create-lab-windows-vm-template/lab-home-page.png" alt-text="Captura de pantalla que muestra la página de información general con la máquina virtual.":::

1. Vuelva atrás y enumere los grupos de recursos de la suscripción. Observe que la implementación creó un nuevo grupo de recursos para contener la máquina virtual. La sintaxis es el nombre del laboratorio + nombre de máquina virtual + números aleatorios. En función de los valores usados en este artículo, el nombre generado automáticamente es `MyOtherLab-myVM-173385`.

   :::image type="content" source="./media/create-lab-windows-vm-template/resource-group-list.png" alt-text="Captura de pantalla de la lista de grupos de recursos":::.

## <a name="clean-up-resources"></a>Limpieza de recursos

Elimine los recursos para evitar cargos por ejecutar el laboratorio y la máquina virtual en Azure. Si tiene previsto consultar el siguiente tutorial para acceder a la máquina virtual del laboratorio, puede limpiar los recursos cuando lo finalice. De lo contrario, siga estos pasos. 

1. Vuelva a la página principal del laboratorio que ha creado.

1. Seleccione **Eliminar** en el menú superior.

   :::image type="content" source="./media/create-lab-windows-vm-template/portal-lab-delete.png" alt-text="Captura de pantalla del botón Eliminar del laboratorio.":::

1. En la página **¿Seguro que quiere eliminarlo?** , escriba el nombre del laboratorio en el cuadro de texto y seleccione **Eliminar**.

1. Durante la eliminación, puede seleccionar **Notificaciones** en la parte superior de la pantalla para ver el progreso. La eliminación del laboratorio tarda un rato. Cuando se haya eliminado el laboratorio, continúe con el paso siguiente.

1. Si creó el laboratorio en un grupo de recursos existente, se habrán quitado todos los recursos del laboratorio. Si creó un grupo de recursos para este tutorial, ahora está vacío y se puede eliminar. No habría sido posible eliminar el grupo de recursos anteriormente mientras el laboratorio todavía pertenecía a él.

## <a name="next-steps"></a>Pasos siguientes
En este inicio rápido, ha creado un laboratorio que tiene una máquina virtual. Para información acerca de cómo acceder al laboratorio y a la máquina virtual, avance hasta el tutorial siguiente:

> [!div class="nextstepaction"]
> [Tutorial: Acceso al laboratorio](tutorial-use-custom-lab.md)
