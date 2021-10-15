---
author: probableprime
ms.service: azure-communication-services
ms.topic: include
ms.date: 09/08/2021
ms.author: rifox
ms.openlocfilehash: a671eb00f2108b96220e72a32f8dd9f5654a4224
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/13/2021
ms.locfileid: "128699415"
---
## <a name="install-the-sdk"></a>Instalación del SDK

Use el comando `npm install` para instalar los SDK comunes y de llamada de Azure Communication Services para JavaScript.

```console
npm install @azure/communication-common --save
npm install @azure/communication-calling --save
```

## <a name="initialize-required-objects"></a>Inicialización de los objetos necesarios

Se requiere una instancia de CallClient para la mayoría de las operaciones de llamada. Vamos a crear una instancia de `CallClient`. Se puede configurar con opciones personalizadas como una instancia del registrador.

Cuando haya creado una instancia de `CallClient`, puede crear una de `CallAgent` mediante una llamada al método `createCallAgent` en la instancia de `CallClient`. Este método devuelve un objeto de instancia `CallAgent` de manera asincrónica.

El método `createCallAgent` utiliza `CommunicationTokenCredential` como argumento. Acepta un [token de acceso de usuario](../../../../quickstarts/access-tokens.md).

Puede usar el método `getDeviceManager` en la instancia de `CallClient` para acceder a `deviceManager`.

```js
const { CallClient } = require('@azure/communication-calling');
const { AzureCommunicationTokenCredential} = require('@azure/communication-common');
const { AzureLogger, setLogLevel } = require("@azure/logger");

// Set the logger's log level
setLogLevel('verbose');

// Redirect log output to wherever desired. To console, file, buffer, REST API, etc...
AzureLogger.log = (...args) => {
    console.log(...args); // Redirect log output to console
};

const userToken = '<USER_TOKEN>';
callClient = new CallClient(options);
const tokenCredential = new AzureCommunicationTokenCredential(userToken);
const callAgent = await callClient.createCallAgent(tokenCredential, {displayName: 'optional ACS user name'});
const deviceManager = await callClient.getDeviceManager()
```