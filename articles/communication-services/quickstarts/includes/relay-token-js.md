---
title: Archivo de inclusión
description: archivo de inclusión
services: Communication Services
author: shahen
manager: anvalent
ms.service: Communication Services
ms.subservice: Communication Services
ms.date: 06/30/2021
ms.topic: include
ms.custom: include file
ms.author: shahen
ms.openlocfilehash: 0193f80952e7f9ea5878a99de0189eb0b5911b0d
ms.sourcegitcommit: 98308c4b775a049a4a035ccf60c8b163f86f04ca
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 06/30/2021
ms.locfileid: "114471452"
---
## <a name="setting-up"></a>Instalación

### <a name="create-a-new-nodejs-application"></a>Creación de una nueva aplicación Node.js

Abra la ventana de comandos o de terminal, cree un nuevo directorio para la aplicación y navegue hasta este.

```console
mkdir relay-token-quickstart && cd relay-token-quickstart
```
Ejecute `npm init -y` para crear un archivo **package.json** con la configuración predeterminada.

```console
npm init -y
```

### <a name="install-the-package"></a>Instalar el paquete

Use el comando `npm install` para instalar la biblioteca cliente de identidades de Azure Communication Services y la biblioteca de Network Traversal para JavaScript.

```console
npm install @azure/communication-identity --save
npm install @azure/communication-network-traversal --save
```

La opción `--save` muestra la biblioteca como una dependencia en el archivo **package.json**.

### <a name="set-up-the-app-framework"></a>Instalación del marco de la aplicación

Desde el directorio del proyecto:

1. Abra un nuevo archivo de texto en el editor de código.
2. Agregue las directivas `require` a la parte superior del archivo para usar el SDK de identidad de Azure Communication y el SDK de Network Traversal.
3. Cree la estructura del programa, incluido un control de excepciones básico.

   Use el código siguiente para empezar:

   ```javascript
   const { CommunicationIdentityClient } = require("@azure/communication-identity");
   const { CommunicationRelayClient } = require("@azure/communication-network-traversal");;

   const main = async () => {
     console.log("Azure Communication Services - Relay Token Quickstart")
  
     // Quickstart code goes here
   };

   main().catch((error) => {
     console.log("Encountered and error");
     console.log(error);
   })
   ```

4. Guarde el nuevo archivo como **relay-token.js** en el directorio *user-tokens-quickstart*.

### <a name="authenticate-the-client"></a>Autenticar el cliente

Cree una instancia de un objeto `CommunicationIdentityClient` con su cadena de conexión. El código siguiente recupera la cadena de conexión para el recurso de una variable de entorno denominada `COMMUNICATION_SERVICES_CONNECTION_STRING`. Aprenda a [administrar la cadena de conexión del recurso](../create-communication-resource.md#store-your-connection-string).

Agregue el código siguiente al método `main`:

```javascript
// This code demonstrates how to fetch your connection string
// from an environment variable.
const connectionString = process.env['COMMUNICATION_SERVICES_CONNECTION_STRING'];

// Instantiate the identity client
const identityClient = new CommunicationIdentityClient(connectionString);
```

## <a name="create-an-identity"></a>Creación de una identidad

Azure Communication Services mantiene un directorio de identidades ligero. Use el método `createUser` para crear una nueva entrada en el directorio con un `Id` único. Almacene la identidad recibida con la asignación a los usuarios de la aplicación. Por ejemplo, almacenándolos en la base de datos del servidor de aplicaciones. La identidad es necesaria más adelante para emitir tokens de acceso.

```javascript
let identityResponse = await identityClient.createUser();
console.log(`\nCreated an identity with ID: ${identityResponse.communicationUserId}`);
```

### <a name="exchange-the-user-access-token-for-a-relay-token"></a>Intercambio del token de acceso de usuario por un token de retransmisión

Llame al servicio de token de Azure Communication para intercambiar el token de acceso de usuario por un token de servicio TURN.

```javascript
    const relayClient = new CommunicationRelayClient(connectionString);
    console.log("Getting relay configuration");

    const config = await relayClient.getRelayConfiguration(identityResponse);
    console.log("RelayConfig", config);
```

### <a name="use-the-token-on-the-client-as-an-ice-candidate"></a>Uso del token en el cliente como candidato de ICE

El token ahora se puede deserializar y agregar como candidato de ICE con WebRTC en el explorador cliente.

```javascript  
var configuration = { iceServers: iceServers };
var peerConnection = new RTCPeerConnection(configuration);
```

## <a name="run-the-code"></a>Ejecución del código

Desde un símbolo del sistema de la consola, navegue hasta el directorio que contiene el archivo *issue-token.py* y, a continuación, ejecute el comando `node` siguiente para ejecutar la aplicación.

```console
node ./relay-token.js
```
