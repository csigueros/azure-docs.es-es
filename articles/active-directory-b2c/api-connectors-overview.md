---
title: Acerca de los conectores de API en Azure AD B2C
description: Use los conectores de API de Azure Active Directory (Azure AD) para personalizar y ampliar los flujos de usuario con las API REST.
services: active-directory-b2c
ms.service: active-directory
ms.subservice: B2C
ms.topic: how-to
ms.date: 07/05/2021
ms.author: mimart
author: msmimart
manager: celestedg
ms.custom: it-pro
zone_pivot_groups: b2c-policy-type
ms.openlocfilehash: cade077501e499893686fcc856129deb61e8778e
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/13/2021
ms.locfileid: "121723318"
---
# <a name="use-api-connectors-to-customize-and-extend-sign-up-user-flows"></a>Uso de conectores de API para personalizar y extender los flujos de usuario de registro

[!INCLUDE [active-directory-b2c-choose-user-flow-or-custom-policy](../../includes/active-directory-b2c-choose-user-flow-or-custom-policy.md)]

::: zone pivot="b2c-user-flow"

## <a name="overview"></a>Información general 

Como desarrollador o administrador de TI, puede usar conectores de API para integrar los flujos de usuario de registro con las API REST a fin de personalizar la experiencia de registro e integrarla con los sistemas externos. Por ejemplo, con los conectores de API, puede:

- **Validar los datos de entrada del usuario**. Haga una validación de los datos de usuario mal formados o no válidos. Por ejemplo, puede validar los datos proporcionados por el usuario frente a los datos existentes en un almacén de datos externo o una lista de valores permitidos. Si los datos no son válidos, puede pedirle al usuario que proporcione datos válidos o impedir que el usuario continúe con el flujo de registro.
- **Compruebe la identidad del usuario**. Use un servicio de verificación de identidad para agregar un nivel de seguridad adicional a las decisiones de creación de cuentas.
- **Integrarse con un flujo de trabajo de aprobación personalizado**. Conéctese a un sistema de aprobaciones personalizado para administrar y limitar la creación de cuentas.
- **Enriquecer los tokens con atributos de orígenes externos**. Enriquezca los tokens con atributos sobre el usuario a partir de orígenes externos a Azure AD B2C, como sistemas en la nube, almacenes de usuarios personalizados, sistemas de permisos personalizados, servicios de identidad heredados, etc.
- **Sobrescribir atributos de usuario**. Asigne un valor a un atributo recopilado del usuario o cámbiele el formato. Por ejemplo, si un usuario escribe el nombre con mayúsculas o minúsculas, se le puede dar un formato en el que solo la primera letra esté en mayúscula. 
- **Ejecutar una lógica de negocios personalizada**. Puede desencadenar eventos descendentes en los sistemas de la nube para enviar notificaciones push, actualizar las bases de datos corporativas, administrar permisos, auditar bases de datos y realizar otras acciones personalizadas.

Un conector de API proporciona a Azure AD B2C la información necesaria para llamar al punto de conexión de API al definir la dirección URL del punto de conexión HTTP y la autenticación de dicha llamada. Una vez que configure un conector de API, puede habilitarlo para un paso específico de un flujo de usuario. Cuando un usuario llega a ese paso del flujo de registro, se invoca el conector de API y se materializa como una solicitud HTTP POST a la API, enviando la información de usuario ("notificaciones") como pares de clave y valor en un cuerpo JSON. La respuesta de la API puede afectar a la ejecución del flujo de usuario. Por ejemplo, la respuesta de la API puede impedir que un usuario se registre, pedir al usuario que vuelva a especificar la información, o sobrescribir los atributos de usuario.

## <a name="where-you-can-enable-an-api-connector-in-a-user-flow"></a>Dónde se puede habilitar un conector de API en un flujo de usuario

El conector de API se puede habilitar en tres lugares del flujo de usuario:

- **Después de la federación con un proveedor de identidades durante el registro**: solo se aplica a las experiencias de registro.
- **Antes de crear el usuario**: solo se aplica a las experiencias de registro
- **Antes de enviar el token (versión preliminar)** : se aplica a los registros y los inicios de sesión

### <a name="after-federating-with-an-identity-provider-during-sign-up"></a>Después de la federación con un proveedor de identidades durante el registro

Inmediatamente después de que el usuario se autentique con un proveedor de identidades (como Google, Facebook o Azure AD), se invoca un conector de API en este paso del proceso de registro. Este paso precede a la ***página de recopilación de atributos***, que es el formulario que se muestra al usuario para recopilar los atributos de usuario. Este paso no se invoca si un usuario se registra con una cuenta local. A continuación, se muestran ejemplos de escenarios del conector de API que se pueden habilitar en este paso:

- Use el correo electrónico o la identidad federada que el usuario proporcionó para buscar notificaciones en un sistema existente. Devuelva estas notificaciones del sistema existente, rellene previamente la página de recopilación de atributos y haga que estén disponibles para que se devuelvan en el token.
- Implemente una lista de permitidos o bloqueados según la identidad social.

### <a name="before-creating-the-user"></a>Antes de crear el usuario

Después de la página de recopilación de atributos, se invoca un conector de API en este paso del proceso de registro, si se incluye uno. Este paso siempre se invoca antes de crear una cuenta de usuario. A continuación, se muestran ejemplos de escenarios que se pueden habilitar en este momento del registro:

- Validar datos introducidos por el usuario y pedirle que vuelva a enviarlos.
- Bloquear un registro de usuario en función de los datos especificados por el usuario.
- Compruebe la identidad del usuario.
- Consultar en sistemas externos los datos existentes sobre el usuario para devolverlos en el token de aplicación o almacenarlos en Azure AD.

### <a name="before-sending-the-token-preview"></a>Antes de enviar el token (versión preliminar)

[!INCLUDE [b2c-public-preview-feature](../../includes/active-directory-b2c-public-preview.md)]

Antes de emitir un token, se invoca a un conector de API en este paso del proceso de registro o inicio de sesión. Los siguientes son ejemplos de escenarios que se pueden habilitar en este paso:
- Enriquecer el token con atributos sobre el usuario a partir de orígenes distintos del directorio, incluidos sistemas de identidad heredados, sistemas de recursos humanos, almacenes de usuarios externos, etc.
- Enriquecer el token con atributos de grupo o rol que se almacenan y administran en su propio sistema de permisos. 
- Aplicar transformaciones o manipulaciones de notificaciones a los valores de las notificaciones del directorio.

::: zone-end

::: zone pivot="b2c-custom-policy"

Identity Experience Framework, que subyace a Azure Active Directory B2C (Azure AD B2C), permite la integración en API de RESTful dentro de un recorrido del usuario. En este artículo se muestra cómo crear un recorrido de usuario que interactúe con un servicio RESTful mediante un [perfil técnico de RESTful](restful-technical-profile.md).

Mediante Azure AD B2C, puede agregar su propia lógica de negocios a un recorrido del usuario mediante una llamada a su propio servicio RESTful. Identity Experience Framework puede enviar y recibir datos del servicio RESTful para intercambiar notificaciones. Por ejemplo, puede:

- **Validar los datos de entrada del usuario**. Por ejemplo, puede verificar que la dirección de correo electrónico que proporciona el usuario existe en la base de datos del cliente y, en caso contrario, presentar un error.
- **Procesar notificaciones**. Si un usuario escribe su nombre todo con mayúsculas o minúsculas, la API de REST puede dar formato al nombre para que solo la primera letra esté en mayúscula y lo devuelva a Azure AD B2C.
- **Enriquecer datos de usuario mediante integración adicional en aplicaciones de línea de negocio corporativas**. el servicio RESTful puede recibir la dirección de correo electrónico del usuario, consultar la base de datos del cliente y devolver el número de fidelidad del usuario a Azure AD B2C. Luego, las notificaciones devueltas pueden almacenarse en la cuenta de Azure AD del usuario, evaluarse en los siguientes pasos de orquestación o incluirse en el token de acceso.
- **Ejecutar una lógica de negocios personalizada**. Puede enviar notificaciones push, actualizar las bases de datos corporativas, ejecutar un proceso de migración de usuarios, administrar permisos, auditar bases de datos y realizar otros flujos de trabajo.

![Diagrama de un intercambio de notificaciones del servicio RESTful](media/api-connectors-overview/restful-service-claims-exchange.png)

> [!NOTE]
> Si el servicio RESTful es lento o no responde a Azure AD B2C, el tiempo de expiración es de 30 segundos y el número de reintentos es de dos veces (es decir, hay tres intentos en total). La configuración de tiempo de expiración y el número de reintentos no es configurable actualmente.

## <a name="calling-a-restful-service"></a>Llamada a un servicio RESTful

La interacción incluye un intercambio de notificaciones de información entre las notificaciones de la API de REST y Azure AD B2C. La integración con los servicios REST se pueden diseñar de las siguientes maneras:

- **Perfil técnico de validación**. La llamada al servicio RESTful tiene lugar dentro de un [perfil técnico de validación](validation-technical-profile.md) del [perfil técnico autoafirmado](self-asserted-technical-profile.md) especificado o un [control de visualización de verificación](display-control-verification.md) de un [control de visualización](display-controls.md). El perfil técnico de validación valida los datos de que proporciona el usuario antes de que el recorrido del usuario avance. Con el perfil técnico de validación, puede realizar las siguientes tareas:

  - Enviar notificaciones a la API de REST.
  - Validar las notificaciones y generar mensajes de error personalizados que se muestren al usuario.
  - Devolver notificaciones de la API REST a los pasos de orquestación siguientes.

- **Intercambio de notificaciones**. Un intercambio de notificaciones directo se puede configurar llamando a un perfil técnico de la API de REST directamente desde un paso de orquestación de un [recorrido del usuario](userjourneys.md). Esta definición se limita a:

  - Enviar notificaciones a la API de REST.
  - Validar las notificaciones y generar mensajes de error personalizados que se devuelvan a la aplicación.
  - Devolver notificaciones de la API REST a los pasos de orquestación siguientes.

Puede agregar una llamada a la API REST en cualquier paso del recorrido del usuario definido por una directiva personalizada. Por ejemplo, puede llamar a una API REST:

- Durante el inicio de sesión, justo antes de que Azure AD B2C valide las credenciales.
- Inmediatamente después del inicio de sesión.
- Antes de que Azure AD B2C cree una cuenta en el directorio.
- Después de que Azure AD B2C cree una cuenta en el directorio.
- Antes de que Azure AD B2C emita un token de acceso.

![Colección de perfiles técnicos de validación](media/api-connectors-overview/validation-technical-profile.png)

## <a name="sending-data"></a>Envío de datos

En el [perfil técnico de RESTful](restful-technical-profile.md), el elemento `InputClaims` contiene una lista de notificaciones que se enviarán al servicio RESTful. Puede asignar el nombre de la notificación al nombre definido en el servicio RESTful, establecer un valor predeterminado y usar [solucionadores de notificaciones](claim-resolver-overview.md).

Puede configurar la manera que las notificaciones de entrada se envían al proveedor de notificaciones de RESTful mediante el atributo SendClaimsIn. Los valores posibles son:

- **Body**, se envía en el cuerpo de la solicitud HTTP POST en formato JSON.
- **Form**, se envía en el cuerpo de la solicitud HTTP POST en un formato de valor de clave separado por "&" (Y comercial).
- **Header**, se envía en el encabezado de la solicitud HTTP GET.
- **QueryString**, se envía en la cadena de consulta de la solicitud HTTP GET.

Cuando la opción **Body** está configurada, el perfil técnico de la API de REST le permite enviar una carga de JSON compleja a un punto de conexión. Para obtener más información, consulte [Envío de una carga de JSON](restful-technical-profile.md#send-a-json-payload).

## <a name="receiving-data"></a>Recibir datos

El elemento `OutputClaims` del [perfil técnico de RESTful](restful-technical-profile.md) contiene una lista de las notificaciones devueltas por la API de REST. Es posible que tenga que asignar el nombre de la notificación definida en la directiva al nombre definido en la API REST. También puede incluir notificaciones que no devuelve el proveedor de identidades de la API de REST, siempre que establezca el atributo DefaultValue.

Las notificaciones de salida analizadas por el proveedor de notificaciones RESTful siempre esperan analizar una respuesta de Body de JSON plana, como:

```json
{
  "name": "Emily Smith",
  "email": "emily@outlook.com",
  "loyaltyNumber":  1234
}
```

Las notificaciones de salida deben tener un aspecto similar al siguiente fragmento de código XML:

```xml
<OutputClaims>
  <OutputClaim ClaimTypeReferenceId="displayName" PartnerClaimType="name" />
  <OutputClaim ClaimTypeReferenceId="email" />
  <OutputClaim ClaimTypeReferenceId="loyaltyNumber" />
</OutputClaims>
```

### <a name="handling-null-values"></a>Tratamiento de valores NULL 

Se utiliza un valor NULL en una base de datos cuando falta el valor de una columna o es desconocido.  No incluya claves JSON con un valor `null`. En el ejemplo siguiente, el correo electrónico devuelve un valor `null`:

```json
{
  "name": "Emily Smith",
  "email": null,
  "loyaltyNumber":  1234
}
```

Cuando un elemento es NULL, puede:

- Omitir el par clave-valor del código JSON.
- Devolver un valor que corresponda al tipo de datos de la notificación de Azure AD B2C. Por ejemplo, para el tipo de datos `string`, devuelva una cadena vacía (`""`). Para el tipo de datos `integer`, devuelva un valor cero (`0`). Para el tipo de datos `dateTime`, devuelva un valor mínimo (`1970-00-00T00:00:00.0000000Z`).

En el siguiente ejemplo, se muestra cómo controlar un valor NULL. El correo electrónico se ha omitido en el código JSON:

```json
{
  "name": "Emily Smith",
  "loyaltyNumber":  1234
}
```

### <a name="parse-a-nested-json-body"></a>Análisis de un cuerpo JSON anidado

Para analizar una respuesta de un cuerpo JSON anidado, establezca los metadatos de ResolveJsonPathsInJsonTokens en true. En una notificación de salida, establezca PartnerClaimType en el elemento de la ruta de acceso JSON que quiere generar.

```json
"contacts": [
  {
    "id": "MAINCONTACT_1",
    "person": {
      "name": "Emily Smith",
      "loyaltyNumber":  1234,
      "emails": [
        {
          "id": "EMAIL_1",
          "type": "WORK",
          "email": "email@domain.com"
        }
      ]
    }
  }
],
```


Las notificaciones de salida deben tener un aspecto similar al siguiente fragmento de código XML:

```xml
<OutputClaims>
  <OutputClaim ClaimTypeReferenceId="displayName" PartnerClaimType="contacts[0].person.name" />
  <OutputClaim ClaimTypeReferenceId="email" PartnerClaimType="contacts[0].person.emails[0].email" />
  <OutputClaim ClaimTypeReferenceId="loyaltyNumber" PartnerClaimType="contacts[0].person.loyaltyNumber" />
</OutputClaims>
```

## <a name="localize-the-rest-api"></a>Localización de la API de REST

En un perfil técnico de RESTful, puede querer enviar el idioma o la configuración regional de la sesión actual y, si es necesario, producir un mensaje de error localizado. Mediante el [solucionador de notificaciones](claim-resolver-overview.md), puede enviar una notificación contextual, como el idioma del usuario. En el ejemplo siguiente se muestra un perfil técnico de RESTful que muestra este escenario.

```xml
<TechnicalProfile Id="REST-ValidateUserData">
  <DisplayName>Validate user input data</DisplayName>
  <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.RestfulProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
  <Metadata>
    <Item Key="ServiceUrl">https://your-app.azurewebsites.net/api/identity</Item>
    <Item Key="AuthenticationType">None</Item>
    <Item Key="SendClaimsIn">Body</Item>
    <Item Key="IncludeClaimResolvingInClaimsHandling">true</Item>
  </Metadata>
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="userLanguage" DefaultValue="{Culture:LCID}" AlwaysUseDefaultValue="true" />
    <InputClaim ClaimTypeReferenceId="email" PartnerClaimType="emailAddress" />
  </InputClaims>
  <UseTechnicalProfileForSessionManagement ReferenceId="SM-Noop" />
</TechnicalProfile>
```

## <a name="handling-error-messages"></a>Control de mensajes de error

Es posible la API de REST tenga que devolver un mensaje de error, como "No se encuentra el usuario en el sistema CRM". Si se produce un error, la API de REST debe devolver un mensaje de error HTTP 409 (código de estado de respuesta de conflicto). Para obtener más información, consulte [Perfil técnico de RESTful](restful-technical-profile.md#returning-validation-error-message).

Este comportamiento solo se puede lograr mediante la llamada a un perfil técnico de API REST desde un perfil técnico de validación. Esto permite al usuario corregir los datos de la página y volver a ejecutar la validación tras el envío de dicha página.

Si hace referencia a un perfil técnico de la API de REST directamente desde un recorrido del usuario, se redirigirá al usuario a la aplicación de usuario de confianza con el mensaje de error correspondiente.

::: zone-end

## <a name="development-of-your-rest-api"></a>Desarrollo de la API REST 

La API REST se puede desarrollar en cualquier plataforma y se puede escribir en cualquier lenguaje de programación, siempre y cuando sea segura y pueda enviar y recibir notificaciones en formato JSON.

La solicitud al servicio de la API de REST procede de servidores de Azure AD B2C. El servicio de la API de REST se debe publicar en un punto de conexión HTTPS accesible públicamente. Las llamadas a la API de REST llegarán desde una dirección IP del centro de datos de Azure.

Puede usar funciones en la nube sin servidor, como los [desencadenadores HTTP de Azure Functions](../azure-functions/functions-bindings-http-webhook-trigger.md), para facilitar el desarrollo.

Debe diseñar el servicio de la API REST y sus componentes subyacentes (como la base de datos y el sistema de archivos) para que tengan alta disponibilidad.

[!INCLUDE [active-directory-b2c-https-cipher-tls-requirements](../../includes/active-directory-b2c-https-cipher-tls-requirements.md)]

## <a name="next-steps"></a>Pasos siguientes

::: zone pivot="b2c-user-flow"

- Obtenga información sobre la [Adición de un conector de API a un flujo de usuario de registro](add-api-connector.md).
- Obtenga información sobre el [Enriquecimiento de tokens con notificaciones de orígenes externos mediante conectores de API](add-api-connector-token-enrichment.md).
- Obtenga información sobre la [Protección de un conector de API](secure-rest-api.md).
- Comience a trabajar con nuestros [ejemplos](api-connector-samples.md#api-connector-rest-api-samples).

::: zone-end

::: zone pivot="b2c-custom-policy"

Consulte los siguientes artículos para obtener ejemplos del uso de un perfil técnico de RESTful:

- [Tutorial: Adición de un conector de API a un flujo de usuario de registro](add-api-connector.md)
- [Tutorial: Agregue los intercambios de notificaciones de la API de REST a directivas personalizadas de Azure Active Directory B2C](add-api-connector-token-enrichment.md)
- [Protección de los servicios de la API de REST](secure-rest-api.md)
- [Referencia: Perfil técnico de RESTful](restful-technical-profile.md)

::: zone-end
