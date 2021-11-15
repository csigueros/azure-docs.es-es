---
title: 'Inicio rápido: Creación de un laboratorio con una API REST'
description: En este inicio rápido, creará un laboratorio en Azure DevTest Labs mediante una API REST de Azure.
ms.topic: quickstart
ms.date: 10/27/2021
ms.openlocfilehash: 83a1509f36f53d51f63f7dbc39ea2d1f6794dc54
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/03/2021
ms.locfileid: "131478329"
---
# <a name="quickstart-create-a-lab-in-azure-devtest-labs-using-azure-rest-api"></a>Inicio rápido: Creación de un laboratorio en Azure DevTest Labs mediante una API REST de Azure.

Introducción a Azure DevTest Labs mediante una API REST de Azure. Azure DevTest Labs abarca un grupo de recursos, como máquinas virtuales (VM) y redes de Azure. Esta infraestructura permite administrar mejor esos recursos mediante la especificación de límites y cuotas.  La API REST de Azure permite administrar las operaciones en los servicios hospedados en la plataforma Azure.

## <a name="prerequisites"></a>Prerrequisitos

- Una cuenta de Azure con una suscripción activa. [Cree una cuenta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

- Instalación del [módulo Az](/powershell/azure/new-azureps-module-az) de PowerShell. Asegúrese de disponer de la versión más reciente. Si es necesario, ejecute `Update-Module -Name Az`.

## <a name="prepare-request-body"></a>Preparación del cuerpo de la solicitud

Prepare el [cuerpo de la solicitud](/rest/api/dtl/labs/create-or-update.md#request-body) para que lo consuma la llamada REST.

Copie y pegue la siguiente sintaxis JSON en un archivo denominado `body.json`. Guarde el archivo en la máquina local o en una cuenta de almacenamiento de Azure.

```json
{
  "properties": {
    "labStorageType": "Standard"
  },
  "location": "westus2",
  "tags": {
    "Env": "alpha"
  }
}
```

## <a name="sign-in-to-your-azure-subscription"></a>Inicie sesión en la suscripción de Azure

1. Proporcione un valor adecuado para las variables y, después, ejecute el script.

    ```powershell
    $subscription = "subscriptionID"
    $resourceGroup = "resourceGroupName"
    $labName = "labName"
    $file = "path\body.json"
    ```

1. En la estación de trabajo, inicie sesión en la suscripción de Azure con el cmdlet [Connect-AzAccount](/powershell/module/Az.Accounts/Connect-AzAccount) de PowerShell y siga las instrucciones en pantalla.

    ```powershell
    # Sign in to your Azure subscription
    $sub = Get-AzSubscription -ErrorAction SilentlyContinue
    if(-not($sub))
    {
        Connect-AzAccount
    }
    
    # If you have multiple subscriptions, set the one to use
    # Set-AzContext -SubscriptionId $subscription
    ```

## <a name="build-request-body-for-submission"></a>Compilación del cuerpo de la solicitud para su envío

La sintaxis de la solicitud PUT es:<br>
 `https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.DevTestLab/labs/{name}?api-version=2018-09-15`.

 Ejecute los siguientes scripts de PowerShell para pasar el valor de la solicitud a un parámetro. El contenido del cuerpo de la solicitud también se pasa a un parámetro.

```powershell
# build URI
$URI = "https://management.azure.com/subscriptions/$subscription/resourceGroups/$resourceGroup/providers/Microsoft.DevTestLab/labs/$labName`?api-version=2018-09-15"

# build body
$body = Get-Content $file
```

## <a name="obtain-an-authentication-token"></a>Obtención de un token de autenticación

Use los siguientes comandos para recuperar un token de autenticación:

```powershell
$azContext = Get-AzContext
$azProfile = [Microsoft.Azure.Commands.Common.Authentication.Abstractions.AzureRmProfileProvider]::Instance.Profile
$profileClient = New-Object -TypeName Microsoft.Azure.Commands.ResourceManager.Common.RMProfileClient -ArgumentList ($azProfile)
$token = $profileClient.AcquireAccessToken($azContext.Subscription.TenantId)
$authHeader = @{
    'Content-Type'='application/json'
    'Authorization'='Bearer ' + $token.AccessToken
}
```

## <a name="invoke-the-rest-api"></a>Invocación de la API REST

Use los siguientes comandos para invocar a la API REST y revisar la respuesta.

```powershell
# Invoke the REST API
$response = Invoke-RestMethod -Uri $URI -Method PUT -Headers $authHeader -Body $body

# Review output
$response | ConvertTo-Json
```

La respuesta debe ser similar al texto siguiente:

```output
{
    "properties":  {
                       "labStorageType":  "Standard",
                       "mandatoryArtifactsResourceIdsLinux":  [

                                                              ],
                       "mandatoryArtifactsResourceIdsWindows":  [

                                                                ],
                       "createdDate":  "2021-10-27T20:22:49.7495913+00:00",
                       "premiumDataDisks":  "Disabled",
                       "environmentPermission":  "Reader",
                       "announcement":  {
                                            "title":  "",
                                            "markdown":  "",
                                            "enabled":  "Disabled",
                                            "expired":  false
                                        },
                       "support":  {
                                       "enabled":  "Disabled",
                                       "markdown":  ""
                                   },
                       "provisioningState":  "Creating",
                       "uniqueIdentifier":  "uniqueID"
                   },
    "id":  "/subscriptions/ContosoID/resourcegroups/groupcontoso/providers/microsoft.devtestlab/labs/myotherlab",

    "name":  "myOtherLab",
    "type":  "Microsoft.DevTestLab/labs",
    "location":  "westus2",
    "tags":  {
                 "Env":  "alpha"
             }
}
```

## <a name="clean-up-resources"></a>Limpieza de recursos

Si no va a seguir usando este laboratorio, lleve a cabo los siguientes pasos para eliminarlo:

1. Proporcione un valor adecuado para las variables y, después, ejecute el script.

    ```powershell
    $subscription = "subscriptionID"
    $resourceGroup = "resourceGroupName"
    $labName = "labName"
    ```

1. Ejecute el siguiente script para quitar el laboratorio con nombre de Azure DevTest Labs.

    ```powershell
    # build URI
    $URI = "https://management.azure.com/subscriptions/$subscription/resourceGroups/$resourceGroup/providers/Microsoft.DevTestLab/labs/$labName`?api-version=2018-09-15"
    
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
    Invoke-RestMethod -Uri $URI -Method DELETE -Headers $authHeader
    ```

## <a name="next-steps"></a>Pasos siguientes

En este inicio rápido, ha creado un laboratorio mediante la API REST de Azure. Para aprender a acceder al laboratorio, vaya al tutorial siguiente:

> [!div class="nextstepaction"]
> [Tutorial: Acceso al laboratorio](../tutorial-use-custom-lab.md)
