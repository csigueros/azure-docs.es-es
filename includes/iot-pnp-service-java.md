---
author: dominicbetts
ms.author: dobett
ms.service: iot-develop
ms.topic: include
ms.date: 11/20/2020
ms.openlocfilehash: 6fa30a56b3fb6e8b16a50bff7fda4c9349348633
ms.sourcegitcommit: 8669087bcbda39e3377296c54014ce7b58909746
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 07/18/2021
ms.locfileid: "114403632"
---
IoT Plug and Play simplifica IoT, ya que permite interactuar con las funcionalidades de un dispositivo sin tener que conocer la implementación subyacente del mismo. En este inicio rápido se muestra cómo usar Java para conectarse y controlar un dispositivo IoT Plug and Play que esté conectado a la solución.

## <a name="prerequisites"></a>Prerrequisitos

[!INCLUDE [iot-pnp-prerequisites](iot-pnp-prerequisites.md)]

Para completar este inicio rápido en Windows, instale el siguiente software en el entorno Windows local:

* Java SE Development Kit 8. En el artículo [Soporte técnico de Java a largo plazo para Azure y Azure Stack](/java/azure/jdk/), en la sección **Soporte técnico a largo plazo**, seleccione **Java 8**.
* [Apache Maven 3](https://maven.apache.org/download.cgi).

### <a name="clone-the-sdk-repository-with-the-sample-code"></a>Clonación del repositorio de SDK con el código de ejemplo

Si completó [Tutorial: Conexión a IoT Hub (Java) de una aplicación de ejemplo del dispositivo IoT Plug and Play que se ejecuta en Windows](../articles/iot-develop/tutorial-connect-device.md), ya ha clonado el repositorio.

Abra un símbolo del sistema en el directorio que prefiera. Ejecute el siguiente comando para clonar el repositorio de GitHub de los [SDK de Microsoft Azure IoT para Java](https://github.com/Azure/azure-iot-sdk-java) en esta ubicación:

```cmd
git clone https://github.com/Azure/azure-iot-sdk-java.git
```

## <a name="build-and-run-the-sample-device"></a>Compilación y ejecución del dispositivo de ejemplo

[!INCLUDE [iot-pnp-environment](iot-pnp-environment.md)]

Para más información sobre la configuración de ejemplo, consulte el [archivo Léame de ejemplo](https://github.com/Azure/azure-iot-sdk-java/blob/master/device/iot-device-samples/readme.md).

En este inicio rápido, se usa como dispositivo IoT Plug and Play un dispositivo termostato de ejemplo que se ha escrito en Java. Para ejecutar el dispositivo de ejemplo:

1. Abra una ventana de terminal y vaya a la carpeta local que contiene el repositorio de SDK de Microsoft Azure IoT para Java que ha clonado de GitHub.

1. Esta ventana de terminal se usa ahora como terminal del **dispositivo**. Vaya a la carpeta raíz del repositorio clonado. Instale todas las dependencias, para lo que debe ejecutar el siguiente comando:

1. Ejecute el siguiente comando para compilar la aplicación del dispositivo de ejemplo:

    ```cmd
    mvn install -T 2C -DskipTests
    ```

1. Para ejecutar la aplicación del dispositivo de ejemplo, vaya a la carpeta *device\iot-device-samples\pnp-device-sample\thermostat-device-sample* y ejecute el siguiente comando:

    ```cmd
    mvn exec:java -Dexec.mainClass="samples.com.microsoft.azure.sdk.iot.device.Thermostat"
    ```

El dispositivo ya está listo para recibir comandos y actualizaciones de propiedades, y ha empezado a enviar datos de telemetría al centro. No deje de ejecutar el dispositivo de ejemplo mientras completa los pasos siguientes.

## <a name="run-the-sample-solution"></a>Ejecución de la solución de ejemplo

En el artículo sobre la [configuración del entorno para los inicios rápidos y tutoriales de IoT Plug and Play](../articles/iot-develop/set-up-environment.md) se crearon dos variables de entorno para configurar el ejemplo para conectarse a su instancia de IoT Hub y al dispositivo:

* **IOTHUB_CONNECTION_STRING**: la cadena de conexión de IoT Hub que anotó anteriormente.
* **IOTHUB_DEVICE_ID**: `"my-pnp-device"`.

En este inicio rápido, se usa una solución de IoT de ejemplo escrita en Java para interactuar con el dispositivo de ejemplo que acaba de configurar.

> [!NOTE]
> En este ejemplo se usa el espacio de nombres **com.microsoft.azure.sdk.iot.service** del **cliente del servicio IoT Hub**. Para más información sobre las API, incluida la API de gemelos digitales, consulte la [guía para desarrolladores de servicios](../articles/iot-develop/concepts-developer-guide-service.md).

1. Abra otra ventana de terminal para utilizarla como terminal de **servicio**.

1. En el repositorio del SDK de Java clonado, vaya a la carpeta *service\iot-service-samples\pnp-service-sample\thermostat-service-sample*.

1. Para ejecutar la aplicación del servicio de ejemplo, utilice el comando siguiente:

    ```cmd
    mvm exec:java -Dexec.mainClass="samples.com.microsoft.azure.sdk.iot.service.Thermostat"
    ```

### <a name="get-device-twin"></a>Obtener dispositivo gemelo

En el fragmento de código siguiente se muestra cómo recuperar el dispositivo gemelo en el servicio:

```java
 // Get the twin and retrieve model Id set by Device client.
DeviceTwinDevice twin = new DeviceTwinDevice(deviceId);
twinClient.getTwin(twin);
System.out.println("Model Id of this Twin is: " + twin.getModelId());
```

### <a name="update-a-device-twin"></a>Actualización de un dispositivo gemelo

En el fragmento de código siguiente se muestra cómo usar una *revisión* para actualizar las propiedades a través del dispositivo gemelo:

```java
String propertyName = "targetTemperature";
double propertyValue = 60.2;
twin.setDesiredProperties(Collections.singleton(new Pair(propertyName, propertyValue)));
twinClient.updateTwin(twin);
```

La salida del dispositivo muestra cómo responde el dispositivo a esta actualización de propiedades.

### <a name="invoke-a-command"></a>Invocación de un comando

En el fragmento de código siguiente se muestra cómo llamar a un comando en el dispositivo:

```java
// The method to invoke for a device without components should be "methodName" as defined in the DTDL.
String methodToInvoke = "getMaxMinReport";
System.out.println("Invoking method: " + methodToInvoke);

Long responseTimeout = TimeUnit.SECONDS.toSeconds(200);
Long connectTimeout = TimeUnit.SECONDS.toSeconds(5);

// Invoke the command.
String commandInput = ZonedDateTime.now(ZoneOffset.UTC).minusMinutes(5).format(DateTimeFormatter.ISO_DATE_TIME);
MethodResult result = methodClient.invoke(deviceId, methodToInvoke, responseTimeout, connectTimeout, commandInput);
if(result == null)
{
    throw new IOException("Method result is null");
}

System.out.println("Method result status is: " + result.getStatus());
```

La salida del dispositivo muestra cómo responde el dispositivo a este comando.
