---
title: Configuración del cambio de contraseñas mediante directivas personalizadas
titleSuffix: Azure AD B2C
description: Aprenda a configurar una directiva personalizada para que los usuarios puedan cambiar su contraseña en Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 08/24/2021
ms.author: mimart
ms.subservice: B2C
zone_pivot_groups: b2c-policy-type
ms.openlocfilehash: 16295eb707968a606c74813f9f6b7585aaf59546
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/24/2021
ms.locfileid: "128570628"
---
# <a name="set-up-password-change-by-using-custom-policies-in-azure-active-directory-b2c"></a>Configuración del cambio de contraseñas mediante directivas personalizadas en Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-choose-user-flow-or-custom-policy](../../includes/active-directory-b2c-choose-user-flow-or-custom-policy.md)]

Puede configurar Azure Active Directory B2C (Azure AD B2C) para que un usuario que haya iniciado sesión con una cuenta local pueda cambiar su contraseña sin usar la comprobación por correo electrónico para demostrar su identidad. 

El flujo de cambio de contraseña requiere los siguientes pasos:

1. El usuario inicia sesión en su cuenta local. Si la sesión sigue activa, Azure AD B2C autoriza al usuario y pasa al siguiente paso.
1. En **Contraseña anterior**, el usuario comprueba su contraseña anterior. En **Nueva contraseña**, crea y confirma su nueva contraseña.

   ![Captura de pantalla que muestra dos diálogos numerados para realizar un cambio de contraseña.](./media/add-password-change-policy/password-change-flow.png)  

> [!TIP]
> Un usuario puede usar el flujo de cambio de contraseña que se describe en este artículo solo si sabe su contraseña y quiere cambiarla. Le recomendamos que habilite también el [autoservicio de restablecimiento de contraseña](add-password-reset-policy.md) para que se admitan aquellos casos en los que el usuario olvida su contraseña.

::: zone pivot="b2c-user-flow"

[!INCLUDE [active-directory-b2c-limited-to-custom-policy](../../includes/active-directory-b2c-limited-to-custom-policy.md)]

::: zone-end

::: zone pivot="b2c-custom-policy"

## <a name="prerequisites"></a>Prerequisites

* Siga los pasos de [Introducción a las directivas personalizadas en Active Directory B2C](tutorial-create-user-flows.md?pivots=b2c-custom-policy).
* [Tutorial: Registro de una aplicación web en Azure Active Directory B2C](tutorial-register-applications.md)

## <a name="add-the-elements"></a>Adición de los elementos

1. Abra el archivo *TrustFrameworkExtensions.xml*. Agregue el siguiente elemento **ClaimType** al elemento [ClaimsSchema](claimsschema.md), con un identificador de `oldPassword`:

    ```xml
    <BuildingBlocks>
      <ClaimsSchema>
        <ClaimType Id="oldPassword">
          <DisplayName>Old Password</DisplayName>
          <DataType>string</DataType>
          <UserHelpText>Enter your old password</UserHelpText>
          <UserInputType>Password</UserInputType>
        </ClaimType>
      </ClaimsSchema>
    </BuildingBlocks>
    ```

1. Un elemento [ClaimsProvider](claimsproviders.md) contiene el perfil técnico que autentica al usuario. Agregue los siguientes proveedores de notificaciones al elemento **ClaimsProviders**:

    ```xml
    <ClaimsProviders>
      <ClaimsProvider>
        <DisplayName>Local Account SignIn</DisplayName>
        <TechnicalProfiles>
          <TechnicalProfile Id="login-NonInteractive-PasswordChange">
            <DisplayName>Local Account SignIn</DisplayName>
            <InputClaims>
              <InputClaim ClaimTypeReferenceId="oldPassword" PartnerClaimType="password" Required="true" />
              </InputClaims>
            <IncludeTechnicalProfile ReferenceId="login-NonInteractive" />
          </TechnicalProfile>
        </TechnicalProfiles>
      </ClaimsProvider>
      <ClaimsProvider>
        <DisplayName>Local Account Password Change</DisplayName>
        <TechnicalProfiles>
          <TechnicalProfile Id="LocalAccountWritePasswordChangeUsingObjectId">
            <DisplayName>Change password (username)</DisplayName>
            <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.SelfAssertedAttributeProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
            <Metadata>
              <Item Key="ContentDefinitionReferenceId">api.selfasserted</Item>
            </Metadata>
            <InputClaims>
              <InputClaim ClaimTypeReferenceId="objectId" />
            </InputClaims>
            <OutputClaims>
              <OutputClaim ClaimTypeReferenceId="oldPassword" Required="true" />
              <OutputClaim ClaimTypeReferenceId="newPassword" Required="true" />
              <OutputClaim ClaimTypeReferenceId="reenterPassword" Required="true" />
            </OutputClaims>
            <ValidationTechnicalProfiles>
              <ValidationTechnicalProfile ReferenceId="login-NonInteractive-PasswordChange" />
              <ValidationTechnicalProfile ReferenceId="AAD-UserWritePasswordUsingObjectId" />
            </ValidationTechnicalProfiles>
          </TechnicalProfile>
        </TechnicalProfiles>
      </ClaimsProvider>
    </ClaimsProviders>
    ```

1. El elemento [UserJourneys](userjourneys.md) define la ruta de acceso que el usuario toma al interactuar con la aplicación. Agregue el elemento **UserJourneys** si no existe, con el elemento **UserJourney** con un identificador de `PasswordChange`:

    ```xml
    <UserJourneys>
      <UserJourney Id="PasswordChange">
        <OrchestrationSteps>
          <OrchestrationStep Order="1" Type="ClaimsProviderSelection" ContentDefinitionReferenceId="api.signuporsignin">
            <ClaimsProviderSelections>
              <ClaimsProviderSelection TargetClaimsExchangeId="LocalAccountSigninEmailExchange" />
            </ClaimsProviderSelections>
          </OrchestrationStep>
          <OrchestrationStep Order="2" Type="ClaimsExchange">
            <ClaimsExchanges>
              <ClaimsExchange Id="LocalAccountSigninEmailExchange" TechnicalProfileReferenceId="SelfAsserted-LocalAccountSignin-Email" />
            </ClaimsExchanges>
          </OrchestrationStep>
          <OrchestrationStep Order="3" Type="ClaimsExchange">
            <ClaimsExchanges>
              <ClaimsExchange Id="NewCredentials" TechnicalProfileReferenceId="LocalAccountWritePasswordChangeUsingObjectId" />
            </ClaimsExchanges>
          </OrchestrationStep>
          <OrchestrationStep Order="4" Type="ClaimsExchange">
            <ClaimsExchanges>
              <ClaimsExchange Id="AADUserReadWithObjectId" TechnicalProfileReferenceId="AAD-UserReadUsingObjectId" />
            </ClaimsExchanges>
          </OrchestrationStep>
          <OrchestrationStep Order="5" Type="SendClaims" CpimIssuerTechnicalProfileReferenceId="JwtIssuer" />
        </OrchestrationSteps>
        <ClientDefinition ReferenceId="DefaultWeb" />
      </UserJourney>
    </UserJourneys>
    ```

1. Guarde el archivo de directiva *TrustFrameworkExtensions.xml*.
1. Copie el archivo *ProfileEdit.xml* que descargó con el paquete de inicio y asígnele el nombre de archivo *ProfileEditPasswordChange.xml*.
1. Abra el archivo nuevo y actualice el atributo **PolicyId** con un valor único. Este valor es el nombre de la directiva. Por ejemplo, *B2C_1A_profile_edit_password_change*.
1. Modifique el atributo **ReferenceId** de **DefaultUserJourney** para que coincida con el identificador del nuevo recorrido del usuario que ha creado. Por ejemplo, *PasswordChange*.
1. Guarde los cambios.

## <a name="upload-and-test-the-policy"></a>Cargue y pruebe la directiva.

1. Inicie sesión en [Azure Portal](https://portal.azure.com/).
1. Asegúrese de usar el directorio que contiene el inquilino de Azure AD B2C. Para ello, seleccione el icono **Directorios y suscripciones** en la barra de herramientas del portal.
1. En la página **Configuración del portal | Directorios y suscripciones**, busque el directorio de Azure AD B2C en la lista **Nombre de directorio** y seleccione **Cambiar**.
1. Elija **Todos los servicios** en la esquina superior izquierda de Azure Portal, y busque y seleccione **Azure AD B2C**.
1. Seleccione **Marco de experiencia de identidad**.
1. En **Directivas personalizadas**, seleccione **Cargar directiva**.
1. Seleccione **Sobrescribir la directiva si existe**, y busque y seleccione el archivo *TrustFrameworkExtensions.xml*.
1. Seleccione **Cargar**.
1. Repita los pasos 5 a 7 para el archivo del usuario de confianza, como *ProfileEditPasswordChange.xml*.

## <a name="run-the-policy"></a>Ejecución de la directiva

1. Abra la directiva que ha cambiado. Por ejemplo, *B2C_1A_profile_edit_password_change*.
1. En **Aplicación**, seleccione la aplicación que registró anteriormente. Para ver el token, **URL de respuesta** debe mostrar `https://jwt.ms`.
1. Seleccione **Ejecutar ahora**. En la nueva pestaña que se abre, quite "&prompt=login" de la dirección URL y actualice la pestaña. A continuación, inicie sesión con la cuenta que creó anteriormente. Un cuadro de diálogo de cambio de contraseña le ofrece la opción de cambiar la contraseña.

## <a name="next-steps"></a>Pasos siguientes

* Busque la [directiva de ejemplo en GitHub](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/tree/master/scenarios/password-change).
* Obtenga información acerca de cómo [configurar la complejidad de la contraseña en Azure AD B2C](password-complexity.md).
* Configure un [flujo de restablecimiento de contraseña](add-password-reset-policy.md).

::: zone-end
