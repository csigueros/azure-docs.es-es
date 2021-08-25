---
title: Aplicaciones cliente públicas y confidenciales (MSAL) | Azure
titleSuffix: Microsoft identity platform
description: Obtenga información sobre las aplicaciones cliente públicas y confidenciales en la Biblioteca de autenticación de Microsoft (MSAL).
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 04/25/2019
ms.author: marsma
ms.reviewer: saeeda
ms.custom: aaddev, has-adal-ref
ms.openlocfilehash: bbf36b67c59e1ec00bb10c748f86d95cf0c4b700
ms.sourcegitcommit: 34aa13ead8299439af8b3fe4d1f0c89bde61a6db
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/18/2021
ms.locfileid: "122418543"
---
# <a name="public-client-and-confidential-client-applications"></a>Aplicación cliente públicas y confidenciales
La Biblioteca de autenticación de Microsoft (MSAL) define dos tipos de clientes: clientes públicos y clientes confidenciales. Los dos tipos de cliente se distinguen por su capacidad de autenticar de forma segura con el servidor de autorización y mantener la confidencialidad de sus credenciales de cliente. En cambio, la Biblioteca de autenticación de Azure AD (ADAL) usa lo que se denomina *contexto de autenticación* (que es una conexión a Azure AD).

- Las **aplicaciones cliente confidenciales** son aplicaciones que se ejecutan en servidores (aplicaciones web, aplicaciones de API web o incluso las aplicaciones de servicio o demonio). Se consideran de acceso difícil y, por ese motivo, pueden mantener un secreto de aplicación. Los clientes confidenciales pueden contener secretos de tiempo de configuración. Cada instancia del cliente tiene una configuración distinta (incluidos el Id. y el secreto de cliente). Estos valores son difíciles de extraer para los usuarios finales. Una aplicación web es el cliente confidencial más común. El Id. de cliente se expone a través del explorador web, pero el secreto solo se transmite en el canal posterior y nunca se expone directamente.

    Aplicaciones cliente confidenciales: <BR>
    ![aplicación web](media/msal-client-applications/web-app.png), ![API web](media/msal-client-applications/web-api.png), ![demonio/servicio](media/msal-client-applications/daemon-service.png)

- Las **aplicaciones cliente públicas** son aplicaciones que se ejecutan en dispositivos o equipos de escritorio, o en un explorador web. No son de confianza para mantener de manera segura secretos de aplicación, por lo que solo tienen acceso a API web en nombre del usuario (solo admiten flujos de cliente públicos). Los clientes públicos no pueden contener secretos de tiempo de configuración, por lo que no tienen secretos de cliente.

    Aplicaciones cliente públicas: <BR>
    ![aplicación de escritorio](media/msal-client-applications/desktop-app.png) ![API sin explorador](media/msal-client-applications/browserless-app.png) ![aplicación móvil](media/msal-client-applications/mobile-app.png)

> [!NOTE]
> En MSAL.js, no hay ninguna separación de aplicaciones cliente públicas y confidenciales.  MSAL.js representa las aplicaciones cliente como aplicaciones de usuario basadas en agente, clientes públicos en los que el código de cliente se ejecuta en un agente de usuario como un explorador web. Estos clientes no almacenan secretos, porque el contexto del explorador es de acceso público.

## <a name="comparing-the-client-types"></a>Comparación de los tipos de cliente
Estas son algunas similitudes y diferencias entre las aplicaciones cliente públicas y las confidenciales:

- Ambos tipos de aplicación mantienen una caché de tokens de usuario y pueden adquirir un token de forma silenciosa (cuando el token ya está en la caché de tokens). Las aplicaciones cliente confidenciales también tienen una caché de tokens de aplicación para los tokens que son para la propia aplicación.
- Ambos tipos de aplicación administran cuentas de usuario y pueden obtener una cuenta de la caché de tokens de usuario, obtener una cuenta de su identificador o quitar una cuenta.
- Las aplicaciones cliente públicas tienen cuatro maneras de adquirir un token (cuatro flujos de autenticación). Las aplicaciones cliente confidenciales tienen tres maneras de adquirir un token (y una manera de procesar la dirección URL del punto de conexión de autorización del proveedor de identidades). Para más información, consulte [Adquisición de tokens](msal-acquire-cache-tokens.md).

Si ha usado ADAL, es posible que observe que, a diferencia del contexto de autenticación de ADAL, en MSAL el Id. de cliente (también denominado *Id. de aplicación* o *Id. de app*) se envía una vez en la construcción de la aplicación. No es necesario volverlo a enviar cuando la aplicación adquiere un token. Esto es válido tanto para las aplicaciones cliente públicas como para las confidenciales. Los constructores de aplicaciones cliente confidenciales también son credenciales de cliente transmitidas: el secreto que comparten con el proveedor de identidades.

## <a name="next-steps"></a>Pasos siguientes
Más información sobre lo siguiente:
- [Client application configuration options](msal-client-application-configuration.md) (Opciones de configuración de aplicación cliente)
- [Instantiating client applications by using MSAL.NET](msal-net-initializing-client-applications.md) (Creación de instancias de aplicación cliente mediante MSAL.NET)
- [Instantiating client applications by using MSAL.js](msal-js-initializing-client-applications.md) (Creación de instancias de aplicación cliente mediante MSAL.js)
