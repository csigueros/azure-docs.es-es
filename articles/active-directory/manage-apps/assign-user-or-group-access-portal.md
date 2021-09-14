---
title: Asignación de usuarios y grupos una aplicación empresarial
titleSuffix: Azure AD
description: Aprenda a asignar y anular la asignación de usuarios y grupos para una aplicación mediante el uso de Azure Active Directory para la administración de identidades.
services: active-directory
author: davidmu1
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: how-to
ms.date: 08/27/2021
ms.author: davidmu
ms.reviewer: alamaral
ms.openlocfilehash: bb7def388096d29e7ab1f41a745f9fbc192230ba
ms.sourcegitcommit: add71a1f7dd82303a1eb3b771af53172726f4144
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/03/2021
ms.locfileid: "123431953"
---
# <a name="assign-users-and-groups-to-an-enterprise-application"></a>Asignación de usuarios y grupos una aplicación empresarial

En este artículo se muestra cómo asignar usuarios y grupos a una aplicación empresarial en Azure Active Directory (Azure AD) mediante PowerShell. Cuando se asigna un usuario a una aplicación, esta aparece en el portal Mis aplicaciones del usuario para que pueda acceder a ella con facilidad. Si la aplicación expone roles, también se puede asignar un rol concreto al usuario.

## <a name="prerequisites"></a>Requisitos previos

Para asignar usuarios a una aplicación mediante PowerShell, necesita lo siguiente:

- Una cuenta de Azure con una suscripción activa. [Cree una cuenta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- Uno de los siguientes roles: Administrador global, Administrador de aplicaciones en la nube, Administrador de aplicaciones o Propietario de la entidad de servicio.
- Configure Azure AD PowerShell. Vea [Azure AD PowerShell](https://docs.microsoft.com/powershell/azure/)
- Opcional: Azure Active Directory Premium P1 o P2 para la asignación basada en grupos. Para conocer más requisitos de licencia de las características abordadas en este artículo, vea la [página de precios de Azure Active Directory](https://azure.microsoft.com/pricing/details/active-directory).
- Opcional: Haber finalizado el inicio rápido de [Configuración de una aplicación](add-application-portal-configure.md).

## <a name="assign-users-and-groups-to-an-app-using-powershell"></a>Asignación de usuarios y grupos a una aplicación mediante PowerShell

1. Abra un símbolo del sistema de Windows PowerShell con privilegios elevados.
   > [!NOTE]
   > Debe instalar el módulo de AzureAD (use el comando `Install-Module -Name AzureAD`). Si se le pide que instale un módulo de NuGet o el nuevo módulo de PowerShell para Azure Active Directory V2, escriba S y presione ENTRAR.
2. Ejecute `Connect-AzureAD` e inicie sesión con una cuenta de usuario administrador global.
3. Use el siguiente script para asignar un usuario y un rol a una aplicación:

    ```powershell
    # Assign the values to the variables
    $username = "<Your user's UPN>"
    $app_name = "<Your App's display name>"
    $app_role_name = "<App role display name>"

    # Get the user to assign, and the service principal for the app to assign to
    $user = Get-AzureADUser -ObjectId "$username"
    $sp = Get-AzureADServicePrincipal -Filter "displayName eq '$app_name'"
    $appRole = $sp.AppRoles | Where-Object { $_.DisplayName -eq $app_role_name }

    # Assign the user to the app role
    New-AzureADUserAppRoleAssignment -ObjectId $user.ObjectId -PrincipalId $user.ObjectId -ResourceId $sp.ObjectId -Id $appRole.Id

To assign a group to an enterprise app, you must replace `Get-AzureADUser` with `Get-AzureADGroup` and replace `New-AzureADUserAppRoleAssignment` with `New-AzureADGroupAppRoleAssignment`.

For more information about how to assign a group to an application role, see the documentation for [New-AzureADGroupAppRoleAssignment](/powershell/module/azuread/new-azureadgroupapproleassignment).

### Example

This example assigns the user Britta Simon to the Microsoft Workplace Analytics application using PowerShell.

1. In PowerShell, assign the corresponding values to the variables $username, $app_name and $app_role_name.

    ```powershell
    # Assign the values to the variables
    $username = "britta.simon@contoso.com"
    $app_name = "Workplace Analytics"

2. In this example, we don't know what is the exact name of the application role we want to assign to Britta Simon. Run the following commands to get the user ($user) and the service principal ($sp) using the user UPN and the service principal display names.

    ```powershell
    # Get the user to assign, and the service principal for the app to assign to
    $user = Get-AzureADUser -ObjectId "$username"
    $sp = Get-AzureADServicePrincipal -Filter "displayName eq '$app_name'"

3. Run the command `$sp.AppRoles` to display the roles available for the Workplace Analytics application. In this example, we want to assign Britta Simon the Analyst (Limited access) Role.
   ![Shows the roles available to a user using Workplace Analytics Role](./media/assign-user-or-group-access-portal/workplace-analytics-role.png)
4. Assign the role name to the `$app_role_name` variable.

    ```powershell
    # Assign the values to the variables
    $app_role_name = "Analyst (Limited access)"
    $appRole = $sp.AppRoles | Where-Object { $_.DisplayName -eq $app_role_name }

5. Run the following command to assign the user to the app role:

    ```powershell
    # Assign the user to the app role
    New-AzureADUserAppRoleAssignment -ObjectId $user.ObjectId -PrincipalId $user.ObjectId -ResourceId $sp.ObjectId -Id $appRole.Id
    ```

## <a name="unassign-users-and-groups-from-an-app-using-powershell"></a>Desasignación de usuarios y grupos de una aplicación mediante PowerShell

1. Abra un símbolo del sistema de Windows PowerShell con privilegios elevados.
   > [!NOTE]
   > Debe instalar el módulo de AzureAD (use el comando `Install-Module -Name AzureAD`). Si se le pide que instale un módulo de NuGet o el nuevo módulo de PowerShell para Azure Active Directory V2, escriba S y presione ENTRAR.
2. Ejecute `Connect-AzureAD` e inicie sesión con una cuenta de usuario administrador global.
3. Use el siguiente script para eliminar un usuario y un rol de una aplicación:

    ```powershell
    # Store the proper parameters
    $user = get-azureaduser -ObjectId <objectId>
    $spo = Get-AzureADServicePrincipal -ObjectId <objectId>

    #Get the ID of role assignment 
    $assignments = Get-AzureADServiceAppRoleAssignment -ObjectId $spo.ObjectId | Where {$_.PrincipalDisplayName -eq $user.DisplayName}

    #if you run the following, it will show you what is assigned what
    $assignments | Select *

    #To remove the App role assignment run the following command.
    Remove-AzureADServiceAppRoleAssignment -ObjectId $spo.ObjectId -AppRoleAssignmentId $assignments[assignment #].ObjectId
    ```

## <a name="related-articles"></a>Artículos relacionados

- [Asignación o desasignación de un usuario o grupo mediante Azure Portal](add-application-portal-assign-users.md)
- [Asignación o desasignación de usuarios y grupos para una aplicación mediante Graph API](/graph/api/resources/approleassignment)
- [Administración del acceso a aplicaciones](what-is-access-management.md)

## <a name="next-steps"></a>Pasos siguientes

Más información sobre la administración de la asignación de usuarios y el acceso con los siguientes recursos

- [Métodos para quitar el acceso del usuario para una aplicación](methods-for-removing-user-access.md)
- [Ocultamiento de una aplicación a un usuario](hide-application-from-user-portal.md)
- [Deshabilitación de los inicios de sesión de usuario de una aplicación empresarial](disable-user-sign-in-portal.md)
