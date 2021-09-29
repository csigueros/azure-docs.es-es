---
title: Deshabilitación de la comprobación de correos electrónicos durante la suscripción de consumidores
titleSuffix: Azure AD B2C
description: Aprenda a deshabilitar la comprobación del correo electrónico durante la suscripción de consumidores en Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 09/15/2021
ms.custom: project-no-code
ms.author: mimart
ms.subservice: B2C
zone_pivot_groups: b2c-policy-type
ms.openlocfilehash: 5d7d332d0ce74151ff42bc689b0222f7020dc50d
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/24/2021
ms.locfileid: "128575216"
---
# <a name="disable-email-verification-during-customer-sign-up-in-azure-active-directory-b2c"></a>Deshabilitar la comprobación del correo electrónico durante la suscripción de consumidores en Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-choose-user-flow-or-custom-policy](../../includes/active-directory-b2c-choose-user-flow-or-custom-policy.md)]

De forma predeterminada, Azure Active Directory B2C (Azure AD B2C) comprueba la dirección de correo electrónico del cliente para las cuentas locales (cuentas de usuarios que se registran con la dirección de correo electrónico o el nombre de usuario). Azure AD B2C garantiza direcciones de correo electrónico válidas exigiendo que los consumidores puedan comprobarlas durante el proceso de suscripción. También evita que actores malintencionados usen procesos automatizados para generar cuentas fraudulentas en las aplicaciones.

Algunos desarrolladores de aplicaciones prefieren pasar por alto la comprobación de correos electrónicos durante el proceso de suscripción y, en su lugar, son los consumidores los que tienen que comprobar la dirección de correo electrónico más tarde. Para ello, Azure AD B2C puede configurarse para deshabilitar la comprobación de correo electrónico. Si se hace esto, se crea un proceso de inicio de sesión más eficaz y ofrece a los desarrolladores la flexibilidad para diferenciar aquellos consumidores que han comprobado su dirección de correo electrónico de los que no lo ha hecho.

> [!WARNING]
> Deshabilitar la comprobación de correos electrónicos en el proceso de registro puede provocar que se reciban correos no deseados. Si deshabilita la comprobación de correo electrónico predeterminada que proporciona Azure AD B2C, es recomendable implementar un sistema de comprobación de reemplazo.

## <a name="prerequisites"></a>Requisitos previos

[!INCLUDE [active-directory-b2c-customization-prerequisites](../../includes/active-directory-b2c-customization-prerequisites.md)]
## <a name="disable-email-verification"></a>Deshabilitación de la comprobación de correo electrónico

::: zone pivot="b2c-user-flow"

Siga estos pasos para deshabilitar la comprobación de correo electrónico:

1. Inicie sesión en el [Portal de Azure](https://portal.azure.com)
1. Asegúrese de que usa el directorio que contiene el inquilino de Azure AD B2C. Seleccione el icono **Directorios y suscripciones** en la barra de herramientas del portal.
1. En la página **Configuración del portal | Directorios y suscripciones**, busque el directorio de Azure AD B2C en la lista **Nombre de directorio** y seleccione **Cambiar**.
1. En el menú de la izquierda, seleccione **Azure AD B2C**. O bien, seleccione **Todos los servicios** y busque y seleccione **Azure AD B2C**.
1. Seleccione **Flujos de usuario**.
1. Seleccione el flujo de usuario para el que desea deshabilitar la comprobación de correo electrónico.
1. Seleccione **Diseños de página**.
1. Seleccione **Página de suscripción de cuenta local**.
1. En **Atributos de usuario**, seleccione **Dirección de correo electrónico**.
1. En el menú desplegable **Requiere verificación**, seleccione **No**.
1. Seleccione **Guardar**. La comprobación de correo electrónico se deshabilitará en este flujo de usuario.

::: zone-end

::: zone pivot="b2c-custom-policy"
El perfil técnico **LocalAccountSignUpWithLogonEmail** es [autofirmado](self-asserted-technical-profile.md), y se invoca durante el flujo de suscripción. Para deshabilitar la comprobación de correo electrónico, establezca los metadatos `EnforceEmailVerification` en false. Invalide los perfiles técnicos LocalAccountSignUpWithLogonEmail en el archivo de extensión. 

1. Abra el archivo de extensiones de la directiva. Por ejemplo, <em>`SocialAndLocalAccounts/` **`TrustFrameworkExtensions.xml`**</em>.
1. Busque el elemento `ClaimsProviders`. Si el elemento no existe, agréguelo.
1. Agregue el siguiente proveedor de notificaciones al elemento `ClaimsProviders`:

```xml
<ClaimsProvider>
  <DisplayName>Local Account</DisplayName>
  <TechnicalProfiles>
    <TechnicalProfile Id="LocalAccountSignUpWithLogonEmail">
      <Metadata>
        <Item Key="EnforceEmailVerification">false</Item>
      </Metadata>
    </TechnicalProfile>
  </TechnicalProfiles>
</ClaimsProvider>
```
::: zone-end

::: zone pivot="b2c-user-flow"

## <a name="test-your-policy"></a>Prueba de la directiva 

1. Inicie sesión en el [Portal de Azure](https://portal.azure.com)
1. Asegúrese de que usa el directorio que contiene el inquilino de Azure AD B2C. Seleccione el icono **Directorios y suscripciones** en la barra de herramientas del portal.
1. En la página **Configuración del portal | Directorios y suscripciones**, busque el directorio de Azure AD B2C en la lista **Nombre de directorio** y seleccione **Cambiar**.
1. En el menú de la izquierda, seleccione **Azure AD B2C**. O bien, seleccione **Todos los servicios** y busque y seleccione **Azure AD B2C**.
1. Seleccione **Flujos de usuario**.
1. Seleccione el flujo de usuario para el que desea deshabilitar la comprobación de correo electrónico. Por ejemplo, *B2C_1_signinsignup*.
1. Para probar la directiva, seleccione **Ejecutar flujo de usuario**.
1. En **Aplicación**, seleccione la aplicación web denominada *testapp1* que registró anteriormente. La **dirección URL de respuesta** debe mostrar `https://jwt.ms`.
1. Haga clic en **Ejecutar flujo de usuario**.
1. Debe poder suscribirse con una dirección de correo electrónico sin la validación.

::: zone-end

::: zone pivot="b2c-custom-policy"

## <a name="update-and-test-the-relying-party-file"></a>Actualización y prueba del archivo del usuario de confianza

1. Inicie sesión en [Azure Portal](https://portal.azure.com).
1. Asegúrese de que usa el directorio que contiene el inquilino de Azure AD. Seleccione el icono **Directorios y suscripciones** en la barra de herramientas del portal.
1. En la página **Configuración del portal | Directorios y suscripciones**, busque el directorio de Azure AD en la lista **Nombre de directorio** y, después, seleccione **Cambiar**.
1. Elija **Todos los servicios** en la esquina superior izquierda de Azure Portal, y busque y seleccione **Registros de aplicaciones**.
1. Seleccione **Marco de experiencia de identidad**.
1. Seleccione **Cargar directiva personalizada** y cargue los dos archivos de directiva modificados.
1. Seleccione la directiva de registro o inicio de sesión que cargó y haga clic en el botón **Ejecutar ahora**.
1. Debe poder suscribirse con una dirección de correo electrónico sin la validación.

::: zone-end


## <a name="next-steps"></a>Pasos siguientes

- Aprenda a [personalizar la interfaz de usuario en Azure Active Directory B2C](customize-ui-with-html.md)

