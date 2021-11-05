---
title: Eliminación de máquinas del Seguimiento de cambios e inventario de Azure Automation
description: En este artículo se explica cómo quitar máquinas de Azure y que no son de Azure del Seguimiento de cambios e inventario.
services: automation
ms.subservice: change-inventory-management
ms.topic: conceptual
ms.date: 10/26/2021
ms.openlocfilehash: 810b2ac388b0f387183f735437ccc2b2a3918cfd
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/02/2021
ms.locfileid: "131026306"
---
# <a name="remove-machines-from-change-tracking-and-inventory"></a>Eliminación de máquinas del Seguimiento de cambios e inventario

Cuando haya terminado de realizar el seguimiento de los cambios en las máquinas virtuales de Azure o que no son de Azure en su entorno, puede dejar de administrarlas con la característica [Seguimiento de cambios e inventario](overview.md). Para dejar de administrarlos, modificará la consulta de búsqueda guardada `MicrosoftDefaultComputerGroup` en el área de trabajo de Log Analytics que está vinculada a su cuenta de Automation.

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

6. En la tabla, haga clic en el icono **Ejecutar consulta** a la derecha del elemento **MicrosoftDefaultComputerGroup** con el valor de **categoría heredada** de **ChangeTracking**.

7. En el editor de consultas, revise la consulta y busque el UUID de la máquina virtual. Quite el UUID de la máquina virtual y repita los pasos para las demás máquinas virtuales que desee quitar.

   > [!NOTE]
   > Para mayor protección, antes de realizar modificaciones, asegúrese de hacer una copia de la consulta. Así puede restaurarla si se produce un problema.

   Si quiere empezar con la consulta original y volver a agregar máquinas como apoyo para las actividades de limpieza o mantenimiento, copie la consulta siguiente:

   ```kusto
   Heartbeat
   | where Computer in~ ("") or VMUUID in~ ("")
   | distinct Computer
   ```

8. Cuando haya terminado de modificarla, guarde la búsqueda guardada; para ello, seleccione **Guardar > Función Guardar como** en la barra superior. Cuando se le pida, especifique lo siguiente:

    * **Nombre**: ChangeTracking__MicrosoftDefaultComputerGroup
    * La opción **Save as computer Group** (Guardar como grupo de equipos) está seleccionada.
    * **Legacy category** (Categoría heredada): ChangeTracking

>[!NOTE]
>Las máquinas todavía se muestran después de anular la inscripción, ya que se informa de todas las máquinas que se han evaluado en las últimas 24 horas. Después de quitar la máquina, debe esperar 24 horas antes de que ya no aparezcan en la lista.

## <a name="next-steps"></a>Pasos siguientes

Para volver a habilitar esta característica, consulte [Habilitación de Seguimiento de cambios e inventario desde una cuenta de Automation](enable-from-automation-account.md), [Habilitación de Seguimiento de cambios e inventario desde Azure Portal](enable-from-portal.md), [Habilitación de Seguimiento de cambios e inventario desde un runbook](enable-from-runbook.md) o [Habilitación de Seguimiento de cambios e inventario desde una máquina virtual de Azure](enable-from-vm.md).