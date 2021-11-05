---
title: Eliminación de máquinas desde Update Management de Azure Automation
description: En este artículo se explica cómo quitar máquinas administradas de Azure y que no son de Azure con Update Management.
services: automation
ms.topic: conceptual
ms.date: 10/26/2021
ms.custom: mvc
ms.openlocfilehash: e5e4e80833bc21e5ffc5533d208b1642de359fd5
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/02/2021
ms.locfileid: "131006741"
---
# <a name="remove-vms-from-update-management"></a>Eliminación de una máquina virtual desde Update Management

Cuando haya terminado de administrar las actualizaciones en las máquinas de Azure o no de Azure de su entorno, puede dejar de administrar las máquinas virtuales con la característica [Update Management](overview.md). Para dejar de administrarlos, modificará la consulta de búsqueda guardada `MicrosoftDefaultComputerGroup` en el área de trabajo de Log Analytics que está vinculada a su cuenta de Automation.

## <a name="sign-into-the-azure-portal"></a>Inicie sesión en Azure Portal.

Inicie sesión en [Azure Portal](https://portal.azure.com).

## <a name="to-remove-your-machines"></a>Para quitar las máquinas

1. En Azure Portal, inicie **Cloud Shell** en la navegación superior de Azure Portal. Si no está familiarizado con Azure Cloud Shell, consulte [Introducción a Azure Cloud Shell](../../cloud-shell/overview.md).

2. Use el método siguiente para identificar el UUID de una máquina virtual de Azure o de una máquina que no es de Azure y que quiere quitar de la administración.

   # <a name="azure-vm"></a>[MV de Azure](#tab/azure-vm)

   ```azurecli
   az vm show -g MyResourceGroup -n MyVm -d
   ```

   # <a name="non-azure-machine"></a>[Máquina que no es de Azure](#tab/non-azure-machine)

   ```kusto
   Heartbeat
   | where TimeGenerated > ago(30d)
   | where ComputerEnvironment == "Non-Azure"
   | summarize by Computer, VMUUID
   ```

   ---

3. En Azure Portal, vaya a **Areas de trabajo de Log Analytics**. Seleccione el área de trabajo de la lista.

4. En el área de trabajo de Log Analytics, seleccione **Grupos de equipos** en el menú de la izquierda.

5. En **Grupos de equipos** en el panel derecho, se muestra de forma predeterminada la pestaña **Grupos guardados**.

6. En la tabla, haga clic en el icono **Ejecutar consulta** a la derecha del elemento **MicrosoftDefaultComputerGroup** con el valor de **Categoría heredada** de **Actualizaciones**.

7. En el editor de consultas, revise la consulta y busque el UUID de la máquina. Quite el UUID de la máquina y repita los pasos para las demás máquinas que desee quitar.

   > [!NOTE]
   > Para mayor protección, antes de realizar modificaciones, asegúrese de hacer una copia de la consulta. Así puede restaurarla si se produce un problema.

   Si quiere empezar con la consulta original y volver a agregar máquinas como apoyo para las actividades de limpieza o mantenimiento, copie la consulta siguiente:

   ```kusto
   Heartbeat
   | where Computer in~ ("") or VMUUID in~ ("")
   | distinct Computer
   ```

8. Cuando haya terminado de modificarla, guarde la búsqueda guardada; para ello, seleccione **Guardar > Función Guardar como** en la barra superior. Cuando se le pida, especifique lo siguiente:

    * **Nombre**: Updates__MicrosoftDefaultComputerGroup
    * La opción **Save as computer Group** (Guardar como grupo de equipos) está seleccionada.
    * **Categoría heredada**: Actualizaciones

>[!NOTE]
>Las máquinas todavía se muestran después de anular la inscripción, ya que se informa de todas las máquinas que se han evaluado en las últimas 24 horas. Después de quitar la máquina, debe esperar 24 horas antes de que ya no aparezcan en la lista.

## <a name="next-steps"></a>Pasos siguientes

Para volver a habilitar la máquina de Azure o que no es de Azure, consulte [Habilitación de Update Management desde Azure Portal](enable-from-portal.md) o [Habilitación de Update Management desde una máquina virtual de Azure](enable-from-vm.md).