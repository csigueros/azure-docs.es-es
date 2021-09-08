---
title: Administración de la asignación de usuarios para una aplicación en Azure Active Directory
description: Aprenda a asignar y anular la asignación de usuarios y grupos para una aplicación mediante el uso de Azure Active Directory para la administración de identidades.
services: active-directory
author: davidmu1
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: how-to
ms.date: 02/21/2020
ms.author: davidmu
ms.reviewer: alamaral
ms.openlocfilehash: 91ffbc9e71da6a1837cd77efff3f0af435c437d1
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/13/2021
ms.locfileid: "121738861"
---
# <a name="manage-user-assignment-for-an-app-in-azure-active-directory"></a>Administración de la asignación de usuarios para una aplicación en Azure Active Directory

En este artículo se explica cómo asignar usuarios o grupos a aplicaciones empresariales en Azure Active Directory (Azure AD), ya sea desde Azure Portal o con PowerShell. Cuando un usuario se asigna a una aplicación, dicha aplicación aparece en [Aplicaciones](https://myapps.microsoft.com/) del usuario para que pueda acceder a ella más fácilmente. Si la aplicación expone roles, también se puede asignar un rol concreto al usuario.

De cara a tener un mayor control, se pueden configurar algunos tipos de aplicaciones empresariales para [requerir la asignación de usuarios](#configure-an-application-to-require-user-assignment).

> [!IMPORTANT]
> Cuando se asigna un grupo a una aplicación, solo tendrán acceso los usuarios del grupo. La asignación no se aplica en cascada a los grupos anidados.

> [!NOTE]
> La asignación basada en grupos requiere Azure Active Directory Premium P1 o P2. La asignación basada en grupo se admite en grupos de seguridad únicamente. Actualmente no se admiten las pertenencias a grupos anidados y los grupos de Microsoft 365. Para conocer más requisitos de licencia de las características abordadas en este artículo, vea la [página de precios de Azure Active Directory](https://azure.microsoft.com/pricing/details/active-directory).

## <a name="configure-an-application-to-require-user-assignment"></a>Configuración de una aplicación para que requiera la asignación de usuarios

En los siguientes tipos de aplicaciones existe la opción de requerir que los usuarios estén asignados a la aplicación para poder acceder a ella:

- Aplicaciones configuradas para el inicio de sesión único (SSO) federado con autenticación basada en SAML
- Aplicaciones de proxy de aplicación que usan la autenticación previa de Azure Active Directory
- Aplicaciones integradas en la plataforma de aplicaciones de Azure AD que usan la autenticación de OAuth 2.0 u OpenID Connect después de que un usuario o un administrador han dado su consentimiento en esa aplicación.

Cuando se requiere la asignación de usuarios, solo podrán iniciar sesión los usuarios que se asignen expresamente a la aplicación (ya sea a través de una asignación directa de usuarios o según la pertenencia a grupos). Estos pueden acceder a la aplicación desde su página Mis aplicaciones o a través de un vínculo directo.

Cuando la asignación *no es necesaria*, ya sea porque esta opción se ha establecido en **No** o porque la aplicación usa otro modo de SSO, podrá acceder a la aplicación cualquier usuario que tenga un vínculo directo a la aplicación o la **dirección URL de acceso de usuario** en la página **Propiedades** de la aplicación.

Esto no afecta a si una aplicación aparece o no en Aplicaciones. Las aplicaciones aparecen en los paneles de acceso Mis aplicaciones de los usuarios en el momento en que un usuario o un grupo se asigna a la aplicación en cuestión. Para obtener información general, vea [Administración del acceso a las aplicaciones](what-is-access-management.md).

> [!NOTE]
> Cuando en una aplicación se necesita la asignación, no se permite el consentimiento del usuario para esa aplicación. Esto es así incluso si los usuarios hubieran dado su consentimiento para esa aplicación. Asegúrese de conceder [consentimiento de administrador en todo el inquilino](../manage-apps/grant-admin-consent.md) a las aplicaciones en las que sea necesaria la asignación.

Para requerir la asignación de usuarios en una aplicación:

1. Inicie sesión en [Azure Portal](https://portal.azure.com) con una cuenta de administrador o como propietario de la aplicación.
2. Seleccione **Azure Active Directory**. En el menú de navegación de la izquierda, seleccione **Aplicaciones empresariales**.
3. Seleccione la aplicación de la lista. Si no la ve, empiece a escribir su nombre en el cuadro de búsqueda. También puede usar controles de filtro para seleccionar el tipo de aplicación, el estado o la visibilidad. Tras ello, seleccione **Aplicar**.
4. En el menú de navegación de la izquierda, seleccione **Propiedades**.
5. Asegúrese de que el botón de alternancia **¿Asignación de usuarios?** está establecido en **Sí**.
   > [!NOTE]
   > Si **¿Asignación de usuarios?** no está disponible, puede usar PowerShell para establecer la propiedad appRoleAssignmentRequired en la entidad de servicio.
6. Seleccione el botón **Guardar** de la parte superior de la pantalla.

## <a name="assign-or-unassign-users-and-groups-for-an-app-using-the-azure-portal"></a>Asignación o desasignación de usuarios y grupos para una aplicación mediante Azure Portal

Para obtener información sobre cómo asignar o desasignar un usuario o grupo mediante Azure Portal, consulte la [serie de inicios rápidos de Administración de aplicaciones](add-application-portal-assign-users.md).

## <a name="assign-or-unassign-users-and-groups-for-an-app-using-the-graph-api"></a>Asignación o desasignación de usuarios y grupos para una aplicación mediante Graph API

Puede usar Graph API para asignar o desasignar usuarios y grupos para una aplicación. Para más información, consulte [Asignaciones de roles de aplicación](/graph/api/resources/approleassignment).

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

For more information about how to assign a user to an application role, see the documentation for [New-AzureADUserAppRoleAssignment](/powershell/module/azuread/new-azureaduserapproleassignment).

To assign a group to an enterprise app, you must replace `Get-AzureADUser` with `Get-AzureADGroup` and replace `New-AzureADUserAppRoleAssignment` with `New-AzureADGroupAppRoleAssignment`.

For more information about how to assign a group to an application role, see the documentation for [New-AzureADGroupAppRoleAssignment](/powershell/module/azuread/new-azureadgroupapproleassignment).

### Example

This example assigns the user Britta Simon to the [Microsoft Workplace Analytics](https://products.office.com/business/workplace-analytics) application using PowerShell.

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

- [Más información sobre el acceso de los usuarios finales a las aplicaciones](end-user-experiences.md)
- [Planeamiento de la implementación de Aplicaciones de Azure AD](my-apps-deployment-plan.md)
- [Administración del acceso a las aplicaciones](what-is-access-management.md)

## <a name="next-steps"></a>Pasos siguientes

- [Ver todos mis grupos](../fundamentals/active-directory-groups-view-azure-portal.md)
- [Quitar una asignación de usuario o grupo de una aplicación empresarial]()
- [Deshabilitar los inicios de sesión de los usuarios de una aplicación empresarial](disable-user-sign-in-portal.md)
- [Cambio del nombre o el logotipo de una aplicación empresarial](./add-application-portal-configure.md)
