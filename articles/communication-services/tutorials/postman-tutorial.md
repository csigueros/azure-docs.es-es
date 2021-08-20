---
title: 'Tutorial: Firma y realización de solicitudes a la API de SMS de ACS con Postman'
titleSuffix: An Azure Communication Services tutorial
description: Obtenga información sobre cómo firmar y realizar solicitudes para ACS con Postman para enviar un mensaje SMS.
author: ProbablePrime
services: azure-communication-services
ms.author: rifox
ms.date: 06/30/2021
ms.topic: overview
ms.service: azure-communication-services
ms.openlocfilehash: 7691bb6ffd93c3c87872659417f91de1599a81e8
ms.sourcegitcommit: 9339c4d47a4c7eb3621b5a31384bb0f504951712
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 07/14/2021
ms.locfileid: "113767157"
---
# <a name="tutorial-sign-and-make-requests-with-postman"></a>Tutorial: Firma y realización de solicitudes con Postman
En este tutorial, vamos a configurar y usar Postman para realizar una solicitud a Azure Communication Services mediante HTTP. Al final de este tutorial, habrá enviado correctamente un mensaje SMS con Communication Services y Postman. A continuación, podrá usar Postman para explorar otras API dentro de Azure Communication Services.

En este tutorial, veremos lo siguiente:
> [!div class="checklist"]
> * Descarga de Postman
> * Configuración de Postman para firmar solicitudes HTTP
> * Realización de una solicitud a la API de SMS de Communication Services para enviar un mensaje

## <a name="prerequisites"></a>Requisitos previos

- Una cuenta de Azure con una suscripción activa. Para más información, consulte [Creación de una cuenta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F). La cuenta gratuita le proporciona 200 $ en créditos de Azure para que pruebe cualquier combinación de servicios.
- Un recurso activo de Communication Services y una cadena de conexión. [Aprenda a crear un recurso de Communication Services](../quickstarts/create-communication-resource.md).
- Un número de teléfono de ACS que pueda enviar mensajes SMS; consulte [Inicio rápido: Obtención de un número de teléfono mediante Azure Portal](../quickstarts/telephony-sms/get-phone-number.md) para obtener uno.

## <a name="downloading-and-installing-postman"></a>Descarga e instalación de Postman

Postman es una aplicación de escritorio que es capaz de realizar solicitudes de API a cualquier API HTTP. Se suele usar para probar y explorar las API. Descargaremos la [versión de escritorio más reciente desde el sitio web de Postman](https://www.postman.com/downloads/). Postman tiene versiones para Windows, Mac y Linux, por lo que debe descargar la versión adecuada para su sistema operativo. Una vez descargada, abra la aplicación. Se le presentará una pantalla de inicio, que le pedirá que inicie sesión o cree una cuenta de Postman. La creación de una cuenta es opcional y se puede omitir mediante un clic en el vínculo "Skip and go to app" (Omitir e ir a la aplicación). Al crear una cuenta, la configuración de la solicitud de API se guardará en Postman, lo que puede permitirle recoger las solicitudes en otros equipos.

:::image type="content" source="media/postman/create-account-or-skip.png" alt-text="Pantalla de inicio de Postman que muestra la capacidad de crear una cuenta u omitir el paso y pasar a la aplicación.":::

Una vez que haya creado una cuenta u omitido la creación de una, debería ver la ventana principal de Postman.

## <a name="creating-and-configuring-a-postman-collection"></a>Creación y configuración de una colección de Postman

Postman puede organizar las solicitudes de muchas maneras. Por lo que respecta a este tutorial, vamos a crear una colección de Postman. Para ello, seleccione el botón Collections (Colecciones) en el lado izquierdo de la aplicación:

:::image type="content" source="media/postman/collections-tab.png" alt-text="Pantalla principal de Postman con la pestaña Colecciones resaltada.":::

Una vez seleccionado, haga clic en "Create new Collection" (Crear nueva colección) para iniciar el proceso de creación de la colección. Se abrirá una nueva pestaña en el área central de Postman. Asigne a la colección el nombre que desee. Aquí, la colección se llama "ACS":

:::image type="content" source="media/postman/acs-collection.png" alt-text="Postman con la colección Communication Services abierta y el nombre de la colección resaltado.":::

Una vez creada la colección y con el nombre asignado, está listo para configurarla.

### <a name="adding-collection-variables"></a>Adición de variables a la colección

Para controlar la autenticación y facilitar las solicitudes, especificaremos dos variables de colección en la colección Communication Services recién creada. Estas variables están disponibles para todas las solicitudes de la colección Communication Services. Para empezar a crear variables, vaya a la pestaña Variables de la colección.

:::image type="content" source="media/postman/variable-stab.png" alt-text="Postman con una pestaña de variables de la colección Communication Services.":::

Una vez en la pestaña de la colección, cree dos variables:
- key (clave): esta variable debe ser una de las claves de la página de claves de Azure Communication Services en Azure Portal. Por ejemplo, `oW...A==`.
- endpoint (punto de conexión): esta variable debe ser el punto de conexión de Azure Communication Services de la página de claves. **Asegúrese de quitar la barra diagonal final**. Por ejemplo, `https://contoso.communication.azure.com`.

Escriba estos valores en la columna "Initial Value" (Valor inicial) de la pantalla Variables. Una vez especificados, pulse el botón "Persist All" (Conservar todo) situado encima de la tabla a la derecha. Cuando se configura correctamente, la pantalla de Postman debería tener un aspecto similar al siguiente:

:::image type="content" source="media/postman/acs-variables-set.png" alt-text="Postman con las variables de la colección Communication Services configuradas correctamente.":::

Para más información acerca de las variables, consulte la [documentación de Postman](https://learning.postman.com/docs/sending-requests/variables) al respecto.

### <a name="creating-a-pre-request-script"></a>Creación de un script previo a la solicitud

El siguiente paso consiste en crear un script previo a la solicitud en Postman. Un script previo a la solicitud es un script que se ejecuta antes de cada solicitud en Postman y puede modificar o cambiar los parámetros de la solicitud en su nombre. Lo usaremos para firmar las solicitudes HTTP de modo que Azure Communication Services la pueda autorizar. Para más información sobre los requisitos de firma, consulte la [guía sobre autenticación](/rest/api/communication/authentication).

Vamos a crear este script dentro de la colección para que se ejecute en todas las solicitudes de la colección. Para ello, en la pestaña de la colección, haga clic en la pestaña secundaria "Pre-request Script" (Script previo a la solicitud).

:::image type="content" source="media/postman/start-pre-request-script.png" alt-text="Postman con una subpestaña de script de solicitud previa de colección de Communication Services seleccionada.":::

En esta pestaña secundaria puede crear un script previo a la solicitud; para ello, escríbalo en el área de texto que aparece debajo. Puede ser más fácil escribir esto en un editor de código completo como [Visual Studio Code](https://code.visualstudio.com/) antes de pegarlo cuando esté completo. En este tutorial vamos a recorrer cada parte del script. No dude en ir directamente al final si desea simplemente copiarlo en Postman y comenzar. Comencemos a escribir el script.

### <a name="writing-the-pre-request-script"></a>Escritura del script previo a la solicitud

Lo primero que haremos es crear una cadena de hora universal coordinada (UTC) y agregarla al encabezado HTTP "Date". También almacenamos esta cadena en una variable para usarla más adelante al firmar:

```JavaScript
// Set the Date header to our Date as a UTC String.
const dateStr = new Date().toUTCString();
pm.request.headers.upsert({key:'Date', value: dateStr});
```

A continuación, vamos a aplicar el algoritmo hash al cuerpo de la solicitud mediante SHA 256 y colocarlo en el encabezado `x-ms-content-sha256`. Postman incluye algunas [bibliotecas estándar](https://learning.postman.com/docs/writing-scripts/script-references/postman-sandbox-api-reference/#using-external-libraries) para la creación de hash y la firma global, por lo que no es necesario instalarlas ni exigirlas:

```JavaScript
// Hash the request body using SHA256 and encode it as Base64
const hashedBodyStr = CryptoJS.SHA256(pm.request.body.raw).toString(CryptoJS.enc.Base64)
// And add that to the header x-ms-content-sha256
pm.request.headers.upsert({
    key:'x-ms-content-sha256',
    value: hashedBodyStr
});
```

Ahora, usaremos la variable de punto de conexión especificada anteriormente para discernir el valor del encabezado Host de HTTP. Necesitamos hacer esto porque el encabezado Host no se establece hasta después de que se procesa este script:

```JavaScript
// Get our previously specified endpoint variable
const endpoint = pm.variables.get('endpoint')
// Remove the https, prefix to create a suitable "Host" value
const hostStr = endpoint.replace('https://','');
```

Con esta información creada, ahora podemos crear la cadena, que se va a firmar para la solicitud HTTP y se compone de varios valores creados anteriormente:

```JavaScript
// This gets the part of our URL that is after the endpoint, for example in https://contoso.communication.azure.com/sms, it will get '/sms'
const url = pm.request.url.toString().replace('{{endpoint}}','');

// Construct our string which we'll sign, using various previously created values.
const stringToSign = pm.request.method + '\n' + url + '\n' + dateStr + ';' + hostStr + ';' + hashedBodyStr;
```

Por último, debemos firmar esta cadena con nuestra clave de Communication Services y, a continuación, agregarla a nuestra solicitud en el encabezado `Authorization`:

```JavaScript
// Decode our access key from previously created variables, into bytes from base64.
const key = CryptoJS.enc.Base64.parse(pm.variables.get('key'));
// Sign our previously calculated string with HMAC 256 and our key. Convert it to Base64.
const signature = CryptoJS.HmacSHA256(stringToSign, key).toString(CryptoJS.enc.Base64);

// Add our final signature in Base64 to the authorization header of the request.
pm.request.headers.upsert({
    key:'Authorization',
    value: "HMAC-SHA256 SignedHeaders=date;host;x-ms-content-sha256&Signature=" + signature
});
```

### <a name="the-final-pre-request-script"></a>Script final previo a la solicitud

El script final previo a la solicitud debe tener un aspecto similar a este:

```JavaScript
// Set the Date header to our Date as a UTC String.
const dateStr = new Date().toUTCString();
pm.request.headers.upsert({key:'Date', value: dateStr});

// Hash the request body using SHA256 and encode it as Base64
const hashedBodyStr = CryptoJS.SHA256(pm.request.body.raw).toString(CryptoJS.enc.Base64)
// And add that to the header x-ms-content-sha256
pm.request.headers.upsert({
    key:'x-ms-content-sha256',
    value: hashedBodyStr
});

// Get our previously specified endpoint variable
const endpoint = pm.variables.get('endpoint')
// Remove the https, prefix to create a suitable "Host" value
const hostStr = endpoint.replace('https://','');

// This gets the part of our URL that is after the endpoint, for example in https://contoso.communication.azure.com/sms, it will get '/sms'
const url = pm.request.url.toString().replace('{{endpoint}}','');

// Construct our string which we'll sign, using various previously created values.
const stringToSign = pm.request.method + '\n' + url + '\n' + dateStr + ';' + hostStr + ';' + hashedBodyStr;

// Decode our access key from previously created variables, into bytes from base64.
const key = CryptoJS.enc.Base64.parse(pm.variables.get('key'));
// Sign our previously calculated string with HMAC 256 and our key. Convert it to Base64.
const signature = CryptoJS.HmacSHA256(stringToSign, key).toString(CryptoJS.enc.Base64);

// Add our final signature in Base64 to the authorization header of the request.
pm.request.headers.upsert({
    key:'Authorization',
    value: "HMAC-SHA256 SignedHeaders=date;host;x-ms-content-sha256&Signature=" + signature
});
```

Escriba o pegue este script final en el área de texto de la pestaña del script previo a la solicitud:

:::image type="content" source="media/postman/finish-pre-request.png" alt-text="Postman con el script previo a la solicitud de la colección ACS escrito.":::

Una vez escrito, pulse CTRL + S o haga clic en el botón Save (Guardar) para guardar el script en la colección. 

:::image type="content" source="media/postman/save-pre-request-script.png" alt-text="Botón Guardar script previo a la solicitud de Postman.":::

## <a name="creating-a-request-in-postman"></a>Creación de una solicitud en Postman

Ahora que todo está configurado, estamos listos para crear una solicitud de Communication Services en Postman. Para empezar, haga clic en el icono de signo más (+) situado junto a la colección Communication Services:

:::image type="content" source="media/postman/create-request.png" alt-text="Botón de signo más de Postman.":::

Se creará una nueva pestaña para nuestra solicitud en Postman. Una vez creada, es necesario configurarla. Realizaremos una solicitud a la API de envío de SMS, por tanto, asegúrese de consultar la [documentación de esta API para obtener ayuda](/rest/api/communication/sms/send). Vamos a configurar la solicitud de Postman.

Para empezar, establezca el tipo de solicitud en `POST` y escriba `{{endpoint}}/sms?api-version=2021-03-07` en el campo de la dirección URL de la solicitud. Esta dirección URL usa la variable `endpoint` creada anteriormente para enviarla automáticamente al recurso de Communication Services.

:::image type="content" source="media/postman/post-request-and-url.png" alt-text="Una solicitud en Postman, con el tipo establecido en POST y la dirección URL establecida correctamente.":::

Ahora, seleccione la pestaña Body (Cuerpo) de la solicitud y, a continuación, cambie el botón de radio situado junto a "raw" (Sin procesar). A la derecha, hay una lista desplegable que tiene seleccionado "Text" (Texto), cámbiela a JSON:

:::image type="content" source="media/postman/postman-json.png" alt-text="Establecimiento del cuerpo de la solicitud en Sin procesar y formato JSON":::

Esto configurará la solicitud para enviar y recibir información en formato JSON.

En el área de texto que aparece debajo tendrá que escribir el cuerpo de la solicitud, que debe tener el formato siguiente:

```JSON
{
    "from":"<Your ACS Telephone Number>",
    "message":"<The message you'd like to send>",
    "smsRecipients": [
        {
            "to":"<The number you'd like to send the message to>"
        }
    ]
}
```

Para el valor de "from" (De), deberá [obtener un número de teléfono](../quickstarts/telephony-sms/get-phone-number.md) en el portal de Communication Services, como se mencionó anteriormente. Escríbalo sin espacios y con el prefijo del código de país. Por ejemplo: `+15555551234`. El valor de "message" (Mensaje) puede ser cualquier cosa que desee enviar, aunque `Hello from ACS` es un buen ejemplo. El valor de "to" (Para) debe ser un teléfono al que tenga acceso y que pueda recibir mensajes SMS. Usar su propio teléfono móvil es una buena idea.

Una vez escrito, es necesario guardar esta solicitud en la colección Communication Services que hemos creado anteriormente. Así se asegurará de que recoge las variables y el script previo a la solicitud que hemos creado anteriormente. Para ello, haga clic en el botón "Save" (Guardar) en la parte superior derecha del área de la solicitud.

:::image type="content" source="media/postman/postman-save.png" alt-text="Botón Guardar para una solicitud de Postman.":::

Esto hará que aparezca una ventana de cuadro de diálogo que le solicita cómo le gustaría llamar a la solicitud y dónde desea guardarla. Puede asignarle el nombre que desee, pero asegúrese de seleccionar la colección Communication Services en la mitad inferior del cuadro de diálogo:

:::image type="content" source="media/postman/postman-save-to-acs.png" alt-text="Cuadro de diálogo de solicitud de guardado de Postman con la colección Communication Services seleccionada.":::

## <a name="sending-a-request"></a>Envío de una solicitud

Ahora que todo está configurado, debería poder enviar la solicitud y recibir un mensaje SMS en el teléfono. Para ello, asegúrese de que está seleccionada la solicitud creada y, a continuación, pulse el botón "Send" (Enviar) de la derecha:

:::image type="content" source="media/postman/postman-send.png" alt-text="Solicitud de Postman con el botón Enviar resaltado.":::

Si todo ha ido bien, debería ver la respuesta de Communication Services, que debe ser el código de estado 202:

:::image type="content" source="media/postman/postman-202.png" alt-text="Solicitud de Postman enviada correctamente con el código de estado 202.":::

El teléfono móvil que posee el número que proporcionó en el valor de "to" (Para) también debe haber recibido un mensaje SMS. Ahora tiene una configuración funcional de Postman que puede hablar con Azure Communication Services y enviar mensajes SMS.


## <a name="next-steps"></a>Pasos siguientes

> [!div class="nextstepaction"]
> [Explorar las API de Azure Communication Services](/rest/api/communication/)
> [Más información sobre la autenticación](/rest/api/communication/authentication)
> [Más información sobre Postman](https://learning.postman.com/)

También puede que desee consultar:

- [Incorporación de chat a una aplicación](../quickstarts/chat/get-started.md)
- [Creación de tokens de acceso de usuario](../quickstarts/access-tokens.md)
- [Información sobre la arquitectura de cliente y servidor](../concepts/client-and-server-architecture.md)
- [Información sobre la autenticación](../concepts/authentication.md)