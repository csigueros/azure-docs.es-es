---
title: 'Conexión a la API de Azure Media Services v3: Node.js'
description: En este artículo se muestra cómo conectarse a Media Services v3 API con Node.js.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 02/17/2021
ms.author: inhenkel
ms.custom: devx-track-js
ms.openlocfilehash: 7c6bba2084247d76a79de9ae9efb1b3ea356a5c4
ms.sourcegitcommit: 40866facf800a09574f97cc486b5f64fced67eb2
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/30/2021
ms.locfileid: "123221490"
---
# <a name="connect-to-media-services-v3-api---nodejs"></a>Conexión a la API de Media Services v3: Node.js

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

En este artículo se muestra cómo conectarse al SDK de Azure Media Services v3 con node.js mediante el método de inicio de sesión de la entidad de servicio. Trabajará con archivos del repositorio de ejemplos *media-services-v3-node-tutorials*. El ejemplo *HelloWorld-ListAssets* contiene el código para conectar con los recursos de la cuenta y después enumerarlos.

## <a name="prerequisites"></a>Requisitos previos

- Instalación de Visual Studio Code.
- [Instale Node.js](https://nodejs.org/en/download/).
- [Instale TypeScript](https://www.typescriptlang.org/download).
- [Cree una cuenta de Media Services](./account-create-how-to.md). Asegúrese de recordar el nombre del grupo de recursos y el nombre de la cuenta de Media Services.
- Cree a una entidad de servicio para la aplicación. Consulte [Acceso a la API](./access-api-howto.md).<br/>**Sugerencia PRO**: mantenga esta ventana abierta o copie todo el contenido de la pestaña JSON en el Bloc de notas. 
- Asegúrese de obtener la versión más reciente del [SDK de Azure Media Services para JavaScript](https://www.npmjs.com/package/@azure/arm-mediaservices).

> [!IMPORTANT]
> Revise las [convenciones de nomenclatura](media-services-apis-overview.md#naming-conventions) de Azure Media Services para comprender las importantes restricciones de nomenclatura asociadas a las entidades.

## <a name="clone-the-nodejs-samples-repo"></a>Clonación del repositorio de ejemplos de Node.JS

Trabajará con algunos archivos de ejemplos de Azure. Clone el repositorio de ejemplos de Node.JS.

```git
git clone https://github.com/Azure-Samples/media-services-v3-node-tutorials.git
```

## <a name="install-the-nodejs-packages"></a>Instalación de paquetes de Node.js

### <a name="install-azurearm-mediaservices"></a>Instalar @azure/arm-mediaservices

```bash
npm install @azure/arm-mediaservices
```

### <a name="install-azurems-rest-nodeauth"></a>Instalar @azure/ms-rest-nodeauth

Instale la versión mínima de "@azure/ms-rest-nodeauth": "^ 3.0.0".

```bash
npm install @azure/ms-rest-nodeauth@"^3.0.0"
```

En este ejemplo, utilizará los siguientes paquetes del archivo `package.json`.

|Paquete|Descripción|
|---|---|
|`@azure/arm-mediaservices`|SDK de Azure Media Services. <br/>Para asegurarse de que usa el paquete más reciente de Azure Media Services, consulte [npm install @azure/arm-mediaservices](https://www.npmjs.com/package/@azure/arm-mediaservices).|
|`@azure/ms-rest-nodeauth` | Se requiere para la autenticación de AAD mediante una entidad de servicio o una identidad administrada.|
|`@azure/storage-blob`|SDK de Storage. Se utiliza cuando se cargan los archivos en los recursos.|
|`@azure/ms-rest-js`| Se usa para iniciar sesión.|
|`@azure/storage-blob` | Se usa para cargar y descargar archivos en los recursos de Azure Media Services para la codificación.|
|`@azure/abort-controller`| Se usa junto con el cliente de almacenamiento para agotar el tiempo de espera de las operaciones de descarga de larga duración.|

### <a name="create-the-packagejson-file"></a>Creación del archivo package.json

1. Cree un archivo `package.json` con el editor que prefiera.
1. Abra el archivo y pegue el siguiente código:

```json
{
  "name": "media-services-node-sample",
  "version": "0.1.0",
  "description": "",
  "main": "./index.ts",
  "dependencies": {
    "@azure/arm-mediaservices": "^8.0.0",
    "@azure/abort-controller": "^1.0.2",
    "@azure/ms-rest-nodeauth": "^3.0.6",
    "@azure/storage-blob": "^12.4.0",
  }
}
```

## <a name="connect-to-nodejs-client-using-typescript"></a>Conexión con el cliente de Node.js mediante TypeScript

### <a name="sample-env-file"></a>Archivo *.env* de ejemplo

Copie el contenido de este archivo en un archivo denominado *.env*. Debe almacenarse en la raíz del repositorio de trabajo. Estos son los valores que se obtuvieron de la página Acceso de API correspondiente a la cuenta de Media Services en el portal.

Para acceder a los valores necesarios para escribir en el archivo *.env*, se recomienda primero leer y revisar el artículo de procedimiento de [Acceso a la API](./access-api-howto.md).
Puede usar Azure Portal o la CLI para obtener los valores necesarios para escribir en el archivo de variables de entorno de este ejemplo.

Después de crear el archivo *.env*, puede empezar a trabajar con los ejemplos.

```nodejs
# Values from the API Access page in the portal
AZURE_CLIENT_ID=""
AZURE_CLIENT_SECRET= ""
AZURE_TENANT_ID= ""

# Change this to match your AAD Tenant domain name. 
AAD_TENANT_DOMAIN = "microsoft.onmicrosoft.com"

# Set this to your Media Services Account name, resource group it is contained in, and location
AZURE_MEDIA_ACCOUNT_NAME = ""
AZURE_LOCATION= ""
AZURE_RESOURCE_GROUP= ""

# Set this to your Azure Subscription ID
AZURE_SUBSCRIPTION_ID= ""

# You must change these if you are using Gov Cloud, China, or other non standard cloud regions
AZURE_ARM_AUDIENCE= "https://management.core.windows.net"
AZURE_ARM_ENDPOINT="https://management.azure.com"

# DRM Testing
DRM_SYMMETRIC_KEY="add random base 64 encoded string here"
```

## <a name="run-the-sample-application-helloworld-listassets"></a>Ejecución de la aplicación de ejemplo *HelloWorld-ListAssets*

1. Cambie el directorio a la carpeta *AMSv3Samples*.

```bash
cd AMSv3Samples
```

2. Instale los paquetes que se usan en el archivo *package.json*.

```
npm install 
```

3. Cambie el directorio a la carpeta *HelloWorld-ListAssets*.

```bash
cd HelloWorld-ListAssets
```

4. Inicie Visual Studio Code desde la carpeta AMSv3Samples (es necesario iniciarlo desde la carpeta donde se encuentran la carpeta ".vscode" y los archivos tsconfig.json).

```dotnetcli
cd ..
code .
```

Acceda a la carpeta *HelloWorld-ListAssets* y abra el archivo *index.ts* en el editor de Visual Studio Code.

En el archivo *index.ts*, presione F5 para iniciar el depurador. Si ya tiene recursos en la cuenta, debería ver una lista con ellos. Si la cuenta está vacía, verá una lista vacía.  

Para ver rápidamente los recursos enumerados, use el portal para cargar algunos archivos de vídeo. Se crearán automáticamente los recursos para cada uno de ellos. Si vuelve a ejecutar el script, se devolverán sus nombres.

### <a name="a-closer-look-at-the-helloworld-listassets-sample"></a>Una mirada más detallada al ejemplo *HelloWorld-ListAssets*

El ejemplo *HelloWorld-ListAssets* muestra cómo conectarse al cliente de Media Services con una entidad de servicio y enumerar los recursos de la cuenta. Consulte en los comentarios del código una explicación detallada de lo que hace.

```ts
import * as msRestNodeAuth from "@azure/ms-rest-nodeauth";
import { AzureMediaServices } from '@azure/arm-mediaservices';
import { AzureMediaServicesOptions } from "@azure/arm-mediaservices/esm/models";
// Load the .env file if it exists
import * as dotenv from "dotenv";
dotenv.config();

export async function main() {
  // Copy the samples.env file and rename it to .env first, then populate it's values with the values obtained 
  // from your Media Services account's API Access page in the Azure portal.
  const clientId = process.env.AZURE_CLIENT_ID as string;
  const secret = process.env.AZURE_CLIENT_SECRET as string;
  const tenantDomain = process.env.AAD_TENANT_DOMAIN as string;
  const subscriptionId = process.env.AZURE_SUBSCRIPTION_ID as string;
  const resourceGroup = process.env.AZURE_RESOURCE_GROUP as string;
  const accountName = process.env.AZURE_MEDIA_ACCOUNT_NAME as string;

  let clientOptions: AzureMediaServicesOptions = {
    longRunningOperationRetryTimeout: 5, // set the time out for retries to 5 seconds
    noRetryPolicy: false // use the default retry policy.
  }

  const creds = await msRestNodeAuth.loginWithServicePrincipalSecret(clientId, secret, tenantDomain);
  const mediaClient = new AzureMediaServices(creds, subscriptionId, clientOptions);

  // List Assets in Account
  console.log("Listing Assets Names in account:")
  var assets = await mediaClient.assets.list(resourceGroup, accountName);

  assets.forEach(asset => {
    console.log(asset.name);
  });

  if (assets.odatanextLink) {
    console.log("There are more than 1000 assets in this account, use the assets.listNext() method to continue listing more assets if needed")
    console.log("For example:  assets = await mediaClient.assets.listNext(assets.odatanextLink)");
  }
}

main().catch((err) => {
  console.error("Error running sample:", err.message);
});
```

## <a name="more-samples"></a>Más ejemplos

En el [repositorio](https://github.com/Azure-Samples/media-services-v3-node-tutorials) puede encontrar los siguientes ejemplos:

|Nombre de proyecto|Caso de uso|
|---|---|
|Live/index.ts| Ejemplo básico de streaming en vivo. **ADVERTENCIA**: asegúrese de comprobar que todos los recursos se han limpiado y ya no se facturan en el portal al usar en producción.|
|StreamFilesSample/index.ts| Ejemplo básico para cargar un archivo local o codificación de una dirección URL de origen. En el ejemplo se muestra cómo usar el SDK de Storage para descargar contenido y se muestra cómo transmitir a un reproductor. |
|StreamFilesWithDRMSample/index.ts| Muestra cómo codificar y transmitir mediante DRM de Widevine y PlayReady. |
|VideoIndexerSample/index.ts| Ejemplo de uso de los valores preestablecidos del analizador de vídeo y audio para generar metadatos e información a partir de un archivo de vídeo o audio. |

## <a name="see-also"></a>Consulte también

- [Documentación de referencia para módulos de Azure Media Services para Node.js](/javascript/api/overview/azure/media-services)
- [Azure para desarrolladores de JavaScript y Node.js](/azure/developer/javascript/)
- [Código fuente de Media Services en el @azure/azure-sdk-for-js repositorio de GitHub](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/mediaservices/arm-mediaservices)
- [Documentación del paquete de Azure para desarrolladores de Node.js](/javascript/api/overview/azure/)
- [Conceptos de Azure Media Services](concepts-overview.md)
- [npm install @azure/arm-mediaservices](https://www.npmjs.com/package/@azure/arm-mediaservices)
- [Azure para desarrolladores de JavaScript y Node.js](/azure/developer/javascript/)
- [Código fuente de Media Services en el @azure/azure-sdk-for-js repositorio](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/mediaservices/arm-mediaservices)

## <a name="next-steps"></a>Pasos siguientes

Explore la documentación de [referencia de Node.js](/javascript/api/overview/azure/mediaservices/management) y los [ejemplos](https://github.com/Azure-Samples/media-services-v3-node-tutorials) que muestran cómo usar Media Services API con node.js.
