---
title: 'Inicio rápido: aplicación que tira dados'
description: Creación rápida de una aplicación que tira dados mediante el servicio Azure Fluid Relay
author: hickeys
ms.service: azure-fluid
ms.topic: quickstart
ms.date: 09/09/2021
ms.author: hickeys
ms.openlocfilehash: f3a25a47cc1bee143f562e936f7927f48d452b4c
ms.sourcegitcommit: e82ce0be68dabf98aa33052afb12f205a203d12d
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/07/2021
ms.locfileid: "129662165"
---
# <a name="quickstart-dice-roller"></a>Inicio rápido: aplicación que tira dados

> [!NOTE]
> Esta versión preliminar se ofrece sin Acuerdo de Nivel de Servicio y no se recomienda para cargas de trabajo de producción. Es posible que algunas características no sean compatibles o que tengan sus funcionalidades limitadas.

En este inicio rápido, se le guiará por el proceso de creación de una aplicación que tira dados y que utiliza el servicio Azure Fluid Relay. El inicio rápido se divide en dos partes. En la primera parte, crearemos la propia aplicación y la ejecutaremos en un servidor de Fluid local. En la segunda parte, volveremos a configurar la aplicación para que se ejecute en el servicio Azure Fluid Relay en lugar de en el servidor de desarrollo local.

Se puede encontrar un código de ejemplo de este inicio rápido [aquí](https://github.com/microsoft/FluidHelloWorld/tree/main-azure).

## <a name="set-up-your-development-environment"></a>Configurado su entorno de desarrollo

Para seguir con este inicio rápido, necesitará una cuenta de Azure que tenga [aprovisionado el servicio Azure Fluid Relay](../how-tos/provision-fluid-azure-portal.md). Si no tiene una cuenta, puede [probar Azure gratis](https://azure.com/free).

También necesita tener el siguiente software instalado en el equipo.

- Un editor de código: se recomienda utilizar [Visual Studio Code](https://code.visualstudio.com/).
- [Git](https://git-scm.com/downloads)
- [Node.js](https://nodejs.org/en/download), versión 12.17 o posterior

## <a name="getting-started-locally"></a>Introducción a un servicio local

En primer lugar, deberá descargar la aplicación de ejemplo de GitHub. Abra una nueva ventana de comandos, vaya a la carpeta en la que desee descargar el código y utilice Git para clonar el [repositorio FluidHelloWorld](https://github.com/microsoft/FluidHelloWorld). El proceso de clonación creará una subcarpeta denominada FluidHelloWorld con los archivos de proyecto que contiene.

```cli
git clone -b main-azure https://github.com/microsoft/FluidHelloWorld.git
```

Vaya a la carpeta recién creada, instale las dependencias e inicie la aplicación.

```cli
cd FluidHelloWorld
npm install
...
npm start
```


Al ejecutar el comando `npm start`, sucederán dos cosas. En primer lugar, se iniciará un servidor de Fluid en un proceso local. Este servidor está pensado solo para el desarrollo. Se actualizará a un servidor de producción hospedado en Azure posteriormente. En segundo lugar, se abrirá una nueva pestaña del explorador en una página que contiene una nueva instancia de la aplicación que tira dados. Puede abrir nuevas pestañas con la misma dirección URL para crear instancias adicionales de la aplicación que tira dados. Cada instancia de la aplicación está configurada de forma predeterminada para utilizar el servicio Fluid local. Haga clic en el botón **Roll** (Tirar) en cualquier instancia de la aplicación y observe que el estado de los dados cambia en cada cliente.

## <a name="upgrading-to-azure-fluid-relay"></a>Actualización a Azure Fluid Relay

Para una ejecución en el servicio Azure Fluid Relay, deberá actualizar la configuración de la aplicación para conectarse al servicio de Azure en lugar de al servidor local.

### <a name="configure-and-create-an-azure-client"></a>Configuración y creación de un cliente de Azure

Para configurar el cliente de Azure, reemplace los valores del objeto `serviceConfig` en `app.js` por los valores de configuración del servicio Azure Fluid Relay. Estos valores se pueden encontrar en la sección "Clave de acceso" del recurso Fluid Relay en Azure Portal.

```javascript
const serviceConfig = {
    connection: {
        tenantId: LOCAL_MODE_TENANT_ID, // REPLACE WITH YOUR TENANT ID
        tokenProvider: new InsecureTokenProvider("" /* REPLACE WITH YOUR PRIMARY KEY */, { id: "userId" }),
        orderer: "http://localhost:7070", // REPLACE WITH YOUR ORDERER ENDPOINT
        storage: "http://localhost:7070", // REPLACE WITH YOUR STORAGE ENDPOINT
    }
};
```

> [!WARNING]
> Durante el desarrollo, puede utilizar `InsecureTokenProvider` para generar y firmar tokens de autenticación que el servicio Azure Fluid Relay aceptará. Sin embargo, como su nombre indica, esto no es seguro y no se debe utilizar en entornos de producción. El proceso de creación de recursos de Azure Fluid Relay proporciona una clave secreta que se puede utilizar para firmar solicitudes seguras. **Para asegurarse de que este secreto no se exponga, debe reemplazarse por otra implementación de ITokenProvider que capture el token de un servicio back-end seguro y proporcionado por el desarrollador antes de publicarlo en producción.**

### <a name="build-and-run-the-client-only"></a>Compilación y ejecución del cliente únicamente

Ahora que ha apuntado a la aplicación para que utilice Azure en lugar de un servidor local, no es necesario iniciar el servidor de Fluid local junto con la aplicación cliente. Puede iniciar el cliente sin iniciar también el servidor con este comando. 

```bash
npm run start:client
```

🥳**Enhorabuena**🎉 Ya ha dado el primer paso en el buen camino para abrir el mundo de la colaboración con Fluid.
