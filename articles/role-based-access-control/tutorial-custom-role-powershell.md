---
title: 'Tutorial: Creación de un rol personalizado de Azure con Azure PowerShell: Azure RBAC'
description: En este tutorial aprenderá a crear un rol personalizado de Azure mediante Azure PowerShell y el control de acceso basado en rol de Azure (Azure RBAC).
services: active-directory
documentationCenter: ''
author: rolyon
manager: mtillman
editor: ''
ms.service: role-based-access-control
ms.devlang: ''
ms.topic: tutorial
ms.tgt_pltfrm: ''
ms.workload: identity
ms.date: 02/20/2019
ms.author: rolyon
ms.openlocfilehash: a4fac2cde6f18e504dc2866cc479ce51e3b70b2a
ms.sourcegitcommit: 87de14fe9fdee75ea64f30ebb516cf7edad0cf87
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/01/2021
ms.locfileid: "129362011"
---
# <a name="tutorial-create-an-azure-custom-role-using-azure-powershell"></a>Tutorial: Creación de un rol personalizado de Azure mediante Azure PowerShell

Si los [roles integrados de Azure](built-in-roles.md) no cumplen las necesidades específicas de su organización, puede crear los suyos propios. En este tutorial, creará un rol personalizado llamado Reader Support Tickets (Lector de incidencias de soporte) con Azure PowerShell. El rol personalizado permite al usuario ver todo lo relativo al plano de control de una suscripción y también abrir incidencias de soporte técnico.

En este tutorial, aprenderá a:

> [!div class="checklist"]
> * Crear un rol personalizado
> * Lista de roles personalizados
> * Actualización de un rol personalizado
> * Eliminación de un rol personalizado

Si no tiene una suscripción a Azure, cree una [cuenta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de empezar.

[!INCLUDE [az-powershell-update](../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Prerrequisitos

Para realizar este tutorial, necesita:

- Permisos para crear roles personalizados, como [Propietario](built-in-roles.md#owner) o [Administrador de acceso de usuarios](built-in-roles.md#user-access-administrator)
- [Azure Cloud Shell](../cloud-shell/overview.md) o [Azure PowerShell](/powershell/azure/install-az-ps)

## <a name="sign-in-to-azure-powershell"></a>Inicio de sesión en Azure PowerShell

Inicie sesión en [Azure PowerShell](/powershell/azure/authenticate-azureps).

## <a name="create-a-custom-role"></a>Crear un rol personalizado

El modo más sencillo de crear un rol personalizado es tomar como partida un rol integrado, editarlo y, a continuación, crear un nuevo rol.

1. En PowerShell, use el comando [Get-AzProviderOperation](/powershell/module/az.resources/get-azprovideroperation) para obtener la lista de operaciones del proveedor de recursos Microsoft.Support. Resulta útil conocer las operaciones que están disponibles para crear los permisos. También puede ver una lista de todas las operaciones en [Operaciones del proveedor de recursos de Azure](resource-provider-operations.md#microsoftsupport).

    ```azurepowershell
    Get-AzProviderOperation "Microsoft.Support/*" | FT Operation, Description -AutoSize
    ```
    
    ```Output
    Operation                              Description
    ---------                              -----------
    Microsoft.Support/register/action      Registers to Support Resource Provider
    Microsoft.Support/supportTickets/read  Gets Support Ticket details (including status, severity, contact ...
    Microsoft.Support/supportTickets/write Creates or Updates a Support Ticket. You can create a Support Tic...
    ```

1. Use el comando [Get-AzRoleDefinition](/powershell/module/az.resources/get-azroledefinition) para obtener la salida del rol [Lector](built-in-roles.md#reader) en formato JSON.

    ```azurepowershell
    Get-AzRoleDefinition -Name "Reader" | ConvertTo-Json | Out-File C:\CustomRoles\ReaderSupportRole.json
    ```

1. Abra el archivo **ReaderSupportRole.json** en un editor.

    A continuación, se muestra la salida JSON. Para información sobre las distintas propiedades, consulte [Roles personalizados de Azure](custom-roles.md).

    ```json
    {
      "Name": "Reader",
      "Id": "acdd72a7-3385-48ef-bd42-f606fba81ae7",
      "IsCustom": false,
      "Description": "Lets you view everything, but not make any changes.",
      "Actions": [
        "*/read"
      ],
      "NotActions": [],
      "DataActions": [],
      "NotDataActions": [],
      "AssignableScopes": [
        "/"
      ]
    }
    ```
    
1. Edite el archivo JSON para agregar la acción `"Microsoft.Support/*"` a la propiedad `Actions`. Asegúrese de incluir una coma después de la acción de lectura. Esta acción permitirá al usuario crear incidencias de soporte técnico.

1. Obtenga el identificador de la suscripción mediante el comando [Get AzSubscription](/powershell/module/Az.Accounts/Get-AzSubscription).

    ```azurepowershell
    Get-AzSubscription
    ```

1. En `AssignableScopes`, agregue el identificador de suscripción con el formato siguiente: `"/subscriptions/00000000-0000-0000-0000-000000000000"`

    Debe agregar identificadores de suscripción explícitos; en caso contrario, no podrá importar el rol en su suscripción.

1. Elimine la línea de la propiedad `Id` y cambie la propiedad `IsCustom` a `true`.

1. Cambie el valor de las propiedades `Name` y `Description` por "Reader Support Tickets" (Lector de incidencias de soporte) y "Ver todo el contenido de la suscripción y también abrir incidencias de soporte técnico".

    El archivo JSON debe ser similar al siguiente:

    ```json
    {
      "Name": "Reader Support Tickets",
      "IsCustom": true,
      "Description": "View everything in the subscription and also open support tickets.",
      "Actions": [
        "*/read",
        "Microsoft.Support/*"
      ],
      "NotActions": [],
      "DataActions": [],
      "NotDataActions": [],
      "AssignableScopes": [
        "/subscriptions/00000000-0000-0000-0000-000000000000"
      ]
    }
    ```
    
1. Para crear el rol personalizado, use el comando [New-AzRoleDefinition](/powershell/module/az.resources/new-azroledefinition) y especifique el archivo de definición de rol JSON.

    ```azurepowershell
    New-AzRoleDefinition -InputFile "C:\CustomRoles\ReaderSupportRole.json"
    ```

    ```Output
    Name             : Reader Support Tickets
    Id               : 22222222-2222-2222-2222-222222222222
    IsCustom         : True
    Description      : View everything in the subscription and also open support tickets.
    Actions          : {*/read, Microsoft.Support/*}
    NotActions       : {}
    DataActions      : {}
    NotDataActions   : {}
    AssignableScopes : {/subscriptions/00000000-0000-0000-0000-000000000000}
    ```
        
    El nuevo rol personalizado ya está disponible en Azure Portal y se puede asignar a los usuarios, los grupos y las entidades de servicio igual que los roles integrados.

## <a name="list-custom-roles"></a>Lista de roles personalizados

- Para enumerar todos los roles personalizados, use el comando [Get-AzRoleDefinition](/powershell/module/az.resources/get-azroledefinition).

    ```azurepowershell
    Get-AzRoleDefinition | ? {$_.IsCustom -eq $true} | FT Name, IsCustom
    ```

    ```Output
    Name                   IsCustom
    ----                   --------
    Reader Support Tickets     True
    ```
    
    También puede ver el rol personalizado en Azure Portal.

    ![Captura de pantalla de un rol importado en Azure Portal](./media/tutorial-custom-role-powershell/custom-role-reader-support-tickets.png)

## <a name="update-a-custom-role"></a>Actualización de un rol personalizado

Para actualizar el rol personalizado, puede actualizar el archivo JSON o usar el objeto `PSRoleDefinition`.

1. Para actualizar el archivo JSON, use el comando [Get-AzRoleDefinition](/powershell/module/az.resources/get-azroledefinition) para obtener la salida del rol personalizado en formato JSON.

    ```azurepowershell
    Get-AzRoleDefinition -Name "Reader Support Tickets" | ConvertTo-Json | Out-File C:\CustomRoles\ReaderSupportRole2.json
    ```

1. Abra el archivo en un editor.

1. En `Actions`, agregue la acción para crear y administrar implementaciones de grupos de recursos `"Microsoft.Resources/deployments/*"`.

    El archivo JSON actualizado debe ser similar al siguiente:

    ```json
    {
      "Name": "Reader Support Tickets",
      "Id": "22222222-2222-2222-2222-222222222222",
      "IsCustom": true,
      "Description": "View everything in the subscription and also open support tickets.",
      "Actions": [
        "*/read",
        "Microsoft.Support/*",
        "Microsoft.Resources/deployments/*"
      ],
      "NotActions": [],
      "DataActions": [],
      "NotDataActions": [],
      "AssignableScopes": [
        "/subscriptions/00000000-0000-0000-0000-000000000000"
      ]
    }
    ```
        
1. Para actualizar el rol personalizado, use el comando [Set-AzRoleDefinition](/powershell/module/az.resources/set-azroledefinition) y especifique el archivo JSON actualizado.

    ```azurepowershell
    Set-AzRoleDefinition -InputFile "C:\CustomRoles\ReaderSupportRole2.json"
    ```

    ```Output
    Name             : Reader Support Tickets
    Id               : 22222222-2222-2222-2222-222222222222
    IsCustom         : True
    Description      : View everything in the subscription and also open support tickets.
    Actions          : {*/read, Microsoft.Support/*, Microsoft.Resources/deployments/*}
    NotActions       : {}
    DataActions      : {}
    NotDataActions   : {}
    AssignableScopes : {/subscriptions/00000000-0000-0000-0000-000000000000}
    ```

1. Para usar el objeto `PSRoleDefintion` para actualizar el rol personalizado, use primero el comando [Get-AzRoleDefinition](/powershell/module/az.resources/get-azroledefinition) para obtener el rol.

    ```azurepowershell
    $role = Get-AzRoleDefinition "Reader Support Tickets"
    ```
    
1. Llame al método `Add` para agregar la acción de lectura de la configuración de diagnóstico.

    ```azurepowershell
    $role.Actions.Add("Microsoft.Insights/diagnosticSettings/*/read")
    ```

1. Use el comando [Set-AzRoleDefinition](/powershell/module/az.resources/set-azroledefinition) para actualizar el rol.

    ```azurepowershell
    Set-AzRoleDefinition -Role $role
    ```
    
    ```Output
    Name             : Reader Support Tickets
    Id               : 22222222-2222-2222-2222-222222222222
    IsCustom         : True
    Description      : View everything in the subscription and also open support tickets.
    Actions          : {*/read, Microsoft.Support/*, Microsoft.Resources/deployments/*,
                       Microsoft.Insights/diagnosticSettings/*/read}
    NotActions       : {}
    DataActions      : {}
    NotDataActions   : {}
    AssignableScopes : {/subscriptions/00000000-0000-0000-0000-000000000000}
    ```
    
## <a name="delete-a-custom-role"></a>Eliminación de un rol personalizado

1. Use el comando [Get-AzRoleDefinition](/powershell/module/az.resources/get-azroledefinition) para obtener el identificador del rol personalizado.

    ```azurepowershell
    Get-AzRoleDefinition "Reader Support Tickets"
    ```

1. Use el comando [Remove-AzRoleDefinition](/powershell/module/az.resources/remove-azroledefinition) y especifique el identificador del rol para eliminar el rol personalizado.

    ```azurepowershell
    Remove-AzRoleDefinition -Id "22222222-2222-2222-2222-222222222222"
    ```

    ```Output
    Confirm
    Are you sure you want to remove role definition with id '22222222-2222-2222-2222-222222222222'.
    [Y] Yes  [N] No  [S] Suspend  [?] Help (default is "Y"):
    ```

1. Cuando se le pida confirmación, escriba **Y**.

## <a name="next-steps"></a>Pasos siguientes

> [!div class="nextstepaction"]
> [Creación o actualización de roles personalizados de Azure mediante Azure PowerShell](custom-roles-powershell.md)
