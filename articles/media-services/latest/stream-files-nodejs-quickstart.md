---
title: Codificación y transmisión de archivos de vídeo con Node.js
description: Obtenga información sobre cómo transmitir archivos de vídeo con Node.js. Siga los pasos de este tutorial para crear una cuenta de Azure Media Services, codificar un archivo y transmitirlo a Azure Media Player.
services: media-services
author: IngridAtMicrosoft
manager: femila
editor: ''
keywords: azure media services, stream, Node.js
ms.service: media-services
ms.workload: media
ms.topic: tutorial
ms.date: 02/17/2021
ms.author: inhenkel
ms.openlocfilehash: f8eafe88168f908025321d1e3ce6304fa34c03ab
ms.sourcegitcommit: c434baa76153142256d17c3c51f04d902e29a92e
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/10/2021
ms.locfileid: "132179935"
---
# <a name="how-to-encode-and-stream-video-files-with-nodejs"></a>Codificación y transmisión de archivos de vídeo con Node.js

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

Este inicio rápido muestra lo fácil que es codificar e iniciar el streaming de vídeos en una amplia variedad de navegadores y dispositivos con Azure Media Services. Para especificar cualquier archivo de vídeo de entrada se pueden usar direcciones URL HTTPS, direcciones URL SAS o rutas de acceso a archivos ubicados en Azure Blob Storage.

Al finalizar este inicio rápido, sabrá:

- Cómo codificar con Node.js
- Cómo hacer streaming con Node.js
- Cómo cargar un archivo desde una dirección URL HTTPS con Node.js
- Cómo usar un reproductor de cliente HLS o DASH con Node.js

En el ejemplo de este artículo se codifica contenido que se hace accesible a través de una dirección URL HTTPS. Tenga en cuenta que, actualmente, AMS v3 no admite la codificación de transferencia fragmentada a través de direcciones URL de HTTPS.

![Reproducción del vídeo](./media/stream-files-nodejs-quickstart/final-video.png)

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Prerrequisitos

- Instalar [Node.js](https://nodejs.org/en/download/)
- [Cree una cuenta de Media Services](./create-account-howto.md).<br/>Asegúrese de recordar los valores que usó para el nombre de la cuenta de Media Services y el nombre del grupo de recursos.
- Siga los pasos de [Acceso a la API de Azure Media Services con la CLI de Azure](./access-api-howto.md) y guarde las credenciales. Deberá usarlas para acceder a la API.
- Siga los pasos que se proporcionan en [Configuración y conexión con Node.js](./configure-connect-nodejs-howto.md) para aprender a usar el SDK del cliente de Node.js.

## <a name="download-and-configure-the-sample"></a>Descarga y configuración del ejemplo

Clone un repositorio GitHub que contenga el ejemplo de Node.js de streaming en la máquina con el siguiente comando:  

 ```bash
 git clone https://github.com/Azure-Samples/media-services-v3-node-tutorials.git
 ```

El ejemplo se encuentra en la carpeta [StreamFilesSample](https://github.com/Azure-Samples/media-services-v3-node-tutorials/tree/master/AMSv3Samples/StreamFilesSample).

Abra [index.ts](https://github.com/Azure-Samples/media-services-v3-node-tutorials/blob/master/AMSv3Samples/StreamFilesSample/index.ts) en el proyecto descargado. Actualice el archivo *sample.env* en la carpeta raíz con los valores y las credenciales que obtuvo al [acceder a las API](./access-api-howto.md). Cambie el nombre del archivo *sample.env* a *.env* (sí, solo la extensión).

Este ejemplo realiza las acciones siguientes:

1. Crea una **transformación** con un [valor preestablecido para la codificación según el contenido](./encode-content-aware-concept.md). En primer lugar, comprueba si existe la transformación especificada.
1. Crea un **recurso** de salida que utiliza el **trabajo** de codificación para contener el resultado.
1. Opcionalmente, carga un archivo local mediante el SDK de Blob Storage.
1. Crea la entrada del **trabajo**, que es una dirección URL HTTPS o un archivo cargado.
1. Envía el **trabajo** de codificación, para lo que usa la entrada y salida que se crearon previamente.
1. Comprueba el estado del trabajo.
1. Descarga la salida del trabajo de codificación en una carpeta local.
1. Crea un **localizador de streaming** para usarlo en el reproductor
1. Genera direcciones URL de streaming para HLS y DASH
1. Reproduce el contenido en una aplicación de reproductor: Azure Media Player

## <a name="run-the-sample"></a>Ejecución del ejemplo

1. La aplicación descarga los archivos codificados. Cree la carpeta a la que desea que vayan los archivos de salida ir y actualice el valor de la variable **outputFolder** en el archivo [index.ts](https://github.com/Azure-Samples/media-services-v3-node-tutorials/blob/main/AMSv3Samples/StreamFilesSample/index.ts#L59). De forma predeterminada se establece en "Temp".
1. Abra un **símbolo del sistema** y desplácese al directorio del ejemplo.
1. Cambie el directorio a la carpeta AMSv3Samples.

    ```bash
    cd AMSv3Samples
    ```

1. Instale los paquetes que se usan en el archivo *packages.json*.

    ```bash
    npm install 
    ```

1. Cambie el directorio a la carpeta *StreamFilesSample*.

    ```bash
    cd StreamFilesSample
    ```

1. Inicie Visual Studio Code desde la carpeta *AMSv3Samples* (es necesario para iniciar desde la carpeta en la que se encuentran la carpeta *.vscode* y los archivos *tsconfig.json*).

    ```bash
    cd ..
    code .
    ```

Abra la carpeta *StreamFilesSample* y el archivo *index.ts* en el editor de Visual Studio Code.
En el archivo *index.ts*, presione F5 para iniciar el depurador.

## <a name="test-with-azure-media-player"></a>Prueba con Azure Media Player

Use Azure Media Player para probar la secuencia. También puede usar cualquier reproductor compatible con HLS o DASH, como Shaka Player, HLS.js, Dash.js u otros.

Debería poder hacer clic en el vínculo generado en el ejemplo e iniciar el reproductor AMP con el manifiesto de DASH cargado.

> [!NOTE]
> Si el reproductor está hospedado en un sitio https, asegúrese de actualizar la dirección URL a "https".

1. Abra un explorador web y vaya a [https://aka.ms/azuremediaplayer/](https://aka.ms/azuremediaplayer/).
2. En el cuadro **Dirección URL:** , pegue uno de los valores de dirección URL de streaming que recibió al ejecutar la aplicación. Puede pegar la dirección URL en formato HLS, Dash o Smooth y Azure Media Player cambiará a un protocolo de streaming adecuado para su reproducción automática en el dispositivo.
3. Presione **Actualizar Player**.

Azure Media Player puede usarse para realizar pruebas, pero no debe usarse en un entorno de producción.

## <a name="clean-up-resources"></a>Limpieza de recursos

Si ya no necesita ninguno de los recursos del grupo de recursos, como las cuentas de almacenamiento y de Media Services que creó en este tutorial, elimine el grupo de recursos.

Ejecute el siguiente comando de la CLI:

```azurecli
az group delete --name amsResourceGroup
```

## <a name="more-developer-documentation-for-nodejs-on-azure"></a>Más documentación para desarrolladores sobre Node.js en Azure

- [Azure para desarrolladores de JavaScript y Node.js](/azure/developer/javascript/)
- [Código fuente de Media Services en el @azure/azure-sdk-for-js repositorio de GitHub](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/mediaservices/arm-mediaservices)
- [Documentación del paquete de Azure para desarrolladores de Node.js](/javascript/api/overview/azure/)

## <a name="see-also"></a>Consulte también

- [Códigos de error de trabajo](/rest/api/media/jobs/get#joberrorcode).
- [Instalación de npm@azure/arm-mediaservices](https://www.npmjs.com/package/@azure/arm-mediaservices)
- [Azure para desarrolladores de JavaScript y Node.js](/azure/developer/javascript/)
- [Código fuente de Media Services en el @azure/azure-sdk-for-js repositorio](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/mediaservices/arm-mediaservices)

## <a name="next-steps"></a>Pasos siguientes

> [Conceptos de Azure Media Services](concepts-overview.md)
