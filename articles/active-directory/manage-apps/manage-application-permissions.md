---
title: Revisión de los permisos concedidos a las aplicaciones
titleSuffix: Azure AD
description: Descubra cómo revisar y administrar los permisos de una aplicación en Azure Active Directory.
services: active-directory
author: davidmu1
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: conceptual
ms.date: 10/23/2021
ms.author: davidmu
ms.reviewer: phsignor
ms.collection: M365-identity-device-management
ms.openlocfilehash: aeb8f00735e9455fd9d6adbdaf9e0cdec6940377
ms.sourcegitcommit: 05c8e50a5df87707b6c687c6d4a2133dc1af6583
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/16/2021
ms.locfileid: "132553180"
---
# <a name="review-permissions-granted-to-applications"></a>Revisión de los permisos concedidos a las aplicaciones

En este artículo aprenderá a revisar los permisos concedidos a las aplicaciones en el inquilino de Azure Active Directory (Azure AD). Es posible que tenga que revisar los permisos cuando haya detectado una aplicación malintencionada o cuando se hayan concedido a la aplicación más permisos de los necesarios.

Los pasos de este artículo se pueden realizar en todas las aplicaciones que se han agregado al inquilino de Azure Active Directory (Azure AD) mediante el consentimiento del usuario o del administrador. Para más información acerca de cómo dar consentimiento a las aplicaciones, consulte [Marco de consentimiento de Azure Active Directory](../develop/consent-framework.md).

## <a name="prerequisites"></a>Prerrequisitos

Para revisar los permisos concedidos a las aplicaciones, necesita:

- Una cuenta de Azure con una suscripción activa. [Cree una cuenta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- Uno de los siguientes roles: Administrador global, Administrador de aplicaciones en la nube, Administrador de aplicaciones o Propietario de la entidad de servicio.

Puede acceder al portal de Azure AD para obtener scripts de PowerShell contextuales para realizar las acciones.

## <a name="review-application-permissions"></a>Revisión de los permisos de la aplicación

Para revisar los permisos de la aplicación:

1. Inicie sesión en [Azure Portal](https://portal.azure.com) con uno de los roles enumerados en los requisitos previos.
1. Seleccione **Azure Active Directory** y, luego, **Aplicaciones empresariales**.
1. Seleccione la aplicación a la que desea restringir el acceso.
1. Seleccione **Permisos**. En la barra de comandos, seleccione **Revisar permisos**.
![Captura de pantalla de la ventana de revisión de permisos.](./media/manage-application-permissions/review-permissions.png)
1. Para explicar por qué quiere revisar los permisos de la aplicación, seleccione cualquiera de las opciones enumeradas después de la pregunta **Why do you want to review permissions for this application?** (¿Por qué quiere revisar los permisos de esta aplicación?)

Cada opción genera scripts de PowerShell que permiten controlar el acceso de los usuarios a la aplicación y revisar los permisos concedidos a esta. Para información sobre cómo controlar el acceso de los usuarios a una aplicación, consulte [Eliminación del acceso de un usuario a una aplicación](methods-for-removing-user-access.md).

## <a name="revoke-permissions-using-powershell-commands"></a>Revocación de permisos mediante comandos de PowerShell

Mediante el script de PowerShell, puede revocar todos los permisos concedidos a esta aplicación.

```powershell
Connect-AzureAD

# Get Service Principal using objectId
$sp = Get-AzureADServicePrincipal -ObjectId "<ServicePrincipal objectID>"

# Get all delegated permissions for the service principal
$spOAuth2PermissionsGrants = Get-AzureADOAuth2PermissionGrant -All $true| Where-Object { $_.clientId -eq $sp.ObjectId }

# Remove all delegated permissions
$spOAuth2PermissionsGrants | ForEach-Object {
    Remove-AzureADOAuth2PermissionGrant -ObjectId $_.ObjectId
}

# Get all application permissions for the service principal
$spApplicationPermissions = Get-AzureADServiceAppRoleAssignedTo -ObjectId $sp.ObjectId -All $true | Where-Object { $_.PrincipalType -eq "ServicePrincipal" }

# Remove all delegated permissions
$spApplicationPermissions | ForEach-Object {
    Remove-AzureADServiceAppRoleAssignment -ObjectId $_.PrincipalId -AppRoleAssignmentId $_.objectId
}
```

> [!NOTE]
> La revocación del permiso actual concedido no impedirá que los usuarios vuelvan a tener consentimiento en la aplicación. Si desea bloquear el consentimiento de los usuarios, lea [Configuración del consentimiento de los usuarios finales a las aplicaciones](configure-user-consent.md).

## <a name="invalidate-the-refresh-tokens"></a>Invalidación de los tokens de actualización

```powershell
Connect-AzureAD

# Get Service Principal using objectId
$sp = Get-AzureADServicePrincipal -ObjectId "<ServicePrincipal objectID>"

# Get Azure AD App role assignments using objectID of the Service Principal
$assignments = Get-AzureADServiceAppRoleAssignment -ObjectId $sp.ObjectId -All $true | Where-Object {$_.PrincipalType -eq "User"}

# Revoke refresh token for all users assigned to the application
$assignments | ForEach-Object {
    Revoke-AzureADUserAllRefreshToken -ObjectId $_.PrincipalId
}
```

## <a name="next-steps"></a>Pasos siguientes

- [Configuración del flujo de trabajo de consentimiento del administrador](configure-admin-consent-workflow.md)
