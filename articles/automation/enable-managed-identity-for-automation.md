---
title: Uso de una identidad administrada asignada por el sistema para una cuenta de Azure Automation (versión preliminar)
description: En este artículo se describe cómo configurar la identidad administrada para las cuentas de Azure Automation.
services: automation
ms.subservice: process-automation
ms.date: 08/12/2021
ms.topic: conceptual
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 882a55d017ed23dc7abbc9096e38f70abb41c425
ms.sourcegitcommit: f53f0b98031cd936b2cd509e2322b9ee1acba5d6
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/30/2021
ms.locfileid: "123214286"
---
# <a name="using-a-system-assigned-managed-identity-for-an-azure-automation-account-preview"></a>Uso de una identidad administrada asignada por el sistema para una cuenta de Azure Automation (versión preliminar)

En este artículo, se muestra cómo agregar una identidad administrada asignada por el sistema para una cuenta de Azure Automation y cómo usarla para acceder a otros recursos. Para más información sobre cómo funcionan las identidades administradas con Azure Automation, consulte [Identidades administradas (versión preliminar)](automation-security-overview.md#managed-identities-preview).

Si no tiene una suscripción a Azure, cree una [cuenta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de empezar.

## <a name="prerequisites"></a>Prerrequisitos

- Una cuenta de Azure Automation Para obtener instrucciones, consulte [Creación de una cuenta de Azure Automation](automation-quickstart-create-account.md).

- La versión más reciente de los módulos de cuenta de Azure. Actualmente, es la versión 2.2.8. (Consulte [Az.Accounts](https://www.powershellgallery.com/packages/Az.Accounts/) para obtener más información sobre esta versión).

- Un recurso de Azure al que desea acceder desde el runbook de Automation. Este recurso debe tener un rol definido para la identidad administrada, lo que ayuda al runbook de Automation a autenticar el acceso al recurso. Para agregar roles, debe ser propietario del recurso en el inquilino de Azure AD correspondiente.

- Si desea ejecutar trabajos híbridos mediante una identidad administrada, actualice Hybrid Runbook Worker a la versión más reciente. Las versiones mínimas obligatorias son:

  - Hybrid Runbook Worker de Windows: versión 7.3.1125.0
  - Hybrid Runbook Worker de Linux: versión 1.7.4.0

## <a name="enable-a-system-assigned-managed-identity-for-an-azure-automation-account"></a>Habilitación de una identidad administrada asignada por el sistema para la cuenta de Azure Automation

Una vez habilitada, las siguientes propiedades se asignarán a la identidad administrada asignada por el sistema.

|Propiedad (JSON) | Value | Descripción|
|----------|-----------|------------|
| principalid | \<principal-ID\> | Identificador único global (GUID) del objeto de entidad de servicio para la identidad administrada por el sistema que representa la cuenta de Automation en el inquilino de Azure AD. Este GUID aparece a veces como "Id. de objeto" u objectID. |
| tenantid | \<Azure-AD-tenant-ID\> | Identificador único global (GUID) que representa el inquilino de Azure AD del que es ahora miembro la cuenta de Automation. En el inquilino de Azure AD, la entidad de servicio tiene el mismo nombre que la cuenta de Automation. |

Puede agregar una identidad administrada asignada por el sistema para una cuenta de Azure Automation mediante Azure Portal, PowerShell, la API REST de Azure o una plantilla de ARM. Para los ejemplos que implican PowerShell, inicie sesión primero en Azure de forma interactiva con el cmdlet [Connect-AzAccount](/powershell/module/Az.Accounts/Connect-AzAccount) y siga las instrucciones.

```powershell
# Sign in to your Azure subscription
$sub = Get-AzSubscription -ErrorAction SilentlyContinue
if(-not($sub))
{
    Connect-AzAccount -Identity
}

# If you have multiple subscriptions, set the one to use
# Select-AzSubscription -SubscriptionId "<SUBSCRIPTIONID>"
```

A continuación, inicialice un conjunto de variables que se usarán a lo largo de los ejemplos. Revise los valores siguientes y, a continuación, ejecute.

```powershell
$subscriptionID = "subscriptionID"
$resourceGroup = "resourceGroupName"
$automationAccount = "automationAccountName"
```

> [!IMPORTANT]
> La nueva identidad de nivel de cuenta de Automation invalidará las identidades asignadas por el sistema de nivel de máquina virtual anteriores que se describen en [Uso de la autenticación de runbook con identidades administradas](./automation-hrw-run-runbooks.md#runbook-auth-managed-identities). Si ejecuta trabajos híbridos en máquinas virtuales de Azure que usan la identidad asignada por el sistema de la máquina virtual para acceder a los recursos del runbook, la identidad de la cuenta de Automation se usará para los trabajos híbridos. Esto significa que la ejecución del trabajo existente puede verse afectada si ha estado usando la característica Claves administradas de cliente (CMK) de la cuenta de Automation.<br/><br/>Si desea seguir usando la identidad administrada de la máquina virtual, no debe habilitar la identidad de nivel de cuenta de Automation. Si ya la ha habilitado, puede deshabilitar la identidad administrada por el sistema de la cuenta de Automation. Consulte [Deshabilitación de la identidad administrada de la cuenta de Azure Automation.](./disable-managed-identity-for-automation.md)

### <a name="enable-using-the-azure-portal"></a>Habilitación de Azure Portal

Lleve a cabo los siguiente pasos:

1. Inicie sesión en [Azure Portal](https://portal.azure.com).

1. En Azure Portal, vaya a su cuenta de Automation.

1. En  **Configuración de la cuenta**, seleccione  **Identidad**.

1. Establezca la opción **Asignado por el sistema** en **Activado** y presione **Guardar**. Cuando se le solicite confirmación, seleccione **Sí**.

   :::image type="content" source="media/managed-identity/managed-identity-on.png" alt-text="Habilitación de una identidad asignada por el sistema en Azure Portal.":::

   Ahora la cuenta de Automation podrá usar la identidad asignada por el sistema, registrada con Azure Active Directory (Azure AD) y representada mediante un identificador de objeto.

   :::image type="content" source="media/managed-identity/managed-identity-object-id.png" alt-text="Identificador de objeto de la identidad administrada.":::

### <a name="enable-using-powershell"></a>Habilitar mediante PowerShell

Use el cmdlet de PowerShell [Set-AzAutomationAccount](/powershell/module/az.automation/set-azautomationaccount) para agregar las identidades administradas asignadas por el sistema.

```powershell
$output = Set-AzAutomationAccount `
    -ResourceGroupName $resourceGroup `
    -Name $automationAccount `
    -AssignSystemIdentity

$output
```

La salida debe tener una apariencia similar a la siguiente:

:::image type="content" source="media/enable-managed-identity-for-automation/set-azautomationaccount-output.png" alt-text="Salida del comando set-azautomationaccount command.":::

Para obtener una salida adicional, ejecute: `$output.identity | ConvertTo-Json`.

### <a name="enable-using-a-rest-api"></a>Habilitación mediante una API REST

A continuación, se proporcionan los pasos de ejemplo y la sintaxis.

#### <a name="syntax"></a>Sintaxis

La sintaxis del cuerpo siguiente permite una identidad administrada asignada por el sistema en una cuenta de Automation mediante el método HTTP **PATCH**. Sin embargo, esta sintaxis quitará las identidades administradas asignadas por el sistema existentes asociadas a la cuenta de Automation.

```json
{ 
 "identity": { 
   "type": "SystemAssigned" 
  } 
}
```

Si hay varias identidades asignadas por el usuario definidas, para conservarlas y quitar solo la identidad asignada por el sistema, debe especificar cada identidad asignada por el usuario mediante una lista delimitada por comas. En el ejemplo siguiente, se usa el método HTTP **PATCH**.

```json
{ 
  "identity" : {
    "type": "SystemAssigned, UserAssigned",
    "userAssignedIdentities": {
        "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/resourceGroupName/providers/Microsoft.ManagedIdentity/userAssignedIdentities/cmkID": {},
        "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/resourceGroupName/providers/Microsoft.ManagedIdentity/userAssignedIdentities/cmkID2": {}
    }
  }
}

```

La sintaxis de la API es la siguiente:

```http
PATCH https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/resource-group-name/providers/Microsoft.Automation/automationAccounts/automation-account-name?api-version=2020-01-13-preview
```

#### <a name="example"></a>Ejemplo

Lleve a cabo los siguiente pasos.

1. Copie y pegue la sintaxis del cuerpo en un archivo denominado `body_sa.json`. Guarde el archivo en la máquina local o en una cuenta de almacenamiento de Azure.

1. Actualice el valor de variable siguiente y, a continuación, ejecútelo.

    ```powershell
    $file = "path\body_sa.json"
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
        "PrincipalId":  "aaaaaaaa-aaaa-aaaa-aaaa-aaaaaaaaaaaa",
        "TenantId":  "bbbbbbbb-bbbb-bbbb-bbbb-bbbbbbbbbbbb",
        "Type":  0,
        "UserAssignedIdentities":  null
    }
    ```

### <a name="enable-using-an-arm-template"></a>Habilitación mediante una plantilla de ARM

A continuación, se proporcionan los pasos de ejemplo y la sintaxis.

#### <a name="template-syntax"></a>Sintaxis de plantillas

La sintaxis de plantilla de ejemplo siguiente permite una identidad administrada asignada por el sistema en la cuenta de Automation. Sin embargo, esta sintaxis quitará las identidades administradas asignadas por el sistema existentes asociadas a la cuenta de Automation.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "resources": [
    {
      "type": "Microsoft.Automation/automationAccounts",
      "apiVersion": "2020-01-13-preview",
      "name": "yourAutomationAccount",
      "location": "[resourceGroup().location]",
      "identity": {
        "type": "SystemAssigned"
        },
      "properties": {
        "sku": {
          "name": "Basic"
        }
      }
    }
  ]
}
```

#### <a name="example"></a>Ejemplo

Lleve a cabo los siguiente pasos.

1. Revise la sintaxis de la plantilla anterior para usar la cuenta de Automation y guárdela en un archivo denominado `template_sa.json`.

1. Actualice el valor de variable siguiente y, a continuación, ejecútelo.

    ```powershell
    $templateFile = "path\template_sa.json"
    ```

1. Use el cmdlet de PowerShell [New-AzResourceGroupDeployment](/powershell/module/az.resources/new-azresourcegroupdeployment) para implementar la plantilla.

    ```powershell
    New-AzResourceGroupDeployment `
        -Name "SystemAssignedDeployment" `
        -ResourceGroupName $resourceGroup `
        -TemplateFile $templateFile
    ```

   El comando no producirá ninguna salida; sin embargo, puede usar el código siguiente para comprobarlo:

    ```powershell
    (Get-AzAutomationAccount `
    -ResourceGroupName $resourceGroup `
    -Name $automationAccount).Identity | ConvertTo-Json
    ```

   La salida tendrá un aspecto similar al que se muestra en el ejemplo anterior de la API REST.

## <a name="give-access-to-azure-resources-by-obtaining-a-token"></a>Concesión de acceso a los recursos de Azure mediante un token

Una cuenta de Automation puede utilizar su identidad administrada asignada por el sistema para obtener tokens para acceder a otros recursos protegidos por Azure AD, como Azure Key Vault. Estos tokens no representan a ningún usuario específico de la aplicación, sino que representan la aplicación que accede al recurso. En este caso, por ejemplo, el token representa una cuenta de Automation.

Para poder usar la identidad administrada asignada por el sistema para la autenticación, configure el acceso para esa identidad en el recurso de Azure en el que planea usar la identidad. Para completar esta tarea, asigne el rol adecuado a esa identidad en el recurso de Azure de destino.

Siga la entidad de seguridad con menos privilegios y asigne cuidadosamente solo los permisos necesarios para ejecutar el runbook. Por ejemplo, si la cuenta de Automation solo es necesaria para iniciar o detener una VM de Azure, los permisos asignados a la cuenta de ejecución o a la identidad administrada solo deben ser para iniciar o detener la VM. De forma similar, si un runbook lee desde Blob Storage, asigne permisos de solo lectura. En este ejemplo, se usa Azure PowerShell para mostrar cómo asignar el colaborador.

En este ejemplo se usa Azure PowerShell para mostrar cómo asignar el rol Colaborador en la suscripción al recurso de Azure de destino. El rol Colaborador se usa como ejemplo y puede ser necesario o no en su caso.

```powershell
New-AzRoleAssignment `
    -ObjectId <automation-Identity-object-id> `
    -Scope "/subscriptions/<subscription-id>" `
    -RoleDefinitionName "Contributor"
```

## <a name="authenticate-access-with-system-assigned-managed-identity"></a>Autenticación del acceso con identidad administrada asignada por el sistema

Después de habilitar la identidad administrada para la cuenta de Automation y de conceder acceso a una identidad al recurso de destino, puede especificar esa identidad en los runbooks para los recursos que admiten la identidad administrada. Para compatibilidad con las identidades, use el cmdlet `Connect-AzAccount`. Consulte [Connect-AzAccount](/powershell/module/az.accounts/Connect-AzAccount) en la referencia de PowerShell. Reemplace `SubscriptionID` por el identificador de suscripción real y, a continuación, ejecute el siguiente comando:

```powershell
Connect-AzAccount -Identity
$AzureContext = Set-AzContext -SubscriptionId "SubscriptionID"
```

> [!NOTE]
> Si su organización sigue usando los cmdlets de AzureRM en desuso, puede usar `Connect-AzureRMAccount -Identity`.

## <a name="generate-an-access-token-without-using-azure-cmdlets"></a>Generación de un token de acceso sin usar cmdlets de Azure

Para los puntos de conexión HTTP, asegúrese de lo siguiente.

- El encabezado de metadatos debe estar presente y establecido en "true".
- Se debe pasar un recurso junto con la solicitud, como parámetro de consulta para una solicitud GET y como datos de formulario para una solicitud POST.
- X-IDENTITY-HEADER debe establecerse en el valor de la variable de entorno IDENTITY_HEADER para Hybrid Runbook Worker.
- El tipo de contenido de la solicitud Post debe ser "application/x-www-form-urlencoded".

### <a name="get-access-token-for-system-assigned-identity-using-http-get"></a>Obtención del token de acceso para la identidad asignada por el sistema mediante HTTP Get

```powershell
$resource= "?resource=https://management.azure.com/" 
$url = $env:IDENTITY_ENDPOINT + $resource 
$Headers = New-Object "System.Collections.Generic.Dictionary[[String],[String]]" 
$Headers.Add("X-IDENTITY-HEADER", $env:IDENTITY_HEADER) 
$Headers.Add("Metadata", "True") 
$accessToken = Invoke-RestMethod -Uri $url -Method 'GET' -Headers $Headers
Write-Output $accessToken.access_token
```

### <a name="get-access-token-for-system-assigned-identity-using-http-post"></a>Obtención del token de acceso para la identidad asignada por el sistema mediante HTTP Post

```powershell
$url = $env:IDENTITY_ENDPOINT  
$headers = New-Object "System.Collections.Generic.Dictionary[[String],[String]]" 
$headers.Add("X-IDENTITY-HEADER", $env:IDENTITY_HEADER) 
$headers.Add("Metadata", "True") 
$body = @{resource='https://management.azure.com/' } 
$accessToken = Invoke-RestMethod $url -Method 'POST' -Headers $headers -ContentType 'application/x-www-form-urlencoded' -Body $body 
Write-Output $accessToken.access_token
```

### <a name="using-system-assigned-managed-identity-in-azure-powershell"></a>Uso de la identidad administrada asignada por el sistema en Azure PowerShell

Para obtener más información, consulte [Get-AzKeyVaultSecret](/powershell/module/az.keyvault/get-azkeyvaultsecret).

```powershell
Write-Output "Connecting to azure via  Connect-AzAccount -Identity" 
Connect-AzAccount -Identity 
Write-Output "Successfully connected with Automation account's Managed Identity" 
Write-Output "Trying to fetch value from key vault using MI. Make sure you have given correct access to Managed Identity" 
$secret = Get-AzKeyVaultSecret -VaultName '<KVname>' -Name '<KeyName>' 

$ssPtr = [System.Runtime.InteropServices.Marshal]::SecureStringToBSTR($secret.SecretValue) 
try { 
  $secretValueText = [System.Runtime.InteropServices.Marshal]::PtrToStringBSTR($ssPtr) 
    Write-Output $secretValueText 
} finally { 
    [System.Runtime.InteropServices.Marshal]::ZeroFreeBSTR($ssPtr) 
}
```

### <a name="using-system-assigned-managed-identity-in-python-runbook"></a>Uso de una identidad administrada asignada por el sistema en un runbook de Python

```python
#!/usr/bin/env python3 
import os 
import requests  
# printing environment variables 
endPoint = os.getenv('IDENTITY_ENDPOINT')+"?resource=https://management.azure.com/" 
identityHeader = os.getenv('IDENTITY_HEADER') 
payload={} 
headers = { 
  'X-IDENTITY-HEADER': identityHeader,
  'Metadata': 'True' 
} 
response = requests.request("GET", endPoint, headers=headers, data=payload) 
print(response.text) 
```

### <a name="using-system-assigned-managed-identity-to-access-sql-database"></a>Uso de una identidad administrada asignada por el sistema para acceder a SQL Database

Para obtener más información sobre el aprovisionamiento del acceso a una base de datos de Azure SQL, consulte [Aprovisionamiento del administrador de Azure AD (SQL Database)](../azure-sql/database/authentication-aad-configure.md#provision-azure-ad-admin-sql-database).

```powershell
$queryParameter = "?resource=https://database.windows.net/" 
$url = $env:IDENTITY_ENDPOINT + $queryParameter
$Headers = New-Object "System.Collections.Generic.Dictionary[[String],[String]]" 
$Headers.Add("X-IDENTITY-HEADER", $env:IDENTITY_HEADER) 
$Headers.Add("Metadata", "True") 
$content =[System.Text.Encoding]::Default.GetString((Invoke-WebRequest -UseBasicParsing -Uri $url -Method 'GET' -Headers $Headers).RawContentStream.ToArray()) | ConvertFrom-Json 
$Token = $content.access_token 
echo "The managed identities for Azure resources access token is $Token" 
$SQLServerName = "<ServerName>"    # Azure SQL logical server name  
$DatabaseName = "<DBname>"     # Azure SQL database name 
Write-Host "Create SQL connection string" 
$conn = New-Object System.Data.SqlClient.SQLConnection  
$conn.ConnectionString = "Data Source=$SQLServerName.database.windows.net;Initial Catalog=$DatabaseName;Connect Timeout=30" 
$conn.AccessToken = $Token 
Write-host "Connect to database and execute SQL script" 
$conn.Open()  
$ddlstmt = "CREATE TABLE Person( PersonId INT IDENTITY PRIMARY KEY, FirstName NVARCHAR(128) NOT NULL)" 
Write-host " " 
Write-host "SQL DDL command" 
$ddlstmt 
$command = New-Object -TypeName System.Data.SqlClient.SqlCommand($ddlstmt, $conn) 
Write-host "results" 
$command.ExecuteNonQuery() 
$conn.Close()
```

## <a name="next-steps"></a>Pasos siguientes

- Si los runbooks no se completan correctamente, revise [Solución de problemas de identidad administrada de Azure Automation (versión preliminar)](troubleshoot/managed-identity.md).

- Si necesita deshabilitar una identidad administrada, consulte [Deshabilitación de la identidad administrada de la identidad administrada de la cuenta de Azure Automation (versión preliminar)](disable-managed-identity-for-automation.md).

- Para información general sobre la seguridad de l cuenta de Azure Automation, consulte [Introducción a la autenticación de cuentas de Automation](automation-security-overview.md).
