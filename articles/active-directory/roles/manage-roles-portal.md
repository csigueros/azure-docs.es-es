---
title: 'Asignación de roles de Azure AD a usuarios: Azure Active Directory'
description: Obtenga información acerca de cómo conceder acceso a los usuarios en Azure Active Directory mediante la asignación de roles de Azure AD.
services: active-directory
author: rolyon
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: roles
ms.topic: how-to
ms.date: 07/15/2021
ms.author: rolyon
ms.reviewer: vincesm
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 311478f52de547a99354a6effad376145166704b
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/13/2021
ms.locfileid: "121724819"
---
# <a name="assign-azure-ad-roles-to-users"></a>Asignación de roles de Azure AD a usuarios

Para conceder acceso a los usuarios en Azure Active Directory (Azure AD), asigne roles de Azure AD. Un rol es una colección de permisos. En este artículo se describe cómo asignar roles de Azure AD mediante Azure Portal y PowerShell.

## <a name="prerequisites"></a>Prerrequisitos

- Administrador global o administrador de roles con privilegios
- Privileged Identity Management requiere una licencia de Azure AD Premium P2
- Módulo de AzureADPreview al usar PowerShell
- Consentimiento del administrador al usar Probador de Graph para Microsoft Graph API

Para más información, consulte [Requisitos previos para usar PowerShell o Probador de Graph](prerequisites.md).

## <a name="azure-portal"></a>Azure portal

Siga estos pasos para asignar roles de Azure AD mediante Azure Portal. Su experiencia será diferente en función de si tiene [Azure AD Privileged Identity Management (PIM)](../privileged-identity-management/pim-configure.md) habilitado.

### <a name="assign-a-role"></a>Asignar un rol

1. Inicie sesión en [Azure Portal](https://portal.azure.com) o en el [centro de administración de Azure AD](https://aad.portal.azure.com).

1. Seleccione **Azure Active Directory** > **Roles y administradores** para ver la lista de roles disponibles.

    ![Página Roles y administradores de Azure Active Directory.](./media/manage-roles-portal/roles-and-administrators.png)

1. Seleccione un rol para ver sus asignaciones.

    Para ayudarle a encontrar el rol que necesita, use **Agregar filtros** para filtrar los roles.

1. Seleccione **Agregar asignaciones** y, a continuación, seleccione los usuarios que desea asignar a este rol.

    Si ve algo diferente de la siguiente imagen, es posible que tenga PIM habilitado. Consulte la sección siguiente.

    ![Panel Agregar asignaciones para el rol seleccionado.](./media/manage-roles-portal/add-assignments.png)

1. Seleccione **Agregar** para asignar el rol.

### <a name="assign-a-role-using-pim"></a>Asignación de un rol mediante PIM

Si tiene [Azure AD Privileged Identity Management (PIM)](../privileged-identity-management/pim-configure.md) habilitado, tiene funcionalidades de asignación de roles adicionales. Por ejemplo, puede hacer que un usuario sea apto para un rol o establecer la duración. Cuando PIM está habilitado, hay dos maneras de asignar roles mediante Azure Portal. Puede usar la página Roles y administradores o la experiencia de PIM. En cualquier caso, se usa el mismo servicio PIM.

Siga estos pasos para asignar roles mediante la página [Roles y administradores](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/RolesAndAdministrators). Si desea asignar roles mediante la página [Privileged Identity Management](https://portal.azure.com/#blade/Microsoft_Azure_PIMCommon/CommonMenuBlade/quickStart), consulte [Asignación de roles de Azure AD en Privileged Identity Management](../privileged-identity-management/pim-how-to-add-role-to-user.md).

1. Inicie sesión en [Azure Portal](https://portal.azure.com) o en el [centro de administración de Azure AD](https://aad.portal.azure.com).

1. Seleccione **Azure Active Directory** > **Roles y administradores** para ver la lista de roles disponibles.

    ![Página Roles y administradores en Azure Active Directory cuando PIM está habilitado.](./media/manage-roles-portal/roles-and-administrators.png)

1. Seleccione un rol para ver sus asignaciones de roles aptas, activas y expiradas.

    Para ayudarle a encontrar el rol que necesita, use **Agregar filtros** para filtrar los roles.

1. Seleccione **Agregar asignaciones**.

1. Elija **No se ha seleccionado ningún miembro** y, a continuación, seleccione los usuarios que desea asignar a este rol.

    ![Página Agregar asignaciones y panel Seleccionar un miembro con PIM habilitado.](./media/manage-roles-portal/add-assignments-pim.png)

1. Seleccione **Siguiente**.

1. En la pestaña **Configuración**, seleccione si el estado de esta asignación de roles debe ser **Apto** o **Activo**.

    Una asignación de roles apta significa que el usuario debe realizar una o varias acciones para usar el rol. Una asignación de roles activa significa que el usuario no tiene que realizar ninguna acción para usar el rol. Para obtener más información sobre lo que significa esta configuración, consulte [Terminología de PIM](../privileged-identity-management/pim-configure.md#terminology).

    ![Página Agregar asignaciones y pestaña Configuración con PIM habilitado.](./media/manage-roles-portal/add-assignments-pim-setting.png)

1. Use las opciones restantes para establecer la duración de la asignación.

1. Seleccione **Asignar** para asignar el rol.

## <a name="powershell"></a>PowerShell

Siga estos pasos para asignar roles Azure AD mediante PowerShell.

### <a name="setup"></a>Instalación

1. Abra una ventana de PowerShell y use [Import-Module](/powershell/module/microsoft.powershell.core/import-module) para importar el módulo AzureADPreview. Para más información, consulte [Requisitos previos para usar PowerShell o Probador de Graph](prerequisites.md).

    ```powershell
    Import-Module -Name AzureADPreview -Force
    ```

1. En una ventana de PowerShell, use [Connect-AzureAD](/powershell/module/azuread/connect-azuread) para iniciar sesión en el inquilino.

    ```powershell
    Connect-AzureAD
    ```

1. Use [Get-AzureADUser](/powershell/module/azuread/get-azureaduser) para obtener el usuario al que desea asignar un rol.

    ```powershell
    $user = Get-AzureADUser -Filter "userPrincipalName eq 'user@contoso.com'"
    ```

### <a name="assign-a-role"></a>Asignar un rol

1. Use [Get-AzureADMSRoleDefinition](/powershell/module/azuread/get-azureadmsroledefinition) para obtener el rol que quiere asignar.

    ```powershell
    $roleDefinition = Get-AzureADMSRoleDefinition -Filter "displayName eq 'Billing Administrator'"
    ```

1. Use [New-AzureADMSRoleAssignment](/powershell/module/azuread/new-azureadmsroleassignment) para asignar el rol.

    ```powershell
    $roleAssignment = New-AzureADMSRoleAssignment -DirectoryScopeId '/' -RoleDefinitionId $roleDefinition.Id -PrincipalId $user.objectId
    ```

### <a name="assign-a-role-as-eligible-using-pim"></a>Asignación de un rol como apto mediante PIM

Si PIM está habilitado, tiene funcionalidades adicionales, como hacer que un usuario sea apto para una asignación de roles o definir las horas de inicio y finalización de una asignación de roles. Estas funcionalidades usan un conjunto diferente de comandos de PowerShell. Para obtener más información sobre el uso de PowerShell y PIM, vea [PowerShell para roles de Azure AD en Privileged Identity Management](../privileged-identity-management/powershell-for-azure-ad-roles.md).


1. Use [Get-AzureADMSRoleDefinition](/powershell/module/azuread/get-azureadmsroledefinition) para obtener el rol que quiere asignar.

    ```powershell
    $roleDefinition = Get-AzureADMSRoleDefinition -Filter "displayName eq 'Billing Administrator'"
    ```

1. Use [Get-AzureADMSPrivilegedResource](/powershell/module/azuread/get-azureadmsprivilegedresource) para obtener el recurso con privilegios. En este caso, es su inquilino.

    ```powershell
    $aadTenant = Get-AzureADMSPrivilegedResource -ProviderId aadRoles
    ```

1. Use [New-Object](/powershell/module/microsoft.powershell.utility/new-object) para crear un objeto `AzureADMSPrivilegedSchedule` para definir las horas de inicio y finalización de la asignación de roles.

    ```powershell
    $schedule = New-Object Microsoft.Open.MSGraph.Model.AzureADMSPrivilegedSchedule
    $schedule.Type = "Once"
    $schedule.StartDateTime = (Get-Date).ToUniversalTime().ToString("yyyy-MM-ddTHH:mm:ss.fffZ")
    $schedule.EndDateTime = "2021-07-25T20:00:00.000Z"
    ```

1. Use [Open-AzureADMSPrivilegedRoleAssignmentRequest](/powershell/module/azuread/open-azureadmsprivilegedroleassignmentrequest) para asignar el rol como apto.

    ```powershell
    $roleAssignmentEligible = Open-AzureADMSPrivilegedRoleAssignmentRequest -ProviderId 'aadRoles' -ResourceId $aadTenant.Id -RoleDefinitionId $roleDefinition.Id -SubjectId $user.objectId -Type 'AdminAdd' -AssignmentState 'Eligible' -schedule $schedule -reason "Review billing info"
    ```

## <a name="microsoft-graph-api"></a>Microsoft Graph API

Siga estas instrucciones para asignar un rol mediante Microsoft Graph API en [Graph Explorer](https://aka.ms/ge).

### <a name="assign-a-role"></a>Asignar un rol

En este ejemplo, se asigna el rol Administrador de facturación (identificador de definición de rol `b0f54661-2d74-4c50-afa3-1ec803f12efe`) a una entidad de seguridad con objectID `f8ca5a85-489a-49a0-b555-0a6d81e56f0d` en el ámbito del inquilino. Si desea ver la lista de los ID de plantilla de rol inmutables de todos los roles integrados, consulte [Roles integrados de Azure AD](permissions-reference.md).

1. Inicie sesión en [Graph Explorer](https://aka.ms/ge).
2. Seleccione **POST** como método HTTP en el menú desplegable. 
3. Seleccione la versión de API para la versión **beta**.
4. Use la API [roleAssignments](/graph/api/rbacapplication-post-roleassignments) para asignar roles. Agregue los detalles siguientes a la dirección URL y al cuerpo de la solicitud y seleccione **Ejecutar consulta**.

```HTTP
POST https://graph.microsoft.com/beta/roleManagement/directory/roleAssignments
Content-type: application/json

{ 
    "@odata.type": "#microsoft.graph.unifiedRoleAssignment",
    "roleDefinitionId": "b0f54661-2d74-4c50-afa3-1ec803f12efe",
    "principalId": "f8ca5a85-489a-49a0-b555-0a6d81e56f0d",
    "directoryScopeId": "/"
}
```

### <a name="assign-a-role-using-pim"></a>Asignación de un rol mediante PIM

En este ejemplo, se asigna a una entidad de seguridad con objectID `f8ca5a85-489a-49a0-b555-0a6d81e56f0d` una asignación de roles elegibles con límite de tiempo para el rol Administrador de facturación (id. de definición de rol `b0f54661-2d74-4c50-afa3-1ec803f12efe`) durante 180 días.

1. Inicie sesión en [Graph Explorer](https://aka.ms/ge).
2. Seleccione **POST** como método HTTP en el menú desplegable. 
3. Seleccione la versión de API para la versión **beta**.
4. Agregue los detalles siguientes a la dirección URL y al cuerpo de la solicitud y seleccione **Ejecutar consulta**.

```HTTP
POST https://graph.microsoft.com/beta/rolemanagement/directory/roleEligibilityScheduleRequests

Content-type: application/json

{
    "action": "AdminAssign",
    "justification": "for managing admin tasks",
    "directoryScopeId": "/",
    "principalId": "f8ca5a85-489a-49a0-b555-0a6d81e56f0d",
    "roleDefinitionId": "b0f54661-2d74-4c50-afa3-1ec803f12efe",
    "scheduleInfo": {
        "startDateTime": "2021-07-15T19:15:08.941Z",
        "expiration": {
            "type": "AfterDuration",
            "duration": "PT180D"
        }
    }
}

```

En el ejemplo siguiente, se asigna a una entidad de seguridad una asignación de roles elegible permanente para el rol Administrador de facturación.

```HTTP
POST https://graph.microsoft.com/beta/rolemanagement/directory/roleEligibilityScheduleRequests

Content-type: application/json

{
    "action": "AdminAssign",
    "justification": "for managing admin tasks",
    "directoryScopeId": "/",
    "principalId": "f8ca5a85-489a-49a0-b555-0a6d81e56f0d",
    "roleDefinitionId": "b0f54661-2d74-4c50-afa3-1ec803f12efe",
    "scheduleInfo": {
        "startDateTime": "2021-07-15T19:15:08.941Z",
        "expiration": {
            "type": "NoExpiration"
        }
    }
}

```

Para activar la asignación de roles, use la API siguiente.

```HTTP
POST https://graph.microsoft.com/beta/roleManagement/directory/roleAssignmentScheduleRequests

Content-type: application/json

{
    "action": "SelfActivate",
    "justification": "activating role assignment for admin privileges",
    "roleDefinitionId": "b0f54661-2d74-4c50-afa3-1ec803f12efe",
    "directoryScopeId": "/",
    "principalId": "f8ca5a85-489a-49a0-b555-0a6d81e56f0d"
}

```

## <a name="next-steps"></a>Pasos siguientes

- [Enumeración de asignaciones de roles de Azure AD](view-assignments.md)
- [Asignación de roles personalizados con ámbito de recurso mediante PowerShell](custom-assign-powershell.md)
- [Roles integrados de Azure AD](permissions-reference.md)
