---
title: Configuración de las opciones del proveedor de servicios SAML
title-suffix: Azure Active Directory B2C
description: Aprenda a configurar las opciones del proveedor de servicios SAML de Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 04/05/2021
ms.author: mimart
ms.subservice: B2C
ms.custom: fasttrack-edit
zone_pivot_groups: b2c-policy-type
ms.openlocfilehash: 51672c7757e5c747bf2b243e32506159a468f2d5
ms.sourcegitcommit: 40866facf800a09574f97cc486b5f64fced67eb2
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/30/2021
ms.locfileid: "123222157"
---
# <a name="options-for-registering-a-saml-application-in-azure-ad-b2c"></a>Opciones para registrar una aplicación SAML en Azure AD B2C

En este artículo se describen las opciones de configuración que están disponibles al conectar Azure Active Directory B2C (Azure AD B2C) con la aplicación de Lenguaje de marcado de aserción de seguridad (SAML).

[!INCLUDE [active-directory-b2c-choose-user-flow-or-custom-policy](../../includes/active-directory-b2c-choose-user-flow-or-custom-policy.md)]

::: zone pivot="b2c-user-flow"

[!INCLUDE [active-directory-b2c-limited-to-custom-policy](../../includes/active-directory-b2c-limited-to-custom-policy.md)]

::: zone-end

::: zone pivot="b2c-custom-policy"


## <a name="specify-a-saml-response-signature"></a>Especificación de una firma de respuesta de SAML

Puede especificar un certificado que se utilizará para firmar los mensajes SAML. El mensaje es el elemento `<samlp:Response>` dentro de la respuesta SAML enviada a la aplicación.

Si aún no tiene una clave de directiva, [créela](saml-service-provider.md#create-a-policy-key). A continuación, configure el elemento de metadatos `SamlMessageSigning` en el perfil técnico del emisor de tokens de SAML. `StorageReferenceId` debe hacer referencia al nombre de la clave de la directiva.

```xml
<ClaimsProvider>
  <DisplayName>Token Issuer</DisplayName>
  <TechnicalProfiles>
    <!-- SAML Token Issuer technical profile -->
    <TechnicalProfile Id="Saml2AssertionIssuer">
      <DisplayName>Token Issuer</DisplayName>
      <Protocol Name="SAML2"/>
      <OutputTokenFormat>SAML2</OutputTokenFormat>
        ...
      <CryptographicKeys>
        <Key Id="SamlMessageSigning" StorageReferenceId="B2C_1A_SamlMessageCert"/>
        ...
      </CryptographicKeys>
    ...
    </TechnicalProfile>
```

### <a name="signature-algorithm"></a>Algoritmo de firma

Puede configurar el algoritmo de firma utilizado para firmar la aserción SAML. Los valores posibles son `Sha256`, `Sha384`, `Sha512` o `Sha1`. Asegúrese de que el perfil técnico y la aplicación usan el mismo algoritmo de firma. Use solo el algoritmo que admite el certificado.

Configure el algoritmo de firma mediante la clave de metadatos `XmlSignatureAlgorithm` en el elemento `Metadata` del usuario de confianza.

```xml
<RelyingParty>
  <DefaultUserJourney ReferenceId="SignUpOrSignIn" />
  <TechnicalProfile Id="PolicyProfile">
    <DisplayName>PolicyProfile</DisplayName>
    <Protocol Name="SAML2"/>
    <Metadata>
      <Item Key="XmlSignatureAlgorithm">Sha256</Item>
    </Metadata>
   ..
  </TechnicalProfile>
</RelyingParty>
```

## <a name="check-the-saml-assertion-signature"></a>Comprobación de la firma de aserción SAML

Cuando una aplicación espere que se firme la sección de aserciones de SAML, asegúrese de que el proveedor de servicios de SAML haya establecido `WantAssertionsSigned` en `true`. Si lo ha establecido en `false` o no existe, la sección de aserciones no se firmará. 

En el ejemplo siguiente se muestran los metadatos del proveedor de servicios de SAML con `WantAssertionsSigned` establecido en `true`.

```xml
<EntityDescriptor ID="id123456789" entityID="https://samltestapp2.azurewebsites.net" validUntil="2099-12-31T23:59:59Z" xmlns="urn:oasis:names:tc:SAML:2.0:metadata">
  <SPSSODescriptor  WantAssertionsSigned="true" AuthnRequestsSigned="false" protocolSupportEnumeration="urn:oasis:names:tc:SAML:2.0:protocol">
  ...
  </SPSSODescriptor>
</EntityDescriptor>
```  

### <a name="signature-certificate"></a>Certificado de firma

La directiva debe especificar que se use un certificado para firmar la sección de aserciones de SAML de la respuesta de SAML. Si aún no tiene una clave de directiva, [créela](saml-service-provider.md#create-a-policy-key). A continuación, configure el elemento de metadatos `SamlAssertionSigning` en el perfil técnico del emisor de tokens de SAML. `StorageReferenceId` debe hacer referencia al nombre de la clave de la directiva.

```xml
<ClaimsProvider>
  <DisplayName>Token Issuer</DisplayName>
  <TechnicalProfiles>
    <!-- SAML Token Issuer technical profile -->
    <TechnicalProfile Id="Saml2AssertionIssuer">
      <DisplayName>Token Issuer</DisplayName>
      <Protocol Name="SAML2"/>
      <OutputTokenFormat>SAML2</OutputTokenFormat>
        ...
      <CryptographicKeys>
        <Key Id="SamlAssertionSigning" StorageReferenceId="B2C_1A_SamlMessageCert"/>
        ...
      </CryptographicKeys>
    ...
    </TechnicalProfile>
```

## <a name="enable-encryption-in-saml-assertions"></a>Habilitación del cifrado en aserciones SAML

Si la aplicación espera que las aserciones SAML estén en un formato cifrado, debe asegurarse de que el cifrado esté habilitado en la directiva de Azure AD B2C.

Azure AD B2C utiliza el certificado de clave pública del proveedor de servicios para cifrar la aserción SAML. La clave pública debe existir en el punto de conexión de metadatos de la aplicación SAML con el valor `use` de `KeyDescriptor` establecido en `Encryption`, como se muestra en el ejemplo siguiente:

```xml
<KeyDescriptor use="encryption">
  <KeyInfo xmlns="https://www.w3.org/2000/09/xmldsig#">
    <X509Data>
      <X509Certificate>valid certificate</X509Certificate>
    </X509Data>
  </KeyInfo>
</KeyDescriptor>
```

Para habilitar Azure AD B2C para enviar aserciones cifradas, establezca el elemento de metadatos `WantsEncryptedAssertion` en `true` en el [perfil técnico del usuario de confianza](relyingparty.md#technicalprofile). También puede configurar el algoritmo utilizado para cifrar la aserción SAML.

```xml
<RelyingParty>
  <DefaultUserJourney ReferenceId="SignUpOrSignIn" />
  <TechnicalProfile Id="PolicyProfile">
    <DisplayName>PolicyProfile</DisplayName>
    <Protocol Name="SAML2"/>
    <Metadata>
      <Item Key="WantsEncryptedAssertions">true</Item>
    </Metadata>
   ..
  </TechnicalProfile>
</RelyingParty>
```

### <a name="encryption-method"></a>Encryption method

Para configurar el método de cifrado que se usa para cifrar los datos de aserción SAML, establezca la clave de metadatos `DataEncryptionMethod` en el usuario de confianza. Los valores posibles son: `Aes256` (predeterminado), `Aes192`, `Sha512` o `Aes128`. Los metadatos controlan el valor del elemento `<EncryptedData>` en la respuesta de SAML.

Para configurar el método de cifrado que se usa para cifrar la copia de la clave que se usó para cifrar los datos de aserción SAML, establezca la clave de metadatos `KeyEncryptionMethod` en el usuario de confianza. Los valores posibles son:

- `Rsa15` (valor predeterminado): algoritmo Public Key Cryptography Standard (PKCS) de RSA, versión 1.5.
- `RsaOaep`: algoritmo de cifrado Optimal Asymmetric Encryption Padding (OAEP) de RSA.  

Los metadatos controlan el valor del elemento `<EncryptedKey>` en la respuesta de SAML.

En el ejemplo siguiente se muestra la sección `EncryptedAssertion` de una aserción SAML. El método de cifrado de datos es `Aes128` y el método de cifrado de clave es `Rsa15`.

```xml
<saml:EncryptedAssertion>
  <xenc:EncryptedData xmlns:xenc="http://www.w3.org/2001/04/xmlenc#"
    xmlns:dsig="http://www.w3.org/2000/09/xmldsig#" Type="http://www.w3.org/2001/04/xmlenc#Element">
    <xenc:EncryptionMethod Algorithm="http://www.w3.org/2001/04/xmlenc#aes128-cbc" />
    <dsig:KeyInfo>
      <xenc:EncryptedKey>
        <xenc:EncryptionMethod Algorithm="http://www.w3.org/2001/04/xmlenc#rsa-1_5" />
        <xenc:CipherData>
          <xenc:CipherValue>...</xenc:CipherValue>
        </xenc:CipherData>
      </xenc:EncryptedKey>
    </dsig:KeyInfo>
    <xenc:CipherData>
      <xenc:CipherValue>...</xenc:CipherValue>
    </xenc:CipherData>
  </xenc:EncryptedData>
</saml:EncryptedAssertion>
```

Puede cambiar el formato de las aserciones cifradas. Para configurar el formato de cifrado, establezca la clave de metadatos `UseDetachedKeys` en el usuario de confianza. Valores posibles: `true` o `false` (valor predeterminado). Cuando el valor se establece en `true`, las claves desasociadas agregan la aserción cifrada como elemento secundario de `EncryptedAssertion` y no de `EncryptedData`.

Para configurar el método y el formato de cifrado, use las claves de metadatos del [perfil técnico del usuario de confianza](relyingparty.md#technicalprofile):

```xml
<RelyingParty>
  <DefaultUserJourney ReferenceId="SignUpOrSignIn" />
  <TechnicalProfile Id="PolicyProfile">
    <DisplayName>PolicyProfile</DisplayName>
    <Protocol Name="SAML2"/>
    <Metadata>
      <Item Key="DataEncryptionMethod">Aes128</Item>
      <Item Key="KeyEncryptionMethod">Rsa15</Item>
      <Item Key="UseDetachedKeys">false</Item>
    </Metadata>
   ..
  </TechnicalProfile>
</RelyingParty>
```

## <a name="configure-idp-initiated-flow"></a>Configuración del flujo iniciado por IdP

Si la aplicación espera recibir una aserción SAML sin enviar primero una solicitud de autenticación SAML al proveedor de identidades (IdP), debe configurar Azure AD B2C para el flujo iniciado por IdP.

En el flujo iniciado por IdP, el proveedor de identidades (Azure AD B2C) comienza el proceso de inicio de sesión. El proveedor de identidades envía una respuesta SAML no solicitada al proveedor de servicios (la aplicación de usuario de confianza).

Actualmente no se admiten escenarios en los que el proveedor de identidades que inicia el proceso sea un proveedor de identidades externo federado con Azure AD B2C; por ejemplo [Servicios de federación de Active Directory (AD FS)](identity-provider-adfs.md) o [Salesforce](identity-provider-salesforce-saml.md). El flujo iniciado por IdP solo se admite para la autenticación de cuentas locales en Azure AD B2C.

Para habilitar el flujo iniciado por IdP, establezca el elemento de metadatos `IdpInitiatedProfileEnabled`Ien `true` en el [perfil técnico del usuario de confianza](relyingparty.md#technicalprofile).

```xml
<RelyingParty>
  <DefaultUserJourney ReferenceId="SignUpOrSignIn" />
  <TechnicalProfile Id="PolicyProfile">
    <DisplayName>PolicyProfile</DisplayName>
    <Protocol Name="SAML2"/>
    <Metadata>
      <Item Key="IdpInitiatedProfileEnabled">true</Item>
    </Metadata>
   ..
  </TechnicalProfile>
</RelyingParty>
```

Para iniciar sesión o registrar un usuario mediante el flujo iniciado por IdP, use la siguiente dirección URL:

```
https://<tenant-name>.b2clogin.com/<tenant-name>.onmicrosoft.com/<policy-name>/generic/login?EntityId=app-identifier-uri 
```

Reemplace los siguientes valores:

* Remplace `<tenant-name>` por el nombre del inquilino.
* Reemplace `<policy-name>` por el nombre de la directiva de usuario de confianza de SAML.
* Reemplace `app-identifier-uri` por el valor `identifierUris` del archivo de metadatos, por ejemplo, `https://contoso.onmicrosoft.com/app-name`.

### <a name="sample-policy"></a>Directiva de ejemplo

Puede usar una directiva de ejemplo completa para realizar pruebas con la aplicación de prueba de SAML:

1. Descargue la [directiva de ejemplo de inicio de sesión iniciada por el proveedor de servicios SAML](https://github.com/azure-ad-b2c/saml-sp/tree/master/policy/SAML-SP-Initiated).
1. Actualice `TenantId` para que coincida con su nombre de inquilino. En este artículo se usa el ejemplo *contoso.b2clogin.com*.
1. Mantenga el nombre de la directiva *B2C_1A_signup_signin_saml*.

## <a name="configure-the-saml-response-lifetime"></a>Configuración de la duración de respuesta de SAML

Puede configurar el período de tiempo durante el que la respuesta de SAML sigue siendo válida. Establezca la duración mediante el elemento de metadatos `TokenLifeTimeInSeconds` en el perfil técnico del emisor de tokens de SAML. Este valor es el número de segundos que pueden transcurrir desde la marca de tiempo `NotBefore` calculada en el momento de emitir el token. La duración predeterminada es de 300 segundos (5 minutos).

```xml
<ClaimsProvider>
  <DisplayName>Token Issuer</DisplayName>
  <TechnicalProfiles>
    <TechnicalProfile Id="Saml2AssertionIssuer">
      <DisplayName>Token Issuer</DisplayName>
      <Protocol Name="SAML2"/>
      <OutputTokenFormat>SAML2</OutputTokenFormat>
      <Metadata>
        <Item Key="TokenLifeTimeInSeconds">400</Item>
      </Metadata>
      ...
    </TechnicalProfile>
```

## <a name="configure-the-time-skew-of-a-saml-response"></a>Configuración del desfase horario de una respuesta de SAML

Puede configurar el desfase horario que se aplica a la marca de tiempo `NotBefore` de la respuesta de SAML. Esta configuración garantiza que, si las horas entre dos plataformas no están sincronizadas, la aserción SAML seguirá considerándose válida cuando se encuentre dentro de este desfase horario.

Establezca el desfase horario mediante el elemento de metadatos `TokenNotBeforeSkewInSeconds` en el perfil técnico del emisor de tokens de SAML. El valor de desfase se proporciona en segundos, con un valor predeterminado de 0. El valor máximo es 3600 (una hora).

Por ejemplo, si `TokenNotBeforeSkewInSeconds` está establecido en `120` segundos:

- El token se emite a las 13:05:10 UTC.
- El token es válido a partir de las 13:03:10 UTC.

```xml
<ClaimsProvider>
  <DisplayName>Token Issuer</DisplayName>
  <TechnicalProfiles>
    <TechnicalProfile Id="Saml2AssertionIssuer">
      <DisplayName>Token Issuer</DisplayName>
      <Protocol Name="SAML2"/>
      <OutputTokenFormat>SAML2</OutputTokenFormat>
      <Metadata>
        <Item Key="TokenNotBeforeSkewInSeconds">120</Item>
      </Metadata>
      ...
    </TechnicalProfile>
```

## <a name="remove-milliseconds-from-the-date-and-time"></a>Eliminación de milisegundos de la fecha y hora

Puede especificar si se eliminarán milisegundos de los valores de fecha y hora en la respuesta SAML. (Estos valores incluyen `IssueInstant`, `NotBefore`, `NotOnOrAfter` y `AuthnInstant`). Para quitar los milisegundos, establezca la clave de metadatos `RemoveMillisecondsFromDateTime` en el usuario de confianza. Valores posibles: `false` (predeterminado) o `true`.

```xml
<ClaimsProvider>
  <DisplayName>Token Issuer</DisplayName>
  <TechnicalProfiles>
    <TechnicalProfile Id="Saml2AssertionIssuer">
      <DisplayName>Token Issuer</DisplayName>
      <Protocol Name="SAML2"/>
      <OutputTokenFormat>SAML2</OutputTokenFormat>
      <Metadata>
        <Item Key="RemoveMillisecondsFromDateTime">true</Item>
      </Metadata>
      ...
    </TechnicalProfile>
```

## <a name="use-an-issuer-id-to-override-an-issuer-uri"></a>Uso de un identificador de emisor para invalidar un URI del emisor

Si tiene varias aplicaciones SAML que dependen de distintos valores de `entityID`, puede reemplazar el valor de `IssuerUri` del archivo de usuario de confianza. Para reemplazar el URI del emisor, copie el perfil técnico con el identificador `IssuerUri` del archivo base y reemplace el valor `Saml2AssertionIssuer`.

> [!TIP]
> Copie la sección `<ClaimsProviders>` de la base y conserve estos elementos en el proveedor de notificaciones: `<DisplayName>Token Issuer</DisplayName>`, `<TechnicalProfile Id="Saml2AssertionIssuer">` y `<DisplayName>Token Issuer</DisplayName>`.
 
Ejemplo:

```xml
   <ClaimsProviders>   
    <ClaimsProvider>
      <DisplayName>Token Issuer</DisplayName>
      <TechnicalProfiles>
        <TechnicalProfile Id="Saml2AssertionIssuer">
          <DisplayName>Token Issuer</DisplayName>
          <Metadata>
            <Item Key="IssuerUri">customURI</Item>
          </Metadata>
        </TechnicalProfile>
      </TechnicalProfiles>
    </ClaimsProvider>
  </ClaimsProviders>
  <RelyingParty>
    <DefaultUserJourney ReferenceId="SignUpInSAML" />
    <TechnicalProfile Id="PolicyProfile">
      <DisplayName>PolicyProfile</DisplayName>
      <Protocol Name="SAML2" />
      <Metadata>
     …
```

## <a name="manage-a-session"></a>Administrar una sesión

Puede administrar la sesión entre Azure AD B2C y la aplicación de usuario de confianza SAML mediante los elementos `UseTechnicalProfileForSessionManagement` y [SamlSSOSessionProvider](custom-policy-reference-sso.md#samlssosessionprovider).

## <a name="force-users-to-reauthenticate"></a>Obligatoriedad de que los usuarios vuelvan a autenticarse 

Para obligar a los usuarios a volver a autenticarse, la aplicación puede incluir el atributo `ForceAuthn` en la solicitud de autenticación de SAML. El atributo `ForceAuthn` es un valor booleano. Cuando se establece en `true`, la sesión del usuario se invalidará en Azure AD B2C y el usuario tendrá que volver a autenticarse. 

La siguiente solicitud de autenticación de SAML muestra cómo establecer el atributo `ForceAuthn` en `true`. 

```xml
<samlp:AuthnRequest 
       Destination="https://contoso.b2clogin.com/contoso.onmicrosoft.com/B2C_1A_SAML2_signup_signin/samlp/sso/login"
       ForceAuthn="true" ...>
    ...
</samlp:AuthnRequest>
```

## <a name="sign-the-azure-ad-b2c-idp-saml-metadata"></a>Firma de los metadatos de SAML del IdP de Azure AD B2C

Puede indicar a Azure AD B2C que firme su documento de metadatos del proveedor de identidades de SAML, si la aplicación así lo requiere. Si aún no tiene una clave de directiva, [créela](saml-service-provider.md#create-a-policy-key). A continuación, configure el elemento de metadatos `MetadataSigning` en el perfil técnico del emisor de tokens de SAML. `StorageReferenceId` debe hacer referencia al nombre de la clave de la directiva.

```xml
<ClaimsProvider>
  <DisplayName>Token Issuer</DisplayName>
  <TechnicalProfiles>
    <!-- SAML Token Issuer technical profile -->
    <TechnicalProfile Id="Saml2AssertionIssuer">
      <DisplayName>Token Issuer</DisplayName>
      <Protocol Name="SAML2"/>
      <OutputTokenFormat>SAML2</OutputTokenFormat>
        ...
      <CryptographicKeys>
        <Key Id="MetadataSigning" StorageReferenceId="B2C_1A_SamlMetadataCert"/>
        ...
      </CryptographicKeys>
    ...
    </TechnicalProfile>
```

## <a name="debug-the-saml-protocol"></a>Depuración del protocolo SAML

Para ayudar a configurar y depurar la integración con el proveedor de servicios, puede usar una extensión de navegador para el protocolo SAML. Las extensiones de explorador incluyen la [extensión SAML DevTools para Chrome](https://chrome.google.com/webstore/detail/saml-devtools-extension/jndllhgbinhiiddokbeoeepbppdnhhio), [SAML-tracer para Firefox](https://addons.mozilla.org/es/firefox/addon/saml-tracer/) y [Herramientas de desarrollo para Edge o Internet Explorer](https://techcommunity.microsoft.com/t5/microsoft-sharepoint-blog/gathering-a-saml-token-using-edge-or-ie-developer-tools/ba-p/320957).

Con estas herramientas, puede comprobar la integración entre la aplicación y Azure AD B2C. Por ejemplo:

* Puede comprobar si la solicitud SAML contiene una firma y determinar qué algoritmo se usa para iniciar sesión en la solicitud de autorización.
* Puede comprobar si Azure AD B2C devuelve un mensaje de error.
* Compruebe si la sección de aserción está cifrada.

## <a name="next-steps"></a>Pasos siguientes

- Puede encontrar más información sobre el protocolo SAML en el [sitio web de OASIS](https://www.oasis-open.org/).
- Obtenga la aplicación web de prueba de SAML en el [repositorio de Azure AD B2C de la comunidad de GitHub](https://github.com/azure-ad-b2c/saml-sp-tester).

<!-- LINKS - External -->
[samltest]: https://aka.ms/samltestapp

::: zone-end
