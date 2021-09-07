---
title: Incorporación de AD FS como proveedor de identidades de SAML mediante directivas personalizadas
titleSuffix: Azure AD B2C
description: Configure AD FS 2016 mediante el protocolo SAML y las directivas personalizadas en Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 03/15/2021
ms.custom: project-no-code
ms.author: mimart
ms.subservice: B2C
zone_pivot_groups: b2c-policy-type
ms.openlocfilehash: 90e16f89a6b591841ed28942c44f7dc1223e975a
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/13/2021
ms.locfileid: "121779863"
---
# <a name="add-ad-fs-as-a-saml-identity-provider-using-custom-policies-in-azure-active-directory-b2c"></a>Incorporación de AD FS como proveedor de identidades de SAML mediante las directivas personalizadas de Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-choose-user-flow-or-custom-policy](../../includes/active-directory-b2c-choose-user-flow-or-custom-policy.md)]

::: zone pivot="b2c-user-flow"

[!INCLUDE [active-directory-b2c-limited-to-custom-policy](../../includes/active-directory-b2c-limited-to-custom-policy.md)]

::: zone-end

::: zone pivot="b2c-custom-policy"

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

En este artículo se muestra cómo habilitar el inicio de sesión para una cuenta de usuario de AD FS mediante [directivas personalizadas](custom-policy-overview.md) en Azure Active Directory B2C (Azure AD B2C). Para habilitar el inicio de sesión, agregue un [proveedor de identidades de SAML](identity-provider-generic-saml.md) a una directiva personalizada.

## <a name="prerequisites"></a>Prerrequisitos

[!INCLUDE [active-directory-b2c-customization-prerequisites-custom-policy](../../includes/active-directory-b2c-customization-prerequisites-custom-policy.md)]

## <a name="create-a-self-signed-certificate"></a>Creación de un certificado autofirmado

[!INCLUDE [active-directory-b2c-create-self-signed-certificate](../../includes/active-directory-b2c-create-self-signed-certificate.md)]

## <a name="create-a-policy-key"></a>Creación de una clave de directiva

Debe almacenar el certificado en el inquilino de Azure AD B2C.

1. Inicie sesión en [Azure Portal](https://portal.azure.com/).
2. Asegúrese de que usa el directorio que contiene el inquilino de Azure AD B2C. Seleccione el filtro **Directorio y suscripciones** del menú superior y elija el directorio que contiene el inquilino.
3. Elija **Todos los servicios** en la esquina superior izquierda de Azure Portal, y busque y seleccione **Azure AD B2C**.
4. En la página de introducción, seleccione **Identity Experience Framework**.
5. Seleccione **Claves de directiva** y luego **Agregar**.
6. En **Opciones**, elija `Upload`.
7. Escriba un **nombre** para la clave de directiva. Por ejemplo, `SAMLSigningCert`. Se agregará el prefijo `B2C_1A_` automáticamente al nombre de la clave.
8. Busque el archivo .pfx de certificado con la clave privada y selecciónelo.
9. Haga clic en **Crear**.

## <a name="add-a-claims-provider"></a>Incorporación de un proveedor de notificaciones

Si quiere que los usuarios inicien sesión con una cuenta de AD FS, deberá definirla como proveedor de notificaciones con el que Azure AD B2C pueda comunicarse mediante un punto de conexión. El punto de conexión proporciona un conjunto de notificaciones que Azure AD B2C usa para comprobar que un usuario concreto se ha autenticado.

Puede definir una cuenta de AD FS como proveedor de notificaciones; para ello, agréguela al elemento **ClaimsProvider** en el archivo de extensión de la directiva. Para más información, consulte cómo [definir un proveedor de identidades de SAML](identity-provider-generic-saml.md).

1. Abra el archivo *TrustFrameworkExtensions.xml*.
1. Busque el elemento **ClaimsProviders**. Si no existe, agréguelo debajo del elemento raíz.
1. Agregue un nuevo elemento **ClaimsProvider** tal como se muestra a continuación:

    ```xml
    <ClaimsProvider>
      <Domain>contoso.com</Domain>
      <DisplayName>Contoso</DisplayName>
      <TechnicalProfiles>
        <TechnicalProfile Id="Contoso-SAML2">
          <DisplayName>Contoso</DisplayName>
          <Description>Login with your AD FS account</Description>
          <Protocol Name="SAML2"/>
          <Metadata>
            <Item Key="WantsEncryptedAssertions">false</Item>
            <Item Key="PartnerEntity">https://your-AD-FS-domain/federationmetadata/2007-06/federationmetadata.xml</Item>
          </Metadata>
          <CryptographicKeys>
            <Key Id="SamlMessageSigning" StorageReferenceId="B2C_1A_SAMLSigningCert"/>
          </CryptographicKeys>
          <OutputClaims>
            <OutputClaim ClaimTypeReferenceId="issuerUserId" PartnerClaimType="userPrincipalName" />
            <OutputClaim ClaimTypeReferenceId="givenName" PartnerClaimType="given_name"/>
            <OutputClaim ClaimTypeReferenceId="surname" PartnerClaimType="family_name"/>
            <OutputClaim ClaimTypeReferenceId="email" PartnerClaimType="email"/>
            <OutputClaim ClaimTypeReferenceId="displayName" PartnerClaimType="name"/>
            <OutputClaim ClaimTypeReferenceId="identityProvider" DefaultValue="contoso.com" />
            <OutputClaim ClaimTypeReferenceId="authenticationSource" DefaultValue="socialIdpAuthentication"/>
          </OutputClaims>
          <OutputClaimsTransformations>
            <OutputClaimsTransformation ReferenceId="CreateRandomUPNUserName"/>
            <OutputClaimsTransformation ReferenceId="CreateUserPrincipalName"/>
            <OutputClaimsTransformation ReferenceId="CreateAlternativeSecurityId"/>
            <OutputClaimsTransformation ReferenceId="CreateSubjectClaimFromAlternativeSecurityId"/>
          </OutputClaimsTransformations>
          <UseTechnicalProfileForSessionManagement ReferenceId="SM-Saml-idp"/>
        </TechnicalProfile>
      </TechnicalProfiles>
    </ClaimsProvider>
    ```

1. Reemplace `your-AD-FS-domain` por el nombre de su dominio de AD FS y reemplace el valor de la notificación de salida **identityProvider** por su DNS (valor arbitrario que indica el dominio).

1. Busque la sección `<ClaimsProviders>` y agregue el siguiente fragmento de código XML. Si la directiva ya contiene el perfil técnico `SM-Saml-idp`, vaya al paso siguiente. Para más información, consulte [Administración de sesión de inicio de sesión único](custom-policy-reference-sso.md).

    ```xml
    <ClaimsProvider>
      <DisplayName>Session Management</DisplayName>
      <TechnicalProfiles>
        <TechnicalProfile Id="SM-Saml-idp">
          <DisplayName>Session Management Provider</DisplayName>
          <Protocol Name="Proprietary" Handler="Web.TPEngine.SSO.SamlSSOSessionProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
          <Metadata>
            <Item Key="IncludeSessionIndex">false</Item>
            <Item Key="RegisterServiceProviders">false</Item>
          </Metadata>
        </TechnicalProfile>
      </TechnicalProfiles>
    </ClaimsProvider>
    ```

1. Guarde el archivo.

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
    <ClaimsExchange Id="ContosoExchange" TechnicalProfileReferenceId="Contoso-SAML2" />
  </ClaimsExchanges>
</OrchestrationStep>
```

[!INCLUDE [active-directory-b2c-configure-relying-party-policy](../../includes/active-directory-b2c-configure-relying-party-policy-user-journey.md)]

## <a name="configure-an-ad-fs-relying-party-trust"></a>Configuración de una relación de confianza para usuario autenticado de AD FS

Para usar AD FS como proveedor de identidades en Azure AD B2C, debe crear una relación de confianza para usuario autenticado de AD FS con los metadatos de SAML de Azure AD B2C. En el ejemplo siguiente se muestra una dirección URL a los metadatos SAML de un perfil técnico de Azure AD B2C:

```
https://your-tenant-name.b2clogin.com/your-tenant-name.onmicrosoft.com/your-policy/samlp/metadata?idptp=your-technical-profile
```

Cuando use un [dominio personalizado](custom-domain.md), use el siguiente formato:

```
https://your-domain-name/your-tenant-name.onmicrosoft.com/your-policy/samlp/metadata?idptp=your-technical-profile
```

Reemplace los siguientes valores:

- **your-tenant-name** por el nombre del inquilino, como inquilino.onmicrosoft.com.
- **your-tenant-name** por el nombre del dominio personalizado, como login.contoso.com.
- **your-policy** por el nombre de la directiva. Por ejemplo, B2C_1A_signup_signin_adfs.
- **your-technical-profile** con el nombre de su perfil técnico del proveedor de identidades de SAML. Por ejemplo, Contoso-SAML2.

Abra un explorador y vaya a esta dirección URL. Asegúrese de escribir la dirección URL correcta y que tenga acceso al archivo XML de metadatos. Para agregar una nueva relación de confianza para usuario autenticado mediante el complemento de administración de AD FS (Servicios de federación de Active Directory) y configurar manualmente los valores, realice el procedimiento siguiente en un servidor de federación. Para realizar este procedimiento, se requiere al menos la pertenencia al grupo **Administradores**, o equivalente, en el equipo local.

1. En el Administrador del servidor, seleccione **Herramientas** y luego **Administración de AD FS**.
2. Seleccione **Agregar confianza para usuario de confianza**.
3. En la **página principal**, elija **Compatible con notificaciones** y seleccione **Iniciar**.
4. En la página **Seleccionar origen de datos**, seleccione **Importar los datos sobre el usuario de confianza publicado en línea o en una red local**, proporcione la dirección URL de metadatos de Azure AD B2C y luego seleccione **Siguiente**.
5. En la página **Especificar nombre para mostrar**, escriba un nombre en **Nombre para mostrar**. En **Notas** escriba una descripción de esta relación de confianza de usuario y luego seleccione **Siguiente**.
6. En **Elegir directiva de control de acceso**, seleccione una directiva y, luego, **Siguiente**.
7. En la página **Listo para agregar confianza**, revise la configuración y luego seleccione **Siguiente** para guardar la información de la relación de confianza para usuario autenticado.
8. En la página **Finalizar**, seleccione **Cerrar**; esta acción muestra automáticamente el cuadro de diálogo **Editar reglas de notificación**.
9. Seleccione **Agregar regla**.
10. En **Claim rule template** (Plantilla de regla de notificación), seleccione **Send LDAP attributes as claims** (Enviar atributos LDAP como notificaciones).
11. Proporcione un valor en **Claim rule name** (Nombre de la regla de notificación). Para **Almacén de atributos**, seleccione **Select Active Directory** (Seleccionar Active Directory), agregue las siguientes notificaciones y seleccione **Finalizar** y luego en **Aceptar**.

    | Atributo LDAP | Tipo de notificación saliente |
    | -------------- | ------------------- |
    | User-Principal-Name | userPrincipalName |
    | Surname | family_name |
    | Given-Name | given_name |
    | E-Mail-Address | email |
    | Display-Name | name |

    Tenga en cuenta que algunos de los nombres no se mostrarán en la lista desplegable de tipo de notificaciones salientes. Debe escribirlos manualmente. (La lista desplegable es editable).

12.  Según el tipo de certificado, es posible que tenga que establecer el algoritmo HASH. En la ventana de propiedades de la relación de usuario de confianza (demo de B2C), haga clic en la pestaña **Avanzado** y cambie el valor de **Algoritmo hash seguro** a `SHA-256` y luego seleccione **Aceptar**.
13. En el Administrador del servidor, seleccione **Herramientas** y luego **Administración de AD FS**.
14. Seleccione la relación de usuario de confianza que creó, seleccione **Actualizar a partir de los metadatos de federación** y luego seleccione **Actualizar**.

## <a name="test-your-custom-policy"></a>Prueba de la directiva personalizada

1. Inicie sesión en [Azure Portal](https://portal.azure.com).
1. Seleccione el icono **Directorio y suscripción** en la barra de herramientas del portal y, luego, elija el directorio que contiene el inquilino de Azure AD B2C.
1. En Azure Portal, busque y seleccione **Azure AD B2C**.
1. En **Directivas**, seleccione **Identity Experience Framework**.
1. Seleccione la directiva de usuarios de confianza, por ejemplo `B2C_1A_signup_signin`.
1. En **Aplicación**, seleccione la aplicación web que [registró anteriormente](tutorial-register-applications.md). La **dirección URL de respuesta** debe mostrar `https://jwt.ms`.
1. Seleccione el botón **Ejecutar ahora**.
1. En la página de registro o de inicio de sesión, seleccione **Contoso AD FS** para iniciar sesión con el proveedor de identidades de Contoso AD FS.

Si el proceso de inicio de sesión se completa correctamente, el explorador se redirige a `https://jwt.ms`, que muestra el contenido del token devuelto por Azure AD B2C.

## <a name="troubleshooting-ad-fs-service"></a>Solución de problemas del servicio de AD FS  

AD FS se configura para utilizar el registro de aplicaciones de Windows. Si experimenta problemas al configurar AD FS como proveedor de identidades de SAML mediante directivas personalizadas en Azure AD B2C, puede que quiera comprobar el registro de eventos de AD FS:

1. En la **barra de búsqueda** de Windows, escriba **Visor de eventos** y, después, seleccione la aplicación de escritorio **Visor de eventos**.
1. Para ver el registro de un equipo diferente, haga clic con el botón derecho en **Visor de eventos (local)** . Seleccione **Conectarse a otro equipo** y rellene los campos para completar el cuadro de diálogo **Seleccionar equipo**.
1. En **Visor de eventos**, abra **Registros de aplicaciones y servicios**.
1. Seleccione **AD FS** y, a continuación, seleccione **Administración**. 
1. Para ver más información acerca de un evento, haga doble clic en el suceso.  

### <a name="saml-request-is-not-signed-with-expected-signature-algorithm-event"></a>La solicitud SAML no está firmada con el evento de algoritmo de firma esperado

Este error indica que la solicitud SAML enviada por Azure AD B2C no está firmada con el algoritmo de firma esperado configurado en AD FS. Por ejemplo, la solicitud SAML está firmada con el algoritmo de firma `rsa-sha256`, pero el algoritmo de firma esperado es `rsa-sha1`. Para corregir este problema, asegúrese de que tanto Azure AD B2C como AD FS estén configurados con el mismo algoritmo de firma.

#### <a name="option-1-set-the-signature-algorithm-in-azure-ad-b2c"></a>Opción 1: Establecer el algoritmo de firma en Azure AD B2C  

Puede configurar cómo firmar la solicitud SAML en Azure AD B2C. Los metadatos de [XmlSignatureAlgorithm](identity-provider-generic-saml.md) controlan el valor del parámetro `SigAlg` (cadena de consulta o parámetro de publicación) en la solicitud SAML. En el ejemplo siguiente se configura Azure AD B2C para usar el algoritmo de firma `rsa-sha256`.

```xml
<Metadata>
  <Item Key="WantsEncryptedAssertions">false</Item>
  <Item Key="PartnerEntity">https://your-AD-FS-domain/federationmetadata/2007-06/federationmetadata.xml</Item>
  <Item Key="XmlSignatureAlgorithm">Sha256</Item>
</Metadata>
```

#### <a name="option-2-set-the-signature-algorithm-in-ad-fs"></a>Opción 2: Establecer el algoritmo de signatura en AD FS 

Como alternativa, puede configurar el algoritmo de firma esperado de la solicitud SAML en AD FS.

1. En el Administrador del servidor, seleccione **Herramientas** y luego **Administración de AD FS**.
1. Seleccione la **Relación de confianza para usuario autenticado** que creó anteriormente.
1. Seleccione **Propiedades** y luego **Avanzado**.
1. Configure el **Algoritmo hash seguro** y seleccione **Aceptar** para guardar los cambios.

::: zone-end
