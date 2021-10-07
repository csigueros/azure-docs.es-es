---
title: Ocultación de una aplicación empresarial
titleSuffix: Azure AD
description: Cómo ocultar una aplicación empresarial de la experiencia del usuario en los portales de acceso de Azure Active Directory o los iniciadores de Microsoft 365.
services: active-directory
author: davidmu1
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: how-to
ms.date: 09/23/2021
ms.author: davidmu
ms.reviewer: lenalepa
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6c76498e27d7cba32e7129b1fcca57e20ab11cfb
ms.sourcegitcommit: 48500a6a9002b48ed94c65e9598f049f3d6db60c
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/26/2021
ms.locfileid: "129061492"
---
# <a name="hide-an-enterprise-application-in-azure-active-directory"></a>Ocultación de una aplicación empresarial en Azure Active Directory

Obtenga información sobre cómo ocultar aplicaciones empresariales en Azure Active Directory. Aunque una aplicación se oculte, los usuarios seguirán teniendo los permisos para ella.

## <a name="prerequisites"></a>Requisitos previos

- Se requieren privilegios de Administrador de aplicaciones para ocultar una aplicación del portal Mis aplicaciones y del iniciador de Microsoft 365.

- Se requieren privilegios de administrador global para ocultar todas las aplicaciones de Microsoft 365.

## <a name="hide-an-application-from-the-end-user"></a>Ocultar una aplicación al usuario final

Siga estos pasos para ocultar una aplicación del portal Mis aplicaciones y del iniciador de aplicaciones de Microsoft 365.

1. Inicie sesión en [Azure Portal](https://portal.azure.com) como administrador global de su directorio.
1. Seleccione **Azure Active Directory**.
1. Seleccione **Aplicaciones empresariales**.
1. En **Tipo de aplicación**, seleccione **Aplicaciones empresariales**, si aún no está seleccionado.
1. Busque la aplicación que quiere ocultar y selecciónela.
1. Para la pregunta **¿Es visible para los usuarios?** , seleccione **No**.
1. Seleccione **Guardar**.

> [!NOTE]
> Estas instrucciones solo se aplican a las aplicaciones empresariales.

## <a name="use-azure-ad-powershell-to-hide-an-application"></a>Uso de Azure AD PowerShell para ocultar una aplicación

Para ocultar una aplicación del portal Mis aplicaciones, puede agregar manualmente la etiqueta HideApp a la entidad de servicio de la aplicación. Ejecute los siguientes comandos de [Azure AD PowerShell](/powershell/module/azuread/#service_principals) para establecer la propiedad **¿Es visible para los usuarios?** en **No**.

```PowerShell
Connect-AzureAD

$objectId = "<objectId>"
$servicePrincipal = Get-AzureADServicePrincipal -ObjectId $objectId
$tags = $servicePrincipal.tags
$tags += "HideApp"
Set-AzureADServicePrincipal -ObjectId $objectId -Tags $tags
```

## <a name="hide-microsoft-365-applications-from-the-my-apps-portal"></a>Ocultación de las aplicaciones de Microsoft 365 del portal Mis aplicaciones

Siga estos pasos para ocultar todas las aplicaciones de Microsoft 365 del portal Mis aplicaciones. Las aplicaciones siguen siendo visibles en el portal de Office 365.

1. Inicie sesión en [Azure Portal](https://portal.azure.com) como administrador global de su directorio.
1. Seleccione **Azure Active Directory**.
1. Seleccione **Usuarios**.
1. Seleccione **Configuración de usuario**.
1. En **Aplicaciones empresariales**, seleccione **Administrar cómo los usuarios finales inician y ven sus aplicaciones**.
1. En **Los usuarios solo pueden ver las aplicaciones de Office 365 en el Portal de Office 365**, seleccione **Sí**.
1. Seleccione **Guardar**.

## <a name="next-steps"></a>Pasos siguientes

- [Quitar una asignación de usuario o grupo de una aplicación empresarial](./assign-user-or-group-access-portal.md)
