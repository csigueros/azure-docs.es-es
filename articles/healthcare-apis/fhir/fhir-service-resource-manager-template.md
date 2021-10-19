---
title: Implementación del servicio FHIR de las API de Azure Healthcare con una plantilla de ARM
description: Aprenda a implementar el servicio FHIR mediante una plantilla de Azure Resource Manager (plantilla de ARM)
author: ginalee-dotcom
ms.service: healthcare-apis
ms.topic: tutorial
ms.author: zxue
ms.date: 08/06/2021
ms.openlocfilehash: 97322c917e12e451affc2fe8a67bb8c8f6200a74
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 09/13/2021
ms.locfileid: "124782429"
---
# <a name="deploy-a-fhir-service-within-azure-healthcare-apis---using-arm-template"></a>Implementación de un servicio FHIR en Azure Healthcare APIs mediante una plantilla de ARM

> [!IMPORTANT]
> Azure Healthcare APIs se encuentra actualmente en VERSIÓN PRELIMINAR. Los [Términos de uso complementarios para las versiones preliminares de Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) incluyen términos legales adicionales que se aplican a las características de Azure que se encuentran en la versión beta, en versión preliminar o que todavía no se han publicado con disponibilidad general.

En este artículo, aprenderá a implementar el servicio FHIR dentro de las API de Azure Healthcare (por lo que se denomina el servicio FHIR) mediante la plantilla Azure Resource Manager (plantilla de ARM). Le proporcionamos dos opciones, mediante PowerShell o mediante la CLI.

Una [plantilla de ARM](../../azure-resource-manager/templates/overview.md) es un archivo JSON que define la infraestructura y la configuración del proyecto. La plantilla usa sintaxis declarativa. En la sintaxis declarativa, se describe la implementación deseada sin escribir la secuencia de comandos de programación para crearla.

## <a name="prerequisites"></a>Requisitos previos

# <a name="powershell"></a>[PowerShell](#tab/PowerShell)

* Una cuenta de Azure con una suscripción activa. [cree una de forma gratuita](https://azure.microsoft.com/free/).
* Si desea ejecutar el código en la máquina local:
    * [Azure PowerShell](/powershell/azure/install-az-ps).

# <a name="cli"></a>[CLI](#tab/CLI)

* Una cuenta de Azure con una suscripción activa. [cree una de forma gratuita](https://azure.microsoft.com/free/).
* Si desea ejecutar el código en la máquina local:
    * Un shell de Bash (como Git Bash, que se incluye en [Git para Windows](https://gitforwindows.org)).
    * [Azure CLI](/cli/azure/install-azure-cli).

---

## <a name="review-the-arm-template"></a>Revisión de la plantilla de ARM

La plantilla usada en este artículo forma parte de las [plantillas de inicio rápido de Azure](https://azure.microsoft.com/resources/templates/azure-api-for-fhir/).

La plantilla define tres recursos de Azure:
- Microsoft.HealthcareApis/workspaces
- Microsoft.HealthcareApis/workspaces/fhirservices      
- Microsoft.Storage/storageAccounts

Puede implementar el recurso del  servicio FHIR quitando el recurso de áreas de trabajo, el recurso de almacenamiento y la propiedad en el recurso `dependsOn` "Microsoft.HealthcareApis/workspaces/fhirservices".


```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "authorityurl": {
            "type": "string",
            "defaultValue": "https://login.microsoftonline.com"
        },
        "tagName": {
            "type": "string",
            "defaultValue": "My Deployment"
        },
        "region": {
            "type": "string",
                  "allowedValues": [
                "australiaeast",
                "canadacentral",
                "eastus",
                "eastus2",
                "germanywestcentral",
                "japaneast",
                "northcentralus",
                "northeurope",
                "southafricanorth",
                "southcentralus",
                "southeastasia",
                "switzerlandnorth",
                "uksouth",
                "ukwest",
                "westcentralus",
                "westeurope",
                "westus2"
            ]
        },
        "workspaceName": {
            "type": "string"
        },
        "fhirServiceName": {
            "type": "string"
        },
        "tenantid": {
            "type": "string"
        },
        "storageAccountName": {
            "type": "string"
        },
        "storageAccountConfirm": {
            "type": "bool",
            "defaultValue": true
        },
        "AccessPolicies": {
            "type": "array",
            "defaultValue": []
        },
        "smartProxyEnabled": {
            "type": "bool",
            "defaultValue": false
        }
    },
    "variables": { 
        "authority": "[Concat(parameters('authorityurl'), '/', parameters('tenantid'))]",
        "createManagedIdentity": true,
        "managedIdentityType": {
            "type": "SystemAssigned"
        },
        "storageBlobDataContributerRoleId": "[concat('/subscriptions/', subscription().subscriptionId, '/providers/Microsoft.Authorization/roleDefinitions/', 'ba92f5b4-2d11-453d-a403-e96b0029c9fe')]"
    },
    "resources": [
        {
            "type": "Microsoft.HealthcareApis/workspaces",
            "name": "[parameters('workspaceName')]",
            "apiVersion": "2020-11-01-preview",
            "location": "[parameters('region')]",
            "properties": {}
        },
       {          
            "type": "Microsoft.HealthcareApis/workspaces/fhirservices",
            "kind": "fhir-R4",
            "name": "[concat(parameters('workspaceName'), '/', parameters('fhirServiceName'))]",
            "apiVersion": "2020-11-01-preview",
            "location": "[parameters('region')]",
            "dependsOn": [
                "[resourceId('Microsoft.HealthcareApis/workspaces', parameters('workspaceName'))]"
            ],
            "tags": {
                "environmentName": "[parameters('tagName')]"
            },
            "properties": {
                "accessPolicies": "[parameters('AccessPolicies')]",
                "authenticationConfiguration": {
                    "authority": "[variables('Authority')]",
                    "audience": "[concat('https//', parameters('workspaceName'), '-', parameters('fhirServiceName'), '.fhir.azurehealthcareapis.com')]",
                    "smartProxyEnabled": "[parameters('smartProxyEnabled')]"
                },
                "corsConfiguration": {
                    "allowCredentials": false,
                    "headers": ["*"],
                    "maxAge": 1440,
                    "methods": ["DELETE", "GET", "OPTIONS", "PATCH", "POST", "PUT"],
                    "origins": ["https://localhost:6001"]
                },
                "exportConfiguration": {
                    "storageAccountName": "[parameters('storageAccountName')]"
                }
            },
            "identity": "[if(variables('createManagedIdentity'), variables('managedIdentityType'), json('null'))]"
        },
        {
            "name": "[parameters('storageAccountName')]",
            "type": "Microsoft.Storage/storageAccounts",
            "apiVersion": "2019-06-01",
            "location": "[resourceGroup().location]",
            "properties": {
                "supportsHttpsTrafficOnly": "true"
            },
            "condition": "[parameters('storageAccountConfirm')]",
            "dependsOn": [
                "[parameters('fhirServiceName')]"
            ],
            "sku": {
                "name": "Standard_LRS"
            },
            "kind": "Storage",
            "tags": {
                "environmentName": "[parameters('tagName')]",
                "test-account-rg": "true"
            }
        }
    ],
    "outputs": {
    }
}
```

## <a name="deploy-arm-template"></a>Implementación de una plantilla de ARM

Puede implementar la plantilla de ARM mediante dos opciones: PowerShell o la CLI.

El código de ejemplo que se proporciona a continuación usa la plantilla en la subcarpeta "templates" de la subcarpeta "src". Es posible que desee cambiar la ruta de acceso de ubicación para hacer referencia correctamente al archivo de plantilla.

El proceso de implementación tarda unos minutos en completarse. Tome nota de los nombres del servicio FHIR y del grupo de recursos, que usará más adelante.

# <a name="powershell"></a>[PowerShell](#tab/PowerShell)

### <a name="deploy-the-template-using-powershell"></a>Implementación de la plantilla: mediante PowerShell

Ejecute el código en PowerShell localmente, en Visual Studio Code o en Azure Cloud Shell, para implementar el servicio FHIR. 

Si no ha iniciado sesión en Azure, use "Conectar-AzAccount" para iniciar sesión. Una vez que haya iniciado sesión, use "Get-AzContext" para comprobar la suscripción y el inquilino que desea usar. Puede cambiar la suscripción y el inquilino si es necesario.

Puede crear un nuevo grupo de recursos o usar uno existente omitiendo el paso o comentando la línea a partir de "New-AzResourceGroup".

```powershell-interactive
### variables
$resourcegroupname="your resource group"
$location="South Central US"
$workspacename="your workspace name"
$servicename="your fhir service name"
$tenantid="xxx"
$subscriptionid="xxx"
$storageaccountname="storage account name"
$storageaccountconfirm=1

### login to azure
Connect-AzAccount 
#Connect-AzAccount SubscriptionId $subscriptionid
Set-AzContext -Subscription $subscriptionid
Connect-AzAccount -Tenant $tenantid -SubscriptionId $subscriptionid
#Get-AzContext 

### create resource group
New-AzResourceGroup -Name $resourcegroupname -Location $location

### deploy the resource
New-AzResourceGroupDeployment -ResourceGroupName $resourcegroupname -TemplateFile "src/templates/fhirtemplate.json" -region $location -workspaceName $workspacename -fhirServiceName $fhirservicename -tenantid $tenantid -storageAccountName $storageaccountname -storageAccountConfirm $storageaccountconfirm
```
# <a name="cli"></a>[CLI](#tab/CLI)

### <a name="deploy-the-template-using-cli"></a>Implementación de la plantilla: mediante la CLI

Ejecute el código localmente, en Visual Studio Code o en Azure Cloud Shell, para implementar el servicio FHIR. 

Si no ha iniciado sesión en Azure, use "az login" para iniciar sesión. Una vez que haya iniciado sesión, use "az account show --output table" para comprobar la suscripción y el inquilino que desea usar. Puede cambiar la suscripción y el inquilino si es necesario.

Puede crear un nuevo grupo de recursos o usar uno existente omitiendo el paso o comentando la línea empezando por "az group create".

```azurecli-interactive
### variables
resourcegroupname=your resource group name
location=southcentralus
workspacename=your workspace name
fhirservicename=your fhir service name
tenantid=xxx
subscriptionid=xxx
storageaccountname=your storage account name
storageaccountconfirm=true

### login to azure
az login
az account show --output table
az account set --subscription $subscriptionid

### create resource group
az group create --name $resourcegroupname --location $location

### deploy the resource
az deployment group create --resource-group $resourcegroupname --template-file 'src\\templates\\fhirtemplate.json' --parameters region=$location workspaceName=$workspacename fhirServiceName=$fhirservicename tenantid=$tenantid storageAccountName=$storageaccountname storageAccountConfirm=$storageaccountconfirm
```

---

## <a name="review-the-deployed-resources"></a>Examen de los recursos implementados

Para comprobar que el servicio FHIR está en funcionamiento, abra el explorador y vaya a `https://<yourfhir servic>.azurehealthcareapis.com/metadata` . Si la instrucción de funcionalidad se muestra o se descarga automáticamente, la implementación se realiza correctamente. 

## <a name="clean-up-the-resources"></a>Limpiar los recursos

Cuando el recurso ya no sea necesario, ejecute el código siguiente para eliminar el grupo de recursos.

# <a name="powershell"></a>[PowerShell](#tab/PowerShell)
```powershell-interactive
$resourceGroupName = “your resource group name”
Remove-AzResourceGroup -Name $resourceGroupName
```
# <a name="cli"></a>[CLI](#tab/CLI)
```azurecli-interactive
resourceGroupName = “your resource group name”
az group delete --name $resourceGroupName
```
---

## <a name="next-steps"></a>Pasos siguientes

En esta guía de inicio rápido, ha implementado el servicio FHIR en Azure Healthcare APis mediante una plantilla de ARM. Para obtener más información sobre las características admitidas del servicio FHIR, consulte.

>[!div class="nextstepaction"]
>[Características de FHIR admitidas](fhir-features-supported.md)