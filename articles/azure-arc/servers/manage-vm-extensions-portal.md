---
title: Habilitación de la extensión de máquina virtual en Azure Portal
description: En este artículo se describe cómo implementar extensiones de máquina virtual en servidores habilitados para Azure Arc que se ejecutan en entornos de nube híbridos desde Azure Portal.
ms.date: 08/11/2021
ms.topic: conceptual
ms.openlocfilehash: 740ee7954340da0a34b581356d51135033a3829d
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/13/2021
ms.locfileid: "121727370"
---
# <a name="enable-azure-vm-extensions-from-the-azure-portal"></a>Habilitación de las extensiones de máquina virtual de Azure desde Azure Portal

En este artículo se muestra cómo implementar, actualizar y desinstalar extensiones de máquina virtual de Azure compatibles con servidores habilitados para Azure Arc, en una máquina híbrida Linux o Windows mediante Azure Portal.

> [!NOTE]
> La extensión de máquina virtual de Key Vault no admite la implementación desde Azure Portal, solo mediante la CLI de Azure, Azure PowerShell o una plantilla de Azure Resource Manager.

> [!NOTE]
> Los servidores habilitados para Azure Arc no admiten la implementación y administración de extensiones de máquina virtual en máquinas virtuales de Azure. Para las máquinas virtuales de Azure, consulte el siguiente artículo de [información general sobre la extensión de máquina virtual](../../virtual-machines/extensions/overview.md).

## <a name="enable-extensions"></a>Habilitación de extensiones

Las extensiones de máquina virtual se pueden aplicar a la máquina administrada de servidor habilitada para Arc mediante Azure Portal.

1. En el explorador, vaya a [Azure Portal](https://portal.azure.com).

2. En el portal, vaya a **Servers - Azure Arc** (Servidores: Azure Arc) y seleccione la máquina híbrida de la lista.

3. Elija **Extensiones** y, después, seleccione **Agregar**. Elija la extensión que quiera en la lista de extensiones disponibles y siga las instrucciones del asistente. En este ejemplo, se implementará la extensión de máquina virtual de Log Analytics.

    ![Selección de la extensión de máquina virtual para la máquina seleccionada](./media/manage-vm-extensions/add-vm-extensions.png)

    En el ejemplo siguiente se muestra la instalación de la extensión de máquina virtual de Log Analytics desde Azure Portal:

    ![Instalación de la extensión de máquina virtual de Log Analytics](./media/manage-vm-extensions/mma-extension-config.png)

    Para completar la instalación, debe proporcionar el id. del área de trabajo y la clave principal. Si no está familiarizado con el método para encontrar esta información, consulte [Obtención del id. y la clave del área de trabajo](../../azure-monitor/agents/log-analytics-agent.md#workspace-id-and-key).

4. Después de confirmar la información necesaria proporcionada, seleccione **Revisar y crear**. Se muestra un resumen de la implementación y puede revisar el estado de esta.

>[!NOTE]
>Aunque puede procesar por lotes varias extensiones, estas se instalan en serie. Una vez completada la instalación de la primera extensión, se intenta la instalación de la siguiente.

## <a name="list-extensions-installed"></a>Enumeración de extensiones instaladas

Puede obtener una lista de las extensiones de máquina virtual del servidor habilitado para Arc desde Azure Portal. Para ello, siga los pasos a continuación.

1. En el explorador, vaya a [Azure Portal](https://portal.azure.com).

2. En el portal, vaya a **Servers - Azure Arc** (Servidores: Azure Arc) y seleccione la máquina híbrida de la lista.

3. Elija **Extensiones** y se devolverá la lista de extensiones instaladas.

    :::image type="content" source="media/manage-vm-extensions/list-vm-extensions.png" alt-text="Lista de extensiones de máquina virtual implementadas en la máquina seleccionada." border="true":::

## <a name="update-extensions"></a>Actualizar extensiones

Cuando se publica una nueva versión de una extensión compatible, puede actualizar la extensión a esa versión más reciente. Los servidores habilitados para Arc presentarán un mensaje emergente en Azure Portal cuando se desplace hasta ellos, para informarle de que hay actualizaciones disponibles para una o varias de las extensiones instaladas en una máquina. Cuando vea la lista de extensiones instaladas para un servidor habilitado para Arc seleccionado, observará una columna con la etiqueta **Actualización disponible**. Si se publica una versión más reciente de una extensión, el valor de **Actualización disponible** de esa extensión muestra **Sí**. 

La actualización de una extensión a la versión más reciente no afecta a la configuración de esa extensión. No es necesario volver a especificar la información de configuración de ninguna extensión que actualice.

:::image type="content" source="media/manage-vm-extensions-portal/vm-extensions-update-status.png" alt-text="Enumeración del estado de actualización de la extensión de máquina virtual." border="true":::

En Azure Portal, puede actualizar una extensión o seleccionar varias extensiones válidas para una actualización siguiendo estos pasos.

> [!NOTE]
> Actualmente solo puede actualizar extensiones desde Azure Portal. Por el momento, esta operación no se admite desde la CLI de Azure, Azure PowerShell o una plantilla de Azure Resource Manager.

1. En el explorador, vaya a [Azure Portal](https://portal.azure.com).

2. En el portal, vaya a **Servers - Azure Arc** (Servidores: Azure Arc) y seleccione la máquina híbrida de la lista.

3. Elija **Extensiones** y revise el estado de las extensiones en la columna **Actualización disponible**. 

Puede actualizar una extensión de una de tres maneras:

* Seleccionar una extensión de la lista de extensiones instaladas y, en las propiedades de la extensión, elegir la opción **Actualizar**.

    :::image type="content" source="media/manage-vm-extensions-portal/vm-extensions-update-from-extension.png" alt-text="Actualización de la extensión seleccionada." border="true":::

* Seleccionar la extensión de la lista de extensiones instaladas y, en las propiedades de la extensión, elegir la opción **Actualizar** en la parte superior de la página.

* Seleccionar una o varias extensiones que sean válidas para una actualización en la lista de extensiones instaladas y, luego, elegir la opción **Actualizar**.

    :::image type="content" source="media/manage-vm-extensions-portal/vm-extensions-update-selected.png" alt-text="Actualización de la extensión seleccionada." border="true":::

## <a name="uninstall-extensions"></a>Desinstalar extensiones

Puede quitar una o más extensiones de un servidor habilitado para Arc desde Azure Portal. Siga los pasos siguientes para quitar una extensión.

1. En el explorador, vaya a [Azure Portal](https://portal.azure.com).

2. En el portal, vaya a **Servers - Azure Arc** (Servidores: Azure Arc) y seleccione la máquina híbrida de la lista.

3. Elija **Extensiones** y, después, seleccione una de la lista de extensiones instaladas.

4. Seleccione **Desinstalar** y, cuando se le pida que realice la comprobación, seleccione **Sí** para continuar.

## <a name="next-steps"></a>Pasos siguientes

- Puede implementar, administrar y quitar extensiones de VM con la [CLI de Azure](manage-vm-extensions-cli.md), [PowerShell](manage-vm-extensions-powershell.md) o [plantillas de Azure Resource Manager](manage-vm-extensions-template.md).

- Puede encontrar información de solución de problemas en la [guía de solución de problemas de las extensiones de máquina virtual](troubleshoot-vm-extensions.md).