---
title: Transmisión de un token de acceso del proveedor de identidades a la aplicación
titleSuffix: Azure AD B2C
description: Obtenga información sobre cómo pasar un token de acceso para proveedores de identidades de OAuth 2.0 como una notificación en un flujo de usuario a la aplicación en Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 09/16/2021
ms.custom: project-no-code
ms.author: mimart
ms.subservice: B2C
zone_pivot_groups: b2c-policy-type
ms.openlocfilehash: ae5f9409811e182bcc64dd9a6ea3ec12d46cfefd
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/24/2021
ms.locfileid: "128574018"
---
# <a name="pass-an-identity-provider-access-token-to-your-application-in-azure-active-directory-b2c"></a>Transmisión de un token de acceso del proveedor de identidades a la aplicación en Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-choose-user-flow-or-custom-policy](../../includes/active-directory-b2c-choose-user-flow-or-custom-policy.md)]

Un [flujo de usuario](user-flow-overview.md) en Azure Active Directory B2C (Azure AD B2C) proporciona una oportunidad a los usuarios para registrarse o iniciar sesión con un proveedor de identidades. Cuando esto sucede, Azure AD B2C recibe un [token de acceso](tokens-overview.md) del proveedor de identidades. Azure AD B2C usa ese token para recuperar información sobre el usuario. Habilite una notificación en el flujo de usuario para pasar el token a través de las aplicaciones que registre en Azure AD B2C.

::: zone pivot="b2c-user-flow"

Actualmente, Azure AD B2C solo admite la transmisión del token de acceso de proveedores de identidades de [OAuth 2.0](add-identity-provider.md), incluidos [Facebook](identity-provider-facebook.md) y [Google](identity-provider-google.md). Para todos los demás proveedores de identidades, la notificación se devuelve en blanco.

::: zone-end

::: zone pivot="b2c-custom-policy"

Azure AD B2C admite pasar el token de acceso de los proveedores de identidades de [OAuth 2.0](authorization-code-flow.md) y [OpenID Connect](openid-connect.md). Para todos los demás proveedores de identidades, la notificación se devuelve en blanco.

::: zone-end

En el diagrama siguiente se muestra cómo un token del proveedor de identidades vuelve a la aplicación: 

![Flujo de paso a través del token del proveedor de identidades](./media/idp-pass-through-user-flow/identity-provider-pass-through-flow.png)

## <a name="prerequisites"></a>Requisitos previos

[!INCLUDE [active-directory-b2c-customization-prerequisites](../../includes/active-directory-b2c-customization-prerequisites.md)]

::: zone pivot="b2c-user-flow"

## <a name="enable-the-claim"></a>Habilitación de la notificación

1. Inicie sesión en [Azure Portal](https://portal.azure.com/) como administrador global del inquilino de Azure AD B2C.
1. Asegúrese de que usa el directorio que contiene el inquilino de Azure AD B2C. Seleccione el icono **Directorios y suscripciones** en la barra de herramientas del portal.
1. En la página **Configuración del portal | Directorios y suscripciones**, busque el directorio de Azure AD B2C en la lista **Nombre de directorio** y seleccione **Cambiar**.
1. Elija **Todos los servicios** en la esquina superior izquierda de Azure Portal, busque y seleccione **Azure AD B2C**.
1. Seleccione **Flujos de usuario (directivas)** y, a continuación, elija el flujo de usuario. Por ejemplo, **B2C_1_signupsignin1**.
1. Seleccione **Notificaciones de aplicación**.
1. Habilite la notificación **Token de acceso del proveedor de identidades**.

    ![Habilitación de la notificación Token de acceso del proveedor de identidades](./media/idp-pass-through-user-flow/identity-provider-pass-through-app-claim.png)

1. Haga clic en **Guardar** para guardar el flujo de usuario.

## <a name="test-the-user-flow"></a>Prueba del flujo de usuario

Al probar las aplicaciones en Azure AD B2C, puede ser útil que el token de Azure AD B2C se devuelva a `https://jwt.ms` para revisar las notificaciones en él.

1. En la página Introducción del flujo de usuario, seleccione **Ejecutar flujo de usuario**.
1. En **Aplicación**, seleccione la aplicación que registró anteriormente. Para ver el token en el ejemplo siguiente, **URL de respuesta** debe mostrar `https://jwt.ms`.
1. Haga clic en **Ejecutar flujo de usuario** y, a continuación, inicie sesión con sus credenciales de cuenta. Debería ver el token de acceso del proveedor de identidades en la notificación **idp_access_token**.

    Debería ver algo parecido al siguiente ejemplo:

    ![Token descodificado en jwt.ms con el bloque idp_access_token resaltado](./media/idp-pass-through-user-flow/identity-provider-pass-through-token.png)

::: zone-end

::: zone pivot="b2c-custom-policy"

## <a name="add-the-claim-elements"></a>Incorporación de elementos de notificación

1. Abra el archivo *TrustframeworkExtensions.xml* y agregue el siguiente elemento **ClaimType** con un identificador de `identityProviderAccessToken` al elemento **ClaimsSchema**:

    ```xml
    <BuildingBlocks>
      <ClaimsSchema>
        <ClaimType Id="identityProviderAccessToken">
          <DisplayName>Identity Provider Access Token</DisplayName>
          <DataType>string</DataType>
          <AdminHelpText>Stores the access token of the identity provider.</AdminHelpText>
        </ClaimType>
        ...
      </ClaimsSchema>
    </BuildingBlocks>
    ```

1. Agregue el elemento **OutputClaim** al elemento **TechnicalProfile** para cada proveedor de identidades de OAuth 2.0 para el que le gustaría el token de acceso. El ejemplo siguiente muestra el elemento agregado en el perfil técnico de Facebook:

    ```xml
    <ClaimsProvider>
      <DisplayName>Facebook</DisplayName>
      <TechnicalProfiles>
        <TechnicalProfile Id="Facebook-OAUTH">
          <OutputClaims>
            <OutputClaim ClaimTypeReferenceId="identityProviderAccessToken" PartnerClaimType="{oauth2:access_token}" />
          </OutputClaims>
          ...
        </TechnicalProfile>
      </TechnicalProfiles>
    </ClaimsProvider>
    ```

1. Guarde el archivo *TrustFrameworkExtensions.xml*.
1. Abra el archivo de directiva del usuario de confianza, como *SignUpOrSignIn.xml* y agregue el elemento **OutputClaim** a **TechnicalProfile**:

    ```xml
    <RelyingParty>
      <DefaultUserJourney ReferenceId="SignUpOrSignIn" />
      <TechnicalProfile Id="PolicyProfile">
        <OutputClaims>
          <OutputClaim ClaimTypeReferenceId="identityProviderAccessToken" PartnerClaimType="idp_access_token"/>
        </OutputClaims>
        ...
      </TechnicalProfile>
    </RelyingParty>
    ```

1. Guarde el archivo de directiva.

## <a name="test-your-policy"></a>Prueba de la directiva

Al probar las aplicaciones en Azure AD B2C, puede ser útil que el token de Azure AD B2C se devuelva a `https://jwt.ms` para poder revisar las notificaciones en él.

### <a name="upload-the-files"></a>Carga de los archivos

1. Inicie sesión en [Azure Portal](https://portal.azure.com/).
1. Asegúrese de que usa el directorio que contiene el inquilino de Azure AD B2C. Seleccione el icono **Directorios y suscripciones** en la barra de herramientas del portal.
1. En la página **Configuración del portal | Directorios y suscripciones**, busque el directorio de Azure AD B2C en la lista **Nombre de directorio** y seleccione **Cambiar**.
1. Elija **Todos los servicios** en la esquina superior izquierda de Azure Portal, y busque y seleccione **Azure AD B2C**.
1. Seleccione **Marco de experiencia de identidad**.
1. En la página Directivas personalizadas, haga clic en **Cargar directiva**.
1. Seleccione **Sobrescribir la directiva, si existe** y busque y seleccione el archivo *TrustframeworkExtensions.xml*.
1. Seleccione **Cargar**.
1. Repita los pasos 5 a 7 para el archivo del usuario de confianza, como *SignUpOrSignIn.xml*.

### <a name="run-the-policy"></a>Ejecución de la directiva

1. Abra la directiva que ha cambiado. Por ejemplo, *B2C_1A_signup_signin*.
1. En **Aplicación**, seleccione la aplicación que registró anteriormente. Para ver el token en el ejemplo siguiente, **URL de respuesta** debe mostrar `https://jwt.ms`.
1. Seleccione **Ejecutar ahora**.

    Debería ver algo parecido al siguiente ejemplo:

    ![Token descodificado en jwt.ms con el bloque idp_access_token resaltado](./media/idp-pass-through-user-flow/identity-provider-pass-through-token-custom.png)

::: zone-end

## <a name="next-steps"></a>Pasos siguientes

Consulte la [información general sobre los tokens de Azure AD B2C](tokens-overview.md).
