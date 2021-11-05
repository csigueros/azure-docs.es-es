---
title: Administración del acceso de usuario en Azure Active Directory B2C
description: Aprenda a identificar a los menores, recopilar datos sobre la fecha y el país o región de nacimiento y a obtener la aceptación de los términos de uso de la aplicación mediante el uso de Azure AD B2C.
services: active-directory-b2c
author: kengaderdus
manager: CelesteDG
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 03/09/2021
ms.author: kengaderdus
ms.subservice: B2C
ms.openlocfilehash: ae1e0c55642865550d58a299041fff6445c67360
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/02/2021
ms.locfileid: "131007738"
---
# <a name="manage-user-access-in-azure-active-directory-b2c"></a>Administración del acceso de usuario en Azure Active Directory B2C

Este artículo trata sobre cómo administrar el acceso de usuarios a las aplicaciones mediante Azure Active Directory B2C (Azure AD B2C). La administración de acceso de la aplicación incluye:

- Identificación de los menores y control del acceso de usuarios a la aplicación.
- Solicitud del consentimiento parental para que los menores puedan usar las aplicaciones.
- Recopilación de datos de nacimiento y país o región de los usuarios.
- Captura de un contrato de términos de uso y puerta de acceso.

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

## <a name="control-minor-access"></a>Control del acceso de menores

Las aplicaciones y organizaciones pueden decidir impedir a los menores el uso de aplicaciones y servicios que no están dirigidos a este tipo de audiencia. Alternativamente, las aplicaciones y organizaciones pueden decidir aceptar a los menores y, posteriormente administrar el consentimiento de los padres, y proporcionar experiencias permitidas para los menores según lo dictado por las reglas de negocio y lo permitido por la ley.

Si se identifica a un usuario como un menor, puede establecer el flujo de usuario de Azure AD B2C en una de estas tres opciones:

- **Enviar un elemento id_token de JWT firmado a la aplicación**: el usuario se registra en el directorio y se devuelve un token a la aplicación. A continuación, la aplicación empieza a aplicar reglas de negocio. Por ejemplo, la aplicación puede continuar con un proceso de consentimiento de los padres. Para usar este método, elija recibir las notificaciones **ageGroup** y **consentProvidedForMinor** de la aplicación.

- **Enviar un token JSON sin firmar a la aplicación**: Azure AD B2C notifica a la aplicación que el usuario es un menor y proporciona el estado del consentimiento parental del usuario. A continuación, la aplicación empieza a aplicar reglas de negocio. Un token JSON no completa una autenticación correcta con la aplicación. La aplicación debe procesar al usuario no autenticado según las notificaciones incluidas en el token JSON, entre las cuales puede que se incluyan **name**, **email**, **ageGroup** y **consentProvidedForMinor**.

- **Bloquear al usuario**: si un usuario es menor de edad y no se ha proporcionado el consentimiento parental, Azure AD B2C puede notificar al usuario que está bloqueado. No se emite ningún token, el acceso está bloqueado y no se crea la cuenta de usuario durante una operación de registro. Para implementar esta notificación, puede proporcionar una página de contenido HTML/CSS adecuada para informar al usuario y ofrecer las opciones apropiadas. La aplicación no necesita ninguna otra acción adicional para los nuevos registros.

## <a name="get-parental-consent"></a>Obtención del consentimiento de los padres

En función de la normativa de la aplicación, puede que un usuario verificado como adulto deba proporcional consentimiento parental. Azure AD B2C no proporciona ninguna experiencia para verificar la edad de un individuo que, a continuación, permita que un adulto verificado pueda otorgar el consentimiento paterno a un menor. Esta experiencia la debe proporcionar la aplicación u otro proveedor de servicios.

El siguiente es un ejemplo de flujo de usuario para recopilar el consentimiento de los padres:

1. Una operación de [Microsoft Graph API](/graph/use-the-api) identifica al usuario como un menor y devuelve los datos del usuario a la aplicación como un token JSON sin firmar.

2. La aplicación procesa el token JSON y muestra una pantalla al menor que le notifica que necesita consentimiento parental y le solicita el consentimiento de uno de los padres en línea.

3. Azure AD B2C muestra un recorrido de inicio de sesión para que el usuario inicie sesión normalmente y emite un token a la aplicación que se establece para incluir **legalAgeGroupClassification = "minorWithParentalConsent"** . La aplicación recopila la dirección de correo electrónico del padre o la madre y verifica que sea una persona adulta. Para ello, usa una fuente de confianza, como un carnet de identidad, una verificación de la licencia o una prueba de la tarjeta de crédito. Si la verificación se realiza correctamente, la aplicación solicita al menor que inicie sesión mediante el uso del flujo de usuario de Azure AD B2C. Si se ha rechazado el consentimiento (por ejemplo, si **legalAgeGroupClassification = "minorWithoutParentalConsent"** ), Azure AD B2C devuelve un token JSON (no un inicio de sesión) a la aplicación para que vuelva a iniciar el proceso de consentimiento. También se puede personalizar el flujo de usuario, de modo que un menor o un adulto pueda recuperar el acceso a la cuenta del menor enviando un código de registro a la dirección de correo electrónico del menor o a la dirección de correo electrónico registrada del adulto.

4. La aplicación ofrece una opción al menor para revocar el consentimiento.

5. Si el menor o el adulto revoca el consentimiento, se puede usar Microsoft Graph API para cambiar **consentProvidedForMinor** a **denegado**. Alternativamente, la aplicación puede elegir eliminar a un menor cuyo consentimiento se haya revocado. También es posible personalizar el flujo de usuario de modo que el menor autenticado (o el padre que usa la cuenta del menor) pueda revocar el consentimiento. Azure AD B2C registra **consentProvidedForMinor** como **denegado**.

Para más información sobre **legalAgeGroupClassification**, **consentProvidedForMinor** y **ageGroup**, consulte el artículo [User resource type](/graph/api/resources/user) (Tipo de recurso del usuario). Para más información acerca de los atributos personalizados, consulte [Uso de atributos personalizados para recopilar información sobre los consumidores](user-flow-custom-attributes.md). Al dirigirse a atributos extendidos mediante Microsoft Graph API, se debe usar la versión extendida del atributo, como *extension_18b70cf9bb834edd8f38521c2583cd86_dateOfBirth*: *2011-01-01T00:00:00Z*.

## <a name="gather-date-of-birth-and-countryregion-data"></a>Recopilación de datos sobre la fecha y el país o región de nacimiento

Algunas aplicaciones pueden depender de Azure AD B2C para recopilar la información de fecha de nacimiento (DOB) y el país o región de todos los usuarios durante el registro. Si esta información no existe, la aplicación puede solicitarla al usuario en la siguiente operación de autenticación (inicio de sesión). Los usuarios no pueden continuar sin proporcionar información sobre su fecha de nacimiento y país o región. Azure AD B2C usa la información para determinar si la persona se considera un menor según la normativa reglamentaria de ese país o región.

Un flujo de usuario personalizado puede recopilar la información sobre la fecha de nacimiento y el país o región y usar la transformación de notificaciones de Azure AD B2C para determinar **ageGroup** y conservar el resultado (o conservar directamente la información sobre la fecha de nacimiento y el país o región) en el directorio.

Los pasos siguientes muestran la lógica que se usa para calcular **ageGroup** a partir de la fecha de nacimiento del usuario:

1. Intente buscar el país o la región por su código en la lista. Si no se encuentra el país o la región, revierta a la opción **Predeterminado**.

2. Si el nodo **MinorConsent** existe en el elemento de país o región, haga lo siguiente:

    a. Calcule la fecha en la que el usuario debe haber nacido para que se considere un adulto. Por ejemplo, si la fecha actual es 14 de marzo de 2015, y **MinorConsent** es 18, la fecha de nacimiento no debe ser posterior al 14 de marzo de 2000.

    b. Compare la fecha de nacimiento mínima con la fecha de nacimiento real. Si la fecha de nacimiento mínima es anterior a la fecha de nacimiento del usuario, el cálculo devuelve **Menor** como grupo de edad.

3. Si el nodo **MinorNoConsentRequired** se encuentra en el elemento de país o región, repita los pasos 2a y 2b utilizando el valor de **MinorNoConsentRequired**. La salida de 2b devuelve **MinorNoConsentRequired** si la fecha de nacimiento mínima es anterior a la fecha de nacimiento del usuario.

4. Si ninguno de los cálculos devuelve true, el cálculo devuelve **Adult**.

Si una aplicación ha recopilado de forma confiable la información sobre la fecha de nacimiento y el país o región con otros métodos, puede usar Graph API para actualizar el registro del usuario con esta información. Por ejemplo:

- Si se sabe que un usuario es un adulto, actualice el atributo del directorio **ageGroup** con un valor de **Adulto**.
- Si se sabe que un usuario es menor, actualice el atributo del directorio **ageGroup** con un valor de **Minor** y establezca **consentProvidedForMinor**, según corresponda.

## <a name="minor-calculation-rules"></a>Reglas de cálculo para la edad de un menor

La restricción de acceso por edad implica dos valores de edad: la edad en que alguien ya se no considera un menor y la edad en la que un menor debe tener consentimiento parental. En la tabla siguiente se enumeran las reglas de edad que se usan para la definición de un menor y de un menor que requiere consentimiento.

| País/región | Nombre del país o región | Edad de consentimiento de menores | Edad del menor |
| -------------- | ------------------- | ----------------- | --------- |
| Valor predeterminado | None | None | 18 |
| AE | Emiratos Árabes Unidos | None | 21 |
| AT | Austria | 14 | 18 |
| BE | Bélgica | 14 | 18 |
| BG | Bulgaria | 16 | 18 |
| BH | Bahréin | None | 21 |
| CM | Camerún | None | 21 |
| CY | Chipre | 16 | 18 |
| CZ | República Checa | 16 | 18 |
| DE | Alemania | 16 | 18 |
| DK | Dinamarca | 16 | 18 |
| EE | Estonia | 16 | 18 |
| EG | Egipto | None | 21 |
| ES | España | 13 | 18 |
| VF | Francia | 16 | 18 |
| GB | Reino Unido | 13 | 18 |
| GR | Grecia | 16 | 18 |
| HR | Croacia | 16 | 18 |
| HU | Hungría | 16 | 18 |
| IE | Irlanda | 13 | 18 |
| IT | Italia | 16 | 18 |
| KR | Corea, República de | 14 | 18 |
| LT | Lituania | 16 | 18 |
| LU | Luxemburgo | 16 | 18 |
| LV | Letonia | 16 | 18 |
| MT | Malta | 16 | 18 |
| N/D | Namibia | None | 21 |
| NL | Países Bajos | 16 | 18 |
| PL | Polonia | 13 | 18 |
| PT | Portugal | 16 | 18 |
| RO | Rumanía | 16 | 18 |
| SE | Suecia | 13 | 18 |
| SG | Singapur | None | 21 |
| SI | Eslovenia | 16 | 18 |
| SK | Eslovaquia | 16 | 18 |
| TD | Chad | None | 21 |
| TH | Tailandia | None | 20 |
| TW | Taiwán | None | 20 |
| US | Estados Unidos | 13 | 18 |



## <a name="capture-terms-of-use-agreement"></a>Captura del contrato de términos de uso

Cuando desarrolla la aplicación, normalmente obtiene la aceptación del usuario de los términos de uso dentro de sus aplicaciones sin necesidad de participación, o con una participación mínima, del directorio del usuario. Sin embargo, es posible usar un flujo de usuario de Azure AD B2C para recopilar la aceptación de los términos de uso de un usuario, restringir el acceso si no se ha otorgado la aceptación y aplicar la aceptación de cambios futuros en los términos de uso, según la fecha de la última aceptación y la fecha de la última versión de los términos de uso.

Los **términos de uso** también pueden incluir la opción "Consent to share data with third parties" ("Acepto compartir los datos con terceros"). Según la normativa local y las reglas de negocio, puede recopilar la aceptación de un usuario de ambas condiciones combinadas o puede permitir que el usuario acepte una condición y no la otra.

En los pasos siguientes se describe cómo puede administrar los términos de uso:

1. Registre la aceptación de los términos de uso y la fecha de esta mediante Graph API y los atributos extendidos. Para ello, use los flujos de usuario integrados y personalizados. Recomendamos que cree y use los atributos **extension_termsOfUseConsentDateTime** y **extension_termsOfUseConsentVersion**.

2. Cree una casilla obligatoria que tenga la etiqueta "Aceptar términos de uso" y registre el resultado durante el proceso de registro. Para ello, use los flujos de usuario integrados y personalizados.

3. Azure AD B2C almacena el contrato de términos de uso y la aceptación del usuario. Puede usar Graph API para consultar el estado de cualquier usuario mediante la lectura del atributo de extensión que se usó para registrar la respuesta (por ejemplo, la lectura de **termsOfUseTestUpdateDateTime**). Para ello, use los flujos de usuario integrados y personalizados.

4. Solicite la aceptación de los términos de uso actualizados mediante la comparación de la fecha de aceptación con la fecha de la última versión de los términos de uso. Puede comparar las fechas mediante un flujo de usuario personalizado. Utilice el atributo extendido **extension_termsOfUseConsentDateTime** y compare el valor con la notificación de **termsOfUseTextUpdateDateTime**. Si la aceptación es antigua, cree una nueva aceptación. Para ello, muestre una pantalla autoafirmada. En caso contrario, bloquee el acceso mediante la lógica de directiva.

5. Solicite la aceptación de términos de uso actualizados comparando el número de versión de la aceptación con el número de versión más reciente aceptado. Puede comparar números de versión solo mediante un flujo de usuario personalizado. Utilice el atributo extendido **extension_termsOfUseConsentDateTime** y compare el valor de la notificación de **extension_termsOfUseConsentVersion**. Si la aceptación es antigua, cree una nueva aceptación. Para ello, muestre una pantalla autoafirmada. En caso contrario, bloquee el acceso mediante la lógica de directiva.

Puede obtener la aceptación de los términos de uso en los siguientes escenarios:

- Se está registrando un nuevo usuario. Se muestran los términos de uso y se almacena el resultado de aceptación.
- Inicia sesión un usuario que ya ha aceptado anteriormente los términos de uso más recientes o activos. No se muestran los términos de uso.
- Inicia sesión un usuario, que aún no ha aceptado los términos de uso más recientes o activos. Se muestran los términos de uso y se almacena el resultado de aceptación.
- Inicia sesión un usuario, que ya ha aceptado una versión antigua de los términos de uso, que ahora se actualizaron a la versión más reciente. Se muestran los términos de uso y se almacena el resultado de aceptación.

En la imagen siguiente se muestra el flujo de usuario recomendado:

![Diagrama de flujo que muestra el flujo de usuario de aceptación recomendado](./media/manage-user-access/user-flow.png)

El siguiente es un ejemplo de consentimiento de términos de uso basado en la fecha de una notificación. Si la notificación `extension_termsOfUseConsentDateTime` es anterior a `2025-01-15T00:00:00`, fuerce una nueva aceptación comprobando la notificación booleana `termsOfUseConsentRequired` y mostrando una pantalla autoafirmada. 

```xml
<ClaimsTransformations>
  <ClaimsTransformation Id="GetNewUserAgreeToTermsOfUseConsentDateTime" TransformationMethod="GetCurrentDateTime">
    <OutputClaims>
      <OutputClaim ClaimTypeReferenceId="extension_termsOfUseConsentDateTime" TransformationClaimType="currentDateTime" />
    </OutputClaims>
  </ClaimsTransformation>
  <ClaimsTransformation Id="IsTermsOfUseConsentRequired" TransformationMethod="IsTermsOfUseConsentRequired">
    <InputClaims>
      <InputClaim ClaimTypeReferenceId="extension_termsOfUseConsentDateTime" TransformationClaimType="termsOfUseConsentDateTime" />
    </InputClaims>
    <InputParameters>
      <InputParameter Id="termsOfUseTextUpdateDateTime" DataType="dateTime" Value="2025-01-15T00:00:00" />
    </InputParameters>
    <OutputClaims>
      <OutputClaim ClaimTypeReferenceId="termsOfUseConsentRequired" TransformationClaimType="result" />
    </OutputClaims>
  </ClaimsTransformation>
</ClaimsTransformations>
```

El siguiente es un ejemplo de consentimiento de términos de uso basado en la versión de una notificación. Si la notificación `extension_termsOfUseConsentVersion` no es igual a `V1`, fuerce una nueva aceptación comprobando la notificación booleana `termsOfUseConsentRequired` y mostrando una pantalla autoafirmada.

```xml
<ClaimsTransformations>
  <ClaimsTransformation Id="GetEmptyTermsOfUseConsentVersionForNewUser" TransformationMethod="CreateStringClaim">
    <InputParameters>
      <InputParameter Id="value" DataType="string" Value=""/>
    </InputParameters>
    <OutputClaims>
      <OutputClaim ClaimTypeReferenceId="extension_termsOfUseConsentVersion" TransformationClaimType="createdClaim" />
    </OutputClaims>
  </ClaimsTransformation>
  <ClaimsTransformation Id="GetNewUserAgreeToTermsOfUseConsentVersion" TransformationMethod="CreateStringClaim">
    <InputParameters>
      <InputParameter Id="value" DataType="string" Value="V1"/>
    </InputParameters>
    <OutputClaims>
      <OutputClaim ClaimTypeReferenceId="extension_termsOfUseConsentVersion" TransformationClaimType="createdClaim" />
    </OutputClaims>
  </ClaimsTransformation>
  <ClaimsTransformation Id="IsTermsOfUseConsentRequiredForVersion" TransformationMethod="CompareClaimToValue">
    <InputClaims>
      <InputClaim ClaimTypeReferenceId="extension_termsOfUseConsentVersion" TransformationClaimType="inputClaim1" />
    </InputClaims>
    <InputParameters>
      <InputParameter Id="compareTo" DataType="string" Value="V1" />
      <InputParameter Id="operator" DataType="string" Value="not equal" />
      <InputParameter Id="ignoreCase" DataType="string" Value="true" />
    </InputParameters>
    <OutputClaims>
      <OutputClaim ClaimTypeReferenceId="termsOfUseConsentRequired" TransformationClaimType="outputClaim" />
    </OutputClaims>
  </ClaimsTransformation>
</ClaimsTransformations>
```

## <a name="next-steps"></a>Pasos siguientes

- [Habilitación del acceso según la edad en Azure AD B2C](age-gating.md)
- Para aprender a eliminar y exportar datos de usuario, consulte [Administración de datos de usuario](manage-user-data.md).
- Para ver un ejemplo de directiva personalizada que implementa un mensaje de condiciones de uso, consulte [Una directiva personalizada de B2C IEF: registro e inicio de sesión con el aviso "Condiciones de uso"](https://github.com/azure-ad-b2c/samples/tree/master/policies/sign-in-sign-up-versioned-tou).
