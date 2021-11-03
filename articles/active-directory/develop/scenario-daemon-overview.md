---
title: Compilación de una aplicación de demonio que llama a las API web | Azure
titleSuffix: Microsoft identity platform
description: Obtenga información sobre cómo compilar una aplicación de demonio que llama a las API web
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 10/14/2021
ms.author: jmprieur
ms.custom: aaddev, identityplatformtop40
ms.openlocfilehash: 1a9aa754d09333b1384fe46e424deaf3c5525ff3
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/02/2021
ms.locfileid: "131018060"
---
# <a name="scenario-daemon-application-that-calls-web-apis"></a>Escenario: Aplicación de demonio que llama a las API web

Obtenga toda la información necesaria para compilar una aplicación de demonio que llama a las API web.

## <a name="overview"></a>Información general

La aplicación puede adquirir un token para llamar a una API web en nombre propio (no en el nombre de un usuario). Este escenario es útil para aplicaciones de demonio. Usa la concesión de [credenciales del cliente](v2-oauth2-client-creds-grant-flow.md) OAuth 2.0 estándar.

![Aplicaciones de demonio](./media/scenario-daemon-app/daemon-app.svg)

Estos son algunos ejemplos de casos de usos para aplicaciones de demonio:

- Aplicaciones web que se usan para aprovisionar o administrar usuarios, o llevar a cabo procesos por lotes en un directorio
- Aplicaciones de escritorio (como servicios de Windows en Windows o procesos de demonio en Linux) que realizan trabajos por lotes, o un servicio de sistema operativo que se ejecuta en segundo plano
- API Web que necesitan manipular directorios, no usuarios específicos

Hay otro caso habitual en el que las aplicaciones que no son demonios usan las credenciales del cliente: incluso cuando actúan en nombre de usuarios, necesitan acceder a una API web o a un recurso con su propia identidad por motivos técnicos. Un ejemplo es el acceso a los secretos de Azure Key Vault o Azure SQL Database de una caché.

Las aplicaciones que adquieren un token para sus propias identidades:

- Son aplicaciones cliente confidenciales. Estas aplicaciones, dado que acceden a recursos con independencia de los usuarios, deben demostrar su identidad. También son aplicaciones bastante confidenciales. Tienen que ser aprobadas por los administradores de inquilinos de Azure Active Directory (Azure AD).
- Han registrado un secreto (contraseña de la aplicación o certificado) en Azure AD. Este secreto se pasa durante la llamada a Azure AD para obtener un token.

## <a name="specifics"></a>Características específicas

Los usuarios no pueden interactuar con una aplicación de demonio. Una aplicación de demonio requiere su propia identidad. Este tipo de aplicación solicita un token de acceso mediante su identidad de aplicación y presenta el identificador de aplicación, las credenciales (contraseña o certificado) y el URI del identificador de aplicación a Azure AD. Tras una autenticación correcta, el demonio recibe un token de acceso (y un token de actualización) de la Plataforma de identidad de Microsoft. Este token se usa luego para llamar a la API web (y se actualiza según sea necesario).

Dado que los usuarios no pueden interactuar con aplicaciones de demonio, no es posible el consentimiento incremental. Todos los permisos de API necesarios deben configurarse en el registro de aplicación. El código de la aplicación simplemente solicita permisos definidos de forma estática. Esto también significa que las aplicaciones de demonio no admiten el consentimiento incremental.

Para los desarrolladores, la experiencia total de este escenario tiene los siguientes aspectos:

- Las aplicaciones de demonio solo pueden funcionar en inquilinos de Azure AD. No tendría sentido crear una aplicación de demonio que intentara manipular cuentas personales de Microsoft. Si es desarrollador de aplicaciones de línea de negocio (LOB), creará la aplicación de demonio en el inquilino. Si es fabricante de software independiente, es posible que quiera crear una aplicación de demonio de varios inquilinos. Cada administrador de inquilinos debe dar su consentimiento.
- Durante el [registro de aplicación](./scenario-daemon-app-registration.md), no se necesita el URI de respuesta. Comparta secretos o certificados, o instrucciones de aserción firmadas, con Azure AD. También debe solicitar permisos de aplicación y otorgar consentimiento del administrador para usar esos permisos de aplicación.
- La [configuración de la aplicación](./scenario-daemon-app-configuration.md) tiene que proporcionar las credenciales de cliente como compartidas con Azure AD durante el registro de la aplicación.
- El [ámbito](scenario-daemon-acquire-token.md#scopes-to-request) usado para adquirir un token con el flujo de credenciales del cliente debe ser un ámbito estático.

## <a name="recommended-reading"></a>Lecturas recomendadas

[!INCLUDE [recommended-topics](../../../includes/active-directory-develop-scenarios-prerequisites.md)]

## <a name="next-steps"></a>Pasos siguientes

Avance al siguiente artículo de este escenario, [Registro de la aplicación](./scenario-daemon-app-registration.md).
