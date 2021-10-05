---
title: Deshabilitar la forma en que un usuario re registra
titleSuffix: Azure AD
description: Cómo deshabilitar una aplicación empresarial para que ningún usuario pueda iniciar sesión en ella en Azure Active Directory
services: active-directory
author: davidmu1
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: how-to
ms.date: 09/23/2021
ms.author: davidmu
ms.reviewer: alamaral
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: e1c07d650ee3b04099a4e4e41cdd7ee7db200bff
ms.sourcegitcommit: 48500a6a9002b48ed94c65e9598f049f3d6db60c
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/26/2021
ms.locfileid: "129061815"
---
# <a name="disable-how-a-user-signs-in-for-an-application-in-azure-active-directory"></a>Deshabilitar la forma en que un usuario se registra en una aplicación en Azure Active Directory

En este artículo, se deshabilita la forma en que un usuario se registra en una aplicación en Azure Active Directory.

## <a name="prerequisites"></a>Requisitos previos

Para deshabilitar la forma en que un usuario se registra, necesita:

- Una cuenta de Azure con una suscripción activa. [Cree una cuenta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- Uno de los siguientes roles: Administrador global, Administrador de aplicaciones en la nube, Administrador de aplicaciones o Propietario de la entidad de servicio.

## <a name="disable-how-a-user-signs-in"></a>Deshabilitar la forma en que un usuario re registra

1. Inicie sesión en [Azure Portal](https://portal.azure.com) como administrador global de su directorio.
1. Busque y seleccione **Azure Active Directory**.
1. Seleccione **Aplicaciones empresariales**.
1. Busque la aplicación en la que desea deshabilitar que un usuario se registre y seleccione la aplicación.
1. Seleccione **Propiedades**.
1. Seleccione **No** **¿Habilitado para que los usuarios inicien sesión?** .
1. Seleccione **Guardar**.

## <a name="use-azure-ad-powershell-to-disable-an-unlisted-app"></a>Uso de Azure AD PowerShell para deshabilitar una aplicación que no está en la lista

Asegúrese de que ha instalado el módulo AzureAD (use el comando Install-Module -Name AzureAD). En caso de que se le pida que instale un módulo de NuGet o el nuevo módulo de PowerShell para Azure Active Directory V2, escriba S y presione ENTRAR.

Si conoce el AppId de una aplicación que no aparece en la lista de aplicaciones empresariales (por ejemplo, porque eliminó la aplicación o porque la entidad de servicio todavía no se ha creado debido a que la aplicación está autorizada previamente por Microsoft), puede crear manualmente la entidad de servicio para la aplicación y, a continuación, deshabilitarla mediante el [cmdlet de AzureAD PowerShell](/powershell/module/azuread/New-AzureADServicePrincipal).

```PowerShell
# The AppId of the app to be disabled
$appId = "{AppId}"

# Check if a service principal already exists for the app
$servicePrincipal = Get-AzureADServicePrincipal -Filter "appId eq '$appId'"
if ($servicePrincipal) {
    # Service principal exists already, disable it
    Set-AzureADServicePrincipal -ObjectId $servicePrincipal.ObjectId -AccountEnabled $false
} else {
    # Service principal does not yet exist, create it and disable it at the same time
    $servicePrincipal = New-AzureADServicePrincipal -AppId $appId -AccountEnabled $false
}
```

## <a name="next-steps"></a>Pasos siguientes

- [Quitar una asignación de usuario o grupo de una aplicación empresarial](./assign-user-or-group-access-portal.md)
