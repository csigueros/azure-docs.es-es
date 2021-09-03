---
title: Eliminación de asignaciones de rol de un grupo en Azure Active Directory
description: Quite las asignaciones de roles de un grupo en Azure Active Directory mediante Azure Portal, PowerShell o Microsoft Graph API.
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
ms.openlocfilehash: add87a653c977c1378feeaa6d204a7bce0184431
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/13/2021
ms.locfileid: "121732329"
---
# <a name="remove-role-assignments-from-a-group-in-azure-active-directory"></a>Eliminación de asignaciones de rol de un grupo en Azure Active Directory

En este artículo se describe cómo un administrador de TI puede quitar roles de Azure AD asignados a grupos. En Azure Portal, ahora puede eliminar asignaciones de roles directas e indirectas a un usuario. Si un usuario tiene asignado un rol mediante la pertenencia a un grupo, elimine el usuario del grupo para quitar la asignación de roles.

## <a name="prerequisites"></a>Requisitos previos

- Una licencia de Azure AD Premium P1 o P2
- Administrador global o administrador de roles con privilegios
- Módulo AzureAD al usar PowerShell
- Consentimiento del administrador al usar Probador de Graph para Microsoft Graph API

Para más información, consulte [Requisitos previos para usar PowerShell o Probador de Graph](prerequisites.md).

## <a name="azure-portal"></a>Azure portal

1. Inicie sesión en [Azure Portal](https://portal.azure.com) o en el [Centro de administración de Azure AD](https://aad.portal.azure.com).

1. Seleccione **Azure Active Directory** > **Roles y administradores** > *Nombre de rol*.

1. Seleccione el grupo del que desea eliminar la asignación de rol y, a continuación, **Quitar asignación**.

   ![Quite una asignación de roles de un grupo seleccionado.](./media/groups-remove-assignment/remove-assignment.png)

1. Cuando se le solicite confirmación, seleccione **Sí**.

## <a name="powershell"></a>PowerShell

### <a name="create-a-group-that-can-be-assigned-to-role"></a>Creación de un grupo al que se pueda asignar un rol

```powershell
$group = New-AzureADMSGroup -DisplayName "Contoso_Helpdesk_Administrators" -Description "This group is assigned to Helpdesk Administrator built-in role in Azure AD." -MailEnabled $true -SecurityEnabled $true -MailNickName "contosohelpdeskadministrators" -IsAssignableToRole $true
```

### <a name="get-the-role-definition-you-want-to-assign-the-group-to"></a>Obtención de la definición de roles a la que desea asignar el grupo

```powershell
$roleDefinition = Get-AzureADMSRoleDefinition -Filter "displayName eq 'Helpdesk Administrator'"
```

### <a name="create-a-role-assignment"></a>Crear una asignación de rol

```powershell
$roleAssignment = New-AzureADMSRoleAssignment -ResourceScope '/' -RoleDefinitionId $roleDefinition.Id -PrincipalId $group.objectId
```

### <a name="remove-the-role-assignment"></a>Quitar la asignación de roles

```powershell
Remove-AzureAdMSRoleAssignment -Id $roleAssignment.Id 
```

## <a name="microsoft-graph-api"></a>Microsoft Graph API

### <a name="create-a-group-that-can-be-assigned-an-azure-ad-role"></a>Creación de un grupo al que asignar un rol de Azure AD

```http
POST https://graph.microsoft.com/beta/groups
{
"description": "This group is assigned to Helpdesk Administrator built-in role of Azure AD",
"displayName": "Contoso_Helpdesk_Administrators",
"groupTypes": [
"Unified"
],
"mailEnabled": true,
"securityEnabled": true
"mailNickname": "contosohelpdeskadministrators",
"isAssignableToRole": true,
}
```

### <a name="get-the-role-definition"></a>Obtención de la definición de rol

```http
GET https://graph.microsoft.com/beta/roleManagement/directory/roleDefinitions?$filter=displayName+eq+'Helpdesk Administrator'
```

### <a name="create-the-role-assignment"></a>Creación de la asignación de roles

```http
POST https://graph.microsoft.com/beta/roleManagement/directory/roleAssignments
{
"principalId":"{object-id-of-group}",
"roleDefinitionId":"{role-definition-id}",
"directoryScopeId":"/"
}
```

### <a name="delete-role-assignment"></a>Eliminación de asignaciones de roles

```http
DELETE https://graph.microsoft.com/beta/roleManagement/directory/roleAssignments/{role-assignment-id}
```

## <a name="next-steps"></a>Pasos siguientes

- [Uso de grupos de Azure AD para administrar asignaciones de roles](groups-concept.md)
- [Solución de problemas de roles de Azure AD asignados a grupos](groups-faq-troubleshooting.yml)
