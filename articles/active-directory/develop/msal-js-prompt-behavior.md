---
title: Comportamiento de la petición en solicitudes interactivas (MSAL.js) | Azure
titleSuffix: Microsoft identity platform
description: Aprenda a personalizar el comportamiento de la petición en las llamadas interactivas mediante la Biblioteca de autenticación de Microsoft para JavaScript (MSAL.js).
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: how-to
ms.workload: identity
ms.date: 04/24/2019
ms.author: marsma
ms.reviewer: saeeda
ms.custom: aaddev
ms.openlocfilehash: f9f81bab52d60e38f07eae8ad8be217fd54082b4
ms.sourcegitcommit: 82d82642daa5c452a39c3b3d57cd849c06df21b0
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 07/07/2021
ms.locfileid: "113356570"
---
# <a name="prompt-behavior-in-msaljs-interactive-requests"></a>Comportamiento de petición en solicitudes interactivas MSAL.js

Cuando un usuario ha establecido una sesión de Azure AD activa con varias cuentas de usuario, la página de inicio de sesión de Azure AD le pedirá de manera predeterminada que seleccione una cuenta antes de continuar con el inicio de sesión. Los usuarios no verán una experiencia de selección de cuentas si solo hay una sesión autenticada con Azure AD.

La biblioteca MSAL.js (a partir de v0.2.4) no envía un parámetro de petición durante las solicitudes interactivas (`loginRedirect`, `loginPopup`, `acquireTokenRedirect` y `acquireTokenPopup`) y, por tanto, no aplica ningún comportamiento de petición. Para las solicitudes de token silencioso con el método `acquireTokenSilent`, MSAL.js pasa un parámetro de petición establecido en `none`.

En función del escenario de aplicación, puede controlar el comportamiento de petición para las solicitudes interactivas estableciendo el parámetro de petición en los parámetros de solicitud que se pasan a los métodos. Por ejemplo, si quiere invocar la experiencia de selección de cuenta:

```javascript
var request = {
    scopes: ["user.read"],
    prompt: 'select_account',
}

userAgentApplication.loginRedirect(request);
```


Al autenticarse con Azure AD, pueden pasarse los siguientes valores de petición:

**login**: Este valor obligará al usuario a escribir las credenciales en la solicitud de autenticación.

**select_account**: Este valor proporcionará al usuario una experiencia de selección de cuenta mostrando todas las cuentas de la sesión.

**consent**: Este valor invocará el cuadro de diálogo de consentimiento de OAuth, que permite a los usuarios conceder permisos a la aplicación.

**none:** Este valor garantizará que el usuario no vea ninguna petición interactiva. Se recomienda no pasar este valor a los métodos interactivos en MSAL.js porque puede tener comportamientos inesperados. En su lugar, use el método `acquireTokenSilent` para lograr llamadas silenciosas.

## <a name="next-steps"></a>Pasos siguientes

Obtenga más información sobre el parámetro `prompt` en el protocolo de [concesión implícita de OAuth 2.0](v2-oauth2-implicit-grant-flow.md) que usa la biblioteca MSAL.js.
