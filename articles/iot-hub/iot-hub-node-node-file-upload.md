---
title: Carga de archivos desde dispositivos a Azure IoT Hub con Node | Microsoft Docs
description: Cómo cargar archivos de un dispositivo a la nube mediante el SDK de dispositivo IoT de Azure para Node.js. Los archivos cargados se almacenan en un contenedor de blobs de Azure Storage.
author: wesmc7777
ms.author: wesmc
ms.service: iot-hub
services: iot-hub
ms.devlang: nodejs
ms.topic: conceptual
ms.date: 07/27/2021
ms.custom: mqtt, devx-track-js
ms.openlocfilehash: a95737cd9d15b0ee21249f0db8d1bbb96fb8a3b2
ms.sourcegitcommit: 860f6821bff59caefc71b50810949ceed1431510
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/09/2021
ms.locfileid: "129710093"
---
# <a name="upload-files-from-your-device-to-the-cloud-with-iot-hub-nodejs"></a>Carga de archivos de un dispositivo a la nube con IoT Hub (Node.js)

[!INCLUDE [iot-hub-file-upload-language-selector](../../includes/iot-hub-file-upload-language-selector.md)]

En este tutorial se muestra cómo realizar las siguientes acciones:

* Proporcionar un dispositivo de forma segura con un identificador URI de blob de Azure para cargar un archivo.

* Usar las notificaciones de carga de archivo de IoT Hub para desencadenar el procesamiento del archivo en el back-end de aplicación.

En el inicio rápido sobre el [Envío de telemetría desde un dispositivo a un centro de IoT](../iot-develop/quickstart-send-telemetry-iot-hub.md?pivots=programming-language-nodejs) se explica la funcionalidad básica de mensajería del dispositivo a la nube de IoT Hub. Sin embargo, en algunos casos no se pueden asignar fácilmente los datos que los dispositivos envían en los mensajes de dispositivo a nube con un tamaño relativamente reducido que acepta IoT Hub. Por ejemplo:

* Archivos grandes con imágenes
* Vídeos
* Datos de vibración muestreados con alta frecuencia
* Alguna forma de datos procesados previamente.

Dichos archivos se suelen procesar por lotes en la nube mediante herramientas como [Azure Data Factory](../data-factory/introduction.md) o la pila [Hadoop](../hdinsight/index.yml). Cuando necesite cargar archivos desde un dispositivo, todavía puede usar la seguridad y confiabilidad de IoT Hub.

Al final de este artículo, ejecutará dos aplicaciones de consola de Node.js:

* **FileUpload.js**, que carga un archivo en el almacenamiento mediante un URI de SAS proporcionado por un centro de IoT.

* **FileUploadNotification.js**, que recibe notificaciones de carga de archivos del centro de IoT.

> [!NOTE]
> IoT Hub admite muchas plataformas de dispositivos y lenguajes (entre los que se incluyen C, Java, Python y JavaScript), mediante los SDK de dispositivo IoT de Azure. Consulte el [Centro para desarrolladores de IoT de Azure](https://azure.microsoft.com/develop/iot) para obtener instrucciones paso a paso sobre cómo conectar el dispositivo a Azure IoT Hub.

[!INCLUDE [iot-hub-include-x509-ca-signed-file-upload-support-note](../../includes/iot-hub-include-x509-ca-signed-file-upload-support-note.md)]

## <a name="prerequisites"></a>Requisitos previos

* Node.js versión 10.0.x o posteriores. Se recomienda la versión LTS. Puede descargar Node.js de [nodejs.org](https://nodejs.org).

* Una cuenta de Azure activa. (En caso de no tener ninguna, puede crear una [cuenta gratuita](https://azure.microsoft.com/pricing/free-trial/) en tan solo unos minutos).

* Asegúrese de que el puerto 8883 está abierto en el firewall. En el ejemplo de dispositivo de este artículo se usa el protocolo MQTT, que se comunica mediante el puerto 8883. Este puerto puede estar bloqueado en algunos entornos de red corporativos y educativos. Para más información y para saber cómo solucionar este problema, consulte el artículo sobre la [conexión a IoT Hub (MQTT)](iot-hub-mqtt-support.md#connecting-to-iot-hub).

## <a name="create-an-iot-hub"></a>Crear un centro de IoT

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

## <a name="register-a-new-device-in-the-iot-hub"></a>Registro de un nuevo dispositivo en el centro de IoT

[!INCLUDE [iot-hub-include-create-device](../../includes/iot-hub-include-create-device.md)]

[!INCLUDE [iot-hub-associate-storage](../../includes/iot-hub-include-associate-storage.md)]

## <a name="upload-a-file-from-a-device-app"></a>Carga de un archivo desde una aplicación de dispositivo

En esta sección, creará una aplicación para dispositivos para cargar un archivo en un centro de IoT. El código se basa en el código disponible en el ejemplo [upload_to_blob_advanced.js](https://github.com/Azure/azure-iot-sdk-node/blob/master/device/samples/javascript/upload_to_blob_advanced.js) en los ejemplos de dispositivos del [SDK de Azure IoT node.js](https://github.com/Azure/azure-iot-sdk-node).

1. Cree una carpeta vacía denominada `fileupload`.  En la carpeta `fileupload`, cree un archivo package.json con el siguiente comando en el símbolo del sistema.  Acepte todos los valores predeterminados:

    ```cmd/sh
    npm init
    ```

1. En el símbolo del sistema, en la carpeta `fileupload`, ejecute el siguiente comando para instalar el SDK de dispositivo **azure-iot-device** y los paquetes **azure-iot-device-mqtt** y **@azure/storage-blob** :

    ```cmd/sh
    npm install azure-iot-device azure-iot-device-mqtt @azure/storage-blob --save
    ```

1. Con un editor de texto, cree un archivo **FileUpload.js** en la carpeta `fileupload` y copie en ella el código siguiente.

    ```javascript
    'use strict';

    const Client = require('azure-iot-device').Client;
    const Protocol = require('azure-iot-device-mqtt').Mqtt;
    const errors = require('azure-iot-common').errors;
    const path = require('path');

    const {
      AnonymousCredential,
      BlockBlobClient,
      newPipeline
    } = require('@azure/storage-blob');

    // make sure you set these environment variables prior to running the sample.
    const deviceConnectionString = process.env.DEVICE_CONNECTION_STRING;
    const localFilePath = process.env.PATH_TO_FILE;
    const storageBlobName = path.basename(localFilePath);

    async function uploadToBlob(localFilePath, client) {
      const blobInfo = await client.getBlobSharedAccessSignature(storageBlobName);
      if (!blobInfo) {
        throw new errors.ArgumentError('Invalid upload parameters');
      }

      const pipeline = newPipeline(new AnonymousCredential(), {
        retryOptions: { maxTries: 4 },
        telemetry: { value: 'HighLevelSample V1.0.0' }, // Customized telemetry string
        keepAliveOptions: { enable: false }
      });

      // Construct the blob URL to construct the blob client for file uploads
      const { hostName, containerName, blobName, sasToken } = blobInfo;
      const blobUrl = `https://${hostName}/${containerName}/${blobName}${sasToken}`;

      // Create the BlockBlobClient for file upload to the Blob Storage Blob
      const blobClient = new BlockBlobClient(blobUrl, pipeline);

      // Setup blank status notification arguments to be filled in on success/failure
      let isSuccess;
      let statusCode;
      let statusDescription;

      try {
        const uploadStatus = await blobClient.uploadFile(localFilePath);
        console.log('uploadStreamToBlockBlob success');

        // Save successful status notification arguments
        isSuccess = true;
        statusCode = uploadStatus._response.status;
        statusDescription = uploadStatus._response.bodyAsText;

        // Notify IoT Hub of upload to blob status (success)
        console.log('notifyBlobUploadStatus success');
      }
      catch (err) {
        isSuccess = false;
        statusCode = err.code;
        statusDescription = err.message;

        console.log('notifyBlobUploadStatus failed');
        console.log(err);
      }

      await client.notifyBlobUploadStatus(blobInfo.correlationId, isSuccess, statusCode, statusDescription);
    }

    // Create a client device from the connection string and upload the local file to blob storage.
    const deviceClient = Client.fromConnectionString(deviceConnectionString, Protocol);
    uploadToBlob(localFilePath, deviceClient)
      .catch((err) => {
        console.log(err);
      })
      .finally(() => {
        process.exit();
      });
    ```

1. Guarde y cierre el archivo **FileUpload.js**.

1. Copie un archivo de imagen en la carpeta `fileupload` y asígnele un nombre, por ejemplo, `myimage.png`.

1. Agregue variables de entorno para la cadena de conexión del dispositivo y la ruta de acceso al archivo que quiere cargar. Obtuvo la cadena de conexión del dispositivo cuando [registró el dispositivo en el centro de IoT](#register-a-new-device-in-the-iot-hub).
    
    - Para Windows:

        ```cmd
        set DEVICE_CONNECTION_STRING={your device connection string}
        set PATH_TO_FILE={your image filepath}
        ```

    - Para Linux/Bash:

        ```bash
        export DEVICE_CONNECTION_STRING="{your device connection string}"
        export PATH_TO_FILE="{your image filepath}"
        ```

## <a name="get-the-iot-hub-connection-string"></a>Obtención de la cadena de conexión de IoT Hub

En este artículo, creará un servicio de back-end para recibir mensajes de notificación de carga de archivos desde el centro de IoT que ha creado. Para recibir mensajes de notificación de carga de archivos, el servicio necesita el permiso de **conexión de servicio**. De forma predeterminada, todas las instancias de IoT Hub se crean con una directiva de acceso compartido denominada **servicio** que concede este permiso.

[!INCLUDE [iot-hub-include-find-service-connection-string](../../includes/iot-hub-include-find-service-connection-string.md)]

## <a name="receive-a-file-upload-notification"></a>Recepción de una notificación de carga de archivos

En esta sección, se crea una aplicación de consola de Node.js que recibe mensajes de notificación de carga de archivos de IoT Hub.

1. Cree una carpeta vacía denominada `fileuploadnotification`.  En la carpeta `fileuploadnotification`, cree un archivo package.json con el siguiente comando en el símbolo del sistema.  Acepte todos los valores predeterminados:

    ```cmd/sh
    npm init
    ```

1. En el símbolo del sistema, en la carpeta `fileuploadnotification`, ejecute el siguiente comando para instalar el paquete del SDK **azure-iothub**:

    ```cmd/sh
    npm install azure-iothub --save
    ```

1. Con un editor de texto, cree un archivo **FileUploadNotification.js** en la carpeta `fileuploadnotification`.

1. Agregue las siguientes instrucciones `require` al principio del archivo **FileUploadNotification.js**:

    ```javascript
    'use strict';

    const Client = require('azure-iothub').Client;
    ```

1. Lea la cadena de conexión del centro de IoT desde el entorno:

    ```javascript
    const connectionString = process.env.IOT_HUB_CONNECTION_STRING;
    ```

1. Agregue el código siguiente para crear un cliente de servicio a partir de la cadena de conexión:

    ```javascript
    const serviceClient = Client.fromConnectionString(connectionString);
    ```

1. Abra el cliente y use la función **getFileNotificationReceiver** para recibir las actualizaciones de estado.

    ```javascript
    serviceClient.open(function (err) {
      if (err) {
        console.error('Could not connect: ' + err.message);
      } else {
        console.log('Service client connected');
        serviceClient.getFileNotificationReceiver(function receiveFileUploadNotification(err, receiver){
          if (err) {
            console.error('error getting the file notification receiver: ' + err.toString());
          } else {
            receiver.on('message', function (msg) {
              console.log('File upload from device:')
              console.log(msg.getData().toString('utf-8'));
            });
          }
        });
      }
    });
    ```

1. Guarde y cierre el archivo **FileUploadNotification.js**.

1. Agregue una variable de entorno para la cadena de conexión de IoT Hub. Ha copiado esta cadena anteriormente en [Obtención de la cadena de conexión de IoT Hub](#get-the-iot-hub-connection-string).
    
    - Para Windows:

        ```cmd
        set IOT_HUB_CONNECTION_STRING={your iot hub connection string}
        ```

    - Para Linux/Bash:

        ```bash
        export IOT_HUB_CONNECTION_STRING="{your iot hub connection string}"
        ```

## <a name="run-the-applications"></a>Ejecución de las aplicaciones

Ahora está listo para ejecutar las aplicaciones.

En la carpeta `fileuploadnotification` del símbolo del sistema, ejecute el siguiente comando:

```cmd/sh
node FileUploadNotification.js
```

En la carpeta `fileupload` del símbolo del sistema, ejecute el siguiente comando:

```cmd/sh
node FileUpload.js
```

La siguiente salida corresponde a la aplicación **FileUpload** una vez completada la carga:

```output
uploadStreamToBlockBlob success
notifyBlobUploadStatus success
```

La siguiente salida de ejemplo corresponde a la aplicación **FileUploadNotification** una vez completada la carga:

```output
Service client connected
File upload from device:
{"deviceId":"myDeviceId","blobUri":"https://{your storage account name}.blob.core.windows.net/device-upload-container/myDeviceId/image.png","blobName":"myDeviceId/image.png","lastUpdatedTime":"2021-07-23T23:27:06+00:00","blobSizeInBytes":26214,"enqueuedTimeUtc":"2021-07-23T23:27:07.2580791Z"}
```

## <a name="verify-the-file-upload"></a>Comprobación de la carga de archivos

Puede usar el portal para ver el archivo cargado en el contenedor de almacenamiento que configuró:

1. Vaya a la cuenta de almacenamiento en Azure Portal.
1. En el panel izquierdo de la cuenta de almacenamiento, seleccione **Contenedores**.
1. Seleccione el contenedor donde cargó el archivo.
1. Seleccione la carpeta que nombró tras el dispositivo.
1. Seleccione el blob donde cargó el archivo. En este artículo, es el blob con el mismo nombre que el archivo.  

    :::image type="content" source="./media/iot-hub-node-node-file-upload/view-uploaded-file.png" alt-text="Captura de pantalla de la visualización del archivo cargado en Azure Portal.":::

1. Vea las propiedades del blob en la página que se abre. Puede seleccionar **Descargar** para descargar el archivo y ver su contenido localmente.

## <a name="next-steps"></a>Pasos siguientes

En este tutorial ha aprendido a usar la funcionalidad de carga de archivos de IoT Hub para simplificar la carga de archivos desde los dispositivos. Puede continuar explorando las características y los escenarios del centro de IoT con los siguientes artículos:

* [Creación de un centro de IoT mediante programación](iot-hub-rm-template-powershell.md)

* [Introducción a SDK para C](iot-hub-device-sdk-c-intro.md)

* [SDK de IoT de Azure](iot-hub-devguide-sdks.md)
