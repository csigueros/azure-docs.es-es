---
title: Configuración de los requisitos de complejidad de las contraseñas
titleSuffix: Azure AD B2C
description: Configuración de los requisitos de complejidad de contraseñas proporcionados por los consumidores en Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 09/20/2021
ms.custom: project-no-code
ms.author: mimart
ms.subservice: B2C
zone_pivot_groups: b2c-policy-type
ms.openlocfilehash: 14c269ae8cb29d32659f500357bbc7bcd81e6d8c
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/24/2021
ms.locfileid: "128578933"
---
# <a name="configure-complexity-requirements-for-passwords-in-azure-active-directory-b2c"></a>Configuración de los requisitos de complejidad de contraseñas de Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-choose-user-flow-or-custom-policy](../../includes/active-directory-b2c-choose-user-flow-or-custom-policy.md)]

Azure Active Directory B2C (Azure AD B2C) admite el cambio de los requisitos de complejidad de las contraseñas proporcionadas por un usuario final al crear una cuenta. De forma predeterminada, Azure AD B2C utiliza contraseñas **seguras**. Azure AD B2C también admite opciones de configuración para controlar la complejidad de las contraseñas que los clientes pueden usar.

## <a name="prerequisites"></a>Requisitos previos

[!INCLUDE [active-directory-b2c-customization-prerequisites](../../includes/active-directory-b2c-customization-prerequisites.md)]

::: zone pivot="b2c-user-flow"

## <a name="password-rule-enforcement"></a>Aplicación de las reglas de contraseña

Al realizar el registro o el restablecimiento de contraseña, un usuario final tiene que proporcionar una contraseña que cumpla las reglas de complejidad. Las reglas de complejidad de contraseña se aplican por flujo de usuario. Es posible tener un flujo de usuario que exija un pin de cuatro dígitos durante el registro mientras otro flujo de usuario requiere una cadena de ocho caracteres al realizar el registro. Por ejemplo, puede utilizar un flujo de usuario con complejidad de contraseña diferente para adultos y para niños.

La complejidad de la contraseña nunca se aplica durante el inicio de sesión. Nunca se pide a los usuarios durante el inicio de sesión que cambien su contraseña porque no cumple los requisitos de complejidad actuales.

La complejidad de la contraseña puede configurarse en los siguientes tipos de flujos de usuario:

- Flujo de usuario de registro o de inicio de sesión
- Flujo de usuario de restablecimiento de contraseña

Si usa directivas personalizadas, puede ([configurar la complejidad de la contraseña en una directiva personalizada](password-complexity.md)).

## <a name="configure-password-complexity"></a>Configurar la complejidad de la contraseña

1. Inicie sesión en [Azure Portal](https://portal.azure.com).
1. Asegúrese de que usa el directorio que contiene el inquilino de Azure AD B2C. Seleccione el icono **Directorios y suscripciones** en la barra de herramientas del portal.
1. En la página **Configuración del portal | Directorios y suscripciones**, busque el directorio de Azure AD B2C en la lista **Nombre de directorio** y seleccione **Cambiar**.
1. En Azure Portal, busque y seleccione **Azure AD B2C**.
1. Seleccione **Flujos de usuario**.
1. Seleccione un flujo de usuario y haga clic en **Propiedades**.
1. En **Complejidad de la contraseña**, cambie la complejidad de contraseña de este flujo de usuario a **Simple**, **Segura** o **Personalizada**.

### <a name="comparison-chart"></a>Gráfico de comparación

| Complejidad | Descripción |
| --- | --- |
| Simple | Una contraseña que tenga al menos de 8 a 64 caracteres. |
| Alta | Una contraseña que tenga al menos de 8 a 64 caracteres. Requiere 3 de 4 de minúsculas, mayúsculas, números o símbolos. |
| Personalizado | Esta opción proporciona el máximo control sobre las reglas de complejidad de contraseña.  Permite configurar una longitud personalizada.  También permite aceptar contraseñas compuestas solo por números (PIN). |

## <a name="custom-options"></a>opciones personalizadas

### <a name="character-set"></a>Juego de caracteres

Permite aceptar solo dígitos (PIN) o el juego completo de caracteres.

- **Solo números** permite solamente dígitos (0-9) al escribir una contraseña.
- **Todos** permite cualquier letra, número o símbolo.

### <a name="length"></a>Length

Permite controlar los requisitos de longitud de la contraseña.

- **Longitud mínima** tiene que ser 4 como mínimo.
- **Longitud máxima** tiene que ser mayor o igual que la longitud mínima y como máximo puede ser de 256 caracteres.

### <a name="character-classes"></a>Clase de caracteres

Permite controlar los distintos tipos de caracteres utilizados en la contraseña.

- **2 de 4: carácter en minúscula, carácter en mayúscula, número (0-9), símbolo** garantiza que la contraseña contiene al menos dos tipos de caracteres. Por ejemplo, un número y un carácter en minúscula.
- **3 de 4: carácter en minúscula, carácter en mayúscula, número (0-9), símbolo** garantiza que la contraseña contiene al menos tres tipos de caracteres. Por ejemplo, un número, un carácter en minúscula y un carácter en mayúscula.
- **4 de 4: carácter en minúscula, carácter en mayúscula, número (0-9), símbolo** garantiza que la contraseña contiene al menos todos tipos de caracteres.

    > [!NOTE]
    > Solicitar una contraseña de tipo **4 de 4** puede dar lugar a la frustración del usuario final. Algunos estudios han demostrado que este requisito no mejorar la entropía de contraseña. Consulte las [directrices para contraseñas del NIST](https://pages.nist.gov/800-63-3/sp800-63b.html#appA)

::: zone-end

::: zone pivot="b2c-custom-policy"

## <a name="password-predicate-validation"></a>Validación de predicados de contraseña

Para configurar la complejidad de la contraseña, reemplace los [tipos de notificaciones](claimsschema.md) de `newPassword` y `reenterPassword` por una referencia a las [validaciones de predicado](predicates.md#predicatevalidations). El elemento PredicateValidations agrupa un conjunto de predicados para formar una validación de entrada de usuario que se puede aplicar a un tipo de notificación. Abra el archivo de extensiones de la directiva. Por ejemplo, <em>`SocialAndLocalAccounts/` **`TrustFrameworkExtensions.xml`**</em>.

1. Busque el elemento [BuildingBlocks](buildingblocks.md). Si el elemento no existe, agréguelo.
1. Busque el elemento [ClaimsSchema](claimsschema.md). Si el elemento no existe, agréguelo.
1. Agregue las notificaciones `newPassword` y `reenterPassword` al elemento **ClaimsSchema**.

    ```xml
    <!-- 
    <BuildingBlocks>
      <ClaimsSchema> -->
        <ClaimType Id="newPassword">
          <PredicateValidationReference Id="CustomPassword" />
        </ClaimType>
        <ClaimType Id="reenterPassword">
          <PredicateValidationReference Id="CustomPassword" />
        </ClaimType>
      <!-- 
      </ClaimsSchema>
    </BuildingBlocks>-->
    ```

1. [Predicates](predicates.md) define una validación básica para comprobar el valor de un tipo de notificación y devuelve true o false. La validación se realiza mediante un elemento de método especificado y un conjunto de parámetros pertinentes para el método. Agregue los siguientes predicados al elemento **BuildingBlocks** justo después del cierre del elemento `</ClaimsSchema>`:

    ```xml
    <!-- 
    <BuildingBlocks>-->
      <Predicates>
        <Predicate Id="LengthRange" Method="IsLengthRange">
          <UserHelpText>The password must be between 6 and 64 characters.</UserHelpText>
          <Parameters>
            <Parameter Id="Minimum">6</Parameter>
            <Parameter Id="Maximum">64</Parameter>
          </Parameters>
        </Predicate>
        <Predicate Id="Lowercase" Method="IncludesCharacters">
          <UserHelpText>a lowercase letter</UserHelpText>
          <Parameters>
            <Parameter Id="CharacterSet">a-z</Parameter>
          </Parameters>
        </Predicate>
        <Predicate Id="Uppercase" Method="IncludesCharacters">
          <UserHelpText>an uppercase letter</UserHelpText>
          <Parameters>
            <Parameter Id="CharacterSet">A-Z</Parameter>
          </Parameters>
        </Predicate>
        <Predicate Id="Number" Method="IncludesCharacters">
          <UserHelpText>a digit</UserHelpText>
          <Parameters>
            <Parameter Id="CharacterSet">0-9</Parameter>
          </Parameters>
        </Predicate>
        <Predicate Id="Symbol" Method="IncludesCharacters">
          <UserHelpText>a symbol</UserHelpText>
          <Parameters>
            <Parameter Id="CharacterSet">@#$%^&amp;*\-_+=[]{}|\\:',.?/`~"();!</Parameter>
          </Parameters>
        </Predicate>
      </Predicates>
    <!-- 
    </BuildingBlocks>-->
    ```

1. Agregue las siguientes validaciones de predicado al elemento **BuildingBlocks** justo después del cierre del elemento `</Predicates>`:

    ```xml
    <!-- 
    <BuildingBlocks>-->
      <PredicateValidations>
        <PredicateValidation Id="CustomPassword">
          <PredicateGroups>
            <PredicateGroup Id="LengthGroup">
              <PredicateReferences MatchAtLeast="1">
                <PredicateReference Id="LengthRange" />
              </PredicateReferences>
            </PredicateGroup>
            <PredicateGroup Id="CharacterClasses">
              <UserHelpText>The password must have at least 3 of the following:</UserHelpText>
              <PredicateReferences MatchAtLeast="3">
                <PredicateReference Id="Lowercase" />
                <PredicateReference Id="Uppercase" />
                <PredicateReference Id="Number" />
                <PredicateReference Id="Symbol" />
              </PredicateReferences>
            </PredicateGroup>
          </PredicateGroups>
        </PredicateValidation>
      </PredicateValidations>
    <!-- 
    </BuildingBlocks>-->
    ```

## <a name="disable-strong-password"></a>Deshabilitar una contraseña segura 

Los siguientes perfiles técnicos son [perfiles técnicos de Active Directory](active-directory-technical-profile.md), que leen y escriben datos en Azure Active Directory. Reemplace estos perfiles técnicos en el archivo de extensión. Use `PersistedClaims` para deshabilitar la directiva de contraseñas seguras. Busque el elemento **ClaimsProviders**.  Agregue los siguientes proveedores de notificaciones tal como se indica a continuación:

```xml
<!-- 
<ClaimsProviders>-->
  <ClaimsProvider>
    <DisplayName>Azure Active Directory</DisplayName>
    <TechnicalProfiles>
      <TechnicalProfile Id="AAD-UserWriteUsingLogonEmail">
        <PersistedClaims>
          <PersistedClaim ClaimTypeReferenceId="passwordPolicies" DefaultValue="DisablePasswordExpiration, DisableStrongPassword"/>
        </PersistedClaims>
      </TechnicalProfile>
      <TechnicalProfile Id="AAD-UserWritePasswordUsingObjectId">
        <PersistedClaims>
          <PersistedClaim ClaimTypeReferenceId="passwordPolicies" DefaultValue="DisablePasswordExpiration, DisableStrongPassword"/>
        </PersistedClaims>
      </TechnicalProfile>
    </TechnicalProfiles>
  </ClaimsProvider>
<!-- 
</ClaimsProviders>-->
```

Si usa la directiva de [inicio de sesión mediante nombre de usuario](https://github.com/azure-ad-b2c/samples/tree/master/policies/username-signup-or-signin), actualice los perfiles técnicos `AAD-UserWriteUsingLogonEmail`, `AAD-UserWritePasswordUsingObjectId` y `LocalAccountWritePasswordUsingObjectId` con la directiva *DisableStrongPassword*.

Guarde el archivo de directiva.

## <a name="test-your-policy"></a>Prueba de la directiva

### <a name="upload-the-files"></a>Carga de los archivos

1. Inicie sesión en [Azure Portal](https://portal.azure.com/).
1. Asegúrese de que usa el directorio que contiene el inquilino de Azure AD B2C. Seleccione el icono **Directorios y suscripciones** en la barra de herramientas del portal.
1. En la página **Configuración del portal | Directorios y suscripciones**, busque el directorio de Azure AD B2C en la lista **Nombre de directorio** y seleccione **Cambiar**.
1. Elija **Todos los servicios** en la esquina superior izquierda de Azure Portal, y busque y seleccione **Azure AD B2C**.
1. Seleccione **Marco de experiencia de identidad**.
1. En la página Directivas personalizadas, haga clic en **Cargar directiva**.
1. Seleccione **Sobrescribir la directiva si existe**, y busque y seleccione el archivo *TrustFrameworkExtensions.xml*.
1. Haga clic en **Cargar**.

### <a name="run-the-policy"></a>Ejecución de la directiva

1. Abra la directiva de registro o de inicio de sesión. Por ejemplo, *B2C_1A_signup_signin*.
2. En **Aplicación**, seleccione la aplicación que registró anteriormente. Para ver el token, **URL de respuesta** debe mostrar `https://jwt.ms`.
3. Haga clic en **Ejecutar ahora**.
4. Seleccione **Registrarse ahora**, escriba una dirección de correo electrónico y escriba una contraseña nueva. Se presenta una guía sobre las restricciones de las contraseñas. Termine de especificar la información de usuario y, a continuación, haga clic en **Crear**. Debería ver el contenido del token que se devolvió.

## <a name="next-steps"></a>Pasos siguientes

- Obtenga información sobre cómo [configurar el cambio de contraseña en Azure Active Directory B2C](add-password-change-policy.md).
- Más información sobre los elementos [Predicates](predicates.md) y [PredicateValidations](predicates.md#predicatevalidations) en la referencia de IEF.


::: zone-end
