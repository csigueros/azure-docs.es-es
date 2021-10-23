---
title: 'Ejemplo de PowerShell: enumeración de todas las aplicaciones de Application Proxy de Azure Active Directory con una directiva'
description: Ejemplo de PowerShell en el que se enumeran todas las aplicaciones de Azure Active Directory (Azure AD) Application Proxy del directorio que tienen una directiva de token de vigencia.
services: active-directory
author: kenwith
manager: karenh444
ms.service: active-directory
ms.subservice: app-proxy
ms.workload: identity
ms.topic: sample
ms.date: 04/29/2021
ms.author: kenwith
ms.reviewer: ashishj
ms.openlocfilehash: 79a0cabfc8874ec2ad8c4bfefe9e7ef733bbdf07
ms.sourcegitcommit: 611b35ce0f667913105ab82b23aab05a67e89fb7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/14/2021
ms.locfileid: "129988095"
---
# <a name="get-all-application-proxy-apps-with-a-token-lifetime-policy"></a>Obtención de todas las aplicaciones de Application Proxy con una directiva de vigencia de tokens

En este ejemplo de script de PowerShell se enumeran todas las aplicaciones de Azure Active Directory (Azure AD) Application Proxy en el directorio que tienen una directiva de vigencia de tokens y se muestran los detalles de la directiva.

[!INCLUDE [quickstarts-free-trial-note](../../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [updated-for-az](../../../../includes/updated-for-az.md)]

[!INCLUDE [cloud-shell-try-it.md](../../../../includes/cloud-shell-try-it.md)]

Este ejemplo requiere la [versión preliminar del módulo Azure AD V2 PowerShell para Graph](/powershell/azure/active-directory/install-adv2?view=azureadps-2.0-preview&preserve-view=true).

## <a name="sample-script"></a>Script de ejemplo

[!code-azurepowershell[main](~/powershell_scripts/application-proxy/get-all-appproxy-apps-with-policy.ps1 "Get all Application Proxy apps with a token lifetime policy")]

## <a name="script-explanation"></a>Explicación del script

| Get-Help | Notas |
|---|---|
|[Get-AzureADServicePrincipal](/powershell/module/azuread/get-azureadserviceprincipal) | Obtiene una entidad de servicio. |
|[Get-AzureADApplication](/powershell/module/azuread/get-azureadapplication) | Obtiene una aplicación de Azure AD. |
|[Get-AzureADPolicy](/powershell/module/azuread/get-azureadpolicy?view=azureadps-2.0-preview&preserve-view=true) | Obtiene una directiva en Azure AD. |
|[Get-AzureADServicePrincipalPolicy](/powershell/module/azuread/get-azureadserviceprincipalpolicy?view=azureadps-2.0-preview&preserve-view=true) | Obtiene la directiva de una entidad de servicio en Azure AD. |


## <a name="next-steps"></a>Pasos siguientes

Para obtener más información sobre el módulo de Azure AD PowerShell, consulte [Información general del módulo de Azure AD PowerShell](/powershell/azure/active-directory/overview).

Para obtener otros ejemplos de PowerShell para Application Proxy, consulte [Ejemplos de Azure AD PowerShell para Azure AD Application Proxy](../application-proxy-powershell-samples.md).
