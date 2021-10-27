---
title: Control de Intelligent Tracking Protection (ITP) en Safari | Azure
titleSuffix: Microsoft identity platform
description: Autenticación de una aplicación de página única (SPA) cuando ya no se permiten las cookies de terceros.
services: active-directory
author: hpsin
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.topic: conceptual
ms.date: 10/06/2021
ms.author: hirsin
ms.reviewer: kkrishna
ms.custom: aaddev
ms.openlocfilehash: 13df7323c1b0e0c7a18ffa722454bf1b9ce147d8
ms.sourcegitcommit: 611b35ce0f667913105ab82b23aab05a67e89fb7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/14/2021
ms.locfileid: "129993689"
---
# <a name="handle-itp-in-safari-and-other-browsers-where-third-party-cookies-are-blocked"></a>Control de ITP en Safari y otros exploradores donde se han bloqueado las cookies de terceros

Hoy en día, muchos exploradores bloquean las cookies de terceros: cookies en las solicitudes a dominios que no son los mismos que los que se muestran en la barra del explorador. Esto interrumpe el flujo implícito y requiere nuevos patrones de autenticación para que los usuarios inicien sesión correctamente. En la Plataforma de identidad de Microsoft, usamos el flujo de autorización con clave de prueba para intercambio de código (PKCE) y tokens de actualización para mantener iniciada la sesión de los usuarios cuando las cookies de terceros están bloqueadas.

## <a name="what-is-intelligent-tracking-protection-itp"></a>¿Qué es Intelligent Tracking Protection (ITP)?

Apple Safari cuenta con una característica de protección de la privacidad predeterminada denominada [Intelligent Tracking Protection](https://webkit.org/tracking-prevention-policy/) o _ITP_. ITP bloquea las cookies de "terceros", cookies en solicitudes que cruzan dominios.

Una forma común de seguimiento de usuarios es mediante la carga de un iframe en un sitio de un tercero en segundo plano y el uso de cookies para correlacionar al usuario a través de Internet. Desafortunadamente, este patrón también es la forma estándar de implementar el [flujo implícito](v2-oauth2-implicit-grant-flow.md) en las aplicaciones de página única (SPA). Cuando un explorador bloquea las cookies de terceros para impedir el seguimiento del usuario, también se interrumpen las SPA.

Safari no es el único en bloquear las cookies de terceros para mejorar la privacidad de los usuarios. Brave bloquea las cookies de terceros de manera predeterminada, y Chromium (la plataforma que subyace a Google Chrome y Microsoft Edge) ha anunciado que también dejará de admitir las cookies de terceros en el futuro.

La solución que se describe en este artículo funciona en todos estos exploradores o en cualquier lugar donde las cookies de terceros estén bloqueadas.

## <a name="overview-of-the-solution"></a>Descripción de la solución

Para seguir autenticando usuarios en SPA, los desarrolladores de aplicaciones deben usar el [flujo de código de autorización](v2-oauth2-auth-code-flow.md). En el flujo de código de autenticación, el proveedor de identidades emite un código, y la SPA canjea el código por un token de acceso y un token de actualización. Cuando la aplicación requiere tokens adicionales, puede usar el [flujo de tokens de actualización](v2-oauth2-auth-code-flow.md#refresh-the-access-token) para obtener nuevos tokens. La Biblioteca de autenticación de Microsoft (MSAL) para JavaScript v2.0 implementa el flujo de código de autorización para las SPA y, con actualizaciones secundarias, es un reemplazo inmediato de MSAL.js 1.x.

Para la Plataforma de identidad de Microsoft, las SPA y los clientes nativos siguen una guía de protocolo similar:

- Uso de un [desafío de código PKCE](https://tools.ietf.org/html/rfc7636)
  - PKCE es _necesario_ para las SPA en la Plataforma de identidad de Microsoft. PKCE se _recomienda_ para los clientes nativos y confidenciales.
- Ausencia de un secreto de cliente

Las SPA tienen dos restricciones adicionales:

- [El URI de redirección debe estar marcado con el tipo `spa`](v2-oauth2-auth-code-flow.md#redirect-uri-setup-required-for-single-page-apps) para habilitar CORS en los puntos de conexión de inicio de sesión.
- Los tokens de actualización emitidos a través del flujo de código de autorización para los URI de redirección `spa` tienen una vigencia de 24 horas en lugar de una vigencia de 90 días.

:::image type="content" source="media/v2-oauth-auth-code-spa/active-directory-oauth-code-spa.svg" alt-text="Diagrama que muestra el flujo del código de autorización de OAuth 2 entre una aplicación de una página y el punto de conexión del servicio de token de seguridad." border="false":::

## <a name="performance-and-ux-implications"></a>Consecuencias en el rendimiento y la experiencia de usuario

Algunas aplicaciones que usan el flujo implícito intentan iniciar sesión sin redireccionar al abrir un iframe de inicio de sesión mediante `prompt=none`. En la mayoría de los exploradores, esta solicitud responderá con los tokens para el usuario que ha iniciado sesión actualmente (suponiendo que ya se haya concedido el consentimiento). Este patrón significaba que las aplicaciones no necesitaban una redirección de página completa para que el usuario pudiera iniciar la sesión, lo que mejoraba el rendimiento y la experiencia del usuario: el usuario visita la página web y ya tiene la sesión iniciada. Dado que `prompt=none` en un iframe ya no es una opción cuando las cookies de terceros están bloqueadas, las aplicaciones deben visitar la página de inicio de sesión en un marco de nivel superior para que se emita un código de autorización.

Hay dos maneras de lograr el inicio de sesión:

- **Redirecciones de página completa**
  - En la primera carga de la SPA, redirija al usuario a la página de inicio de sesión si no existe ya ninguna sesión (o si la sesión ha expirado). El explorador del usuario visitará la página de inicio de sesión, presentará las cookies que contienen la sesión del usuario y, a continuación, se redirigirá de nuevo a la aplicación con el código y los tokens en un fragmento.
  - La redirección da lugar a que la SPA se cargue dos veces. Siga los procedimientos recomendados para almacenar las SPA en caché, de modo que la aplicación no se descargue completa dos veces.
  - Considere la posibilidad de tener en la aplicación una secuencia previa a la carga, que compruebe si hay una sesión iniciada y redirija a la página de inicio de sesión antes de que la aplicación se desempaquete completamente y ejecute la carga de JavaScript.
- **Elementos emergentes**
  - Si la experiencia del usuario (UX) de una redirección de página completa no funciona para la aplicación, considere la posibilidad de usar un elemento emergente para controlar la autenticación.
  - Cuando el elemento emergente termine de redireccionar a la aplicación después de la autenticación, el código del controlador de redirección almacenará el código y los tokens en el almacenamiento local para que la aplicación los use. MSAL.js admite elementos emergentes para autenticación, al igual que la mayoría de las bibliotecas.
  - Los exploradores están disminuyendo la compatibilidad con los elementos emergentes, por lo que es posible que no sean la opción más confiable. Puede que la interacción del usuario con la SPA antes de crear el elemento emergente sea necesaria para satisfacer los requisitos del explorador.

> [!NOTE]
> Apple [describe un método emergente](https://webkit.org/blog/8311/intelligent-tracking-prevention-2-0/) como una corrección de compatibilidad temporal para otorgar a la ventana original acceso a las cookies de terceros. Aunque Apple puede quitar esta trasmisión de permisos en el futuro, no afectará a la guía que aquí se da. En este caso, el elemento emergente se usa como navegación propia a la página de inicio de sesión para que se encuentre una sesión y se pueda proporcionar un código de autenticación. Esto debería seguir funcionando en el futuro.

### <a name="a-note-on-iframe-apps"></a>Una nota sobre las aplicaciones de iframe

Un patrón común en las aplicaciones web es usar un iframe para insertar una aplicación dentro de otra. El marco de nivel superior controla la autenticación del usuario, y la aplicación hospedada en el iframe puede confiar en que el usuario ha iniciado sesión y recuperar los tokens de forma silenciosa mediante el flujo implícito. La adquisición de tokens silenciosa ya no funciona cuando se han bloqueado las cookies de terceros: la aplicación incrustada en el iframe debe cambiar al uso de elementos emergentes para tener acceso a la sesión del usuario, ya que no puede navegar a la página de inicio de sesión.

## <a name="security-implications-of-refresh-tokens-in-the-browser"></a>Consecuencias de seguridad de los tokens de actualización en el explorador

La emisión de tokens de actualización al explorador se considera un problema de seguridad. Los ataques de scripts de sitios (XSS) o los paquetes JS en peligro pueden robar el token de actualización y usarlo de forma remota hasta que expire o se revoque. Con el fin de minimizar el riesgo de los tokens de actualización robados, se emitirán tokens para las SPA con una validez de 24 horas únicamente. Después de las 24 horas, la aplicación debe adquirir un nuevo código de autorización a través de una visita de marco de nivel superior a la página de inicio de sesión.

Este patrón de token de actualización de vigencia limitada se eligió como equilibrio entre la seguridad y la experiencia de usuario degradada. Sin tokens de actualización ni cookies de terceros, el flujo de código de autorización (tal y como se recomienda en el [borrador de procedimientos recomendados actuales de seguridad de OAuth](https://tools.ietf.org/html/draft-ietf-oauth-security-topics-14)) se vuelve más oneroso cuando se requieren tokens nuevos o adicionales. Se necesita una redirección de página completa o un elemento emergente para cada token único, cada vez que un token expira (en general, cada hora en el caso de los tokens de la Plataforma de identidad de Microsoft).

## <a name="next-steps"></a>Pasos siguientes

Para obtener más información sobre el flujo de código de autorización y la Biblioteca de autenticación de Microsoft (MSAL) para JavaScript v2.0, vea:

- [Flujo de código de autorización](v2-oauth2-auth-code-flow.md).
- [Inicio rápido de MSAL.js 2.0](quickstart-v2-javascript-auth-code.md).
