---
title: 'Inicio rápido: Creación y administración de flujos de trabajo con Azure PowerShell en Azure Logic Apps multiinquilino'
description: Cree y administre flujos de trabajo de aplicaciones lógicas en instancias de Azure Logic Apps multiinquilino mediante PowerShell.
services: logic-apps
ms.suite: integration
ms.reviewer: estfan, azla
ms.topic: quickstart
ms.custom: mvc, devx-track-azurepowershell, contperf-fy21q2
ms.date: 07/26/2021
ms.openlocfilehash: 42f6ac6ce06f1b852af2027e6ef79f347d8050a4
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/13/2021
ms.locfileid: "121787386"
---
# <a name="quickstart-create-and-manage-workflows-using-azure-powershell-in-multi-tenant-azure-logic-apps"></a>Inicio rápido: Creación y administración de flujos de trabajo con Azure PowerShell en Azure Logic Apps multiinquilino

En este inicio rápido, se muestra cómo crear y administrar aplicaciones lógicas mediante [Azure PowerShell](/powershell/azure/install-az-ps). Desde PowerShell, puede crear una aplicación lógica mediante el archivo JSON de una definición de flujo de trabajo de una aplicación lógica. A continuación, puede administrar la aplicación lógica mediante la ejecución de los cmdlets del módulo [Az.LogicApp](/powershell/module/az.logicapp/) de PowerShell.

Si no está familiarizado con Azure Logic Apps, también puede aprender a crear sus primeras aplicaciones lógicas [mediante Azure Portal](quickstart-create-first-logic-app-workflow.md), [en Visual Studio](quickstart-create-logic-apps-with-visual-studio.md)y [en Visual Studio Code](quickstart-create-logic-apps-visual-studio-code.md).

## <a name="prerequisites"></a>Requisitos previos

* Una cuenta de Azure con una suscripción activa. Si no tiene una suscripción a Azure, [cree una cuenta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* El módulo [Az de PowerShell](/powershell/azure/install-az-ps) instalado en el equipo local.
* [Grupo de recursos de Azure](#example---create-resource-group) en el que se crea la aplicación lógica.

### <a name="prerequisite-check"></a>Comprobación de requisitos previos

Valide el entorno antes de empezar:

* Inicie sesión en Azure Portal y compruebe que la suscripción esté activa; para ello, ejecute [Connect-AzAccount](/powershell/module/az.accounts/connect-azaccount).
* Compruebe la versión de Azure PowerShell mediante la ejecución de `Get-InstalledModule -Name Az`. Para saber cuál es la versión más reciente, consulte las [notas de la versión más reciente](/powershell/azure/migrate-az-6.0.0).
  * Si no tiene la versión más reciente, actualice la instalación según las instrucciones que se describen en [Actualización del módulo de Azure PowerShell](/powershell/azure/install-az-ps#update-the-azure-powershell-module).

### <a name="example---create-resource-group"></a>Ejemplo: creación de un grupo de recursos

Si aún no tiene un grupo de recursos para la aplicación lógica, créelo con el cmdlet [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup). Por ejemplo, el siguiente comando crea un grupo de recursos denominado `testResourceGroup` en la ubicación `westus`.

```azurepowershell-interactive
New-AzResourceGroup -Name testResourceGroup -Location westus
```

La salida muestra `ProvisioningState` como `Succeeded` cuando el grupo de recursos se crea correctamente:

```Output
ResourceGroupName : testResourceGroup
Location          : westus
ProvisioningState : Succeeded
Tags              :
ResourceId        : /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testResourceGroup
```

## <a name="workflow-definition"></a>Definición del flujo de trabajo

Antes de [crear una aplicación lógica](#create-logic-apps-from-powershell) o [actualizar una aplicación lógica ya existente](#update-logic-apps-from-powershell) mediante Azure PowerShell, necesita una definición de flujo de trabajo de la aplicación lógica. En Azure Portal, para ver la definición del flujo de trabajo subyacente de una aplicación lógica en formato JSON es preciso cambiar de la vista **Diseñador** a la **vista Código**.

Cuando se ejecutan los comandos para crear o actualizar la aplicación lógica, la definición de flujo de trabajo se carga como un parámetro requerido (`Definition`) o (`DefinitionFilePath`), en función del conjunto de parámetros. La definición del flujo de trabajo se debe crear en forma de archivo JSON que siga el [esquema del lenguaje de definición de flujo de trabajo](./logic-apps-workflow-definition-language.md).

## <a name="create-logic-apps-from-powershell"></a>Creación de aplicaciones lógicas desde PowerShell

Para crear un flujo de trabajo de aplicaciones lógicas desde Azure PowerShell, use el cmdlet [`New-AzLogicApp`](/powershell/module/az.logicapp/new-azlogicapp) con un archivo JSON para la definición.

### <a name="example---create-logic-app"></a>Ejemplo: creación de una aplicación lógica

En este ejemplo, se crea un flujo de trabajo llamado `testLogicApp` en el grupo de recursos `testResourceGroup` en la ubicación `westus`. El archivo JSON `testDefinition.json` contiene la definición del flujo de trabajo.

```azurepowershell-interactive
New-AzLogicApp -ResourceGroupName testResourceGroup -Location westus -Name testLogicApp -DefinitionFilePath .\testDefinition.json
```

Cuando el flujo de trabajo se crea correctamente, PowerShell muestra la definición del nuevo flujo de trabajo.

## <a name="update-logic-apps-from-powershell"></a>Actualización de aplicaciones lógicas desde PowerShell

Desde Azure PowerShell también se puede actualizar el flujo de trabajo de una aplicación lógica mediante el cmdlet [`Set-AzLogicApp`](/powershell/module/az.logicapp/set-azlogicapp).

### <a name="example---update-logic-app"></a>Ejemplo: actualización de una aplicación lógica

En este ejemplo, se muestra cómo actualizar el [flujo de trabajo de ejemplo creado en la sección anterior](#example---create-logic-app) mediante otro archivo de definición JSON, `newTestDefinition.json`.

```azurepowershell-interactive
Set-AzLogicApp -ResourceGroupName testResourceGroup -Name testLogicApp -DefinitionFilePath .\newTestDefinition.json
```

Cuando el flujo de trabajo se actualiza correctamente, PowerShell muestra la definición de flujo de trabajo actualizada de la aplicación lógica.

## <a name="delete-logic-apps-from-powershell"></a>Eliminación de aplicaciones lógicas desde PowerShell

Puede eliminar el flujo de trabajo de una aplicación lógica desde Azure PowerShell mediante el cmdlet [`Remove-AzLogicApp`](/powershell/module/az.logicapp/remove-azlogicapp).

### <a name="example---delete-logic-app"></a>Ejemplo: eliminación de una aplicación lógica

En este ejemplo, se elimina el [flujo de trabajo de ejemplo creado en una sección anterior](#example---create-logic-app).

```azurepowershell-interactive
Remove-AzLogicApp -ResourceGroupName testResourceGroup -Name testLogicApp
```

Después de responder al aviso de confirmación con `y`, se elimina la aplicación lógica.

### <a name="considerations---delete-logic-app"></a>Aspectos a tener en cuenta: eliminación de una aplicación lógica

La eliminación de una aplicación lógica afecta a las instancias de flujo de trabajo de las maneras siguientes:

* El servicio Logic Apps hace todo lo posible por cancelar las ejecuciones en curso y pendientes.

  Incluso con un gran volumen o trabajo pendiente, la mayoría de las ejecuciones se cancelan antes de que finalicen o se inicien. Sin embargo, el proceso de cancelación puede tardar en completarse. Mientras tanto, pueden seleccionarse algunas ejecuciones para su ejecución mientras el entorno en tiempo de ejecución se encarga del proceso de cancelación.

* El servicio Logic Apps no crea ni ejecuta nuevas instancias de flujo de trabajo.

* Si elimina un flujo de trabajo y, luego, vuelve a crear el mismo flujo de trabajo, el flujo de trabajo recreado no tendrá los mismos metadatos que el flujo de trabajo eliminado. Tiene que volver a guardar todo flujo de trabajo que haya llamado al flujo de trabajo eliminado. De este modo, el autor de la llamada obtiene la información correcta para el flujo de trabajo recreado. De lo contrario, las llamadas al flujo de trabajo recreado producirán un error `Unauthorized`. Este comportamiento también se aplica a los flujos de trabajo que usan artefactos en cuentas de integración y a flujos de trabajo que llaman a funciones de Azure.

## <a name="show-logic-apps-in-powershell"></a>Visualización de aplicaciones lógicas en PowerShell

Puede obtener un flujo de trabajo de aplicación lógica específico mediante el comando [`Get-AzLogicApp`](/powershell/module/az.logicapp/get-azlogicapp).

### <a name="example---get-logic-app"></a>Ejemplo: obtener una aplicación lógica

En este ejemplo, se devuelve la aplicación lógica `testLogicApp` del grupo de recursos `testResourceGroup`.

```azurepowershell-interactive
Get-AzLogicApp -ResourceGroupName testResourceGroup -Name testLogicApp
```

## <a name="next-steps"></a>Pasos siguientes

Para más información sobre Azure PowerShell, consulte la [documentación de Azure PowerShell](/powershell/azure/).

Puede encontrar más ejemplos de scripts de Logic Apps en el [explorador de ejemplos de código de Microsoft](/samples/browse/?products=azure-logic-apps).