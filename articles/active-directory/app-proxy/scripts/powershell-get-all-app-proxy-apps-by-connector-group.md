---
title: Enumeración de los grupos de conectores para las aplicaciones del proxy de aplicación de Azure Active Directory.
description: Ejemplo de PowerShell en el que se enumeran todos los grupos de conectores de Azure Active Directory (Azure AD) Application Proxy con las aplicaciones asignadas.
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
ms.openlocfilehash: 14e22cef85d554f1c9710a3cc8054f1142e77770
ms.sourcegitcommit: 611b35ce0f667913105ab82b23aab05a67e89fb7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/14/2021
ms.locfileid: "129988295"
---
# <a name="get-all-application-proxy-apps-and-list-by-connector-group"></a>Obtención de todas las aplicaciones de Application Proxy por grupo de conectores

En este ejemplo de script de PowerShell se muestra información sobre todos los grupos de conectores de Azure Active Directory (Azure AD) Application Proxy con las aplicaciones asignadas.

[!INCLUDE [quickstarts-free-trial-note](../../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [updated-for-az](../../../../includes/updated-for-az.md)]

[!INCLUDE [cloud-shell-try-it.md](../../../../includes/cloud-shell-try-it.md)]

Este ejemplo requiere el [módulo AzureAD V2 PowerShell para Graph](/powershell/azure/active-directory/install-adv2) (AzureAD) o la [versión preliminar del módulo AzureAD V2 PowerShell para Graph](/powershell/azure/active-directory/install-adv2?view=azureadps-2.0-preview&preserve-view=true) (AzureADPreview).

## <a name="sample-script"></a>Script de ejemplo

[!code-azurepowershell[main](~/powershell_scripts/application-proxy/get-all-appproxy-apps-by-connectorgroup.ps1 "Get all Application Proxy Connector groups with the assigned applications")]

## <a name="script-explanation"></a>Explicación del script

| Get-Help | Notas |
|---|---|
|[Get-AzureADServicePrincipal](/powershell/module/azuread/get-azureadserviceprincipal) | Obtiene una entidad de servicio. |
|[Get-AzureADApplication](/powershell/module/azuread/get-azureadapplication) | Obtiene una aplicación de Azure AD. |
|[Get-AzureADApplicationProxyApplication](/powershell/module/azuread/get-azureadapplicationproxyapplication) | Recupera una aplicación configurada para Application Proxy en Azure AD. |
| [Get-AzureADApplicationProxyConnectorGroup](/powershell/module/azuread/get-azureadapplicationproxyconnectorgroup) | Recupera una lista de todos los grupos de conectores o, si se especifica, los detalles del grupo de conectores especificado. |


## <a name="next-steps"></a>Pasos siguientes

Para obtener más información sobre el módulo de Azure AD PowerShell, consulte [Información general del módulo de Azure AD PowerShell](/powershell/azure/active-directory/overview).

Para obtener otros ejemplos de PowerShell para Application Proxy, consulte [Ejemplos de Azure AD PowerShell para Azure AD Application Proxy](../application-proxy-powershell-samples.md).
