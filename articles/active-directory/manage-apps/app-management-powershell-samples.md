---
title: Ejemplos de PowerShell para la administración de aplicaciones de Azure Active Directory
description: Estos ejemplos de PowerShell se usan para las aplicaciones que administra en el inquilino de Azure Active Directory. Puede usar estos scripts de ejemplo para buscar información de expiración sobre secretos y certificados.
services: active-directory
author: davidmu1
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: sample
ms.date: 02/18/2021
ms.author: davidmu
ms.reviewer: sureshja
ms.openlocfilehash: 4d1ec8858099fed1a826d9413e40bcb7f2979010
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/13/2021
ms.locfileid: "121723128"
---
# <a name="azure-active-directory-powershell-examples-for-application-management"></a>Ejemplos de PowerShell de Azure Active Directory para la administración de aplicaciones

La tabla siguiente incluye vínculos a ejemplos de scripts de PowerShell para la administración de aplicaciones de Azure AD. Estos ejemplos requieren uno de estos módulos:

- [AzureAD V2 PowerShell para Graph](/powershell/azure/active-directory/install-adv2).
- [Azure AD V2 PowerShell para Graph (versión preliminar)](/powershell/azure/active-directory/install-adv2?view=azureadps-2.0-preview&preserve-view=true), a menos que se indique otra cosa.

Para obtener más información sobre los cmdlets utilizados en estos ejemplos, consulte [Applications](/powershell/module/azuread/#applications).

| Vínculo | Descripción |
|---|---|
|**Scripts de administración de aplicaciones**||
| [Exportación de secretos y certificados (registros de aplicaciones)](scripts/powershell-export-all-app-registrations-secrets-and-certs.md) | Exporte de secretos y certificados para los registros de aplicaciones al inquilino de Azure Active Directory. |
| [Exportación de secretos y certificados (aplicaciones empresariales)](scripts/powershell-export-all-enterprise-apps-secrets-and-certs.md) | Exporte secretos y certificados para aplicaciones empresariales al inquilino de Azure Active Directory. |
| [Exportación de secretos y certificados que expiran](scripts/powershell-export-apps-with-expriring-secrets.md) | Exporte los registros de aplicaciones con secretos y certificados que van a expirar y sus propietarios en el inquilino de Azure Active Directory. |
| [Exportación de secretos y certificados que expiran después de la fecha requerida](scripts/powershell-export-apps-with-secrets-beyond-required.md) | Exporte aplicaciones con secretos y certificados que expiran después de la fecha requerida en el inquilino de Azure Active Directory. Usa el flujo de OAuth de Client_Credentials no interactivo. |
