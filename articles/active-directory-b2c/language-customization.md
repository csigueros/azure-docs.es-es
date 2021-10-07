---
title: Personalización de idioma en Azure Active Directory B2C
description: Más información acerca de cómo personalizar la experiencia de lenguaje en los flujos de usuario en Azure Active Directory B2C.
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
ms.openlocfilehash: 0954f438f21229b16800847f5d248909a61df051
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/24/2021
ms.locfileid: "128573961"
---
# <a name="language-customization-in-azure-active-directory-b2c"></a>Personalización de idioma en Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-choose-user-flow-or-custom-policy](../../includes/active-directory-b2c-choose-user-flow-or-custom-policy.md)]

La personalización de idioma en Azure Active Directory B2C (Azure AD B2C) permite que el flujo de usuario albergue distintos idiomas a fin de satisfacer las necesidades de los clientes. Microsoft proporciona las traducciones de [36 idiomas](#supported-languages), pero el usuario también puede proporcionar sus propias traducciones para cualquier idioma. Incluso si su experiencia se proporciona únicamente para un idioma, puede personalizar cualquier texto de las páginas.

## <a name="how-language-customization-works"></a>¿Cómo funciona la personalización de idioma?

Se utiliza la personalización de idioma para seleccionar los idiomas en los que está disponible el flujo de usuario. Cuando la característica está habilitada, puede proporcionar el parámetro de cadena de consulta, `ui_locales`, desde la aplicación. Al llamar en Azure AD B2C, la página se traduce a la configuración regional que haya indicado. Este tipo de configuración le proporciona un control completo sobre los idiomas del flujo de usuario y omite la configuración de idioma del explorador del cliente.

Quizás no necesite ese nivel de control sobre los idiomas que ve el cliente. Si no proporciona un parámetro `ui_locales`, la experiencia del cliente está determinada por la configuración de su explorador. Aun así, puede controlar los idiomas a los que se traduce el flujo de usuario si los agrega como idioma admitido. Si el explorador del cliente está establecido para mostrar un idioma que no desea admitir, se muestra en su lugar el idioma que ha seleccionado como predeterminado en las referencias culturales admitidas.

* **Idioma especificado por configuraciones regionales de interfaz de usuario**: después de habilitar la personalización de idioma, el recorrido del usuario se traduce al idioma especificado aquí.
* **Idioma solicitado por el explorador**: si no se especificó ningún parámetro `ui_locales`, el recorrido del usuario se traduce al idioma solicitado por el explorador, *si se admite ese idioma*.
* **Idioma predeterminado de directiva**: si el explorador no especifica un idioma, o si especifica uno que no se admite, el recorrido del usuario se traduce al idioma predeterminado de la directiva.

> [!NOTE]
> Si va a usar atributos de usuario personalizados, debe proporcionar sus propias traducciones. Para más información, consulte [Personalización de las cadenas](#customize-your-strings).

Vea este vídeo para saber cómo buscar o personalizar el idioma mediante Azure AD B2C.

>[!Video https://www.youtube.com/embed/yqrX5_tA7Ms]

::: zone pivot="b2c-custom-policy"

La localización requiere tres pasos: 

1. Configurar la lista explícita de idiomas admitidos
1. Proporcionar cadenas y colecciones específicas del idioma
1. Editar la [definición de contenido](contentdefinitions.md) para la página 

::: zone-end 

## <a name="prerequisites"></a>Requisitos previos

[!INCLUDE [active-directory-b2c-customization-prerequisites](../../includes/active-directory-b2c-customization-prerequisites.md)]

::: zone pivot="b2c-user-flow"

## <a name="support-requested-languages-for-ui_locales"></a>Compatibilidad con idiomas solicitados de configuraciones regionales de interfaz de usuario

Las directivas que se crearon antes de la disponibilidad general de la personalización de idioma deben habilitar primero esta característica. Las directivas y los flujos de usuario que se crearon después tienen habilitada de forma predeterminada la personalización de idioma.

La habilitación de la personalización de idioma en un flujo de usuario le permite controlar ahora el idioma del flujo de usuario agregando el parámetro `ui_locales`.

1. En el inquilino de Azure AD B2C, seleccione **Flujos de usuario**.
1. Haga clic en el flujo de usuario que desea habilitar para las traducciones.
1. Seleccione **Idiomas**.
1. Seleccione **Habilitación de la personalización de idioma**.

## <a name="select-which-languages-in-your-user-flow-are-enabled"></a>Seleccione los idiomas que están habilitados en su flujo de usuario.

Habilite un conjunto de idiomas para la traducción del flujo de usuario cuando lo solicite el explorador sin el parámetro `ui_locales`.

1. Asegúrese de que el flujo de usuario tenga habilitada la personalización de idioma según las instrucciones anteriores.
1. En la página **Idiomas** del flujo de usuario, seleccione un idioma que desee admitir.
1. En el panel de propiedades, cambie **Habilitado** a **Sí**.
1. Seleccione **Guardar** en la parte superior del panel de propiedades.

>[!NOTE]
>Si no se proporciona un parámetro `ui_locales`, la página se traduce al idioma del explorador del cliente solo si está habilitado.
>

## <a name="customize-your-strings"></a>Personalización de las cadenas

La personalización de idioma le permite personalizar cualquier cadena del flujo de usuario.

1. Asegúrese de que la directiva tenga habilitada la personalización de idioma según las instrucciones anteriores.
1. En la página **Idiomas** del flujo de usuario, seleccione un idioma que desee personalizar.
1. En **Page-level-resources files** (Archivos de recursos de nivel de página), seleccione la página que desea editar.
1. Seleccione **Descargar valores predeterminados** (o **Descargar invalidaciones** si ha editado anteriormente este idioma).

Estos pasos le proporcionan un archivo JSON que puede usar para comenzar a editar las cadenas.

### <a name="change-any-string-on-the-page"></a>Cambio de cualquier cadena en la página

1. Abra el archivo JSON descargado según las instrucciones anteriores en un editor JSON.
1. Busque el elemento que desea cambiar. Puede encontrar el valor de `StringId` de la cadena que busca o buscar el atributo `Value` que quiere cambiar.
1. Actualice el atributo `Value` con el que quiere que se muestre.
1. Para cada cadena que desea cambiar, cambie `Override` a `true`.
1. Guarde el archivo y cargue los cambios. (Puede encontrar el control de carga en el mismo lugar que donde descargó el archivo JSON).

> [!IMPORTANT]
> Si necesita reemplazar una cadena, asegúrese de establecer el valor `Override` en `true`. Si no se cambia el valor, se omite la entrada.

### <a name="change-extension-attributes"></a>Cambio de los atributos de extensión

Si quiere cambiar la cadena de un atributo de usuario personalizado, o quiere agregar una a JSON, debe estar en el siguiente formato:

```json
{
  "LocalizedStrings": [
    {
      "ElementType": "ClaimType",
      "ElementId": "extension_<ExtensionAttribute>",
      "StringId": "DisplayName",
      "Override": true,
      "Value": "<ExtensionAttributeValue>"
    }
    [...]
  ]
}
```

Reemplace `<ExtensionAttribute>` por el nombre de su atributo de usuario personalizado.

Reemplace `<ExtensionAttributeValue>` por la nueva cadena que se mostrará.

### <a name="provide-a-list-of-values-by-using-localizedcollections"></a>Entrega de una lista de valores mediante LocalizedCollections

Si desea proporcionar una lista establecida de valores para respuestas, debe crear un atributo `LocalizedCollections`. `LocalizedCollections` es una matriz de pares de `Name` y `Value`. El orden de los elementos será el orden en el que se muestran. Para agregar `LocalizedCollections`, utilice el siguiente formato:

```json
{
  "LocalizedStrings": [...],
  "LocalizedCollections": [
    {
      "ElementType":"ClaimType",
      "ElementId":"<UserAttribute>",
      "TargetCollection":"Restriction",
      "Override": true,
      "Items":[
        {
          "Name":"<Response1>",
          "Value":"<Value1>"
        },
        {
          "Name":"<Response2>",
          "Value":"<Value2>"
        }
      ]
    }
  ]
}
```

* `ElementId` es el atributo de usuario para el que este atributo `LocalizedCollections` es una respuesta.
* `Name` es el valor que se muestra al usuario.
* `Value` es lo que se devuelve en la notificación cuando se selecciona esta opción.

### <a name="upload-your-changes"></a>Carga de los cambios

1. Una vez completados los cambios en el archivo JSON, vuelva al inquilino B2C.
1. Seleccione **Flujos de usuario** y haga clic en el flujo de usuario que desea habilitar para las traducciones.
1. Seleccione **Idiomas**.
1. Seleccione el idioma al que quiere traducir.
1. Seleccione la página en donde quiere proporcionar traducciones.
1. Seleccione el icono de carpeta y el archivo JSON para cargar.

Este cambio se guarda en el flujo de usuario automáticamente.

## <a name="customize-the-page-ui-by-using-language-customization"></a>Personalización de la interfaz de usuario de la página mediante la personalización de idioma

Hay dos maneras de localizar el [contenido HTML](customize-ui-with-html.md). Una manera consiste en activar la [personalización de idioma](language-customization.md). Al habilitar esta característica, Azure AD B2C puede reenviar el parámetro de OpenID Connect, `ui-locales`, al punto de conexión. El servidor de contenido puede usar este parámetro para proporcionar páginas HTML personalizadas específicas del idioma.

Como alternativa, puede extraer contenido de distintos lugares, según la configuración regional usada. En el punto de conexión habilitado para CORS, puede configurar una estructura de carpetas para hospedar contenido de idiomas concretos. Se llamará al adecuado si usa el valor de carácter comodín `{Culture:RFC5646}`. Por ejemplo, supongamos que se trata del URI de la página personalizada:

```
https://wingtiptoysb2c.blob.core.windows.net/{Culture:RFC5646}/wingtip/unified.html
```

Puede cargar la página en `fr`. Cuando la página extrae contenido HTML y CSS, se extrae desde:

```
https://wingtiptoysb2c.blob.core.windows.net/fr/wingtip/unified.html
```

## <a name="add-custom-languages"></a>Incorporación de idiomas personalizados

También puede agregar idiomas para los que Microsoft no proporciona actualmente traducciones. Deberá proporcionar las traducciones para todas las cadenas del flujo de usuario. Los idiomas y los códigos de configuración regional se limitan a los indicados en la norma ISO 639-1. El formato de código de configuración regional debe ser "ISO_639-1_code"-"CountryCode", por ejemplo `en-GB`. Para obtener más información, consulte [Formatos de Id. de la configuración regional](/openspecs/office_standards/ms-oe376/6c085406-a698-4e12-9d4d-c3b0ee3dbc4a).

1. En el inquilino de Azure AD B2C, seleccione **Flujos de usuario**.
2. Haga clic en el flujo de usuario donde desea agregar idiomas personalizados y, luego, haga clic en **Idiomas**.
3. Seleccione **Agregar un idioma personalizado** en la parte superior de la página.
4. En el panel de contexto que se abre, identifique el idioma para el que va a proporcionar traducciones mediante la especificación de un código de configuración regional válido.
5. Puede descargar para cada página un conjunto de invalidaciones para inglés y trabajar en las traducciones.
6. Después de haber terminado con los archivos JSON, puede cargarlos para cada página.
7. Seleccione **Habilitar** y el flujo de usuario mostrará ahora este idioma para sus usuarios.
8. Guarde el idioma.

>[!IMPORTANT]
>Debe habilitar los idiomas personalizados o cargar invalidaciones para poder guardar.

::: zone-end

::: zone pivot="b2c-custom-policy"

## <a name="set-up-the-list-of-supported-languages"></a>Configuración de una lista de los idiomas admitidos

Abra el archivo de extensiones de la directiva. Por ejemplo, <em>`SocialAndLocalAccounts/` **`TrustFrameworkExtensions.xml`**</em>.

1. Busque el elemento [BuildingBlocks](buildingblocks.md). Si el elemento no existe, agréguelo.
1. Agregue el elemento `Localization` con los idiomas admitidos: Inglés (predeterminado) y español.  


```xml
<Localization Enabled="true">
  <SupportedLanguages DefaultLanguage="en" MergeBehavior="ReplaceAll">
    <SupportedLanguage>en</SupportedLanguage>
    <SupportedLanguage>es</SupportedLanguage>
  </SupportedLanguages>
</Localization>
```

## <a name="provide-language-specific-labels"></a>Proporcionar etiquetas específicas del idioma

[LocalizedResources](localization.md#localizedresources) del elemento `Localization` contiene la lista de cadenas localizadas. El elemento de recursos localizados tiene un identificador que se utiliza para identificar de manera única los recursos localizados. Este identificador se usa posteriormente en el elemento de [definición de contenido](contentdefinitions.md).

Configura los elementos de recursos localizados para la definición de contenido y cualquier idioma que quiera admitir. Para personalizar las páginas de registro o de inicio de sesión unificadas para inglés y español, agregue los siguientes elementos `LocalizedResources` después del cierre del elemento `</SupportedLanguages>`.

> [!NOTE]
> En el ejemplo siguiente se ha agregado el símbolo de almohadilla `#` al comienzo de cada línea, por lo que puede encontrar fácilmente las etiquetas localizadas en la pantalla.

```xml
<!--Local account sign-up or sign-in page English-->
<Localization Enabled="true">
  ...
  <LocalizedResources Id="api.signuporsignin.en">
    <LocalizedStrings>
      <LocalizedString ElementType="UxElement" StringId="logonIdentifier_email">#Email Address</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="requiredField_email">#Please enter your email</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="logonIdentifier_username">#Username</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="password">#Password</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="createaccount_link">#Sign up now</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="requiredField_username">#Please enter your user name</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="createaccount_intro">#Don't have an account?</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="forgotpassword_link">#Forgot your password?</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="divider_title">#OR</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="cancel_message">#The user has forgotten their password</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="button_signin">#Sign in</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="social_intro">#Sign in with your social account</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="requiredField_password">#Please enter your password</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="invalid_password">#The password you entered is not in the expected format.</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="local_intro_username">#Sign in with your user name</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="local_intro_email">#Sign in with your existing account</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="invalid_email">#Please enter a valid email address</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="unknown_error">#We are having trouble signing you in. Please try again later.</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="email_pattern">^[a-zA-Z0-9.!#$%&amp;'^_`{}~-]+@[a-zA-Z0-9-]+(?:\.[a-zA-Z0-9-]+)*$</LocalizedString>
      <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfInvalidPassword">#Your password is incorrect.</LocalizedString>
      <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfClaimsPrincipalDoesNotExist">#We can't seem to find your account.</LocalizedString>
      <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfOldPasswordUsed">#Looks like you used an old password.</LocalizedString>
      <LocalizedString ElementType="ErrorMessage" StringId="DefaultMessage">#Invalid username or password.</LocalizedString>
      <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfUserAccountDisabled">#Your account has been locked. Contact your support person to unlock it, then try again.</LocalizedString>
      <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfUserAccountLocked">#Your account is temporarily locked to prevent unauthorized use. Try again later.</LocalizedString>
      <LocalizedString ElementType="ErrorMessage" StringId="AADRequestsThrottled">#There are too many requests at this moment. Please wait for some time and try again.</LocalizedString>
    </LocalizedStrings>
  </LocalizedResources>
  <!--Local account sign-up or sign-in page Spanish-->
  <LocalizedResources Id="api.signuporsignin.es">
    <LocalizedStrings>
      <LocalizedString ElementType="UxElement" StringId="logonIdentifier_email">#Correo electrónico</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="requiredField_email">#Este campo es obligatorio</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="logonIdentifier_username">#Nombre de usuario</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="password">#Contraseña</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="createaccount_link">#Registrarse ahora</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="requiredField_username">#Escriba su nombre de usuario</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="createaccount_intro">#¿No tiene una cuenta?</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="forgotpassword_link">#¿Olvidó su contraseña?</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="divider_title">#O</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="cancel_message">#El usuario ha olvidado su contraseña</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="button_signin">#Iniciar sesión</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="social_intro">#Iniciar sesión con su cuenta de redes sociales</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="requiredField_password">#Escriba su contraseña</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="invalid_password">#La contraseña que ha escrito no está en el formato esperado.</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="local_intro_username">#Iniciar sesión con su nombre de usuario</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="local_intro_email">#Iniciar sesión con su cuenta existente</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="invalid_email">#Escriba una dirección de correo electrónico válida</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="unknown_error">#Tenemos problemas para iniciar su sesión. Vuelva a intentarlo más tarde.  </LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="email_pattern">^[a-zA-Z0-9.!#$%&amp;'^_`{}~-]+@[a-zA-Z0-9-]+(?:\.[a-zA-Z0-9-]+)*$</LocalizedString>
      <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfInvalidPassword">#Su contraseña es incorrecta.</LocalizedString>
      <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfClaimsPrincipalDoesNotExist">#Parece que no podemos encontrar su cuenta.</LocalizedString>
      <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfOldPasswordUsed">#Parece que ha usado una contraseña antigua.</LocalizedString>
      <LocalizedString ElementType="ErrorMessage" StringId="DefaultMessage">#El nombre de usuario o la contraseña no son válidos.</LocalizedString>
      <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfUserAccountDisabled">#Se bloqueó su cuenta. Póngase en contacto con la persona responsable de soporte técnico para desbloquearla y vuelva a intentarlo.</LocalizedString>
      <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfUserAccountLocked">#Su cuenta se bloqueó temporalmente para impedir un uso no autorizado. Vuelva a intentarlo más tarde.</LocalizedString>
      <LocalizedString ElementType="ErrorMessage" StringId="AADRequestsThrottled">#Hay demasiadas solicitudes en este momento. Espere un momento y vuelva a intentarlo.</LocalizedString>
    </LocalizedStrings>
  </LocalizedResources>
  <!--Local account sign-up page English-->
  <LocalizedResources Id="api.localaccountsignup.en">
    <LocalizedStrings>
      <LocalizedString ElementType="ClaimType" ElementId="email" StringId="DisplayName">#Email Address</LocalizedString>
      <LocalizedString ElementType="ClaimType" ElementId="email" StringId="UserHelpText">#Email address that can be used to contact you.</LocalizedString>
      <LocalizedString ElementType="ClaimType" ElementId="email" StringId="PatternHelpText">#Please enter a valid email address.</LocalizedString>
      <LocalizedString ElementType="ClaimType" ElementId="newPassword" StringId="DisplayName">#New Password</LocalizedString>
      <LocalizedString ElementType="ClaimType" ElementId="newPassword" StringId="UserHelpText">#Enter new password</LocalizedString>
      <LocalizedString ElementType="ClaimType" ElementId="newPassword" StringId="PatternHelpText">#8-16 characters, containing 3 out of 4 of the following: Lowercase characters, uppercase characters, digits (0-9), and one or more of the following symbols: @ # $ % ^ &amp; * - _ + = [ ] { } | \ : ' , ? / ` ~ " ( ) ; .</LocalizedString>
      <LocalizedString ElementType="ClaimType" ElementId="reenterPassword" StringId="DisplayName">#Confirm New Password</LocalizedString>
      <LocalizedString ElementType="ClaimType" ElementId="reenterPassword" StringId="UserHelpText">#Confirm new password</LocalizedString>
      <LocalizedString ElementType="ClaimType" ElementId="reenterPassword" StringId="PatternHelpText">#8-16 characters, containing 3 out of 4 of the following: Lowercase characters, uppercase characters, digits (0-9), and one or more of the following symbols: @ # $ % ^ &amp; * - _ + = [ ] { } | \ : ' , ? / ` ~ " ( ) ; .</LocalizedString>
      <LocalizedString ElementType="ClaimType" ElementId="displayName" StringId="DisplayName">#Display Name</LocalizedString>
      <LocalizedString ElementType="ClaimType" ElementId="displayName" StringId="UserHelpText">#Your display name.</LocalizedString>
      <LocalizedString ElementType="ClaimType" ElementId="surname" StringId="DisplayName">#Surname</LocalizedString>
      <LocalizedString ElementType="ClaimType" ElementId="surname" StringId="UserHelpText">#Your surname (also known as family name or last name).  </LocalizedString>
      <LocalizedString ElementType="ClaimType" ElementId="givenName" StringId="DisplayName">#Given Name</LocalizedString>
      <LocalizedString ElementType="ClaimType" ElementId="givenName" StringId="UserHelpText">#Your given name (also known as first name).</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="button_continue">#Create</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="error_fieldIncorrect">#One or more fields are filled out incorrectly. Please check your entries and try again.</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="error_passwordEntryMismatch">#The password entry fields do not match. Please enter the same password in both fields and try again.</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="error_requiredFieldMissing">#A required field is missing. Please fill out all required fields and try again.</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="helplink_text">#What is this?</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="initial_intro">#Please provide the following details.</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="preloader_alt">#Please wait</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="required_field">#This information is required.</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="ver_but_edit">#Change e-mail</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="ver_but_resend">#Send new code</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="ver_but_send">#Send verification code</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="ver_but_verify">#Verify code</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="ver_fail_code_expired">#That code is expired. Please request a new code.</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="ver_fail_no_retry">#You've made too many incorrect attempts. Please try again later.</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="ver_fail_retry">#That code is incorrect. Please try again.</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="ver_fail_server">#We are having trouble verifying your email address. Please enter a valid email address and try again.</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="ver_fail_throttled">#There have been too many requests to verify this email address. Please wait a while, then try again.</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="ver_info_msg">#Verification code has been sent to your inbox. Please copy it to the input box below.</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="ver_input">#Verification code</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="ver_intro_msg">#Verification is necessary. Please click Send button.</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="ver_success_msg">#E-mail address verified. You can now continue.</LocalizedString>
      <LocalizedString ElementType="ErrorMessage" StringId="ServiceThrottled">#There are too many requests at this moment. Please wait for some time and try again.</LocalizedString>
      <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfClaimNotVerified">#Claim not verified: {0}</LocalizedString>
      <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfClaimsPrincipalAlreadyExists">#A user with the specified ID already exists. Please choose a different one.</LocalizedString>
      <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfIncorrectPattern">#Incorrect pattern for: {0}</LocalizedString>
      <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfInvalidInput">#{0} has invalid input.</LocalizedString>
      <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfMissingRequiredElement">#Missing required element: {0}</LocalizedString>
      <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfValidationError">#Error in validation by: {0}</LocalizedString>
    </LocalizedStrings>
  </LocalizedResources>
  <!--Local account sign-up page Spanish-->
  <LocalizedResources Id="api.localaccountsignup.es">
    <LocalizedStrings>
      <LocalizedString ElementType="ClaimType" ElementId="email" StringId="DisplayName">#Dirección de correo electrónico</LocalizedString>
      <LocalizedString ElementType="ClaimType" ElementId="email" StringId="UserHelpText">#Dirección de correo electrónico que puede usarse para ponerse en contacto con usted.</LocalizedString>
      <LocalizedString ElementType="ClaimType" ElementId="email" StringId="PatternHelpText">#Introduzca una dirección de correo electrónico válida.  </LocalizedString>
      <LocalizedString ElementType="ClaimType" ElementId="newPassword" StringId="DisplayName">#Nueva contraseña</LocalizedString>
      <LocalizedString ElementType="ClaimType" ElementId="newPassword" StringId="UserHelpText">#Escriba la contraseña nueva</LocalizedString>
      <LocalizedString ElementType="ClaimType" ElementId="newPassword" StringId="PatternHelpText">#De 8 a 16 caracteres, que contengan 3 de los 4 tipos siguientes: caracteres en minúsculas, caracteres en mayúsculas, dígitos (0-9) y uno o más de los siguientes símbolos: @ # $ % ^ &amp; * - _ + = [ ] { } | \\ : ' , ? / ` ~ \" ( ) ; .</LocalizedString>
      <LocalizedString ElementType="ClaimType" ElementId="reenterPassword" StringId="DisplayName">#Confirmar nueva contraseña</LocalizedString>
      <LocalizedString ElementType="ClaimType" ElementId="reenterPassword" StringId="UserHelpText">#Confirmar nueva contraseña</LocalizedString>
      <LocalizedString ElementType="ClaimType" ElementId="reenterPassword" StringId="PatternHelpText">#8 a 16 caracteres, que contengan 3 de los 4 tipos siguientes: caracteres en minúsculas, caracteres en mayúsculas, dígitos (0-9) y uno o más de los siguientes símbolos: @ # $ % ^ &amp; * - _ + = [ ] { } | \\ : ' , ? / ` ~ \" ( ) ; .</LocalizedString>
      <LocalizedString ElementType="ClaimType" ElementId="displayName" StringId="DisplayName">#Nombre para mostrar</LocalizedString>
      <LocalizedString ElementType="ClaimType" ElementId="displayName" StringId="UserHelpText">#Su nombre para mostrar.</LocalizedString>
      <LocalizedString ElementType="ClaimType" ElementId="surname" StringId="DisplayName">#Apellido</LocalizedString>
      <LocalizedString ElementType="ClaimType" ElementId="surname" StringId="UserHelpText">#Su apellido.</LocalizedString>
      <LocalizedString ElementType="ClaimType" ElementId="givenName" StringId="DisplayName">#Nombre</LocalizedString>
      <LocalizedString ElementType="ClaimType" ElementId="givenName" StringId="UserHelpText">#Su nombre (también conocido como nombre de pila).</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="button_continue">#Crear</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="error_fieldIncorrect">#Hay uno o varios campos rellenados de forma incorrecta. Compruebe las entradas y vuelva a intentarlo.</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="error_passwordEntryMismatch">#Los campos de entrada de contraseña no coinciden. Escriba la misma contraseña en ambos campos y vuelva a intentarlo.</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="error_requiredFieldMissing">#Falta un campo obligatorio. Rellene todos los campos necesarios y vuelva a intentarlo.</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="helplink_text">#¿Qué es esto?</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="initial_intro">#Proporcione los siguientes detalles.</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="preloader_alt">#Espere</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="required_field">#Esta información es obligatoria.</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="ver_but_edit">#Cambiar correo electrónico</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="ver_but_resend">#Enviar nuevo código</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="ver_but_send">#Enviar código de comprobación</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="ver_but_verify">#Comprobar código</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="ver_fail_code_expired">#El código ha expirado. Solicite otro nuevo.</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="ver_fail_no_retry">#Ha realizado demasiados intentos incorrectos. Vuelva a intentarlo más tarde.</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="ver_fail_retry">#Ese código es incorrecto. Inténtelo de nuevo.</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="ver_fail_server">#Tenemos problemas para comprobar la dirección de correo electrónico. Escriba una dirección de correo electrónico válida y vuelva a intentarlo.</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="ver_fail_throttled">#Ha habido demasiadas solicitudes para comprobar esta dirección de correo electrónico. Espere un poco y vuelva a intentarlo.</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="ver_info_msg">#Se ha enviado el código de verificación a su Bandeja de entrada. Cópielo en el siguiente cuadro de entrada.</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="ver_input">#Código de verificación</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="ver_intro_msg">#La comprobación es obligatoria. Haga clic en el botón Enviar.</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="ver_success_msg">#Dirección de correo electrónico comprobada. Puede continuar.</LocalizedString>
      <LocalizedString ElementType="ErrorMessage" StringId="ServiceThrottled">#Hay demasiadas solicitudes en este momento. Espere un momento y vuelva a intentarlo.</LocalizedString>
      <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfClaimNotVerified">#Reclamación no comprobada: {0}</LocalizedString>
      <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfClaimsPrincipalAlreadyExists">#Ya existe un usuario con el id. especificado. Elija otro diferente.</LocalizedString>
      <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfIncorrectPattern">#Patrón incorrecto para: {0}</LocalizedString>
      <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfInvalidInput">#{0} tiene una entrada no válida.</LocalizedString>
      <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfMissingRequiredElement">#Falta un elemento obligatorio: {0}</LocalizedString>
      <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfValidationError">#Error en la validación de: {0}</LocalizedString>
    </LocalizedStrings>
  </LocalizedResources>
</Localization>
```

## <a name="edit-the-content-definition-with-the-localization"></a>Edición de la definición de contenido con la localización

Pegue todo el contenido del elemento ContentDefinitions que ha copiado como elemento secundario del elemento BuildingBlocks.

En el ejemplo siguiente, las cadenas personalizadas inglés (en) y español (es) se agregan a la página de registro o de inicio de sesión y a la página de registro de la cuenta local. El elemento **LocalizedResourcesReferenceId** por cada elemento **LocalizedResourcesReference** es el mismo que la configuración regional, pero puede usarse cualquier cadena como el identificador. Por cada combinación de idioma y página, apunte al elemento **LocalizedResources** correspondiente que ha creado anteriormente.

```xml
<ContentDefinitions>
  <ContentDefinition Id="api.signuporsignin">
    <LocalizedResourcesReferences MergeBehavior="Prepend">
        <LocalizedResourcesReference Language="en" LocalizedResourcesReferenceId="api.signuporsignin.en" />
        <LocalizedResourcesReference Language="es" LocalizedResourcesReferenceId="api.signuporsignin.es" />
    </LocalizedResourcesReferences>
  </ContentDefinition>

  <ContentDefinition Id="api.localaccountsignup">
    <LocalizedResourcesReferences MergeBehavior="Prepend">
        <LocalizedResourcesReference Language="en" LocalizedResourcesReferenceId="api.localaccountsignup.en" />
        <LocalizedResourcesReference Language="es" LocalizedResourcesReferenceId="api.localaccountsignup.es" />
    </LocalizedResourcesReferences>
  </ContentDefinition>
</ContentDefinitions>
```

## <a name="upload-and-test-your-updated-custom-policy"></a>Carga y prueba de la directiva personalizada actualizada

### <a name="upload-the-custom-policy"></a>Carga de la directiva personalizada

1. Guarde el archivo de extensiones.
1. Asegúrese de que usa el directorio que contiene el inquilino de Azure AD B2C. Seleccione el icono **Directorios y suscripciones** en la barra de herramientas del portal.
1. En la página **Configuración del portal | Directorios y suscripciones**, busque el directorio de Azure AD B2C en la lista **Nombre de directorio** y seleccione **Cambiar**.
1. Busque y seleccione **Azure AD B2C**.
1. En **Directivas**, seleccione **Identity Experience Framework**.
1. Seleccione **Cargar directiva personalizada**.
1. Cargue el archivo de extensiones que cambió anteriormente.

### <a name="test-the-custom-policy-by-using-run-now"></a>Prueba de la directiva personalizada con **Ejecutar ahora**

1. Seleccione la directiva cargada y, a continuación, **Ejecutar ahora**.
1. Debería poder ver la página de registro o de inicio de sesión localizada.
1. Haga clic en el vínculo de registro y podrá ver la página de registro localizada.
1. Cambie el idioma predeterminado del explorador a español. O bien, puede agregar el parámetro de cadena de consulta, `ui_locales`, a la solicitud de autorización. Por ejemplo: 

```http
https://<tenant-name>.b2clogin.com/<tenant-name>.onmicrosoft.com/B2C_1A_signup_signin/oauth2/v2.0/authorize&client_id=0239a9cc-309c-4d41-12f1-31299feb2e82&nonce=defaultNonce&redirect_uri=https%3A%2F%2Fjwt.ms&scope=openid&response_type=id_token&prompt=login&ui_locales=es
```

::: zone-end


## <a name="additional-information"></a>Información adicional

::: zone pivot="b2c-user-flow"

### <a name="page-ui-customization-labels-as-overrides"></a>Etiquetas de personalización de la interfaz de usuario de página como invalidaciones

Cuando se habilita la personalización de idioma, las ediciones anteriores de etiquetas que usan la personalización de la interfaz de usuario de página se almacenan en un archivo JSON para inglés (en). Para seguir cambiando las etiquetas y otras cadenas, cargue los recursos de idioma en Personalización de idioma.

::: zone-end

### <a name="up-to-date-translations"></a>Actualización de traducciones

Microsoft se compromete a proporcionar las traducciones más actualizadas para que haga uso de ellas. Microsoft mejora continuamente las traducciones y garantiza su cumplimiento. Microsoft identificará errores y cambios en la terminología global y creará actualizaciones que funcionen perfectamente en su flujo de usuario.

### <a name="support-for-right-to-left-languages"></a>Compatibilidad con idiomas que se leen de derecha a izquierda

Microsoft no proporciona actualmente compatibilidad con idiomas que se leen de derecha a izquierda. Para ello, puede usar configuraciones regionales personalizadas y CSS para cambiar la manera en la que se muestran las cadenas. Si necesita esta característica, vote por ella en [Comentarios de Azure](https://feedback.azure.com/forums/169401-azure-active-directory/suggestions/19393000-provide-language-support-for-right-to-left-languag).

### <a name="social-identity-provider-translations"></a>Traducciones de proveedores de identidades sociales

Microsoft proporciona el parámetro OIDC `ui_locales` a los inicios de sesión de redes sociales. Pero algunos proveedores de identidades de redes sociales, incluidas Facebook y Google, no los respetan.

### <a name="browser-behavior"></a>Comportamiento del explorador

Tanto Chrome como Firefox solicitan su idioma establecido. Si es un idioma admitido, se muestra antes el valor predeterminado. Microsoft Edge no solicita actualmente un idioma y va directamente al predeterminado.

## <a name="supported-languages"></a>Idiomas compatibles

Azure AD B2C incluye compatibilidad con los idiomas siguientes. Azure AD B2C proporciona los idiomas de flujo de usuario. [MFA de Azure AD](../active-directory/authentication/concept-mfa-howitworks.md) proporciona los lenguajes de notificación de autenticación multifactor (MFA).

| Idioma              | Código de lenguaje | Flujos de usuario         | Notificaciones de MFA  |
|-----------------------| :-----------: | :----------------: | :----------------: |
| Árabe                | ar            | ![X indica No.](./media/user-flow-language-customization/no.png) | ![Marca de verificación verde.](./media/user-flow-language-customization/yes.png) |
| Búlgaro             | bg            | ![X indica No.](./media/user-flow-language-customization/no.png) | ![Marca de verificación verde.](./media/user-flow-language-customization/yes.png) |
| Bengalí                | bn            | ![Marca de verificación verde.](./media/user-flow-language-customization/yes.png) | ![X indica No.](./media/user-flow-language-customization/no.png) |
| Catalán               | ca            | ![X indica No.](./media/user-flow-language-customization/no.png) | ![Marca de verificación verde.](./media/user-flow-language-customization/yes.png) |
| Checo                 | cs            | ![Marca de verificación verde.](./media/user-flow-language-customization/yes.png) | ![Marca de verificación verde.](./media/user-flow-language-customization/yes.png) |
| Danés                | da            | ![Marca de verificación verde.](./media/user-flow-language-customization/yes.png) | ![Marca de verificación verde.](./media/user-flow-language-customization/yes.png) |
| Alemán                | de            | ![Marca de verificación verde.](./media/user-flow-language-customization/yes.png) | ![Marca de verificación verde.](./media/user-flow-language-customization/yes.png) |
| Griego                 | el            | ![Marca de verificación verde.](./media/user-flow-language-customization/yes.png) | ![Marca de verificación verde.](./media/user-flow-language-customization/yes.png) |
| Inglés               | en            | ![Marca de verificación verde.](./media/user-flow-language-customization/yes.png) | ![Marca de verificación verde.](./media/user-flow-language-customization/yes.png) |
| Español               | es            | ![Marca de verificación verde.](./media/user-flow-language-customization/yes.png) | ![Marca de verificación verde.](./media/user-flow-language-customization/yes.png) |
| Estonio              | et            | ![X indica No.](./media/user-flow-language-customization/no.png) | ![Marca de verificación verde.](./media/user-flow-language-customization/yes.png) |
| Vasco                | eu            | ![X indica No.](./media/user-flow-language-customization/no.png) | ![Marca de verificación verde.](./media/user-flow-language-customization/yes.png) |
| Finés               | fi            | ![Marca de verificación verde.](./media/user-flow-language-customization/yes.png) | ![Marca de verificación verde.](./media/user-flow-language-customization/yes.png) |
| Francés                | fr            | ![Marca de verificación verde.](./media/user-flow-language-customization/yes.png) | ![Marca de verificación verde.](./media/user-flow-language-customization/yes.png) |
| Gallego              | gl            | ![X indica No.](./media/user-flow-language-customization/no.png) | ![Marca de verificación verde.](./media/user-flow-language-customization/yes.png) |
| Gujarati              | gu            | ![Marca de verificación verde.](./media/user-flow-language-customization/yes.png) | ![X indica No.](./media/user-flow-language-customization/no.png) |
| Hebreo                | he            | ![X indica No.](./media/user-flow-language-customization/no.png) | ![Marca de verificación verde.](./media/user-flow-language-customization/yes.png) |
| Hindi                 | hi            | ![Marca de verificación verde.](./media/user-flow-language-customization/yes.png) | ![Marca de verificación verde.](./media/user-flow-language-customization/yes.png) |
| Croata              | hr            | ![Marca de verificación verde.](./media/user-flow-language-customization/yes.png) | ![Marca de verificación verde.](./media/user-flow-language-customization/yes.png) |
| Húngaro             | hu            | ![Marca de verificación verde.](./media/user-flow-language-customization/yes.png) | ![Marca de verificación verde.](./media/user-flow-language-customization/yes.png) |
| Indonesio            | id            | ![X indica No.](./media/user-flow-language-customization/no.png) | ![Marca de verificación verde.](./media/user-flow-language-customization/yes.png) |
| Italiano               | it            | ![Marca de verificación verde.](./media/user-flow-language-customization/yes.png) | ![Marca de verificación verde.](./media/user-flow-language-customization/yes.png) |
| Japonés              | ja            | ![Marca de verificación verde.](./media/user-flow-language-customization/yes.png) | ![Marca de verificación verde.](./media/user-flow-language-customization/yes.png) |
| Kazajo                | kk            | ![X indica No.](./media/user-flow-language-customization/no.png) | ![Marca de verificación verde.](./media/user-flow-language-customization/yes.png) |
| Canarés               | kn            | ![Marca de verificación verde.](./media/user-flow-language-customization/yes.png) | ![X indica No.](./media/user-flow-language-customization/no.png) |
| Coreano                | ko            | ![Marca de verificación verde.](./media/user-flow-language-customization/yes.png) | ![Marca de verificación verde.](./media/user-flow-language-customization/yes.png) |
| Lituano            | lt            | ![X indica No.](./media/user-flow-language-customization/no.png) | ![Marca de verificación verde.](./media/user-flow-language-customization/yes.png) |
| Letón               | lv            | ![X indica No.](./media/user-flow-language-customization/no.png) | ![Marca de verificación verde.](./media/user-flow-language-customization/yes.png) |
| Malayalam             | ml            | ![Marca de verificación verde.](./media/user-flow-language-customization/yes.png) | ![X indica No.](./media/user-flow-language-customization/no.png) |
| Maratí               | mr            | ![Marca de verificación verde.](./media/user-flow-language-customization/yes.png) | ![X indica No.](./media/user-flow-language-customization/no.png) |
| Malayo                 | ms            | ![Marca de verificación verde.](./media/user-flow-language-customization/yes.png) | ![Marca de verificación verde.](./media/user-flow-language-customization/yes.png) |
| Noruego Bokmal      | nb            | ![Marca de verificación verde.](./media/user-flow-language-customization/yes.png) | ![X indica No.](./media/user-flow-language-customization/no.png) |
| Neerlandés                 | nl            | ![Marca de verificación verde.](./media/user-flow-language-customization/yes.png) | ![Marca de verificación verde.](./media/user-flow-language-customization/yes.png) |
| Noruego             | no            | ![X indica No.](./media/user-flow-language-customization/no.png) | ![Marca de verificación verde.](./media/user-flow-language-customization/yes.png) |
| Punjabi               | pa            | ![Marca de verificación verde.](./media/user-flow-language-customization/yes.png) | ![X indica No.](./media/user-flow-language-customization/no.png) |
| Polaco                | pl            | ![Marca de verificación verde.](./media/user-flow-language-customization/yes.png) | ![Marca de verificación verde.](./media/user-flow-language-customization/yes.png) |
| Portugués (Brasil)   | pt-br         | ![Marca de verificación verde.](./media/user-flow-language-customization/yes.png) | ![Marca de verificación verde.](./media/user-flow-language-customization/yes.png) |
| Portugués (Portugal) | pt-pt         | ![Marca de verificación verde.](./media/user-flow-language-customization/yes.png) | ![Marca de verificación verde.](./media/user-flow-language-customization/yes.png) |
| Rumano              | ro            | ![Marca de verificación verde.](./media/user-flow-language-customization/yes.png) | ![Marca de verificación verde.](./media/user-flow-language-customization/yes.png) |
| Ruso               | ru            | ![Marca de verificación verde.](./media/user-flow-language-customization/yes.png) | ![Marca de verificación verde.](./media/user-flow-language-customization/yes.png) |
| Eslovaco                | sk            | ![Marca de verificación verde.](./media/user-flow-language-customization/yes.png) | ![Marca de verificación verde.](./media/user-flow-language-customization/yes.png) |
| Esloveno             | sl            | ![X indica No.](./media/user-flow-language-customization/no.png) | ![Marca de verificación verde.](./media/user-flow-language-customization/yes.png) |
| Serbio (cirílico)    | sr-cryl-cs    | ![X indica No.](./media/user-flow-language-customization/no.png) | ![Marca de verificación verde.](./media/user-flow-language-customization/yes.png) |
| Serbio (latino)       | sr-latn-cs    | ![X indica No.](./media/user-flow-language-customization/no.png) | ![Marca de verificación verde.](./media/user-flow-language-customization/yes.png) |
| Sueco               | sv            | ![Marca de verificación verde.](./media/user-flow-language-customization/yes.png) | ![Marca de verificación verde.](./media/user-flow-language-customization/yes.png) |
| Tamil                 | ta            | ![Marca de verificación verde.](./media/user-flow-language-customization/yes.png) | ![X indica No.](./media/user-flow-language-customization/no.png) |
| Telugu                | te            | ![Marca de verificación verde.](./media/user-flow-language-customization/yes.png) | ![X indica No.](./media/user-flow-language-customization/no.png) |
| Tailandés                  | th            | ![Marca de verificación verde.](./media/user-flow-language-customization/yes.png) | ![Marca de verificación verde.](./media/user-flow-language-customization/yes.png) |
| Turco               | tr            | ![Marca de verificación verde.](./media/user-flow-language-customization/yes.png) | ![Marca de verificación verde.](./media/user-flow-language-customization/yes.png) |
| Ucraniano             | uk            | ![X indica No.](./media/user-flow-language-customization/no.png) | ![Marca de verificación verde.](./media/user-flow-language-customization/yes.png) |
| Vietnamita            | vi            | ![X indica No.](./media/user-flow-language-customization/no.png) | ![Marca de verificación verde.](./media/user-flow-language-customization/yes.png) |
| Chino (simplificado)  | zh-hans       | ![Marca de verificación verde.](./media/user-flow-language-customization/yes.png) | ![Marca de verificación verde.](./media/user-flow-language-customization/yes.png) |
| Chino (tradicional) | zh-hant       | ![Marca de verificación verde.](./media/user-flow-language-customization/yes.png) | ![Marca de verificación verde.](./media/user-flow-language-customization/yes.png) |


## <a name="next-steps"></a>Pasos siguientes

::: zone pivot="b2c-user-flow"

Encuentre más información sobre cómo personalizar la interfaz de usuario de las aplicaciones en [Personalización de la interfaz de usuario de la aplicación en Azure Active Directory B2C](customize-ui-with-html.md).

::: zone-end 

::: zone pivot="b2c-custom-policy"

- Más información sobre el elemento [localization](localization.md) en la referencia de IEF.
- Consulte la lista de [identificadores de cadena de localización](localization-string-ids.md) disponibles en Azure AD B2C.

::: zone-end 
