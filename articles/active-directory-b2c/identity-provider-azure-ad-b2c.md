---
title: Configuración del registro y el inicio de sesión con una cuenta de Azure AD B2C de otro inquilino de Azure AD B2C
titleSuffix: Azure AD B2C
description: Proporcione registro e inicio de sesión a los clientes con cuentas de Azure AD B2C de otro inquilino en sus aplicaciones mediante Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 08/09/2021
ms.author: mimart
ms.subservice: B2C
ms.custom: fasttrack-edit, project-no-code
zone_pivot_groups: b2c-policy-type
ms.openlocfilehash: 6ad2014b8fce21eada9ced1e63a3511daa5e1891
ms.sourcegitcommit: 2d412ea97cad0a2f66c434794429ea80da9d65aa
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/14/2021
ms.locfileid: "122178059"
---
# <a name="set-up-sign-up-and-sign-in-with-an-azure-ad-b2c-account-from-another-azure-ad-b2c-tenant"></a>Configuración del registro y el inicio de sesión con una cuenta de Azure AD B2C de otro inquilino de Azure AD B2C

[!INCLUDE [active-directory-b2c-choose-user-flow-or-custom-policy](../../includes/active-directory-b2c-choose-user-flow-or-custom-policy.md)]

::: zone pivot="b2c-custom-policy"

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

::: zone-end

## <a name="overview"></a>Información general

En este artículo se describe cómo configurar una federación con otro inquilino de Azure AD B2C. Cuando las aplicaciones están protegidas con Azure AD B2C, los usuarios de otras instancias de Azure AD B2C pueden iniciar sesión con sus cuentas existentes. En el diagrama siguiente, los usuarios pueden iniciar sesión en una aplicación protegida por una instancia de Azure AD B2C de *Contoso*, con una cuenta administrada por el inquilino de Azure AD B2C de *Fabrikam*. 

![Federación de Azure AD B2C con otro inquilino de Azure AD B2C](./media/identity-provider-azure-ad-b2c/azure-ad-b2c-federation.png)


## <a name="prerequisites"></a>Requisitos previos

[!INCLUDE [active-directory-b2c-customization-prerequisites](../../includes/active-directory-b2c-customization-prerequisites.md)]

### <a name="verify-the-applications-publisher-domain"></a>Comprobación del dominio del publicador de la aplicación
A partir de noviembre de 2020, los nuevos registros de aplicaciones se muestran como no comprobados en el mensaje de consentimiento del usuario, a menos que [se haya comprobado el dominio del publicador de la aplicación](../active-directory/develop/howto-configure-publisher-domain.md) ***y además*** se haya comprobado la identidad de la empresa con Microsoft Partner Network y se haya asociado a la aplicación. [Obtenga más información](../active-directory/develop/publisher-verification-overview.md) sobre este cambio. Tenga en cuenta que, en los flujos de usuario de Azure AD B2C, el dominio del publicador solo aparece cuando se usa una [cuenta de Microsoft](../active-directory-b2c/identity-provider-microsoft-account.md) u otro inquilino de Azure AD como proveedor de identidades. Para cumplir estos nuevos requisitos, haga lo siguiente:

1. [Compruebe la identidad de la empresa con la cuenta de Microsoft Partner Network (MPN)](/partner-center/verification-responses). Este proceso comprueba la información sobre la empresa y el contacto principal de la empresa.
1. Complete el proceso de comprobación del publicador para asociar la cuenta de MPN con el registro de la aplicación mediante una de las siguientes opciones:
   - Si el registro de la aplicación para el proveedor de identidades de la cuenta de Microsoft está en un inquilino de Azure AD, [compruebe la aplicación en el portal de registro de aplicaciones](../active-directory/develop/mark-app-as-publisher-verified.md).
   - Si el registro de la aplicación para el proveedor de identidades de la cuenta de Microsoft está en un inquilino de Azure AD B2C, [marque la aplicación como publicador comprobado mediante las API de Microsoft Graph](../active-directory/develop/troubleshoot-publisher-verification.md#making-microsoft-graph-api-calls) (por ejemplo, mediante Probador de Graph). La interfaz de usuario para establecer el publicador comprobado de una aplicación está deshabilitada actualmente para inquilinos de Azure AD B2C.

## <a name="create-an-azure-ad-b2c-application"></a>Creación de una aplicación de Azure AD B2C

Para permitir el inicio de sesión de los usuarios con una cuenta de otro inquilino de Azure AD B2C (por ejemplo, Fabrikam), en su instancia de Azure AD B2C (por ejemplo, Contoso):

1. Cree un [flujo de usuario](tutorial-create-user-flows.md?pivots=b2c-user-flow) o una [directiva personalizada](tutorial-create-user-flows.md?pivots=b2c-custom-policy).
1. A continuación, cree una aplicación en Azure AD B2C, como se describe en esta sección. 

Para crear una aplicación:

1. Inicie sesión en [Azure Portal](https://portal.azure.com).
1. Asegúrese de que usa el directorio que contiene el otro inquilino de Azure AD B2C (por ejemplo, Fabrikam.com).
1. En Azure Portal, busque y seleccione **Azure AD B2C**.
1. Seleccione **Registros de aplicaciones** y luego **Nuevo registro**.
1. Escriba un **Nombre** para la aplicación. Por ejemplo, *ContosoApp*.
1. En **Tipos de cuenta compatibles**, seleccione **Cuentas en cualquier proveedor de identidades o directorio de la organización (para autenticar usuarios con flujos de usuario)** .
1. En **URI de redirección**, seleccione **Web** y, luego, escriba la siguiente dirección URL en minúsculas, donde `your-B2C-tenant-name` se reemplaza por el nombre de su inquilino de Azure AD B2C (por ejemplo, Contoso).

    ```
    https://your-B2C-tenant-name.b2clogin.com/your-B2C-tenant-name.onmicrosoft.com/oauth2/authresp
    ```

    Por ejemplo, `https://contoso.b2clogin.com/contoso.onmicrosoft.com/oauth2/authresp`.

    Si usa un [dominio personalizado](custom-domain.md), escriba `https://your-domain-name/your-tenant-name.onmicrosoft.com/oauth2/authresp`. Reemplace `your-domain-name` por el dominio personalizado y `your-tenant-name` por el nombre del inquilino.

1. En Permisos, active la casilla **Conceda consentimiento del administrador a los permisos openid y offline_access**.
1. Seleccione **Registrar**.
1. En la página **Azure AD B2C: Registros de aplicaciones**, seleccione la aplicación que ha creado, por ejemplo, *ContosoApp*.
1. Registre el **Id. de aplicación (cliente)** que se muestra en la página de información general de la aplicación. Necesitará esta información al configurar el proveedor de identidades en la siguiente sección.
1. En el menú de la izquierda, en **Administrar**, seleccione **Certificados y secretos**.
1. Seleccione **Nuevo secreto de cliente**.
1. Escriba una descripción para el secreto de cliente en el cuadro **Descripción**. Por ejemplo, *clientsecret1*.
1. En **Expira**, seleccione el tiempo durante el cual el secreto es válido y, a continuación, seleccione **Agregar**.
1. Registre el **Valor** del secreto. Necesitará esta información al configurar el proveedor de identidades en la siguiente sección.


::: zone pivot="b2c-user-flow"

## <a name="configure-azure-ad-b2c-as-an-identity-provider"></a>Configuración de Azure AD B2C como proveedor de identidades

1. Inicie sesión en [Azure Portal](https://portal.azure.com).
1. Asegúrese de que usa el directorio que contiene el inquilino de Azure AD B2C en el que quiere configurar la federación (por ejemplo, Contoso). Seleccione el filtro **Directorio y suscripción** del menú superior y elija el directorio que contiene el inquilino de Azure AD B2C (por ejemplo, Contoso).
1. Elija **Todos los servicios** en la esquina superior izquierda de Azure Portal, y busque y seleccione **Azure AD B2C**.
1. Seleccione **Proveedores de identidades** y luego **Nuevo proveedor de OpenID Connect**.
1. Escriba un **nombre**. Por ejemplo, escriba *Fabrikam*.
1. En **URL de metadatos**, escriba la siguiente dirección URL y sustituya `{tenant}` por el nombre de dominio del inquilino de Azure AD B2C (por ejemplo, Fabrikam). Reemplace `{policy}` por el nombre de la directiva que configure en el otro inquilino:

    ```
    https://{tenant}.b2clogin.com/{tenant}.onmicrosoft.com/{policy}/v2.0/.well-known/openid-configuration
    ```

    Por ejemplo, `https://fabrikam.b2clogin.com/fabrikam.onmicrosoft.com/B2C_1_susi/v2.0/.well-known/openid-configuration`.

1. En **Id. de cliente**, escriba el identificador de aplicación que ha anotado anteriormente.
1. En **Secreto de cliente**, escriba el secreto de cliente que ha anotado anteriormente.
1. En **Ámbito**, escriba el valor de `openid`.
1. Deje los valores predeterminados para **Tipo de respuesta** y **Modo de respuesta**.
1. (Opcional) En **Sugerencia de dominio**, escriba el nombre de dominio que quiere usar para el [inicio de sesión directo](direct-signin.md#redirect-sign-in-to-a-social-provider). Por ejemplo, *fabrikam.com*.
1. En **Asignación de notificaciones del proveedor de identidades**, seleccione las siguientes notificaciones:

    - **Id. de usuario**: *sub*
    - **Nombre para mostrar**: *name*
    - **Nombre propio**: *given_name*
    - **Apellido**: *family_name*
    - **Correo electrónico**: *email*

1. Seleccione **Guardar**.

## <a name="add-azure-ad-b2c-identity-provider-to-a-user-flow"></a>Adición del proveedor de identidades de Azure AD B2C a un flujo de usuario 

1. En el inquilino de Azure AD B2C, seleccione **Flujos de usuario**.
1. Haga clic en el flujo de usuario al que quiera agregar el proveedor de identidades de Azure AD B2C.
1. En **Proveedores de identidades sociales**, seleccione **Fabrikam**.
1. Seleccione **Guardar**.
1. Para probar la directiva, seleccione **Ejecutar flujo de usuario**.
1. En **Aplicación**, seleccione la aplicación web denominada *testapp1* que registró anteriormente. La **dirección URL de respuesta** debe mostrar `https://jwt.ms`.
1. Seleccione el botón **Ejecutar flujo de usuario**.
1. En la página de registro o de inicio de sesión, seleccione **Fabrikam** para iniciar sesión con el otro inquilino de Azure AD B2C.

Si el proceso de inicio de sesión se completa correctamente, el explorador se redirige a `https://jwt.ms`, que muestra el contenido del token devuelto por Azure AD B2C.

::: zone-end

::: zone pivot="b2c-custom-policy"

## <a name="create-a-policy-key"></a>Creación de una clave de directiva

Debe almacenar la clave de la aplicación que creó anteriormente en el inquilino de Azure AD B2C.

1. Asegúrese de que usa el directorio que contiene el inquilino de Azure AD B2C en el que quiere configurar la federación (por ejemplo, Contoso). Seleccione el filtro **Directorio y suscripción** del menú superior y elija el directorio que contiene el inquilino de Azure AD B2C (por ejemplo, Contoso).
1. Elija **Todos los servicios** en la esquina superior izquierda de Azure Portal, y busque y seleccione **Azure AD B2C**.
1. En **Directivas**, seleccione **Identity Experience Framework**.
1. Seleccione **Claves de directiva** y luego **Agregar**.
1. En **Opciones**, elija `Manual`.
1. Escriba un **nombre** para la clave de directiva. Por ejemplo, `FabrikamAppSecret`.  El prefijo `B2C_1A_` se agrega automáticamente al nombre de la clave cuando se crea, por lo que hace referencia a *B2C_1A_FabrikamAppSecret* en el código XML de la siguiente sección.
1. En **Secreto**, escriba el secreto de cliente que registró previamente.
1. En **Uso de claves**, seleccione `Signature`.
1. Seleccione **Crear**.

## <a name="configure-azure-ad-b2c-as-an-identity-provider"></a>Configuración de Azure AD B2C como proveedor de identidades

Para permitir que los usuarios inicien sesión con una cuenta de otro inquilino de Azure AD B2C (Fabrikam), debe definir dicha cuenta como proveedor de notificaciones con el que Azure AD B2C puede comunicarse a través de un punto de conexión. El punto de conexión proporciona un conjunto de notificaciones que Azure AD B2C usa para comprobar que un usuario concreto se ha autenticado.

Para definir Azure AD B2C como proveedor de notificaciones, agregue Azure AD B2C al elemento **ClaimsProvider** del archivo de extensión de la directiva.

1. Abra el archivo *TrustFrameworkExtensions.xml*.
1. Busque el elemento **ClaimsProviders**. Si no existe, agréguelo debajo del elemento raíz.
1. Agregue un nuevo elemento **ClaimsProvider** tal como se muestra a continuación:
    ```xml
    <ClaimsProvider>
      <Domain>fabrikam.com</Domain>
      <DisplayName>Federation with Fabrikam tenant</DisplayName>
      <TechnicalProfiles>
        <TechnicalProfile Id="AzureADB2CFabrikam-OpenIdConnect">
        <DisplayName>Fabrikam</DisplayName>
        <Protocol Name="OpenIdConnect"/>
        <Metadata>
          <!-- Update the Client ID below to the Application ID -->
          <Item Key="client_id">00000000-0000-0000-0000-000000000000</Item>
          <!-- Update the metadata URL with the other Azure AD B2C tenant name and policy name -->
          <Item Key="METADATA">https://{tenant}.b2clogin.com/{tenant}.onmicrosoft.com/{policy}/v2.0/.well-known/openid-configuration</Item>
          <Item Key="UsePolicyInRedirectUri">false</Item>
          <Item Key="response_types">code</Item>
          <Item Key="scope">openid</Item>
          <Item Key="response_mode">form_post</Item>
          <Item Key="HttpBinding">POST</Item>
        </Metadata>
        <CryptographicKeys>
          <Key Id="client_secret" StorageReferenceId="B2C_1A_FabrikamAppSecret"/>
        </CryptographicKeys>
        <OutputClaims>
          <OutputClaim ClaimTypeReferenceId="issuerUserId" PartnerClaimType="sub" />
          <OutputClaim ClaimTypeReferenceId="givenName" PartnerClaimType="given_name" />
          <OutputClaim ClaimTypeReferenceId="surname" PartnerClaimType="family_name" />
          <OutputClaim ClaimTypeReferenceId="displayName" PartnerClaimType="name" />
          <OutputClaim ClaimTypeReferenceId="email" PartnerClaimType="email" />
          <OutputClaim ClaimTypeReferenceId="identityProvider" PartnerClaimType="iss"  />
          <OutputClaim ClaimTypeReferenceId="authenticationSource" DefaultValue="socialIdpAuthentication" AlwaysUseDefaultValue="true" />
          <OutputClaim ClaimTypeReferenceId="otherMails" PartnerClaimType="emails"/>    
        </OutputClaims>
        <OutputClaimsTransformations>
          <OutputClaimsTransformation ReferenceId="CreateRandomUPNUserName" />
          <OutputClaimsTransformation ReferenceId="CreateUserPrincipalName" />
          <OutputClaimsTransformation ReferenceId="CreateAlternativeSecurityId" />
        </OutputClaimsTransformations>
        <UseTechnicalProfileForSessionManagement ReferenceId="SM-SocialLogin"/>
      </TechnicalProfile>
     </TechnicalProfiles>
    </ClaimsProvider>
    ```

1. Actualice los siguientes elementos XML con el valor pertinente:

    |Elemento XML  |Value  |
    |---------|---------|
    |ClaimsProvider\Domain  | El nombre de dominio que se usa para el [inicio de sesión directo](direct-signin.md?pivots=b2c-custom-policy#redirect-sign-in-to-a-social-provider). Escriba el nombre de dominio que quiere usar en el inicio de sesión directo. Por ejemplo, *fabrikam.com*. |
    |TechnicalProfile\DisplayName|Este valor se mostrará en el botón de inicio de sesión de la pantalla de inicio de sesión. Por ejemplo, *Fabrikam*. |
    |Metadata\client_id|El identificador de la aplicación del proveedor de identidades. Actualice el identificador de cliente con el identificador de aplicación que creó anteriormente en el otro inquilino de Azure AD B2C.|
    |Metadata\METADATA|Una dirección URL que apunta a un documento de configuración del proveedor de identidades de OpenID Connect, que es un punto de conexión de configuración conocido de OpenID. Escriba la siguiente dirección URL y reemplace `{tenant}` por el nombre de dominio del otro inquilino de Azure AD B2C (Fabrikam). Reemplace `{tenant}` por el nombre de la directiva que configure en el otro inquilino y `{policy]` por el nombre de la directiva: `https://{tenant}.b2clogin.com/{tenant}.onmicrosoft.com/{policy}/v2.0/.well-known/openid-configuration`. Por ejemplo, `https://fabrikam.b2clogin.com/fabrikam.onmicrosoft.com/B2C_1_susi/v2.0/.well-known/openid-configuration`.|
    |CryptographicKeys| Actualice el valor de **StorageReferenceId** con el nombre de la clave de la directiva que creó anteriormente. Por ejemplo, `B2C_1A_FabrikamAppSecret`.| 
    

[!INCLUDE [active-directory-b2c-add-identity-provider-to-user-journey](../../includes/active-directory-b2c-add-identity-provider-to-user-journey.md)]


```xml
<OrchestrationStep Order="1" Type="CombinedSignInAndSignUp" ContentDefinitionReferenceId="api.signuporsignin">
  <ClaimsProviderSelections>
    ...
    <ClaimsProviderSelection TargetClaimsExchangeId="AzureADB2CFabrikamExchange" />
  </ClaimsProviderSelections>
  ...
</OrchestrationStep>

<OrchestrationStep Order="2" Type="ClaimsExchange">
  ...
  <ClaimsExchanges>
    <ClaimsExchange Id="AzureADB2CFabrikamExchange" TechnicalProfileReferenceId="AzureADB2CFabrikam-OpenIdConnect" />
  </ClaimsExchanges>
</OrchestrationStep>
```

[!INCLUDE [active-directory-b2c-configure-relying-party-policy](../../includes/active-directory-b2c-configure-relying-party-policy-user-journey.md)]


## <a name="test-your-custom-policy"></a>Prueba de la directiva personalizada

1. Seleccione la directiva de usuarios de confianza, por ejemplo `B2C_1A_signup_signin`.
1. En **Aplicación**, seleccione la aplicación web que [registró anteriormente](tutorial-register-applications.md). La **dirección URL de respuesta** debe mostrar `https://jwt.ms`.
1. Seleccione el botón **Ejecutar ahora**.
1. En la página de registro o de inicio de sesión, seleccione **Fabrikam** para iniciar sesión con el otro inquilino de Azure AD B2C.

Si el proceso de inicio de sesión se completa correctamente, el explorador se redirige a `https://jwt.ms`, que muestra el contenido del token devuelto por Azure AD B2C.

::: zone-end

## <a name="next-steps"></a>Pasos siguientes

Más información sobre cómo [pasar el otro token de Azure AD B2C a la aplicación](idp-pass-through-user-flow.md).