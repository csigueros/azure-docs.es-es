---
title: Incorporación de AD FS como proveedor de identidades de OpenID Connect mediante directivas personalizadas
titleSuffix: Azure AD B2C
description: Configure AD FS 2016 mediante el protocolo OpenID Connect y las directivas personalizadas en Azure Active Directory B2C.
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
ms.openlocfilehash: 63d94ebe209c4b1a40c58f3c4b1b02e70c51a391
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/24/2021
ms.locfileid: "128572287"
---
# <a name="add-ad-fs-as-an-openid-connect-identity-provider-using-custom-policies-in-azure-active-directory-b2c"></a>Incorporación de AD FS como proveedor de identidades de OpenID Connect mediante las directivas personalizadas de Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-choose-user-flow-or-custom-policy](../../includes/active-directory-b2c-choose-user-flow-or-custom-policy.md)]


## <a name="prerequisites"></a>Requisitos previos

[!INCLUDE [active-directory-b2c-customization-prerequisites](../../includes/active-directory-b2c-customization-prerequisites.md)]


## <a name="create-an-ad-fs-application"></a>Crear una aplicación de AD FS

Para habilitar el inicio de sesión para los usuarios con una cuenta deADFS en Azure Active Directory B2C (Azure AD B2C), cree un grupo de aplicaciones en su AD FS. Para más información, consulte [Compilación de una aplicación web mediante OpenID Connect con AD FS 2016 y versiones posteriores](/windows-server/identity/ad-fs/development/enabling-openid-connect-with-ad-fs)

Para crear un grupo de aplicaciones, siga estos pasos:

1. En el **Administrador del servidor**, seleccione **Herramientas** y luego **Administración de AD FS**.
1. En Administración de AD FS, haga clic con el botón derecho en **Grupos de aplicaciones** y seleccione **Agregar grupo de aplicaciones**.
1. En la pantalla de **bienvenida** del asistente para grupos de aplicaciones:
    1. Escriba el **nombre** de la aplicación. Por ejemplo, *Aplicación de Azure AD B2C*.
    1. En **Aplicaciones cliente-servidor**, seleccione la plantilla **Explorador web que accede a una aplicación web**.
    1. Seleccione **Siguiente**.
1. En la pantalla **Aplicación nativa** del Asistente para grupos de aplicaciones:
    1. Copie el valor de **Identificador de cliente**. El identificador de cliente es el **id. de aplicación** de AD FS. Necesitará el id. de aplicación más adelante en este artículo.
    1. En **URI de redirección**, escriba `https://your-tenant-name.b2clogin.com/your-tenant-name.onmicrosoft.com/oauth2/authresp` . Si usa un [dominio personalizado](custom-domain.md), escriba `https://your-domain-name/your-tenant-name.onmicrosoft.com/oauth2/authresp`. Reemplace `your-tenant-name` por el nombre del inquilino, y `your-domain-name` por el dominio personalizado.
    1. Seleccione **Siguiente**, y después, **Siguiente**, para completar el asistente para registro de aplicaciones. 
    1. Seleccione **Cerrar**.


### <a name="configure-the-app-claims"></a>Configurar las notificaciones de la aplicación

En este paso, configure las notificaciones que la aplicación AD FS devuelve a Azure AD B2C.  

1. En los **Grupos de aplicaciones**, seleccione la aplicación que ha creado.
1. En la ventana de propiedades de la aplicación, en **Aplicaciones**, seleccione la **Aplicación web**. Después, seleccione **Editar**.
    :::image type="content" source="./media/identity-provider-adfs/ad-fs-edit-app.png" alt-text="Captura de pantalla que muestra cómo editar una aplicación web.":::
1. Seleccione la pestaña **Reglas de transformación de emisión**. Luego, seleccione **Agregar regla**.
1. En **Claim rule template** (Plantilla de regla de notificación), seleccione **Send LDAP attributes as claims** (Enviar atributos LDAP como notificaciones).
1. Proporcione un valor en **Claim rule name** (Nombre de la regla de notificación). Para el **Almacén de atributos**, seleccione **Active Directory** y agregue las siguientes notificaciones.

    | Atributo LDAP | Tipo de notificación saliente |
    | -------------- | ------------------- |
    | User-Principal-Name | UPN |
    | Surname | family_name |
    | Given-Name | given_name |
    | Display-Name | name |

    Tenga en cuenta que algunos de los nombres no se mostrarán en la lista desplegable de tipo de notificaciones salientes. Debe escribirlos manualmente. (La lista desplegable es editable).

1. Seleccione **Finalizar** y, a continuación, **Cerrar**.


::: zone pivot="b2c-user-flow"

## <a name="configure-ad-fs-as-an-identity-provider"></a>Configurar AD FS como proveedor de identidades

1. Inicie sesión en [Azure Portal](https://portal.azure.com/) como administrador global del inquilino de Azure AD B2C.
1. Asegúrese de que usa el directorio que contiene el inquilino de Azure AD B2C. Seleccione el icono **Directorios y suscripciones** en la barra de herramientas del portal.
1. En la página **Configuración del portal | Directorios y suscripciones**, busque el directorio de Azure AD B2C en la lista **Nombre de directorio** y seleccione **Cambiar**.
1. Elija **Todos los servicios** en la esquina superior izquierda de Azure Portal, y busque y seleccione **Azure AD B2C**.
1. Seleccione **Proveedores de identidades** y luego **Nuevo proveedor de OpenID Connect**.
1. Escriba un **nombre**. Por ejemplo, *Contoso*.
1. En **URL de metadatos**, escriba la dirección URL del [documento de configuración de AD FS OpenID Connect](/windows-server/identity/ad-fs/development/ad-fs-openid-connect-oauth-concepts#ad-fs-endpoints). Por ejemplo:

    ```http
    https://adfs.contoso.com/adfs/.well-known/openid-configuration 
    ```

1. En **Id. de cliente**, escriba el identificador de aplicación que ha anotado anteriormente.
1. En **Ámbito**, escriba el valor de `openid`.
1. En **Tipo de respuesta**, seleccione **id_token**.
1. (Opcional) En **Sugerencia de dominio**, escriba `contoso.com`. Para más información, consulte [Configuración de inicio de sesión directo con Azure Active Directory B2C](direct-signin.md#redirect-sign-in-to-a-social-provider).
1. En **Asignación de notificaciones del proveedor de identidades**, seleccione las siguientes notificaciones:

    - **Id. de usuario**: *upn*
    - **Nombre para mostrar**: *unique_name*
    - **Nombre propio**: *given_name*
    - **Apellido**: *family_name*

1. Seleccione **Guardar**.

## <a name="add-ad-fs-identity-provider-to-a-user-flow"></a>Adición del proveedor de identidades AD FS a un flujo de usuario 

En este momento ya está configurado el proveedor de identidades de AD FS (Contoso), pero no está disponible en ninguna de las páginas de inicio de sesión. Para agregar el proveedor de identidades de AD FS a un flujo de usuario:

1. En el inquilino de Azure AD B2C, seleccione **Flujos de usuario**.
1. Seleccione el flujo de usuario al que quiera agregar el proveedor de identidades de AD FS (Contoso).
1. En **Proveedores de identidades sociales**, seleccione **Contoso**.
1. Seleccione **Guardar**.
1. Para probar la directiva, seleccione **Ejecutar flujo de usuario**.
1. En **Aplicación**, seleccione la aplicación web denominada *testapp1* que registró anteriormente. La **dirección URL de respuesta** debe mostrar `https://jwt.ms`.
1. Seleccione el botón **Ejecutar flujo de usuario**.
1. En la página de registro o de inicio de sesión, seleccione **Contoso** para iniciar sesión con la cuenta de Contoso.

Si el proceso de inicio de sesión se completa correctamente, el explorador se redirige a `https://jwt.ms`, que muestra el contenido del token devuelto por Azure AD B2C.

::: zone-end

::: zone pivot="b2c-custom-policy"


## <a name="configure-ad-fs-as-an-identity-provider"></a>Configurar AD FS como proveedor de identidades

Para permitir que los usuarios inicien sesión con una cuenta de AD FS, debe definir AD FS como proveedor de notificaciones con el que Azure AD B2C pueda comunicarse a través de un punto de conexión. 

1. Abra el archivo *TrustFrameworkExtensions.xml*.
2. Busque el elemento **ClaimsProviders**. Si no existe, agréguelo debajo del elemento raíz.
3. Agregue un nuevo elemento **ClaimsProvider** tal como se muestra a continuación:

    ```xml
    <ClaimsProvider>
      <Domain>contoso.com</Domain>
      <DisplayName>Contoso</DisplayName>
      <TechnicalProfiles>
        <TechnicalProfile Id="Contoso-OpenIdConnect">
          <DisplayName>Contoso</DisplayName>
          <Protocol Name="OpenIdConnect" />
          <Metadata>
            <Item Key="METADATA">https://your-adfs-domain/adfs/.well-known/openid-configuration</Item>
            <Item Key="response_types">id_token</Item>
            <Item Key="response_mode">form_post</Item>
            <Item Key="scope">openid</Item>
            <Item Key="HttpBinding">POST</Item>
            <Item Key="UsePolicyInRedirectUri">0</Item>
            <!-- Update the Client ID below to the Application ID -->
            <Item Key="client_id">Your AD FS application ID</Item>
          </Metadata>
          <OutputClaims>
            <OutputClaim ClaimTypeReferenceId="issuerUserId" PartnerClaimType="upn" />
            <OutputClaim ClaimTypeReferenceId="givenName" PartnerClaimType="given_name" />
            <OutputClaim ClaimTypeReferenceId="surname" PartnerClaimType="family_name" />
            <OutputClaim ClaimTypeReferenceId="displayName" PartnerClaimType="unique_name" />
            <OutputClaim ClaimTypeReferenceId="identityProvider" PartnerClaimType="iss"  />
            <OutputClaim ClaimTypeReferenceId="authenticationSource" DefaultValue="socialIdpAuthentication" AlwaysUseDefaultValue="true" />
          </OutputClaims>
          <OutputClaimsTransformations>
            <OutputClaimsTransformation ReferenceId="CreateRandomUPNUserName" />
            <OutputClaimsTransformation ReferenceId="CreateUserPrincipalName" />
            <OutputClaimsTransformation ReferenceId="CreateAlternativeSecurityId" />
          </OutputClaimsTransformations>
          <UseTechnicalProfileForSessionManagement ReferenceId="SM-SocialLogin" />
        </TechnicalProfile>
      </TechnicalProfiles>
    </ClaimsProvider>
    ```

4. En la **URL de metadatos**, escriba la dirección URL del [documento de configuración de AD FS OpenID Connect](/windows-server/identity/ad-fs/development/ad-fs-openid-connect-oauth-concepts#ad-fs-endpoints). Por ejemplo:

    ```
    https://adfs.contoso.com/adfs/.well-known/openid-configuration 
    ```
5. Establezca **client_id** en el identificador de la aplicación desde el registro de aplicación.
6. Guarde el archivo.

[!INCLUDE [active-directory-b2c-add-identity-provider-to-user-journey](../../includes/active-directory-b2c-add-identity-provider-to-user-journey.md)]


```xml
<OrchestrationStep Order="1" Type="CombinedSignInAndSignUp" ContentDefinitionReferenceId="api.signuporsignin">
  <ClaimsProviderSelections>
    ...
    <ClaimsProviderSelection TargetClaimsExchangeId="ContosoExchange" />
  </ClaimsProviderSelections>
  ...
</OrchestrationStep>

<OrchestrationStep Order="2" Type="ClaimsExchange">
  ...
  <ClaimsExchanges>
    <ClaimsExchange Id="ContosoExchange" TechnicalProfileReferenceId="Contoso-OpenIdConnect" />
  </ClaimsExchanges>
</OrchestrationStep>
```

[!INCLUDE [active-directory-b2c-configure-relying-party-policy](../../includes/active-directory-b2c-configure-relying-party-policy-user-journey.md)]

## <a name="test-your-custom-policy"></a>Prueba de la directiva personalizada

1. Seleccione la directiva de usuarios de confianza, por ejemplo `B2C_1A_signup_signin`.
1. En **Aplicación**, seleccione la aplicación web que [registró anteriormente](tutorial-register-applications.md). La **dirección URL de respuesta** debe mostrar `https://jwt.ms`.
1. Seleccione el botón **Ejecutar ahora**.
1. En la página de registro o de inicio de sesión, seleccione **Contoso** para iniciar sesión con la cuenta de Contoso.

Si el proceso de inicio de sesión se completa correctamente, el explorador se redirige a `https://jwt.ms`, que muestra el contenido del token devuelto por Azure AD B2C.


::: zone-end

## <a name="next-steps"></a>Pasos siguientes

Obtenga información sobre cómo [pasar el token de AD-FS a la aplicación](idp-pass-through-user-flow.md).