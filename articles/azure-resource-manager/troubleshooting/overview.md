---
title: Introducción a la solución de problemas de plantillas de ARM y archivos Bicep
description: Se describe la solución de problemas relativos a la implementación de recursos con plantillas de Azure Resource Manager (plantillas de ARM) y archivos Bicep.
ms.topic: overview
ms.date: 10/26/2021
ms.custom: troubleshooting-overview
ms.openlocfilehash: 604ba2be3fc1adcf22df9774400237a1b07f93e1
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/02/2021
ms.locfileid: "131093240"
---
# <a name="what-is-deployment-troubleshooting"></a>¿En qué consiste la solución de problemas de implementación?

Al implementar archivos Bicep o plantillas de Azure Resource Manager (plantillas de ARM), es posible que reciba un error. Esta documentación le ayuda a encontrar sus posibles soluciones.

## <a name="error-types"></a>Tipos de errores

Hay dos tipos de errores que puede recibir: **errores de validación** y **errores de implementación**.

Los errores de validación se producen antes de que se inicie la implementación. Estos errores se pueden determinar sin interactuar con el entorno actual de Azure. Por ejemplo, la validación le permite conocer los errores de sintaxis o los argumentos que faltan en una función antes de que se inicie la implementación.

Los errores de implementación solo se pueden determinar intentando la implementación e interactuando con el entorno de Azure. Por ejemplo, una máquina virtual (VM) requiere una tarjeta de interfaz de red (NIC). Si la NIC no existe cuando se implementa la máquina virtual, se produce un error de implementación.

## <a name="troubleshooting-tools"></a>Herramienta para la solución de problemas

Para ayudar a identificar errores de sintaxis antes de una implementación, use la versión más reciente de [Visual Studio Code](https://code.visualstudio.com). Instale la última versión de:

* La [extensión de Bicep](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-bicep)
* La [extensión de Azure Resource Manager Tools](https://marketplace.visualstudio.com/items?itemName=msazurermtools.azurerm-vscode-tools)

Para solucionar problemas de implementaciones, resulta útil obtener información sobre las propiedades o las versiones de API de un proveedor de recursos. Para más información, consulte [Definición de recursos con Bicep y plantillas de ARM](/azure/templates).

Para seguir los procedimientos recomendados para desarrollar las plantillas, use:

* [Linter de Bicep](../bicep/linter.md)
* [Kit de herramientas para pruebas de plantillas de ARM](../templates/test-toolkit.md)

Durante la implementación, puede encontrar la causa de los errores en Azure Portal, en las secciones **Implementaciones** o **Registro de actividad** de un grupo de recursos. Si usa Azure PowerShell, utilice comandos como [Get-AzResourceGroupDeploymentOperation](/powershell/module/az.resources/get-azresourcegroupdeploymentoperation) y [Get-AzActivityLog](/powershell/module/az.monitor/get-azactivitylog). Con la CLI de Azure, use comandos como [az deployment operation group](/cli/azure/deployment/operation/group) y [az monitor activity-log list](/cli/azure/monitor/activity-log#az_monitor_activity_log_list).

## <a name="next-steps"></a>Pasos siguientes

- Para ver las soluciones basadas en el código de error, consulte [Solución de problemas de errores comunes de implementación en Azure](common-deployment-errors.md).
- Para ver una introducción a la búsqueda del código de error, consulte [Inicio rápido: Solución de problemas de implementaciones de plantillas de ARM](quickstart-troubleshoot-arm-deployment.md) o [Inicio rápido: Solución de problemas de implementaciones de archivos Bicep](quickstart-troubleshoot-bicep-deployment.md).
