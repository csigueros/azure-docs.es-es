---
title: Configuración de Azure Active Directory B2C como IdP de SAML para las aplicaciones
title-suffix: Azure Active Directory B2C
description: Obtenga información sobre cómo configurar Azure Active Directory B2C para proporcionar aserciones de protocolo SAML a las aplicaciones (proveedores de servicios).
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 09/20/2021
ms.author: mimart
ms.subservice: B2C
ms.custom: fasttrack-edit
zone_pivot_groups: b2c-policy-type
ms.openlocfilehash: 673835a3e3112bf433faeba815e65c6203dd9ce8
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/24/2021
ms.locfileid: "128603816"
---
# <a name="register-a-saml-application-in-azure-ad-b2c"></a>Registrar una aplicación SAML en Azure AD B2C

En este artículo, aprenderá a conectar las aplicaciones de Lenguaje de marcado de aserción de seguridad (SAML) (proveedores de servicios) a Azure Active Directory B2C (Azure AD B2C) para la autenticación.

[!INCLUDE [active-directory-b2c-choose-user-flow-or-custom-policy](../../includes/active-directory-b2c-choose-user-flow-or-custom-policy.md)]

::: zone pivot="b2c-user-flow"

[!INCLUDE [active-directory-b2c-limited-to-custom-policy](../../includes/active-directory-b2c-limited-to-custom-policy.md)]

::: zone-end

::: zone pivot="b2c-custom-policy"

## <a name="overview"></a>Información general

Las organizaciones que usan Azure AD B2C como solución de administración de acceso e identidad de clientes pueden requerir la interacción con aplicaciones que se autentican mediante el protocolo SAML. En el diagrama siguiente se muestra cómo Azure AD B2C actúa como *proveedor de identidades* (IdP) para lograr el inicio de sesión único (SSO) con aplicaciones basadas en SAML.

![Diagrama con Azure Active Directory B2C como proveedor de identidades a la izquierda y como proveedor de servicios a la derecha.](media/saml-service-provider/saml-service-provider-integration.png)

1. La aplicación crea una solicitud de autenticación de SAML que se envía al punto de conexión de inicio de sesión de SAML de Azure AD B2C.
2. El usuario puede usar una cuenta local de Azure AD B2C o cualquier otro proveedor de identidades federado (si está configurado) para autenticarse.
3. Si el usuario inicia sesión con un proveedor de identidades federado, se envía una respuesta de token a Azure AD B2C.
4. Azure AD B2C genera una aserción de SAML y la envía a la aplicación.

Vea este vídeo para aprender a integrar aplicaciones SAML con Azure AD B2C. 

>[!Video https://www.youtube.com/embed/r2TIVBCm7v4]

## <a name="prerequisites"></a>Prerrequisitos

Para configurar el escenario de este artículo, necesita lo siguiente:

* La directiva personalizada *SocialAndLocalAccounts* de un paquete de inicio de directivas personalizadas. Realice los pasos del artículo [Introducción a las directivas personalizadas en Azure AD B2C](tutorial-create-user-flows.md?pivots=b2c-custom-policy). 
* Conocimientos básicos del protocolo SAML y familiaridad con la implementación de SAML de la aplicación.
* Una aplicación web configurada como una aplicación SAML. Esta debe poder enviar solicitudes de autenticación de SAML y recibir, descodificar y comprobar las respuestas SAML de Azure AD B2C. La aplicación SAML también se conoce como la aplicación de usuario de confianza o el proveedor de servicios. 
* El documento XML o el *punto de conexión de metadatos* de SAML disponible públicamente de la aplicación SAML.
* Un [inquilino de Azure AD B2C](tutorial-create-tenant.md).

Si aún no tiene una aplicación SAML y un punto de conexión de metadatos asociado, puede usar la [aplicación SAML de ejemplo][samltest] que hemos puesto a su disposición para las pruebas.

[!INCLUDE [active-directory-b2c-https-cipher-tls-requirements](../../includes/active-directory-b2c-https-cipher-tls-requirements.md)]

## <a name="set-up-certificates"></a>Configurar los certificados

Para crear una relación de confianza entre la aplicación y Azure AD B2C, ambos servicios deben poder crear y validar las firmas de los demás. Configure certificados X509 en la aplicación y en Azure AD B2C.

**Certificados de aplicación**

| Uso | Obligatorio | Descripción |
| --------- | -------- | ----------- |
| Firma de solicitud SAML  | No | Certificado con una clave privada almacenada en la aplicación web. La aplicación usa el certificado para firmar las solicitudes SAML enviadas a Azure AD B2C. La aplicación web debe exponer la clave pública a través de su punto de conexión de metadatos de SAML. Azure AD B2C valida la firma de la solicitud SAML mediante la clave pública de los metadatos de la aplicación.|
| Cifrado de aserciones de SAML  | No | Certificado con una clave privada almacenada en la aplicación web. La aplicación web debe exponer la clave pública a través de su punto de conexión de metadatos de SAML. Azure AD B2C puede cifrar las aserciones en la aplicación mediante la clave pública. La aplicación utiliza la clave privada para descifrar la aserción.|

**Certificados de Azure AD B2C**

| Uso | Obligatorio | Descripción |
| --------- | -------- | ----------- |
| Firma de respuesta SAML | Sí  | Certificado con una clave privada almacenada en Azure AD B2C. Azure AD B2C usa este certificado para firmar la respuesta SAML enviada a la aplicación. La aplicación lee la clave pública de metadatos de Azure AD B2C para validar la firma de la respuesta SAML. |
| Firma de aserciones SAML | Sí | Certificado con una clave privada almacenada en Azure AD B2C. Azure AD B2C usa este certificado para firmar la parte `<saml:Assertion>` de la respuesta SAML.  |

En un entorno de producción, se recomienda usar certificados emitidos por una entidad de certificación pública. Sin embargo, también puede completar este procedimiento con certificados autofirmados.

### <a name="create-a-policy-key"></a>Creación de una clave de directiva

Para tener una relación de confianza entre la aplicación y Azure AD B2C, cree un certificado de firma para la respuesta SAML. Azure AD B2C usa este certificado para firmar la respuesta SAML enviada a la aplicación. La aplicación lee la clave pública de metadatos de Azure AD B2C para validar la firma de la respuesta SAML. 

> [!TIP]
> Puede usar esta clave de directiva para otros fines, como firmar la [aserción de SAML](saml-service-provider-options.md#check-the-saml-assertion-signature). 

### <a name="obtain-a-certificate"></a>Obtener un certificado

[!INCLUDE [active-directory-b2c-create-self-signed-certificate](../../includes/active-directory-b2c-create-self-signed-certificate.md)]

### <a name="upload-the-certificate"></a>Carga del certificado

Debe almacenar el certificado en el inquilino de Azure AD B2C.

1. Inicie sesión en [Azure Portal](https://portal.azure.com/).
1. Asegúrese de que usa el directorio que contiene el inquilino de Azure AD B2C. Seleccione el icono **Directorios y suscripciones** en la barra de herramientas del portal.
1. En la página **Configuración del portal | Directorios y suscripciones**, busque el directorio de Azure AD B2C en la lista **Nombre de directorio** y seleccione **Cambiar**.
1. Seleccione **Todos los servicios** en la esquina superior izquierda de Azure Portal y, a continuación, busque y seleccione **Azure AD B2C**.
1. En la página de **introducción**, seleccione **Identity Experience Framework**.
1. Seleccione **Claves de directiva** y, luego, **Agregar**.
1. En **Opciones**, seleccione **Cargar**.
1. En **Nombre**, escriba un nombre para la clave de directiva. Por ejemplo, escriba **SamlIdpCert**. Se agregará el prefijo **B2C_1A_** automáticamente al nombre de la clave.
1. Busque el archivo .pfx de certificado con la clave privada y selecciónelo.
1. Seleccione **Crear**.

## <a name="enable-your-policy-to-connect-with-a-saml-application"></a>Habilitación de la directiva para conectarse con una aplicación SAML

Para conectarse a la aplicación SAML, Azure AD B2C debe ser capaz de crear respuestas SAML.

Abra *SocialAndLocalAccounts\TrustFrameworkExtensions.xml* en el paquete de inicio de directivas personalizadas.

Busque la sección `<ClaimsProviders>` y agregue el siguiente fragmento de código XML para implementar el generador de respuestas SAML:

```xml
<ClaimsProvider>
  <DisplayName>Token Issuer</DisplayName>
  <TechnicalProfiles>

    <!-- SAML Token Issuer technical profile -->
    <TechnicalProfile Id="Saml2AssertionIssuer">
      <DisplayName>Token Issuer</DisplayName>
      <Protocol Name="SAML2"/>
      <OutputTokenFormat>SAML2</OutputTokenFormat>
      <Metadata>
        <Item Key="IssuerUri">https://issuerUriMyAppExpects</Item>
      </Metadata>
      <CryptographicKeys>
        <Key Id="SamlAssertionSigning" StorageReferenceId="B2C_1A_SamlIdpCert"/>
        <Key Id="SamlMessageSigning" StorageReferenceId="B2C_1A_SamlIdpCert"/>
      </CryptographicKeys>
      <InputClaims/>
      <OutputClaims/>
      <UseTechnicalProfileForSessionManagement ReferenceId="SM-Saml-issuer"/>
    </TechnicalProfile>

    <!-- Session management technical profile for SAML-based tokens -->
    <TechnicalProfile Id="SM-Saml-issuer">
      <DisplayName>Session Management Provider</DisplayName>
      <Protocol Name="Proprietary" Handler="Web.TPEngine.SSO.SamlSSOSessionProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null"/>
    </TechnicalProfile>

  </TechnicalProfiles>
</ClaimsProvider>
```

#### <a name="configure-the-issuer-uri-of-the-saml-response"></a>Configuración del URI del emisor de la respuesta SAML

Puede cambiar el valor del elemento de metadatos `IssuerUri` en el perfil técnico del emisor de tokens SAML. Este cambio se reflejará en el atributo `issuerUri` devuelto en la respuesta SAML desde Azure AD B2C. Configure la aplicación para que acepte el mismo valor `IssuerUri` durante la validación de la respuesta SAML.

```xml
<ClaimsProvider>
  <DisplayName>Token Issuer</DisplayName>
  <TechnicalProfiles>
    <!-- SAML Token Issuer technical profile -->
    <TechnicalProfile Id="Saml2AssertionIssuer">
      <DisplayName>Token Issuer</DisplayName>
      <Protocol Name="SAML2"/>
      <OutputTokenFormat>SAML2</OutputTokenFormat>
      <Metadata>
        <Item Key="IssuerUri">https://issuerUriMyAppExpects</Item>
      </Metadata>
      ...
    </TechnicalProfile>
```

## <a name="configure-your-policy-to-issue-a-saml-response"></a>Configuración de la directiva para emitir una respuesta SAML

Ahora que la directiva puede crear respuestas SAML, debe configurarla para emitir una respuesta SAML en lugar de la respuesta JWT predeterminada a la aplicación.

### <a name="create-a-sign-up-or-sign-in-policy-configured-for-saml"></a>Creación de una directiva de registro o de inicio de sesión configurada para SAML

1. Cree una copia del archivo *SignUpOrSignin.xml* en el directorio de trabajo del paquete de inicio y guárdelo con un nuevo nombre. En este artículo se usa *SignUpOrSigninSAML.xml* como ejemplo. Este archivo es el archivo de directiva del usuario de confianza. Está configurado para emitir una respuesta JWT de forma predeterminada.

1. Abra el archivo *SignUpOrSigninSAML.xml* en el editor que prefiera.

1. Cambie los valores `PolicyId` y `PublicPolicyUri` de la directiva por `_B2C_1A_signup_signin_saml_` y `http://<tenant-name>.onmicrosoft.com/B2C_1A_signup_signin_saml`.

    ```xml
    <TrustFrameworkPolicy
    xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
    xmlns:xsd="http://www.w3.org/2001/XMLSchema"
    xmlns="http://schemas.microsoft.com/online/cpim/schemas/2013/06"
    PolicySchemaVersion="0.3.0.0"
    TenantId="tenant-name.onmicrosoft.com"
    PolicyId="B2C_1A_signup_signin_saml"
    PublicPolicyUri="http://<tenant-name>.onmicrosoft.com/B2C_1A_signup_signin_saml">
    ```

1. Al final del recorrido del usuario, Azure AD B2C contiene un paso `SendClaims`. Este paso hace referencia al perfil técnico del emisor de tokens. Para emitir una respuesta SAML en lugar de la respuesta JWT predeterminada, modifique el paso `SendClaims` para que haga referencia al nuevo perfil técnico del emisor de tokens SAML, `Saml2AssertionIssuer`.

Agregue el siguiente fragmento de código XML justo delante del elemento `<RelyingParty>`. Este XML sobrescribe el paso 7 de la orquestación del recorrido del usuario _SignUpOrSignIn_. 

Si ha empezado desde una carpeta diferente en el paquete de inicio o ha personalizado el recorrido del usuario mediante la adición o eliminación de pasos de orquestación, asegúrese de que el número del elemento `order` corresponde al número especificado en el paso de recorrido del usuario para el emisor de tokens. Por ejemplo, en las otras carpetas del paquete de inicio, el número de paso correspondiente es 4 para `LocalAccounts`, 6 para `SocialAccounts` y 9 para `SocialAndLocalAccountsWithMfa`.

```xml
<UserJourneys>
  <UserJourney Id="SignUpOrSignIn">
    <OrchestrationSteps>
      <OrchestrationStep Order="7" Type="SendClaims" CpimIssuerTechnicalProfileReferenceId="Saml2AssertionIssuer"/>
    </OrchestrationSteps>
  </UserJourney>
</UserJourneys>
```

El elemento de usuario de confianza determina el protocolo que utiliza la aplicación. El valor predeterminado es `OpenId`. El elemento `Protocol` debe cambiarse a `SAML`. Las notificaciones de salida crearán la asignación de notificaciones a la aserción de SAML.

Reemplace el elemento `<TechnicalProfile>` completo en el elemento `<RelyingParty>` por el siguiente XML de perfil técnico. Actualice `tenant-name` con el nombre del inquilino de Azure AD B2C.

```xml
    <TechnicalProfile Id="PolicyProfile">
      <DisplayName>PolicyProfile</DisplayName>
      <Protocol Name="SAML2"/>
      <OutputClaims>
        <OutputClaim ClaimTypeReferenceId="displayName" />
        <OutputClaim ClaimTypeReferenceId="givenName" />
        <OutputClaim ClaimTypeReferenceId="surname" />
        <OutputClaim ClaimTypeReferenceId="email" DefaultValue="" />
        <OutputClaim ClaimTypeReferenceId="identityProvider" DefaultValue="" />
        <OutputClaim ClaimTypeReferenceId="objectId" PartnerClaimType="objectId"/>
      </OutputClaims>
      <SubjectNamingInfo ClaimType="objectId" ExcludeAsClaim="true"/>
    </TechnicalProfile>
```

El archivo de directiva final del usuario de confianza debe tener un aspecto similar al código XML siguiente:

```xml
<?xml version="1.0" encoding="UTF-8" standalone="yes"?>
<TrustFrameworkPolicy
  xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
  xmlns:xsd="http://www.w3.org/2001/XMLSchema"
  xmlns="http://schemas.microsoft.com/online/cpim/schemas/2013/06"
  PolicySchemaVersion="0.3.0.0"
  TenantId="contoso.onmicrosoft.com"
  PolicyId="B2C_1A_signup_signin_saml"
  PublicPolicyUri="http://contoso.onmicrosoft.com/B2C_1A_signup_signin_saml">

  <BasePolicy>
    <TenantId>contoso.onmicrosoft.com</TenantId>
    <PolicyId>B2C_1A_TrustFrameworkExtensions</PolicyId>
  </BasePolicy>

  <UserJourneys>
    <UserJourney Id="SignUpOrSignIn">
      <OrchestrationSteps>
        <OrchestrationStep Order="7" Type="SendClaims" CpimIssuerTechnicalProfileReferenceId="Saml2AssertionIssuer"/>
      </OrchestrationSteps>
    </UserJourney>
  </UserJourneys>

  <RelyingParty>
    <DefaultUserJourney ReferenceId="SignUpOrSignIn" />
    <TechnicalProfile Id="PolicyProfile">
      <DisplayName>PolicyProfile</DisplayName>
      <Protocol Name="SAML2"/>
      <OutputClaims>
        <OutputClaim ClaimTypeReferenceId="displayName" />
        <OutputClaim ClaimTypeReferenceId="givenName" />
        <OutputClaim ClaimTypeReferenceId="surname" />
        <OutputClaim ClaimTypeReferenceId="email" DefaultValue="" />
        <OutputClaim ClaimTypeReferenceId="identityProvider" DefaultValue="" />
        <OutputClaim ClaimTypeReferenceId="objectId" PartnerClaimType="objectId"/>
      </OutputClaims>
      <SubjectNamingInfo ClaimType="objectId" ExcludeAsClaim="true"/>
    </TechnicalProfile>
  </RelyingParty>
</TrustFrameworkPolicy>
```

> [!NOTE]
> Puede seguir el mismo proceso para implementar otros tipos de flujos de usuario (por ejemplo, flujos de inicio de sesión, restablecimiento de contraseña o edición de perfiles).

### <a name="upload-your-policy"></a>Carga de la directiva

Guarde los cambios y cargue los nuevos archivos de directivas *TrustFrameworkExtensions.xml* y *SignUpOrSigninSAML.xml* en Azure Portal.

### <a name="test-the-azure-ad-b2c-idp-saml-metadata"></a>Prueba de los metadatos de SAML del IdP de Azure AD B2C

Una vez cargados los archivos de directivas, Azure AD B2C usa la información de configuración para generar el documento de metadatos de SAML del proveedor de identidades que usará la aplicación. El documento de metadatos de SAML contiene las ubicaciones de los servicios, como los métodos de inicio y cierre de sesión, y los certificados.

Los metadatos de la directiva de Azure AD B2C están disponibles en la siguiente dirección URL:

`https://<tenant-name>.b2clogin.com/<tenant-name>.onmicrosoft.com/<policy-name>/samlp/metadata`

Reemplace `<tenant-name>` por el nombre del inquilino de Azure AD B2C. Reemplace `<policy-name>` por el nombre (id.) de la directiva. Veamos un ejemplo:

`https://contoso.b2clogin.com/contoso.onmicrosoft.com/B2C_1A_signup_signin_saml/samlp/metadata`

## <a name="register-your-saml-application-in-azure-ad-b2c"></a>Registro de una aplicación SAML en Azure AD B2C

Para que Azure AD B2C confíe en la aplicación, cree un registro de aplicación de Azure AD B2C. El registro contiene información de configuración, como el punto de conexión de metadatos de la aplicación.

1. Inicie sesión en [Azure Portal](https://portal.azure.com).
1. Asegúrese de que usa el directorio que contiene el inquilino de Azure AD B2C. Seleccione el icono **Directorios y suscripciones** en la barra de herramientas del portal.
1. En la página **Configuración del portal | Directorios y suscripciones**, busque el directorio de Azure AD B2C en la lista **Nombre de directorio** y seleccione **Cambiar**.
1. En el menú de la izquierda, seleccione **Azure AD B2C**. O bien, seleccione **Todos los servicios** y, luego, busque y seleccione **Azure AD B2C**.
1. Seleccione **Registros de aplicaciones** y luego **Nuevo registro**.
1. Escriba un **Nombre** para la aplicación. Por ejemplo, escriba **SAMLApp1**.
1. En **Tipos de cuenta admitidos**, seleccione **Solo las cuentas de este directorio organizativo**.
1. En **URI de redirección**, seleccione **Web** y escriba `https://localhost`. Modificará este valor más adelante en el manifiesto del registro de aplicación.
1. Seleccione **Registrar**.

### <a name="configure-your-application-in-azure-ad-b2c"></a>Configuración de la aplicación en Azure AD B2C

En el caso de las aplicaciones SAML, debe configurar varias propiedades en el manifiesto del registro de aplicación.

1. En [Azure Portal](https://portal.azure.com), vaya al registro de aplicación que creó en la sección anterior.
1. En **Administrar**, seleccione **Manifiesto** para abrir el editor de manifiestos. A continuación, modifique las propiedades que se describen en las secciones siguientes.

#### <a name="add-the-identifier"></a>Adición del identificador

Cuando la aplicación SAML realiza una solicitud a Azure AD B2C, la solicitud de autenticación de SAML incluye un atributo `Issuer`. El valor de este atributo suele coincidir con el valor `entityID` de los metadatos de la aplicación. Azure AD B2C usa este valor para buscar el registro de aplicación en el directorio y leer la configuración. Para que esta búsqueda se realice correctamente, el valor `identifierUri` del registro de aplicación se debe rellenar con un valor que coincida con el atributo `Issuer`.

En el manifiesto de registro, busque el parámetro `identifierURIs` y agregue el valor adecuado. Este valor será el mismo que el configurado en las solicitudes de autenticación de SAML para `EntityId` en la aplicación y el valor `entityID` en los metadatos de la aplicación.

En el ejemplo siguiente se muestra el valor `entityID` de los metadatos de SAML:

```xml
<EntityDescriptor ID="id123456789" entityID="https://samltestapp2.azurewebsites.net" validUntil="2099-12-31T23:59:59Z" xmlns="urn:oasis:names:tc:SAML:2.0:metadata">
```

La propiedad `identifierUris` solo aceptará direcciones URL en el dominio `tenant-name.onmicrosoft.com`.

```json
"identifierUris":"https://samltestapp2.azurewebsites.net",
```

#### <a name="share-the-applications-metadata-with-azure-ad-b2c"></a>Uso compartido de los metadatos de la aplicación con Azure AD B2C

Una vez que el registro de aplicación se ha cargado por su valor `identifierUri`, Azure AD B2C usa los metadatos de la aplicación para validar la solicitud de autenticación de SAML y determinar cómo responder.

Se recomienda que la aplicación exponga un punto de conexión de metadatos accesible públicamente.

Si hay propiedades especificadas *tanto* en la dirección URL de metadatos de SAML como en el manifiesto del registro de aplicación, se *combinan*. Las propiedades especificadas en la dirección URL de metadatos se procesan primero y tienen prioridad.

Mediante el uso de la aplicación de prueba de SAML como ejemplo, usaría el siguiente valor para `samlMetadataUrl` en el manifiesto de aplicación:

```json
"samlMetadataUrl":"https://samltestapp2.azurewebsites.net/Metadata",
```

#### <a name="override-or-set-the-assertion-consumer-url-optional"></a>Invalidación o establecimiento de la dirección URL del consumidor de aserciones (opcional)

Puede configurar la dirección URL de respuesta a la que Azure AD B2C envía respuestas SAML. Las direcciones URL de respuesta se pueden configurar en el manifiesto de aplicación. Esta configuración es útil cuando la aplicación no expone un punto de conexión de metadatos de acceso público.

La dirección URL de respuesta de una aplicación SAML es el punto de conexión en el que la aplicación espera recibir respuestas SAML. Normalmente, la aplicación proporciona esta dirección URL en el documento de metadatos bajo el atributo `AssertionConsumerServiceUrl`, como se muestra a continuación:

```xml
<SPSSODescriptor AuthnRequestsSigned="false" WantAssertionsSigned="false" protocolSupportEnumeration="urn:oasis:names:tc:SAML:2.0:protocol">
    ...
    <AssertionConsumerService index="0" isDefault="true" Binding="urn:oasis:names:tc:SAML:2.0:bindings:HTTP-POST" Location="https://samltestapp2.azurewebsites.net/SP/AssertionConsumer" />        
</SPSSODescriptor>
```

Si quiere invalidar los metadatos proporcionados en el atributo `AssertionConsumerServiceUrl` o la dirección URL no está presente en el documento de metadatos, puede configurar la dirección URL en el manifiesto en la propiedad `replyUrlsWithType`. El valor `BindingType` se establecerá en `HTTP POST`.

Si usa la aplicación de prueba de SAML, establezca la propiedad `url` de `replyUrlsWithType` en el valor que se muestra en el siguiente fragmento de código JSON:

```json
"replyUrlsWithType":[
  {
    "url":"https://samltestapp2.azurewebsites.net/SP/AssertionConsumer",
    "type":"Web"
  }
],
```

#### <a name="override-or-set-the-logout-url-optional"></a>Invalidación o establecimiento de la dirección URL de cierre de sesión (opcional)

Puede configurar la dirección URL de cierre de sesión a la que Azure AD B2C enviará al usuario después de una solicitud de cierre de sesión. Las direcciones URL de respuesta se pueden configurar en el manifiesto de aplicación.

Si quiere invalidar los metadatos proporcionados en el atributo `SingleLogoutService` o la dirección URL no está presente en el documento de metadatos, puede configurarlo en el manifiesto bajo la propiedad `Logout`. El valor `BindingType` se establecerá en `Http-Redirect`.

Normalmente, la aplicación proporciona esta dirección URL en el documento de metadatos bajo el atributo `AssertionConsumerServiceUrl`, como se muestra en el ejemplo siguiente:

```xml
<IDPSSODescriptor WantAuthnRequestsSigned="false" WantAssertionsSigned="false" protocolSupportEnumeration="urn:oasis:names:tc:SAML:2.0:protocol">
    <SingleLogoutService Binding="urn:oasis:names:tc:SAML:2.0:bindings:HTTP-Redirect" Location="https://samltestapp2.azurewebsites.net/logout" ResponseLocation="https://samltestapp2.azurewebsites.net/logout" />

</IDPSSODescriptor>
```

Si usa la aplicación de prueba de SAML como ejemplo, debe dejar `logoutUrl` establecido en `https://samltestapp2.azurewebsites.net/logout`:

```json
"logoutUrl": "https://samltestapp2.azurewebsites.net/logout",
```

> [!NOTE]
> Si decide configurar la dirección URL de respuesta y la dirección URL de cierre de sesión en el manifiesto de aplicación sin rellenar el punto de conexión de metadatos de la aplicación mediante la propiedad `samlMetadataUrl`, Azure AD B2C no validará la firma de la solicitud SAML. Tampoco cifrará la respuesta SAML.

## <a name="configure-azure-ad-b2c-as-a-saml-idp-in-your-saml-application"></a>Configuración de Azure AD B2C como IdP de SAML en la aplicación SAML

El último paso es habilitar Azure AD B2C como IdP de SAML en la aplicación SAML. Cada aplicación es diferente y los pasos para hacerlo varían. Consulte la documentación de la aplicación para obtener información detallada.

Los metadatos se pueden configurar en la aplicación como *metadatos estáticos* o *metadatos dinámicos*. En el modo estático, copie todos o parte de los metadatos de la directiva de Azure AD B2C. En el modo dinámico, proporcione la dirección URL en los metadatos y permita que la aplicación los lea de forma dinámica.

Normalmente se requieren todos o algunos de los siguientes elementos:

* **Metadatos**: use el formato `https://<tenant-name>.b2clogin.com/<tenant-name>.onmicrosoft.com/<policy-name>/Samlp/metadata`.
* **Emisor**: el valor `issuer` de la solicitud SAML debe coincidir con uno de los URI configurados en el elemento `identifierUris` del manifiesto de registro de la aplicación. Si el nombre `issuer` de la solicitud SAML no existe en el elemento `identifierUris`, [agréguelo al manifiesto de registro de la aplicación](#add-the-identifier). Por ejemplo: `https://contoso.onmicrosoft.com/app-name`. 
* **Dirección URL de inicio de sesión, punto de conexión SAML, dirección URL de SAML**: compruebe el valor en el archivo de metadatos de la directiva SAML de Azure AD B2C para el elemento XML `<SingleSignOnService>`.
* **Certificado**: este certificado es *B2C_1A_SamlIdpCert*, pero sin la clave privada. Para obtener la clave pública del certificado:

    1. Vaya a la dirección URL de metadatos especificada anteriormente.
    1. Copie el valor en el elemento `<X509Certificate>`.
    1. Péguelo en un archivo de texto.
    1. Guarde el archivo de texto como archivo *.cert*.

### <a name="test-with-the-saml-test-app"></a>Prueba con la aplicación de prueba de SAML

Puede usar nuestra [aplicación de prueba de SAML][samltest] para probar la configuración:

* Actualice el nombre del inquilino.
* Actualice el nombre de la directiva. Por ejemplo, use *B2C_1A_signup_signin_saml*.
* Especifique el URI del emisor. Use uno de los URI que se encuentran en el elemento `identifierUris` del manifiesto de registro de la aplicación. Por ejemplo, use `https://contoso.onmicrosoft.com/app-name`.

Seleccione **Iniciar sesión**. Al hacerlo, debería aparecer una pantalla de inicio de sesión de usuario. Después de iniciar sesión, se volverá a emitir una respuesta SAML a la aplicación de ejemplo.

## <a name="supported-and-unsupported-saml-modalities"></a>Modalidades de SAML admitidas y no admitidas

Los siguientes escenarios de aplicación SAML se admiten a través de su propio punto de conexión de metadatos:

* Especificación de varias direcciones URL de cierre de sesión o el enlace POST para la dirección URL de cierre de sesión en el objeto de entidad de servicio o aplicación.
* Especificación de una clave de firma para comprobar las solicitudes de usuario de confianza en el objeto de entidad de servicio o aplicación.
* Especificación de una clave de cifrado de tokens en el objeto de entidad de servicio o aplicación.
* Especificación del inicio de sesión iniciado por el proveedor de identidades, donde el proveedor de identidades es Azure AD B2C.

## <a name="next-steps"></a>Pasos siguientes

- Obtenga la aplicación web de prueba de SAML en el [repositorio de la comunidad de Azure AD B2C de GitHub](https://github.com/azure-ad-b2c/saml-sp-tester).
- Consulte las [opciones para registrar una aplicación SAML en Azure AD B2C](saml-service-provider-options.md).

<!-- LINKS - External -->
[samltest]: https://aka.ms/samltestapp

::: zone-end
