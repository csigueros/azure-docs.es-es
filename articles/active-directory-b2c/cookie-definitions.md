---
title: Definiciones de cookie
titleSuffix: Azure AD B2C
description: Proporciona definiciones para las cookies utilizadas en Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 08/12/2021
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 0b2cae7f3c338ea5966ea8e37821e7ae0ebd746d
ms.sourcegitcommit: 0396ddf79f21d0c5a1f662a755d03b30ade56905
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/17/2021
ms.locfileid: "122271437"
---
# <a name="cookies-definitions-for-azure-ad-b2c"></a>Definiciones de cookies para Azure AD B2C

En las secciones siguientes se proporciona información sobre las cookies usadas en Azure Active Directory B2C (Azure AD B2C).

## <a name="samesite"></a>SameSite

El servicio Microsoft Azure AD B2C es compatible con las configuraciones del explorador de SameSite, lo que incluye la compatibilidad de `SameSite=None` con el atributo `Secure`.

Para proteger el acceso a los sitios, los exploradores web incorporan un nuevo modelo seguro de forma predeterminada que supone que todas las cookies deben protegerse frente al acceso externo a menos que se especifique lo contrario. El explorador Chrome es el primero en implementar este cambio a partir de [Chrome 80 en febrero de 2020](https://www.chromium.org/updates/same-site). Para más información sobre la preparación del cambio en Chrome, consulte [Desarrolladores: Listos para la nueva configuración de cookies SameSite=None; Secure](https://blog.chromium.org/2019/10/developers-get-ready-for-new.html) en el blog de Chromium.

Los desarrolladores deben usar el nuevo valor de cookies, `SameSite=None`, para designar las cookies para el acceso entre sitios. Cuando el atributo `SameSite=None` está presente, se debe usar un atributo `Secure` adicional para que solo se pueda acceder a las cookies entre sitios a través de conexiones HTTPS. Valide y pruebe todas las aplicaciones, incluidas aquellas que usan Azure AD B2C.

Para más información, consulte:

* [Control de los cambios de cookies de SameSite en el explorador Chrome](../active-directory/develop/howto-handle-samesite-cookie-changes-chrome-browser.md)
* [Efecto en los sitios web de cliente y los servicios y productos de Microsoft en Chrome versión 80 o posterior](https://support.microsoft.com/help/4522904/potential-disruption-to-customer-websites-in-latest-chrome)

## <a name="cookies"></a>Cookies

En la tabla siguiente se indican las cookies empleadas en Azure AD B2C.

| Nombre | Domain | Expiration | Propósito |
| ----------- | ------ | -------------------------- | --------- |
| `x-ms-cpim-admin` | main.b2cadmin.ext.azure.com | Fin de [sesión del explorador](session-behavior.md) | Contiene datos de pertenencia de usuario entre inquilinos. Los inquilinos de los que es miembro un usuario y el nivel de pertenencia (administrador o usuario). |
| `x-ms-cpim-slice` | b2clogin.com, login.microsoftonline.com, dominio con marca | Fin de [sesión del explorador](session-behavior.md) | Se utiliza para enrutar las solicitudes a la instancia de producción adecuada. |
| `x-ms-cpim-trans` | b2clogin.com, login.microsoftonline.com, dominio con marca | Fin de [sesión del explorador](session-behavior.md) | Se utiliza para realizar un seguimiento de las transacciones (número de solicitudes de autenticación a Azure AD B2C) y la transacción actual. |
| `x-ms-cpim-sso:{Id}` | b2clogin.com, login.microsoftonline.com, dominio con marca | Fin de [sesión del explorador](session-behavior.md) | Se utiliza para mantener la sesión de SSO. Esta cookie se establece en `persistent` cuando se habilita [Mantener la sesión iniciada](session-behavior.md#enable-keep-me-signed-in-kmsi).|
| `x-ms-cpim-cache:{id}_n` | b2clogin.com, login.microsoftonline.com, dominio con marca | Fin de [sesión del explorador](session-behavior.md), autenticación correcta | Se utiliza para mantener el estado de la solicitud. |
| `x-ms-cpim-csrf` | b2clogin.com, login.microsoftonline.com, dominio con marca | Fin de [sesión del explorador](session-behavior.md) | Token de falsificación de solicitud entre sitios usado para la protección frente a falsificación de solicitud entre sitios. |
| `x-ms-cpim-dc` | b2clogin.com, login.microsoftonline.com, dominio con marca | Fin de [sesión del explorador](session-behavior.md) | Se utiliza para el enrutamiento de red de Azure AD B2C. |
| `x-ms-cpim-ctx` | b2clogin.com, login.microsoftonline.com, dominio con marca | Fin de [sesión del explorador](session-behavior.md) | Context |
| `x-ms-cpim-rp` | b2clogin.com, login.microsoftonline.com, dominio con marca | Fin de [sesión del explorador](session-behavior.md) | Se utiliza para almacenar datos de pertenencia en el inquilino del proveedor de recursos. |
| `x-ms-cpim-rc` | b2clogin.com, login.microsoftonline.com, dominio con marca | Fin de [sesión del explorador](session-behavior.md) | Se utiliza para almacenar la cookie de retransmisión. |
