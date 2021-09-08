---
title: Deshabilitación de una identidad administrada asignada por el sistema para una cuenta de Azure Automation (versión preliminar)
description: En este artículo, se explica cómo deshabilitar una identidad administrada asignada por el sistema para una cuenta de Azure Automation.
services: automation
ms.subservice: process-automation
ms.date: 07/24/2021
ms.topic: conceptual
ms.openlocfilehash: 7c0d2d1f64d0d931c670b87438a032c646c45f9d
ms.sourcegitcommit: 98e126b0948e6971bd1d0ace1b31c3a4d6e71703
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 07/26/2021
ms.locfileid: "114673926"
---
# <a name="disable-system-assigned-managed-identity-for-azure-automation-account-preview"></a>Deshabilitación de una identidad administrada asignada por el sistema para una cuenta de Azure Automation (versión preliminar)

Puede deshabilitar una identidad administrada asignada por el sistema en Azure Automation mediante Azure Portal o mediante la API REST.

## <a name="disable-using-the-azure-portal"></a>Deshabilitación mediante Azure Portal

Puede deshabilitar la identidad administrada asignada por el sistema desde Azure Portal independientemente de cómo la haya configurado originalmente.

1. Inicie sesión en [Azure Portal](https://portal.azure.com).

1. Vaya a la cuenta de Automation y, en **Configuración de la cuenta**, seleccione **Identidad**.

1. En la pestaña **Asignada por el sistema**, en el botón **Estado**, seleccione **Desactivado** y, a continuación, seleccione **Guardar**. Cuando se le solicite confirmación, seleccione **Sí**.

La identidad administrada asignada por el sistema se deshabilitará y ya no tendrá acceso al recurso de destino.

## <a name="disable-using-rest-api"></a>Deshabilitación mediante la API REST

A continuación, se proporcionan los pasos de ejemplo y la sintaxis.

### <a name="request-body"></a>Cuerpo de la solicitud

El cuerpo de la solicitud siguiente deshabilita la identidad administrada asignada por el sistema y quita todas las identidades administradas asignadas por el usuario mediante el método HTTP **PATCH**.

```json
{ 
 "identity": { 
   "type": "None" 
  } 
}

```

Si hay varias identidades asignadas por el usuario definidas, para conservarlas y quitar solo la identidad asignada por el sistema, debe especificar cada identidad asignada por el usuario mediante una lista delimitada por comas. En el ejemplo siguiente, se usa el método HTTP **PATCH**.

```json
{ 
"identity" : {
    "type": "UserAssigned",
    "userAssignedIdentities": {
        "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/resourceGroupName/providers/Microsoft.ManagedIdentity/userAssignedIdentities/firstIdentity": {},
        "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/resourceGroupName/providers/Microsoft.ManagedIdentity/userAssignedIdentities/secondIdentity": {}
        }
    }
}
```

A continuación, se muestra el identificador URI de solicitud de la API REST del servicio para enviar la solicitud PATCH.

```http
PATCH https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/resource-group-name/providers/Microsoft.Automation/automationAccounts/automation-account-name?api-version=2020-01-13-preview
```

### <a name="example"></a>Ejemplo

Lleve a cabo los siguiente pasos.

1. Copie y pegue el cuerpo de la solicitud, en función de la operación que desee realizar, en un archivo llamado `body_remove_sa.json`. Guarde el archivo en la máquina local o en una cuenta de almacenamiento de Azure.

1. Inicie sesión en Azure de forma interactiva con el cmdlet [Connect-AzAccount](/powershell/module/Az.Accounts/Connect-AzAccount) y siga las instrucciones.

    ```powershell
    # Sign in to your Azure subscription
    $sub = Get-AzSubscription -ErrorAction SilentlyContinue
    if(-not($sub))
    {
        Connect-AzAccount -Subscription
    }
    
    # If you have multiple subscriptions, set the one to use
    # Select-AzSubscription -SubscriptionId "<SUBSCRIPTIONID>"
    ```

1. Proporcione un valor adecuado para las variables y, a continuación, ejecute el script.

    ```powershell
    $subscriptionID = "subscriptionID"
    $resourceGroup = "resourceGroupName"
    $automationAccount = "automationAccountName"
    $file = "path\body_remove_sa.json"
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

    En función de la sintaxis que se utilice, la salida será `UserAssigned` o en blanco.

## <a name="next-steps"></a>Pasos siguientes

- Para más información acerca de cómo habilitar las identidades administradas en Azure Automation, consulte [Habilitación y uso de la identidad administrada para Automation (versión preliminar)](enable-managed-identity-for-automation.md).

- Para información general acerca de la seguridad de la cuenta de Automation, consulte [Introducción a la autenticación de cuentas de Automation](automation-security-overview.md).