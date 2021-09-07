---
title: 'Adición de conectores de API a flujos de autoservicio de registro: Azure AD'
description: Configurar una API Web para usarla en un flujo de usuario.
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: article
ms.date: 07/13/2021
ms.author: mimart
author: msmimart
manager: celestedg
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 456e02b5d84dfde9534a62ea909da513ff8f1c81
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/13/2021
ms.locfileid: "121746149"
---
# <a name="add-an-api-connector-to-a-user-flow"></a>Adición de un conector de API a un flujo de usuario

Para usar un [conector de API](api-connectors-overview.md), primero debe crear el conector de API y, después, habilitarlo en un flujo de usuario.

> [!IMPORTANT]
>
> - **A partir del 12 de julio de 2021**, si los clientes de Azure AD B2B configuran nuevas integraciones de Google para usarlas con registro de autoservicio para sus aplicaciones personalizadas o de línea de negocio, la autenticación con identidades de Google no funcionará hasta que las autenticaciones se trasladen a las vistas web del sistema. [Más información](google-federation.md#deprecation-of-web-view-sign-in-support).
> - **A partir del 30 de septiembre de 2021,** Google [retira la compatibilidad con el inicio de sesión en la vista web insertada](https://developers.googleblog.com/2016/08/modernizing-oauth-interactions-in-native-apps.html). Si sus aplicaciones autentican a los usuarios con una vista web insertada y va a usar la federación de Google con [Azure AD B2C](../../active-directory-b2c/identity-provider-google.md) o Azure AD B2B para las [invitaciones de usuarios externos](google-federation.md) o el [registro de autoservicio](identity-providers.md), los usuarios de Google Gmail no podrán autenticarse. [Más información](google-federation.md#deprecation-of-web-view-sign-in-support).

## <a name="create-an-api-connector"></a>Creación de un conector de API

1. Inicie sesión en [Azure Portal](https://portal.azure.com/).
2. En **Servicios de Azure**, seleccione **Azure Active Directory**.
3. En el menú de la izquierda, seleccione **External Identities**.
4. Seleccione **All API connectors** (Todos los conectores de API) y **New API connector** (Nuevo conector de API).

    :::image type="content" source="media/self-service-sign-up-add-api-connector/api-connector-new.png" alt-text="Indicación de la configuración básica, como la dirección URL de destino y el nombre para mostrar de un conector de API durante la experiencia de creación.":::

5. Escriba el nombre para mostrar de la llamada. Por ejemplo, **Comprobar el estado de aprobación**.
6. Proporcione el valor de **Dirección URL del punto de conexión** de la llamada API.
7. Elija el **tipo de autenticación** y configure la información de autenticación para llamar a la API. Obtenga más información sobre la [Protección de un conector de API](self-service-sign-up-secure-api-connector.md).

    :::image type="content" source="media/self-service-sign-up-add-api-connector/api-connector-config.png" alt-text="Indicación de la configuración de autenticación para un conector de API durante la experiencia de creación.":::

8. Seleccione **Guardar**.

## <a name="the-request-sent-to-your-api"></a>Solicitud enviada a la API
Un conector de API se materializa como una solicitud **HTTP POST** y envía los atributos de usuario ("claims") como pares de clave y valor en un cuerpo JSON. Los atributos se serializan de forma similar a las propiedades de usuario de [Microsoft Graph](/graph/api/resources/user#properties). 

**Solicitud de ejemplo**
```http
POST <API-endpoint>
Content-type: application/json

{
 "email": "johnsmith@fabrikam.onmicrosoft.com",
 "identities": [ // Sent for Google, Facebook, and Email One Time Passcode identity providers 
     {
     "signInType":"federated",
     "issuer":"facebook.com",
     "issuerAssignedId":"0123456789"
     }
 ],
 "displayName": "John Smith",
 "givenName":"John",
 "surname":"Smith",
 "jobTitle":"Supplier",
 "streetAddress":"1000 Microsoft Way",
 "city":"Seattle",
 "postalCode": "12345",
 "state":"Washington",
 "country":"United States",
 "extension_<extensions-app-id>_CustomAttribute1": "custom attribute value",
 "extension_<extensions-app-id>_CustomAttribute2": "custom attribute value",
 "ui_locales":"en-US"
}
```

Solo se pueden enviar en la solicitud las propiedades de usuario y los atributos personalizados que se enumeran en la experiencia **Azure Active Directory** > **Identidades externas** > **Atributos de usuario personalizados**.

Los atributos personalizados existen en el formato **extension_\<extensions-app-id>_AttributeName** en el directorio. La API esperará recibir las notificaciones con este mismo formato serializado. Para más información acerca de los atributos personalizados, consulte cómo [definir atributos personalizados para flujos de autoservicio de registro](user-flow-add-custom-attributes.md).

Además, las notificaciones normalmente se envían en todas las solicitudes:
- **Configuraciones regionales de interfaz de usuario ("ui_locales"):** configuraciones regionales de un usuario final configuradas en su dispositivo. La API puede usar esto para devolver respuestas internacionalizadas.
<!-- - **Step ('step')** - The step or point on the user flow that the API connector was invoked for. Values include:
  - `PostFederationSignup` - corresponds to "After federating with an identity provider during sign-up"
  - `PostAttributeCollection` - corresponds to "Before creating the user"
- **Client ID ('client_id')** - The `appId` value of the application that an end-user is authenticating to in a user flow. This is *not* the resource application's `appId` in access tokens. -->
- **Dirección de correo electrónico ("email")** o [**identidades ("identidades")** ](/graph/api/resources/objectidentity): la API puede usar estas notificaciones para identificar al usuario final que se autentica en la aplicación.

> [!IMPORTANT]
> Si una notificación no tiene un valor en el momento en que se llama al punto de conexión de la API, la notificación no se enviará a la API. La API debe estar diseñada para comprobar y controlar explícitamente el caso en el que una notificación no está en la solicitud.

## <a name="enable-the-api-connector-in-a-user-flow"></a>Habilitación del conector de API en un flujo de usuario

Siga estos pasos para agregar el conector de API a un flujo de usuario de autoservicio de registro.

1. Inicie sesión en [Azure Portal](https://portal.azure.com/) como administrador de Azure AD.
2. En **Servicios de Azure**, seleccione **Azure Active Directory**.
3. En el menú de la izquierda, seleccione **External Identities**.
4. Seleccione **Flujos de usuario** y, después, seleccione el flujo de usuario para el que desea habilitar el conector de API.
5. Seleccione **Conectores de API** y, después, seleccione los puntos de conexión de API que desea invocar en los pasos siguientes del flujo de usuario:

   - **Después de la federación con un proveedor de identidades durante el registro**
   - **Antes de crear el usuario**

    :::image type="content" source="media/self-service-sign-up-add-api-connector/api-connectors-user-flow-select.png" alt-text="Selección del conector de API que se va a usar para un paso del flujo de usuario, como &quot;Antes de crear el usuario&quot;.":::

6. Seleccione **Guardar**.

## <a name="after-federating-with-an-identity-provider-during-sign-up"></a>Después de la federación con un proveedor de identidades durante el registro

Inmediatamente después de que el usuario se autentique con un proveedor de identidades (como Google, Facebook o Azure AD), se invoca un conector de API en este paso del proceso de registro. Este paso precede a la ***página de recopilación de atributos***, que es el formulario que se muestra al usuario para recopilar los atributos de usuario.

### <a name="example-request-sent-to-the-api-at-this-step"></a>Solicitud de ejemplo enviada a la API en este paso
```http
POST <API-endpoint>
Content-type: application/json

{
 "email": "johnsmith@fabrikam.onmicrosoft.com",
 "identities": [ // Sent for Google, Facebook, and Email One Time Passcode identity providers 
     {
     "signInType":"federated",
     "issuer":"facebook.com",
     "issuerAssignedId":"0123456789"
     }
 ],
 "displayName": "John Smith",
 "givenName":"John",
 "lastName":"Smith",
 "ui_locales":"en-US"
}
```

Las notificaciones exactas enviadas a la API dependen de la información proporcionada por el proveedor de identidades. Siempre se envía "email".

### <a name="expected-response-types-from-the-web-api-at-this-step"></a>Tipos de respuesta esperados de la API web en este paso

Cuando la API web recibe una solicitud HTTP de Azure AD durante un flujo de usuario, puede devolver estas respuestas:

- Respuesta de continuación
- Respuesta de bloqueo

#### <a name="continuation-response"></a>Respuesta de continuación

Una respuesta de continuación indica que el flujo de usuario debe continuar en el paso siguiente: la página de colección de atributos.

En una respuesta de continuación, la API puede devolver notificaciones. Si la API devuelve una notificación, esta realiza lo siguiente:

- Rellena previamente el campo de entrada en la página de colección de atributos.

Vea un ejemplo de una [respuesta de continuación](#example-of-a-continuation-response).

#### <a name="blocking-response"></a>Respuesta de bloqueo

Una respuesta de bloqueo termina el flujo de usuario. La API puede emitirla intencionadamente para detener el flujo de usuario y mostrar una página de bloqueo al usuario. La página de bloqueo muestra el mensaje `userMessage` proporcionado por la API.

Vea un ejemplo de una [respuesta de bloqueo](#example-of-a-blocking-response).

## <a name="before-creating-the-user"></a>Antes de crear el usuario

Después de la página de recopilación de atributos, se invoca un conector de API en este paso del proceso de registro, si se incluye uno. Este paso siempre se invoca antes de crear una cuenta de usuario en Azure AD. 

### <a name="example-request-sent-to-the-api-at-this-step"></a>Solicitud de ejemplo enviada a la API en este paso

```http
POST <API-endpoint>
Content-type: application/json

{
 "email": "johnsmith@fabrikam.onmicrosoft.com",
 "identities": [ // Sent for Google, Facebook, and Email One Time Passcode identity providers 
     {
     "signInType":"federated",
     "issuer":"facebook.com",
     "issuerAssignedId":"0123456789"
     }
 ],
 "displayName": "John Smith",
 "givenName":"John",
 "surname":"Smith",
 "jobTitle":"Supplier",
 "streetAddress":"1000 Microsoft Way",
 "city":"Seattle",
 "postalCode": "12345",
 "state":"Washington",
 "country":"United States",
 "extension_<extensions-app-id>_CustomAttribute1": "custom attribute value",
 "extension_<extensions-app-id>_CustomAttribute2": "custom attribute value",
 "ui_locales":"en-US"
}
```
Las notificaciones exactas enviadas a la API dependen de la información recopilada por el usuario o proporcionada por el proveedor de identidades.

### <a name="expected-response-types-from-the-web-api-at-this-step"></a>Tipos de respuesta esperados de la API web en este paso

Cuando la API web recibe una solicitud HTTP de Azure AD durante un flujo de usuario, puede devolver estas respuestas:

- Respuesta de continuación
- Respuesta de bloqueo
- Respuesta de validación

#### <a name="continuation-response"></a>Respuesta de continuación
Una respuesta de continuación indica que el flujo de usuario debe continuar en el paso siguiente: creación del usuario en el directorio.

En una respuesta de continuación, la API puede devolver notificaciones. Si la API devuelve una notificación, esta realiza lo siguiente:

- Invalida cualquier valor que ya se haya asignado a la notificación de la página de colección de atributos.

Vea un ejemplo de una [respuesta de continuación](#example-of-a-continuation-response).

#### <a name="blocking-response"></a>Respuesta de bloqueo
Una respuesta de bloqueo termina el flujo de usuario. La API puede emitirla intencionadamente para detener el flujo de usuario y mostrar una página de bloqueo al usuario. La página de bloqueo muestra el mensaje `userMessage` proporcionado por la API.

Vea un ejemplo de una [respuesta de bloqueo](#example-of-a-blocking-response).

### <a name="validation-error-response"></a>Respuesta de error de validación
 Cuando la API responde con una respuesta de error de validación, el flujo de usuario permanece en la página de colección de atributos y se muestra `userMessage` al usuario. El usuario puede editar el formulario y volver a enviarlo. Este tipo de respuesta se puede usar para la validación de datos de entrada.

Vea un ejemplo de una [respuesta de error de validación](#example-of-a-validation-error-response).

## <a name="example-responses"></a>Respuestas de ejemplo

### <a name="example-of-a-continuation-response"></a>Ejemplo de una respuesta de continuación

```http
HTTP/1.1 200 OK
Content-type: application/json

{
    "version": "1.0.0",
    "action": "Continue",
    "postalCode": "12349", // return claim
    "extension_<extensions-app-id>_CustomAttribute": "value" // return claim
}
```

| Parámetro                                          | Tipo              | Obligatorio | Descripción                                                                                                                                                                                                                                                                            |
| -------------------------------------------------- | ----------------- | -------- | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| version                                            | String            | Sí      | Versión de la API.                                                                                                                                                                                                                                                                |
| action                                             | String            | Sí      | El valor debe ser `Continue`.                                                                                                                                                                                                                                                              |
| \<builtInUserAttribute>                            | \<attribute-type> | No       | Los valores se pueden almacenar en el directorio si están seleccionados como **Claim to receive** (Notificación para recibir) en la configuración del conector de API y **User attribute** (Atributo de usuario) de un flujo de usuario. Los valores se pueden devolver en el token si están seleccionados como **Application claim** (Notificación de aplicación).                                              |
| \<extension\_{extensions-app-id}\_CustomAttribute> | \<attribute-type> | No       | No es necesario que la notificación contenga `_<extensions-app-id>_`, es *opcional*. Los valores devueltos pueden sobrescribir los valores recopilados de un usuario.  |

### <a name="example-of-a-blocking-response"></a>Ejemplo de una respuesta de bloqueo

```http
HTTP/1.1 200 OK
Content-type: application/json

{
    "version": "1.0.0",
    "action": "ShowBlockPage",
    "userMessage": "There was a problem with your request. You are not able to sign up at this time.",
}

```

| Parámetro   | Tipo   | Obligatorio | Descripción                                                                |
| ----------- | ------ | -------- | -------------------------------------------------------------------------- |
| version     | String | Sí      | Versión de la API.                                                    |
| action      | String | Sí      | El valor debe ser `ShowBlockPage`.                                              |
| userMessage | String | Sí      | Mensaje que se va a mostrar al usuario.                                            |

**Experiencia del usuario final con una respuesta de bloqueo**

:::image type="content" source="media/api-connectors-overview/blocking-page-response.png" alt-text="Una imagen de ejemplo de cómo es la experiencia del usuario final después de que una API devuelve una respuesta de bloqueo.":::

### <a name="example-of-a-validation-error-response"></a>Ejemplo de una respuesta de error de validación

```http
HTTP/1.1 400 Bad Request
Content-type: application/json

{
    "version": "1.0.0",
    "status": 400,
    "action": "ValidationError",
    "userMessage": "Please enter a valid Postal Code.",
}
```

| Parámetro   | Tipo    | Obligatorio | Descripción                                                                |
| ----------- | ------- | -------- | -------------------------------------------------------------------------- |
| version     | String  | Sí      | Versión de la API.                                                    |
| action      | String  | Sí      | El valor debe ser `ValidationError`.                                           |
| status      | Entero/cadena | Sí      | Debe ser un valor `400` o `"400"` para una respuesta ValidationError.  |
| userMessage | String  | Sí      | Mensaje que se va a mostrar al usuario.                                            |

> [!NOTE]
> El código de estado HTTP debe ser "400" además del valor de "status" en el cuerpo de la respuesta.

**Experiencia del usuario final con una respuesta de error de validación**

:::image type="content" source="media/api-connectors-overview/validation-error-postal-code.png" alt-text="Una imagen de ejemplo del aspecto de la experiencia del usuario final después de que una API devuelve una respuesta de error de validación.":::

## <a name="best-practices-and-how-to-troubleshoot"></a>Procedimientos recomendados y solución de problemas

### <a name="using-serverless-cloud-functions"></a>Uso de funciones de nube sin servidor

Las funciones sin servidor, como los [desencadenadores HTTP de Azure Functions](../../azure-functions/functions-bindings-http-webhook-trigger.md), proporcionan una manera de crear puntos de conexión de API para usarlos con el conector de API. Puede usar la función de nube sin servidor para, [por ejemplo](code-samples-self-service-sign-up.md#api-connector-azure-function-quickstarts), crear la lógica de validación y limitar los registros a dominios específicos. La función de nube sin servidor también puede llamar e invocar a otras API web, almacenes de datos y otros servicios en la nube para escenarios complejos.

### <a name="best-practices"></a>Procedimientos recomendados
Asegúrese de que:
* La API sigue los contratos de solicitud y respuesta de la API, tal y como se describe anteriormente. 
* La **URL del punto de conexión** del conector de API apunta al punto de conexión de API correcto.
* La API busca explícitamente valores NULL de las notificaciones recibidas de las que depende.
* La API implementa un método de autenticación descrito en [Protección de un conector de API](self-service-sign-up-secure-api-connector.md).
* La API responde lo más rápido posible para garantizar una experiencia de usuario fluida.
    * Azure AD espera un máximo de *20 segundos* para recibir una respuesta. Si no se recibe ninguna, realiza *un intento más (reintento)* de llamar a la API.
    * Si usa una función sin servidor o un servicio web escalable, use un plan de hospedaje que mantenga la API "activa" o "caliente" en producción. En Azure Functions, se recomienda usar como mínimo el [plan Premium](../../azure-functions/functions-scale.md).
* Garantice una alta disponibilidad de la API.
* Supervise y optimice el rendimiento de las API de nivel inferior, las bases de datos u otras dependencias de la API.
* Los puntos de conexión deben cumplir los requisitos de seguridad de TLS y cifrado de Azure AD. Para obtener más información, consulte [Requisitos de TLS y del conjunto de cifrado](../../active-directory-b2c/https-cipher-tls-requirements.md). 
 
### <a name="use-logging"></a>Uso del registro

En general, resulta útil usar las herramientas de registro que habilita el servicio de API web, como [Application Insights](../../azure-functions/functions-monitoring.md), para supervisar la API en busca de códigos de error inesperados, excepciones y rendimiento deficiente.
* Supervise los códigos de estado HTTP que no sean HTTP 200 ni 400.
* Un código de estado HTTP 401 o 403 suele indicar que hay un problema con la autenticación. Compruebe la capa de autenticación de la API y la configuración correspondiente en el conector de API.
* Use niveles más agresivos de registro (por ejemplo, "trace" o "debug") en el desarrollo, si es necesario.
* Supervise la API en busca de tiempos de respuesta prolongados. 

## <a name="next-steps"></a>Pasos siguientes
- Obtenga información sobre cómo [agregar un flujo de trabajo personalizado de aprobación al autoservicio de registro](self-service-sign-up-add-approvals.md).
- Comience con nuestros [ejemplos de inicio rápido](code-samples-self-service-sign-up.md#api-connector-azure-function-quickstarts).
