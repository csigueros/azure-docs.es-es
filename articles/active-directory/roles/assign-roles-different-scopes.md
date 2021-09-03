---
title: 'Asignación de roles de Azure AD en distintos ámbitos: Azure Active Directory'
description: Obtenga información sobre cómo asignar roles en distintos ámbitos en Azure Active Directory.
services: active-directory
author: abhijeetsinha
manager: vincesm
ms.service: active-directory
ms.workload: identity
ms.subservice: roles
ms.topic: how-to
ms.date: 08/12/2021
ms.author: absinh
ms.reviewer: rolyon
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 02c9e92d09ce929b47cd9a71559eabb4e7c40858
ms.sourcegitcommit: 6c6b8ba688a7cc699b68615c92adb550fbd0610f
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/13/2021
ms.locfileid: "121862817"
---
# <a name="assign-azure-ad-roles-at-different-scopes"></a>Asignación de roles de Azure AD en distintos ámbitos

En este artículo se describe cómo asignar roles de Azure AD en distintos ámbitos. Para comprender los ámbitos en Azure AD, consulte este documento: [Introducción al control de acceso basado en rol en Azure Active Directory](custom-overview.md). En general, debe estar dentro del ámbito al que quiera que se limite la asignación de roles. Por ejemplo, si quiere asignar el rol Administrador del departamento de soporte técnico en el ámbito de una [unidad administrativa](administrative-units.md), deberá ir a **Azure AD > Unidades administrativas > {unidad administrativa} > Roles y administradores** y, luego, realizar la asignación de roles. Esto creará una asignación de roles con ámbito en la unidad administrativa, no en todo el inquilino.

## <a name="prerequisites"></a>Requisitos previos

- Administrador global o administrador de roles con privilegios
- Módulo AzureADPreview al usar PowerShell
- Consentimiento del administrador al usar Probador de Graph para la API de Microsoft Graph

Para más información, consulte [Requisitos previos para usar PowerShell o Probador de Graph](prerequisites.md).

## <a name="assign-roles-scoped-to-the-tenant"></a>Asignación de roles con ámbito en el inquilino

En esta sección se describe cómo asignar roles en el ámbito del inquilino.

### <a name="azure-portal"></a>Azure portal

1. Inicie sesión en [Azure Portal](https://portal.azure.com) o en el [centro de administración de Azure AD](https://aad.portal.azure.com).

1. Seleccione **Azure Active Directory** > **Roles y administradores** para ver la lista de roles disponibles.

    ![Página Roles y administradores de Azure Active Directory](./media/manage-roles-portal/roles-and-administrators.png)

1. Seleccione un rol para ver sus asignaciones. Para poder encontrar el rol que necesita, use **Agregar filtros** para filtrar los roles.

1. Seleccione **Agregar asignaciones** y, a continuación, seleccione los usuarios que desea asignar a este rol.

    ![Panel Agregar asignaciones para el rol seleccionado](./media/manage-roles-portal/add-assignments.png)

1. Seleccione **Agregar** para asignar el rol.

### <a name="powershell"></a>PowerShell

Siga estos pasos para asignar roles de Azure AD mediante PowerShell.

1. Abra una ventana de PowerShell y use [Import-Module](/powershell/module/microsoft.powershell.core/import-module) para importar el módulo AzureADPreview. Para más información, consulte [Requisitos previos para usar PowerShell o Probador de Graph](prerequisites.md).

    ```powershell
    Import-Module -Name AzureADPreview -Force
    ```

1. En una ventana de PowerShell, use [Connect-AzureAD](/powershell/module/azuread/connect-azuread) para iniciar sesión en el inquilino.

    ```powershell
    Connect-AzureAD
    ```

1. Use [Get-AzureADUser](/powershell/module/azuread/get-azureaduser) para obtener el usuario.

    ```powershell
    $user = Get-AzureADUser -Filter "userPrincipalName eq 'alice@contoso.com'"
    ```

1. Use [Get-AzureADMSRoleDefinition](/powershell/module/azuread/get-azureadmsroledefinition) para obtener el rol que quiera asignar.

    ```powershell
    $roleDefinition = Get-AzureADMSRoleDefinition -Filter "displayName eq 'Billing Administrator'"
    ```

1. Establezca el inquilino como ámbito de la asignación de roles.

    ```powershell
    $directoryScope = '/'
    ```

1. Use [New-AzureADMSRoleAssignment](/powershell/module/azuread/new-azureadmsroleassignment) para asignar el rol.

    ```powershell
    $roleAssignment = New-AzureADMSRoleAssignment -DirectoryScopeId $directoryScope -RoleDefinitionId $roleDefinition.Id -PrincipalId $user.objectId
    ```
 
### <a name="microsoft-graph-api"></a>Microsoft Graph API

Siga estas instrucciones para asignar un rol mediante la API de Microsoft Graph en [Probador de Graph](https://aka.ms/ge).

1. Inicie sesión en [Graph Explorer](https://aka.ms/ge).

1. Use la API [Mostrar usuario](/graph/api/user-list) para obtener el usuario.

    ```HTTP
    GET https://graph.microsoft.com/beta/users?$filter=userPrincipalName eq 'alice@contoso.com'
    ```
    
1. Use la API [Mostrar roleDefinitions](/graph/api/rbacapplication-list-roledefinitions) para obtener el rol que quiera asignar.

    ```HTTP
    GET https://graph.microsoft.com/beta/rolemanagement/directory/roleDefinitions?$filter=displayName eq 'Billing Administrator'
    ```
    
1. Use la API [Crear roleAssignments](/graph/api/rbacapplication-post-roleassignments) para asignar el rol.

    ```HTTP
    POST https://graph.microsoft.com/beta/roleManagement/directory/roleAssignments
    {
        "principalId": "<provide objectId of the user obtained above>",
        "roleDefinitionId": "<provide templateId of the role obtained above>",
        "directoryScopeId": "/"
    }
    ```

## <a name="assign-roles-scoped-to-an-administrative-unit"></a>Asignación de roles con ámbito en una unidad administrativa

### <a name="azure-portal"></a>Azure portal

1. Inicie sesión en [Azure Portal](https://portal.azure.com) o en el [centro de administración de Azure AD](https://aad.portal.azure.com).

1. Seleccione **Azure Active Directory > Unidades administrativas** para ver la lista de todas las unidades administrativas.

1. Seleccione una unidad administrativa.

    ![Unidades administrativas en Azure Active Directory](./media/assign-roles-different-scopes/admin-units.png)

1. Seleccione **Roles y administradores** en el menú de navegación izquierdo para ver la lista de todos los roles disponibles para asignarse mediante una unidad administrativa.

    ![Menú Roles y administradores en Unidades administrativas en Azure Active Directory](./media/assign-roles-different-scopes/admin-units-roles.png)

1. Seleccione el rol que quiera.

1. Seleccione **Agregar asignaciones** y, después, seleccione los usuarios o el grupo a los que quiera asignar a este rol.

1. Seleccione **Agregar** para asignar el rol con ámbito en la unidad administrativa.

>[!Note] 
>Aquí no verá la lista completa de roles integrados o personalizados de Azure AD. Se espera que esto sea así. Se muestran los roles que tienen permisos relacionados con los objetos que se admiten en la unidad administrativa. Consulte [esta documentación](administrative-units.md) para ver la lista de objetos que se admiten en una unidad administrativa.

### <a name="powershell"></a>PowerShell

Siga estos pasos para asignar roles de Azure AD en el ámbito de la unidad administrativa mediante PowerShell.

1. Abra una ventana de PowerShell y use [Import-Module](/powershell/module/microsoft.powershell.core/import-module) para importar el módulo AzureADPreview. Para más información, consulte [Requisitos previos para usar PowerShell o Probador de Graph](prerequisites.md).

    ```powershell
    Import-Module -Name AzureADPreview -Force
    ```

1. En una ventana de PowerShell, use [Connect-AzureAD](/powershell/module/azuread/connect-azuread) para iniciar sesión en el inquilino.

    ```powershell
    Connect-AzureAD
    ```

1. Use [Get-AzureADUser](/powershell/module/azuread/get-azureaduser) para obtener el usuario.

    ```powershell
    $user = Get-AzureADUser -Filter "userPrincipalName eq 'alice@contoso.com'"
    ```

1. Use [Get-AzureADMSRoleDefinition](/powershell/module/azuread/get-azureadmsroledefinition) para obtener el rol que quiera asignar.

    ```powershell
    $roleDefinition = Get-AzureADMSRoleDefinition -Filter "displayName eq 'User Administrator'"
    ```

1. Use [Get-AzureADMSAdministrativeUnit](/powershell/module/azuread/get-azureadmsadministrativeunit) para obtener la unidad administrativa en la que quiera que se defina el ámbito de la asignación de roles.

    ```powershell
    $adminUnit = Get-AzureADMSAdministrativeUnit -Filter "displayName eq 'Seattle Admin Unit'"
    $directoryScope = '/administrativeUnits/' + $adminUnit.Id
    ```

1. Use [New-AzureADMSRoleAssignment](/powershell/module/azuread/new-azureadmsroleassignment) para asignar el rol.

    ```powershell
    $roleAssignment = New-AzureADMSRoleAssignment -DirectoryScopeId $directoryScope -RoleDefinitionId $roleDefinition.Id -PrincipalId $user.objectId
    ```

### <a name="microsoft-graph-api"></a>Microsoft Graph API

Siga estas instrucciones para asignar un rol en el ámbito de la unidad administrativa mediante la API de Microsoft Graph en [Probador de Graph](https://aka.ms/ge).

1. Inicie sesión en [Graph Explorer](https://aka.ms/ge).

1. Use la API [Mostrar usuario](/graph/api/user-list) para obtener el usuario.

    ```HTTP
    GET https://graph.microsoft.com/beta/users?$filter=userPrincipalName eq 'alice@contoso.com'
    ```
    
1. Use la API [Mostrar roleDefinitions](/graph/api/rbacapplication-list-roledefinitions) para obtener el rol que quiera asignar.

    ```HTTP
    GET https://graph.microsoft.com/beta/rolemanagement/directory/roleDefinitions?$filter=displayName eq 'User Administrator'
    ```
    
1. Use la API [Mostrar administrativeUnits](/graph/api/administrativeunit-list) para obtener la unidad administrativa en la que quiera que se defina el ámbito de la asignación de roles.

    ```HTTP
    GET https://graph.microsoft.com/beta/administrativeUnits?$filter=displayName eq 'Seattle Admin Unit'
    ```

1. Use la API [Crear roleAssignments](/graph/api/rbacapplication-post-roleassignments) para asignar el rol.

    ```HTTP
    POST https://graph.microsoft.com/beta/roleManagement/directory/roleAssignments
    {
        "principalId": "<provide objectId of the user obtained above>",
        "roleDefinitionId": "<provide templateId of the role obtained above>",
        "directoryScopeId": "/administrativeUnits/<provide objectId of the admin unit obtained above>"
    }
    ```
    
>[!Note] 
>Aquí, directoryScopeId se especifica como */administrativeUnits/foo* en lugar de */foo*. Esto es así de forma predeterminada. El ámbito */administrativeUnits/foo\* significa que la entidad de seguridad puede administrar los miembros de la unidad administrativa (en función del rol asignado), no la propia unidad administrativa. El ámbito de */foo* significa que la entidad de seguridad puede administrar ese mismo objeto de Azure AD. En la sección siguiente, verá que el ámbito es */foo*, ya que un rol con ámbito en un registro de aplicación concede el privilegio para administrar el propio objeto.

## <a name="assign-roles-scoped-to-an-app-registration"></a>Asignación de roles con ámbito en un registro de aplicación

### <a name="azure-portal"></a>Azure portal

1. Inicie sesión en [Azure Portal](https://portal.azure.com) o en el [centro de administración de Azure AD](https://aad.portal.azure.com).

1. Seleccione **Azure Active Directory > Registros de aplicaciones** para ver una lista de todos los registros de aplicaciones.

1. Seleccione una aplicación. Puede utilizar el cuadro de búsqueda para encontrar la aplicación que quiera.

    ![Registros de aplicaciones en Azure Active Directory](./media/assign-roles-different-scopes/app-reg.png)

1. Seleccione **Roles y administradores** en el menú de navegación izquierdo para ver la lista de todos los roles disponibles para asignarse mediante el registro de aplicaciones.

    ![Roles para los registros de una aplicación en Azure Active Directory](./media/assign-roles-different-scopes/app-reg-roles.png)

1. Seleccione el rol que quiera.

1. Seleccione **Agregar asignaciones** y, después, seleccione los usuarios o el grupo a los que quiera asignar a este rol.

    ![Agregue la asignación de roles con ámbito en los registros de una aplicación en Azure Active Directory.](./media/assign-roles-different-scopes/app-reg-add-assignment.png)

1. Seleccione **Agregar** para asignar el rol con ámbito en el registro de aplicaciones. 

    ![Se ha agregado correctamente la asignación de roles con ámbito en los registros de una aplicación en Azure Active Directory.](./media/assign-roles-different-scopes/app-reg-assignment.png)
    
    ![Rol asignado al usuario con ámbito en los registros de una aplicación en Azure Active Directory](./media/assign-roles-different-scopes/app-reg-scoped-assignment.png)
    

>[!Note] 
>Aquí no verá la lista completa de roles integrados o personalizados de Azure AD. Se espera que esto sea así. Se muestran los roles que tienen permisos relacionados con la administración solo de registros de aplicaciones. 


### <a name="powershell"></a>PowerShell

Siga estos pasos para asignar roles de Azure AD en el ámbito de la unidad administrativa mediante PowerShell.

1. Abra una ventana de PowerShell y use [Import-Module](/powershell/module/microsoft.powershell.core/import-module) para importar el módulo AzureADPreview. Para más información, consulte [Requisitos previos para usar PowerShell o Probador de Graph](prerequisites.md).

    ```powershell
    Import-Module -Name AzureADPreview -Force
    ```

1. En una ventana de PowerShell, use [Connect-AzureAD](/powershell/module/azuread/connect-azuread) para iniciar sesión en el inquilino.

    ```powershell
    Connect-AzureAD
    ```

1. Use [Get-AzureADUser](/powershell/module/azuread/get-azureaduser) para obtener el usuario.

    ```powershell
    $user = Get-AzureADUser -Filter "userPrincipalName eq 'alice@contoso.com'"
    ```

1. Use [Get-AzureADMSRoleDefinition](/powershell/module/azuread/get-azureadmsroledefinition) para obtener el rol que quiera asignar.

    ```powershell
    $roleDefinition = Get-AzureADMSRoleDefinition -Filter "displayName eq 'Application Administrator'"
    ```

1. Use [Get-AzureADApplication](/powershell/module/azuread/get-azureadapplication) para obtener el registro de aplicaciones en el que quiera que se defina el ámbito de la asignación de roles.

    ```powershell
    $appRegistration = Get-AzureADApplication -Filter "displayName eq 'f/128 Filter Photos'"
    $directoryScope = '/' + $appRegistration.objectId
    ```

1. Use [New-AzureADMSRoleAssignment](/powershell/module/azuread/new-azureadmsroleassignment) para asignar el rol.

    ```powershell
    $roleAssignment = New-AzureADMSRoleAssignment -DirectoryScopeId $directoryScope -RoleDefinitionId $roleDefinition.Id -PrincipalId $user.objectId
    ```

### <a name="microsoft-graph-api"></a>Microsoft Graph API

Siga estas instrucciones para asignar un rol en el ámbito de una aplicación mediante la API de Microsoft Graph en [Probador de Graph](https://aka.ms/ge).

1. Inicie sesión en [Graph Explorer](https://aka.ms/ge).

1. Use la API [Mostrar usuario](/graph/api/user-list) para obtener el usuario.

    ```HTTP
    GET https://graph.microsoft.com/beta/users?$filter=userPrincipalName eq 'alice@contoso.com'
    ```
    
1. Use la API [Mostrar roleDefinitions](/graph/api/rbacapplication-list-roledefinitions) para obtener el rol que quiera asignar.

    ```HTTP
    GET https://graph.microsoft.com/beta/rolemanagement/directory/roleDefinitions?$filter=displayName eq 'Application Administrator'
    ```
    
1. Use la API [Mostrar aplicaciones](/graph/api/application-list) para obtener la unidad administrativa en la que quiera que se defina el ámbito de la asignación de roles.

    ```HTTP
    GET https://graph.microsoft.com/beta/applications?$filter=displayName eq 'f/128 Filter Photos'
    ```

1. Use la API [Crear roleAssignments](/graph/api/rbacapplication-post-roleassignments) para asignar el rol.

    ```HTTP
    POST https://graph.microsoft.com/beta/roleManagement/directory/roleAssignments
    {
        "principalId": "<provide objectId of the user obtained above>",
        "roleDefinitionId": "<provide templateId of the role obtained above>",
        "directoryScopeId": "/<provide objectId of the app registration obtained above>"
    }
    ```

>[!Note] 
>Aquí, directoryScopeId se especifica como */foo*, a diferencia de la sección anterior. Esto es así de forma predeterminada. El ámbito de */foo* significa que la entidad de seguridad puede administrar ese objeto de Azure AD. El ámbito */administrativeUnits/foo\* significa que la entidad de seguridad puede administrar los miembros de la unidad administrativa (en función del rol asignado), no la propia unidad administrativa.


## <a name="next-steps"></a>Pasos siguientes

* [Enumeración de asignaciones de roles de Azure AD](view-assignments.md)
* [Asignación de roles de Azure AD a usuarios](manage-roles-portal.md)
* [Asignación de roles de Azure AD a grupos](groups-assign-role.md)