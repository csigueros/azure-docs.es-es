---
title: Tutorial para configurar Azure Active Directory B2C con BlokSec
titleSuffix: Azure AD B2C
description: Aprenda a integrar la autenticación de Azure AD B2C con BlokSec para la autenticación sin contraseña.
services: active-directory-b2c
author: gargi-sinha
manager: martinco
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 09/20/2021
ms.author: gasinh
ms.subservice: B2C
zone_pivot_groups: b2c-policy-type
ms.openlocfilehash: a983da4159f41ae6dfe261b7f42ce20c2d2fa3a4
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/24/2021
ms.locfileid: "128594752"
---
# <a name="tutorial-configure-azure-active-directory-b2c-with-bloksec-for-passwordless-authentication"></a>Tutorial: Configurar Azure Active Directory B2C con BlokSec para la autenticación sin contraseña

[!INCLUDE [active-directory-b2c-choose-user-flow-or-custom-policy](../../includes/active-directory-b2c-choose-user-flow-or-custom-policy.md)]

::: zone pivot="b2c-custom-policy"



::: zone-end

En este tutorial de ejemplo, aprenderá a integrar la autenticación de Azure Active Directory (AD) B2C con [BlokSec](https://bloksec.com/). BlokSec simplifica la experiencia de inicio de sesión del usuario final al proporcionar a los clientes autenticación sin contraseña y autenticación multifactor sin token (MFA). BlokSec protege a los clientes frente a ciberataques centrados en la identidad, como el relleno de contraseñas, la suplantación de identidad y los ataques de tipo "Man in the middle".

## <a name="scenario-description"></a>Descripción del escenario

La integración de BlokSec incluye los siguientes componentes:

- **Azure AD B2C**: se configura como el proveedor de identidades o servidor de autorización para cualquier aplicación B2C.

- **Enrutador de identidad descentralizada de BlokSec**: actúa como puerta de enlace para los servicios que desean aplicar BlokSec DIaaS™ para enrutar las solicitudes de autenticación y autorización a las aplicaciones del proveedor de identidades personales (PIdP) de los usuarios finales. configurado como un proveedor de identidades Conectar OpenID (OIDC) en Azure AD B2C.

- **Aplicación móvil basada en el SDK de BlokSec**: actúa como PIdP de los usuarios en el escenario de autenticación descentralizada. La aplicación [BlokSec yuID](https://play.google.com/store/apps/details?id=com.bloksec) que se puede descargar de forma gratuita se puede usar si su organización prefiere no desarrollar sus propias aplicaciones móviles mediante los SDK de BlokSec.
En el siguiente diagrama de arquitectura se muestra la implementación.

![Imagen que muestra el diagrama de la arquitectura](./media/partner-bloksec/partner-bloksec-architecture-diagram.png)

|Pasos| Descripción|
|:---------------|:----------------|
|1.| El usuario intenta iniciar sesión en una aplicación Azure AD B2C y se reenvía a la directiva de inicio de sesión y registro combinada de Azure AD B2C de la aplicación.|
|2.| Azure AD B2C redirige al usuario al enrutador de identidad descentralizada BlokSec mediante el flujo de código de autorización de OIDC.|
|3.| El enrutador descentralizado BlokSec envía una notificación push a la aplicación móvil del usuario, incluidos todos los detalles de contexto de la solicitud de autenticación y autorización.|
|4.| El usuario revisa el desafío de autenticación, si se acepta, se le pide biometría, como huella digital o examen facial, tal y como está disponible en su dispositivo, lo que demuestra la identidad del usuario.|
|5.| La respuesta está firmada digitalmente con la clave digital única del usuario. La respuesta de autenticación final proporciona prueba de posesión, presencia y consentimiento. La respuesta se devuelve al enrutador de identidad descentralizada BlokSec.|
|6.| El enrutador de identidad descentralizada BlokSec comprueba la firma digital con la clave pública única inmutable del usuario que se almacena en un libro de contabilidad distribuido y, a continuación, responde a Azure AD B2C con el resultado de la autenticación.|
|7.| En función del resultado de la autenticación, se concede o se deniega el acceso al usuario.|

## <a name="onboard-to-bloksec"></a>Incorporación a BlokSec

Solicite un inquilino de demostración con BlokSec rellenando [el formulario](https://bloksec.com/request-a-demo/). En el campo de mensaje indica que le gustaría incorporar con Azure AD B2C. Descargue e instale la aplicación móvil BlokSec yuID gratuita desde la tienda de aplicaciones. Una vez preparado el inquilino de demostración, recibirá un correo electrónico. En el dispositivo móvil donde está instalada la aplicación BlokSec, seleccione el vínculo para registrar la cuenta de administrador con la aplicación yuID.

::: zone pivot="b2c-user-flow"
## <a name="prerequisites"></a>Requisitos previos

Para empezar, necesitará lo siguiente:

- Una suscripción de Azure AD. Si no tiene una suscripción, puede crear una [cuenta gratuita](https://azure.microsoft.com/free/).

- Un [inquilino de Azure AD B2C](./tutorial-create-tenant.md) vinculado a la suscripción de Azure.

- Una [cuenta de prueba](https://bloksec.com/) de BlokSec.

- Si todavía no lo ha hecho, [registre una aplicación web](./tutorial-register-applications.md) y [habilite la concesión implícita de token de identificador](./tutorial-register-applications.md#enable-id-token-implicit-grant).
::: zone-end

::: zone pivot="b2c-custom-policy"
## <a name="prerequisites"></a>Requisitos previos

Para empezar, necesitará lo siguiente:

- Una suscripción de Azure AD. Si no tiene una suscripción, puede crear una [cuenta gratuita](https://azure.microsoft.com/free/).

- Un [inquilino de Azure AD B2C](./tutorial-create-tenant.md) vinculado a la suscripción de Azure.

- Una [cuenta de prueba](https://bloksec.com/) de BlokSec.

- Si todavía no lo ha hecho, [registre una aplicación web](./tutorial-register-applications.md) y [habilite la concesión implícita de token de identificador](./tutorial-register-applications.md#enable-id-token-implicit-grant).

- Siga los pasos de [**Introducción a las directivas personalizadas en Azure Active Directory B2C**](./tutorial-create-user-flows.md?pivots=b2c-custom-policy).
::: zone-end

### <a name="part-1---create-an-application-registration-in-bloksec"></a>Parte 1: Creación de un registro de aplicación en BlokSec

1. Inicie sesión en el portal de administración de BlokSec. Se incluirá un vínculo como parte del correo electrónico de registro de la cuenta recibido al incorporarse a BlokSec.

2. En el panel principal, seleccione **Agregar aplicación > Crear personalizada**

3. Complete los detalles de la aplicación como se muestra a continuación y envíe:  

   |Propiedad  |Value  |
   |---------|---------|
   |  Nombre         |Azure AD B2C o el nombre de aplicación deseado|
   |Tipo de SSO         | OIDC|
   |URI del logotipo     |[https://bloksec.io/assets/AzureB2C.png](https://bloksec.io/assets/AzureB2C.png) un vínculo a la imagen de su elección|
   |URI de redirección     | https://**your-B2C-tenant-name**.b2clogin.com/**your-B2C-tenant-name**.onmicrosoft.com/oauth2/authresp<BR>**Por ejemplo**:      'https://fabrikam.b2clogin.com/fabrikam.onmicrosoft.com/oauth2/authresp ' <BR><BR>Si utiliza un dominio personalizado, introduzca https://**su-nombre-dominio**/**su-nombre-inquilino**.onmicrosoft.com/oauth2/authresp. <BR> Sustituye su-nombre-de-dominio por el dominio personalizado y su-nombre-de-inquilino por el nombre del inquilino.         |
   |URI de redireccionamiento posteriores al cierre de sesión  |https://**your-B2C-tenant-name**.b2clogin.com/**your-B2C-tenant-name**.onmicrosoft.com/ **{policy}** /oauth2/v2.0/logout <BR> [Envíe una solicitud de cierre de sesión](./openid-connect.md#send-a-sign-out-request). |

4. Una vez guardado, seleccione la aplicación recién Azure AD B2C para abrir la configuración de la aplicación, seleccione **Generar secreto de aplicación**.

>[!NOTE]
>Necesitará el identificador de aplicación y el secreto de aplicación más adelante para configurar el proveedor de identidades en Azure AD B2C.

::: zone pivot="b2c-user-flow"

### <a name="part-2---add-a-new-identity-provider-in-azure-ad-b2c"></a>Parte 2: Adición de un nuevo proveedor de identidades en Azure AD B2C

1. Inicie sesión en [Azure Portal](https://portal.azure.com/#home) como administrador global del inquilino de Azure AD B2C.
1. Asegúrese de que usa el directorio que contiene el inquilino de Azure AD B2C. Seleccione el icono **Directorios y suscripciones** en la barra de herramientas del portal.
1. En la página **Configuración del portal | Directorios y suscripciones**, busque el directorio de Azure AD B2C en la lista **Nombre de directorio** y seleccione **Cambiar**.
1. Elija **Todos los servicios** en la esquina superior izquierda de Azure Portal, busque y seleccione **Azure AD B2C**. 
1. Vaya a **Panel > Azure Active Directory B2C > Proveedores de identidades**.
1. Seleccione **Proveedor de OpenID Connect**.
1. Seleccione **Agregar**.

### <a name="part-3---configure-an-identity-provider"></a>Parte 3: Configuración de un proveedor de identidades

1. Seleccione **Tipo de proveedor de identidades OpenID Connect**.

1. Rellene el formulario para configurar el proveedor de identidades:

|Propiedad  |Value  |
|:---------|:---------|
|Nombre     |Escriba BlokSec yuID – Passwordless (BlokSec yuID: sin contraseña) o un nombre de su elección.|
|URL de metadatos|https://api.bloksec.io/oidc/.well-known/openid-configuration|         
|Id. de cliente|El identificador de aplicación de la interfaz de usuario de administrador de BlokSec capturada en la **parte 1**.|
|Secreto del cliente|El secreto de aplicación de la interfaz de usuario de administrador de BlokSec capturada en la **parte 1**.|
|Ámbito|Perfil de correo electrónico de OpenID|
|Tipo de respuesta|Código|
|Sugerencia de dominio|yuID|

1. Seleccione **Aceptar**.

1. Seleccione **Asignar las notificaciones de este proveedor de identidades**.

1. Rellene el formulario para asignar el proveedor de identidades:

|Propiedad  |Valor  |
|:---------|:---------|
|Id. de usuario|sub|
|Nombre para mostrar|name|
|Nombre propio|given_name|
|Surname|family_name|
|Email|email|

1. Seleccione **Guardar** para completar la instalación del nuevo proveedor de identidades de OIDC.  

### <a name="part-4---user-registration"></a>Parte 4: Registro del usuario

1. Inicie sesión en la consola de administración de BlokSec con la credencial proporcionada anteriormente.

1. Vaya a la aplicación Azure AD B2C que se creó anteriormente. Seleccione el icono de engranaje en la parte superior derecha y, a continuación, seleccione **Crear cuenta**.  

1. Escriba la información del usuario en el formulario Crear cuenta, anote el nombre de la cuenta y seleccione **Enviar**.  

El usuario recibirá un **correo electrónico de registro de cuenta** en la dirección de correo electrónico proporcionada. Haga que el usuario siga el vínculo de registro en el dispositivo móvil donde está instalada la aplicación BlokSec yuID.

### <a name="part-5---create-a-user-flow-policy"></a>Parte 5: Creación de una directiva de flujo de usuario

Ahora debería ver BlokSec como un nuevo proveedor de identidades de Open ID Connect enumerado entre los proveedores de identidades de B2C.  

1. En el inquilino de Azure AD B2C, en **Directivas**, seleccione **Flujos de usuario**.  

1. Seleccione **Nuevo flujo de usuario**.

1. Seleccione **Inscribirse e iniciar sesión en** > **Versión** > **Crear**.

1. Escriba un **nombre** para la directiva.

1. En la sección Proveedores de identidades seleccione el proveedor de identidades de BlokSec recién creado.  

1. Seleccione **Ninguna** en Cuentas locales para desactivar la autenticación basada en correo electrónico y contraseña.

1. Seleccione **Ejecutar el flujo de usuario**.

1. En el formulario, escriba la dirección URL de respuesta, por ejemplo, https://jwt.ms

1. El explorador se redirigirá a la página de inicio de sesión de BlokSec. Escriba el nombre de cuenta registrado durante el registro del usuario. El usuario recibirá una notificación push en su dispositivo móvil donde está instalada la aplicación BlokSec yuID. Al abrir la notificación, se presentará al usuario un desafío de autenticación.

1. Una vez aceptado el desafío de autenticación, el explorador redirigirá al usuario a la dirección URL de respuesta.  

## <a name="next-steps"></a>Pasos siguientes 

Para más información, consulte los artículos siguientes:

- [Directivas personalizadas de Azure AD B2C](./custom-policy-overview.md)

- [Introducción a las directivas personalizadas en Azure AD B2C](./tutorial-create-user-flows.md?pivots=b2c-custom-policy)

::: zone-end

::: zone pivot="b2c-custom-policy"

>[!NOTE]
>En Azure Active Directory B2C, las [**directivas personalizadas**](./user-flow-overview.md) se han diseñado principalmente para abordar escenarios complejos. Para la mayoría de los escenarios, se recomienda usar [**flujos de usuario**](./user-flow-overview.md) integrados.

### <a name="part-2---create-a-policy-key"></a>Parte 2: Creación de una clave de directiva

Almacene el secreto de cliente que haya registrado previamente en el inquilino de Azure AD B2C.

1. Inicie sesión en [Azure Portal](https://portal.azure.com/).
1. Asegúrese de que usa el directorio que contiene el inquilino de Azure AD B2C. Seleccione el icono **Directorios y suscripciones** en la barra de herramientas del portal.
1. En la página **Configuración del portal | Directorios y suscripciones**, busque el directorio de Azure AD B2C en la lista **Nombre de directorio** y seleccione **Cambiar**.
1. Elija **Todos los servicios** en la esquina superior izquierda de Azure Portal, y busque y seleccione **Azure AD B2C**.
1. En la página de introducción, seleccione **Identity Experience Framework**.
1. Seleccione **Claves de directiva** y luego **Agregar**.
1. En **Opciones**, elija `Manual`.
1. Escriba un **nombre** para la clave de directiva. Por ejemplo, `BlokSecAppSecret`. Se agregará el prefijo `B2C_1A_` automáticamente al nombre de la clave.
1. En **Secreto**, escriba el secreto de cliente que haya registrado previamente.
1. En **Uso de claves**, seleccione `Signature`.
1. Seleccione **Crear**.

### <a name="part-3---configure-bloksec-as-an-identity-provider"></a>Parte 3: Configuración de BlokSec como proveedor de identidades

Para permitir que los usuarios inicien sesión utilizando la identidad descentralizada de BlokSec, debe definir BlokSec como un proveedor de reclamaciones con el que Azure AD B2C pueda comunicarse a través de un punto de conexión. El punto de conexión proporciona un conjunto de notificaciones que usa Azure AD B2C para comprobar que un usuario específico se ha autenticado mediante biometría, como huella digital o examen facial, tal y como está disponible en su dispositivo, lo que demuestra la identidad del usuario.

Puede definir BlokSec como proveedor de notificaciones. Para ello, agregue el elemento **ClaimsProvider** en el archivo de extensión de la directiva.

1. Abra `TrustFrameworkExtensions.xml`.

2. Busque el elemento **ClaimsProviders**. Si no existe, agréguelo debajo del elemento raíz.

3. Agregue un nuevo elemento **ClaimsProvider** tal como se muestra a continuación:

    ```xml
    <ClaimsProvider>
      <Domain>bloksec</Domain>
      <DisplayName>BlokSec</DisplayName>
      <TechnicalProfiles>
        <TechnicalProfile Id="BlokSec-OpenIdConnect">
          <DisplayName>BlokSec</DisplayName>
          <Description>Login with your BlokSec decentriled identity</Description>
          <Protocol Name="OpenIdConnect" />
          <Metadata>
            <Item Key="METADATA">https://api.bloksec.io/oidc/.well-known/openid-configuration</Item>
            <!-- Update the Client ID below to the BlokSec Application ID -->
            <Item Key="client_id">00000000-0000-0000-0000-000000000000</Item>
            <Item Key="response_types">code</Item>
            <Item Key="scope">openid profile email</Item>
            <Item Key="response_mode">form_post</Item>
            <Item Key="HttpBinding">POST</Item>
            <Item Key="UsePolicyInRedirectUri">false</Item>
            <Item Key="DiscoverMetadataByTokenIssuer">true</Item>
            <Item Key="ValidTokenIssuerPrefixes">https://api.bloksec.io/oidc</Item>
          </Metadata>
          <CryptographicKeys>
            <Key Id="client_secret" StorageReferenceId="B2C_1A_BlokSecAppSecret" />
          </CryptographicKeys>
          <OutputClaims>
            <OutputClaim ClaimTypeReferenceId="issuerUserId" PartnerClaimType="sub" />
            <OutputClaim ClaimTypeReferenceId="displayName" PartnerClaimType="name" />
            <OutputClaim ClaimTypeReferenceId="givenName" PartnerClaimType="given_name" />
            <OutputClaim ClaimTypeReferenceId="surName" PartnerClaimType="family_name" />
            <OutputClaim ClaimTypeReferenceId="email" PartnerClaimType="email" />
            <OutputClaim ClaimTypeReferenceId="authenticationSource" DefaultValue="socialIdpAuthentication" AlwaysUseDefaultValue="true" />
            <OutputClaim ClaimTypeReferenceId="identityProvider" PartnerClaimType="iss" />
          </OutputClaims>
          <OutputClaimsTransformations>
            <OutputClaimsTransformation ReferenceId="CreateRandomUPNUserName" />
            <OutputClaimsTransformation ReferenceId="CreateUserPrincipalName" />
            <OutputClaimsTransformation ReferenceId="CreateAlternativeSecurityId" />
            <OutputClaimsTransformation ReferenceId="CreateSubjectClaimFromAlternativeSecurityId" />
          </OutputClaimsTransformations>
          <UseTechnicalProfileForSessionManagement ReferenceId="SM-SocialLogin" />
        </TechnicalProfile>
      </TechnicalProfiles>
    </ClaimsProvider>
    ```

4. Establezca **client_id** en el identificador de la aplicación desde el registro de aplicación.

5. Guarde el archivo.

### <a name="part-4---add-a-user-journey"></a>Parte 4: Adición de un recorrido del usuario

En este momento, el proveedor de identidades ya se ha configurado, pero no está disponible en ninguna de las pantallas de inicio de sesión. Si no tiene su propio recorrido del usuario personalizado, cree un duplicado de un recorrido del usuario de la plantilla existente; de lo contrario, continúe con el paso siguiente.  

1. Abra el archivo `TrustFrameworkBase.xml` desde el paquete de iniciación.

2. Busque y copie todo el contenido del elemento **UserJourneys** que incluya el id.= `SignUpOrSignIn`.

3. Abra el `TrustFrameworkExtensions.xml` y localice el elemento **UserJourneys**. Si el elemento no existe, agréguelo.

4. Pegue todo el contenido del elemento **UserJourney** que ha copiado como elemento secundario del elemento **UserJourneys**.

5. Cambie el identificador del recorrido del usuario. Por ejemplo, id.=`CustomSignUpSignIn`.  

### <a name="part-5---add-the-identity-provider-to-a-user-journey"></a>Parte 5: Adición del proveedor de identidades a un recorrido del usuario

Ahora que tiene un recorrido del usuario, agregue el nuevo proveedor de identidades al recorrido del usuario. En primer lugar, agregue un botón de inicio de sesión y, después, vincule el botón a una acción. La acción es el perfil técnico que creó anteriormente.  

1. Busque el elemento del paso de orquestación que incluya Tipo=`CombinedSignInAndSignUp` o Tipo=`ClaimsProviderSelection`en el recorrido del usuario. Normalmente es el primer paso de orquestación. El elemento **ClaimsProviderSelections** contiene una lista de proveedores de identidades con los que un usuario puede iniciar sesión. El orden de los elementos controla el orden de los botones de inicio de sesión que se presentan al usuario. Agregue un elemento XML **ClaimsProviderSelection**. Establezca el valor de **TargetClaimsExchangeId** en un nombre descriptivo.

2. En el paso de orquestación siguiente, agregue un elemento **ClaimsExchange**. Establezca el **id.** en el valor del id. de intercambio de notificaciones de destino. Cambie el valor de **TechnicalProfileReferenceId** para el identificador del perfil técnico que creó anteriormente.

En el siguiente código XML se muestran los dos primeros pasos de orquestación de un recorrido del usuario con el proveedor de identidades:

```xml
<OrchestrationStep Order="1" Type="CombinedSignInAndSignUp" ContentDefinitionReferenceId="api.signuporsignin">
  <ClaimsProviderSelections>
    ...
    <ClaimsProviderSelection TargetClaimsExchangeId="BlokSecExchange" />
  </ClaimsProviderSelections>
  ...
</OrchestrationStep>

<OrchestrationStep Order="2" Type="ClaimsExchange">
  ...
  <ClaimsExchanges>
    <ClaimsExchange Id="BlokSecExchange" TechnicalProfileReferenceId="BlokSec-OpenIdConnect" />
  </ClaimsExchanges>
</OrchestrationStep>
```

### <a name="part-6---configure-the-relying-party-policy"></a>Parte 6: Configuración de la directiva de usuario de confianza

La directiva de usuario de confianza, por ejemplo [SignUpSignIn.xml](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/blob/master/SocialAndLocalAccounts/SignUpOrSignin.xml), especifica el recorrido del usuario que ejecutará Azure AD B2C. Busque el elemento **DefaultUserJourney** en el usuario de confianza. Actualice **ReferenceId** para que coincida con el identificador del recorrido del usuario, en el que agregó el proveedor de identidades.

En el ejemplo siguiente, para el recorrido de usuario `CustomSignUpOrSignIn`, ReferenceId está establecido en `CustomSignUpOrSignIn`:  
```xml
<RelyingParty>
  <DefaultUserJourney ReferenceId="CustomSignUpSignIn" />
  ...
</RelyingParty>
```

### <a name="part-7---upload-the-custom-policy"></a>Parte 7: Carga de la directiva personalizada

1. Inicie sesión en [Azure Portal](https://portal.azure.com/#home).
1. Asegúrese de que usa el directorio que contiene el inquilino de Azure AD B2C. Seleccione el icono **Directorios y suscripciones** en la barra de herramientas del portal.
1. En la página **Configuración del portal | Directorios y suscripciones**, busque el directorio de Azure AD B2C en la lista **Nombre de directorio** y seleccione **Cambiar**.
1. En [Azure Portal](https://portal.azure.com/#home), busque **Azure AD B2C** y selecciónelo.
1. En Directivas, seleccione **Identity Experience Framework**.
Seleccione **Cargar directiva personalizada** y, a continuación, cargue los dos archivos de directivas que ha cambiado, en el siguiente orden: la directiva de extensiones, por ejemplo `TrustFrameworkExtensions.xml`, luego la directiva de usuarios de confianza, como `SignUpSignIn.xml`.

### <a name="part-8---test-your-custom-policy"></a>Parte 8: Prueba de la directiva personalizada

1. Seleccione la directiva de usuarios de confianza, por ejemplo `B2C_1A_signup_signin`.
1. En **Aplicación**, seleccione la aplicación web que [registró anteriormente](./tutorial-register-applications.md). La **dirección URL de respuesta** debe mostrar `https://jwt.ms`.
1. Seleccione el botón **Ejecutar ahora**.
1. En la página de registro o de inicio de sesión, seleccione **Google** para iniciar sesión con la cuenta de Google.

Si el proceso de inicio de sesión se completa correctamente, el explorador se redirige a `https://jwt.ms`, que muestra el contenido del token devuelto por Azure AD B2C.

## <a name="next-steps"></a>Pasos siguientes 

Para más información, consulte los artículos siguientes:

- [Directivas personalizadas de Azure AD B2C](./custom-policy-overview.md)

- [Introducción a las directivas personalizadas en Azure AD B2C](./tutorial-create-user-flows.md?pivots=b2c-custom-policy)

::: zone-end