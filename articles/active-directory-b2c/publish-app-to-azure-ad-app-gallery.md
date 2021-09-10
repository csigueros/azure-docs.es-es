---
title: Publicación de la aplicación de Azure Active Directory B2C en la galería de aplicaciones de Azure Active Directory
description: Obtenga información sobre cómo mostrar una aplicación de Azure AD B2C compatible con el inicio de sesión único en la galería de aplicaciones de Azure Active Directory.
titleSuffix: Azure AD B2C
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 06/15/2021
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 4413832661ffe2eea6fd48f8337618e628f2094b
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/13/2021
ms.locfileid: "121723155"
---
# <a name="publish-your-azure-ad-b2c-app-to-the-azure-ad-app-gallery"></a>Publicación de la aplicación de Azure AD B2C en la galería de aplicaciones de Azure AD

La galería de aplicaciones de Azure Active Directory (Azure AD) es un catálogo de miles de aplicaciones. La galería de aplicaciones facilita la implementación y configuración del inicio de sesión único (SSO) y la automatización de la configuración de usuario. Puede encontrar aplicaciones en la nube populares en la galería, como Workday, ServiceNow y Zoom.

En este artículo se describe cómo publicar la aplicación de Azure Active Directory B2C (Azure AD B2C) en la galería de aplicaciones de Azure AD. Cuando se publica la aplicación, se muestra entre las opciones que los clientes pueden elegir cuando agregan aplicaciones a su inquilino de Azure AD.

Estas son algunas de las ventajas de agregar la aplicación de Azure AD B2C a la galería de aplicaciones:  

- La aplicación es una integración comprobada con Microsoft.
- El acceso con SSO está habilitado entre la aplicación y las aplicaciones de Azure AD.
- Los clientes pueden encontrar la aplicación en la galería con una búsqueda rápida.
- La configuración de la aplicación es sencilla y mínima.
- Los clientes obtienen un tutorial de configuración paso a paso.
- Los clientes pueden asignar la aplicación a varios usuarios y grupos de su organización.
- El administrador de inquilinos puede conceder consentimiento del administrador para todo el inquilino a la aplicación.

## <a name="sign-in-flow-overview"></a>Introducción al flujo de inicio de sesión

El flujo de inicio de sesión consta de los pasos siguientes:

1. Los usuarios van al [portal Aplicaciones](https://myapps.microsoft.com/) y seleccionan la aplicación, que abre la dirección URL de inicio de sesión de la aplicación.
1. La dirección URL de inicio de sesión de la aplicación inicia una solicitud de autorización y redirige a los usuarios al punto de conexión de autorización de Azure AD B2C.
1. Los usuarios deciden iniciar sesión con su cuenta "Corporativa" de Azure AD. Azure AD B2C los lleva al punto de conexión de autorización de Azure AD, donde inician sesión con su cuenta de trabajo.
1. Si la sesión de SSO de Azure AD está activa, Azure AD emite un token de acceso sin pedir a los usuarios que vuelvan a iniciar sesión. Si la sesión de Azure AD expira o deja de ser válida, se pedirá a los usuarios que vuelvan a iniciar sesión.

![Diagrama del flujo de conexión de OpenID de inicio de sesión.](./media/publish-app-to-azure-ad-app-gallery/app-gallery-sign-in-flow.png)

En función de la configuración de identidad de Azure AD y la sesión de SSO de los usuarios, podría pedírseles que hicieran lo siguiente:

- Proporcionar su dirección de correo electrónico o número de teléfono.
- Escribir su contraseña o inicio de sesión con la [aplicación Microsoft Authenticator](https://www.microsoft.com/p/microsoft-authenticator/9nblgggzmcj6).
- Completar la autenticación multifactor.
- Aceptar la página de consentimiento. El administrador de inquilinos del cliente puede [conceder consentimiento del administrador para todo el inquilino a la aplicación](../active-directory/manage-apps/grant-admin-consent.md). Cuando se concede consentimiento, la página de consentimiento no se presentará a los usuarios.

Tras un inicio de sesión correcto, Azure AD devuelve un token a Azure AD B2C. Azure AD B2C valida y lee las notificaciones de token y, a continuación, devuelve un token a la aplicación.

## <a name="prerequisites"></a>Requisitos previos

[!INCLUDE [active-directory-b2c-customization-prerequisites-custom-policy](../../includes/active-directory-b2c-customization-prerequisites-custom-policy.md)]

## <a name="step-1-register-your-application-in-azure-ad-b2c"></a>Paso 1: Registro de la aplicación en Azure AD B2C

Para permitir que la aplicación inicie sesión con Azure AD B2C, regístrela en el directorio de Azure AD B2C. El registro de la aplicación establece una relación de confianza entre la aplicación y Azure AD B2C. 

Si todavía no lo ha hecho, [registre una aplicación web](tutorial-register-applications.md) y [habilite la concesión implícita de token de identificador](tutorial-register-applications.md#enable-id-token-implicit-grant). Más adelante, registrará esta aplicación en la galería de aplicaciones de Azure.

## <a name="step-2-set-up-sign-in-for-multitenant-azure-ad"></a>Paso 2: Configuración del inicio de sesión para Azure AD multiinquilino

Para permitir a los empleados y consumidores de cualquier inquilino de Azure AD iniciar sesión mediante Azure AD B2C, siga las instrucciones sobre cómo [configurar el inicio de sesión para Azure AD multiinquilino](identity-provider-azure-ad-multi-tenant.md?pivots=b2c-custom-policy).

## <a name="step-3-prepare-your-app"></a>Paso 3: Preparación de la aplicación

En la aplicación, copie la dirección URL del punto de conexión de inicio de sesión. Si usa el [ejemplo de aplicación web](configure-authentication-sample-web-app.md), la dirección URL de inicio de sesión es `https://localhost:5001/MicrosoftIdentity/Account/SignIn?`. Esta dirección URL es donde la galería de aplicaciones de Azure AD lleva a los usuarios a iniciar sesión en la aplicación.

En entornos de producción, el URI de redirección del registro de la aplicación suele ser un punto de conexión accesible públicamente donde se ejecuta la aplicación, como `https://woodgrovedemo.com/Account/SignIn`. La dirección URL de respuesta debe comenzar con `https`.

## <a name="step-4-publish-your-azure-ad-b2c-app"></a>Paso 4: Publicación de la aplicación de Azure AD B2C

Por último, agregue la aplicación multiinquilino a la galería de aplicaciones de Azure AD. Siga las instrucciones en [Publicación de la aplicación en la galería de aplicaciones de Azure AD](../active-directory/develop/v2-howto-app-gallery-listing.md). Para agregar la aplicación a la galería de aplicaciones, haga lo siguiente:

1. [Cree y publique documentación](../active-directory/develop/v2-howto-app-gallery-listing.md#step-5---create-and-publish-documentation).
1. [Envíe la aplicación](../active-directory/develop/v2-howto-app-gallery-listing.md#step-6---submit-your-app) con la información siguiente:

    |Pregunta  |Respuesta que debe proporcionar  |
    |---------|---------|
    |¿Qué tipo de solicitud desea enviar?| Seleccione **List my application in the gallery** (Enumerar mi aplicación en la galería).|
    |¿Qué característica desea habilitar al enumerar su aplicación en la galería? | Seleccione **SSO federado (SAML, WS-Fed y OpenID Connect)** . | 
    | Selección del protocolo de federación de aplicaciones| Seleccione **OpenID Connect y OAuth 2.0**. |
    | Id. de aplicación (cliente) | Proporcione el identificador de [la aplicación de Azure AD B2C](#step-1-register-your-application-in-azure-ad-b2c). |
    | Dirección URL de inicio de sesión de la aplicación|Proporcione la dirección URL de inicio de sesión de la aplicación tal como se configura en [Paso 3. Preparación de la aplicación](#step-3-prepare-your-app).|
    | Multiinquilino| Seleccione **Sí**. |
    | | |

## <a name="next-steps"></a>Pasos siguientes

- Más información sobre [Publicación de la aplicación en la galería de aplicaciones de Azure AD](../active-directory/develop/v2-howto-app-gallery-listing.md).
