---
title: 'Asignación de roles de Azure AD a grupos: Azure Active Directory'
description: Asigne roles de Azure AD a aquellos grupos a los que se puedan asignar roles en Azure Portal, PowerShell o Graph API.
services: active-directory
author: rolyon
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: roles
ms.topic: article
ms.date: 07/30/2021
ms.author: rolyon
ms.reviewer: vincesm
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 35982d62a0e92b5f4647243cde5920529d6c2989
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/13/2021
ms.locfileid: "121723112"
---
# <a name="assign-azure-ad-roles-to-groups"></a>Asignación de roles de Azure AD a grupos

En esta sección se describe cómo un administrador de TI puede asignar el rol de Azure Active Directory (Azure AD) a un grupo de Azure AD.

## <a name="prerequisites"></a>Requisitos previos

- Una licencia de Azure AD Premium P1 o P2
- Administrador global o administrador de roles con privilegios
- Módulo AzureAD al usar PowerShell
- Consentimiento del administrador al usar Probador de Graph para Microsoft Graph API

Para más información, consulte [Requisitos previos para usar PowerShell o Probador de Graph](prerequisites.md).

## <a name="azure-portal"></a>Portal de Azure

La asignación de un grupo a un rol de Azure AD es similar a la asignación de usuarios y entidades de seguridad de servicio, salvo que solo se pueden usar los grupos que admiten la asignación de roles. En Azure Portal, solo se muestran los grupos a los que se pueden asignar roles.

1. Inicie sesión en [Azure Portal](https://portal.azure.com) o en el [centro de administración de Azure AD](https://aad.portal.azure.com).

1. Seleccione los roles de **Azure Active Directory** > **y los administradores** y, después, seleccione el rol que desee asignar.

1. En la página ***Nombre de rol** _, seleccione > _*Agregar asignación**.

   ![Agregación de la nueva asignación de roles](./media/groups-assign-role/add-assignment.png)

1. Seleccione el grupo. Solo se muestran los grupos a los que se pueden asignar roles de Azure AD.

    [![Solo se muestran los grupos con los que se puede realizar una nueva asignación de roles.](./media/groups-assign-role/eligible-groups.png "Solo se muestran los grupos con los que se puede realizar una nueva asignación de roles.")](./media/groups-assign-role/eligible-groups.png#lightbox)

1. Seleccione **Agregar**.

Para obtener más información sobre la asignación de permisos de roles, consulte [Asignar roles de administrador y no administrador a los usuarios](../fundamentals/active-directory-users-assign-role-azure-portal.md).

## <a name="powershell"></a>PowerShell

### <a name="create-a-group-that-can-be-assigned-to-role"></a>Creación de un grupo al que se pueda asignar un rol

```powershell
$group = New-AzureADMSGroup -DisplayName "Contoso_Helpdesk_Administrators" -Description "This group is assigned to Helpdesk Administrator built-in role in Azure AD." -MailEnabled $true -SecurityEnabled $true -MailNickName "contosohelpdeskadministrators" -IsAssignableToRole $true 
```

### <a name="get-the-role-definition-for-the-role-you-want-to-assign"></a>Obtención de la definición de roles para el rol que quiera asignar

```powershell
$roleDefinition = Get-AzureADMSRoleDefinition -Filter "displayName eq 'Helpdesk Administrator'" 
```

### <a name="create-a-role-assignment"></a>Crear una asignación de rol

```powershell
$roleAssignment = New-AzureADMSRoleAssignment -DirectoryScopeId '/' -RoleDefinitionId $roleDefinition.Id -PrincipalId $group.Id 
```

## <a name="microsoft-graph-api"></a>Microsoft Graph API

### <a name="create-a-group-that-can-be-assigned-azure-ad-role"></a>Creación de un grupo al que se pueda asignar un rol de Azure AD

```
POST https://graph.microsoft.com/beta/groups
{
"description": "This group is assigned to Helpdesk Administrator built-in role of Azure AD.",
"displayName": "Contoso_Helpdesk_Administrators",
"groupTypes": [],
"mailEnabled": false,
"securityEnabled": true,
"mailNickname": "contosohelpdeskadministrators",
"isAssignableToRole": true
}
```

### <a name="get-the-role-definition"></a>Obtención de la definición de rol

```
GET https://graph.microsoft.com/beta/roleManagement/directory/roleDefinitions?$filter = displayName eq 'Helpdesk Administrator'
```

### <a name="create-the-role-assignment"></a>Creación de la asignación de roles

```
POST https://graph.microsoft.com/beta/roleManagement/directory/roleAssignments
{
"principalId":"<Object Id of Group>",
"roleDefinitionId":"<ID of role definition>",
"directoryScopeId":"/"
}
```
## <a name="next-steps"></a>Pasos siguientes

- [Uso de grupos de Azure AD para administrar asignaciones de roles](groups-concept.md)
- [Solución de problemas de roles de Azure AD asignados a grupos](groups-faq-troubleshooting.yml)
