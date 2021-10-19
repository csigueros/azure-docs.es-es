---
title: 'Procedimiento: uso de la automatización de pruebas con Azure Fluid Relay'
description: Uso de las bibliotecas de automatización de pruebas para crear pruebas automatizadas para aplicaciones Fluid
services: azure-fluid
author: hickeys
ms.author: hickeys
ms.date: 10/05/2021
ms.topic: article
ms.service: azure-fluid
fluid.url: https://fluidframework.com/docs/testing/testing/
ms.openlocfilehash: 0087be9dccc1b040720ea53f5e6aae6d64f6fa17
ms.sourcegitcommit: e82ce0be68dabf98aa33052afb12f205a203d12d
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/07/2021
ms.locfileid: "129661701"
---
# <a name="how-to-use-test-automation-with-azure-fluid-relay"></a>Procedimiento: uso de la automatización de pruebas con Azure Fluid Relay

> [!NOTE]
> Esta versión preliminar se ofrece sin Acuerdo de Nivel de Servicio y no se recomienda para cargas de trabajo de producción. Es posible que algunas características no sean compatibles o que tengan sus funcionalidades limitadas.

Las pruebas y la automatización resultan fundamentales para mantener la calidad y duración del código. Internamente, Fluid usa una variedad de pruebas unitarias y de integración con tecnología de [Mocha](https://mochajs.org/), [Jest](https://jestjs.io/), [Puppeteer](https://github.com/puppeteer/puppeteer) y [Webpack](https://webpack.js.org/).

Puede ejecutar pruebas mediante **@fluidframework/azure-local-service** local o con un inquilino de prueba en el servicio Azure Fluid Relay. **AzureClient** se puede configurar para que se conecte tanto a un servicio remoto como a un servicio local, lo que le permite utilizar un tipo de cliente único entre pruebas en instancias de servicio activas y locales. La única diferencia es la configuración que se utiliza para crear el cliente.

## <a name="automation-against-azure-fluid-relay"></a>Automatización en Azure Fluid Relay

La automatización puede conectarse a un inquilino de prueba para Azure Fluid Relay de la misma manera que a un inquilino de producción, y solo necesita la configuración de conexión adecuada. Para más información, consulte el artículo sobre [cómo conectarse a un servicio Azure Fluid Relay](connect-fluid-azure-service.md).

## <a name="creating-an-adaptable-test-client"></a>Creación de un cliente de prueba adaptable

A fin de crear un cliente de prueba adaptable, debe configurar AzureClient de manera distinta según el destino del servicio. La función que se muestra a continuación usa una variable de entorno para determinarlo. Puede establecer la variable de entorno en un script de prueba para controlar cuál es el destino del servicio.

```typescript
function createAzureClient(): AzureClient {
    const useAzure = process.env.FLUID_CLIENT === "azure";
    const tenantKey = useAzure ? process.env.FLUID_TENANTKEY as string : "";
    const user = { id: "userId", name: "Test User" };

    const connectionConfig = useAzure ? {
        tenantId: "myTenantId",
        tokenProvider: new InsecureTokenProvider(tenantKey, user),
        orderer: "https://myOrdererUrl",
        storage: "https://myStorageUrl",
    } : {
        tenantId: LOCAL_MODE_TENANT_ID,
        tokenProvider: new InsecureTokenProvider("", user),
        orderer: "http://localhost:7070",
        storage: "http://localhost:7070",
    };

    const clientProps = {
        connection: config,
    };

    return new AzureClient(clientProps);
}
```

Las pruebas pueden llamar a esta función para crear un objeto AzureClient sin preocuparse por el servicio subyacente. La prueba [Mocha](https://mochajs.org/) que se indica a continuación crea el cliente de servicio antes de ejecutar las pruebas y, luego, lo usa para ejecutar cada prueba. Hay una sola prueba que usa el cliente del servicio para crear un contenedor que se realizará correctamente siempre y cuando no se produzca ningún error.

```typescript
describe("ClientTest", () => {
    const client = createAzureClient();
    let documentId: string;

    it("can create Azure container successfully", async () => {
        const schema: ContainerSchema = {
            initialObjects: {
                customMap: SharedMap
            },
        };
        documentId = await container.attach();
        const { container, services } = await azureClient.createContainer(schema);
    });
});

```

## <a name="running-tests"></a>Ejecución de las pruebas

Puede agregar los scripts npm siguientes en el archivo package.json del proyecto para ejecutar pruebas:

```json
"scripts": {
    "start:local": "npx @fluidframework/azure-local-service@latest > local-service.log 2>&1",
    "test:mocha": "mocha",
    "test:azure": "cross-env process.env.FLUID_CLIENT='\"azure\"' && npm run test:mocha",
    "test:local": "start-server-and-test start:local 7070 test:mocha"
}
```

Si desea instalar las dependencias que requieren los scripts mencionados, puede usar el comando siguiente:

```bash
npm install cross-env start-server-and-test mocha
```

El script `test:local` usa la biblioteca [start-server-and-test](https://www.npmjs.com/package/start-server-and-test) para iniciar el servidor local, esperar hasta que el puerto 7070 (el puerto predeterminado que utiliza el servidor local) responda, ejecutar el script de prueba y, luego, finalizar el servidor local.
