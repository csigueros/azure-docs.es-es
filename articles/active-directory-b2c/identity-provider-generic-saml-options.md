---
title: Establecimiento del inicio de sesión con las opciones del proveedor de identidades de SAML
titleSuffix: Azure Active Directory B2C
description: Configure las opciones del proveedor de identidades SAML (IdP) de inicio de sesión en Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 08/25/2021
ms.custom: project-no-code
ms.author: mimart
ms.subservice: B2C
zone_pivot_groups: b2c-policy-type
ms.openlocfilehash: c245fef005be5937887a3b8af1a5264e6520a6e8
ms.sourcegitcommit: 47fac4a88c6e23fb2aee8ebb093f15d8b19819ad
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/26/2021
ms.locfileid: "122967789"
---
# <a name="configure-saml-identity-provider-options-with-azure-active-directory-b2c"></a>Configuración de las opciones del proveedor de identidades de SAML con Azure Active Directory B2C

Azure Active Directory B2C (Azure AD B2C) admite la Federación con proveedores de identidades de SAML 2.0. En este artículo se describen las opciones de configuración que están disponibles al habilitar el inicio de sesión con un proveedor de identidades de SAML.

[!INCLUDE [active-directory-b2c-choose-user-flow-or-custom-policy](../../includes/active-directory-b2c-choose-user-flow-or-custom-policy.md)]

::: zone pivot="b2c-user-flow"

[!INCLUDE [active-directory-b2c-limited-to-custom-policy](../../includes/active-directory-b2c-limited-to-custom-policy.md)]

::: zone-end

::: zone pivot="b2c-custom-policy"

## <a name="claims-mapping"></a>Asignación de notificaciones

El elemento **OutputClaims** contiene una lista de notificaciones devueltas por el proveedor de identidades de SAML. Debe asignar el nombre de la notificación definida en la directiva al nombre definido en el proveedor de identidades. Consulte el proveedor de identidades para obtener la lista de notificaciones (aserciones). También puede comprobar el contenido de la respuesta de SAML que devuelve el proveedor de identidades. Para obtener más información, consulte [depurar los mensajes SAML](#debug-saml-protocol). Para agregar una notificación, primero [defina una notificación](claimsschema.md) y, después, agregue la notificación a la colección de notificaciones de entrada.

También puede incluir notificaciones no especificadas por el proveedor de identidades, siempre que establezca el atributo `DefaultValue`. El valor predeterminado puede ser estático o dinámico, mediante [notificaciones de contexto](#enable-use-of-context-claims).

El elemento de notificación de entrada contiene los atributos siguientes:

- **ClaimTypeReferenceId** es la referencia a un tipo de notificación. 
- **PartnerClaimType** es el nombre de la propiedad que aparece en la instrucción de aserción SAML. 
- **DefaultValue** es un valor predeterminado predefinido. Si la notificación está vacía, se usará el valor predeterminado. También puede utilizar [resoluciones de notificaciones](claim-resolver-overview.md) con un valor contextual, como el id. de correlación o la dirección IP del usuario.

### <a name="subject-name"></a>Nombre de sujeto

Para leer la aserción SAML **NamedId** en **Subject** como si fuera una notificación normalizada, establezca la notificación **PartnerClaimType** en el valor del atributo `SPNameQualifier`. No el atributo `SPNameQualifier` no aparece, establezca la notificación **PartnerClaimType** en el valor del atributo `NameQualifier`.

Aserción SAML:

```xml
<saml:Subject>
  <saml:NameID SPNameQualifier="http://your-idp.com/unique-identifier" Format="urn:oasis:names:tc:SAML:2.0:nameid-format:transient">david@contoso.com</saml:NameID>
  <SubjectConfirmation Method="urn:oasis:names:tc:SAML:2.0:cm:bearer">
    <SubjectConfirmationData InResponseTo="_cd37c3f2-6875-4308-a9db-ce2cf187f4d1" NotOnOrAfter="2020-02-15T16:23:23.137Z" Recipient="https://<your-tenant>.b2clogin.com/<your-tenant>.onmicrosoft.com/B2C_1A_TrustFrameworkBase/samlp/sso/assertionconsumer" />
    </SubjectConfirmation>
  </saml:SubjectConfirmation>
</saml:Subject>
```

Notificación de salida:

```xml
<OutputClaim ClaimTypeReferenceId="issuerUserId" PartnerClaimType="http://your-idp.com/unique-identifier" />
```

Si los atributos `SPNameQualifier` o `NameQualifier` no se encuentran en la aserción SAML, establezca la notificación **PartnerClaimType** en `assertionSubjectName`. Asegúrese de que **NameId** es el primer valor en el XML de la aserción. Cuando defina más de una aserción, Azure AD B2C toma el valor del tema de la última aserción.


## <a name="configure-saml-protocol-bindings"></a>Configurar enlaces de protocolo SAML

Las solicitudes SAML se envían al proveedor de identidades tal y como se especifica en el elemento de metadatos del proveedor de identidades `SingleSignOnService`. La mayoría de las solicitudes de autorización de los proveedores de identidades se transfieren directamente en la cadena de consulta de la dirección URL de una solicitud HTTP GET (dado que los mensajes son relativamente cortos). Consulte la documentación del proveedor de identidades para obtener información sobre cómo configurar los enlaces para ambas solicitudes SAML.

A continuación se proporciona un ejemplo de un servicio de inicio de sesión único de metadatos de Azure AD con dos enlaces. `HTTP-Redirect` tiene prioridad sobre `HTTP-POST` porque aparece en primer lugar en los metadatos del proveedor de identidades de SAML.

```xml
<IDPSSODescriptor protocolSupportEnumeration="urn:oasis:names:tc:SAML:2.0:protocol">
  ...
  <SingleSignOnService Binding="urn:oasis:names:tc:SAML:2.0:bindings:HTTP-Redirect" Location="https://login.microsoftonline.com/00000000-0000-0000-0000-000000000000/saml2"/>
  <SingleSignOnService Binding="urn:oasis:names:tc:SAML:2.0:bindings:HTTP-POST" Location="https://login.microsoftonline.com/00000000-0000-0000-0000-000000000000/saml2"/>
</IDPSSODescriptor>
```

### <a name="assertion-consumer-service"></a>Servicio de consumidor de aserciones

El Servicio de consumidor de aserciones (o ACS) es el lugar donde Azure AD B2C puede enviar y recibir las respuestas SAML del proveedor de identidades. Las respuestas SAML se transmiten a Azure AD B2C a través del enlace HTTP POST. La ubicación de ACS apunta a la directiva base del usuario de confianza. Por ejemplo, si la directiva de confianza es *B2C_1A_signup_signin*, ACS es la directiva base de *B2C_1A_signup_signin*, como *B2C_1A_TrustFrameworkBase*.

El siguiente es un ejemplo de un elemento de servicio del consumidor de aserción de metadatos de la directiva de Azure AD B2C. 

```xml
<SPSSODescriptor AuthnRequestsSigned="true" protocolSupportEnumeration="urn:oasis:names:tc:SAML:2.0:protocol">
  ...
  <AssertionConsumerService Binding="urn:oasis:names:tc:SAML:2.0:bindings:HTTP-POST" Location="https://your-tenant.b2clogin.com/your-tenant/B2C_1A_TrustFrameworkBase/samlp/sso/assertionconsumer" index="0" isDefault="true"/>
</SPSSODescriptor>
```

## <a name="configure-the-saml-request-signature"></a>Configurar la firma de la solicitud SAML

Azure AD B2C firma todas las solicitudes de autenticación salientes mediante la clave criptográfica **SamlMessageSigning**. Para deshabilitar la firma de la solicitud SAML, establezca **WantsSignedRequests** en `false`. Si los metadatos se establecen en `false`, se omiten los parámetros **SigAlg** y **Signature** (cadena de consulta o parámetro posterior) de la solicitud.

Estos metadatos también controlan el atributo **AuthnRequestsSigned**, que se incluye en los metadatos del perfil técnico de Azure AD B2C que se comparte con el proveedor de identidades. Azure AD B2C no firma la solicitud si el valor de **WantsSignedRequests** en los metadatos del perfil técnico se establece en `false` y los metadatos del proveedor de identidades **WantAuthnRequestsSigned** están establecidos en `false` o no se han especificado.

En el siguiente ejemplo se quita la firma del certificado de la solicitud SAML.

```xml
<Metadata>
  ...
  <Item Key="WantsSignedRequests">false</Item>
</Metadata>
```

### <a name="signature-algorithm"></a>Algoritmo de firma

Azure AD B2C usa `Sha1` para firmar la solicitud SAML. Use los metadatos de **XmlSignatureAlgorithm** para configurar el algoritmo que se va a usar. Los valores posibles son `Sha256`, `Sha384`, `Sha512` o `Sha1` (valor predeterminado). Estos metadatos controlan el valor del parámetro **SigAlg** (cadena de consulta o parámetro posterior) en la solicitud SAML. Asegúrese de configurar el algoritmo de firma en ambos lados con el mismo valor. Use solo el algoritmo que admite el certificado.

### <a name="include-key-info"></a>Incluir información de clave

Cuando el proveedor de identidades indica que Azure AD B2C enlace está establecido en `HTTP-POST`, Azure AD B2C incluye la firma y el algoritmo en el cuerpo de la solicitud SAML. También puede configurar Azure AD para incluir la clave pública del certificado cuando el enlace se establece en `HTTP-POST`. Use los metadatos de **IncludeKeyInfo** para `true` o `false`. En el ejemplo siguiente, Azure AD no incluirá la clave pública del certificado.

```xml
<Metadata>
  ...
  <Item Key="IncludeKeyInfo">false</Item>
</Metadata>
```

## <a name="configure-saml-request-name-id"></a>Configurar el identificador de nombre de la solicitud SAML

El elemento de solicitud de autorización de SAML `<NameID>` indica el formato del identificador de nombre de SAML. En esta sección se describe la configuración predeterminada y cómo personalizar el elemento de identificador de nombre.

## <a name="preferred-username"></a>Nombre de usuario preferido

Durante el recorrido de inicio de sesión de un usuario, una aplicación de usuario de confianza puede tener como destino un usuario específico. Azure AD B2C permite enviar un nombre de usuario preferido al proveedor de identidades de SAML. El elemento **InputClaims** se usa para enviar un objeto **NameId** en el **Asunto** de la solicitud de autorización de SAML.

Para incluir el identificador de nombre de sujeto dentro de la solicitud de autorización, agregue el siguiente `<InputClaims>` elemento inmediatamente después de `<CryptographicKeys>`. **PartnerClaimType** debe establecerse en `subject`.

```xml
<InputClaims>
  <InputClaim ClaimTypeReferenceId="signInName" PartnerClaimType="subject" />
</InputClaims>
```

En este ejemplo, Azure AD B2C envía el valor de la notificación **signInName** con **NameId** en el **asunto** de la solicitud de autorización de SAML.

```xml
<samlp:AuthnRequest ... >
  ...
  <saml:Subject>
    <saml:NameID>sam@contoso.com</saml:NameID>
  </saml:Subject>
</samlp:AuthnRequest>
```

Puede usar [notificaciones de contexto](claim-resolver-overview.md), como `{OIDC:LoginHint}` para rellenar el valor de notificación.

```xml
<Metadata>
  ...
  <Item Key="IncludeClaimResolvingInClaimsHandling">true</Item>
</Metadata>
  ...
<InputClaims>
  <InputClaim ClaimTypeReferenceId="signInName" PartnerClaimType="subject" DefaultValue="{OIDC:LoginHint}" AlwaysUseDefaultValue="true" />
</InputClaims>
```

### <a name="name-id-policy-format"></a>Formato de la directiva de ID. de nombre

De forma predeterminada, la solicitud de autorización de SAML especifica la directiva `urn:oasis:names:tc:SAML:1.1:nameid-format:unspecified`. Esto indica que se puede usar cualquier tipo de identificador compatible con el proveedor de identidades para el tema solicitado.

Para cambiar este comportamiento, consulte la documentación de su proveedor de identidades para obtener instrucciones sobre qué directivas de id. de nombre se admiten. A continuación, agregue los metadatos `NameIdPolicyFormat` en el formato de directiva correspondiente. Por ejemplo:

```xml
<Metadata>
  ...
  <Item Key="NameIdPolicyFormat">urn:oasis:names:tc:SAML:1.1:nameid-format:emailAddress</Item>
</Metadata>
```

La siguiente solicitud de autorización de SAML contiene la directiva de ID. de nombre.

```xml
<samlp:AuthnRequest ... >
  ...
  <samlp:NameIDPolicy Format="urn:oasis:names:tc:SAML:1.1:nameid-format:emailAddress" />
</samlp:AuthnRequest>
```

### <a name="allow-creating-new-accounts"></a>Permitir la creación de cuentas nuevas

Si especifica el [formato de directiva de identificador de nombre](#name-id-policy-format), también puede especificar la propiedad `AllowCreate` de **NameIDPolicy** para indicar si se permite al proveedor de identidades crear una nueva cuenta durante el flujo de inicio de sesión. Consulte la documentación del proveedor de identidades para obtener instrucciones.

Azure AD B2C omite la propiedad `AllowCreate` de forma predeterminada. Puede cambiar este comportamiento con los metadatos `NameIdPolicyAllowCreate`. El valor de estos metadatos es `true` o `false`.

En el ejemplo siguiente se muestra cómo establecer la propiedad `AllowCreate` de `NameIDPolicy` a `true`.

```xml
<Metadata>
  ...
  <Item Key="NameIdPolicyFormat">urn:oasis:names:tc:SAML:1.1:nameid-format:emailAddress</Item>
  <Item Key="NameIdPolicyAllowCreate">true</Item>
</Metadata>
```


En el ejemplo siguiente se muestra una solicitud de autorización con **AllowCreate** del elemento **NameIDPolicy** en la solicitud de autorización.


```xml
<samlp:AuthnRequest ... >
  ...
  <samlp:NameIDPolicy 
      Format="urn:oasis:names:tc:SAML:1.1:nameid-format:emailAddress" 
      AllowCreate="true" />
</samlp:AuthnRequest>
```

### <a name="force-authentication"></a>Forzado de la autenticación

Puede forzar que el IDP de SAML externo solicite al usuario que se autentique pasando la propiedad `ForceAuthN` en la solicitud de autenticación de SAML. Su proveedor de identidades también debe admitir esta propiedad.

La propiedad `ForceAuthN` es un valor booleano `true` o `false`. De manera predeterminada, Azure AD B2C establece el valor de ForceAuthN en `false`. Si después se restablece la sesión (por ejemplo, mediante el objeto `prompt=login` de OIDC), el valor de ForceAuthN se establecerá en `true`. Si se establece el elemento de metadatos como se muestra a continuación, se impondrá el valor al IDP externo para todas las solicitudes.

En el siguiente ejemplo se muestra la propiedad `ForceAuthN` establecida en `true`:

```xml
<Metadata>
  ...
  <Item Key="ForceAuthN">true</Item>
  ...
</Metadata>
```

En el siguiente ejemplo se muestra la propiedad `ForceAuthN` en una solicitud de autorización:


```xml
<samlp:AuthnRequest AssertionConsumerServiceURL="https://..."  ...
                    ForceAuthN="true">
  ...
</samlp:AuthnRequest>
```

### <a name="include-authentication-context-class-references"></a>Incluir referencias de clase de contexto de autenticación

Una solicitud de autorización de SAML puede contener un elemento **AuthnContext**, que especifica el contexto de una solicitud de autorización. El elemento puede contener una referencia de clase de contexto de autenticación, que indica al proveedor de identidades de SAML qué mecanismo de autenticación debe presentar al usuario.

Para configurar las referencias a la clase de contexto de autenticación, agregue metadatos de **IncludeAuthnContextClassReferences**. En el valor, especifica una o varias referencias de URI que identifican las clases de contexto de autenticación. Especifique varios URI como una lista delimitada por comas. Consulte la documentación del proveedor de identidades para obtener instrucciones sobre las URI de **AuthnContextClassRef** que se admiten.

En el ejemplo siguiente se permite a los usuarios iniciar sesión con el nombre de usuario y la contraseña, y para iniciar sesión con el nombre de usuario y la contraseña en una sesión protegida (SSL/TLS).

```xml
<Metadata>
  ...
  <Item Key="IncludeAuthnContextClassReferences">urn:oasis:names:tc:SAML:2.0:ac:classes:Password,urn:oasis:names:tc:SAML:2.0:ac:classes:PasswordProtectedTransport</Item>
</Metadata>
```

La siguiente solicitud de autorización de SAML contiene las referencias de la clase de contexto de autenticación.

```xml
<samlp:AuthnRequest ... >
  ...
  <samlp:RequestedAuthnContext>
    <saml:AuthnContextClassRef>urn:oasis:names:tc:SAML:2.0:ac:classes:Password</saml:AuthnContextClassRef>
    <saml:AuthnContextClassRef>urn:oasis:names:tc:SAML:2.0:ac:classes:PasswordProtectedTransport</saml:AuthnContextClassRef>
  </samlp:RequestedAuthnContext>
</samlp:AuthnRequest>
```

## <a name="include-custom-data-in-the-authorization-request"></a>Incluir datos personalizados en la solicitud de autorización

Opcionalmente, puede incluir elementos de extensión de mensajes de protocolo acordados por Azure AD BC y el proveedor de identidades. La extensión se presenta en formato XML. Los elementos de extensión se incluyen agregando datos XML dentro del elemento CDATA `<![CDATA[Your Custom XML]]>`. Consulte la documentación del proveedor de identidades para ver si se admite el elemento de extensiones.

En el siguiente ejemplo se muestra el uso de los datos de extensión:

```xml
<Metadata>
  ...
  <Item Key="AuthenticationRequestExtensions"><![CDATA[
            <ext:MyCustom xmlns:ext="urn:ext:custom">
              <ext:AssuranceLevel>1</ext:AssuranceLevel>
              <ext:AssuranceDescription>Identity verified to level 1.</ext:AssuranceDescription>
            </ext:MyCustom>]]></Item>
</Metadata>
```

> [!NOTE]
> De acuerdo con la especificación SAML, los datos de extensión deben ser un XML completo de espacio de nombres (por ejemplo, "urn:ext:custom" en el ejemplo anterior) y no uno de los espacios de nombres específicos de SAML.

Al usar la extensión de mensaje de protocolo SAML, la respuesta de SAML será similar al ejemplo siguiente:

```xml
<samlp:AuthnRequest ... >
  ...
  <samlp:Extensions>
    <ext:MyCustom xmlns:ext="urn:ext:custom">
      <ext:AssuranceLevel>1</ext:AssuranceLevel>
      <ext:AssuranceDescription>Identity verified to level 1.</ext:AssuranceDescription>
    </ext:MyCustom>
  </samlp:Extensions>
</samlp:AuthnRequest>
```

## <a name="require-signed-saml-responses"></a>Requerir respuestas SAML firmadas

Azure AD B2C requiere que se firmen todas las aserciones entrantes. Puede quitar este requisito estableciendo **WantsSignedAssertions** en `false`. En este caso no es necesario que el proveedor de identidades firme las aserciones, pero aunque lo haga, Azure AD B2C no validará la firma.

Los metadatos **WantsSignedAssertions** controlan la marca de metadatos **WantAssertionsSigned**, que se incluye en los metadatos del perfil técnico de Azure AD B2C que se comparte con el proveedor de identidades.

```xml
<SPSSODescriptor AuthnRequestsSigned="true" WantAssertionsSigned="true" protocolSupportEnumeration="urn:oasis:names:tc:SAML:2.0:protocol">
```

Si deshabilita la validación de las aserciones, también puede interesarle deshabilitar la validación de la firma de respuesta. Establecer los metadatos **ResponsesSigned** en `false`. En este caso no es necesario que el proveedor de identidades firme la respuesta de SAML, pero incluso si lo hace, Azure AD B2C no validará la firma.

En el ejemplo siguiente se quitan tanto el mensaje como la firma de aserción:

```xml
<Metadata>
  ...
  <Item Key="WantsSignedAssertions">false</Item>
  <Item Key="ResponsesSigned">false</Item>
</Metadata>
```

## <a name="require-encrypted-saml-responses"></a>Requerir respuestas SAML cifradas

Para requerir el cifrado de todas las aserciones entrantes, establezca los metadatos de **WantsEncryptedAssertions**. Cuando se requiere cifrado, el proveedor de identidades usa una clave pública de un certificado de cifrado en un perfil técnico de Azure AD B2C. Azure AD B2C descifra la aserción de respuesta mediante la parte privada del certificado de cifrado.

Si habilita el cifrado de aserciones, es posible que también tenga que deshabilitar la validación de firma de respuesta (para obtener más información, consulte [Solicitar respuestas SAML firmadas](#require-signed-saml-responses)).

Cuando los metadatos **WantsEncryptedAssertions** se establecen en `true`, los metadatos del perfil técnico de Azure AD B2C incluyen la sección **cifrado**. El proveedor de identidades lee los metadatos y cifra la aserción de respuesta de SAML con la clave pública que se proporciona en los metadatos del perfil técnico de Azure AD B2C.

En el ejemplo siguiente se muestra la sección del descriptor de clave de los metadatos de SAML usados para el cifrado:

```xml
<SPSSODescriptor AuthnRequestsSigned="true"  protocolSupportEnumeration="urn:oasis:names:tc:SAML:2.0:protocol">
  <KeyDescriptor use="encryption">
    <KeyInfo xmlns="https://www.w3.org/2000/09/xmldsig#">
      <X509Data>
        <X509Certificate>valid certificate</X509Certificate>
      </X509Data>
    </KeyInfo>
  </KeyDescriptor>
  ...
</SPSSODescriptor>
```

Para cifrar la aserción de respuesta SAML:

1. [Cree una clave de directiva](identity-provider-generic-saml.md#create-a-policy-key) con un identificador único. Por ejemplo, `B2C_1A_SAMLEncryptionCert`.
2. En la colección **CryptographicKeys** de perfil técnico de SAML. Asigne el nombre de la clave de directiva que creó en el primer paso a **StorageReferenceId**. El identificador `SamlAssertionDecryption` indica el uso de la clave criptográfica para cifrar y descifrar la aserción de la respuesta de SAML.

    ```xml
    <CryptographicKeys>
            ...
      <Key Id="SamlAssertionDecryption" StorageReferenceId="B2C_1A_SAMLEncryptionCert"/>
    </CryptographicKeys>
    ```

3. Establezca los metadatos del perfil técnico **WantsEncryptedAssertions** en `true`.
    
    ```xml
    <Metadata>
      ...
      <Item Key="WantsEncryptedAssertions">true</Item>
    </Metadata>
    ```

4. Actualice el proveedor de identidades con los nuevos metadatos del perfil técnico de Azure AD B2C. La propiedad **use** de **KeyDescriptor** debería estar establecida en `encryption` y contener la clave pública del certificado.

## <a name="enable-use-of-context-claims"></a>Habilitar el uso de notificaciones de contexto

En la colección de notificaciones de entrada y salida, puede incluir las notificaciones que el proveedor de identidades no devuelve, siempre y cuando establezca el atributo `DefaultValue`. También puede usar [notificaciones de contexto](claim-resolver-overview.md) para incluirlas en el perfil técnico. Para usar una demanda de contexto:

1. Agregue un tipo de demanda al elemento [ClaimsSchema](claimsschema.md) en [BuildingBlocks](buildingblocks.md).
2. Agregue una notificación de salida a la colección de entrada o de salida. En el ejemplo siguiente, la primera notificación establece el valor del proveedor de identidades. La segunda notificación usa las [notificaciones de contexto](claim-resolver-overview.md) de la dirección IP del usuario.
    
    ```xml
    <OutputClaims>
      <OutputClaim ClaimTypeReferenceId="identityProvider" DefaultValue="contoso.com" AlwaysUseDefaultValue="true" />
      <OutputClaim ClaimTypeReferenceId="IpAddress" DefaultValue="{Context:IPAddress}" AlwaysUseDefaultValue="true" />
    </OutputClaims>
    ```

## <a name="disable-single-logout"></a>Deshabilitar cierre de sesión único

En una solicitud de cierre de sesión de aplicación, Azure AD B2C intenta cerrar la sesión del proveedor de identidades de SAML. Para obtener más información, consulte [Cierre de sesión de Azure AD B2C](session-behavior.md#sign-out).  Para deshabilitar el comportamiento de cierre de sesión único, establezca los metadatos **SingleLogoutEnabled** en `false`.

```xml
<Metadata>
  ...
  <Item Key="SingleLogoutEnabled">false</Item>
</Metadata>
```

## <a name="debug-saml-protocol"></a>Depurar protocolo SAML

Para ayudar a configurar y depurar la federación con el proveedor de identidades de SAML, puede usar una extensión del navegador para el protocolo SAML; como la [extensión SAML DevTools](https://chrome.google.com/webstore/detail/saml-devtools-extension/jndllhgbinhiiddokbeoeepbppdnhhio) para Chrome, [SAML-Tracer](https://addons.mozilla.org/es/firefox/addon/saml-tracer/) para Firefox o las [herramientas para desarrolladores de Edge o IE](https://techcommunity.microsoft.com/t5/microsoft-sharepoint-blog/gathering-a-saml-token-using-edge-or-ie-developer-tools/ba-p/320957).

Con estas herramientas, puede comprobar la integración entre Azure AD B2C y el proveedor de identidades de SAML. Por ejemplo:

* Puede comprobar si la solicitud SAML contiene una firma y determinar qué algoritmo se usa para iniciar sesión en la solicitud de autorización.
* Obtiene las notificaciones (aserciones) en la sección `AttributeStatement`.
* Compruebe si el proveedor de identidades devuelve un mensaje de error.
* Compruebe si la sección de aserción está cifrada.

## <a name="next-steps"></a>Pasos siguientes

- Obtenga información sobre cómo diagnosticar problemas con las directivas personalizadas mediante [Application Insights](troubleshoot-with-application-insights.md). 

::: zone-end
