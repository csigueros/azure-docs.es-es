---
title: 'Asignación de una identidad administrada a un rol de aplicación mediante CLI de Azure: Azure AD'
description: Instrucciones paso a paso para asignar acceso de identidad administrada al rol de otra aplicación mediante CLI de Azure.
services: active-directory
documentationcenter: ''
author: christoc
manager: ''
editor: ''
ms.service: active-directory
ms.subservice: msi
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 11/03/2021
ms.author: christoc
ms.collection: M365-identity-device-management
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 846085c773c1923f581968c06c02c5c4f8372e65
ms.sourcegitcommit: c434baa76153142256d17c3c51f04d902e29a92e
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/10/2021
ms.locfileid: "132180202"
---
# <a name="assign-a-managed-identity-access-to-an-application-role-using-azure-cli"></a>Asignación de acceso de identidad administrada a un rol de aplicación mediante CLI de Azure

Las identidades administradas para los recursos de Azure proporcionan a los servicios de Azure una identidad en Azure Active Directory. Funcionan sin necesidad de usar credenciales en el código. Los servicios de Azure usan esta identidad para autenticarse en servicios compatibles con la autenticación de Azure AD. Los roles de aplicación ofrecen una forma de control de acceso basado en roles y permiten que un servicio implemente reglas de autorización.

En este artículo, aprenderá a asignar una identidad administrada a un rol de aplicación mostrado por otra aplicación mediante CLI de Azure.

## <a name="prerequisites"></a>Prerrequisitos

- Si no está familiarizado con las identidades administradas de los recursos de Azure, consulte la [sección de introducción](overview.md). **No olvide revisar la [diferencia entre una identidad administrada asignada por el sistema y una identidad administrada asignada por el usuario](overview.md#managed-identity-types)** .

[!INCLUDE [azure-cli-prepare-your-environment-no-header.md](../../../includes/azure-cli-prepare-your-environment-no-header.md)]

## <a name="assign-a-managed-identity-access-to-another-applications-app-role"></a>Asignación de acceso de identidad administrada al rol de aplicación de otra aplicación

1. Habilite la identidad administrada en un recurso de Azure, [como una máquina virtual de Azure](qs-configure-cli-windows-vm.md).

1. Busque el identificador de objeto de la entidad de servicio de la identidad administrada.

   **En el caso de una identidad administrada asignada por el sistema**, encontrará el identificador de objeto en la página de **identidad** del recurso en Azure Portal. También puede usar el siguiente script para buscar el identificador de objeto. Necesitará el identificador del recurso que creó en el paso 1, que está disponible en la página **Propiedades** del recurso en Azure Portal.

    ```azurecli
    resourceIdWithManagedIdentity="/subscriptions/{my subscription ID}/resourceGroups/{my resource group name}/providers/Microsoft.Compute/virtualMachines/{my virtual machine name}"
    
    oidForMI=$(az resource show --ids $resourceIdWithManagedIdentity --query "identity.principalId" -o tsv | tr -d '[:space:]')
    echo "object id for managed identity is: $oidForMI"
    ```

    **En el caso de una identidad administrada asignada por el usuario**, encontrará el identificador de objeto de la identidad administrada en la página de **información general** del recurso en Azure Portal. También puede usar el siguiente script para buscar el identificador de objeto. Necesitará el identificador del recurso de la identidad administrada asignada por el usuario.

    ```azurecli
    userManagedIdentityResourceId="/subscriptions/{my subscription ID}/resourceGroups/{my resource group name}/providers/Microsoft.ManagedIdentity/userAssignedIdentities/{my managed identity name}"
    
    oidForMI=$(az resource show --id $userManagedIdentityResourceId --query "properties.principalId" -o tsv | tr -d '[:space:]')
    echo "object id for managed identity is: $oidForMI"
    ```

1. Cree un nuevo registro de aplicación que represente el servicio al que la identidad administrada enviará una solicitud. Omita este paso si la API o el servicio que muestran la concesión de rol de aplicación a la identidad administrada ya tiene una entidad de servicio en el inquilino de Azure AD.

1. Busque el identificador de objeto de la entidad de servicio de la aplicación de servicio. Puede encontrarlo en Azure Portal. Vaya a Azure Active Directory y abra la página **Aplicaciones empresariales**. A continuación, busque la aplicación y el **identificador de objeto**. También puede buscar el identificador de objeto de la entidad de servicio por su nombre para mostrar. Para ello, use el siguiente script:

    ```azurecli
    appName="{name for your application}"
    serverSPOID=$(az ad sp list --filter "displayName eq 'My App'" --query '[0].objectId' -o tsv | tr -d '[:space:]')
    echo "object id for server service principal is: $serverSPOID"
    ```

    > [!NOTE]
    > Los nombres para mostrar de las aplicaciones no son únicos, por lo que debe comprobar que obtiene la entidad de servicio de la aplicación correcta.

    También puede encontrar el identificador de objeto por el identificador de aplicación único para el registro de la aplicación:

    ```azurecli
    appID="{application id for your application}"
    serverSPOID=$(az ad sp list --filter "appId eq '$appID'" --query '[0].objectId' -o tsv | tr -d '[:space:]')
    echo "object id for server service principal is: $serverSPOID"
    ```

1. Agregue un [rol de aplicación](../develop/howto-add-app-roles-in-azure-ad-apps.md) a la aplicación que creó en el paso 3. Puede crear el rol mediante Azure Portal o Microsoft Graph. Por ejemplo, podría agregar un rol de aplicación como el siguiente:

    ```json
    {
        "allowedMemberTypes": [
            "Application"
        ],
        "displayName": "Read data from MyApi",
        "id": "0566419e-bb95-4d9d-a4f8-ed9a0f147fa6",
        "isEnabled": true,
        "description": "Allow the application to read data as itself.",
        "value": "MyApi.Read.All"
    }
    ```

1. Asigne el rol de aplicación a la identidad administrada. Para asignar el rol de aplicación, necesitará la siguiente información:
    * `managedIdentityObjectId`: el identificador de objeto de la entidad de servicio de la identidad administrada, que encontró en el paso 2.
    * `serverServicePrincipalObjectId`: el identificador de objeto de la entidad de servicio de la aplicación de servidor, que encontró en el paso 4.
    * `appRoleId`: el identificador del rol de aplicación mostrado por la aplicación de servidor, que se generó en el paso 5; en el ejemplo, el identificador del rol de aplicación es `0566419e-bb95-4d9d-a4f8-ed9a0f147fa6`.
   
   Ejecute el siguiente script para agregar la asignación de roles.  Tenga en cuenta que esta funcionalidad no se expone directamente en CLI de Azure y que aquí se usa un comando REST en su lugar:

    ```azurecli
    roleguid="0566419e-bb95-4d9d-a4f8-ed9a0f147fa6"
    az rest -m POST -u https://graph.microsoft.com/beta/servicePrincipals/$oidForMI/appRoleAssignments -b "{\"principalId\": \"$oidForMI\", \"resourceId\": \"$serverSPOID\",\"appRoleId\": \"$roleguid\"}"
    ```

## <a name="next-steps"></a>Pasos siguientes

- [Identidades administradas de recursos de Azure](overview.md)
- Para habilitar la identidad administrada de una máquina virtual de Azure, consulte [Configuración de identidades administradas de recursos de Azure en una VM de Azure mediante PowerShell](qs-configure-cli-windows-vm.md).
