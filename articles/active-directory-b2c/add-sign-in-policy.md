---
title: Configuración de un flujo de inicio de sesión
titleSuffix: Azure Active Directory B2C
description: Aprenda a configurar un flujo de inicio de sesión en Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 08/24/2021
ms.custom: project-no-code
ms.author: mimart
ms.subservice: B2C
zone_pivot_groups: b2c-policy-type
ms.openlocfilehash: 78556d5f6d6a203a3d105f971cb7850109daa01a
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/24/2021
ms.locfileid: "128570281"
---
# <a name="set-up-a-sign-in-flow-in-azure-active-directory-b2c"></a>Configuración de un flujo de inicio de sesión en Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-choose-user-flow-or-custom-policy](../../includes/active-directory-b2c-choose-user-flow-or-custom-policy.md)]

## <a name="sign-in-flow-overview"></a>Introducción al flujo de inicio de sesión

La directiva de inicio de sesión contiene las siguientes directrices: 

* Los usuarios pueden iniciar sesión con una cuenta local de Azure AD B2C
* Registrarse o iniciar sesión con una cuenta de redes sociales
* Restablecimiento de contraseña
* Los usuarios no pueden suscribirse a una cuenta local de Azure AD B2C. Para crear una cuenta, un administrador puede usar [Azure Portal](manage-users-portal.md#create-a-consumer-user) o [MS Graph API](microsoft-graph-operations.md).

![Flujo de edición de perfiles](./media/add-sign-in-policy/sign-in-user-flow.png)

## <a name="prerequisites"></a>Prerrequisitos

Si todavía no lo ha hecho, [registre una aplicación web en Azure Active Directory B2C](tutorial-register-applications.md).

::: zone pivot="b2c-user-flow"

## <a name="create-a-sign-in-user-flow"></a>Creación de un flujo de usuario de inicio de sesión

Para agregar la directiva de inicio de sesión:

1. Inicie sesión en [Azure Portal](https://portal.azure.com).
1. Seleccione el icono **Directorios y suscripciones** en la barra de herramientas del portal.
1. En la página **Configuración del portal | Directorios y suscripciones**, busque el directorio de Azure AD B2C en la lista **Nombre de directorio** y seleccione **Cambiar**.
1. En Azure Portal, busque y seleccione **Azure AD B2C**.
1. En **Directivas**, seleccione **Flujos de usuario** y **Nuevo flujo de usuario**.
1. En la página **Crear un flujo de usuario**, seleccione el flujo de usuario **Iniciar sesión**.
1. En **Seleccione una versión**, elija **Recomendada** y, luego, seleccione **Crear**. [Más información](user-flow-versions.md) sobre las versiones del flujo de usuario.
1. Escriba un **nombre** para el flujo de usuario. Por ejemplo, *signupsignin1*.
1. En **Proveedores de identidades**, seleccione al menos un proveedor de identidades:

   * En **Cuentas locales**, seleccione una de las opciones siguientes: **Email signin** (Inicio de sesión de correo electrónico), **User ID signin** (Inicio de sesión de usuario), **Phone signin** (Inicio de sesión de teléfono), **Phone/Email signin** (Inicio de sesión de teléfono o correo electrónico), **User ID/Email signin** (Inicio de sesión de id. de usuario o correo electrónico) o **None** (Ninguno). [Más información](sign-in-options.md).
   * En **Proveedores de identidades sociales**, seleccione cualquiera de los proveedores de identidades sociales o empresariales externos que haya configurado. [Más información](add-identity-provider.md).
1. En **Autenticación multifactor**, si quiere exigir a los usuarios que comprueben su identidad con un segundo método de autenticación, elija el tipo de método y cuándo aplicar la autenticación multifactor (MFA). [Más información](multi-factor-authentication.md).
1. En **Acceso condicional**, si ha configurado directivas de acceso condicional para el inquilino de Azure AD B2C y quiere habilitarlas para este flujo de usuario, active la casilla **Aplicar directivas de acceso condicional**. No es necesario especificar un nombre de directiva. [Más información](conditional-access-user-flow.md?pivots=b2c-user-flow).
1. En **Notificaciones de la aplicación**, elija las notificaciones que quiere que se devuelvan a la aplicación en el token. Para obtener la lista completa de valores, seleccione **Mostrar más**, elija los valores y, después, seleccione **Aceptar**.
   > [!NOTE]
   > También puede [crear atributos personalizados](user-flow-custom-attributes.md?pivots=b2c-user-flow) para usarlos en el inquilino de Azure AD B2C.
1. Haga clic en **Crear** para agregar el flujo de usuario. El prefijo *B2C_1* se anexa automáticamente al nombre.

### <a name="test-the-user-flow"></a>Prueba del flujo de usuario

1. Seleccione el flujo de usuario que ha creado para abrir su página de información general y, luego, **Ejecutar flujo de usuario**.
1. En **Aplicación**, seleccione la aplicación web denominada *webapp1* que registró anteriormente. La **dirección URL de respuesta** debe mostrar `https://jwt.ms`.
1. Haga clic en **Ejecutar flujo de usuario**.
1. Debería poder iniciar sesión con la cuenta que ha creado (mediante MS Graph API), sin el vínculo de registro. El token devuelto incluye las notificaciones que ha seleccionado.

::: zone-end

::: zone pivot="b2c-custom-policy"

## <a name="remove-the-sign-up-link"></a>Eliminación del vínculo de registro

El perfil técnico **SelfAsserted-LocalAccountSignin-Email** es [autoafirmado](self-asserted-technical-profile.md), y se invoca durante el flujo de registro o de inicio de sesión. Para quitar el vínculo de registro, establezca los metadatos de `setting.showSignupLink` en `false`. Invalide los perfiles técnicos de SelfAsserted-LocalAccountSignin-Email en el archivo de extensión. 

1. Abra el archivo de extensiones de la directiva. Por ejemplo, _`SocialAndLocalAccounts/` **`TrustFrameworkExtensions.xml`**_.
1. Busque el elemento `ClaimsProviders`. Si el elemento no existe, agréguelo.
1. Agregue el siguiente proveedor de notificaciones al elemento `ClaimsProviders`:

    ```xml
    <!--
    <ClaimsProviders> -->
      <ClaimsProvider>
        <DisplayName>Local Account</DisplayName>
        <TechnicalProfiles>
          <TechnicalProfile Id="SelfAsserted-LocalAccountSignin-Email">
            <Metadata>
              <Item Key="setting.showSignupLink">false</Item>
            </Metadata>
          </TechnicalProfile>
        </TechnicalProfiles>
      </ClaimsProvider>
    <!--
    </ClaimsProviders> -->
    ```

1. Dentro del elemento `<BuildingBlocks>`, agregue el siguiente valor de [ContentDefinition](contentdefinitions.md) para hacer referencia a la versión 1.2.0 o un URI de datos más reciente:

    ```XML
    <!-- 
    <BuildingBlocks> 
      <ContentDefinitions>-->
        <ContentDefinition Id="api.localaccountsignup">
          <DataUri>urn:com:microsoft:aad:b2c:elements:contract:unifiedssp:1.2.0</DataUri>
        </ContentDefinition>
      <!--
      </ContentDefinitions>
    </BuildingBlocks> -->
    ```

## <a name="update-and-test-your-policy"></a>Carga y prueba de la directiva

1. Inicie sesión en [Azure Portal](https://portal.azure.com).
1. Asegúrese de que usa el directorio que contiene el inquilino de Azure AD. Para ello, seleccione el icono **Directorios y suscripciones** en la barra de herramientas del portal.
1. En la página **Configuración del portal | Directorios y suscripciones**, busque el directorio de Azure AD en la lista **Nombre de directorio** y, después, seleccione **Cambiar**.
1. Elija **Todos los servicios** en la esquina superior izquierda de Azure Portal, y busque y seleccione **Registros de aplicaciones**.
1. Seleccione **Marco de experiencia de identidad**.
1. Seleccione **Cargar directiva personalizada** y cargue el archivo de la directiva que ha modificado, *TrustFrameworkExtensions.xml*.
1. Seleccione la directiva de inicio de sesión que cargó y haga clic en el botón **Ejecutar ahora**.
1. Debería poder iniciar sesión con la cuenta que ha creado (mediante MS Graph API), sin el vínculo de registro.

::: zone-end

## <a name="next-steps"></a>Pasos siguientes

* [Iniciar sesión con un proveedor de identidades de redes sociales](add-identity-provider.md)
* Configure un [flujo de restablecimiento de contraseña](add-password-reset-policy.md).
