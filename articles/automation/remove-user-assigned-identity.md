---
title: Eliminación de una identidad administrada asignada por el usuario para una cuenta de Azure Automation (versión preliminar)
description: En este artículo se explica cómo quitar una identidad administrada por el usuario para una cuenta de Azure Automation.
services: automation
ms.subservice: process-automation
ms.date: 07/24/2021
ms.topic: conceptual
ms.openlocfilehash: 2f5aba42974c34f9d6799f3965848bdd0ca3787c
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/24/2021
ms.locfileid: "128635173"
---
# <a name="remove-user-assigned-managed-identity-for-azure-automation-account-preview"></a>Eliminación de una identidad administrada asignada por el usuario para una cuenta de Azure Automation (versión preliminar)

Puede eliminar una identidad administrada asignada por el usuario de Azure Automation mediante Azure Portal, PowerShell, la API REST de Azure o una plantilla de Azure Resource Manager (ARM).

## <a name="remove-using-the-azure-portal"></a>Eliminación mediante Azure Portal

Puede eliminar la identidad administrada asignada por el usuario desde Azure Portal independientemente de cómo la haya agregado originalmente.

1. Inicie sesión en [Azure Portal](https://portal.azure.com).

1. Vaya a la cuenta de Automation y, en **Configuración de la cuenta**, seleccione **Identidad**.

1. Seleccione la pestaña **Usuario asignado**.

1. Seleccione la identidad administrada asignada por el usuario que se va a quitar de la lista.

1. Seleccione **Quitar**. Cuando se le solicite confirmación, seleccione **Sí**.

La identidad administrada asignada por el usuario se quitará y ya no tendrá acceso al recurso de destino.

## <a name="remove-using-powershell"></a>Eliminación con PowerShell

Use el cmdlet de PowerShell [Set-AzAutomationAccount](/powershell/module/az.automation/set-azautomationaccount) para quitar todas las identidades administradas asignadas por el usuario y conservar una identidad administrada asignada por el sistema existente.

1. Inicie sesión en Azure de forma interactiva con el cmdlet [Connect-AzAccount](/powershell/module/Az.Accounts/Connect-AzAccount) y siga las instrucciones.

    ```powershell
    # Sign in to your Azure subscription
    $sub = Get-AzSubscription -ErrorAction SilentlyContinue
    if(-not($sub))
    {
        Connect-AzAccount
    }
    ```

1. Proporcione un valor adecuado para las variables y, después, ejecute el script.

    ```powershell
    $resourceGroup = "resourceGroupName"
    $automationAccount = "automationAccountName"
    ```

1. Ejecute [Set-AzAutomationAccount](/powershell/module/az.automation/set-azautomationaccount).

    ```powershell
    # Removes all UAs, keeps SA
    $output = Set-AzAutomationAccount `
        -ResourceGroupName $resourceGroup `
        -Name $automationAccount `
        -AssignSystemIdentity 
    
    $output.identity.Type
    ```

    El resultado será `SystemAssigned`.

## <a name="remove-using-rest-api"></a>Eliminación mediante la API REST

Puede eliminar una identidad administrada asignada por el usuario de la cuenta de Automation mediante el siguiente ejemplo y llamada a la API REST.

### <a name="request-body"></a>Cuerpo de la solicitud

Escenario: la identidad administrada asignada por el sistema está habilitada o se va a habilitar. Se va a quitar una de las muchas identidades administradas asignadas por el usuario. En este ejemplo se quita una identidad administrada asignada por el usuario denominada `firstIdentity` mediante el método HTTP **PATCH**.

```json
{
  "identity": {
    "type": "SystemAssigned, UserAssigned",
    "userAssignedIdentities": {
      "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/resource-group-name/providers/Microsoft.ManagedIdentity/userAssignedIdentities/firstIdentity": null
    }
  }
}
```

Escenario: la identidad administrada asignada por el sistema está habilitada o se va a habilitar. Todas las identidades administradas asignadas por el usuario se quitarán mediante el método HTTP **PUT**.

```json
{
  "identity": {
    "type": "SystemAssigned"
  }
}
```

Escenario: la identidad administrada asignada por el sistema está deshabilitada o se va a deshabilitar. Se va a quitar una de las muchas identidades administradas asignadas por el usuario. En este ejemplo se quita una identidad administrada asignada por el usuario denominada `firstIdentity` mediante el método HTTP **PATCH**.

```json
{
  "identity": {
    "type": "UserAssigned",
    "userAssignedIdentities": {
      "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/resource-group-name/providers/Microsoft.ManagedIdentity/userAssignedIdentities/firstIdentity": null
    }
  }
}

```

Escenario: la identidad administrada asignada por el sistema está deshabilitada o se va a deshabilitar. Todas las identidades administradas asignadas por el usuario se quitarán mediante el método HTTP **PUT**.

```json
{
  "identity": {
    "type": "None"
  }
}
```

A continuación se muestra el URI de solicitud de la API REST del servicio para enviar la solicitud PATCH.

```http
https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/resource-group-name/providers/Microsoft.Automation/automationAccounts/automation-account-name?api-version=2020-01-13-preview
```

### <a name="example"></a>Ejemplo

Lleve a cabo los siguiente pasos.

1. Copie y pegue el cuerpo de la solicitud, en función de la operación que quiera realizar, en un archivo denominado `body_remove_ua.json`. Realice las modificaciones necesarias y guarde el archivo en el equipo local o en una cuenta de Azure Storage.

1. Inicie sesión en Azure de forma interactiva con el cmdlet [Connect-AzAccount](/powershell/module/Az.Accounts/Connect-AzAccount) y siga las instrucciones.

    ```powershell
    # Sign in to your Azure subscription
    $sub = Get-AzSubscription -ErrorAction SilentlyContinue
    if(-not($sub))
    {
        Connect-AzAccount -Subscription
    }
    ```

1. Proporcione un valor adecuado para las variables y, después, ejecute el script.

    ```powershell
    $subscriptionID = "subscriptionID"
    $resourceGroup = "resourceGroupName"
    $automationAccount = "automationAccountName"
    $file = "path\body_remove_ua.json"
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
    Invoke-RestMethod -Uri $URI -Method PATCH -Headers $authHeader -Body $body
    
    # Confirm removal
    (Get-AzAutomationAccount `
        -ResourceGroupName $resourceGroup `
        -Name $automationAccount).Identity.Type
    ```

   En función de la sintaxis que se utilice, el resultado será `SystemAssignedUserAssigned`, `SystemAssigned`, `UserAssigned` o en blanco.

## <a name="remove-using-azure-resource-manager-template"></a>Eliminación mediante la plantilla de Azure Resource Manager

Si ha agregado la identidad administrada asignada por el usuario para la cuenta de Automation mediante una plantilla de Azure Resource Manager, puede quitar la identidad administrada asignada por el usuario modificando la plantilla y ejecutándola de nuevo.

Escenario: la identidad administrada asignada por el sistema está habilitada o se va a habilitar. Se va a quitar una de las dos identidades administradas asignadas por el usuario. Este fragmento de código de sintaxis quita **todas** las identidades administradas asignadas por el usuario, **excepto** la que se pasa como parámetro a la plantilla.

```json
...
"identity": {
    "type": "SystemAssigned, UserAssigned",
    "userAssignedIdentities": {
        "[resourceID('Microsoft.ManagedIdentity/userAssignedIdentities/',parameters('userAssignedOne'))]": {}
    }
},
...
```

Escenario: la identidad administrada asignada por el sistema está habilitada o se va a habilitar. Se quitarán todas las identidades administradas asignadas por el usuario.

```json
...
"identity": {
    "type": "SystemAssigned"
},
...
```

Escenario: la identidad administrada asignada por el sistema está deshabilitada o se va a deshabilitar. Se va a quitar una de las dos identidades administradas asignadas por el usuario. Este fragmento de código de sintaxis quita **todas** las identidades administradas asignadas por el usuario, **excepto** la que se pasa como parámetro a la plantilla.

```json
...
"identity": {
    "type": "UserAssigned",
    "userAssignedIdentities": {
        "[resourceID('Microsoft.ManagedIdentity/userAssignedIdentities/',parameters('userAssignedOne'))]": {}
    }
},
...
```

Use el cmdlet [Get-AzAutomationAccount](/powershell/module/az.automation/get-azautomationaccount) para realizar la comprobación. En función de la sintaxis que se utilice, el resultado será `SystemAssignedUserAssigned`, `SystemAssigned` o `UserAssigned`.

```powershell
(Get-AzAutomationAccount `
    -ResourceGroupName $resourceGroup `
    -Name $automationAccount).Identity.Type
```

## <a name="next-steps"></a>Pasos siguientes

- Para obtener más información acerca de cómo habilitar las identidades administradas en Azure Automation, consulte [Habilitación y uso de la identidad administrada para Automation (versión preliminar)](enable-managed-identity-for-automation.md).

- Para información general acerca de la seguridad de la cuenta de Automation, consulte [Introducción a la autenticación de cuentas de Automation](automation-security-overview.md).
