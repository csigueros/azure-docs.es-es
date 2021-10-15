---
title: Carga de archivos desde dispositivos a Azure IoT Hub con .NET | Microsoft Docs
description: Cómo cargar archivos de un dispositivo a la nube mediante el SDK de dispositivo IoT de Azure para. NET. Los archivos cargados se almacenan en un contenedor de blobs de Azure Storage.
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.devlang: csharp
ms.topic: conceptual
ms.date: 08/24/2021
ms.author: robinsh
ms.custom: mqtt, devx-track-csharp
ms.openlocfilehash: 91637d6f508eb198345add5ad40fed52a67ec952
ms.sourcegitcommit: 557ed4e74f0629b6d2a543e1228f65a3e01bf3ac
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/05/2021
ms.locfileid: "129457722"
---
# <a name="upload-files-from-your-device-to-the-cloud-with-iot-hub-net"></a>Carga de archivos de un dispositivo a la nube con IoT Hub (.NET)

[!INCLUDE [iot-hub-file-upload-language-selector](../../includes/iot-hub-file-upload-language-selector.md)]

En este tutorial se muestra cómo usar la característica de carga de archivos de IoT Hub con los SDK de servicio y dispositivo de Azure IoT .NET.

En el inicio rápido [Envío de telemetría de un dispositivo a IoT Hub](../iot-develop/quickstart-send-telemetry-iot-hub.md?pivots=programming-language-csharp) y el tutorial de [Envío de mensajes de la nube al dispositivo con IoT Hub](iot-hub-csharp-csharp-c2d.md) se muestra cómo usar la funcionalidad básica de mensajería del dispositivo a la nube y de la nube al dispositivo de IoT Hub. En el tutorial [Configuración del enrutamiento de mensajes con IoT Hub](tutorial-routing.md) se describe una forma de almacenar de manera confiable los mensajes enviados del dispositivo a la nube en Microsoft Azure Blob Storage. Sin embargo, en algunos casos no se pueden asignar fácilmente los datos que los dispositivos envían en los mensajes de dispositivo a nube con un tamaño relativamente reducido que acepta IoT Hub. Por ejemplo:

* Archivos grandes con imágenes

* Vídeos

* Datos de vibración muestreados con alta frecuencia

* Cierto tipo de datos procesados previamente

Dichos archivos se suelen procesar por lotes en la nube mediante herramientas como [Azure Data Factory](../data-factory/introduction.md) o la pila [Hadoop](../hdinsight/index.yml). Sin embargo, cuando necesite cargar archivos desde un dispositivo, todavía puede usar la seguridad y confiabilidad de IoT Hub. En este tutorial se muestra cómo realizar las siguientes acciones.

Al final de este tutorial, ejecutará dos aplicaciones de consola de .NET:

* **FileUploadSample**. Esta aplicación de dispositivo carga un archivo en el almacenamiento mediante un identificador URI de SAS proporcionado por el centro de IoT. Esta aplicación se ejecutará desde el repositorio de ejemplos de C# para Azure IoT que descargará en los requisitos previos.

* **ReadFileUploadNotification**. Esta aplicación de servicio recibe notificaciones de carga de archivos de IoT Hub. Usted creará esta aplicación.

> [!NOTE]
> IoT Hub admite muchas plataformas de dispositivos y lenguajes (entre los que se incluyen C, Java, Python y JavaScript), mediante los SDK de dispositivo IoT de Azure. Consulte el [Centro para desarrolladores de IoT de Azure](https://azure.microsoft.com/develop/iot) para obtener instrucciones paso a paso sobre cómo conectar el dispositivo a Azure IoT Hub.

[!INCLUDE [iot-hub-include-x509-ca-signed-file-upload-support-note](../../includes/iot-hub-include-x509-ca-signed-file-upload-support-note.md)]

## <a name="prerequisites"></a>Requisitos previos

* Una cuenta de Azure activa. En caso de no tener ninguna, puede crear una [cuenta gratuita](https://azure.microsoft.com/pricing/free-trial/) en tan solo unos minutos.

* Las aplicaciones de ejemplo que se ejecutan en este artículo se escriben con C#. En los ejemplos de C# de Azure IoT, se recomienda tener la SDK de .NET Core 3.1 o superior en la máquina de desarrollo.

    Puede descargar el SDK de .NET Core para varias plataformas desde [.NET](https://dotnet.microsoft.com/download).

    Puede verificar la versión actual de SDK de .NET Core en el equipo de desarrollo con el comando siguiente:

    ```cmd/sh
    dotnet --version
    ```

* Descargue los ejemplos en C# de Azure IoT de [https://github.com/Azure-Samples/azure-iot-samples-csharp/archive/main.zip](https://github.com/Azure-Samples/azure-iot-samples-csharp/archive/main.zip) y extraiga el archivo ZIP.

* Asegúrese de que el puerto 8883 está abierto en el firewall. En el ejemplo de este artículo se usa el protocolo MQTT, que se comunica mediante el puerto 8883. Este puerto puede estar bloqueado en algunos entornos de red corporativos y educativos. Para más información y para saber cómo solucionar este problema, consulte el artículo sobre la [conexión a IoT Hub (MQTT)](iot-hub-mqtt-support.md#connecting-to-iot-hub).

## <a name="create-an-iot-hub"></a>Crear un centro de IoT

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

## <a name="register-a-new-device-in-the-iot-hub"></a>Registro de un nuevo dispositivo en el centro de IoT

[!INCLUDE [iot-hub-include-create-device](../../includes/iot-hub-include-create-device.md)]

[!INCLUDE [iot-hub-associate-storage](../../includes/iot-hub-include-associate-storage.md)]

## <a name="upload-file-from-a-device-app"></a>Carga de un archivo desde una aplicación de dispositivo

En este artículo, usará un ejemplo del repositorio ejemplos de C# para Azure IoT que descargó anteriormente como aplicación de dispositivo. Puede abrir los archivos siguientes mediante Visual Studio, Visual Studio Code o un editor de texto de su elección.  

El ejemplo se encuentra en **azure-iot-samples-csharp-main\iot-hub\Samples\device\FileUploadSample** en la carpeta donde extrajo los ejemplos de C# para Azure IoT.

Examine el código de **FileUpLoadSample.cs**. Este archivo contiene la lógica de ejemplo principal. Después de crear el cliente de dispositivo de IoT Hub, sigue el procedimiento estándar de tres partes para cargar archivos desde un dispositivo:

1. El código llama al método **GetFileUploadSasUriAsync** en el cliente de dispositivo para obtener un URI de SAS del centro de IoT:

    ```csharp
    var fileUploadSasUriRequest = new FileUploadSasUriRequest
    {
        BlobName = fileName
    };

    // Lines removed for clarity

    FileUploadSasUriResponse sasUri = await _deviceClient.GetFileUploadSasUriAsync(fileUploadSasUriRequest);
    Uri uploadUri = sasUri.GetBlobUri();
    ```

1. El código usa el URI de SAS para cargar el archivo en Azure Storage. En este ejemplo, usa el URI de SAS para crear un cliente de blob en bloques de Azure Storage y carga el archivo:

    ```csharp
    var blockBlobClient = new BlockBlobClient(uploadUri);
    await blockBlobClient.UploadAsync(fileStreamSource, new BlobUploadOptions());
    ```

1. El código notifica al centro de IoT que ha completado la carga. Esto indica al centro de IoT que puede liberar recursos asociados a la carga (el URI de SAS). Si las notificaciones de carga de archivos están habilitadas, el centro de IoT enviará un mensaje de notificación a los servicios back-end.

    ```csharp
    var successfulFileUploadCompletionNotification = new FileUploadCompletionNotification
    {
        // Mandatory. Must be the same value as the correlation id returned in the sas uri response
        CorrelationId = sasUri.CorrelationId,
    
        // Mandatory. Will be present when service client receives this file upload notification
        IsSuccess = true,
    
        // Optional, user defined status code. Will be present when service client receives this file upload notification
        StatusCode = 200,
    
        // Optional, user-defined status description. Will be present when service client receives this file upload notification
        StatusDescription = "Success"
    };
    
    await _deviceClient.CompleteFileUploadAsync(successfulFileUploadCompletionNotification);
    ```

Si examina el archivo **parameter.cs**, verá lo siguiente:

- El ejemplo requiere que pase un parámetro, *p*, que toma una cadena de conexión de dispositivo. 

- De manera predeterminada, el ejemplo de dispositivo usa el protocolo MQTT para comunicarse con IoT Hub. Puede usar el parámetro *t* para cambiar este protocolo de transporte. Tenga en cuenta que, independientemente de esta selección, el cliente de blobs de Azure siempre usa HTTPS como protocolo para cargar el archivo Azure Storage.

## <a name="get-the-iot-hub-connection-string"></a>Obtención de la cadena de conexión de IoT Hub

En este artículo, crea un servicio de back-end para recibir mensajes de notificación de carga de archivos desde el centro de IoT. Para recibir mensajes de notificación de carga de archivos, el servicio necesita el permiso de **conexión de servicio**. De forma predeterminada, todas las instancias de IoT Hub se crean con una directiva de acceso compartido denominada **servicio** que concede este permiso.

[!INCLUDE [iot-hub-include-find-service-connection-string](../../includes/iot-hub-include-find-service-connection-string.md)]

## <a name="receive-a-file-upload-notification"></a>Recepción de una notificación de carga de archivos

En esta sección, se crea una aplicación de consola de C# que recibe mensajes de notificación de carga de archivos del centro de IoT.

1. Abra una ventana de comandos y vaya a la carpeta donde quiere crear el proyecto. Cree una carpeta denominada **ReadFileUploadNotifications** y cambie los directorios a esa carpeta.

    ```cmd/sh
    mkdir ReadFileUploadNotification
    cd ReadFileUploadNotification
    ```

1. Ejecute el siguiente comando para crear un proyecto de consola de C#. Después de ejecutar el comando, la carpeta contendrá un archivo **Program.cs** y un archivo **ReadFileUploadNotification.csproj**.

    ```cmd/sh
    dotnet new console --language c#
    ```

1. Ejecute el siguiente comando para agregar el paquete **Microsoft.Azure.Devices** al archivo del proyecto. Este paquete es el SDK del servicio .NET de Azure IoT.

    ```cmd/sh
    dotnet add package Microsoft.Azure.Devices
    ```

1. Abra el archivo **Program.cs** y agregue las siguientes instrucciones al principio del archivo:

    ```csharp
    using Microsoft.Azure.Devices;
    ```
1. Agregue los campos siguientes a la clase **Program** . Reemplace el valor de marcador de posición `{iot hub connection string}` por la cadena de conexión de IoT Hub que copió anteriormente en [Obtener la cadena de conexión de IoT Hub](#get-the-iot-hub-connection-string).

    ```csharp
    static ServiceClient serviceClient;
    static string connectionString = "{iot hub connection string}";
    ```

1. Agregue el método siguiente a la clase **Program** :

    ```csharp
    private async static void ReceiveFileUploadNotificationAsync()
    {
        var notificationReceiver = serviceClient.GetFileNotificationReceiver();
        Console.WriteLine("\nReceiving file upload notification from service");
        while (true)
        {
            var fileUploadNotification = await notificationReceiver.ReceiveAsync();
            if (fileUploadNotification == null) continue;
            Console.ForegroundColor = ConsoleColor.Yellow;
            Console.WriteLine("Received file upload notification: {0}", 
              string.Join(", ", fileUploadNotification.BlobName));
            Console.ResetColor();
            await notificationReceiver.CompleteAsync(fileUploadNotification);
        }
    }
    ```

    Tenga en cuenta que este patrón de recepción es el mismo que se usa para recibir mensajes de nube a dispositivo desde la aplicación de dispositivo.

1. Por último, reemplace las líneas en el método **Main** por las siguientes:

    ```csharp
    Console.WriteLine("Receive file upload notifications\n");
    serviceClient = ServiceClient.CreateFromConnectionString(connectionString);
    ReceiveFileUploadNotificationAsync();
    Console.WriteLine("Press Enter to exit\n");
    Console.ReadLine();
    ```
    
## <a name="run-the-applications"></a>Ejecución de las aplicaciones

Ahora está listo para ejecutar las aplicaciones.

1. En primer lugar, ejecute la aplicación de servicio para que reciba notificaciones de carga de archivos desde el centro de IoT. En el símbolo del sistema, en la carpeta **ReadFileUploadNotification**, ejecute los comandos siguientes: 

    ```cmd/sh
    dotnet restore
    dotnet run
    ```
    
    La aplicación se inicia y espera una notificación de carga de archivos desde el centro de IoT:

    ```cmd/sh
    Receive file upload notifications


    Receiving file upload notification from service
    Press Enter to exit
    ```



1. A continuación, ejecute la aplicación de dispositivo para cargar el archivo en Azure Storage. Abra un nuevo símbolo del sistema y cambie las carpetas a **azure-iot-samples-csharp-main\iot-hub\Samples\device\FileUploadSample** en la carpeta donde expandió los ejemplos de C# para Azure IoT. Ejecute los comandos siguientes: Reemplace el valor del marcador de posición `{Your device connection string}` en el segundo comando por la cadena de conexión de dispositivo que copió anteriormente en [Registro de un nuevo dispositivo en el centro de IoT](#register-a-new-device-in-the-iot-hub).

    ```cmd/sh
    dotnet restore
    dotnet run --p "{Your device connection string}"
    ```
    
    La siguiente salida corresponde a la aplicación de dispositivo una vez completada la carga:
    
    ```cmd/sh
      Uploading file TestPayload.txt
      Getting SAS URI from IoT Hub to use when uploading the file...
      Successfully got SAS URI (https://contosostorage.blob.core.windows.net/contosocontainer/MyDevice%2FTestPayload.txt?sv=2018-03-28&sr=b&sig=x0G1Baf%2BAjR%2BTg3nW34zDNKs07p6dLzkxvZ3ZSmjIhw%3D&se=2021-05-04T16%3A40%3A52Z&sp=rw) from IoT Hub
      Uploading file TestPayload.txt using the Azure Storage SDK and the retrieved SAS URI for authentication
      Successfully uploaded the file to Azure Storage
      Notified IoT Hub that the file upload succeeded and that the SAS URI can be freed.
      Time to upload file: 00:00:01.5077954.
      Done.
    ```
    
1. Observe que la aplicación de servicio muestra que ha recibido la notificación de carga de archivos:

    ```cmd/sh
    Receive file upload notifications
    
    
    Receiving file upload notification from service
    Press Enter to exit
    
    Received file upload notification: myDeviceId/TestPayload.txt
    ```

## <a name="verify-the-file-upload"></a>Comprobación de la carga de archivos

Puede usar el portal para ver el archivo cargado en el contenedor de almacenamiento que configuró:

1. Vaya a la cuenta de almacenamiento en Azure Portal.
1. En el panel izquierdo de la cuenta de almacenamiento, seleccione **Contenedores**.
1. Seleccione el contenedor donde cargó el archivo.
1. Seleccione la carpeta que nombró tras el dispositivo.
1. Seleccione el blob donde cargó el archivo. En este artículo, es el blob denominado **TestPayload.txt**.  

    :::image type="content" source="./media/iot-hub-csharp-csharp-file-upload/view-uploaded-file.png" alt-text="Captura de pantalla de la selección del archivo cargado en Azure Portal.":::

1. Vea las propiedades del blob en la página que se abre. Puede seleccionar **Descargar** para descargar el archivo y ver su contenido localmente.

## <a name="next-steps"></a>Pasos siguientes

En este tutorial ha aprendido a usar la característica de carga de archivos de IoT Hub para simplificar la carga de archivos desde los dispositivos. Puede seguir explorando esta característica con los siguientes artículos:

* [Introducción a las cargas de archivos con IoT Hub](iot-hub-devguide-file-upload.md)

* [Configuración de cargas de archivos de IoT Hub](iot-hub-configure-file-upload.md)

* [Documentación de Azure Blob Storage](../storage/blobs/storage-blobs-introduction.md)

* [Referencia de la API de Azure Blob Storage](../storage/blobs/reference.md)

* [SDK de IoT de Azure](iot-hub-devguide-sdks.md)