---
title: SDK de dispositivo IoT de Azure para C | Microsoft Docs
description: Introducción al SDK de dispositivo IoT de Azure para C e información sobre cómo crear aplicaciones para dispositivos que se comunican con un centro de IoT Hub.
author: eross-msft
ms.service: iot-hub
services: iot-hub
ms.devlang: c
ms.topic: conceptual
ms.date: 05/17/2019
ms.author: lizross
ms.custom:
- amqp
- mqtt
- 'Role: Cloud Development'
- 'Role: IoT Device'
ms.openlocfilehash: cfbbae0cd7fa54b3403b52d1cced5e81f4df7438
ms.sourcegitcommit: 05c8e50a5df87707b6c687c6d4a2133dc1af6583
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/16/2021
ms.locfileid: "132556028"
---
# <a name="azure-iot-device-sdk-for-c"></a>SDK de dispositivo IoT de Azure para C

**SDK de dispositivo IoT de Azure para C** es un conjunto de bibliotecas diseñadas para simplificar el proceso de envío y recepción de mensajes desde el servicio **Azure IoT Hub**. Hay distintas variantes del SDK, cada una dirigida a una plataforma concreta, pero este artículo describe el **SDK de dispositivo IoT de Azure para C**.

> [!NOTE]
> El SDK de C insertado es una alternativa para los dispositivos restringidos que admite el enfoque traiga su propia red (BYON). Los desarrolladores de IoT tienen la libertad de utilizar el cliente de MQTT, TLS y el socket de su elección para crear una solución de dispositivo. [Obtenga más información sobre el SDK de C insertado](https://github.com/Azure/azure-sdk-for-c/tree/master/sdk/docs/iot).

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-partial.md)]

El SDK de dispositivo IoT de Azure para C está escrito en ANSI C (C99) para maximizar su portabilidad. Esta característica hace que las bibliotecas sean idóneas para operar en varias plataformas y dispositivos, especialmente si la prioridad es minimizar la superficie de la memoria y del disco.

Existe una amplia gama de plataformas en las que se ha probado el SDK (consulte [Catálogo de dispositivos de Azure Certified for IoT](https://devicecatalog.azure.com/) para obtener más información). Aunque este artículo incluye tutoriales con código de ejemplo que se ejecuta en la plataforma Windows, el código que describe en él es idéntico en todas las plataformas compatibles.

El siguiente vídeo contiene información general sobre el SDK para C de Azure IoT:

>[!VIDEO https://channel9.msdn.com/Shows/Internet-of-Things-Show/Azure-IoT-C-SDK-insights/Player]

En este artículo se presenta la arquitectura del SDK de dispositivo IoT de Azure para C. En él se muestra cómo inicializar la biblioteca de dispositivos, enviar datos a IoT Hub y recibir mensajes de dicho servicio. La información de este artículo debería ser suficiente para comenzar a usar el SDK, pero también se proporcionan referencias a información adicional sobre las bibliotecas.

## <a name="sdk-architecture"></a>Arquitectura del SDK

Puede encontrar el [**SDK de dispositivo IoT de Azure para C**](https://github.com/Azure/azure-iot-sdk-c) en el repositorio de GitHub y ver los detalles de la API en la [referencia de la API de C](/azure/iot-hub/iot-c-sdk-ref/).

La versión más reciente de las bibliotecas se puede encontrar en la rama **principal** del repositorio:

  ![Captura de pantalla de la rama principal del repositorio](./media/iot-hub-device-sdk-c-intro/RepoMasterBranch.png)

* La implementación básica del SDK está en la carpeta **iothub\_client** que contiene la implementación de la capa inferior de la API del SDK: la biblioteca **IoTHubClient**. La biblioteca **IoTHubClient** contiene las API que implementan la mensajería sin formato tanto para enviar mensajes a IoT Hub como para recibirlos de él. Quien use esta biblioteca es responsable de la implementación de la serialización de mensajes, pero otros detalles de la comunicación con IoT Hub se controlan automáticamente.

* La biblioteca **IoTHubClient** depende de otras bibliotecas de código abierto:

  * La biblioteca [Azure C shared utility](https://github.com/Azure/azure-c-shared-utility), que proporciona una funcionalidad común para las tareas básicas (como cadenas, manipulación de listas y E/S) necesarias en varios SDK de C relacionados con Azure.

  * La biblioteca [Azure uAMQP](https://github.com/Azure/azure-uamqp-c), que es una implementación del lado cliente de AMQP optimizada para los dispositivos de restricción de recursos.

  * La biblioteca [Azure uMQTT](https://github.com/Azure/azure-umqtt-c), que es una biblioteca de uso general que implementa el protocolo MQTT y que está optimizada para los dispositivos de restricción de recursos.

El uso de estas bibliotecas se entiende más fácilmente si se examina el código de ejemplo. Las siguientes secciones le guiarán a través de varias aplicaciones de ejemplo que se incluyen en el SDK. Este tutorial le permitirá hacerse una idea muy aproximada de las distintas funcionalidades de las capas arquitectónicas del SDK y proporcionará una introducción al funcionamiento de las API.

## <a name="before-you-run-the-samples"></a>Antes de ejecutar los ejemplos

Antes de ejecutar los ejemplos en el SDK de dispositivo IoT de Azure para C, debe [crear una instancia del servicio IoT Hub](iot-hub-create-through-portal.md) en su suscripción de Azure. Después complete las siguientes tareas:

* Preparación del entorno de desarrollo
* Obtención de las credenciales del dispositivo.

### <a name="prepare-your-development-environment"></a>Preparación del entorno de desarrollo

Se proporcionan paquetes para plataformas comunes (como NuGet para Windows o apt_get para Debian y Ubuntu) y los ejemplos usan estos paquetes cuando estén disponibles. En algunos casos, es preciso compilar el SDK para el dispositivo. Si necesita compilar el SDK, consulte [Prepare your development environment](https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/devbox_setup.md) (Preparación de un entorno de desarrollo) en el repositorio de GitHub.

Para obtener el código de la aplicación de ejemplo, descargue una copia del SDK de GitHub. Obtenga la copia del código fuente de la rama **principal** del [repositorio de GitHub](https://github.com/Azure/azure-iot-sdk-c).


### <a name="obtain-the-device-credentials"></a>Obtención de las credenciales del dispositivo

Ahora que tiene el código fuente de ejemplo, lo siguiente que debe hacer es obtener un conjunto de credenciales del dispositivo. Para que un dispositivo pueda acceder a un centro de IoT, primero debe agregar el dispositivo al registro de identidad del centro de IoT. Al agregar el dispositivo, se obtiene un conjunto de credenciales del dispositivo que se necesitan para que el dispositivo se pueda conectar al servicio IoT Hub. Las aplicaciones de ejemplo de la siguiente sección esperan que estas credenciales tengan la forma de una **cadena de conexión de dispositivo**.

Hay varias herramientas de código abierto que le ayudan a administrar IoT Hub.

* Una aplicación Windows llamada [Azure IoT Explorer](https://github.com/Azure/azure-iot-explorer).

* Una extensión de Visual Studio Code multiplataforma llamada [Azure IoT Tools](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools).

* Una herramienta de la CLI de Python multiplataforma denominada [extensión de IoT para la CLI de Azure](https://github.com/Azure/azure-iot-cli-extension).

En este tutorial se utiliza la herramienta gráfica *device explorer*. Puede usar *Azure IoT Tools para VS Code* si desarrolla en VS Code. También puede usar *extensión de IoT para la CLI de Azure 2.0* si prefiere usar una herramienta de la CLI.

La herramienta device explorer usa las bibliotecas del servicio Azure IoT para realizar varias funciones en IoT Hub, entre las que se incluye la adición de dispositivos. Si usa la herramienta device explorer para agregar un dispositivo, obtiene una cadena de conexión para el dispositivo. Esta cadena de conexión se necesita para ejecutar las aplicaciones de ejemplo.

Si no está familiarizado con la herramienta device explorer, el siguiente procedimiento describe cómo usarla para agregar un dispositivo y obtener la cadena de conexión del dispositivo.

1. Para instalar la herramienta device explorer, consulte [How to use Device Explorer for IoT Hub devices](https://github.com/Azure/azure-iot-sdk-csharp/tree/main/tools/) (Uso de la herramienta device explorer para dispositivos de IoT Hub).

1. Al ejecutar el programa se ve esta interfaz:

   ![Captura de pantalla de Device Explorer Twin](./media/iot-hub-device-sdk-c-intro/DeviceExplorerTwinConfigTab.png)

1. Escriba su **cadena de conexión del IoT Hub** en el primer campo y haga clic en **Update** (Actualizar). Este paso configura la herramienta para que pueda comunicarse con IoT Hub. 

La **Cadena de conexión** puede encontrarse en **Servicio IoT Hub** > **Configuración** > **Directiva de acceso compartido** > **iothubowner**.

1. Cuando se configura la cadena de conexión de IoT Hub, haga clic en la pestaña **Management** (Administración):

   ![Captura de pantalla de Management de Device Explorer Twin](./media/iot-hub-device-sdk-c-intro/DeviceExplorerTwinManagementTab.png)

En esta pestaña se administran los dispositivos registrados en IoT Hub.

1. Para crear un dispositivo, haga clic en el botón **Create** (Crear). Se muestra un cuadro de diálogo con un conjunto de claves (principal y secundaria) previamente rellenadas. Escriba un valor en **Device ID** (Id. de dispositivo) y haga clic en **Create** (Crear).

   ![Captura de pantalla de Create Device](./media/iot-hub-device-sdk-c-intro/CreateDevice.png)

1. Cuando se crea el dispositivo, la lista de dispositivos se actualiza con todos los dispositivos registrados, incluido el que acaba de crear. Si hace clic con el botón derecho en el dispositivo nuevo, verá este menú:

   ![Resultado de hacer clic con el botón derecho en Device Explorer Twin](./media/iot-hub-device-sdk-c-intro/DeviceExplorerTwinManagementTab_RightClick.png)

1. Si elige la opción **Copy connection string for selected device** (Copiar cadena de conexión de dispositivo seleccionado), la cadena de conexión del dispositivo se copia en el Portapapeles. Mantenga una copia de la cadena de conexión de dispositivo, ya que se necesita al ejecutar las aplicaciones de ejemplo que se describen en las secciones siguientes.

Cuando haya completados los pasos anteriores, estará listo para empezar a ejecutar código. La mayoría de los ejemplos tienen una constante en la parte superior del archivo de código fuente principal, que permite especificar una cadena de conexión. Por ejemplo, la línea correspondiente de la aplicación **iothub_client\_samples\_iothub_convenience_sample** aparece de la siguiente manera.

```c
static const char* connectionString = "[device connection string]";
```

## <a name="use-the-iothubclient-library"></a>Uso de la biblioteca IoTHubClient

Dentro de la carpeta **iothub\_client**, en el repositorio [azure-iot-sdks](https://github.com/azure/azure-iot-sdk-c), hay una carpeta **samples** que contiene una aplicación denominada **iothub\_client\_sample\_mqtt**.

La versión de Windows para la aplicación **iothub_client\_samples\_iothub_convenience_sample** incluye la siguiente solución de Visual Studio:

  ![Explorador de soluciones de Visual Studio](./media/iot-hub-device-sdk-c-intro/iothub-client-sample-mqtt.png)

> [!NOTE]
> Si Visual Studio le solicita que redestine el proyecto a la última versión, acepte la solicitud.

Esta solución contiene un proyecto. En esta solución hay cuatro paquetes de NuGet instalados:

* Microsoft.Azure.C.SharedUtility
* Microsoft.Azure.IoTHub.MqttTransport
* Microsoft.Azure.IoTHub.IoTHubClient
* Microsoft.Azure.umqtt

El paquete **Microsoft.Azure.C.SharedUtility** siempre es necesario al trabajar con el SDK. Este ejemplo usa el protocolo MQTT, por lo que es necesario incluir los paquetes **Microsoft.Azure.umqtt** y **Microsoft.Azure.IoTHub.MqttTransport** (hay paquetes equivalentes para AMQP y HTTPS). Como en el ejemplo se usa la biblioteca **IoTHubClient**, se debe incluir también el paquete **Microsoft.Azure.IoTHub.IoTHubClient** en la solución.

La implementación de la aplicación de ejemplo en se encuentra en el archivo de código fuente **iothub_client\_samples\_iothub_convenience_sample**.

En los siguientes pasos se usa esta aplicación de ejemplo para guiarle por los requisitos necesarios para usar la biblioteca **IoTHubClient**.

### <a name="initialize-the-library"></a>Inicialización de la biblioteca

> [!NOTE]
> Antes de empezar a trabajar con las bibliotecas, puede que necesite realizar alguna tarea de inicialización específica de la plataforma. Por ejemplo, si tiene previsto usar AMQP en Linux, debe inicializar la biblioteca OpenSSL. Los ejemplos del [repositorio de GitHub](https://github.com/Azure/azure-iot-sdk-c) llaman a la función **platform\_init** de la utilidad cuando el cliente se inicia y llaman a la función **platform\_deinit** antes de salir. Estas funciones se declaran en el archivo de encabezado platform.h. Examine las definiciones de estas funciones para la plataforma de destino del [repositorio](https://github.com/Azure/azure-iot-sdk-c) para determinar si necesita incluir código de inicialización específico de la plataforma en el cliente.

Para empezar a trabajar con las bibliotecas, primero asigne un identificador de cliente de IoT Hub:

```c
if ((iotHubClientHandle = 
  IoTHubClient_LL_CreateFromConnectionString(connectionString, MQTT_Protocol)) == NULL)
{
    (void)printf("ERROR: iotHubClientHandle is NULL!\r\n");
}
else
{
    ...
```

Pase a esta función una copia de la cadena de conexión del dispositivo que obtuvo de la herramienta device explorer. Designe también el protocolo de comunicaciones que va a usar. En este ejemplo se usa MQTT, pero también se pueden usar AMQP y HTTPS.

Cuando tenga un valor **IOTHUB\_CLIENT\_HANDLE** válido, puede empezar a llamar a las API para enviar mensajes a IoT Hub y recibirlos de este.

### <a name="send-messages"></a>Envío de mensajes

La aplicación de ejemplo configura un bucle para enviar mensajes a IoT Hub. El siguiente fragmento de código:

- Crea un mensaje.
- Agrega una propiedad al mensaje.
- Envía un mensaje.

Primero, cree un mensaje:

```c
size_t iterator = 0;
do
{
    if (iterator < MESSAGE_COUNT)
    {
        sprintf_s(msgText, sizeof(msgText), "{\"deviceId\":\"myFirstDevice\",\"windSpeed\":%.2f}", avgWindSpeed + (rand() % 4 + 2));
        if ((messages[iterator].messageHandle = IoTHubMessage_CreateFromByteArray((const unsigned char*)msgText, strlen(msgText))) == NULL)
        {
            (void)printf("ERROR: iotHubMessageHandle is NULL!\r\n");
        }
        else
        {
            messages[iterator].messageTrackingId = iterator;
            MAP_HANDLE propMap = IoTHubMessage_Properties(messages[iterator].messageHandle);
            (void)sprintf_s(propText, sizeof(propText), "PropMsg_%zu", iterator);
            if (Map_AddOrUpdate(propMap, "PropName", propText) != MAP_OK)
            {
                (void)printf("ERROR: Map_AddOrUpdate Failed!\r\n");
            }

            if (IoTHubClient_LL_SendEventAsync(iotHubClientHandle, messages[iterator].messageHandle, SendConfirmationCallback, &messages[iterator]) != IOTHUB_CLIENT_OK)
            {
                (void)printf("ERROR: IoTHubClient_LL_SendEventAsync..........FAILED!\r\n");
            }
            else
            {
                (void)printf("IoTHubClient_LL_SendEventAsync accepted message [%d] for transmission to IoT Hub.\r\n", (int)iterator);
            }
        }
    }
    IoTHubClient_LL_DoWork(iotHubClientHandle);
    ThreadAPI_Sleep(1);

    iterator++;
} while (g_continueRunning);
```

Cada vez que envía un mensaje, especifica una referencia a una función de devolución de llamada que se invoca cuando se envían los datos. En este ejemplo, la función de devolución de llamada de llama **SendConfirmationCallback**. El siguiente fragmento de código muestra dicha función:

```c
static void SendConfirmationCallback(IOTHUB_CLIENT_CONFIRMATION_RESULT result, void* userContextCallback)
{
    EVENT_INSTANCE* eventInstance = (EVENT_INSTANCE*)userContextCallback;
    (void)printf("Confirmation[%d] received for message tracking id = %zu with result = %s\r\n", callbackCounter, eventInstance->messageTrackingId, MU_ENUM_TO_STRING(IOTHUB_CLIENT_CONFIRMATION_RESULT, result));
    /* Some device specific action code goes here... */
    callbackCounter++;
    IoTHubMessage_Destroy(eventInstance->messageHandle);
}
```

Observe la llamada a la función **IoTHubMessage\_Destroy** cuando termine con el mensaje. Esta función libera los recursos asignados al crear el mensaje.

### <a name="receive-messages"></a>Recepción de mensajes

La recepción de mensajes es una operación asincrónica. En primer lugar, se registra la devolución de llamada que se invocará cuando el dispositivo recibe un mensaje:

```c
if (IoTHubClient_LL_SetMessageCallback(iotHubClientHandle, ReceiveMessageCallback, &receiveContext) != IOTHUB_CLIENT_OK)
{
    (void)printf("ERROR: IoTHubClient_LL_SetMessageCallback..........FAILED!\r\n");
}
else
{
    (void)printf("IoTHubClient_LL_SetMessageCallback...successful.\r\n");
    ...
```

El último parámetro es un puntero nulo a lo que quiera. En el ejemplo, es un puntero a un entero, pero podría ser un puntero a una estructura de datos más compleja. Este parámetro permite que la función de devolución de llamada funcione en un estado compartido con el autor de la llamada de esta función.

Cuando el dispositivo recibe un mensaje, se invoca la función de devolución de llamada registrada. Dicha función de devolución de llamada recupera:

* El identificador del mensaje y el identificador de correlación del mensaje.
* El contenido del mensaje.
* Todas las propiedades personalizadas del mensaje.

```c
static IOTHUBMESSAGE_DISPOSITION_RESULT ReceiveMessageCallback(IOTHUB_MESSAGE_HANDLE message, void* userContextCallback)
{
    int* counter = (int*)userContextCallback;
    const char* buffer;
    size_t size;
    MAP_HANDLE mapProperties;
    const char* messageId;
    const char* correlationId;

    // Message properties
    if ((messageId = IoTHubMessage_GetMessageId(message)) == NULL)
    {
        messageId = "<null>";
    }

    if ((correlationId = IoTHubMessage_GetCorrelationId(message)) == NULL)
    {
        correlationId = "<null>";
    }

    // Message content
    if (IoTHubMessage_GetByteArray(message, (const unsigned char**)&buffer, &size) != IOTHUB_MESSAGE_OK)
    {
        (void)printf("unable to retrieve the message data\r\n");
    }
    else
    {
        (void)printf("Received Message [%d]\r\n Message ID: %s\r\n Correlation ID: %s\r\n Data: <<<%.*s>>> & Size=%d\r\n", *counter, messageId, correlationId, (int)size, buffer, (int)size);
        // If we receive the work 'quit' then we stop running
        if (size == (strlen("quit") * sizeof(char)) && memcmp(buffer, "quit", size) == 0)
        {
            g_continueRunning = false;
        }
    }

    // Retrieve properties from the message
    mapProperties = IoTHubMessage_Properties(message);
    if (mapProperties != NULL)
    {
        const char*const* keys;
        const char*const* values;
        size_t propertyCount = 0;
        if (Map_GetInternals(mapProperties, &keys, &values, &propertyCount) == MAP_OK)
        {
            if (propertyCount > 0)
            {
                size_t index;

                printf(" Message Properties:\r\n");
                for (index = 0; index < propertyCount; index++)
                {
                    (void)printf("\tKey: %s Value: %s\r\n", keys[index], values[index]);
                }
                (void)printf("\r\n");
            }
        }
    }

    /* Some device specific action code goes here... */
    (*counter)++;
    return IOTHUBMESSAGE_ACCEPTED;
}
```

Use la función **IoTHubMessage\_GetByteArray** para recuperar el mensaje, que en este ejemplo es una cadena.

### <a name="uninitialize-the-library"></a>Anulación de la inicialización de la biblioteca

Cuando haya terminado de enviar eventos y recibir de mensajes, puede anular la inicialización de la biblioteca de IoT. Para ello, emita la siguiente llamada de función:

```c
IoTHubClient_LL_Destroy(iotHubClientHandle);
```

Esto libera los recursos asignados que previamente ha asignado la función **IoTHubClient\_CreateFromConnectionString**.

Como puede ver, es fácil enviar y recibir mensajes con la biblioteca **IoTHubClient**. La biblioteca controla los detalles de la comunicación con Azure IoT Hub, incluido el protocolo que debe usar (desde la perspectiva del desarrollador, es una opción de configuración simple).

## <a name="next-steps"></a>Pasos siguientes

Este artículo contiene los aspectos básicos del uso de las bibliotecas en el **SDK de dispositivo IoT de Azure para C**. Se le ha proporcionado información suficiente para conocer lo que incluye el SDK, su arquitectura y cómo empezar a trabajar con los ejemplos de Windows. El siguiente artículo continúa la descripción del SDK y explica [más información sobre la biblioteca IoTHubClient](iot-hub-device-sdk-c-iothubclient.md).

Para más información acerca del desarrollo para IoT Hub, consulte los [SDK de IoT Hub](iot-hub-devguide-sdks.md).

Para explorar aún más las funcionalidades de IoT Hub, consulte:

* [Implementación de IA en dispositivos perimetrales con Azure IoT Edge](../iot-edge/quickstart-linux.md)
