---
title: Información sobre la carga de archivos de Azure IoT Hub | Microsoft Docs
description: 'Guía del desarrollador: uso de la característica de carga de archivos de IoT Hub para administrar la carga de archivos desde un dispositivo a un contenedor de blobs de Azure Storage.'
author: robinsh
ms.author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 10/25/2021
ms.custom:
- mqtt
- 'Role: Cloud Development'
- 'Role: IoT Device'
ms.openlocfilehash: 991a55da8a4359192e499e464f034a8742055c99
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/02/2021
ms.locfileid: "131005335"
---
# <a name="upload-files-with-iot-hub"></a>Carga de archivos con IoT Hub

Hay muchos escenarios en los que no se pueden asignar fácilmente los datos de los dispositivos en los mensajes de dispositivo a nube, relativamente pequeños, que IoT Hub acepta. Por ejemplo, cuando se envían archivos multimedia o lotes de telemetría de gran tamaño que han cargado dispositivos conectados de forma intermitente o que se han agregado y comprimido para ahorrar ancho de banda.

Cuando necesite cargar estos archivos desde un dispositivo, puede seguir utilizando la seguridad y confiabilidad de IoT Hub. En lugar de actuar como agente de mensajería, IoT Hub envía los mensajes a una cuenta de Azure Storage asociada. IoT Hub puede proporcionar también notificaciones a los servicios de back-end cuando un dispositivo termina de cargar archivos.

Si necesita ayuda para decidir cuándo usar propiedades notificadas, mensajes de dispositivo a la nube o cargas de archivos, consulte la [Guía de comunicación de dispositivo a nube](iot-hub-devguide-d2c-guidance.md).

[!INCLUDE [iot-hub-include-x509-ca-signed-file-upload-support-note](../../includes/iot-hub-include-x509-ca-signed-file-upload-support-note.md)]

## <a name="file-upload-overview"></a>Introducción a la carga de archivos

Un centro de IoT facilita la carga de archivos desde dispositivos conectados proporcionándoles un URI con firma de acceso compartido (SAS) por cada carga para un contenedor de blobs y una cuenta de Azure Storage que se hayan preconfigurado con el centro. El uso de cargas de archivos con IoT Hub comprende tres partes: la preconfiguración de una cuenta de almacenamiento de Azure y un contenedor de blobs en ioT Hub, la carga de archivos desde los dispositivos y, opcionalmente, la notificación a los servicios de back-end de que se han completado las cargas de archivos.

Para poder usar la característica de carga de archivos, debe asociar una [cuenta de Azure Storage](/azure/storage/common/storage-account-overview) y un [contenedor de blobs](/azure/storage/blobs/storage-blobs-introduction) al centro de IoT. También puede configurar opciones para controlar la autenticación de IoT Hub en Azure Storage, el período de vida (TTL) de los URI con SAS que el centro de IoT entrega a los dispositivos y las notificaciones de carga de archivos a los servicios de back-end. Para obtener más información, vea [Asociación de una cuenta de Azure Storage a IoT Hub](#associate-an-azure-storage-account-with-iot-hub).

Los dispositivos siguen un proceso de tres pasos para cargar un archivo en el contenedor de blobs asociado:

1. El dispositivo inicia la carga de archivos con IoT Hub. Pasa el nombre de un blob en la solicitud y, a cambio, obtiene un URI con SAS y un identificador de correlación. El URI con SAS contiene un token de SAS para Azure Storage que concede al dispositivo permiso de lectura y escritura en el blob solicitado del contenedor de blobs. Para obtener más información, vea [Dispositivo: inicialización de una carga de archivos](#device-initialize-a-file-upload).

1. El dispositivo utiliza el URI con SAS para llamar de forma segura a las API de Azure Blob Storage para cargar el archivo en el contenedor de blobs. Para obtener más información, vea [Dispositivo: carga de archivos con las API de Azure Storage](#device-upload-file-using-azure-storage-apis).

1. Una vez completada la carga de archivos, el dispositivo se lo notifica al centro de IoT usando el identificador de correlación que recibió de IoT Hub cuando inició la carga. Para obtener más información, vea [Dispositivo: notificación a IoT Hub de que se ha completado una carga de archivos](#device-notify-iot-hub-of-a-completed-file-upload).

Los servicios de back-end pueden suscribirse a las notificaciones de carga de archivos en el punto de conexión de notificaciones de carga de archivos del centro de IoT accesible desde los servicios. Si ha habilitado estas notificaciones en el centro de IoT, las entrega en este punto de conexión cada vez que un dispositivo notifica al centro que ha completado una carga de archivos. Los servicios pueden usar estas notificaciones para desencadenar un procesamiento adicional de los datos del blob. Para obtener más información, vea [Servicio: notificaciones de carga de archivos](#service-file-upload-notifications).

La carga de archivos es totalmente compatible con los SDK de dispositivo y servicios IoT de Azure. Para obtener más información, vea [Carga de archivos mediante un SDK](#file-upload-using-an-sdk).

### <a name="file-upload-quotas-and-limits"></a>Cuotas y límites de carga de archivos

IoT Hub impone límites en cuanto al número de cargas de archivos que se pueden iniciar en un período determinado. El umbral se basa en la SKU y el número de unidades del centro de IoT. Además, cada dispositivo está limitado a 10 cargas de archivos activas a la vez. Para obtener más información, vea [Límites y cuotas](iot-hub-devguide-quotas-throttling.md).

## <a name="associate-an-azure-storage-account-with-iot-hub"></a>Asociación de una cuenta de Azure Storage a IoT Hub

Para poder usar las características de carga de archivos, debe asociar una cuenta de Azure Storage y un contenedor de blobs al centro de IoT. Todas las cargas de archivos de dispositivos registrados en su centro de IoT irán a este contenedor. Para configurar una cuenta de almacenamiento y un contenedor de blobs en su centro de IoT, consulte [Configuración de cargas de archivos de IoT Hub mediante Azure Portal](iot-hub-configure-file-upload.md), [Configuración de cargas de archivos de IoT Hub mediante la CLI de Azure](iot-hub-configure-file-upload-cli.md) o [Configuración de cargas de archivos de IoT Hub mediante PowerShell](iot-hub-configure-file-upload-powershell.md). También puede usar las API de administración de IoT Hub para configurar las cargas de archivos mediante programación.

Si usa el portal, puede crear una cuenta de almacenamiento y un contenedor durante la configuración. De lo contrario, para crear una cuenta de almacenamiento, consulte [Creación de una cuenta de Storage](../storage/common/storage-account-create.md) en la documentación de Azure Storage. Cuando ya tenga una cuenta de almacenamiento, puede ver cómo crear un contenedor de blobs en las [guías de inicio rápido de Azure Blob Storage](/azure/storage/blobs/storage-quickstart-blobs-portal).

Hay otras opciones de configuración que controlan el comportamiento de las cargas de archivos y las notificaciones de esas cargas. En las secciones siguientes, se explican todas las opciones de configuración disponibles. Dependiendo de si usa Azure Portal, la CLI de Azure, PowerShell o las API de administración para configurar las cargas de archivos, es posible que algunas de estas opciones no estén disponibles. Asegúrese de establecer la opción **enableFileUploadNotifications** si desea que se envíen notificaciones a los servicios de back-end cuando se complete una carga de archivos.

### <a name="iot-hub-storage-and-authentication-settings"></a>Configuración de la autenticación y el almacenamiento de IoT Hub

Las siguientes opciones de configuración asocian una cuenta de almacenamiento y un contenedor a su centro de IoT y controlan cómo se autentica el centro en Azure Storage. Estas opciones de configuración no afectan al modo en el que se autentican los dispositivos en Azure Storage. Los dispositivos siempre se autentican con el token de SAS que se presenta en el URI con SAS recuperado de IoT Hub.

| Propiedad | Descripción | Intervalo y valor predeterminado |
| --- | --- | --- |
| **storageEndpoints.$default.authenticationType** | Controla el modo en el que IoT Hub se autentica en Azure Storage. | Los valores posibles son keyBased e identityBased. Valor predeterminado: keyBased. |
| **storageEndpoints.$default.connectionString** | Cadena de conexión a la cuenta de Azure Storage que se usará para las cargas de archivos. | Valor predeterminado: cadena vacía. |
| **storageEndpoints.$default.containerName** | Nombre del contenedor en el que se cargarán los archivos. | Valor predeterminado: cadena vacía. |
| **storageEndpoints.$default.identity** | Identidad administrada que se usará para la autenticación basada en la identidad. | Los valores posibles son `[system]` para la identidad administrada asignada por el sistema o un identificador de recurso para una identidad administrada asignada por el usuario. Este valor no se usa para la autenticación basada en una clave. Valor predeterminado: null. |

### <a name="file-upload-settings"></a>Configuración de la carga de archivos

Las siguientes opciones de configuración controlan las cargas de archivos desde el dispositivo.

| Propiedad | Descripción | Intervalo y valor predeterminado |
| --- | --- | --- |
| **storageEndpoints.$default.ttlAsIso8601** | TTL predeterminado para los URI con SAS generados por IoT Hub. | Intervalo ISO_8601 de hasta 48 horas (1 minuto como mínimo). Valor predeterminado: 1 hora. |

### <a name="file-upload-notification-settings"></a>Configuración de las notificaciones de carga de archivos

Las siguientes opciones de configuración controlan las notificaciones de carga de archivos en los servicios de back-end.

| Propiedad | Descripción | Intervalo y valor predeterminado |
| --- | --- | --- |
| **enableFileUploadNotifications** |Controla si las notificaciones de carga de archivos se escriben en el punto de conexión de notificaciones de archivo. |Bool. Valor predeterminado: False. |
| **fileNotifications.ttlAsIso8601** |TTL predeterminado para las notificaciones de carga de archivos. |Intervalo ISO_8601 de hasta 48 horas (1 minuto como mínimo). Valor predeterminado: 1 hora. |
| **fileNotifications.lockDuration** |Duración del bloqueo de la cola de notificaciones de carga de archivos. |De 5 a 300 segundos. Valor predeterminado: 60 segundos. |
| **fileNotifications.maxDeliveryCount** |Número máximo de entregas en la cola de notificaciones de carga de archivos. |De 1 a 100. Valor predeterminado: 100. |

## <a name="file-upload-using-an-sdk"></a>Carga de archivos mediante un SDK

En las siguientes guías paso a paso, se proporcionan instrucciones detalladas para cargar archivos usando los SDK de dispositivo y servicios IoT de Azure. En ellas, se explica cómo usar Azure Portal para asociar una cuenta de almacenamiento a un centro de IoT y contienen fragmentos de código o hacen referencia a ejemplos que le guiarán para realizar una carga.

| Guía paso a paso | Ejemplo del SDK de dispositivo | Ejemplo del SDK de servicios |
|---------|--------|---------|
| [.NET](iot-hub-csharp-csharp-file-upload.md) | Sí | Sí |
| [Java](iot-hub-java-java-file-upload.md) | Sí | Sí |
| [Node.js](iot-hub-node-node-file-upload.md) | Sí | Sí |
| [Python](iot-hub-python-python-file-upload.md) | Yes | No (no es compatible) |

> [!NOTE]
> El SDK de dispositivo C utiliza una sola llamada en el cliente de dispositivo para realizar cargas de archivos. Para obtener más información, vea [IoTHubDeviceClient_UploadToBlobAsync()](/azure/iot-hub/iot-c-sdk-ref/iothub-device-client-h/iothubdeviceclient-uploadtoblobasync) y [IoTHubDeviceClient_UploadMultipleBlocksToBlobAsync()](/azure/iot-hub/iot-c-sdk-ref/iothub-device-client-h/iothubdeviceclient-uploadmultipleblockstoblobasync). Estas funciones realizan todos los aspectos de la carga de archivos en una sola llamada: inician la carga, cargan el archivo en Azure Storage y se lo notifican a IoT Hub cuando se completa la carga. Esto significa que, además de cualquier protocolo que el dispositivo utilice para comunicarse con IoT Hub, también deberá poder comunicarse mediante HTTPS con Azure Storage, ya que estas funciones realizan llamadas a las API de Azure Storage.

## <a name="device-initialize-a-file-upload"></a>Dispositivo: inicialización de una carga de archivos

El dispositivo llama a la API REST para [crear un URI con SAS para la carga de archivos](/rest/api/iothub/device/create-file-upload-sas-uri) o a la API equivalente de alguno de los SDK de dispositivo para iniciar la carga de archivos.

**Protocolos admitidos:** AMQP, AMQP-WS, MQTT, MQTT-WS y HTTPS. <br/>
**Punto de conexión:** {iot hub}.azure-devices.net/devices/{deviceId}/files. <br/>
**Método**: POST

```json
{
    "blobName":"myfile.txt"
}

```

| Propiedad | Descripción |
|----------|-------------|
| blobName | Nombre del blob para el que se generará el URI con SAS. |

 IoT Hub responde con un identificador de correlación y los elementos de un URI con SAS que el dispositivo puede usar para autenticarse en Azure Storage. Esta respuesta está sujeta a los límites y a la cuota de carga por dispositivo del centro de IoT de destino.

```json
{
    "correlationId":"MjAyMTA3MzAwNjIxXzBiNjgwOGVkLWZjNzQtN...MzYzLWRlZmI4OWQxMzdmNF9teWZpbGUudHh0X3ZlcjIuMA==",
    "hostName":"contosostorageaccount.blob.core.windows.net",
    "containerName":"device-upload-container",
    "blobName":"mydevice/myfile.txt",
    "sasToken":"?sv=2018-03-28&sr=b&sig=mBLiODhpKXBs0y9RVzwk1S...l1X9qAfDuyg%3D&se=2021-07-30T06%3A11%3A10Z&sp=rw"
}

```

| Propiedad | Descripción |
|----------|-------------|
| correlationId | Identificador del dispositivo que se usará al enviar la notificación de carga de archivos completada a IoT Hub. |
| hostName | Nombre de host de la cuenta de Azure Storage para la cuenta de almacenamiento configurada en el centro de IoT. |
| containerName | Nombre del contenedor de blobs configurado en el centro de IoT. |
| blobName | Ubicación donde se almacenará el blob en el contenedor. El nombre tiene el siguiente formato: `{device ID of the device making the request}/{blobName in the request}`. |
| sasToken | Token de SAS que concede acceso de lectura y escritura en el blob con Azure Storage. IoT Hub genera y firma este token. |

Cuando el dispositivo recibe la respuesta, hace lo siguiente:

* Guarda el identificador de correlación para incluirlo en la notificación que se envía a IoT Hub cuando se completa la carga de archivos.

* Usa las demás propiedades para crear un URI con SAS para el blob que utiliza para autenticarse en Azure Storage. El URI con SAS contiene el URI del recurso para el blob solicitado y el token de SAS. Tiene el siguiente formato: `https://{hostName}/{containerName}/{blobName}{sasToken}` (la propiedad `sasToken` de la respuesta tiene el carácter "?" al principio). Las llaves no se incluyen.

    Por ejemplo, para los valores devueltos en el ejemplo anterior, el URI con SAS es `https://contosostorageaccount.blob.core.windows.net/device-upload-container/mydevice/myfile.txt?sv=2018-03-28&sr=b&sig=mBLiODhpKXBs0y9RVzwk1S...l1X9qAfDuyg%3D&se=2021-07-30T06%3A11%3A10Z&sp=rw`.

    Para obtener más información sobre el URI con SAS y el token de SAS, consulte [Creación de una SAS de servicio](/rest/api/storageservices/create-service-sas) en la documentación de Azure Storage.

## <a name="device-upload-file-using-azure-storage-apis"></a>Dispositivo: carga de archivos con las API de Azure Storage

El dispositivo usa las [API REST de Azure Blob Storage](/rest/api/storageservices/blob-service-rest-api) o las API equivalentes del SDK de Azure Storage para cargar el archivo en el blob de Azure Storage.

**Protocolos admitidos:** HTTPS.

En el ejemplo siguiente, se muestra una solicitud [Put Blob](/rest/api/storageservices/put-blob) para crear o actualizar un pequeño blob en bloques. Tenga en cuenta que el URI que se usa para esta solicitud es el URI con SAS devuelto por IoT Hub en la sección anterior. El encabezado `x-ms-blob-type` indica que esta solicitud es para un blob en bloques. Si la solicitud se realiza correctamente, Azure Storage devuelve `201 Created`.

```http
PUT https://contosostorageaccount.blob.core.windows.net/device-upload-container/mydevice/myfile.txt?sv=2018-03-28&sr=b&sig=mBLiODhpKXBs0y9RVzwk1S...l1X9qAfDuyg%3D&se=2021-07-30T06%3A11%3A10Z&sp=rw HTTP/1.1
Content-Length: 11
Content-Type: text/plain; charset=UTF-8
Host: contosostorageaccount.blob.core.windows.net
x-ms-blob-type: BlockBlob

hello world
```

El trabajo con las API de Azure Storage está fuera del ámbito de este artículo. Además de las API REST de Azure Blob Storage para las que se incluyen vínculos en esta sección, puede explorar la siguiente documentación como ayuda para comenzar:

* Si desea obtener más información sobre cómo trabajar con blobs en Azure Storage, consulte la documentación de [Azure Blob Storage](/azure/storage/blobs/).

* Para obtener información sobre el uso de los SDK de cliente de Azure Storage para cargar blobs, vea [Referencia de la API de Azure Blob Storage](/azure/storage/blobs/reference).  

## <a name="device-notify-iot-hub-of-a-completed-file-upload"></a>Dispositivo: notificación a IoT Hub de que se ha completado una carga de archivos

El dispositivo llama a la API REST para [actualizar el estado de la carga de archivos](/rest/api/iothub/device/update-file-upload-status) o a la API equivalente de alguno de los SDK de dispositivo cuando se completa la carga de archivos. El dispositivo debe actualizar el estado de la carga de archivos en IoT Hub, independientemente de si la carga se realiza correctamente o no.

**Protocolos admitidos:** AMQP, AMQP-WS, MQTT, MQTT-WS y HTTPS. <br/>
**Punto de conexión:** {iot hub}.azure-devices.net/devices/{deviceId}/files/notifications. <br/>
**Método**: POST 

```json
{
    "correlationId": "MjAyMTA3MzAwNjIxXzBiNjgwOGVkLWZjNzQtN...MzYzLWRlZmI4OWQxMzdmNF9teWZpbGUudHh0X3ZlcjIuMA==",
    "isSuccess": true,
    "statusCode": 200,
    "statusDescription": "File uploaded successfully"
}

```

 Propiedad | Descripción |
|----------|-------------|
| correlationId | Identificador de correlación recibido en la solicitud del URI con SAS inicial. |
| isSuccess | Valor booleano que indica si la carga de archivos se ha realizado correctamente. |
| statusCode | Entero que representa el código de estado de la carga de archivos. Normalmente, son tres dígitos; por ejemplo, 200 o 201. |
| statusDescription | Descripción del estado de la carga de archivos. |

Cuando IoT Hub recibe una notificación de que se ha completado una carga de archivos del dispositivo, hace lo siguiente:

* Desencadena una notificación de carga de archivos que se le envía a los servicios de back-end si están configuradas las notificaciones de carga de archivos.

* Libera los recursos asociados a la carga de archivos. Si no recibe ninguna notificación, IoT Hub mantiene los recursos hasta que expira el período de vida (TTL) del URI con SAS asociado a la carga.

## <a name="service-file-upload-notifications"></a>Servicio: notificaciones de carga de archivos

Si las notificaciones de carga de archivos están habilitadas en IoT Hub, genera un mensaje de notificación para los servicios de back-end cuando recibe una notificación de que se ha completado una carga de archivos de un dispositivo. IoT Hub entrega estas notificaciones de carga de archivos a través de un punto de conexión al que tienen acceso los servicios. La semántica de recepción de las notificaciones de carga de archivos es la misma que para los mensajes de nube a dispositivo y tiene el mismo [ciclo de vida del mensaje](iot-hub-devguide-messages-c2d.md#the-cloud-to-device-message-life-cycle). Los SDK de servicio exponen API para controlar las notificaciones de carga de archivos.

**Protocolos admitidos:** AMQP, AMQP-WS. <br/>
**Punto de conexión:** {iot hub}.azure-devices.net/messages/servicebound/fileuploadnotifications. <br/>
**Método:** GET

Cada mensaje recuperado del punto de conexión de notificaciones de carga de archivos es un registro JSON con las siguientes propiedades:

```json
{
    "deviceId":"mydevice",
    "blobUri":"https://contosostorageaccount.blob.core.windows.net/device-upload-container/mydevice/myfile.txt",
    "blobName":"mydevice/myfile.txt",
    "lastUpdatedTime":"2021-07-31T00:26:50+00:00",
    "blobSizeInBytes":11,
    "enqueuedTimeUtc":"2021-07-31T00:26:51.5134008Z"
}
```

| Propiedad | Descripción |
| --- | --- |
| enqueuedTimeUtc | Marca de tiempo que indica cuándo se creó la notificación. |
| deviceId | Identificador del dispositivo que cargó el archivo. |
| blobUri | URI del archivo cargado. |
| blobName | Nombre del archivo cargado. El nombre tiene el siguiente formato: `{device ID of the device}/{name of the blob}`.|
| lastUpdatedTime |Marca de tiempo que indica cuándo se actualizó por última vez el archivo. |
| blobSizeInBytes | Entero que representa el tamaño del archivo cargado en bytes. |

Los servicios pueden usar las notificaciones para administrar las cargas. Por ejemplo, pueden desencadenar su propio procesamiento de los datos de blob o el procesamiento de estos datos con otros servicios de Azure, o bien guardar en un registro las notificaciones de carga de archivos para revisarlas más adelante.

## <a name="next-steps"></a>Pasos siguientes

* [Guías paso a paso de carga de archivos](iot-hub-csharp-csharp-file-upload.md)

* [Configuración de cargas de archivos de IoT Hub mediante Azure Portal](iot-hub-configure-file-upload.md)

* [Compatibilidad de IoT Hub con identidades administradas](iot-hub-managed-identity.md)

* [Documentación de Azure Blob Storage](/azure/storage/blobs/)

* En [SDK de dispositivo y servicio IoT de Azure](iot-hub-devguide-sdks.md) se muestran los diversos SDK de lenguaje que puede usar para desarrollar aplicaciones para dispositivo y de servicio que interactúen con IoT Hub.
