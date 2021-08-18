---
title: Incorporación de identificadores de correlación a mensajes de IoT con seguimiento distribuido (versión preliminar)
description: Aprenda a usar la funcionalidad de seguimiento distribuido para realizar un seguimiento de los mensajes de IoT en todos los servicios de Azure que usa la solución.
author: jlian
manager: briz
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 02/06/2019
ms.author: jlian
ms.custom:
- amqp
- mqtt
- fasttrack-edit
- iot
ms.openlocfilehash: c24b7ee5154ec7f79bbbf19ffc6c59b8bf6221b3
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/13/2021
ms.locfileid: "121743319"
---
# <a name="trace-azure-iot-device-to-cloud-messages-with-distributed-tracing-preview"></a>Seguimiento de mensajes del dispositivo a la nube de Azure IoT con seguimiento distribuido (versión preliminar)

Microsoft Azure IoT Hub actualmente admite el seguimiento distribuido como un [característica en vista previa (GB)](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

IoT Hub es uno de los primeros servicios de Azure que admite el seguimiento distribuido. A medida que más servicios de Azure admitan el seguimiento distribuido, podrá rastrear los mensajes de IoT a través de los servicios de Azure involucrados en su solución. Para información sobre el seguimiento distribuido, consulte [¿Qué es el seguimiento distribuido?](../azure-monitor/app/distributed-tracing.md).

La habilitación del seguimiento distribuido para IoT Hub le ofrece la capacidad de:

- Supervisar de forma precisa el flujo de cada mensaje a través de IoT Hub mediante [contexto de seguimiento](https://github.com/w3c/trace-context). Este contexto de seguimiento incluye id. de correlación que permiten correlacionar eventos de un componente con eventos de otro componente. Se puede aplicar a un subconjunto de mensajes de dispositivo de IoT o a todos esos mensajes mediante [dispositivo gemelo](iot-hub-devguide-device-twins.md).
- Registrar automáticamente el contexto de seguimiento en [registros de Azure Monitor](monitor-iot-hub.md).
- Medir y comprender la latencia y el flujo de mensajes desde los dispositivos a IoT Hub y los puntos de conexión de enrutamiento.
- Comenzar a considerar cómo desea implementar el seguimiento distribuido para los servicios que no son de Azure en su solución de IoT.

En este artículo, se usa el [SDK de dispositivo de IoT de Azure para C](iot-hub-device-sdk-c-intro.md) con seguimiento distribuido. La compatibilidad con el seguimiento distribuido aún está en curso para lo demás SDK.

## <a name="prerequisites"></a>Prerrequisitos

- La versión preliminar del seguimiento distribuido actualmente solo se admite para instancias de IoT hub creadas en las siguientes regiones:

  - **Norte de Europa**
  - **Sudeste de Asia**
  - **Oeste de EE. UU. 2**

- En este artículo se da por supuesto que está familiarizado con el envío de mensajes de telemetría a IoT hub. Asegúrese de que ha completado el [Inicio rápido: Envío de telemetría desde un dispositivo a un centro de IoT y su lectura con una aplicación de back-end (C)](../iot-develop/quickstart-send-telemetry-iot-hub.md?pivots=programming-language-ansi-c).

- Registre un dispositivo con IoT hub (los pasos están disponibles en cada inicio rápido) y anote la cadena de conexión.

- Instale la última versión de [Git](https://git-scm.com/download/).

## <a name="configure-iot-hub"></a>Configuración de IoT Hub

En esta sección, configurará una instancia de IoT Hub para registrar atributos de seguimiento distribuido (id. de correlación y marcas de tiempo).

1. Vaya a IoT Hub en [Azure Portal](https://portal.azure.com/).

1. En el panel izquierdo de IoT Hub, desplácese hacia abajo hasta la sección **Supervisión** y haga clic en **Configuración de diagnóstico**.

1. Haga clic en **Agregar configuración de diagnóstico**.

1. En el campo **Nombre**, escriba un nombre para una nueva configuración de diagnóstico. Por ejemplo, **DistributedTracingSettings**.

1. Elija una o varias de las opciones siguientes que determinan dónde se enviará el registro:

    - **Archivar en una cuenta de almacenamiento**: configure una cuenta de almacenamiento para que contenga la información de registro.
    - **Transmisión a un centro de eventos**: configure un centro de eventos para que contenga la información de registro.
    - **Enviar a Log Analytics**: Configure un área de trabajo de Log Analytics para que contenga la información de registro.

1. En la sección **Registro**, seleccione las operaciones para las que desea la información de registro.

    No olvide incluir **DistributedTracing** y configure un valor para **Retención** para establecer el número de días que desea conservar el registro. La retención de registros afecta a los costos de almacenamiento.

    ![Captura de pantalla que muestra donde está la categoría DistributedTracing para la configuración de diagnóstico de IoT](./media/iot-hub-distributed-tracing/diag-logs.png)

1. Haga clic en **Guardar** para la nueva configuración.

1. (Opcional) Para ver el flujo de los mensajes a distintos lugares, configure [reglas de enrutamiento para al menos dos puntos de conexión diferentes](iot-hub-devguide-messages-d2c.md).

Una vez activado el registro, IoT Hub graba un registro cuando un mensaje que contiene las propiedades de seguimiento válidas se encuentra en cualquiera de las situaciones siguientes:

- Los mensajes llegan a la puerta de enlace de IoT Hub.
- IoT Hub procesa el mensaje.
- El mensaje se enruta a puntos de conexión personalizados. El enrutamiento debe estar habilitado.

Para obtener más información sobre estos registros y sus esquemas, consulte [Supervisión de IoT Hub](monitor-iot-hub.md) y [Seguimiento distribuido en registros de recursos de IoT Hub](monitor-iot-hub-reference.md#distributed-tracing-preview).

## <a name="set-up-device"></a>Configuración de un dispositivo

En esta sección, preparará un entorno de desarrollo para usarse con el [SDK de Azure IoT para C](https://github.com/Azure/azure-iot-sdk-c). Después, modificará uno de los muestreos para habilitar el seguimiento distribuido en mensajes de telemetría del dispositivo.

Estas instrucciones sirven para generar el muestreo en Windows. Para otros entornos, consulte [Compile the C SDK](https://github.com/Azure/azure-iot-sdk-c/blob/master/iothub_client/readme.md#compile) (Compilar el SDK de C) o [Prepackaged C SDK for Platform Specific Development](https://github.com/Azure/azure-iot-sdk-c/blob/master/iothub_client/readme.md#prepackaged-c-sdk-for-platform-specific-development) (SDK de C previamente empaquetado para el desarrollo específico de plataforma).

### <a name="clone-the-source-code-and-initialize"></a>Clonación del código fuente e inicialización

1. Instale la [carga de trabajo "desarrollo para el escritorio con C++"](/cpp/build/vscpp-step-0-installation?view=vs-2019&preserve-view=true) para Visual Studio 2019. También se admiten Visual Studio 2017 y 2015.

1. Instale [CMake](https://cmake.org/). Asegúrese de que está en su `PATH` escribiendo `cmake -version` desde un símbolo del sistema.

1. Abra un símbolo del sistema o el shell de Bash de Git. Ejecute los siguientes comandos para clonar la versión más reciente del repositorio de GitHub del [SDK de Azure IoT para C](https://github.com/Azure/azure-iot-sdk-c):

    ```cmd
    git clone -b public-preview https://github.com/Azure/azure-iot-sdk-c.git
    cd azure-iot-sdk-c
    git submodule update --init
    ```

    Esta operación puede tardar varios minutos en completarse.

1. Cree un subdirectorio `cmake` en el directorio raíz del repositorio de Git y vaya a esa carpeta. Ejecute los siguientes comandos desde el directorio `azure-iot-sdk-c`:

    ```cmd
    mkdir cmake
    cd cmake
    cmake ..
    ```

    Si `cmake` no puede encontrar el compilador de C++, es posible que obtenga errores de compilación durante la ejecución del comando anterior. Si sucede, intente ejecutar este comando en el [símbolo del sistema de Visual Studio](/dotnet/framework/tools/developer-command-prompt-for-vs). 

    Una vez realizada la compilación, las últimas líneas de salida tendrán un aspecto similar al siguiente:

    ```cmd
    $ cmake ..
    -- Building for: Visual Studio 15 2017
    -- Selecting Windows SDK version 10.0.16299.0 to target Windows 10.0.17134.
    -- The C compiler identification is MSVC 19.12.25835.0
    -- The CXX compiler identification is MSVC 19.12.25835.0

    ...

    -- Configuring done
    -- Generating done
    -- Build files have been written to: E:/IoT Testing/azure-iot-sdk-c/cmake
    ```

### <a name="edit-the-send-telemetry-sample-to-enable-distributed-tracing"></a>Edición del muestreo de telemetría de envío para habilitar el seguimiento distribuido

> [!div class="button"]
> <a href="https://github.com/Azure-Samples/azure-iot-distributed-tracing-sample/blob/master/iothub_ll_telemetry_sample-c/iothub_ll_telemetry_sample.c" target="_blank">Obtención del ejemplo de GitHub</a>

1. Utilice un editor para abrir el archivo de origen `azure-iot-sdk-c/iothub_client/samples/iothub_ll_telemetry_sample/iothub_ll_telemetry_sample.c`.

1. Busque la declaración de la constante `connectionString`:

    [!code-c[](~/samples-iot-distributed-tracing/iothub_ll_telemetry_sample-c/iothub_ll_telemetry_sample.c?name=snippet_config&highlight=2)]

    Reemplace el valor de la constante `connectionString` por la cadena de conexión de dispositivo que anotó en la sección [Registrar un dispositivo](../iot-develop/quickstart-send-telemetry-iot-hub.md?pivots=programming-language-ansi-c#register-a-device) de [Inicio rápido: Envío de telemetría desde un dispositivo a un centro de IoT y su lectura con una aplicación de back-end (C)](../iot-develop/quickstart-send-telemetry-iot-hub.md?pivots=programming-language-ansi-c).

1. Cambie la declaración define `MESSAGE_COUNT` a `5000`:

    [!code-c[](~/samples-iot-distributed-tracing/iothub_ll_telemetry_sample-c/iothub_ll_telemetry_sample.c?name=snippet_config&highlight=3)]

1. Busque la línea de código que llama a `IoTHubDeviceClient_LL_SetConnectionStatusCallback` para registrar una función de devolución de llamada de estado de conexión antes del bucle de mensajes de envío. Agregue el código en esa línea tal y como se muestra a continuación para llamar a `IoTHubDeviceClient_LL_EnablePolicyConfiguration` habilitando el seguimiento distribuido para el dispositivo:

    [!code-c[](~/samples-iot-distributed-tracing/iothub_ll_telemetry_sample-c/iothub_ll_telemetry_sample.c?name=snippet_tracing&highlight=5)]

    La función `IoTHubDeviceClient_LL_EnablePolicyConfiguration` habilita directivas para características específicas de IoT Hub que se configuran a través [dispositivos gemelos](./iot-hub-devguide-device-twins.md). Cundo `POLICY_CONFIGURATION_DISTRIBUTED_TRACING` se haya habilitado con la línea de código anterior, el comportamiento del seguimiento del dispositivo reflejará los cambios de seguimiento distribuido realizados en el dispositivo gemelo.

1. Para que la aplicación de ejemplo siga ejecutándose sin usar toda la cuota, agregue un retraso de un segundo al final del bucle de mensaje de envío:

    [!code-c[](~/samples-iot-distributed-tracing/iothub_ll_telemetry_sample-c/iothub_ll_telemetry_sample.c?name=snippet_sleep&highlight=8)]

### <a name="compile-and-run"></a>Compilación y ejecución

1. Vaya al directorio del proyecto *iothub_ll_telemetry_sample* desde el directorio de CMake (`azure-iot-sdk-c/cmake`) que creó anteriormente y compile el ejemplo:

    ```cmd
    cd iothub_client/samples/iothub_ll_telemetry_sample
    cmake --build . --target iothub_ll_telemetry_sample --config Debug
    ```

1. Ejecute la aplicación. El dispositivo envía datos de telemetría compatibles con el seguimiento distribuido.

    ```cmd
    Debug/iothub_ll_telemetry_sample.exe
    ```

1. Mantenga la aplicación en ejecución. Opcionalmente, observe el mensaje que se envía a IoT Hub echando un vistazo a la ventana de la consola.

<!-- For a client app that can receive sampling decisions from the cloud, check out [this sample](https://aka.ms/iottracingCsample).  -->

### <a name="workaround-for-third-party-clients"></a>Solución alternativa para clientes de terceros

**No resulta sencillo** obtener la versión preliminar de la característica de seguimiento distribuido sin usar el SDK de C. Por lo tanto, no se recomienda este enfoque.

En primer lugar, debe implementar todos los primitivos de protocolo de IoT Hub en los mensajes; para ello, siga la guía de desarrollo [Creación y lectura de mensajes de IoT Hub](iot-hub-devguide-messages-construct.md). A continuación, edite las propiedades del protocolo en los mensajes de MQTT y AMQP para agregar `tracestate` como **propiedad del sistema**. Concretamente, puede:

* En MQTT, agregar `%24.tracestate=timestamp%3d1539243209` al tema del mensaje, donde `1539243209` debe reemplazarse por la hora de creación del mensaje en el formato de marca de tiempo de Unix. A modo de ejemplo, consulte la implementación [en el SDK de C](https://github.com/Azure/azure-iot-sdk-c/blob/6633c5b18710febf1af7713cf1a336fd38f623ed/iothub_client/src/iothubtransport_mqtt_common.c#L761).
* En AMQP, agregar `key("tracestate")` y `value("timestamp=1539243209")` como anotación del mensaje. Consulte [aquí](https://github.com/Azure/azure-iot-sdk-c/blob/6633c5b18710febf1af7713cf1a336fd38f623ed/iothub_client/src/uamqp_messaging.c#L527) una implementación de referencia.

Para controlar el porcentaje de los mensajes que contienen esta propiedad, implemente la lógica para que escuche eventos iniciados en la nube, como las actualizaciones gemelas.

## <a name="update-sampling-options"></a>Actualización de opciones de ejemplo 

Para cambiar el porcentaje de mensajes que se van a seguir desde la nube, debe actualizar el dispositivo gemelo. Puede lograr esto de varias maneras, lo que incluye al editor JSON del portal y al SDK del servicio IoT Hub. En las subsecciones siguientes se proporcionan ejemplos.

### <a name="update-using-the-portal"></a>Actualización mediante el portal

1. Vaya a IoT Hub en [Azure Portal](https://portal.azure.com/) y haga clic en **Dispositivos de IoT**.

1. Haga clic en su dispositivo.

1. Busque **Enable distributed tracing (preview)** (Habilitación del seguimiento distribuido [versión preliminar]) y seleccione **Habilitar**.

    ![Habilitación del seguimiento distribuido en Azure Portal](./media/iot-hub-distributed-tracing/azure-portal.png)

1. Elija un valor en **Velocidad de muestreo** entre 0 % y 100 %.

1. Haga clic en **Save**(Guardar).

1. Espere unos segundos y presione **Actualizar**. Si el dispositivo lo reconoce correctamente, aparece un icono de sincronización con una marca de verificación.

1. Vuelva a la ventana de la consola de la aplicación de mensajes de telemetría. Verá que los mensajes se envían con `tracestate` en las propiedades de la aplicación.

    ![Estado del seguimiento](./media/iot-hub-distributed-tracing/MicrosoftTeams-image.png)

1. (Opcional) Cambie la velocidad de muestreo a un valor diferente y observe el cambio en la frecuencia con la que los mensajes incluyen `tracestate` en las propiedades de la aplicación.

### <a name="update-using-azure-iot-hub-for-vs-code"></a>Actualización mediante Azure IoT Hub para VS Code

1. Instale VS Code y luego instale la última versión de Azure IoT Hub para VS Code desde [aquí](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools).

1. Abra VS Code y [configure la cadena de conexión de IoT Hub](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-toolkit#user-content-prerequisites).

1. Expanda el dispositivo y busque **Distributed Tracing Setting (Preview)** (Configuración del seguimiento distribuido [versión preliminar]). Aquí, haga clic en **Update Distributed Tracing Setting (Preview)** (Actualizar configuración del seguimiento distribuido [versión preliminar]) del nodo secundario.

    ![Habilitación del seguimiento distribuido en la extensión Azure IoT Hub](./media/iot-hub-distributed-tracing/update-distributed-tracing-setting-1.png)

1. En la ventana emergente, seleccione **Habilitar** y presione Entrar para confirmar 100 como la velocidad de muestreo.

    ![Actualización del modo de muestreo](./media/iot-hub-distributed-tracing/update-distributed-tracing-setting-2.png)

    ![Actualización de la velocidad de muestreo](./media/iot-hub-distributed-tracing/update-distributed-tracing-setting-3.png)

### <a name="bulk-update-for-multiple-devices"></a>Actualización masiva para varios dispositivos

Para actualizar la configuración de muestreo del seguimiento distribuido para varios dispositivos, use la [configuración automática de dispositivos](./iot-hub-automatic-device-management.md). Asegúrese de que seguir este esquema gemelo:

```json
{
    "properties": {
        "desired": {
            "azureiot*com^dtracing^1": {
                "sampling_mode": 1,
                "sampling_rate": 100
            }
        }
    }
}
```

| Nombre del elemento | Obligatorio | Tipo | Descripción |
|-----------------|----------|---------|-----------------------------------------------------|
| `sampling_mode` | Sí | Entero | Actualmente se admiten dos valores de modo para activar o desactivar el muestreo. `1` significa activado y `2` desactivado. |
| `sampling_rate` | Sí | Entero | Este valor es un porcentaje. Solo se permiten valores de `0` a `100` (inclusive).  |

## <a name="query-and-visualize"></a>Consultar y visualizar

Para ver todos los seguimientos registrados por una instancia de IoT Hub, consulte el almacén de registro que seleccionó en la configuración de diagnóstico. En esta sección le guía a través de un par de opciones diferentes.

### <a name="query-using-log-analytics"></a>Consulta mediante Log Analytics

Si ha configurado [Log Analytics con registros de recursos](../azure-monitor/essentials/resource-logs.md#send-to-azure-storage), consulte mediante la búsqueda de registros en la categoría `DistributedTracing`. Por ejemplo, esta consulta muestra todos los seguimientos registrados:

```Kusto
// All distributed traces 
AzureDiagnostics 
| where Category == "DistributedTracing" 
| project TimeGenerated, Category, OperationName, Level, CorrelationId, DurationMs, properties_s 
| order by TimeGenerated asc  
```

Registros de ejemplo como se muestran en Log Analytics:

| TimeGenerated | OperationName | Category | Nivel | CorrelationId | DurationMs | Propiedades |
|--------------------------|---------------|--------------------|---------------|---------------------------------------------------------|------------|------------------------------------------------------------------------------------------------------------------------------------------|
| 2018-02-22T03:28:28.633Z | DiagnosticIoTHubD2C | DistributedTracing | Informativo | 00-8cd869a412459a25f5b4f31311223344-0144d2590aacd909-01 |  | {"deviceId":"AZ3166","messageSize":"96","callerLocalTimeUtc":"2018-02-22T03:27:28.633Z","calleeLocalTimeUtc":"2018-02-22T03:27:28.687Z"} |
| 2018-02-22T03:28:38.633Z | DiagnosticIoTHubIngress | DistributedTracing | Informativo | 00-8cd869a412459a25f5b4f31311223344-349810a9bbd28730-01 | 20 | {"isRoutingEnabled":"false","parentSpanId":"0144d2590aacd909"} |
| 2018-02-22T03:28:48.633Z | DiagnosticIoTHubEgress | DistributedTracing | Informativo | 00-8cd869a412459a25f5b4f31311223344-349810a9bbd28730-01 | 23 | {"endpointType":"EventHub","endpointName":"myEventHub", "parentSpanId":"0144d2590aacd909"} |

Para entender los diferentes tipos de registros, consulte [Registros de seguimiento distribuido de Azure IoT Hub](monitor-iot-hub-reference.md#distributed-tracing-preview).

### <a name="application-map"></a>Mapa de aplicación

Para visualizar el flujo de mensajes de IoT, configure la aplicación de ejemplo Application Map. La aplicación de ejemplo envía los registros de seguimiento distribuidos a [Application Map](../azure-monitor/app/app-map.md) mediante una función de Azure y un centro de eventos.

> [!div class="button"]
> <a href="https://github.com/Azure-Samples/e2e-diagnostic-provision-cli" target="_blank">Obtención del ejemplo de GitHub</a>

La imagen siguiente muestra el seguimiento distribuido en App Map con tres puntos de conexión de enrutamiento:

![Seguimiento distribuido de IoT en App Map](./media/iot-hub-distributed-tracing/app-map.png)

## <a name="understand-azure-iot-distributed-tracing"></a>Descripción del seguimiento distribuido de Azure IoT

### <a name="context"></a>Context

Muchas soluciones de IoT, incluida nuestra propia [arquitectura de referencia](/azure/architecture/reference-architectures/iot) (solo en inglés), suelen seguir una variante de la [arquitectura de microservicios](/azure/architecture/microservices/). A medida que una solución de IoT se vuelve más compleja, se termina usando una docena o más de microservicios. Estos microservicios pueden o no ser de Azure. La identificación de dónde se quitan o ralentizan los mensajes puede ser complicado. Por ejemplo, tiene una solución de IoT que usa cinco servicios diferentes de Azure y 1500 dispositivos activos. Cada dispositivo envía diez mensajes de dispositivo a la nube por segundo (lo que hace un total de 15 000 mensajes por segundo), pero observa que la aplicación web solo ve 10 000 mensajes por segundo. ¿Dónde está el problema? ¿Cómo encontrar la causa?

### <a name="distributed-tracing-pattern-in-microservice-architecture"></a>Patrón de seguimiento distribuido en arquitectura de microservicio

Para reconstruir el flujo de un mensaje de IoT en los diferentes servicios, cada servicio debe propagar un *id. de correlación* que identifica inequívocamente el mensaje. Una vez recopilados en un sistema centralizado, los id. de correlación permiten ver el flujo de mensajes. Este método se denomina [patrón de seguimiento distribuido](/azure/architecture/microservices/logging-monitoring#distributed-tracing).

Para admitir una adopción más amplia para el seguimiento distribuido, Microsoft está contribuyendo a la [propuesta estándar de W3C para el seguimiento distribuido](https://w3c.github.io/trace-context/).

### <a name="iot-hub-support"></a>Compatibilidad con IoT Hub

Una vez habilitada, la compatibilidad con el seguimiento distribuido para IoT Hub seguirá este flujo:

1. Se genera un mensaje en el dispositivo de IoT.
1. El dispositivo de IoT decide (con la ayuda de la nube) que este mensaje se debería asignar con un contexto de seguimiento.
1. El SDK agrega `tracestate` a la propiedad del mensaje, que contiene la marca de tiempo de creación del mensaje.
1. El dispositivo de IoT envía el mensaje a IoT Hub.
1. El mensaje llega a la puerta de enlace de IoT Hub.
1. IoT Hub busca `tracestate` en las propiedades del mensaje y comprobaciones si tiene el formato correcto.
1. En ese caso, IoT Hub genera un elemento `trace-id` único global para el mensaje y un elemento `span-id` para el "salto" y los anota en los [registros de seguimiento distribuido de IoT Hub](monitor-iot-hub-reference.md#distributed-tracing-preview) en la operación `DiagnosticIoTHubD2C`.
1. Una vez finalizado el procesamiento del mensaje, IoT Hub genera otro elemento `span-id` y lo registra junto con el elemento `trace-id` existente en la operación `DiagnosticIoTHubIngress`.
1. Si el enrutamiento está habilitado para el mensaje, IoT Hub lo escribe en el punto de conexión personalizado y registra otro `span-id` con el mismo `trace-id` bajo la categoría `DiagnosticIoTHubEgress`.
1. Los pasos anteriores se repiten para cada mensaje generado.

## <a name="public-preview-limits-and-considerations"></a>Consideraciones y límites de la versión preliminar pública

- La propuesta para el estándar de contexto de seguimiento de W3C es actualmente un borrador de trabajo.
- Actualmente, el único lenguaje de desarrollo que admite el SDK del cliente es C.
- La funcionalidad de nube a dispositivo gemelo no está disponible para el [nivel básico de IoT Hub](iot-hub-scaling.md#basic-and-standard-tiers). Sin embargo, IoT Hub seguirá registrando en Azure Monitor si ve un encabezado de contexto de seguimiento compuesto correctamente.
- Para garantizar un funcionamiento eficaz, IoT Hub impondrá una limitación en la tasa de registro que se puede producir como parte del seguimiento distribuido.

## <a name="next-steps"></a>Pasos siguientes

- Para obtener más información sobre el patrón general del seguimiento distribuido en microservicios, consulte [Microservice architecture pattern: distributed tracing](https://microservices.io/patterns/observability/distributed-tracing.html) (Patrón de arquitectura de microservicios: seguimiento distribuido).
- Para establecer la configuración para aplicar parámetros de seguimiento distribuido a un gran número de dispositivos, consulte [Configuración y supervisión de dispositivos IoT a escala mediante Azure Portal](./iot-hub-automatic-device-management.md).
- Para obtener más información sobre Azure Monitor, consulte [Introducción a Azure Monitor](../azure-monitor/overview.md).
- Para obtener más información sobre el uso de Azure Monitor con IoT HUb, consulte [Supervisión de IoT Hub](monitor-iot-hub.md).