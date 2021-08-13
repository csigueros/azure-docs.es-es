---
title: Creación de roles personalizados en el control de acceso basado en rol de Azure AD | Microsoft Docs
description: Cree y asigne roles personalizados de Azure AD con el ámbito de recurso en recursos de Azure Active Directory.
services: active-directory
author: rolyon
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: roles
ms.topic: how-to
ms.date: 05/14/2021
ms.author: rolyon
ms.reviewer: vincesm
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: bef0dc016b2b216d51a4844c469d14a24e11068b
ms.sourcegitcommit: 070122ad3aba7c602bf004fbcf1c70419b48f29e
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 06/04/2021
ms.locfileid: "111437773"
---
# <a name="create-and-assign-a-custom-role-in-azure-active-directory"></a>Creación y asignación de un rol personalizado en Azure Active Directory

En este artículo se describe cómo crear roles personalizados en Azure Active Directory (Azure AD). Para conocer los aspectos básicos de los roles personalizados, consulte la [información general sobre roles personalizados](custom-overview.md). El rol se puede asignar en el ámbito de nivel de directorio o en un ámbito de recurso del registro de aplicación únicamente.

Los roles personalizados se pueden crear en la pestaña [Roles y administradores](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/RolesAndAdministrators) en la página de información general sobre Azure AD.

## <a name="prerequisites"></a>Prerrequisitos

- Una licencia de Azure AD Premium P1 o P2
- Administrador global o administrador de roles con privilegios
- Módulo de AzureADPreview al usar PowerShell
- Consentimiento del administrador al usar Probador de Graph para Microsoft Graph API

Para más información, consulte [Requisitos previos para usar PowerShell o Probador de Graph](prerequisites.md).

## <a name="create-a-role-in-the-azure-portal"></a>Creación de un rol en Azure Portal

### <a name="create-a-new-custom-role-to-grant-access-to-manage-app-registrations"></a>Creación de un rol personalizado para conceder acceso para administrar los registros de aplicaciones

1. Inicie sesión en el [Centro de administración de Azure AD](https://aad.portal.azure.com).
1. Seleccione **Azure Active Directory** > **Roles y administradores** > **Nuevo rol personalizado**.

   ![Creación o edición de roles en la página Roles y administradores](./media/custom-create/new-custom-role.png)

1. En la pestaña **Aspectos básicos**, proporcione un nombre y una descripción para el rol y, luego, haga clic en **Siguiente**.

   ![Proporcione un nombre y una descripción para un rol personalizado en la pestaña Aspectos básicos](./media/custom-create/basics-tab.png)

1. En la pestaña **Permisos**, seleccione los permisos necesarios para administrar las propiedades básicas y las propiedades de credenciales de los registros de aplicaciones. Para una descripción detallada de cada permiso, consulte [Permisos y subtipos del registro de aplicaciones en Azure Active Directory](custom-available-permissions.md).
   1. En primer lugar, escriba "credenciales" en la barra de búsqueda y seleccione el permiso `microsoft.directory/applications/credentials/update`.

      ![Selección de los permisos de un rol personalizado en la pestaña Permisos](./media/custom-create/permissions-tab.png)

   1. A continuación, escriba "básico" en la barra de búsqueda, seleccione el permiso `microsoft.directory/applications/basic/update` y, luego, haga clic en **Siguiente**.
1. En la pestaña **Revisar y crear**, revise los detalles y seleccione **Crear**.

El rol personalizado se mostrará en la lista de los roles disponibles para asignar.

## <a name="create-a-role-using-powershell"></a>Creación de un rol con PowerShell

### <a name="connect-to-azure"></a>Conexión con Azure

Para la conexión con Azure Active Directory, use el siguiente comando:

``` PowerShell
Connect-AzureAD
```

### <a name="create-the-custom-role"></a>Creación del rol personalizado

Cree un nuevo rol mediante el siguiente script de PowerShell:

``` PowerShell
# Basic role information
$displayName = "Application Support Administrator"
$description = "Can manage basic aspects of application registrations."
$templateId = (New-Guid).Guid
 
# Set of permissions to grant
$allowedResourceAction =
@(
    "microsoft.directory/applications/basic/update",
    "microsoft.directory/applications/credentials/update"
)
$rolePermissions = @{'allowedResourceActions'= $allowedResourceAction}
 
# Create new custom admin role
$customAdmin = New-AzureADMSRoleDefinition -RolePermissions $rolePermissions -DisplayName $displayName -Description $description -TemplateId $templateId -IsEnabled $true
```

### <a name="assign-the-custom-role-using-powershell"></a>Asignación del rol personalizado con PowerShell

Asigne el rol mediante el siguiente script de PowerShell:

``` PowerShell
# Get the user and role definition you want to link
$user = Get-AzureADUser -Filter "userPrincipalName eq 'cburl@f128.info'"
$roleDefinition = Get-AzureADMSRoleDefinition -Filter "displayName eq 'Application Support Administrator'"

# Get app registration and construct resource scope for assignment.
$appRegistration = Get-AzureADApplication -Filter "displayName eq 'f/128 Filter Photos'"
$resourceScope = '/' + $appRegistration.objectId

# Create a scoped role assignment
$roleAssignment = New-AzureADMSRoleAssignment -ResourceScope $resourceScope -RoleDefinitionId $roleDefinition.Id -PrincipalId $user.objectId
```

## <a name="create-a-role-with-the-microsoft-graph-api"></a>Creación de un rol con Microsoft Graph API

1. Cree la definición de roles.

    Solicitud HTTP para crear una definición de roles personalizados.

    POST

    ``` HTTP
    https://graph.microsoft.com/beta/roleManagement/directory/roleDefinitions
    ```

    Body

    ``` HTTP
    {
       "description": "Can manage basic aspects of application registrations.",
       "displayName": "Application Support Administrator",
       "isEnabled": true,
       "templateId": "<GUID>",
       "rolePermissions": [
           {
               "allowedResourceActions": [
                   "microsoft.directory/applications/basic/update",
                   "microsoft.directory/applications/credentials/update"
               ]
           }
       ]
    }
    ```

    > [!Note]
    > El valor `"templateId": "GUID"` es un parámetro opcional que se envía al cuerpo según el requisito. Si tiene un requisito para crear varios roles personalizados con parámetros comunes, es mejor crear una plantilla y definir un valor `templateId`. Puede generar un valor `templateId` de antemano mediante el cmdlet de PowerShell `(New-Guid).Guid`. 

1. Cree la asignación de roles.

    Solicitud HTTP para crear una definición de roles personalizados.

    POST

    ``` HTTP
    https://graph.microsoft.com/beta/roleManagement/directory/roleAssignments
    ```

    Body

    ``` HTTP
   {
       "principalId":"<GUID OF USER>",
       "roleDefinitionId":"<GUID OF ROLE DEFINITION>",
       "resourceScope":"/<GUID OF APPLICATION REGISTRATION>"
   }
    ```

## <a name="assign-a-custom-role-scoped-to-a-resource"></a>Asignación de un rol personalizado con ámbito de un recurso

Al igual que los roles integrados, los roles personalizados se asignan de forma predeterminada en el ámbito predeterminado de toda la organización para conceder permisos de acceso a todos los registros de aplicaciones de la organización. Además, los roles personalizados y algunos roles integrados pertinentes (según el tipo de recurso de Azure AD) también se pueden asignar en el ámbito de un único recurso de Azure AD. Esto le permite entregar al usuario el permiso para actualizar las credenciales y las propiedades básicas de una aplicación única sin la necesidad de crear un segundo rol personalizado.

1. Inicie sesión en el [Centro de administración de Azure AD](https://aad.portal.azure.com) con los permisos Desarrollador de aplicaciones.
1. Seleccione **App registrations** (Registros de aplicaciones).
1. Seleccione el registro de aplicaciones al que se concede acceso para administrar. Es posible que tenga que seleccionar **Todas las aplicaciones** para ver la lista completa de los registros de aplicaciones de la organización de Azure AD.

    ![Selección del registro de aplicaciones como un ámbito de recurso para una asignación de roles](./media/custom-create/appreg-all-apps.png)

1. En el registro de aplicaciones, seleccione **Roles y administradores**. Si todavía no crea uno, puede encontrar instrucciones sobre cómo hacerlo en el [procedimiento anterior](#create-a-new-custom-role-to-grant-access-to-manage-app-registrations).

1. Seleccione el rol para abrir la página **Asignaciones**.
1. Seleccione **Agregar asignación** para agregar un usuario. Al usuario se le concederán permisos únicamente sobre el registro de aplicaciones seleccionado.

## <a name="next-steps"></a>Pasos siguientes

- No dude en compartir con nosotros en el [foro de roles administrativos de Azure AD](https://feedback.azure.com/forums/169401-azure-active-directory?category_id=166032).
- Para más información acerca de los permisos de roles, consulte [Roles integrados en Azure AD](permissions-reference.md).
- Para conocer los permisos predeterminados de usuario, vea una [comparación de los permisos predeterminados de usuario miembro e invitado](../fundamentals/users-default-permissions.md?context=azure%2factive-directory%2froles%2fcontext%2fugr-context).
