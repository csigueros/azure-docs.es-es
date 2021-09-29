---
title: Uso de una identidad administrada asignada por el usuario para una cuenta de Azure Automation (versión preliminar)
description: En este artículo, se describe cómo configurar una identidad administrada asignada por el usuario para las cuentas de Azure Automation.
services: automation
ms.subservice: process-automation
ms.date: 09/23/2021
ms.topic: conceptual
ms.openlocfilehash: 7b1a75aac3166b1fdd3cdd39f5f66bd380339975
ms.sourcegitcommit: 48500a6a9002b48ed94c65e9598f049f3d6db60c
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/26/2021
ms.locfileid: "129061796"
---
# <a name="using-a-user-assigned-managed-identity-for-an-azure-automation-account-preview"></a>Uso de una identidad administrada asignada por el usuario para una cuenta de Azure Automation (versión preliminar)

En este artículo, se muestra cómo agregar una identidad administrada asignada por el usuario para una cuenta de Azure Automation y cómo usarla para acceder a otros recursos. Para más información sobre cómo funcionan las identidades administradas con Azure Automation, consulte [Identidades administradas (versión preliminar)](automation-security-overview.md#managed-identities-preview).

> [!NOTE]
> Las identidades administradas asignadas por el usuario solo se admiten para trabajos en la nube.  

Si no tiene una suscripción a Azure, cree una [cuenta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de empezar.

## <a name="prerequisites"></a>Prerrequisitos

- Una cuenta de Azure Automation Para obtener instrucciones, consulte [Creación de una cuenta de Azure Automation](./quickstarts/create-account-portal.md).

- Una identidad administrada asignada por el sistema. Para obtener instrucciones, consulte [Habilitación de una identidad administrada para la cuenta de Azure Automation (versión preliminar)](enable-managed-identity-for-automation.md).

- Una identidad administrada asignada por el usuario. Para obtener instrucciones, consulte [Crear una identidad administrada asignada por el usuario](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md#create-a-user-assigned-managed-identity).

- La identidad administrada asignada por el usuario y los recursos de Azure de destino que administra el runbook con esa identidad deben estar en la misma suscripción de Azure.

- La versión más reciente de los módulos de cuenta de Azure. Actualmente, es la versión 2.2.8. (Consulte [Az.Accounts](https://www.powershellgallery.com/packages/Az.Accounts/) para obtener más información sobre esta versión).

- Un recurso de Azure al que desea acceder desde el runbook de Automation. Este recurso debe tener un rol definido para la identidad administrada asignada por el usuario, lo que ayuda al runbook de Automation a autenticar el acceso al recurso. Para agregar roles, debe ser propietario del recurso en el inquilino de Azure AD correspondiente.

- Si desea ejecutar trabajos híbridos mediante una identidad administrada asignada por el usuario, actualice Hybrid Runbook Worker a la versión más reciente. Las versiones mínimas obligatorias son:

  - Hybrid Runbook Worker de Windows: versión 7.3.1125.0
  - Hybrid Runbook Worker de Linux: versión 1.7.4.0

## <a name="add-user-assigned-managed-identity-for-azure-automation-account"></a>Adición de una identidad administrada asignada por el usuario para una cuenta de Azure Automation

Puede agregar una identidad administrada asignada por el usuario para una cuenta de Azure Automation mediante Azure Portal, PowerShell, la API REST de Azure o una plantilla de ARM. Para los ejemplos que implican PowerShell, inicie sesión primero en Azure de forma interactiva con el cmdlet [Connect-AzAccount](/powershell/module/Az.Accounts/Connect-AzAccount) y siga las instrucciones.

```powershell
# Sign in to your Azure subscription
$sub = Get-AzSubscription -ErrorAction SilentlyContinue
if(-not($sub))
{
    Connect-AzAccount
}

# If you have multiple subscriptions, set the one to use
# Select-AzSubscription -SubscriptionId "<SUBSCRIPTIONID>"
```

A continuación, inicialice un conjunto de variables que se usarán a lo largo de los ejemplos. Revise los valores siguientes y, a continuación, ejecute:

```powershell
$subscriptionID = "subscriptionID"
$resourceGroup = "resourceGroupName"
$automationAccount = "automationAccountName"
$userAssignedOne = "userAssignedIdentityOne"
$userAssignedTwo = "userAssignedIdentityTwo"
```

### <a name="add-using-the-azure-portal"></a>Adición mediante Azure Portal

Lleve a cabo los siguiente pasos:

1. Inicie sesión en [Azure Portal](https://portal.azure.com).

1. En Azure Portal, vaya a su cuenta de Automation.

1. En  **Configuración de la cuenta**, seleccione  **Identidad**.

1. Seleccione la pestaña **Usuario asignado** y, a continuación, seleccione **Agregar**.

1. Seleccione la identidad administrada asignada por el usuario existente y, a continuación, seleccione **Agregar**. A continuación, volverá a la pestaña **Usuario asignado**.

   :::image type="content" source="media/add-user-assigned-identity/user-assigned-managed-identity.png" alt-text="Salida del portal.":::

### <a name="add-using-powershell"></a>Incorporación mediante PowerShell

Use el cmdlet de PowerShell [Set-AzAutomationAccount](/powershell/module/az.automation/set-azautomationaccount) para agregar las identidades administradas asignadas por el usuario. En primer lugar, debe tener en cuenta si hay una identidad administrada asignada por el sistema existente. En el ejemplo siguiente, se agregan dos identidades administradas asignadas por el usuario existentes a una cuenta de Automation existente y se deshabilitará una identidad administrada asignada por el sistema si existe una.

```powershell
$output = Set-AzAutomationAccount `
    -ResourceGroupName $resourceGroup `
    -Name $automationAccount `
    -AssignUserIdentity "/subscriptions/$subscriptionID/resourcegroups/$resourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/$userAssignedOne", `
        "/subscriptions/$subscriptionID/resourcegroups/$resourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/$userAssignedTwo"

$output
```

Para mantener una identidad administrada asignada por el sistema existente, use lo siguiente:

```powershell
$output = Set-AzAutomationAccount `
    -ResourceGroupName $resourceGroup `
    -Name $automationAccount `
    -AssignUserIdentity "/subscriptions/$subscriptionID/resourcegroups/$resourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/$userAssignedOne", `
        "/subscriptions/$subscriptionID/resourcegroups/$resourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/$userAssignedTwo" `
    -AssignSystemIdentity

$output
```

La salida debe tener una apariencia similar a la siguiente:

:::image type="content" source="media/add-user-assigned-identity/set-azautomationaccount-output.png" alt-text="Salida del comando Set-AzAutomationAccount.":::

Para obtener una salida adicional, ejecute: `$output.identity | ConvertTo-Json`.

### <a name="add-using-a-rest-api"></a>Adición mediante una API REST

A continuación, se proporcionan los pasos de ejemplo y la sintaxis.

#### <a name="syntax"></a>Sintaxis

La sintaxis del cuerpo del ejemplo siguiente habilita una identidad administrada asignada por el sistema si aún no está habilitada y asigna dos identidades administradas asignadas por el usuario existentes a la cuenta de Automation existente.

PATCH

```json
{
  "identity": {
    "type": "SystemAssigned, UserAssigned",
    "userAssignedIdentities": {
      "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/resource-group-name/providers/Microsoft.ManagedIdentity/userAssignedIdentities/firstIdentity": {},
      "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/resource-group-name/providers/Microsoft.ManagedIdentity/userAssignedIdentities/secondIdentity": {}
    }
  }
}
```

La sintaxis de la API es la siguiente:

```http
https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/resource-group-name/providers/Microsoft.Automation/automationAccounts/automation-account-name?api-version=2020-01-13-preview 
```

#### <a name="example"></a>Ejemplo

Lleve a cabo los siguiente pasos.

1. Revise la sintaxis del cuerpo anterior en un archivo llamado `body_ua.json`. Guarde el archivo en la máquina local o en una cuenta de almacenamiento de Azure.

1. Revise el valor de la variable siguiente y, a continuación, ejecute:

    ```powershell
    $file = "path\body_ua.json"
    ```

1. Este ejemplo usa el cmdlet de PowerShell [Invoke-RestMethod](/powershell/module/microsoft.powershell.utility/invoke-restmethod) para enviar una solicitud PATCH a la cuenta de Automation.

    ```powershell
    # build URI
    $URI = "https://management.azure.com/subscriptions/$subscriptionID/resourceGroups/$resourceGroup/providers/Microsoft.Automation/automationAccounts/$automationAccount`?api-version=2020-01-13-preview"
    
    # build body
    $body = Get-Content $file
    
    # obtain access token
    $azContext = Get-AzContext
    $azProfile = [Microsoft.Azure.Commands.Common.Authentication.Abstractions.AzureRmProfileProvider]::Instance.Profile
    $profileClient = New-Object -TypeName Microsoft.Azure.Commands.ResourceManager.Common.RMProfileClient -ArgumentList ($azProfile)
    $token = $profileClient.AcquireAccessToken($azContext.Subscription.TenantId)
    $authHeader = @{
        'Content-Type'='application/json'
        'Authorization'='Bearer ' + $token.AccessToken
    }
    
    # Invoke the REST API
    $response = Invoke-RestMethod -Uri $URI -Method PATCH -Headers $authHeader -Body $body
    
    # Review output
    $response.identity | ConvertTo-Json
    ```

    La salida debe tener una apariencia similar a la siguiente:

    ```json
    {
    "type": "SystemAssigned, UserAssigned",
    "principalId": "00000000-0000-0000-0000-000000000000",
    "tenantId": "00000000-0000-0000-0000-000000000000",
    "userAssignedIdentities":  {
        "/subscriptions/ContosoID/resourcegroups/ContosoLab/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ContosoUAMI1":  {
                "PrincipalId":  "00000000-0000-0000-0000-000000000000",
                "ClientId":  "00000000-0000-0000-0000-000000000000"
                    },
        "/subscriptions/ContosoID/resourcegroups/ContosoLab/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ContosoUAMI2":  {
                "PrincipalId":  "00000000-0000-0000-0000-000000000000",
                "ClientId":  "00000000-0000-0000-0000-000000000000"
                    }
        }
    }
    ```

### <a name="add-using-an-arm-template"></a>Adición mediante una plantilla de ARM

A continuación, se proporcionan los pasos de ejemplo y la sintaxis.

#### <a name="template-syntax"></a>Sintaxis de plantillas

La sintaxis de la plantilla de ejemplo siguiente habilita una identidad administrada asignada por el sistema si aún no está habilitada y asigna dos identidades administradas asignadas por el usuario existentes a la cuenta de Automation existente.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "automationAccountName": {
     "defaultValue": "YourAutomationAccount",
      "type": "String",
      "metadata": {
        "description": "Automation account name"
      }
    },
    "userAssignedOne": {
     "defaultValue": "userAssignedOne",
      "type": "String",
      "metadata": {
        "description": "User-assigned managed identity"
      }
      },
    "userAssignedTwo": {
     "defaultValue": "userAssignedTwo",
      "type": "String",
      "metadata": {
        "description": "User-assigned managed identity"
      }
      }
   },
  "resources": [
    {
      "type": "Microsoft.Automation/automationAccounts",
      "apiVersion": "2020-01-13-preview",
      "name": "[parameters('automationAccountName')]",
      "location": "[resourceGroup().location]",
      "identity": {
        "type": "SystemAssigned, UserAssigned",
        "userAssignedIdentities": {
          "[resourceID('Microsoft.ManagedIdentity/userAssignedIdentities/',parameters('userAssignedOne'))]": {},
          "[resourceID('Microsoft.ManagedIdentity/userAssignedIdentities/',parameters('userAssignedTwo'))]": {}
        }
      },
      "properties": {
        "sku": {
          "name": "Basic"
        },
        "encryption": {
          "keySource": "Microsoft.Automation",
          "identity": {}
        }
      }
    }
  ]
}
```

#### <a name="example"></a>Ejemplo

Lleve a cabo los siguiente pasos.

1. Copie y pegue la plantilla en un archivo llamado `template_ua.json`. Guarde el archivo en la máquina local o en una cuenta de almacenamiento de Azure.

1. Revise el valor de la variable siguiente y, a continuación, ejecute:

    ```powershell
    $templateFile = "path\template_ua.json"
    ```

1. Use el cmdlet de PowerShell [New-AzResourceGroupDeployment](/powershell/module/az.resources/new-azresourcegroupdeployment) para implementar la plantilla.

    ```powershell
    New-AzResourceGroupDeployment `
        -Name "UserAssignedDeployment" `
        -ResourceGroupName $resourceGroup `
        -TemplateFile $templateFile `
        -automationAccountName $automationAccount `
        -userAssignedOne $userAssignedOne `
        -userAssignedTwo $userAssignedTwo
    ```

   El comando no producirá ninguna salida; sin embargo, puede usar el código siguiente para comprobarlo:

    ```powershell
    (Get-AzAutomationAccount `
    -ResourceGroupName $resourceGroup `
    -Name $automationAccount).Identity | ConvertTo-Json
    ```

    La salida tendrá un aspecto similar al que se muestra en el ejemplo anterior de la API REST.

## <a name="give-identity-access-to-azure-resources-by-obtaining-a-token"></a>Concesión de acceso a los recursos de Azure mediante un token

Una cuenta de Automation puede utilizar su identidad administrada asignada por el usuario para obtener tokens para acceder a otros recursos protegidos por Azure AD, como Azure Key Vault. Estos tokens no representan a ningún usuario específico de la aplicación, sino que representan la aplicación que accede al recurso. En este caso, por ejemplo, el token representa una cuenta de Automation.

Para poder usar la identidad administrada asignada por el usuario para la autenticación, configure el acceso para esa identidad en el recurso de Azure en el que planea usar la identidad. Para completar esta tarea, asigne el rol adecuado a esa identidad en el recurso de Azure de destino.

Siga la entidad de seguridad con menos privilegios y asigne cuidadosamente solo los permisos necesarios para ejecutar el runbook. Por ejemplo, si la cuenta de Automation solo es necesaria para iniciar o detener una VM de Azure, los permisos asignados a la cuenta de ejecución o a la identidad administrada solo deben ser para iniciar o detener la VM. De manera similar, si un runbook lee del almacenamiento de blobs, asigne permisos de solo lectura.

En este ejemplo se usa Azure PowerShell para mostrar cómo asignar el rol Colaborador en la suscripción al recurso de Azure de destino. El rol Colaborador se usa como ejemplo y puede ser necesario o no en su caso. Como alternativa, puede usar [Azure Portal](../role-based-access-control/role-assignments-portal.md) para asignar el rol al recurso de Azure de destino.

```powershell
New-AzRoleAssignment `
    -ObjectId <automation-Identity-object-id> `
    -Scope "/subscriptions/<subscription-id>" `
    -RoleDefinitionName "Contributor"
```

## <a name="authenticate-access-with-user-assigned-managed-identity"></a>Autenticación del acceso con una identidad administrada asignada por el usuario

Después de habilitar la identidad administrada asignada por el usuario para la cuenta de Automation y de conceder acceso a una identidad al recurso de destino, puede especificar esa identidad en los runbooks para los recursos que admiten la identidad administrada. Para ver la compatibilidad con las identidades, use el cmdlet Az [Connect-AzAccount](/powershell/module/az.accounts/Connect-AzAccount).

```powershell
# Ensures you do not inherit an AzContext in your runbook
Disable-AzContextAutosave -Scope Process

# Connect to Azure with user-assigned managed identity
$AzureContext = (Connect-AzAccount -Identity -AccountId <user-assigned-identity-ClientId>).context

# set and store context
$AzureContext = Set-AzContext -SubscriptionName $AzureContext.Subscription -DefaultProfile $AzureContext
```

## <a name="generate-an-access-token-without-using-azure-cmdlets"></a>Generación de un token de acceso sin usar cmdlets de Azure

Para los puntos de conexión HTTP, asegúrese de lo siguiente.
- El encabezado de metadatos debe estar presente y establecido en "true".
- Se debe pasar un recurso junto con la solicitud, como parámetro de consulta para una solicitud GET y como datos de formulario para una solicitud POST.
- El tipo de contenido de la solicitud Post debe ser `application/x-www-form-urlencoded`.

### <a name="get-access-token-for-user-assigned-managed-identity-using-http-get"></a>Obtención de un token de acceso para la identidad administrada asignada por el usuario mediante Http Get  

```powershell
$resource= "?resource=https://management.azure.com/"
$client_id="&client_id=<ClientId of USI>"
$url = $env:IDENTITY_ENDPOINT + $resource + $client_id 
$Headers = New-Object "System.Collections.Generic.Dictionary[[String],[String]]"  
$Headers.Add("Metadata", "True")
$accessToken = Invoke-RestMethod -Uri $url -Method 'GET' -Headers $Headers
Write-Output $accessToken.access_token 
```

### <a name="get-access-token-for-user-assigned-managed-identity-using-http-post"></a>Obtención de un token de acceso para la identidad administrada asignada por el usuario mediante Http Post

```powershell
$url = $env:IDENTITY_ENDPOINT
$headers = New-Object "System.Collections.Generic.Dictionary[[String],[String]]"
$headers.Add("Metadata", "True")
$body = @{'resource'='https://management.azure.com/' 
'client_id'='<ClientId of USI>'}
$accessToken = Invoke-RestMethod $url -Method 'POST' -Headers $headers -ContentType 'application/x-www-form-urlencoded' -Body $body
Write-Output $accessToken.access_token 
```

### <a name="using-user-assigned-managed-identity-in-azure-powershell"></a>Uso de una identidad administrada asignada por el usuario en Azure PowerShell

```powershell
Write-Output "Connecting to azure via  Connect-AzAccount -Identity -AccountId <ClientId of USI>"  
Connect-AzAccount -Identity -AccountId <ClientId of USI> 
Write-Output "Successfully connected with Automation account's Managed Identity"  
Write-Output "Trying to fetch value from key vault using User Assigned Managed identity. Make sure you have given correct access to Managed Identity"  
$secret = Get-AzKeyVaultSecret -VaultName '<KVname>' -Name '<KeyName>'  
$ssPtr = [System.Runtime.InteropServices.Marshal]::SecureStringToBSTR($secret.SecretValue)  
try {  
  $secretValueText = [System.Runtime.InteropServices.Marshal]::PtrToStringBSTR($ssPtr)  
    Write-Output $secretValueText  
} finally {  
    [System.Runtime.InteropServices.Marshal]::ZeroFreeBSTR($ssPtr)  
} 
```

### <a name="using-user-assigned-managed-identity-in-python-runbook"></a>Uso de una identidad administrada asignada por el usuario en un runbook de Python

```python
#!/usr/bin/env python3  
import os  
import requests   

resource = "?resource=https://management.azure.com/" 
client_id = "&client_id=<ClientId of USI>" 
endPoint = os.getenv('IDENTITY_ENDPOINT')+ resource +client_id 
payload={}  
headers = {  
  'Metadata': 'True'  
}  
response = requests.request("GET", endPoint, headers=headers, data=payload)  
print(response.text) 
```

## <a name="next-steps"></a>Pasos siguientes

- Si los runbooks no se completan correctamente, revise [Solución de problemas de identidad administrada de Azure Automation (versión preliminar)](troubleshoot/managed-identity.md).

- Si necesita deshabilitar una identidad administrada, consulte [Deshabilitación de la identidad administrada de la identidad administrada de la cuenta de Azure Automation (versión preliminar)](disable-managed-identity-for-automation.md).

- Para información general sobre la seguridad de l cuenta de Azure Automation, consulte [Introducción a la autenticación de cuentas de Automation](automation-security-overview.md).