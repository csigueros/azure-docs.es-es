---
title: Carga de archivos desde dispositivos a Azure IoT Hub con .NET | Microsoft Docs
description: Cómo cargar archivos de un dispositivo a la nube mediante el SDK de dispositivo IoT de Azure para. NET. Los archivos cargados se almacenan en un contenedor de blobs de Azure Storage.
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.devlang: csharp
ms.topic: conceptual
ms.date: 07/18/2021
ms.author: robinsh
ms.custom: mqtt, devx-track-csharp
ms.openlocfilehash: 41cf392fb4b50c06e6af1f20e0c53570589b4090
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/13/2021
ms.locfileid: "121780469"
---
# <a name="upload-files-from-your-device-to-the-cloud-with-iot-hub-net"></a>Carga de archivos de un dispositivo a la nube con IoT Hub (.NET)

[!INCLUDE [iot-hub-file-upload-language-selector](../../includes/iot-hub-file-upload-language-selector.md)]

Este tutorial le muestra el uso de las funcionalidades de carga de archivos de IoT Hub mediante el ejemplo de carga de archivos de .NET. 

En el inicio rápido [Envío de telemetría de un dispositivo a IoT Hub](../iot-develop/quickstart-send-telemetry-iot-hub.md?pivots=programming-language-csharp) y el tutorial de [Envío de mensajes de la nube al dispositivo con IoT Hub](iot-hub-csharp-csharp-c2d.md) se muestra cómo usar la funcionalidad básica de mensajería del dispositivo a la nube y de la nube al dispositivo de IoT Hub. En el tutorial [Configuración del enrutamiento de mensajes con IoT Hub](tutorial-routing.md) se describe una forma de almacenar de manera confiable los mensajes enviados del dispositivo a la nube en Microsoft Azure Blob Storage. Sin embargo, en algunos casos no se pueden asignar fácilmente los datos que los dispositivos envían en los mensajes de dispositivo a nube con un tamaño relativamente reducido que acepta IoT Hub. Por ejemplo:

* Archivos grandes con imágenes

* Vídeos

* Datos de vibración muestreados con alta frecuencia

* Cierto tipo de datos procesados previamente

Dichos archivos se suelen procesar por lotes en la nube mediante herramientas como [Azure Data Factory](../data-factory/introduction.md) o la pila [Hadoop](../hdinsight/index.yml). Sin embargo, cuando necesite cargar archivos desde un dispositivo, todavía puede usar la seguridad y confiabilidad de IoT Hub. En este tutorial se muestra cómo realizar las siguientes acciones.

> [!NOTE]
> IoT Hub admite muchas plataformas de dispositivos y lenguajes (entre los que se incluyen C, Java, Python y JavaScript), mediante los SDK de dispositivo IoT de Azure. Consulte el [Centro para desarrolladores de IoT de Azure](https://azure.microsoft.com/develop/iot) para obtener instrucciones paso a paso sobre cómo conectar el dispositivo a Azure IoT Hub.

[!INCLUDE [iot-hub-include-x509-ca-signed-file-upload-support-note](../../includes/iot-hub-include-x509-ca-signed-file-upload-support-note.md)]

## <a name="prerequisites"></a>Requisitos previos

* Visual Studio Code

* Una cuenta de Azure activa. En caso de no tener ninguna, puede crear una [cuenta gratuita](https://azure.microsoft.com/pricing/free-trial/) en tan solo unos minutos.

* Las aplicaciones de ejemplo que se ejecutan en este artículo se escriben con C#. En los ejemplos de C# de Azure IoT, se recomienda tener la SDK de .NET Core 3.1 o superior en la máquina de desarrollo.

    Puede descargar el SDK de .NET Core para varias plataformas desde [.NET](https://dotnet.microsoft.com/download).

    Puede verificar la versión actual de SDK de .NET Core en el equipo de desarrollo con el comando siguiente:

    ```cmd/sh
    dotnet --version
    ```

* Descargue los ejemplos en C# de Azure IoT de [https://github.com/Azure-Samples/azure-iot-samples-csharp/archive/master.zip](https://github.com/Azure-Samples/azure-iot-samples-csharp/archive/master.zip) y extraiga el archivo ZIP.

* Asegúrese de que el puerto 8883 está abierto en el firewall. En el ejemplo de este artículo se usa el protocolo MQTT, que se comunica mediante el puerto 8883. Este puerto puede estar bloqueado en algunos entornos de red corporativos y educativos. Para más información y para saber cómo solucionar este problema, consulte el artículo sobre la [conexión a IoT Hub (MQTT)](iot-hub-mqtt-support.md#connecting-to-iot-hub).

## <a name="create-an-iot-hub"></a>Crear un centro de IoT

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

## <a name="register-a-new-device-in-the-iot-hub"></a>Registro de un nuevo dispositivo en el centro de IoT

[!INCLUDE [iot-hub-include-create-device](../../includes/iot-hub-include-create-device.md)]

[!INCLUDE [iot-hub-associate-storage](../../includes/iot-hub-include-associate-storage.md)]

## <a name="examine-the-application"></a>Examen de la aplicación

En Visual Studio Code, abra la carpeta *azure-iot-samples-csharp-master\iot-hub\Samples\device\FileUploadSample* en la descarga de ejemplos de .NET. La carpeta contiene un archivo denominado *parameters.cs*. Si abre ese archivo, verá que el parámetro *p* es necesario y contiene la cadena de conexión del dispositivo. Ha copiado y guardado esta cadena de conexión al registrar el dispositivo. El parámetro *t* se puede especificar si desea cambiar el protocolo de transporte. El protocolo predeterminado es mqtt. El archivo *program.cs* contiene la función *main*. El archivo *FileUploadSample.cs* contiene la lógica de ejemplo principal. *TestPayload.txt* es el archivo que se va a cargar en el contenedor de blobs.

## <a name="run-the-application"></a>Ejecución de la aplicación

Ahora está preparado para ejecutar la aplicación.

1. En Visual Studio Code, abra una ventana de terminal.
1. Escriba los comandos siguientes:
    ```cmd/sh
    dotnet restore
    dotnet run --p "{Your device connection string}"
    ```

La salida debe ser similar a la siguiente:

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

## <a name="verify-the-file-upload"></a>Comprobación de la carga de archivos

Realice los pasos siguientes para comprobar que *TestPayload.txt* se ha cargado en el contenedor:

1. En el panel izquierdo de la cuenta de almacenamiento, en **Almacenamiento de datos**, seleccione **Contenedores**.
1. Seleccione el contenedor en el que ha cargado *TestPayload.txt*.
1. Seleccione la carpeta que nombró tras el dispositivo.
1. Seleccione *TestPayload.txt*.
1. Descargue el archivo para ver su contenido localmente.

## <a name="next-steps"></a>Pasos siguientes

En este tutorial ha aprendido a usar la funcionalidad de carga de archivos de IoT Hub para simplificar la carga de archivos desde los dispositivos. Consulte los siguientes artículos para seguir explorando las características y los escenarios de IoT Hub:

* [Creación de un centro de IoT mediante programación](iot-hub-rm-template-powershell.md)

* [Introducción a SDK para C](iot-hub-device-sdk-c-intro.md)

* [SDK de IoT de Azure](iot-hub-devguide-sdks.md)

Para explorar aún más las funcionalidades de IoT Hub, consulte:

* [Implementación de IA en dispositivos perimetrales con Azure IoT Edge](../iot-edge/quickstart-linux.md)