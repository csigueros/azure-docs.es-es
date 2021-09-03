---
title: 'Enumeración de las definiciones de roles de Azure AD: Azure AD'
description: Aprenda a enumerar roles integrados y personalizados de Azure.
services: active-directory
author: rolyon
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: roles
ms.topic: how-to
ms.date: 07/23/2021
ms.author: rolyon
ms.reviewer: absinh
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3c5131cb56ff65b6c559186cf491c4367ecbc7d5
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/13/2021
ms.locfileid: "121748955"
---
# <a name="list-azure-ad-role-definitions"></a>Enumeración de las definiciones de roles de Azure AD

Una definición de roles es una colección de permisos que se pueden realizar, por ejemplo, de lectura, escritura y eliminación. Suele denominarse un rol. Azure Active Directory tiene más de 60 roles integrados, aunque también puede crear sus propios roles personalizados. Si alguna vez se ha preguntado "¿qué es lo que realmente hacen estos roles?", puede ver una lista detallada de los permisos para cada uno de los roles.

En este artículo se describe cómo enumerar los roles integrados y personalizados de Azure AD junto con sus permisos.

## <a name="prerequisites"></a>Requisitos previos

- Módulo de AzureADPreview al usar PowerShell
- Consentimiento del administrador al usar Probador de Graph para Microsoft Graph API

Para más información, consulte [Requisitos previos para usar PowerShell o Probador de Graph](prerequisites.md).

## <a name="azure-portal"></a>Azure portal

1. Inicie sesión en [Azure Portal](https://portal.azure.com) o en el [Centro de administración de Azure AD](https://aad.portal.azure.com).

1. Seleccione **Azure Active Directory** > **Roles y administradores** para ver la lista de roles disponibles.

    ![lista de roles en Azure Portal](./media/role-definitions-list/view-roles-in-azure-active-directory.png)

1. A la derecha, seleccione el botón de puntos suspensivos y, a continuación, **Descripción** para ver la lista completa de permisos de un rol.

    En la página se incluyen vínculos a documentación relevante que le guiarán a través de la administración de los roles.

    ![Captura de pantalla que muestra la página "Administrador global - Descripción".](./media/role-definitions-list/role-description-updated.png)

## <a name="powershell"></a>PowerShell

Siga estos pasos para enumerar roles de Azure AD mediante PowerShell.

1. Abra una ventana de PowerShell y use [Import-Module](/powershell/module/microsoft.powershell.core/import-module) para importar el módulo AzureADPreview. Para más información, consulte [Requisitos previos para usar PowerShell o Probador de Graph](prerequisites.md).

    ```powershell
    Import-Module -Name AzureADPreview -Force
    ```

2. En una ventana de PowerShell, use [Connect-AzureAD](/powershell/module/azuread/connect-azuread) para iniciar sesión en el inquilino.

    ```powershell
    Connect-AzureAD
    ```
3. Use [Get-AzureADMSRoleDefinition](/powershell/module/azuread/get-azureadmsroledefinition) para obtener todos los roles.

    ```powershell
    Get-AzureADMSRoleDefinition
    ```

4. Para ver la lista de permisos de un rol, use el siguiente cmdlet.
    
    ```powershell
    # Do this avoid truncation of the list of permissions
    $FormatEnumerationLimit = -1
    
    (Get-AzureADMSRoleDefinition -Filter "displayName eq 'Conditional Access Administrator'").RolePermissions | Format-list
    ```

## <a name="microsoft-graph-api"></a>Microsoft Graph API

Siga estas instrucciones para enumerar roles de Azure AD mediante Microsoft Graph API en [Graph Explorer](https://aka.ms/ge).

1. Inicie sesión en [Graph Explorer](https://aka.ms/ge).
2. Seleccione **GET** como método HTTP en el menú desplegable. 
3. Seleccione la versión de API para la versión **beta**.
4. Agregue la siguiente consulta para usar la API [Mostrar roleDefinitions](/graph/api/rbacapplication-list-roledefinitions).

   ```HTTP
   GET https://graph.microsoft.com/beta/roleManagement/directory/roleDefinitions
   ```

5. Seleccione **Ejecutar consulta** para enumerar los roles.
6. Para ver los permisos de un rol, use la API siguiente.

   ```HTTP
   GET https://graph.microsoft.com/beta/roleManagement/directory/roleDefinitions?$filter=DisplayName eq 'Conditional Access Administrator'&$select=rolePermissions
   ```

## <a name="next-steps"></a>Pasos siguientes

* [Enumeración de asignaciones de roles de Azure AD](view-assignments.md).
* [Asignación de roles de Azure AD a usuarios](manage-roles-portal.md).
* [Roles integrados de Azure AD](permissions-reference.md).
