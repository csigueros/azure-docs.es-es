---
title: Ingesta de telemetría desde IoT Hub
titleSuffix: Azure Digital Twins
description: Consulte cómo ingerir los mensajes de telemetría de dispositivos desde IoT Hub.
author: baanders
ms.author: baanders
ms.date: 9/9/2021
ms.topic: how-to
ms.service: digital-twins
ms.openlocfilehash: 8fd5b6a046c53a34e41340250c8d6ac38fdf5706
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/24/2021
ms.locfileid: "128667357"
---
# <a name="ingest-iot-hub-telemetry-into-azure-digital-twins"></a>Ingesta de telemetría de IoT Hub en Azure Digital Twins

Azure Digital Twins se basa en los datos de los dispositivos IoT y otros orígenes. Un origen común de los datos del dispositivo que se va a usar en Azure Digital Twins es [IoT Hub](../iot-hub/about-iot-hub.md).

El proceso de ingesta de datos en Azure Digital Twins consiste en configurar un recurso de proceso externo como, por ejemplo, una función creada con [Azure Functions](../azure-functions/functions-overview.md). La función recibe los datos y usa las [API de DigitalTwins](/rest/api/digital-twins/dataplane/twins) para establecer propiedades o desencadenar eventos de telemetría en los [gemelos digitales](concepts-twins-graph.md) en consecuencia. 

Este documento de procedimientos le guía en el proceso de escritura de una función que pueda ingerir datos de telemetría de IoT Hub.

## <a name="prerequisites"></a>Requisitos previos

Antes de continuar con este ejemplo, debe configurar los siguientes recursos como requisito previo:
* **Una instancia de IoT Hub**. Para instrucciones, consulte la sección *Creación de una instancia de IoT Hub* de [este inicio rápido de IoT Hub](../iot-hub/quickstart-send-telemetry-cli.md).
* **Una instancia de Azure Digital Twins** que recibirá la telemetría del dispositivo. Para obtener instrucciones, consulte [Configuración de una instancia de Azure Digital Twins y autenticación](./how-to-set-up-instance-portal.md).

En este artículo se utiliza también **Visual Studio**. Puede descargar la versión más reciente de [Descargas de Visual Studio](https://visualstudio.microsoft.com/downloads/).

## <a name="example-telemetry-scenario"></a>Escenario de telemetría de ejemplo

En este procedimiento se explica cómo enviar mensajes desde IoT Hub a Azure Digital Twins, mediante una función de Azure. Hay muchas configuraciones y estrategias de coincidencia posibles que puede usar para enviar mensajes, pero el ejemplo de este artículo contiene los siguientes elementos:
* Un dispositivo de termómetro en IoT Hub, con un identificador de dispositivo conocido
* Un gemelo digital para representar el dispositivo, con un identificador coincidente

> [!NOTE]
> En este ejemplo se usa una coincidencia de identificador sencillo entre el identificador de dispositivo y el identificador de gemelo digital correspondiente, pero es posible proporcionar asignaciones más sofisticadas del dispositivo a su gemelo (por ejemplo, con una tabla de asignación).

Siempre que el dispositivo de termostato envía un evento de telemetría de temperatura, una función procesa la telemetría y la propiedad *temperatura* del gemelo digital debe actualizarse. Este escenario se describe en un diagrama a continuación:

:::image type="content" source="media/how-to-ingest-iot-hub-data/events.png" alt-text="Diagrama de un dispositivo de IoT Hub que envía la telemetría de temperatura a una función de Azure que actualiza una propiedad de temperatura en un gemelo de Azure Digital Twins." border="false":::

## <a name="add-a-model-and-twin"></a>Adición de un modelo y un gemelo

En esta sección, configurará un [gemelo digital](concepts-twins-graph.md) en Azure Digital Twins que representará el dispositivo de termostato, y se actualizará con información de IoT Hub.

Para crear un gemelo de tipo termostato, primero debe cargar el [modelo](concepts-models.md) de termostato en la instancia, que describe las propiedades de un termostato y que se usará más adelante para crear el gemelo.

[!INCLUDE [digital-twins-thermostat-model-upload.md](../../includes/digital-twins-thermostat-model-upload.md)]

A continuación, deberá **crear un gemelo con este modelo**. Use el comando siguiente para crear un gemelo de termostato llamado thermostat67 y establezca 0,0 como valor de temperatura inicial.

```azurecli-interactive
az dt twin create  --dt-name <instance-name> --dtmi "dtmi:contosocom:DigitalTwins:Thermostat;1" --twin-id thermostat67 --properties '{"Temperature": 0.0,}'
```

Si el gemelo se crea correctamente, la salida de la CLI del comando debe ser similar a la siguiente:
```json
{
  "$dtId": "thermostat67",
  "$etag": "W/\"0000000-9735-4f41-98d5-90d68e673e15\"",
  "$metadata": {
    "$model": "dtmi:contosocom:DigitalTwins:Thermostat;1",
    "Temperature": {
      "lastUpdateTime": "2021-09-09T20:32:46.6692326Z"
    }
  },
  "Temperature": 0.0
}
```

## <a name="create-a-function"></a>Creación de una función

En esta sección, creará una función de Azure para acceder a Azure Digital Twins y actualizar los gemelos en función de los eventos de telemetría de IoT que reciba. Siga los pasos que se indican a continuación para crear y publicar la función.

1. En primer lugar, cree un proyecto de aplicación de funciones en Visual Studio. Para obtener instrucciones sobre cómo hacerlo, consulte [Desarrollo de Azure Functions con Visual Studio](../azure-functions/functions-develop-vs.md#create-an-azure-functions-project).

2. Agregue los siguientes paquetes al proyecto:
    * [Azure.DigitalTwins.Core](https://www.nuget.org/packages/Azure.DigitalTwins.Core/)
    * [Azure.Identity](https://www.nuget.org/packages/Azure.Identity/)
    * [Microsoft.Azure.WebJobs.Extensions.EventGrid](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.EventGrid/)

3. Cambie el nombre de la función de ejemplo *Function1.cs* que Visual Studio ha generado a *IoTHubtoTwins.cs*. Reemplace el código de este archivo por el código siguiente:

    :::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/IoTHubToTwins.cs":::

    Guarde el código de función.

4. Publique el proyecto con la función *IoTHubtoTwins.cs* en una aplicación de funciones de Azure. Para obtener instrucciones sobre cómo hacerlo, consulte [Desarrollo de Azure Functions con Visual Studio](../azure-functions/functions-develop-vs.md#publish-to-azure).

[!INCLUDE [digital-twins-verify-function-publish.md](../../includes/digital-twins-verify-function-publish.md)]

Para acceder a Azure Digital Twins, la aplicación de funciones necesita una identidad administrada por el sistema con permisos para acceder a la instancia de Azure Digital Twins. Va a realizar esa configuración a continuación.

### <a name="configure-the-function-app"></a>Configuración de la Function App

A continuación, **asigne un rol de acceso** para la función y **configure las opciones de la aplicación** para que pueda acceder a la instancia de Azure Digital Twins.

[!INCLUDE [digital-twins-configure-function-app.md](../../includes/digital-twins-configure-function-app.md)]

## <a name="connect-your-function-to-iot-hub"></a>Conexión de la función a IoT Hub

En esta sección, configurará la función como un destino de evento para los datos del dispositivo de IoT Hub. De esta forma, se garantiza que los datos del dispositivo de termostato de IoT Hub se enviarán a la función de Azure para procesarlos.

En [Azure Portal](https://portal.azure.com/), navegue a la instancia de IoT Hub que creó en la sección [Requisitos previos](#prerequisites). En **Eventos**, cree una suscripción para su función.

:::image type="content" source="media/how-to-ingest-iot-hub-data/add-event-subscription.png" alt-text="Captura de pantalla de Azure Portal que muestra la adición de una suscripción de evento.":::

En la página **Crear suscripción de eventos**, rellene los campos como se indica a continuación:
  1. En **Nombre**, elija el nombre que desee para la suscripción de eventos.
  2. En **Esquema de eventos**, elija _Esquema de Event Grid_.
  3. En **System Topic Name** (Nombre de tema del sistema), elija el nombre que prefiera.
  1. En **Filtro para tipos de evento**, active la casilla _Device Telemetry_ (Telemetría de dispositivo) y desactive otros tipos de eventos.
  1. En **Tipo de punto de conexión**, seleccione _Función de Azure_.
  1. En **Punto de conexión**, use el vínculo _Seleccionar un extremo_ para elegir qué función de Azure se usará para el punto de conexión.
    
:::image type="content" source="media/how-to-ingest-iot-hub-data/create-event-subscription.png" alt-text="Captura de pantalla de Azure Portal que muestra la creación de los detalles de una suscripción a eventos.":::

En la página _Seleccionar la función de Azure_ que se abre, compruebe los detalles siguientes.
 1. **Suscripción**: Su suscripción de Azure.
 2. **Grupo de recursos**: su grupo de recursos.
 3. **Aplicación de funciones**: nombre de la aplicación de funciones.
 4. **Ranura**: _producción_
 5. **Función**: seleccione la función anterior, *IoTHubtoTwins*, en la lista desplegable.

Guarde los detalles con el botón _Confirmar selección_.            
      
:::image type="content" source="media/how-to-ingest-iot-hub-data/select-azure-function.png" alt-text="Captura de pantalla de Azure Portal para seleccionar la función.":::

Seleccione el botón _Crear_ para crear una suscripción de eventos.

## <a name="send-simulated-iot-data"></a>Envío de datos de IoT simulados

Para probar la nueva función de entrada, use el simulador de dispositivos de [Conexión de una solución de un extremo a otro](./tutorial-end-to-end.md). Este tutorial se basa en este [proyecto de ejemplo completo de Azure Digital Twins escrito en C#](/samples/azure-samples/digital-twins-samples/digital-twins-samples). Usará el proyecto **DeviceSimulator** en ese repositorio.

En este tutorial integral, va a completar los siguientes pasos:
1. [Registro del dispositivo simulado en el centro de IoT](./tutorial-end-to-end.md#register-the-simulated-device-with-iot-hub)
2. [Configuración y ejecución de la simulación](./tutorial-end-to-end.md#configure-and-run-the-simulation)

## <a name="validate-your-results"></a>Validación de los resultados

Mientras se ejecuta el simulador de dispositivos anterior, cambiará el valor de temperatura del gemelo digital. En la CLI de Azure, ejecute el siguiente comando para ver el valor de temperatura.

```azurecli-interactive
az dt twin query --query-command "select * from digitaltwins" --dt-name <Digital-Twins-instance-name>
```

La salida debe contener un valor de temperatura similar al siguiente:

```json
{
  "result": [
    {
      "$dtId": "thermostat67",
      "$etag": "W/\"dbf2fea8-d3f7-42d0-8037-83730dc2afc5\"",
      "$metadata": {
        "$model": "dtmi:contosocom:DigitalTwins:Thermostat;1",
        "Temperature": {
          "lastUpdateTime": "2021-06-03T17:05:52.0062638Z"
        }
      },
      "Temperature": 70.20518558807913
    }
  ]
}
```

Para ver el cambio de valor, ejecute repetidamente el comando de consulta anterior.

## <a name="next-steps"></a>Pasos siguientes

Obtenga información sobre la entrada y salida de datos con Azure Digital Twins:
* [Entrada y salida de datos](concepts-data-ingress-egress.md)
