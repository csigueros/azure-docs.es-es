---
title: Servicio de confianza para JavaScript
description: Esta es la versión para JavaScript de la creación de un Servicio de confianza para Communication Services.
author: dademath
manager: nimag
services: azure-communication-services
ms.author: ddematheu2
ms.date: 06/30/2021
ms.topic: include
ms.service: azure-communication-services
ms.openlocfilehash: 1c399f05467fe23504b9f204a50484620fcec86e
ms.sourcegitcommit: 98308c4b775a049a4a035ccf60c8b163f86f04ca
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 06/30/2021
ms.locfileid: "113107306"
---
## <a name="download-code"></a>Descarga de código

Busque el código finalizado de este inicio rápido en [GitHub](https://github.com/Azure-Samples/communication-services-javascript-quickstarts/tree/main/trusted-authentication-service)

## <a name="prerequisites"></a>Requisitos previos

- Una cuenta de Azure con una suscripción activa. Para más información, consulte [Creación de una cuenta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- [Visual Studio Code](https://code.visualstudio.com/) en una de las [plataformas admitidas](https://code.visualstudio.com/docs/supporting/requirements#_platforms).
- Node.js [, las versiones Active LTS](https://nodejs.org/) y Maintenance LTS (se recomienda la 10.14.1). Use el comando `node --version` para comprobar la versión.
- La [extensión de Azure Functions](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurefunctions) para Visual Studio Code.
- Un recurso activo de Communication Services y una cadena de conexión. [Cree un recurso de Communication Services](../../quickstarts/create-communication-resource.md).

## <a name="overview"></a>Información general

:::image type="content" source="../media/trusted-service-architecture.png" alt-text="Diagrama de arquitectura del servicio de confianza":::

En este tutorial, vamos a crear una instancia de Azure Functions que servirá como servicio de aprovisionamiento de tokens de confianza. Puede usar este tutorial para arrancar su propio servicio de aprovisionamiento de tokens.

Este servicio es responsable de autenticar a los usuarios en Azure Communication Services. Los usuarios de las aplicaciones de Communication Services requerirán un `Access Token` para poder participar en hilos de chat y llamadas VoIP. La instancia de Azure Functions funcionará como intermediario de confianza entre el usuario y Communication Services. Esto le permite aprovisionar tokens de acceso sin exponer la cadena de conexión de recursos a los usuarios.

Para obtener más información, consulte la [arquitectura cliente-servidor](../../concepts/client-and-server-architecture.md) y la documentación conceptual de [autenticación y autorización](../../concepts/authentication.md).

## <a name="setting-up"></a>Instalación

### <a name="azure-functions-set-up"></a>Configuración de Azure Functions

Vamos a configurar primero la estructura básica de la instancia de Azure Functions. Aquí encontrará instrucciones detalladas sobre la configuración: [Creación de una función mediante Visual Studio Code](../../../azure-functions/create-first-function-vs-code-csharp.md?pivots=programming-language-javascript)

La instancia de Azure Functions requiere la siguiente configuración:

- Language: JavaScript
- Plantilla: Desencadenador HTTP
- Nivel de autorización: Anónimo (se puede cambiar más adelante si prefiere otro modelo de autorización)
- Nombre de la función: Definido por el usuario

Después de seguir las [instrucciones de Azure Functions](../../../azure-functions/create-first-function-vs-code-csharp.md?pivots=programming-language-javascript) con la configuración anterior, debe tener un proyecto en Visual Studio Code para la instancia de Azure Functions con un archivo `index.js` que contenga la propia función. El código dentro de este archivo debe ser el siguiente:

```javascript

module.exports = async function (context, req) {
    context.log('JavaScript HTTP trigger function processed a request.');

    const name = (req.query.name || (req.body && req.body.name));
    const responseMessage = name
        ? "Hello, " + name + ". This HTTP triggered function executed successfully."
        : "This HTTP triggered function executed successfully. Pass a name in the query string or in the request body for a personalized response.";

    context.res = {
        // status: 200, /* Defaults to 200 */
        body: responseMessage
    };
}

```

Ahora vamos a instalar las bibliotecas de Azure Communication Services.

### <a name="install-communication-services-libraries"></a>Instalación de las bibliotecas de Communication Services

Usaremos la biblioteca `Identity` para generar `User Access Tokens`.

Use el comando `npm install` para instalar Identity SDK de Azure Communication Services para JavaScript.

```console

npm install @azure/communication-identity --save

```

La opción `--save` muestra la biblioteca como dependencia en el archivo **package.json**.

En la parte superior del archivo `index.js`, importe la interfaz para `CommunicationIdentityClient`.

```javascript
const { CommunicationIdentityClient } = require('@azure/communication-identity');
```

## <a name="access-token-generation"></a>Generación del token de acceso

Para permitir que la instancia de Azure Functions genere `User Access Tokens`, primero necesitaremos usar la cadena de conexión para el recurso de Communication Services.

Para obtener más información sobre cómo recuperar la cadena de conexión, visite la [guía de inicio rápido de aprovisionamiento de recursos](../../quickstarts/create-communication-resource.md).

``` javascript
const connectionString = 'INSERT YOUR RESOURCE CONNECTION STRING'
```

A continuación, modificaremos la función original para generar `User Access Tokens`.

`User Access Tokens` se generan al crear un usuario a partir del método `createUser`. Una vez creado el usuario, se puede usar el método `getToken` para generar un token para ese usuario que devuelve la instancia de Azure Functions.

En este ejemplo, configuraremos el ámbito del token para `voip`. Puede que se necesiten otros ámbitos para su aplicación. Obtenga más información sobre los [ámbitos](../../quickstarts/access-tokens.md).

```javascript
module.exports = async function (context, req) {
    let tokenClient = new CommunicationIdentityClient(connectionString);

    const user = await tokenClient.createUser();

    const userToken = await tokenClient.getToken(user, ["voip"]);

    context.res = {
        body: userToken
    };
}
```

En el caso del `CommunicationUser` existente de Communication Services, puede omitir el paso de creación y simplemente generar un token de acceso. Puede encontrar más detalles en la [guía de inicio rápido de creación de tokens de acceso de usuario](../../quickstarts/access-tokens.md).

## <a name="test-the-azure-function"></a>Prueba de la instancia de Azure Functions

Ejecute la instancia de Azure Functions localmente con `F5`. Esto inicializará la instancia de Azure Functions localmente y hará que sea accesible a través de `http://localhost:7071/api/FUNCTION_NAME`. Consulte la documentación adicional sobre la [ejecución local](../../../azure-functions/create-first-function-vs-code-csharp.md?pivots=programming-language-javascript#run-the-function-locally).

Abra la dirección URL en el explorador; verá un cuerpo de respuesta con el identificador de usuario de la comunicación, el token y la expiración del token.

:::image type="content" source="../media/trusted-service-sample-response.png" alt-text="Captura de pantalla que muestra un ejemplo de respuesta para la instancia de Azure Functions creada.":::

## <a name="deploy-the-function-to-azure"></a>Implementación de la función en Azure

Para implementar la instancia de Azure Functions, puede seguir las [instrucciones paso a paso](../../../azure-functions/create-first-function-vs-code-csharp.md?pivots=programming-language-javascript#sign-in-to-azure).

En resumen, tendrá que:
1. Iniciar sesión en Azure desde Visual Studio.
2. Publicar el proyecto en la cuenta de Azure. Aquí tendrá que elegir una suscripción existente.
3. Crear un nuevo recurso de Azure Functions con el Asistente de Visual Studio o usar un recurso existente. En el caso de un nuevo recurso, deberá configurarlo para la región, el tiempo de ejecución y el identificador único que desee.
4. Espere a que la implementación finalice.
5. Ejecución de la función 🎉

## <a name="run-azure-function"></a>Ejecución de la instancia de Azure Functions

Ejecute la instancia de Azure Functions con la URL `http://<function-appn-ame>.azurewebsites.net/api/<function-name>`.

Para encontrar la dirección URL, puede hacer clic con el botón derecho en la función en Visual Studio Code y copiar la dirección URL de la función.

Más información sobre la [ejecución la instancia de Azure Functions](../../../azure-functions/create-first-function-vs-code-csharp.md?pivots=programming-language-javascript#run-the-function-in-azure)
