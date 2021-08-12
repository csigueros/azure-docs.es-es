---
title: 'Asignación de roles personalizados con Azure AD PowerShell: Azure AD | Microsoft Docs'
description: Administre los miembros de un rol personalizado de administrador de Azure AD con Azure AD PowerShell.
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
ms.openlocfilehash: 60517714e0aa19a2cf465c22c6511b0c89648942
ms.sourcegitcommit: 7f59e3b79a12395d37d569c250285a15df7a1077
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 06/02/2021
ms.locfileid: "110792449"
---
# <a name="assign-custom-roles-with-resource-scope-using-powershell-in-azure-active-directory"></a>Asignación de roles personalizados con ámbito de recurso mediante PowerShell en Azure Active Directory

En este artículo se describe cómo crear una asignación de roles en un ámbito de toda la organización en Azure Active Directory (Azure AD). La asignación de un rol en un ámbito de toda la organización concede acceso a toda la organización de Azure AD. Para crear una asignación de roles con un ámbito de un solo recurso de Azure AD, consulte [Cómo crear un rol personalizado y asignarlo en un ámbito de recurso](custom-create.md). Este artículo se usa el módulo [Azure Active Directory PowerShell, versión 2](/powershell/module/azuread/#directory_roles).

Para más información sobre los roles de Azure AD, consulte [Roles integrados en Azure AD](permissions-reference.md).

## <a name="prerequisites"></a>Requisitos previos

- Una licencia de Azure AD Premium P1 o P2
- Administrador global o administrador de roles con privilegios
- Módulo de AzureADPreview al usar PowerShell

Para obtener más información, consulte [Requisitos previos para usar PowerShell o Probador de Graph](prerequisites.md).

## <a name="assign-a-directory-role-to-a-user-or-service-principal-with-resource-scope"></a>Asignación de un rol de directorio a un usuario o entidad de servicio con ámbito de recurso

1. Cargue el módulo de Azure AD PowerShell (versión preliminar).
1. Para iniciar sesión, ejecute el comando `Connect-AzureAD`.
1. Cree un nuevo rol mediante el siguiente script de PowerShell.

``` PowerShell
## Assign a role to a user or service principal with resource scope
# Get the user and role definition you want to link
$user = Get-AzureADUser -Filter "userPrincipalName eq 'cburl@f128.info'"
$roleDefinition = Get-AzureADMSRoleDefinition -Filter "displayName eq 'Application Support Administrator'"

# Get app registration and construct resource scope for assignment.
$appRegistration = Get-AzureADApplication -Filter "displayName eq 'f/128 Filter Photos'"
$resourceScope = '/' + $appRegistration.objectId

# Create a scoped role assignment
$roleAssignment = New-AzureADMSRoleAssignment -ResourceScope $resourceScope -RoleDefinitionId $roleDefinition.Id -PrincipalId $user.objectId
```

Para asignar el rol a una entidad de servicio en lugar de a un usuario, use el [cmdlet Get-AzureADMSServicePrincipal](/powershell/module/azuread/get-azureadserviceprincipal).

## <a name="role-definitions"></a>Definiciones de roles

Los objetos de definición de roles contienen la definición del rol integrado o personalizado, junto con los permisos que dicha asignación de roles concede. Este recurso muestra tanto las definiciones de roles personalizados como los roles de directorio integrados (que se muestran en el formulario equivalente de roleDefinition). En la actualidad, una organización de Azure AD puede tener definidas un máximo de 30 definiciones de roles personalizados únicos.

### <a name="create-a-role-definition"></a>Creación de una definición de roles

``` PowerShell
# Basic information
$description = "Can manage credentials of application registrations"
$displayName = "Application Registration Credential Administrator"
$templateId = (New-Guid).Guid

# Set of actions to include
$rolePermissions = @{
    "allowedResourceActions" = @(
        "microsoft.directory/applications/standard/read",
        "microsoft.directory/applications/credentials/update"
    )
}

# Create new custom directory role
$customAdmin = New-AzureADMSRoleDefinition -RolePermissions $rolePermissions -DisplayName $displayName -Description $description -TemplateId $templateId -IsEnabled $true
```

### <a name="read-and-list-role-definitions"></a>Lectura y enumeración de definiciones de roles

``` PowerShell
# Get all role definitions
Get-AzureADMSRoleDefinitions

# Get single role definition by ID
Get-AzureADMSRoleDefinition -Id 86593cfc-114b-4a15-9954-97c3494ef49b

# Get single role definition by templateId
Get-AzureADMSRoleDefinition -Filter "templateId eq 'c4e39bd9-1100-46d3-8c65-fb160da0071f'"
```

### <a name="update-a-role-definition"></a>Actualización de una definición de roles

``` PowerShell
# Update role definition
# This works for any writable property on role definition. You can replace display name with other
# valid properties.
Set-AzureADMSRoleDefinition -Id c4e39bd9-1100-46d3-8c65-fb160da0071f -DisplayName "Updated DisplayName"
```

### <a name="delete-a-role-definition"></a>Eliminación de una definición de roles

``` PowerShell
# Delete role definition
Remove-AzureADMSRoleDefinitions -Id c4e39bd9-1100-46d3-8c65-fb160da0071f
```

## <a name="role-assignments"></a>Asignaciones de roles

Las asignaciones de roles contienen información que vincula una entidad de seguridad determinada (un usuario o una entidad de servicio de aplicación) a una definición de roles. Si es necesario, puede agregar un ámbito de recurso de Azure AD único para los permisos asignados.  Es posible restringir el ámbito de una asignación de roles en los roles personalizados e integrados.

### <a name="create-a-role-assignment"></a>Crear una asignación de rol

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

### <a name="read-and-list-role-assignments"></a>Lectura y enumeración de asignaciones de roles

``` PowerShell
# Get role assignments for a given principal
Get-AzureADMSRoleAssignment -Filter "principalId eq '27c8ca78-ab1c-40ae-bd1b-eaeebd6f68ac'"

# Get role assignments for a given role definition 
Get-AzureADMSRoleAssignment -Filter "roleDefinitionId eq '355aed8a-864b-4e2b-b225-ea95482e7570'"
```

### <a name="delete-a-role-assignment"></a>Eliminación de una asignación de roles

``` PowerShell
# Delete role assignment
Remove-AzureADMSRoleAssignment -Id 'qiho4WOb9UKKgng_LbPV7tvKaKRCD61PkJeKMh7Y458-1'
```

## <a name="next-steps"></a>Pasos siguientes

- Comparta sus comentarios con nosotros en el [foro de roles administrativos de Azure AD](https://feedback.azure.com/forums/169401-azure-active-directory?category_id=166032).
- Para obtener más información sobre los roles y las asignaciones de roles de administrador de Azure AD, consulte [Asignación de roles de administrador](permissions-reference.md).
- Para conocer los permisos predeterminados de usuario, consulte [Comparación de los permisos predeterminados de usuario miembro e invitado](../fundamentals/users-default-permissions.md).
