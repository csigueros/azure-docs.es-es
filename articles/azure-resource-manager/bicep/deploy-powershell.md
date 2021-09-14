---
title: Implementación de recursos con PowerShell y Bicep
description: Use Azure Resource Manager y Azure PowerShell para implementar recursos para Azure. Los recursos se definen en un archivo Bicep.
author: mumian
ms.author: jgao
ms.topic: conceptual
ms.date: 06/01/2021
ms.openlocfilehash: a367c643ea2780c081250ec35ebcb890fdfec24e
ms.sourcegitcommit: add71a1f7dd82303a1eb3b771af53172726f4144
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/03/2021
ms.locfileid: "123427013"
---
# <a name="deploy-resources-with-bicep-and-azure-powershell"></a>Implementación de recursos con Bicep y Azure PowerShell

En este artículo, se explica el uso de Azure PowerShell con archivos de Bicep para implementar recursos en Azure. Si no conoce los conceptos de implementación y administración de las soluciones de Azure, consulte [Introducción a Bicep](overview.md).

Para implementar archivos Bicep, necesita [Azure PowerShell versión 5.6.0 o posterior](/powershell/azure/install-az-ps).

## <a name="prerequisites"></a>Requisitos previos

Necesita un archivo de Bicep para implementarlo. El nombre del archivo local que se utiliza en este artículo es _C:\MyTemplates\azuredeploy.bicep_.

Debe instalar Azure PowerShell y conectarse a Azure:

- **Instale los cmdlets de Azure PowerShell en el equipo local.** Para más información, consulte el artículo de [introducción a Azure PowerShell](/powershell/azure/get-started-azureps).
- **Conéctese a Azure con [Connect-AzAccount](/powershell/module/az.accounts/connect-azaccount)** . Si tiene varias suscripciones de Azure, es posible que también tenga que ejecutar [Set-AzContext](/powershell/module/Az.Accounts/Set-AzContext). Para más información, consulte [Use multiple Azure subscriptions](/powershell/azure/manage-subscriptions-azureps) (Uso de varias suscripciones de Azure).

Si no tiene instalado PowerShell, puede usar Azure Cloud Shell. Para más información, consulte [Implementación de archivos Bicep desde Azure Cloud Shell](./deploy-cloud-shell.md).

## <a name="deployment-scope"></a>Ámbito de la implementación

La implementación puede tener como destino un grupo de recursos, una suscripción, un grupo de administración o un inquilino. Según el ámbito de la implementación, usará comandos diferentes.

- Para implementar en un **grupo de recursos**, utilice [New-AzResourceGroupDeployment](/powershell/module/az.resources/new-azresourcegroupdeployment):

  ```azurepowershell
  New-AzResourceGroupDeployment -ResourceGroupName <resource-group-name> -TemplateFile <path-to-bicep>
  ```

- Para realizar la implementación en una **suscripción**, use [New-AzSubscriptionDeployment](/powershell/module/az.resources/new-azdeployment), que es un alias del cmdlet `New-AzDeployment`:

  ```azurepowershell
  New-AzSubscriptionDeployment -Location <location> -TemplateFile <path-to-bicep>
  ```

  Para más información sobre las implementaciones en el nivel de suscripción, consulte [Creación de grupos de recursos y otros recursos en el nivel de suscripción](deploy-to-subscription.md).

- Para hacer la implementación en un **grupo de administración**, use [New-AzManagementGroupDeployment](/powershell/module/az.resources/New-AzManagementGroupDeployment).

  ```azurepowershell
  New-AzManagementGroupDeployment -ManagementGroupId <management-group-id> -Location <location> -TemplateFile <path-to-bicep>
  ```

  Para obtener más información sobre las implementaciones de nivel de grupo de administración, consulte [Creación de recursos en el nivel de grupo de administración](deploy-to-management-group.md).

- Para hacer la implementación en un **inquilino**, use [New-AzTenantDeployment](/powershell/module/az.resources/new-aztenantdeployment).

  ```azurepowershell
  New-AzTenantDeployment -Location <location> -TemplateFile <path-to-bicep>
  ```

  Para más información sobre las implementaciones a nivel de inquilino, consulte [Creación de recursos en el nivel de inquilino](deploy-to-tenant.md).

Para cada ámbito, el usuario que implementa la plantilla debe tener permisos para crear recursos.

## <a name="deploy-local-bicep-file"></a>Implementación de un archivo Bicep local

Puede implementar un archivo Bicep desde la máquina local o una que esté almacenada externamente. En esta sección se describe la implementación de un archivo Bicep local.

Si va a realizar la implementación en un grupo de recursos que no existe, cree el grupo de recursos. El nombre del grupo de recursos solo puede incluir caracteres alfanuméricos, puntos, guiones bajos, guiones y paréntesis. Puede tener hasta 90 caracteres. El nombre no puede terminar con un punto.

```azurepowershell
New-AzResourceGroup -Name ExampleGroup -Location "Central US"
```

Para implementar un archivo Bicep, use el parámetro `-TemplateFile` en el comando de implementación.

```azurepowershell
New-AzResourceGroupDeployment `
  -Name ExampleDeployment `
  -ResourceGroupName ExampleGroup `
  -TemplateFile <path-to-bicep>
```

La implementación puede tardar unos minutos en finalizar.

## <a name="deploy-remote-bicep-file"></a>Implementación de un archivo Bicep remoto

Actualmente, Azure PowerShell no admite la implementación de archivos Bicep remotos. Use la [CLI de Bicep](./install.md#development-environment) para compilar el archivo de Bicep en una plantilla JSON y, luego, cargue el archivo JSON en la ubicación remota.

## <a name="parameters"></a>Parámetros

Para pasar valores de parámetros, puede usar parámetros en línea o un archivo de parámetros.

### <a name="inline-parameters"></a>Parámetros en línea

Para pasar parámetros en línea, proporcione los nombres de parámetro con el comando `New-AzResourceGroupDeployment`. Por ejemplo, para pasar una cadena y una matriz a un archivo de Bicep, use:

```powershell
$arrayParam = "value1", "value2"
New-AzResourceGroupDeployment -ResourceGroupName testgroup `
  -TemplateFile <path-to-bicep> `
  -exampleString "inline string" `
  -exampleArray $arrayParam
```

También puede obtener el contenido del archivo y proporcionar ese contenido como un parámetro en línea.

```powershell
$arrayParam = "value1", "value2"
New-AzResourceGroupDeployment -ResourceGroupName testgroup `
  -TemplateFile <path-to-bicep> `
  -exampleString $(Get-Content -Path c:\MyTemplates\stringcontent.txt -Raw) `
  -exampleArray $arrayParam
```

Obtener un valor de parámetro de un archivo es útil cuando se necesita proporcionar valores de configuración. Por ejemplo, puede proporcionar [valores de cloud-init para una máquina virtual Linux](../../virtual-machines/linux/using-cloud-init.md).

Si necesita pasar una matriz de objetos, cree las tablas hash en PowerShell y agréguelas a una matriz. Pase esa matriz como un parámetro durante la implementación.

```powershell
$hash1 = @{ Name = "firstSubnet"; AddressPrefix = "10.0.0.0/24"}
$hash2 = @{ Name = "secondSubnet"; AddressPrefix = "10.0.1.0/24"}
$subnetArray = $hash1, $hash2
New-AzResourceGroupDeployment -ResourceGroupName testgroup `
  -TemplateFile <path-to-bicep> `
  -exampleArray $subnetArray
```

### <a name="parameter-files"></a>Archivos de parámetros

En lugar de pasar parámetros como valores en línea en el script, quizá le resulte más fácil usar un archivo JSON que contiene los valores de parámetro. El archivo de parámetros puede ser un archivo local o un archivo externo con un identificador URI accesible. El archivo Bicep usa archivos de parámetros JSON.

Para más información sobre el archivo de parámetro, consulte [Creación de un archivo de parámetros de Resource Manager](./parameter-files.md).

Para pasar un archivo de parámetros local, use el parámetro `TemplateParameterFile`:

```powershell
New-AzResourceGroupDeployment -Name ExampleDeployment -ResourceGroupName ExampleResourceGroup `
  -TemplateFile <path-to-bicep> `
  -TemplateParameterFile c:\MyTemplates\storage.parameters.json
```

Para pasar un archivo de parámetros externo, use el parámetro `TemplateParameterUri`:

```powershell
New-AzResourceGroupDeployment -Name ExampleDeployment -ResourceGroupName ExampleResourceGroup `
  -TemplateFile <path-to-bicep> `
  -TemplateParameterUri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/quickstarts/microsoft.storage/storage-account-create/azuredeploy.parameters.json
```

## <a name="preview-changes"></a>Vista previa de los cambios

Antes de implementar el archivo Bicep, puede obtener una vista previa de los cambios que el archivo Bicep realizará en su entorno. Use la [operación "what-if"](./deploy-what-if.md) para comprobar que la plantilla realiza los cambios esperados. La operación "what-if" también valida que el archivo Bicep no tenga errores.

## <a name="deploy-template-specs"></a>Especificaciones de la implementación de la plantilla

Actualmente, Azure PowerShell no admite la creación de especificaciones de plantilla mediante archivos Bicep. Pero puede crear un archivo de Bicep con el recurso [Microsoft.Resources/templateSpecs](/azure/templates/microsoft.resources/templatespecs) para implementar una especificación de plantilla. En el [ejemplo Creación de una especificación de plantilla](https://github.com/Azure/azure-docs-bicep-samples/blob/main/samples/create-template-spec/azuredeploy.bicep) se muestra cómo crear una especificación de plantilla en un archivo de Bicep. También puede compilar el archivo Bicep en una plantilla ARM JSON mediante la CLI de Bicep y, a continuación, crear una especificación de plantilla con la plantilla JSON.

## <a name="deployment-name"></a>Nombre de implementación

Al implementar un archivo Bicep, puede asignarle un nombre a la implementación. Este nombre puede ayudarle a recuperar la implementación del historial de implementaciones. Si no especifica un nombre para la implementación, se utilizará el nombre del archivo Bicep. Por ejemplo, si implementa un archivo de Bicep llamado `azuredeploy.bicep` y no especifica ningún nombre para la implementación, se le asignará el nombre `azuredeploy`.

Cada vez que se ejecuta una implementación, se agrega una entrada al historial de implementación del grupo de recursos con el nombre de la implementación. Si ejecuta otra implementación y le asigna el mismo nombre, la entrada anterior se reemplazará por la implementación actual. Si desea que todas las entradas del historial de implementaciones sean diferentes, asigne un nombre único a cada implementación.

Para crear un nombre único, puede asignar un número aleatorio.

```azurepowershell-interactive
$suffix = Get-Random -Maximum 1000
$deploymentName = "ExampleDeployment" + $suffix
```

También puede agregar un valor de fecha.

```azurepowershell-interactive
$today=Get-Date -Format "MM-dd-yyyy"
$deploymentName="ExampleDeployment"+"$today"
```

Si ejecuta implementaciones simultáneas en el mismo grupo de recursos utilizando el mismo nombre de implementación, solo se completará la última implementación. Aquellas implementaciones que tengan el mismo nombre y no hayan finalizado se sustituirán por la última implementación. Por ejemplo, si ejecuta una implementación llamada `newStorage` que implementa la cuenta de almacenamiento `storage1` y, al mismo tiempo, ejecuta otra implementación llamada `newStorage` que implementa la cuenta de almacenamiento `storage2`, solo se implementará una única cuenta de almacenamiento. La cuenta de almacenamiento resultante será `storage2`.

Sin embargo, si ejecuta una implementación llamada `newStorage` que implementa la cuenta de almacenamiento `storage1` e inmediatamente después ejecuta otra implementación llamada `newStorage` que implementa la cuenta de almacenamiento `storage2`, tendrá dos cuentas de almacenamiento. Una se llamará `storage1` y la otra, `storage2`. Sin embargo, solo tendrá una entrada en el historial de implementaciones.

Si especifica un nombre único para cada implementación, podrá ejecutarlas simultáneamente sin conflictos. Si ejecuta una implementación llamada `newStorage1` que implementa la cuenta de almacenamiento `storage1` y, al mismo tiempo, ejecuta otra implementación llamada `newStorage2` que implementa la cuenta de almacenamiento `storage2`, tendrá dos cuentas de almacenamiento y dos entradas en el historial de implementación.

Para evitar conflictos con las implementaciones simultáneas y garantizar que las entradas del historial de implementaciones sean únicas, asigne un nombre diferente a cada implementación.

## <a name="next-steps"></a>Pasos siguientes

- Para revertir a una implementación correcta cuando se produce un error, consulte [Revertir en caso de error a una implementación correcta](../templates/rollback-on-error.md).
- Para entender cómo definir parámetros en el archivo, consulte [Nociones sobre la estructura y la sintaxis de los archivos Bicep](file.md).
- Para más información sobre la implementación de una plantilla que requiere un token de SAS, vea [Implementación de una plantilla de Resource Manager privada con el token de SAS](../templates/secure-template-with-sas-token.md).
