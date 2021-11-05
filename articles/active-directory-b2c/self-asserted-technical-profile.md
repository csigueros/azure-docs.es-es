---
title: Definición de un perfil técnico autoafirmado en una directiva personalizada
titleSuffix: Azure AD B2C
description: Defina un perfil técnico autoafirmado en una directiva personalizada en Azure Active Directory B2C.
services: active-directory-b2c
author: kengaderdus
manager: CelesteDG
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 03/10/2021
ms.author: kengaderdus
ms.subservice: B2C
ms.openlocfilehash: 031b7b1ae4d776ce08a18da5e292d83206c4c3b2
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/02/2021
ms.locfileid: "131040159"
---
# <a name="define-a-self-asserted-technical-profile-in-an-azure-active-directory-b2c-custom-policy"></a>Definición de un perfil técnico autoafirmado en una directiva personalizada en Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Todas las interacciones realizadas en Azure Active Directory B2C (Azure AD B2C) en las que se espera que el usuario proporcione una entrada son perfiles técnicos autoafirmados. Por ejemplo, una página de registro, de inicio de sesión o de restablecimiento de la contraseña.

## <a name="protocol"></a>Protocolo

El atributo **Name** del elemento **Protocol** tiene que establecerse en `Proprietary`. El atributo **handler** debe contener el nombre completo del ensamblado de controlador de protocolo que usa Azure AD B2C para la autoafirmación: `Web.TPEngine.Providers.SelfAssertedAttributeProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null`.

En el ejemplo siguiente se muestra un perfil técnico autoafirmado para el registro de un correo electrónico:

```xml
<TechnicalProfile Id="LocalAccountSignUpWithLogonEmail">
  <DisplayName>Email signup</DisplayName>
  <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.SelfAssertedAttributeProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
```

## <a name="input-claims"></a>Notificaciones de entrada

En un perfil técnico autoafirmado, puede usar los elementos **InputClaims** y **InputClaimsTransformations** para rellenar previamente el valor de las notificaciones que aparecen en la página autoafirmada (notificaciones de salida). Por ejemplo, en la directiva del perfil de edición, el recorrido del usuario lee primero el perfil de usuario desde el servicio de directorio de Azure AD B2C y, después, el perfil técnico autoafirmado establece las notificaciones de entrada con los datos de usuario almacenados en el perfil de usuario. Estas notificaciones se recopilan desde el perfil de usuario y se presentan al usuario, que después puede editar los datos existentes.

```xml
<TechnicalProfile Id="SelfAsserted-ProfileUpdate">
...
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="alternativeSecurityId" />
    <InputClaim ClaimTypeReferenceId="userPrincipalName" />
    <InputClaim ClaimTypeReferenceId="givenName" />
    <InputClaim ClaimTypeReferenceId="surname" />
  </InputClaims>
```

## <a name="display-claims"></a>Notificaciones de visualización

La característica de notificaciones de visualización se encuentra actualmente en **versión preliminar**.

El elemento **DisplayClaims** contiene una lista de las notificaciones que se van a presentar en la pantalla para recopilar datos del usuario. Para rellenar previamente los valores de las notificaciones de visualización, use las notificaciones de entrada descritas anteriormente. El elemento también puede incluir un valor predeterminado.

El orden de las notificaciones en **DisplayClaims** especifica el orden en que Azure AD B2C presenta las notificaciones en la pantalla. Para forzar que el usuario proporcione un valor para una notificación específica, establezca el atributo **Required** del elemento **DisplayClaim** en `true`.

El elemento **ClaimType** de la colección **DisplayClaims** necesita establecer el elemento **UserInputType** en cualquier tipo de entrada de usuario admitido por Azure AD B2C. Por ejemplo, `TextBox` o `DropdownSingleSelect`.

### <a name="add-a-reference-to-a-displaycontrol"></a>Adición de una referencia a un elemento DisplayControl

En la colección de notificaciones de visualización, puede incluir una referencia a un elemento [DisplayControl](display-controls.md) que haya creado. Un control de visualización es un elemento de la interfaz de usuario que tiene una funcionalidad especial e interactúa con el servicio de back-end de Azure AD B2C. Permite al usuario realizar acciones en la página que invocan un perfil técnico de validación en el back-end. Por ejemplo, la comprobación de una dirección de correo electrónico, un número de teléfono o un número de fidelidad del cliente.

En el siguiente ejemplo `TechnicalProfile` se muestra el uso de las notificaciones de visualización con controles de visualización.

* La primera notificación de visualización hace referencia al control de visualización `emailVerificationControl`, que recopila y comprueba la dirección de correo.
* La quinta notificación de visualización hace referencia al control de visualización `phoneVerificationControl`, que recopila y comprueba un número de teléfono.
* Las demás notificaciones de visualización son elementos ClaimTypes que se van a recopilar del usuario.

```xml
<TechnicalProfile Id="Id">
  <DisplayClaims>
    <DisplayClaim DisplayControlReferenceId="emailVerificationControl" />
    <DisplayClaim ClaimTypeReferenceId="displayName" Required="true" />
    <DisplayClaim ClaimTypeReferenceId="givenName" Required="true" />
    <DisplayClaim ClaimTypeReferenceId="surName" Required="true" />
    <DisplayClaim DisplayControlReferenceId="phoneVerificationControl" />
    <DisplayClaim ClaimTypeReferenceId="newPassword" Required="true" />
    <DisplayClaim ClaimTypeReferenceId="reenterPassword" Required="true" />
  </DisplayClaims>
</TechnicalProfile>
```

Como hemos mencionado, una notificación de visualización con una referencia a un control de visualización puede ejecutar su propia validación, por ejemplo la comprobación de la dirección de correo electrónico. Además, la página autofirmada admite el uso de un perfil técnico de validación para validar toda la página, incluidos los datos proporcionados por el usuario (tipos de notificaciones o controles de visualización), antes de pasar al siguiente paso de orquestación.

### <a name="combine-usage-of-display-claims-and-output-claims-carefully"></a>Combinación del uso de notificaciones de visualización y notificaciones de salida con cuidado

Si especifica uno o más elementos de **DisplayClaim** en un perfil técnico autoafirmado, debe usar un elemento DisplayClaim para *cada* notificación que desee mostrar en pantalla y recopilar del usuario. No existe ninguna notificación de salida que muestre el perfil técnico autoafirmado que contenga una notificación de salida como mínimo.

Considere el siguiente ejemplo en el que se define una notificación `age` como una notificación de **salida** en una directiva base. Antes de agregar cualquier notificación de visualización al perfil técnico autoafirmado, la notificación `age` se muestra en la pantalla para la recopilación de datos del usuario:

```xml
<TechnicalProfile Id="id">
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="age" />
  </OutputClaims>
</TechnicalProfile>
```

Si una directiva de hoja que hereda esa base especifica posteriormente `officeNumber` como una notificación de **visualización**:

```xml
<TechnicalProfile Id="id">
  <DisplayClaims>
    <DisplayClaim ClaimTypeReferenceId="officeNumber" />
  </DisplayClaims>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="officeNumber" />
  </OutputClaims>
</TechnicalProfile>
```

La notificación `age` de la directiva base ya no se muestra al usuario en la pantalla, sino que está "oculta". Para mostrar la notificación `age` y recopilar el valor de edad del usuario, debe agregar un elemento **DisplayClaim** `age`.

## <a name="output-claims"></a>Notificaciones de salida

El elemento **OutputClaims** contiene una lista de las notificaciones que se van a devolver en el siguiente paso de orquestación. El atributo **DefaultValue** solo tiene efecto si la notificación nunca se ha establecido. Si se ha establecido en un paso de orquestación anterior, el valor predeterminado no se aplica aunque el usuario deje el valor vacío. Para forzar el uso de un valor predeterminado, establezca el atributo **AlwaysUseDefaultValue** en `true`.

Por motivos de seguridad, un valor de notificación de contraseña (`UserInputType` establecido en `Password`) solo está disponible para los perfiles técnicos de validación del perfil técnico autoafirmado. En los pasos de orquestación siguientes, no se puede usar la notificación de contraseñas. 

> [!NOTE]
> En las versiones anteriores de Identity Experience Framework (IEF), las notificaciones de salida se usaban para recopilar datos del usuario. Para recopilar datos del usuario, use una colección **DisplayClaims** en su lugar.

El elemento **OutputClaimsTransformations** puede contener una colección de elementos **OutputClaimsTransformation** que se usan para modificar las notificaciones de salida o para generar nuevas.

### <a name="when-you-should-use-output-claims"></a>Cuándo se deben usar las notificaciones de salida

En un perfil técnico autoafirmado, la colección de notificaciones de salida devuelve las notificaciones en el siguiente paso de orquestación.

Use notificaciones de salida si:

- **Las notificaciones se emiten mediante la transformación de notificaciones de salida**
- **Establece un valor predeterminado en una notificación de salida** sin recopilar datos del usuario ni devolver los datos desde el perfil técnico de validación. El perfil técnico autoafirmado `LocalAccountSignUpWithLogonEmail` establece la notificación **executed-SelfAsserted-Input** en `true`.
- **Un perfil técnico de validación devuelve las notificaciones de salida**: su perfil técnico puede llamar a un perfil técnico de validación que devuelve algunas notificaciones. Es posible que desee propagar las notificaciones y devolverlas a los siguientes pasos de orquestación en el recorrido del usuario. Por ejemplo, al iniciar sesión con una cuenta local, el perfil técnico autoafirmado denominado `SelfAsserted-LocalAccountSignin-Email` llama al perfil técnico de validación denominado `login-NonInteractive`. Este perfil técnico valida las credenciales de usuario y también devuelve el perfil de usuario. Por ejemplo, “userPrincipalName”, “displayName”, “givenName” y “surName”.
- **Un control de visualización devuelve las notificaciones de salida**: su perfil técnico puede tener una referencia a un [control de visualización](display-controls.md). El control de visualización devuelve algunas notificaciones, como la dirección de correo electrónico comprobada. Es posible que desee propagar las notificaciones y devolverlas a los siguientes pasos de orquestación en el recorrido del usuario. La característica de notificaciones de visualización se encuentra actualmente en **versión preliminar**.

En el ejemplo siguiente se muestra el uso de un perfil técnico autoafirmado que utiliza notificaciones de visualización y notificaciones de salida.

```xml
<TechnicalProfile Id="LocalAccountSignUpWithLogonEmail">
  <DisplayName>Email signup</DisplayName>
  <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.SelfAssertedAttributeProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
  <Metadata>
    <Item Key="IpAddressClaimReferenceId">IpAddress</Item>
    <Item Key="ContentDefinitionReferenceId">api.localaccountsignup</Item>
    <Item Key="language.button_continue">Create</Item>
  </Metadata>
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="email" />
  </InputClaims>
  <DisplayClaims>
    <DisplayClaim DisplayControlReferenceId="emailVerificationControl" />
    <DisplayClaim DisplayControlReferenceId="SecondaryEmailVerificationControl" />
    <DisplayClaim ClaimTypeReferenceId="displayName" Required="true" />
    <DisplayClaim ClaimTypeReferenceId="givenName" Required="true" />
    <DisplayClaim ClaimTypeReferenceId="surName" Required="true" />
    <DisplayClaim ClaimTypeReferenceId="newPassword" Required="true" />
    <DisplayClaim ClaimTypeReferenceId="reenterPassword" Required="true" />
  </DisplayClaims>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="email" Required="true" />
    <OutputClaim ClaimTypeReferenceId="objectId" />
    <OutputClaim ClaimTypeReferenceId="executed-SelfAsserted-Input" DefaultValue="true" />
    <OutputClaim ClaimTypeReferenceId="authenticationSource" />
    <OutputClaim ClaimTypeReferenceId="newUser" />
  </OutputClaims>
  <ValidationTechnicalProfiles>
    <ValidationTechnicalProfile ReferenceId="AAD-UserWriteUsingLogonEmail" />
  </ValidationTechnicalProfiles>
  <UseTechnicalProfileForSessionManagement ReferenceId="SM-AAD" />
</TechnicalProfile>
```

### <a name="output-claims-sign-up-or-sign-in-page"></a>Página de registro o inicio de sesión de notificaciones de salida

En una página de registro e inicio de sesión combinados, tenga en cuenta lo siguiente al usar un elemento [DataUri](contentdefinitions.md#datauri) de definición de contenido que especifica un tipo de página `unifiedssp` o `unifiedssd`:

- Solo se representan las notificaciones de nombre de usuario y contraseña.
- Las dos primeras notificaciones de salida deben ser el nombre de usuario y la contraseña (en este orden). 
- Las demás notificaciones no se representan; para ellas, debe establecer `defaultValue` o invocar un perfil técnico de validación de formularios de notificaciones. 

## <a name="persist-claims"></a>Conservar las notificaciones

El elemento PersistedClaims no se usa. El perfil técnico autoafirmado no conserva los datos en Azure AD B2C. En cambio, se realiza una llamada a un perfil técnico de validación que es responsable de conservar los datos. Por ejemplo, la directiva de registro usa el perfil técnico autoafirmado `LocalAccountSignUpWithLogonEmail` para recopilar el nuevo perfil de usuario. El perfil técnico `LocalAccountSignUpWithLogonEmail` llama al perfil técnico de validación para crear la cuenta en Azure AD B2C.

## <a name="validation-technical-profiles"></a>Perfiles técnicos de validación

Los perfiles técnicos de validación se usan para validar algunas o todas las notificaciones de salida del perfil técnico al que hace referencia. Las notificaciones de entrada del perfil técnico de validación de entrada deben aparecer en las notificaciones de salida del perfil técnico autoafirmado. El perfil técnico de validación valida la entrada del usuario y puede devolver un error al usuario.

El perfil técnico de validación puede ser cualquier perfil técnico de la directiva, como perfiles técnicos de [Azure Active Directory](active-directory-technical-profile.md) o de una [API de REST](restful-technical-profile.md). En el ejemplo anterior, el perfil técnico `LocalAccountSignUpWithLogonEmail` valida que signinName no exista en el directorio. En caso contrario, el perfil técnico de validación crea una cuenta local y devuelve los valores objectId, authenticationSource y newUser. El perfil técnico `SelfAsserted-LocalAccountSignin-Email` llama al perfil técnico de validación `login-NonInteractive` para validar las credenciales del usuario.

También puede llamar a un perfil técnico de la API de REST con la lógica de negocios, sobrescribir notificaciones de entrada o enriquecer los datos del usuario mediante una integración adicional de una aplicación de línea de negocio corporativa. Para obtener más información, vea [Perfil técnico de validación](validation-technical-profile.md).

## <a name="metadata"></a>Metadatos

| Atributo | Obligatorio | Descripción |
| --------- | -------- | ----------- |
| setting.operatingMode <sup>1</sup>| No | En una página de inicio de sesión, esta propiedad controla el comportamiento del campo de nombre de usuario, como la validación de entrada y los mensajes de error. Valores esperados: `Username` o `Email`.  |
| AllowGenerationOfClaimsWithNullValues| No| Permite que se genere una notificación con un valor NULL. Por ejemplo, en caso de que un usuario no active una casilla.|
| ContentDefinitionReferenceId | Sí | El identificador de la [definición de contenido](contentdefinitions.md) asociada a este perfil técnico. |
| EnforceEmailVerification | No | Para registrarse o editar el perfil, exige la comprobación del correo electrónico. Valores posibles: `true` (opción predeterminada) o `false`. |
| setting.retryLimit | No | Controla el número de veces que un usuario puede intentar proporcionar los datos que se comprueban con un perfil técnico de validación. Por ejemplo, si un usuario intenta registrarse con una cuenta que ya existe y sigue intentándolo hasta que alcance el límite.
| SignUpTarget <sup>1</sup>| No | Identificador de intercambio de destinos del registro. Cuando el usuario hace clic en el botón de registro, Azure AD B2C ejecuta el identificador de intercambio especificado. |
| setting.showCancelButton | No | Muestra el botón para cancelar. Valores posibles: `true` (opción predeterminada) o `false` |
| setting.showContinueButton | No | Muestra el botón para continuar. Valores posibles: `true` (opción predeterminada) o `false` |
| setting.showSignupLink <sup>2</sup>| No | Muestra el botón para registrarse. Valores posibles: `true` (opción predeterminada) o `false` |
| setting.forgotPasswordLinkLocation <sup>2</sup>| No| Muestra el vínculo de contraseña olvidada. Valores posibles: `AfterLabel` (valor predeterminado) muestra el vínculo directamente después de la etiqueta o después del campo de entrada de contraseña cuando no hay ninguna etiqueta, `AfterInput` muestra el vínculo después del campo de entrada de contraseña, `AfterButtons` muestra el vínculo en la parte inferior del formulario después de los botones y `None` quita el vínculo de contraseña olvidada.|
| setting.enableRememberMe <sup>2</sup>| No| Muestra la casilla [Mantener la sesión iniciada](session-behavior.md?pivots=b2c-custom-policy#enable-keep-me-signed-in-kmsi). Valores posibles: `true` o `false` (valor predeterminado). |
| setting.inputVerificationDelayTimeInMilliseconds <sup>3</sup>| No| Mejora la experiencia del usuario, ya que espera a que el usuario deje de escribir y, a continuación, valida el valor. El valor predeterminado es 2000 milisegundos. |
| IncludeClaimResolvingInClaimsHandling  | No | En el caso de las notificaciones de entrada y salida, especifica si se incluye la [resolución de notificaciones](claim-resolver-overview.md) en el perfil técnico. Valores posibles: `true` o `false` (valor predeterminado). Si desea utilizar un solucionador de notificaciones en el perfil técnico, establézcalo en `true`. |
|forgotPasswordLinkOverride <sup>4</sup>| No | Intercambio de solicitudes de restablecimiento de contraseña que se va a ejecutar. Para más información, consulte [Autoservicio de restablecimiento de contraseña](add-password-reset-policy.md). |

Notas:
1. Disponible para el tipo de definición de contenido [DataUri](contentdefinitions.md#datauri) de `unifiedssp` o `unifiedssd`.
1. Disponible para el tipo de definición de contenido [DataUri](contentdefinitions.md#datauri) de `unifiedssp` o `unifiedssd`. [Versión de diseño de página](page-layout.md) 1.1.0 y versiones posteriores.
1. Disponible para la [versión de diseño de página](page-layout.md) 1.2.0 y versiones posteriores.
1. Disponible para el tipo de definición de contenido [DataUri](contentdefinitions.md#datauri) de `unifiedssp`. [Versión de diseño de página](page-layout.md) 2.1.2 y versiones posteriores.

## <a name="cryptographic-keys"></a>Claves de cifrado

El elemento **CryptographicKeys** no se usa.
