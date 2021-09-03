---
title: Definición de atributos personalizados en Azure Active Directory B2C | Microsoft Docs
description: Defina atributos personalizados para su aplicación en Azure Active Directory B2C a fin de recopilar información sobre sus clientes.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 08/10/2021
ms.custom: project-no-code
ms.author: mimart
ms.subservice: B2C
zone_pivot_groups: b2c-policy-type
ms.openlocfilehash: 836802e65c8eb76f17bbf053e54ed9dbedf54049
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/13/2021
ms.locfileid: "121740030"
---
# <a name="define-custom-attributes-in-azure-active-directory-b2c"></a>Definición de atributos personalizados en Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-choose-user-flow-or-custom-policy](../../includes/active-directory-b2c-choose-user-flow-or-custom-policy.md)]

En el artículo [Adición de notificaciones y personalización de la entrada del usuario mediante directivas personalizadas](configure-user-input.md) aprendió a usar los [atributos de perfil de usuario](user-profile-attributes.md) integrados. En este artículo, habilitará un atributo personalizado en el directorio de Azure Active Directory B2C (Azure AD B2C). Más adelante, puede usar el nuevo atributo como una notificación personalizada en los [flujos de usuario](user-flow-overview.md) o las [directivas personalizadas](user-flow-overview.md) simultáneamente.

El directorio de Azure AD B2C viene con un [conjunto integrado de atributos](user-profile-attributes.md). Sin embargo, a menudo es necesario crear sus propios atributos para administrar un escenario concreto; por ejemplo, en las siguientes situaciones:

* Una aplicación orientada al cliente necesita conservar un atributo **LoyaltyId**.
* Un proveedor de identidades tiene un identificador de usuario único (**uniqueUserGUID**) que se debe conservar.
* Un recorrido del usuario personalizado debe conservar el estado del usuario, **migrationStatus**, para que otra lógica funcione.

En el contexto de este artículo, los términos *propiedad de extensión*, *atributo personalizado* y *notificación personalizada* se refieren a lo mismo. El nombre varía según el contexto, como aplicación, objeto o directiva.

Azure AD B2C le permite ampliar el conjunto de atributos que se almacenan en cada cuenta de usuario. También puede leer y escribir estos atributos mediante [Microsoft Graph API](microsoft-graph-operations.md).

## <a name="prerequisites"></a>Requisitos previos

[!INCLUDE [active-directory-b2c-customization-prerequisites](../../includes/active-directory-b2c-customization-prerequisites.md)]

## <a name="create-a-custom-attribute"></a>Creación de un atributo personalizado

1. Inicie sesión en [Azure Portal](https://portal.azure.com/) como administrador global del inquilino de Azure AD B2C.
1. Asegúrese de que está usando el directorio que contiene su inquilino de Azure AD B2C cambiando a él en la esquina superior derecha de Azure Portal. Seleccione la información de la suscripción y, después, seleccione **Cambiar directorio**.
1. Elija **Todos los servicios** en la esquina superior izquierda de Azure Portal, busque y seleccione **Azure AD B2C**.
1. Seleccione **Atributos de usuario** y, a continuación, seleccione **Agregar**.
1. Proporcione un **Nombre** para el atributo personalizado (por ejemplo, "ShoeSize").
1. Elija un **Tipo de datos**. Solo están disponibles **String**, **Boolean** e **Int**.
1. Si lo desea, escriba una **Descripción** con fines informativos.
1. Haga clic en **Crear**.

El atributo personalizado ahora está disponible en la lista de **Atributos de usuario** y puede usarlo en los flujos de usuario. Solo se crea la primera vez que se utiliza en cualquier flujo de usuario y no cuando se agrega a la lista de **atributos de usuario**.

::: zone pivot="b2c-user-flow"

## <a name="use-a-custom-attribute-in-your-user-flow"></a>Uso de un atributo personalizado en el flujo de usuario

1. En el inquilino de Azure AD B2C, seleccione **Flujos de usuario**.
1. Haga clic en la directiva (por ejemplo, "B2C_1_SignupSignin") para abrirla.
1. Seleccione **Atributos de usuario** y, después, seleccione el atributo personalizado (por ejemplo, "ShoeSize"). Haga clic en **Save**(Guardar).
1. Seleccione **Notificaciones de aplicación** y, después, seleccione el atributo personalizado.
1. Haga clic en **Save**(Guardar).

Después de crear un usuario mediante un flujo de usuarios que usa el atributo personalizado recién creado, el objeto se puede consultar en el [Explorador de Microsoft Graph](https://developer.microsoft.com/graph/graph-explorer). Como alternativa, puede usar la característica [Ejecutar flujo de usuario](./tutorial-create-user-flows.md) del flujo de usuario para comprobar la experiencia del cliente. Ahora debe ver **ShoeSize** en la lista de atributos que se recopilan durante el viaje de suscripción y en el token enviado de vuelta a la aplicación.

::: zone-end

## <a name="azure-ad-b2c-extensions-app"></a>Aplicación de extensiones de Azure AD B2C

Los atributos de extensión solo se pueden registrar en un objeto de aplicación, aunque pueden contener datos para un usuario. El atributo de extensión está conectado a la aplicación denominada `b2c-extensions-app`. No modifique esta aplicación, ya que Azure AD B2C la usa para almacenar los datos de usuario. Puede encontrar esta aplicación en los registros de aplicaciones de Azure AD B2C. 

::: zone pivot="b2c-user-flow"

Para obtener el identificador de la aplicación:

1. Inicie sesión en [Azure Portal](https://portal.azure.com).
1. Seleccione el filtro **Directorio y suscripción** en el menú superior y, luego, elija el directorio que contiene el inquilino de Azure AD B2C.
1. En el menú de la izquierda, seleccione **Azure AD B2C**. O bien, seleccione **Todos los servicios** y busque y seleccione **Azure AD B2C**.
1. Seleccione **Registros de aplicaciones** y, después, **Todas las aplicaciones**.
1. Seleccione la aplicación `b2c-extensions-app. Do not modify. Used by AADB2C for storing user data.`.
1. Copie el **id. de la aplicación**. Ejemplo: `11111111-1111-1111-1111-111111111111`.
 
::: zone-end

::: zone pivot="b2c-custom-policy"

Obtención de las propiedades de la aplicación:

1. Inicie sesión en [Azure Portal](https://portal.azure.com).
1. Seleccione el filtro **Directorio y suscripción** en el menú superior y, luego, elija el directorio que contiene el inquilino de Azure AD B2C.
1. En el menú de la izquierda, seleccione **Azure AD B2C**. O bien, seleccione **Todos los servicios** y busque y seleccione **Azure AD B2C**.
1. Seleccione **Registros de aplicaciones** y, después, **Todas las aplicaciones**.
1. Seleccione la aplicación `b2c-extensions-app. Do not modify. Used by AADB2C for storing user data.`.
1. Copie los identificadores siguientes en el Portapapeles y guárdelos:
    * **Identificador de aplicación**. Ejemplo: `11111111-1111-1111-1111-111111111111`.
    * **Identificador de objeto**. Ejemplo: `22222222-2222-2222-2222-222222222222`.

## <a name="modify-your-custom-policy"></a>Modificación de la directiva personalizada

Para habilitar los atributos personalizados en la directiva, especifique el **Id. de aplicación** y el **Id. de objeto** de la aplicación en los metadatos de perfil técnico AAD-Common. El perfil técnico *AAD-Common* se encuentra en el perfil técnico base de [Azure Active Directory](active-directory-technical-profile.md) y ofrece compatibilidad con la administración de usuarios Azure AD. Otros perfiles técnicos de Azure AD incluyen AAD-Common para aprovechar su configuración. Reemplace el perfil técnico AAD-Common en el archivo de extensión.

1. Abra el archivo de extensiones de la directiva. Por ejemplo, <em>`SocialAndLocalAccounts/`**`TrustFrameworkExtensions.xml`**</em>.
1. Busque el elemento ClaimsProviders. Agregue un nuevo ClaimsProvider al elemento ClaimsProviders.
1. Inserte el **identificador de aplicación** que registró previamente entre los elementos de apertura `<Item Key="ClientId">` y cierre `</Item>`.
1. Inserte el **identificador de objeto de aplicación** que registró previamente entre los elementos de apertura `<Item Key="ApplicationObjectId">` y cierre `</Item>`.

    ```xml
    <!-- 
    <ClaimsProviders> -->
      <ClaimsProvider>
        <DisplayName>Azure Active Directory</DisplayName>
        <TechnicalProfiles>
          <TechnicalProfile Id="AAD-Common">
            <Metadata>
              <!--Insert b2c-extensions-app application ID here, for example: 11111111-1111-1111-1111-111111111111-->  
              <Item Key="ClientId"></Item>
              <!--Insert b2c-extensions-app application ObjectId here, for example: 22222222-2222-2222-2222-222222222222-->
              <Item Key="ApplicationObjectId"></Item>
            </Metadata>
          </TechnicalProfile>
        </TechnicalProfiles> 
      </ClaimsProvider>
    <!-- 
    </ClaimsProviders> -->
    ```

## <a name="upload-your-custom-policy"></a>Carga de la directiva personalizada

1. Inicie sesión en [Azure Portal](https://portal.azure.com).
2. Asegúrese de usar el directorio que contiene el inquilino de Azure AD. Para ello, seleccione el filtro **Directorio y suscripción** en el menú superior y luego el directorio que contiene el inquilino de Azure AD B2C.
3. Elija **Todos los servicios** en la esquina superior izquierda de Azure Portal, y busque y seleccione **Registros de aplicaciones**.
4. Seleccione **Marco de experiencia de identidad**.
5. Seleccione **Cargar directiva personalizada** y cargue los dos archivos de la directiva TrustFrameworkExtensions.xml modificados.

> [!NOTE]
> La primera vez que el perfil técnico de Azure AD conserva la notificación en el directorio, comprueba si existe el atributo personalizado. Si no existe, lo crea.  

## <a name="create-a-custom-attribute-through-azure-portal"></a>Creación de un atributo personalizado mediante Azure Portal

Los mismos atributos de extensión se comparten entre directivas integradas y personalizadas. Cuando se agregan atributos personalizados a través de la experiencia del portal, estos se registran mediante la aplicación **b2c-extensions-app** que existe en cada inquilino B2C.

Puede crear estos atributos mediante la interfaz de usuario del portal antes o después de usarlos en las directivas personalizadas. Cuando se crea un atributo **loyaltyId** en el portal, tiene que hacer referencia a él como se muestra a continuación:

|Nombre     |Se usa en |
|---------|---------|
|`extension_loyaltyId`  | Directiva personalizada|
|`extension_<b2c-extensions-app-guid>_loyaltyId`  | [Microsoft Graph API](microsoft-graph-operations.md)|

En el ejemplo siguiente se muestra el uso de atributos personalizados en una definición de notificación de directiva personalizada de Azure AD B2C.

```xml
<BuildingBlocks>
  <ClaimsSchema>
    <ClaimType Id="extension_loyaltyId">
      <DisplayName>Loyalty Identification</DisplayName>
      <DataType>string</DataType>
      <UserHelpText>Your loyalty number from your membership card</UserHelpText>
      <UserInputType>TextBox</UserInputType>
    </ClaimType>
  </ClaimsSchema>
</BuildingBlocks>
```

En el ejemplo siguiente se muestra el uso de un atributo personalizado en una directiva personalizada de Azure AD B2C en un perfil técnico y en notificaciones de entrada, salida y persistentes.

```xml
<InputClaims>
  <InputClaim ClaimTypeReferenceId="extension_loyaltyId"  />
</InputClaims>
<PersistedClaims>
  <PersistedClaim ClaimTypeReferenceId="extension_loyaltyId" />
</PersistedClaims>
<OutputClaims>
  <OutputClaim ClaimTypeReferenceId="extension_loyaltyId" />
</OutputClaims>
```

::: zone-end

## <a name="using-custom-attribute-with-ms-graph-api"></a>Uso de un atributo personalizado con MS Graph API

Microsoft Graph API admite la creación y actualización de un usuario con atributos de extensión. Los atributos de extensión de Graph API se denominan mediante la convención `extension_ApplicationClientID_attributename`, donde `ApplicationClientID` es el **id. de la aplicación (cliente)** de la aplicación `b2c-extensions-app`. Tenga en cuenta que el **id. de la aplicación (cliente)** , tal como se representa en el nombre del atributo de extensión, no incluye guiones. Por ejemplo:

```json
"extension_831374b3bd5041bfaa54263ec9e050fc_loyaltyId": "212342" 
``` 

## <a name="next-steps"></a>Pasos siguientes

Siga las instrucciones sobre cómo [agregar notificaciones y personalizar la entrada del usuario mediante directivas personalizadas](configure-user-input.md). En este ejemplo se usa una notificación "city" integrada. Para usar un atributo personalizado, reemplace "city" por sus propios atributos personalizados.
