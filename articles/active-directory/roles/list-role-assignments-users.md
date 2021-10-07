---
title: 'Enumeración de las asignaciones de roles de Azure AD de un usuario: Azure Active Directory'
description: Aprenda a enumerar las asignaciones de roles de Azure AD de un usuario.
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
ms.openlocfilehash: 58647d37da29066c14457ae6624763c800e6f123
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/13/2021
ms.locfileid: "124776427"
---
# <a name="list-azure-ad-role-assignments-for-a-user"></a>Enumeración de las asignaciones de roles de Azure AD de un usuario

Un rol se puede asignar a un usuario de forma directa o transitiva a través de un grupo. En este artículo se describe cómo enumerar los roles de Azure AD asignados a un usuario. Para información sobre cómo asignar roles a grupos, consulte [Uso de grupos de Azure AD para administrar asignaciones de roles](groups-concept.md).

## <a name="prerequisites"></a>Requisitos previos

- Módulo de AzureADPreview al usar PowerShell
- Módulo Microsoft.Graph cuando se usa PowerShell
- Consentimiento del administrador al usar el Probador de Graph de Microsoft Graph API

Para más información, consulte [Requisitos previos para usar PowerShell o Probador de Graph](prerequisites.md).

## <a name="azure-portal"></a>Azure portal
Siga estos pasos para enumerar los roles de Azure AD de un usuario mediante Azure Portal. Su experiencia será diferente en función de si tiene [Azure AD Privileged Identity Management (PIM)](../privileged-identity-management/pim-configure.md) habilitado.

1. Inicie sesión en [Azure Portal](https://portal.azure.com) o en el [Centro de administración de Azure AD](https://aad.portal.azure.com).

2. Seleccione **Azure Active Directory** > **Usuarios** > *nombre de usuario* > **Roles asignados**.

    Puede ver la lista de los roles asignados al usuario en distintos ámbitos. Además, puede ver si el rol se ha asignado directamente o a través de un grupo.
    
    ![Lista de roles asignados a un usuario en Azure Portal](./media/list-role-assignments-users/list-role-definition.png)

    Si tiene una licencia Premium P2, verá la experiencia PIM, con detalles de asignación de roles válidos, activos y expirados.

    ![Lista de roles asignados a un usuario en PIM](./media/list-role-assignments-users/list-role-definition-pim.png)

## <a name="powershell"></a>PowerShell

Siga estos pasos para enumerar los roles de Azure AD asignados a un usuario mediante PowerShell.

1. Instale AzureADPreview y el módulo Microsoft.Graph mediante [Install-module](/powershell/azure/active-directory/install-adv2).
  
    ```powershell
    Install-module -name AzureADPreview
    Install-module -name Microsoft.Graph
    ```
  
2. Abra una ventana de PowerShell y use [Import-Module](/powershell/module/microsoft.powershell.core/import-module) para importar el módulo AzureADPreview. Para más información, consulte [Requisitos previos para usar PowerShell o Probador de Graph](prerequisites.md).

    ```powershell
    Import-Module -Name AzureADPreview -Force
    ```

3. En una ventana de PowerShell, use [Connect-AzureAD](/powershell/module/azuread/connect-azuread) para iniciar sesión en el inquilino.

    ```powershell
    Connect-AzureAD
    ```
4. Use [Get-AzureADMSRoleAssignment](/powershell/module/azuread/get-azureadmsroleassignment) para obtener los roles asignados directamente a un usuario.

    ```powershell
    #Get the user
    $userId = (Get-AzureADUser -Filter "userPrincipalName eq 'alice@contoso.com'").ObjectId

    #Get direct role assignments to the user
    $directRoles = (Get-AzureADMSRoleAssignment -Filter "principalId eq '$userId'").RoleDefinitionId
    ```

5. Para obtener los roles transitivos asignados al usuario, use los siguientes cmdlets.
  
    a. Use [Get-AzureADMSGroup](/powershell/module/azuread/get-azureadmsgroup) para obtener la lista de todos los grupos asignables del usuario.  
  
      ```powershell
      $roleAssignableGroups = (Get-AzureADMsGroup -All $true | Where-Object IsAssignableToRole -EQ 'True').Id
      ```

    b. Use [Connect-MgGraph](/graph/powershell/get-started) para iniciar sesión y usar cmdlets de PowerShell de Microsoft Graph.
  
      ```powershell
      Connect-MgGraph -Scopes "User.Read.All”
      ```
  
    c. Use la API [checkMemberObjects](/graph/api/user-checkmemberobjects) para averiguar de qué grupos asignables de roles es miembro el usuario. 
    
      ```powershell
      $uri = "https://graph.microsoft.com/beta/directoryObjects/$userId/microsoft.graph.checkMemberObjects"

      $userRoleAssignableGroups = (Invoke-MgGraphRequest -Method POST -Uri $uri -Body @{"ids"= $roleAssignableGroups}).value
      ```
  
    d. Use [Get-AzureADMSRoleAssignment](/powershell/module/azuread/get-azureadmsroleassignment) para recorrer en bucle los grupos y obtener los roles asignados a ellos.
  
      ```powershell
      $transitiveRoles=@()
      foreach($item in $userRoleAssignableGroups){
          $transitiveRoles += (Get-AzureADMSRoleAssignment -Filter "principalId eq '$item'").RoleDefinitionId
      }
      ```

6. Combine las asignaciones de roles directas y transitivas del usuario.
  
    ```powershell
    $allRoles = $directRoles + $transitiveRoles
    ```
  
## <a name="microsoft-graph-api"></a>Microsoft Graph API

Siga estos pasos para enumerar los roles de Azure AD asignados a un usuario mediante Microsoft Graph API en [Probador de Graph](https://aka.ms/ge).

1. Inicie sesión en [Probador de Graph](https://aka.ms/ge).

1. Use la API [List roleAssignments](/graph/api/rbacapplication-list-roleassignments) para obtener los roles asignados directamente a un usuario. Agregue la siguiente consulta a la dirección URL y seleccione **Ejecutar consulta**.

   ```HTTP
   GET https://graph.microsoft.com/beta/rolemanagement/directory/roleAssignments?$filter=principalId eq '55c07278-7109-4a46-ae60-4b644bc83a31'
   ```
  
3. Para obtener los roles transitivos asignados al usuario, siga estos pasos.

    a. Use [List groups](/graph/api/group-list) para obtener la lista de todos los grupos asignables de roles.
  
      ```HTTP
      GET https://graph.microsoft.com/beta/groups?$filter=isAssignableToRole eq true 
      ```
  
    b. Pase esta lista a la API [checkMemberObjects](/graph/api/user-checkmemberobjects) para averiguar de qué grupos asignables de roles es miembro el usuario. 
    
      ```HTTP
      POST https://graph.microsoft.com/beta/users/55c07278-7109-4a46-ae60-4b644bc83a31/checkMemberObjects
      {
          "ids": [
              "936aec09-47d5-4a77-a708-db2ff1dae6f2",
              "5425a4a0-8998-45ca-b42c-4e00920a6382",
              "ca9631ad-2d2a-4a7c-88b7-e542bd8a7e12",
              "ea3cee12-360e-411d-b0ba-2173181daa76",
              "c3c263bb-b796-48ee-b4d2-3fbc5be5f944"
          ]
      }
      ```
  
    c. Use [List roleAssignments](/graph/api/rbacapplication-list-roleassignments) para recorrer en bucle los grupos y obtener los roles asignados a ellos.
  
      ```HTTP
      GET https://graph.microsoft.com/beta/roleManagement/directory/roleAssignments?$filter=principalId eq '5425a4a0-8998-45ca-b42c-4e00920a6382' 
      ```

## <a name="next-steps"></a>Pasos siguientes

* [Enumeración de asignaciones de roles de Azure AD](view-assignments.md).
* [Asignación de roles de Azure AD a usuarios](manage-roles-portal.md)
* [Asignación de roles de Azure AD a grupos](groups-assign-role.md)
