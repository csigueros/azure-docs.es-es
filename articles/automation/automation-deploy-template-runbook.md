---
title: Implementación de una plantilla de Azure Resource Manager en un runbook de Azure Automation PowerShell
description: En este artículo se describe cómo implementar una plantilla de Azure Resource Manager almacenada en Azure Storage desde un runbook de PowerShell.
services: automation
ms.subservice: process-automation
ms.date: 09/23/2021
ms.topic: how-to
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 61ff25cd9878ee94ce0ba6db7b2c4e4ac8e649de
ms.sourcegitcommit: 48500a6a9002b48ed94c65e9598f049f3d6db60c
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/26/2021
ms.locfileid: "129057764"
---
# <a name="deploy-an-azure-resource-manager-template-in-an-automation-powershell-runbook"></a>Implementación de una plantilla de Azure Resource Manager en un runbook de PowerShell de Automation

Puede escribir un [runbook de PowerShell de Automation](./learn/automation-tutorial-runbook-textual-powershell.md) que implemente un recurso de Azure mediante una [plantilla de Azure Resource Manager](../azure-resource-manager/templates/quickstart-create-templates-use-the-portal.md). Las plantillas le permiten usar Azure Automation para automatizar la implementación de los recursos de Azure. Puede mantener las plantillas de Resource Manager en una ubicación central segura como Azure Storage.

En este artículo, crearemos un runbook de PowerShell que use una plantilla de Resource Manager almacenada en [Azure Storage](../storage/common/storage-introduction.md) para implementar una nueva cuenta de Azure Storage.

Si no tiene una suscripción a Azure, cree una [cuenta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de empezar.

## <a name="prerequisites"></a>Prerrequisitos

* Una cuenta de Azure Automation con al menos una identidad administrada asignada por el usuario. Para más información, consulte [Uso de una identidad administrada asignada por el usuario para una cuenta de Azure Automation](./add-user-assigned-identity.md).

* Módulos Az: `Az.Accounts`, `Az.ManagedServiceIdentity`, `Az.Resources` y `Az.Storage` importados en la cuenta de Automation. Para más información, consulte [Importación de módulos Az](./shared-resources/modules.md#import-az-modules).

* [Cuenta de Azure Storage](../storage/common/storage-account-create.md) donde se va a almacenar la plantilla de Resource Manager.

* Azure PowerShell instalado en una máquina local. Consulte [Instalación del módulo de Azure PowerShell](/powershell/azure/install-az-ps) para información sobre cómo obtener Azure PowerShell. También necesitará el módulo [Az.ManagedServiceIdentity](/powershell/module/az.managedserviceidentity). `Az.ManagedServiceIdentity` es un módulo en versión preliminar y no se instala como parte del módulo Az. Para instalarlo, ejecute `Install-Module -Name Az.ManagedServiceIdentity`.

## <a name="assign-permissions-to-managed-identities"></a>Asignación de permisos a las identidades administradas

Asigne permisos a las identidades administradas para realizar las tareas relacionadas con el almacenamiento en el runbook.

1. Inicie sesión en Azure de forma interactiva con el cmdlet [Connect-AzAccount](/powershell/module/az.accounts/connect-azaccount) y siga las instrucciones.

    ```powershell
    # Sign in to your Azure subscription
    $sub = Get-AzSubscription -ErrorAction SilentlyContinue
    if(-not($sub))
    {
        Connect-AzAccount
    }
    
    # If you have multiple subscriptions, set the one to use
    # Select-AzSubscription -SubscriptionId <SUBSCRIPTIONID>
    ```

1. Proporcione un valor adecuado para las variables siguientes y, a continuación, ejecute el script.

    ```powershell
    $resourceGroup = "resourceGroup"
    $automationAccount = "automationAccount"
    $storageAccount = "storageAccount"
    $userAssignedManagedIdentity = "userAssignedManagedIdentity"
    $storageTemplate = "path\storageTemplate.json"
    $runbookScript = "path\runbookScript.ps1"
    ```

1. Asigne el rol `reader` a la identidad administrada asignada por el sistema para ejecutar el cmdlet `Get-AzUserAssignedIdentity`.

    ```powershell
    $SAMI = (Get-AzAutomationAccount -ResourceGroupName $resourceGroup -Name $automationAccount).Identity.PrincipalId
    New-AzRoleAssignment `
        -ObjectId $SAMI `
        -ResourceGroupName $resourceGroup `
        -RoleDefinitionName "Reader"
    ```

1. Asigne el rol `Storage Account Contributor` a la identidad administrada asignada por el sistema para realizar acciones en la cuenta de almacenamiento.

    ```powershell
    $UAMI_ID = (Get-AzUserAssignedIdentity -ResourceGroupName $resourceGroup -Name $userAssignedManagedIdentity).PrincipalId
    New-AzRoleAssignment `
        -ObjectId $UAMI_ID `
        -ResourceGroupName $resourceGroup `
        -RoleDefinitionName "Storage Account Contributor"
    ```


## <a name="create-the-resource-manager-template"></a>Creación de la plantilla de Resource Manager

En este ejemplo, se usa una plantilla de Resource Manager que implementa una nueva cuenta de Azure Storage. Cree un archivo local denominado `storageTemplate.json` y pegue el código siguiente:

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "storageAccountType": {
      "type": "string",
      "defaultValue": "Standard_LRS",
      "allowedValues": [
        "Standard_LRS",
        "Standard_GRS",
        "Standard_ZRS",
        "Premium_LRS"
      ],
      "metadata": {
        "description": "Storage Account type"
      }
    },
    "location": {
      "type": "string",
      "defaultValue": "[resourceGroup().location]",
      "metadata": {
        "description": "Location for all resources."
      }
    }
  },
  "variables": {
    "storageAccountName": "[concat(uniquestring(resourceGroup().id), 'standardsa')]"
  },
  "resources": [
    {
      "type": "Microsoft.Storage/storageAccounts",
      "name": "[variables('storageAccountName')]",
      "apiVersion": "2018-02-01",
      "location": "[parameters('location')]",
      "sku": {
          "name": "[parameters('storageAccountType')]"
      },
      "kind": "Storage", 
      "properties": {
      }
    }
  ],
  "outputs": {
      "storageAccountName": {
          "type": "string",
          "value": "[variables('storageAccountName')]"
      }
  }
}
```

## <a name="save-the-resource-manager-template-in-azure-files"></a>Guardado de la plantilla de Resource Manager en Azure Files

Use PowerShell para crear un recurso compartido de Azure y cargar `storageTemplate.json`. Para obtener instrucciones sobre cómo crear un recurso compartido de archivos y cargar un archivo en Azure Portal, vea [Introducción a Azure Files en Windows](../storage/files/storage-files-quick-create-use-windows.md).

Ejecute los comandos siguientes para crear un recurso compartido de archivos y cargar la plantilla de Resource Manager en él.

```powershell
# Get the access key for your storage account
$key = Get-AzStorageAccountKey -ResourceGroupName $resourceGroup -Name $storageAccount

# Create an Azure Storage context using the first access key
$context = New-AzStorageContext -StorageAccountName $storageAccount -StorageAccountKey $key[0].value

# Create a file share named 'resource-templates' in your Azure Storage account
$fileShare = New-AzStorageShare -Name 'resource-templates' -Context $context

# Add the storageTemplate.json file to the new file share
Set-AzStorageFileContent -ShareName $fileShare.Name -Context $context -Source $storageTemplate
```

## <a name="create-the-powershell-runbook-script"></a>Creación del script del runbook de PowerShell

Cree un script de PowerShell que obtenga el archivo `storageTemplate.json` de Azure Storage e implemente la plantilla para crear una cuenta de Azure Storage.  Cree un archivo local denominado `runbookScript.ps1` y pegue el código siguiente:


```powershell
param (
    [Parameter(Mandatory=$true)]
    [string]
    $resourceGroup,

    [Parameter(Mandatory=$true)]
    [string]
    $storageAccount,

    [Parameter(Mandatory=$true)]
    [string]
    $storageAccountKey,

    [Parameter(Mandatory=$true)]
    [string]
    $storageFileName,

    [Parameter(Mandatory=$true)]
    [string]
    $userAssignedManagedIdentity
)

# Ensures you do not inherit an AzContext in your runbook
Disable-AzContextAutosave -Scope Process

# Connect to Azure with user-assigned managed identity
$AzureContext = (Connect-AzAccount -Identity).context
$identity = Get-AzUserAssignedIdentity -ResourceGroupName $resourceGroup `
    -Name $userAssignedManagedIdentity `
    -DefaultProfile $AzureContext
$AzureContext = (Connect-AzAccount -Identity -AccountId $identity.ClientId).context

# set and store context
$AzureContext = Set-AzContext -SubscriptionName $AzureContext.Subscription `
    -DefaultProfile $AzureContext

#Set the parameter values for the Resource Manager template
$Parameters = @{
    "storageAccountType"="Standard_LRS"
    }

# Create a new context
$Context = New-AzStorageContext -StorageAccountName $storageAccount -StorageAccountKey $storageAccountKey

Get-AzStorageFileContent `
    -ShareName 'resource-templates' `
    -Context $Context `
    -path 'storageTemplate.json' `
    -Destination 'C:\Temp' -Force

$TemplateFile = Join-Path -Path 'C:\Temp' -ChildPath $storageFileName

# Deploy the storage account
New-AzResourceGroupDeployment `
    -ResourceGroupName $resourceGroup `
    -TemplateFile $TemplateFile `
    -TemplateParameterObject $Parameters 
```

## <a name="import-and-publish-the-runbook-into-your-azure-automation-account"></a>Importación y publicación del runbook en su cuenta de Azure Automation

Use PowerShell para importar el runbook en su cuenta de Azure Automation y, luego, publique el runbook. Para información sobre cómo importar y publicar un runbook en Azure Portal, consulte [Administración de runbooks en Azure Automation](manage-runbooks.md).

Para importar `runbookScript.ps1` en su cuenta de Automation como un runbook de PowerShell, ejecute los siguientes comandos de PowerShell:

```powershell
$importParams = @{
    Path = $runbookScript
    ResourceGroupName = $resourceGroup
    AutomationAccountName = $automationAccount
    Type = "PowerShell"
}
Import-AzAutomationRunbook @importParams

# Publish the runbook
$publishParams = @{
    ResourceGroupName = $resourceGroup
    AutomationAccountName = $automationAccount
    Name = "runbookScript"
}
Publish-AzAutomationRunbook @publishParams
```

## <a name="start-the-runbook"></a>Inicio del runbook

Ahora iniciaremos el runbook llamando al cmdlet [Start-AzAutomationRunbook](/powershell/module/Az.Automation/Start-AzAutomationRunbook). Para obtener información acerca de cómo iniciar un runbook en Azure Portal, consulte [Inicio de un runbook en Azure Automation](./start-runbooks.md).

Ejecute los siguientes comandos en la consola de PowerShell:

```powershell
# Set up the parameters for the runbook
$runbookParams = @{
    resourceGroup = $resourceGroup
    storageAccount = $storageAccount
    storageAccountKey = $key[0].Value # We got this key earlier
    storageFileName = "storageTemplate.json"
    userAssignedManagedIdentity = $userAssignedManagedIdentity
}

# Set up parameters for the Start-AzAutomationRunbook cmdlet
$startParams = @{
    resourceGroup = $resourceGroup
    AutomationAccountName = $automationAccount
    Name = "runbookScript"
    Parameters = $runbookParams
}

# Start the runbook
$job = Start-AzAutomationRunbook @startParams
```

Después de ejecutar el runbook, puede comprobar su estado recuperando el valor de propiedad del objeto de trabajo `$job.Status`.

El runbook obtiene la plantilla de Resource Manager y la utiliza para implementar una nueva cuenta de Azure Storage. Puede ver la nueva cuenta de almacenamiento que se ha creado ejecutando el siguiente comando:

```powershell
Get-AzStorageAccount
```

## <a name="next-steps"></a>Pasos siguientes

* Para más información sobre las plantillas de Resource Manager, consulte [Información general de Azure Resource Manager](../azure-resource-manager/management/overview.md).
* Para empezar a trabajar con Azure Storage, consulte [Introducción a Azure Storage](../storage/common/storage-introduction.md).
* Para encontrar otros runbooks útiles de Azure Automation, vea [Utilizar runbooks y módulos en Azure Automation](automation-runbook-gallery.md).
