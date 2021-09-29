---
title: Delegación de registros de usuario y suscripciones a producto
description: Obtenga información acerca de cómo delegar el registro de usuario y la suscripción de producto un tercero en la administración de la API de Azure.
services: api-management
documentationcenter: ''
author: dlepow
manager: cfowler
editor: ''
ms.assetid: 8b7ad5ee-a873-4966-a400-7e508bbbe158
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 08/13/2021
ms.author: danlep
ms.openlocfilehash: a642206389c5c92209a8d47e7a72992296b46428
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/24/2021
ms.locfileid: "128603626"
---
# <a name="how-to-delegate-user-registration-and-product-subscription"></a>Delegación de registros de usuario y suscripciones a producto

La delegación permite que su sitio web se apropie de los datos del usuario y realice una validación personalizada. Con la delegación, puede controlar el inicio de sesión o el registro de desarrolladores y la suscripción de productos mediante el sitio web existente, en lugar de la funcionalidad integrada del portal para desarrolladores. 

[!INCLUDE [premium-dev-standard-basic.md](../../includes/api-management-availability-premium-dev-standard-basic.md)]

## <a name="delegating-developer-sign-in-and-sign-up"></a><a name="delegate-signin-up"> </a>Delegación de inicios de sesión y suscripciones de desarrolladores

Para delegar el inicio de sesión y la suscripción para desarrolladores al sitio web existente, deberá crear un punto de conexión de delegación especial en su sitio. Esta delegación especial actúa como punto de entrada para cualquier solicitud de inicio de sesión o registro iniciada desde el portal para desarrolladores de API Management.

El flujo de trabajo final será el siguiente:

1. El desarrollador hace clic en el enlace de inicio de sesión o de suscripción que se encuentra en el portal para desarrolladores de API Management.
2. El explorador se redirige al extremo de delegación.
3. El punto de conexión de delegación a cambio redirige al usuario o presenta al usuario el inicio de sesión o el registro. 
4. Tras el inicio de sesión o el registro correctos, se redirige al usuario al portal para desarrolladores de API Management en la ubicación que dejó.

### <a name="set-up-api-management-to-route-requests-via-delegation-endpoint"></a>Configuración de API Management para enrutar solicitudes a través del punto de conexión de delegación

1. En Azure Portal, busque **Portal para desarrolladores** en su recurso de API Management.
2. Haga clic en la opción **Delegación**. 
3. Haga clic en la casilla para activar **"Delegar inicio de sesión y suscripción"** .

:::image type="content" source="media/api-management-howto-setup-delegation/api-management-delegation-signin-up.png" alt-text="Introducción a la delegación":::

4. Determine la URL del extremo especial de delegación y escríbala en el campo **Dirección URL del extremo de delegación**. 
5. En el campo **Clave de validación de delegación** haga lo siguiente:
    * Escriba un secreto usado para calcular una firma proporcionada para comprobar que la solicitud se origina en API Management. 
    * Haga clic en el botón **Generar** para que API Management genere una clave aleatoria.
6. Haga clic en **Guardar**.

### <a name="create-your-delegation-endpoint"></a>Creación del punto de conexión de delegación

>[!NOTE]
> Aunque el procedimiento siguiente proporciona ejemplos de la operación **SignIn**, puede realizar la administración de cuentas mediante cualquiera de las operaciones disponibles con los pasos siguientes. 

Pasos recomendados para crear un nuevo punto de conexión de delegación que se implementará en el sitio:

1. Recibir una solicitud de la forma siguiente:
   
   > *http:\//www.yourwebsite.com/apimdelegation?operation=SignIn&returnUrl={URL de la página de origen}&salt={string}&amp;sig={string}*
   
    Parámetros de consulta en el caso de inicio de sesión o suscripción:

   | Parámetro | Descripción |
   | --------- | ----------- |
   | **operation** | Identifica el tipo de solicitud de delegación. Operaciones disponibles: **SignIn**, **ChangePassword**, **ChangeProfile**, **CloseAccount** y **SignOut**. |
   | **returnUrl** | La dirección URL en la que el usuario hizo clic en un vínculo de suscripción o de inicio de sesión. |
   | **salt** | Una cadena salt especial que se usa para procesar un hash de seguridad. |
   | **sig** | Un hash de seguridad procesado que se comparará con su propio hash procesado. |
   
3. Compruebe que la solicitud procede de Azure API Management (opcional, pero especialmente recomendado por motivos de seguridad).
   
   * Procese un hash HMAC-SHA512 de una cadena según los parámetros de consulta **returnUrl** y **salt**. Para obtener más información, consulte este [código de ejemplo].
     
     ```
         HMAC(salt + '\n' + returnUrl)
     ```
   * Compare el hash procesado anteriormente con el valor del parámetro de consulta **sig** . Si los dos hashes coinciden, vaya a paso siguiente. De lo contrario, deniegue la solicitud.
4. Compruebe que recibe una solicitud de inicio de sesión o registro.
    * El parámetro de consulta **operation** se establecerá en "**SignIn**".
5. Presente al usuario la interfaz de usuario para que inicie sesión o se suscriba.
    * Si el usuario se suscribe, cree una cuenta correspondiente para él en API Management. 
      * [Cree un usuario] con la API de REST de API Management. 
      * Establezca el identificador de usuario en el mismo valor en el almacén de usuarios o en un nuevo id. de seguimiento sencillo.
6. Cuando el usuario se autentique correctamente:
   
   * [Solicite un token de acceso compartido] a través de la API de REST de API Management.
   * Anexe un parámetro de consulta **returnUrl** a la URL de SSO que se recibió de la llamada de API anterior. Por ejemplo:
     
     > `https://contoso.developer.azure-api.net/signin-sso?token=<URL-encoded token>&returnUrl=%2Freturn%2Furl` 
     
   * Redirija al usuario a la URL producida anteriormente.

>[!NOTE]
> Para las operaciones de administración de la cuenta (**ChangePassword**, **ChangeProfile** y **CloseAccount**), pase los siguientes parámetros de consulta:
> 
>    | Parámetro | Descripción |
>    | --------- | ----------- |
>    | **operation** | Identifica el tipo de solicitud de delegación. |
>    | **userId** | El id. de usuario de la cuenta para administrar. |
>    | **salt** | Una cadena salt especial que se usa para procesar un hash de seguridad. |
>    | **sig** | Un hash de seguridad procesado que se comparará con su propio hash procesado. |

## <a name="delegating-product-subscription"></a><a name="delegate-product-subscription"> </a>Delegación de suscripciones a productos

La delegación de una suscripción a productos funciona de forma similar a la delegación de inicio de sesión y suscripción de usuario. El flujo de trabajo final sería el siguiente:

1. El desarrollador selecciona un producto en el portal para desarrolladores de API Management y hace clic en el botón **Suscribir**.
2. El explorador se redirige al extremo de delegación.
3. El extremo de delegación realiza los pasos necesarios para la suscripción al producto que usted diseña. Estos pueden incluir lo siguiente: 
   * Redirigir a otra página para solicitar información de facturación.
   * Hacer preguntas adicionales.
   * Almacenar la información y no requerir ninguna acción del usuario.

### <a name="enable-the-api-management-functionality"></a>Habilitación de la API de REST de API Management

En la página **Delegación**, haga clic en **Delegar suscripción de productos**.

### <a name="create-your-delegation-endpoint"></a>Creación del punto de conexión de delegación

Pasos recomendados para crear un nuevo punto de conexión de delegación que se implementará en el sitio:

1. Reciba una solicitud de la forma siguiente:
   
   > *http:\//www.yourwebsite.com/apimdelegation?operation={operación}&amp;productId={producto al que se suscribe}&amp;userId={usuario que realiza la solicitud}&amp;salt={cadena}&amp;sig={cadena}*
   >
   
    Parámetros de consulta en el caso de suscripción a producto:

   | Parámetro | Descripción |
   | --------- | ----------- |
   | **operation** | Identifica el tipo de solicitud de delegación. Las opciones válidas de solicitudes de suscripción a producto son: <ul><li>**Subscribe**: una solicitud para suscribir al usuario a un producto determinado con el id. especificado (consulte más información a continuación).</li><li>**Unsubscribe**: una solicitud para cancelar la suscripción de un usuario a un producto.</li><li>**Renew**: una solicitud para renovar una suscripción (por ejemplo, que esté a punto de expirar).</li></ul> |
   | **productId** | En *Subscribe*, el id. del producto al que el usuario solicitó la suscripción. |
   | **subscriptionId** | En *Unsubscribe* y *Renew*, el identificador de la suscripción del producto. |
   | **userId** | En *Subscribe*, el id. del usuario que realiza la solicitud. |
   | **salt** | Una cadena salt especial que se usa para procesar un hash de seguridad. |
   | **sig** | Un hash de seguridad procesado que se comparará con su propio hash procesado. |

2. Compruebe que la solicitud procede de Azure API Management (opcional, pero especialmente recomendado por motivos de seguridad).
   
   * Procesar un hash HMAC-SHA512 de una cadena en función de los parámetros de consulta **productId**, **userId** y **salt**:
     ```
     HMAC(**salt** + '\n' + **productId** + '\n' + **userId**)
     ```
   * Compare el hash procesado anteriormente con el valor del parámetro de consulta **sig** . Si los dos hashes coinciden, vaya a paso siguiente. De lo contrario, deniegue la solicitud.
3. Procese cualquier suscripción a producto en función del tipo de operación solicitada en **operation**; por ejemplo, facturación, preguntas adicionales, etc.
4. Tras la correcta suscripción del usuario al producto por su parte, suscriba al usuario al producto de API Management [llamando a la API de REST para suscripciones].

## <a name="example-code"></a><a name="delegate-example-code"> </a>Ejemplo de código

Estos ejemplos de código enseñan cómo:

* Tomar la *clave de validación de delegación*, que se establece en la pantalla de **delegación** del portal del publicador.
* Crear un HMAC que valida la firma, probando la validez del valor de returnUrl que se pasó.

Con una ligera modificación, puede utilizar el mismo código para el **productId** y el **userId**.

### <a name="c-code-to-generate-hash-of-returnurl"></a>Código C# para generar el hash de returnUrl

```csharp
using System.Security.Cryptography;

string key = "delegation validation key";
string returnUrl = "returnUrl query parameter";
string salt = "salt query parameter";
string signature;
using (var encoder = new HMACSHA512(Convert.FromBase64String(key)))
{
    signature = Convert.ToBase64String(encoder.ComputeHash(Encoding.UTF8.GetBytes(salt + "\n" + returnUrl)));
    // change to (salt + "\n" + productId + "\n" + userId) when delegating product subscription
    // compare signature to sig query parameter
}
```

### <a name="nodejs-code-to-generate-hash-of-returnurl"></a>Código NodeJS para generar el hash de returnUrl

```
var crypto = require('crypto');

var key = 'delegation validation key'; 
var returnUrl = 'returnUrl query parameter';
var salt = 'salt query parameter';

var hmac = crypto.createHmac('sha512', new Buffer(key, 'base64'));
var digest = hmac.update(salt + '\n' + returnUrl).digest();
// change to (salt + "\n" + productId + "\n" + userId) when delegating product subscription
// compare signature to sig query parameter

var signature = digest.toString('base64');
```

> [!IMPORTANT]
> Debe [volver a publicar el portal para desarrolladores](api-management-howto-developer-portal-customize.md#publish) para que los cambios en la delegación surtan efecto.

## <a name="next-steps"></a>Pasos siguientes
- [Obtenga más información sobre el portal para desarrolladores.](api-management-howto-developer-portal.md)
- [Autentíquese con Azure AD](api-management-howto-aad.md) o con [Azure AD B2C](api-management-howto-aad-b2c.md).
- ¿Tiene más preguntas sobre el portal para desarrolladores? [Encuentre respuestas en nuestras preguntas más frecuentes](developer-portal-faq.md).

[Delegating developer sign-in and sign-up]: #delegate-signin-up
[Delegating product subscription]: #delegate-product-subscription
[Solicitud de un token de acceso compartido]: /rest/api/apimanagement/2020-12-01/user/get-shared-access-token
[Cree un usuario]: /rest/api/apimanagement/2020-12-01/user/create-or-update
[llamando a la API de REST para las suscripciones]: /rest/api/apimanagement/2020-12-01/subscription/create-or-update
[Next steps]: #next-steps
[código de ejemplo]: #delegate-example-code

[api-management-delegation-signin-up]: ./media/api-management-howto-setup-delegation/api-management-delegation-signin-up.png
