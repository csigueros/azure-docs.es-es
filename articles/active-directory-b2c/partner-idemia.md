---
title: Configuración de IDEMIA con Azure Active Directory B2C (versión preliminar)
titleSuffix: Azure AD B2C
description: Obtenga información sobre cómo integrar la autenticación de Azure AD B2C con IDEMIA para que el usuario de confianza consuma identificadores móviles emitidos por IDEMIA o los Estados de EE. UU.
services: active-directory-b2c
author: gargi-sinha
manager: martinco
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 10/21/2021
ms.author: gasinh
ms.subservice: B2C
zone_pivot_groups: b2c-policy-type
ms.openlocfilehash: 27c83d2ade4552150793aa8fbe777e376a2ae762
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/02/2021
ms.locfileid: "131012885"
---
# <a name="tutorial-configure-idemia-with-azure-active-directory-b2c-for-relying-party-to-consume-idemia-or-us-state-issued-mobile-identity-credentials-preview"></a>Tutorial: Configuración de IDEMIA con Azure Active Directory B2C para que el usuario de confianza consuma credenciales de identidad móvil emitidas por IDEMIA o los Estados de EE. UU. (versión preliminar).
[!INCLUDE [active-directory-b2c-choose-user-flow-or-custom-policy](../../includes/active-directory-b2c-choose-user-flow-or-custom-policy.md)]

::: zone pivot="b2c-user-flow"

Esta característica está disponible solo para directivas personalizadas. En los pasos de configuración, elija **Directiva personalizada** en el selector anterior.

::: zone-end

::: zone pivot="b2c-custom-policy"

En este tutorial de ejemplo, aprenderá a integrar Azure Active Directory (Azure AD) B2C con [IDEMIA](https://www.idemia.com/). IDEMIA es un proveedor de autenticación sin contraseña que proporciona servicios basados en consentimiento en tiempo real con autenticación biométrica, como la identificación facial y la huella dactilar, lo que elimina el fraude y la reutilización de credenciales. El identificador móvil de IDEMIA permite a los ciudadanos beneficiarse de un documento de identidad digital de confianza emitido por el gobierno, como complemento a su documento de identidad físico. Esta aplicación se usa para comprobar la identidad mediante el uso de un PIN seleccionado por el usuario o una identificación táctil o facial. El identificador móvil permite a los ciudadanos controlar sus identidades, ya que les permite compartir solo la información necesaria para una transacción y habilita la protección contra fraudes.



## <a name="scenario-description"></a>Descripción del escenario

La integración de IDEMIA incluye los siguientes componentes:

- **Azure AD B2C**: el servidor de autorización, responsable de comprobar las credenciales del usuario, al que también se le conoce como proveedor de identidades (IdP).

- **IDEMIA mID**: proveedor de OpenID Connect (OIDC) configurado como [proveedor externo de Azure AD B2C](add-identity-provider.md).

- **[Aplicación mID de IDEMIA](https://idemia-mobile-id.com/)** : una identidad digital de confianza, emitida por el gobierno. El identificador móvil (mID) es una versión digital de su permiso de conducir o de su documento de identidad emitido por el Estado que reside en una aplicación de su teléfono. [IDEMIA](https://idemia-mobile-id.com/).

IDEMIA proporciona estos mID a muchas agencias gubernamentales de EE. UU. que administran a nivel estatal el registro de vehículos y las licencias de conducir.

El mID es la digitalización de un documento de identificación en un token de identidad móvil seguro que es muy portátil para la verificación y que actúa como índice para la autorización de **servicios de mID**. El servicio mID permite a tales agencias demostrar las identidades de los individuos por medio de la autenticación de los documentos de credenciales utilizando las licencias de conducir que ha emitido y los servicios de coincidencia de reconocimiento facial biométrico comparando las **fotos** que se toman los usuarios con las de las credenciales.  

Una vez creado, el mID se almacena en el teléfono móvil del usuario final como una **identidad en el perímetro** firmada digitalmente. Los usuarios finales pueden ahora utilizar esa credencial firmada para acceder a otros servicios donde para acceder es necesario demostrar la identidad, como la prueba de edad, la información financiera del cliente o cuentas en las que la seguridad es primordial.

La oferta a Microsoft es la compatibilidad de estos servicios como un usuario de confianza que usará un mID emitido por el Estado para proporcionar servicios mediante los atributos enviados por el propietario del mID.

En el diagrama siguiente se muestra la implementación para escenarios web o locales:

![Captura de pantalla que muestra la comprobación local](./media/partner-idemia/idemia-architecture-diagram.png)

| Paso | Descripción |
|:--------|:--------|
| 1. | El usuario visita la página de inicio de sesión de Azure AD B2C, que es la parte que responde en este caso en su dispositivo para realizar una transacción e inicia sesión a través de su aplicación mID. |
| 2. | Azure AD B2C requiere una comprobación de identidad y para ello redirige al usuario al enrutador de IDEMIA utilizando el flujo de código de autorización de OIDC.|
| 3. | El enrutador de IDEMIA envía un desafío biométrico a la aplicación móvil del usuario, incluidos todos los detalles de contexto de la solicitud de autenticación y autorización.|
| 4. | En función del nivel de seguridad necesario, es posible que el usuario deba proporcionar detalles adicionales: escribir su PIN o hacerse una foto en el momento, o ambas cosas.|
| 5. | La respuesta de autenticación final proporciona prueba de posesión, presencia y consentimiento. La respuesta se devuelve al enrutador de IDEMIA.|
| 6. | El enrutador de IDEMIA comprueba la información proporcionada por el usuario y responde a Azure AD B2C con el resultado de la autenticación.|
|7. | En función del resultado de la autenticación, se concede o se deniega el acceso al usuario. |

## <a name="onboard-with-idemia"></a>Incorporación con IDEMIA

Póngase en contacto con [IDEMIA](https://www.idemia.com/get-touch/) para solicitar una demostración. Al rellenar el formulario de solicitud, en el campo de mensaje indique que desea incorporarse con Azure AD B2C.

## <a name="integrate-idemia-with-azure-ad-b2c"></a>Integración de IDEMIA con Azure AD B2C

## <a name="prerequisites"></a>Requisitos previos

Para empezar, necesitará lo siguiente:

- Acceso a los usuarios finales que tienen una credencial de identidad móvil (mID) emitida por un Estado de EE. UU. - IDEMIA o, durante la fase de prueba, la aplicación de demostración de mID proporcionada por [IDEMIA](https://www.idemia.com/).

- Una suscripción de Azure AD. Si no tiene una, obtenga una  [cuenta gratuita](https://azure.microsoft.com/free/).

- Un  [inquilino de Azure AD B2C](tutorial-create-tenant.md)  vinculado a la suscripción de Azure.

- La aplicación web empresarial registrada en el inquilino de Azure AD B2C. Con fines de prueba puede configurar https://jwt.ms, una aplicación web propiedad de Microsoft que muestra el contenido descodificado de un token.

>[!NOTE]
>El contenido del token nunca sale del explorador.


### <a name="part-1---submit-a-relying-party-application-on-boarding-for-mid"></a>Parte 1: Envío de una incorporación de aplicación de usuario de confianza para mID

Como parte de la integración con IDEMIA, se le proporciona la siguiente información:

| Propiedad | Descripción |
|:---------|:----------|
| Nombre de la aplicación | Azure AD B2C o el nombre de aplicación deseado |
| Identificador del cliente | Este es el identificador único proporcionado por el IdP. |
| Secreto del cliente | Contraseña que la aplicación de usuario de confianza usará para autenticarse con el IdP de IDEMIA. |
| Punto de conexión de metadatos | Una dirección URL que apunta a un documento de configuración del emisor del token, que es un punto de conexión de configuración conocido de OpenID. |
|URI de redirección | `https://your-B2C-tenant-name.b2clogin.com/your-B2C-tenant-name.onmicrosoft.com/oauth2/authresp`<br>Por ejemplo, `https://fabrikam.b2clogin.com/fabrikam.onmicrosoft.com/oauth2/authresp`.<br><br>Si usa un dominio personalizado, escriba `https://your-domain-name/your-tenant-name.onmicrosoft.com/oauth2/authresp`.<br>Sustituye su-nombre-de-dominio por el dominio personalizado y su-nombre-de-inquilino por el nombre del inquilino. |
|URI de redireccionamiento posteriores al cierre de sesión | `https://your-B2C-tenant-name.b2clogin.com/your-B2C-tenant-name.onmicrosoft.com/{policy}/oauth2/v2.0/logout`<br>Envíe una solicitud de cierre de sesión. |

>[!NOTE]
>Necesitará el identificador de cliente y el secreto de cliente de IDEMIA más adelante para configurar el IdP en Azure AD B2C.

### <a name="part-2---create-a-policy-key"></a>Parte 2: Creación de una clave de directiva

Almacene el secreto de cliente de IDEMA que haya registrado previamente en el inquilino de Azure AD B2C.

1. Inicie sesión en [Azure Portal](https://portal.azure.com/).

2. Asegúrese de que usa el directorio que contiene el inquilino de Azure AD B2C. Seleccione el icono **Directorios y suscripciones** en la barra de herramientas del portal.

3. En la página **Configuración del portal | Directorios y suscripciones**, busque el directorio de Azure AD B2C en la lista **Nombre de directorio** y seleccione **Cambiar**.

4. Elija **Todos los servicios** en la esquina superior izquierda de Azure Portal, y busque y seleccione **Azure AD B2C**.

5. En la página de **introducción**, seleccione **Identity Experience Framework**.

6. Seleccione **Claves de directiva** y luego **Agregar**.

7. En **Opciones**, elija **Manual**.

8. Escriba un **nombre** para la clave de directiva. Por ejemplo, IdemiaAppSecret. Se agrega el prefijo B2C_1A_ automáticamente al nombre de la clave.

9. En **Secreto**, escriba el secreto de cliente que haya registrado previamente.

10. En **Uso de la clave**, seleccione **Firma**.

11. Seleccione **Crear**.

### <a name="part-3---configure-idemia-as-an-external-idp"></a>Parte 3: Configuración de IDEMIA como idP externo

Para permitir que los usuarios inicien sesión utilizando la identidad sin contraseña del identificador móvil de IDEMIA, debe definir IDEMIA como un proveedor de notificaciones con el que Azure AD B2C pueda comunicarse a través de un punto de conexión. El punto de conexión proporciona un conjunto de notificaciones que usa Azure AD B2C para comprobar que un usuario específico se ha autenticado mediante biometría, como huella digital o examen facial, tal y como está disponible en su dispositivo, lo que demuestra la identidad del usuario.
Puede definir IDEMIA como proveedor de notificaciones. Para ello, agregue el elemento **ClaimsProvider** en el archivo de extensión de la directiva.

```PowerShell
     <TechnicalProfile Id="Idemia-Oauth2">
          <DisplayName>IDEMIA</DisplayName>
          <Description>Login with your IDEMIA identity</Description>
          <Protocol Name="OAuth2" />
          <Metadata>
            <Item Key="METADATA">https://idp.XXXX.net/oxauth/.well-known/openid-configuration</Item>
            <!-- Update the Client ID below to the Application ID -->
            <Item Key="client_id">00000000-0000-0000-0000-000000000000</Item>
            <Item Key="response_types">code</Item>
            <Item Key="scope">openid id_basic mt_scope</Item>
            <Item Key="response_mode">form_post</Item>
            <Item Key="HttpBinding">POST</Item>
            <Item Key="UsePolicyInRedirectUri">false</Item>
            <Item Key="token_endpoint_auth_method">client_secret_basic</Item>
            <Item Key="ClaimsEndpoint">https://idp.XXXX.net/oxauth/restv1/userinfo</Item>
            <Item Key="ValidTokenIssuerPrefixes">https://login.microsoftonline.com/</Item>
          </Metadata>
          <CryptographicKeys>
            <Key Id="client_secret" StorageReferenceId="B2C_1A_IdemiaAppSecret" />
</CryptographicKeys>
          <InputClaims>
            <InputClaim ClaimTypeReferenceId="acr" PartnerClaimType="acr_values" DefaultValue="loa-2" />
          </InputClaims>
          <OutputClaims>
            <OutputClaim ClaimTypeReferenceId="issuerUserId" PartnerClaimType="sub" />
            <OutputClaim ClaimTypeReferenceId="tenantId" PartnerClaimType="tid" />
            <OutputClaim ClaimTypeReferenceId="givenName" PartnerClaimType="firstName1" />
            <OutputClaim ClaimTypeReferenceId="surName" PartnerClaimType="lastName1" />
            <OutputClaim ClaimTypeReferenceId="authenticationSource" DefaultValue="socialIdpAuthentication" AlwaysUseDefaultValue="true" />
            <OutputClaim ClaimTypeReferenceId="identityProvider" DefaultValue="idemia" />
            <OutputClaim ClaimTypeReferenceId="documentId" />
            <OutputClaim ClaimTypeReferenceId="address1" />
          </OutputClaims>
          <OutputClaimsTransformations>
            <OutputClaimsTransformation ReferenceId="CreateRandomUPNUserName" />
            <OutputClaimsTransformation ReferenceId="CreateUserPrincipalName" />
            <OutputClaimsTransformation ReferenceId="CreateAlternativeSecurityId" />
            <OutputClaimsTransformation ReferenceId="CreateSubjectClaimFromAlternativeSecurityId" />
          </OutputClaimsTransformations>
          <UseTechnicalProfileForSessionManagement ReferenceId="SM-SocialLogin" />
        </TechnicalProfile>
           
```

Establezca client_id en el identificador de la aplicación desde el registro de aplicación.

|Propiedad | Descripción|
|:------|:-------|
|Ámbito| Para OpenID Connect (OIDC), el requisito mínimo es que el parámetro scope se establezca en **openid**. Se pueden anexar ámbitos adicionales como una lista delimitada por espacios.|
|redirect_uri | Esto define dónde devuelve el agente de usuario el código de autorización a Azure AD B2C.|
|response_type| Para el flujo de código de autorización, se establece en **code**.|
|acr_values| Este parámetro controla los métodos de autenticación que el usuario debe realizar durante el proceso de autenticación. |

Debe seleccionarse uno de los valores siguientes:

|Valor del parámetro| Efecto en el proceso de autenticación del usuario |
|:------|:-------|
|`loa-2`| Solo autenticación multifactor de Azure AD basada en el cifrado|
|`loa-3`| Autenticación multifactor de Azure AD basada en el cifrado más un factor adicional|
|`loa-4`| Autenticación multifactor de Azure AD basada en el cifrado con el requisito de que el usuario también debe realizar la autenticación biométrica y basada en PIN. |

El punto de conexión **/userinfo** proporciona las notificaciones para los ámbitos solicitados en la solicitud de autorización. Para **<mt_scope>** incluye notificaciones como el nombre, los apellidos o el número de permiso de conducir, entre otros.
Las notificaciones establecidas para cualquier ámbito determinado se publican en la sección **scope_to_claims_mapping** de la API de detección.
Azure AD B2C solicita notificaciones desde el punto de conexión de notificaciones y devuelve esas notificaciones en el elemento OutputClaims. Es posible que tenga que asignar el nombre de la notificación definida en la directiva al nombre definido en el IdP, asegurándose de definir el tipo de notificación en el [elemento ClaimSchema](claimsschema.md):

```PowerShell
<ClaimType Id="documentId">
     <DisplayName>documentId</DisplayName>
     <DataType>string</DataType>
</ClaimType>
<ClaimType Id="address1">
     <DisplayName>address</DisplayName>
     <DataType>string</DataType>
</ClaimType>
```

### <a name="part-4---add-a-user-journey"></a>Parte 4: Adición de un recorrido del usuario

En este momento, el IdP ya se ha configurado, pero no está disponible en ninguna de las pantallas de inicio de sesión. Si no tiene su propio recorrido del usuario personalizado, cree un duplicado de un recorrido del usuario de la plantilla existente; de lo contrario, continúe con el paso siguiente.

1. Abra el archivo `TrustFrameworkBase.xml` desde el paquete de iniciación.

2. Busque y copie todo el contenido del elemento **UserJourneys** que incluya `ID=SignUpOrSignIn`.

3. Abra el `TrustFrameworkExtensions.xml` y localice el elemento **UserJourneys**. Si el elemento no existe, agréguelo.

4. Pegue todo el contenido del elemento **UserJourney** que ha copiado como elemento secundario del elemento UserJourneys.

5. Cambie el identificador del recorrido del usuario. Por ejemplo, `ID=CustomSignUpSignIn`.

### <a name="part-5---add-the-idp-to-a-user-journey"></a>Parte 5: Adición del IdP a un recorrido del usuario

Ahora que tiene un recorrido del usuario, agregue el nuevo IdP a ese recorrido del usuario. En primer lugar, agregue un botón de inicio de sesión y, después, vincule el botón a una acción. La acción es el perfil técnico que creó anteriormente.

1. Busque el elemento del paso de orquestación que incluya Tipo=`CombinedSignInAndSignUp` o Tipo=`ClaimsProviderSelection`en el recorrido del usuario. Normalmente es el primer paso de orquestación. El elemento **ClaimsProviderSelections** contiene una lista de IdP con los que un usuario puede iniciar sesión. El orden de los elementos controla el orden de los botones de inicio de sesión que se presentan al usuario. Agregue un elemento XML **ClaimsProviderSelection**. Establezca el valor de **TargetClaimsExchangeId** en un nombre descriptivo.

2. En el paso de orquestación siguiente, agregue un elemento **ClaimsExchange**. Establezca el **id.** en el valor del id. de intercambio de notificaciones de destino. Cambie el valor de **TechnicalProfileReferenceId** para el identificador del perfil técnico que creó anteriormente.

En el siguiente código XML se muestran los dos primeros pasos de orquestación de un recorrido del usuario con el IdP:

```xml
<OrchestrationStep Order="1" Type="CombinedSignInAndSignUp" ContentDefinitionReferenceId="api.signuporsignin">
  <ClaimsProviderSelections>
    ...
    <ClaimsProviderSelection TargetClaimsExchangeId="IdemiaExchange" />
  </ClaimsProviderSelections>
  ...
</OrchestrationStep>

<OrchestrationStep Order="2" Type="ClaimsExchange">
  ...
  <ClaimsExchanges>
    <ClaimsExchange Id="IdemiaExchange" TechnicalProfileReferenceId="Idemia-Oauth2" />
  </ClaimsExchanges>
</OrchestrationStep>
```

### <a name="part-6---configure-the-relying-party-policy"></a>Parte 6: Configuración de la directiva de usuario de confianza

La directiva de usuario de confianza, por ejemplo [SignUpSignIn.xml](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/blob/master/SocialAndLocalAccounts/SignUpOrSignin.xml), especifica el recorrido del usuario que ejecutará Azure AD B2C. Busque el elemento **DefaultUserJourney** en el usuario de confianza. Actualice **ReferenceId** para que coincida con el identificador del recorrido del usuario, en el que agregó el IdP.

En el ejemplo siguiente, para el recorrido de usuario `CustomSignUpOrSignIn`, **ReferenceId** está establecido en `CustomSignUpOrSignIn`.
 
```xml
<RelyingParty>
  <DefaultUserJourney ReferenceId="CustomSignUpSignIn" />
  ...
</RelyingParty>
```

### <a name="part-7---upload-the-custom-policy"></a>Parte 7: Carga de la directiva personalizada

1. Inicie sesión en [Azure Portal](https://portal.azure.com/#home).

2. Asegúrese de que usa el directorio que contiene el inquilino de Azure AD B2C. Seleccione el icono **Directorios y suscripciones** en la barra de herramientas del portal.

3. En la página **Configuración del portal | Directorios y suscripciones**, busque el directorio de Azure AD B2C en la lista **Nombre de directorio** y seleccione **Cambiar**.

4. En [Azure Portal](https://portal.azure.com/#home), busque **Azure AD B2C** y selecciónelo.

5. En Directivas, seleccione **Identity Experience Framework**.

Seleccione **Cargar directiva personalizada** y, a continuación, cargue los dos archivos de directivas que ha cambiado, en el siguiente orden: la directiva de extensiones, por ejemplo `TrustFrameworkExtensions.xml`, luego la directiva de usuarios de confianza, como `SignUpSignIn.xml`.

### <a name="part-8---test-your-custom-policy"></a>Parte 8: Prueba de la directiva personalizada

1. Seleccione la directiva de usuarios de confianza, por ejemplo `B2C_1A_signup_signin`.

2. En **Aplicación**, seleccione la aplicación web que [registró anteriormente](./tutorial-register-applications.md). La **dirección URL de respuesta** debe mostrar `https://jwt.ms`.

3. Seleccione el botón **Ejecutar ahora**.

4. En la página de registro o inicio de sesión, seleccione **IDEMIA** para iniciar sesión con una credencial de identidad móvil (mID) emitida por un Estado de EE. UU. - IDEMIA.

Si el proceso de inicio de sesión se completa correctamente, el explorador se redirige a `https://jwt.ms`, que muestra el contenido del token devuelto por Azure AD B2C.


## <a name="next-steps"></a>Pasos siguientes

Para más información, consulte los artículos siguientes:

- [Directivas personalizadas de Azure AD B2C](custom-policy-overview.md)

- [Introducción a las directivas personalizadas en Azure AD B2C](tutorial-create-user-flows.md?pivots=b2c-custom-policy)

- [Más información sobre el mID de IDEMIA](https://www.idemia.com/mobile-id)

::: zone-end
