---
title: Tokens de actualización de la Plataforma de identidad de Microsoft | Azure
titleSuffix: Microsoft identity platform
description: Conozca los tokens de actualización emitidos por Azure AD.
services: active-directory
author: SHERMANOUKO
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.topic: conceptual
ms.date: 05/25/2021
ms.author: shermanouko
ms.reviewer: mmacy, hirsin
ms.custom: aaddev, identityplatformtop40, fasttrack-edit
ms.openlocfilehash: e4d2721905d1e344cd0825466e0b0eb08578ef47
ms.sourcegitcommit: 63f3fc5791f9393f8f242e2fb4cce9faf78f4f07
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 07/26/2021
ms.locfileid: "114688115"
---
# <a name="microsoft-identity-platform-refresh-tokens"></a>Tokens de actualización de la Plataforma de identidad de Microsoft

Cuando un cliente adquiere un token de acceso para acceder a un recurso protegido, recibe también un token de actualización. El token de actualización se usa para obtener nuevos pares de tokens de acceso/actualización cuando el token de acceso actual expira. Los tokens de actualización se usan también para adquirir tokens de acceso adicionales para otros recursos. Los tokens de actualización están enlazados a una combinación de usuario y cliente, pero no están asociados a ningún recurso ni inquilino. De esta forma, un cliente puede usar un token de actualización para adquirir tokens de acceso en cualquier combinación de recurso e inquilino donde tenga permiso para hacerlo. Los tokens de actualización están cifrados y solo la Plataforma de identidad de Microsoft puede leerlos.

## <a name="prerequisites"></a>Prerrequisitos

Antes de leer este artículo, se recomienda que leer estos otros:

* [Tokens de id.](id-tokens.md) de la plataforma de identidad de Microsoft.
* [Tokens de acceso](access-tokens.md) de la Plataforma de identidad de Microsoft.

## <a name="refresh-token-lifetime"></a>Vigencia del token de actualización

Los tokens de actualización tienen una vigencia superior a la de los tokens de acceso. La vigencia predeterminada de los tokens es de 90 días y se reemplazan automáticamente por un token nuevo después de cada uso. De esta forma, cada vez que se usa un token de actualización para adquirir un token de acceso nuevo, también se emite un token de actualización nuevo. La Plataforma de identidad de Microsoft no revoca los tokens de actualización antiguos cuando se usan para obtener nuevos tokens de acceso. Elimine de forma segura el token de actualización antiguo después de adquirir uno nuevo. Los tokens de actualización deben almacenarse de forma segura, como los tokens de acceso o las credenciales de las aplicaciones. 

## <a name="refresh-token-expiration"></a>Expiración de los tokens de actualización

Los tokens de actualización se pueden revocar en cualquier momento, porque se agote el tiempo de espera o por revocaciones. Cuando esto sucede, la aplicación debe abordar los rechazos del servicio de inicio de sesión de forma controlada. Para ello, muestra al usuario un mensaje de inicio de sesión interactivo para que vuelva a iniciar sesión. 

### <a name="token-timeouts"></a>Tiempos de espera de token

No se puede configurar la vigencia de un token de actualización. No se puede reducir ni aumentar su vigencia. Si necesita definir los periodos de tiempo antes de que se pida al usuario que vuelva a iniciar sesión, configure la frecuencia de inicio de sesión en el acceso condicional. Obtenga más información sobre la [Configuración de la administración de las sesiones de autenticación con el acceso condicional](../conditional-access/howto-conditional-access-session-lifetime.md).

No todos los tokens de actualización siguen las reglas establecidas en la directiva de vigencia de los tokens. En concreto, los tokens de actualización que se usan en las [aplicaciones de una sola página](reference-third-party-cookies-spas.md) están fijadas siempre a 24 horas de actividad, como si se les aplicara una directiva de `MaxAgeSessionSingleFactor` de 24 horas. 

### <a name="revocation"></a>Revocación

El servidor puede revocar los tokens de actualización por un cambio de credenciales o por una acción del usuario o del administrador.  Los tokens de actualización se dividen en dos clases: los emitidos para clientes confidenciales (columna del extremo derecho) y los emitidos para clientes públicos (el resto de columnas).

| Change | Cookie basada en contraseñas | Token basado en contraseñas | Cookie no basada en contraseñas | Token no basado en contraseñas | Token de cliente confidencial |
|---|-----------------------|----------------------|---------------------------|--------------------------|---------------------------|
| La contraseña expira | Permanece activa | Permanece activa | Permanece activa | Permanece activa | Permanece activa |
| Contraseña cambiada por el usuario | Revocada | Revocada | Permanece activa | Permanece activa | Permanece activa |
| Usuario realiza SSPR | Revocada | Revocada | Permanece activa | Permanece activa | Permanece activa |
| Administrador restablece la contraseña | Revocada | Revocada | Permanece activa | Permanece activa | Permanece activa |
| Usuario revoca sus tokens de actualización [a través de PowerShell](/powershell/module/azuread/revoke-azureadsignedinuserallrefreshtoken) | Revocada | Revocada | Revocada | Revocada | Revocada |
| El administrador revoca todos los tokens de actualización de un usuario [a través de PowerShell](/powershell/module/azuread/revoke-azureaduserallrefreshtoken) | Revocada | Revocada |Revocada | Revocada | Revocada |
| Cierre de sesión único [en la Web](v2-protocols-oidc.md#single-sign-out) | Revocada | Permanece activa | Revocada | Permanece activa | Permanece activa |

## <a name="next-steps"></a>Pasos siguientes

* Más información acerca de la [vigencia configurable de los tokens](active-directory-configurable-token-lifetimes.md).
* Consulte [Tokens de actualización principales](../devices/concept-primary-refresh-token.md) para obtener más detalles sobre estos tokens.
