---
title: 'Información sobre cómo el entorno de ejecución administra los dispositivos: Azure IoT Edge | Microsoft Docs'
description: Obtenga información sobre cómo el entorno de ejecución de Azure IoT Edge administra los módulos, la seguridad, la comunicación y los informes sobre los dispositivos.
author: kgremban
ms.author: kgremban
ms.date: 11/10/2020
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.custom: amqp, mqtt, devx-track-csharp
ms.openlocfilehash: 30690cb90fcaa57f955b531e1902333dc43976f5
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/13/2021
ms.locfileid: "121740666"
---
# <a name="understand-the-azure-iot-edge-runtime-and-its-architecture"></a>Información del entorno de ejecución de Azure IoT Edge y su arquitectura

[!INCLUDE [iot-edge-version-201806-or-202011](../../includes/iot-edge-version-201806-or-202011.md)]

El runtime de IoT Edge es una colección de programas que convierten un dispositivo en un dispositivo IoT Edge. Colectivamente, los componentes del entorno de ejecución de Azure IoT Edge permiten que los dispositivos IoT Edge reciban el código para ejecutarse en el perímetro y comunicar los resultados.

El entorno de ejecución de Azure IoT Edge es el responsable de las siguientes funciones en los dispositivos IoT Edge:

* Instalación y actualización de las cargas de trabajo en el dispositivo.

* Mantenimiento de los estándares de seguridad de Azure IoT Edge en el dispositivo.

* Garantía de que los [módulos de IoT Edge](iot-edge-modules.md) están siempre en ejecución.

* Notificación del mantenimiento del módulo a la nube para la supervisión remota.

* Administre la comunicación entre los dispositivos de bajada y los dispositivos IoT Edge.

* Administrar la comunicación entre módulos en un dispositivo IoT Edge.

* Administrar la comunicación entre el dispositivo IoT Edge y la nube.
<!-- 1.2.0 -->
::: moniker range=">=iotedge-2020-11"
* Administrar la comunicación entre dispositivos IoT Edge.
::: moniker-end

![El entorno de ejecución comunica la información y el estado del módulo a IoT Hub](./media/iot-edge-runtime/Pipeline.png)

Las responsabilidades del entorno de ejecución de IoT Edge se dividen en dos categorías: comunicación y administración de los módulos. Estos dos roles los realizan dos componentes que forman parte del runtime de IoT Edge. El *agente de IoT Edge* implementa y supervisa los módulos, mientras que el *centro de IoT Edge* se encarga de la comunicación.

El agente y el centro de IoT Edge son módulos, como cualquier otro que se ejecuta en un dispositivo IoT Edge. A veces se denominan *módulos del entorno de ejecución*.

## <a name="iot-edge-agent"></a>Agente de IoT Edge

El agente de IoT Edge es uno de los dos módulos que componen el entorno de ejecución de Azure IoT Edge. Es responsable de crear instancias de los módulos, lo que garantiza que continúen ejecutándose y notificando el estado de los módulos a IoT Hub. Estos datos de configuración se escriben como una propiedad del módulo gemelo del agente de IoT Edge.

El [demonio de seguridad de IoT Edge](iot-edge-security-manager.md) inicia el agente de IoT Edge durante el inicio del dispositivo. El agente recupera su módulo gemelo de IoT Hub e inspecciona el manifiesto de implementación. El manifiesto de implementación es un archivo JSON que declara al módulo que debe iniciarse.

Cada elemento del manifiesto de implementación contiene información específica de un módulo y el agente de IoT Edge lo usa para controlar el ciclo de vida del módulo. Para obtener más información acerca de todas las propiedades que usa el agente de IoT Edge para controlar los módulos, lea acerca de las [propiedades del agente de IoT Edge y los módulos gemelos del centro de IoT Edge](module-edgeagent-edgehub.md).

El agente de IoT Edge envía la respuesta de entorno de ejecución a IoT Hub. A continuación, se ofrece una lista de las posibles respuestas:
  
* 200 - CORRECTO
* 400 - La configuración de implementación tiene un formato incorrecto o no es válida.
* 417 - El dispositivo no tiene un conjunto de configuración de implementación.
* 412 - La versión del esquema de la configuración de implementación no es válida.
* 406 - El dispositivo de IoT Edge está sin conexión o no envía informes de estado.
* 500 - Error en el entorno en tiempo de ejecución de Azure IoT Edge.

Para más información sobre la creación de manifiestos de implementación, consulte [Obtenga información sobre cómo implementar módulos y establecer rutas en IoT Edge](module-composition.md).

### <a name="security"></a>Seguridad

El agente de IoT Edge desempeña un papel fundamental en la seguridad de un dispositivo IoT Edge. Por ejemplo, realiza acciones como comprobar la imagen de un módulo antes de iniciarlo.

Para más información acerca del marco de seguridad de Azure IoT Edge, consulte [Administrador de seguridad de IoT Edge](iot-edge-security-manager.md).

## <a name="iot-edge-hub"></a>Centro de IoT Edge

El centro de IoT Edge es el otro módulo que constituye el entorno de ejecución de Azure IoT Edge. Actúa como un proxy local de IoT Hub exponiendo los mismos puntos de conexión de protocolo que IoT Hub. Esta coherencia significa que los clientes pueden conectarse a la instancia de IoT Edge en tiempo de ejecución de la misma forma que lo harían a IoT Hub.

El centro de IoT Edge no es una versión completa del centro de IoT Hub que se ejecuta localmente. El centro de IoT Edge delega silenciosamente algunas tareas en IoT Hub. Por ejemplo, el centro de IoT Edge descarga automáticamente la información de autorización de IoT Hub durante la primera conexión de un dispositivo para habilitarlo. Una vez establecida la primera conexión, el centro de IoT Edge almacena la información de autorización en caché localmente. Las conexiones futuras del dispositivo se autorizan sin tener que descargar de nuevo la información de autorización de la nube.

### <a name="cloud-communication"></a>Comunicación en la nube

Para reducir el ancho de banda que usa la solución IoT Edge, el centro de IoT Edge optimiza el número real de conexiones a la nube. El centro de IoT Edge toma las conexiones lógicas de módulos o dispositivos de bajada y las combina para crear una sola conexión física a la nube. Los detalles de este proceso son transparentes para el resto de la solución. Los clientes creen que tienen su propia conexión a la nube, aunque todos los datos van a enviarse a través de la misma. El centro de IoT Edge puede usar el protocolo AMQP o MQTT para comunicarse con la nube, independientemente de los protocolos que usen los dispositivos de nivel inferior. No obstante, el centro de IoT Edge actualmente solo admite la combinación de conexiones lógicas en una única conexión física mediante el uso de AMQP como protocolo de nivel superior y sus capacidades de multiplexación. AMQP es el protocolo de nivel superior predeterminado.

![El centro de IoT Edge es una puerta de enlace entre los dispositivos físicos e IoT Hub](./media/iot-edge-runtime/gateway-communication.png)

El centro de IoT Edge puede determinar si está conectado a IoT Hub. Si se pierde la conexión, el centro de IoT Edge guarda los mensajes o las actualizaciones gemelas localmente. Una vez que se vuelva a establecer una conexión, se sincronizan todos los datos. La ubicación que usa esta caché temporal viene determinada por una propiedad del módulo gemelo del centro de IoT Edge. El tamaño de la caché no está limitado y aumentará siempre y cuando el dispositivo tenga capacidad de almacenamiento.  Para más información, consulte la [Funcionalidades sin conexión](offline-capabilities.md).

<!-- <1.1> -->
::: moniker range="iotedge-2018-06"

### <a name="module-communication"></a>Comunicación entre módulos

El centro de IoT Edge facilita la comunicación entre módulos. Al usar el centro de IoT Edge como un agente de mensajes, los módulos seguirán siendo independientes entre sí. Los módulos solo necesitan especificar las entradas en las que aceptan mensajes y las salidas en las que los escriben. Un desarrollador de soluciones puede unir estas entradas y salidas para que los módulos procesen los datos en el orden específico de la solución.

![El centro de IoT Edge facilita la comunicación entre módulos.](./media/iot-edge-runtime/module-endpoints.png)

Para enviar datos al centro de IoT Edge, un módulo llama al método SendEventAsync. El primer argumento especifica en qué salida enviará el mensaje. El siguiente seudocódigo envía un mensaje a la salida **output1**:

   ```csharp
   ModuleClient client = await ModuleClient.CreateFromEnvironmentAsync(transportSettings);
   await client.OpenAsync();
   await client.SendEventAsync("output1", message);
   ```

Para recibir un mensaje, registre una devolución de llamada que procese los mensajes procedentes de una entrada específica. El siguiente seudocódigo registra la función messageProcessor, que se usará para procesar todos los mensajes recibidos en la entrada **input1**:

   ```csharp
   await client.SetInputMessageHandlerAsync("input1", messageProcessor, userContext);
   ```

Para más información sobre la clase ModuleClient y sus métodos de comunicación, consulte la referencia de API de su lenguaje preferido para el SDK: [C#](/dotnet/api/microsoft.azure.devices.client.moduleclient), [C](/azure/iot-hub/iot-c-sdk-ref/iothub-module-client-h), [Python](/python/api/azure-iot-device/azure.iot.device.iothubmoduleclient), [Java](/java/api/com.microsoft.azure.sdk.iot.device.moduleclient) o [Node.js](/javascript/api/azure-iot-device/moduleclient).

El desarrollador de soluciones es responsable de especificar las reglas que determinan cómo el centro de IoT Edge pasa los mensajes entre los módulos. Las reglas de enrutamiento se definen en la nube y se envían al centro de IoT Edge de su módulo gemelo. Se utiliza la misma sintaxis de las rutas de IoT Hub para definir las rutas entre módulos de Azure IoT Edge. Para más información, consulte [Aprenda a implementar módulos y establecer rutas en IoT Edge](module-composition.md).

![Las rutas entre los módulos pasan por el centro de IoT Edge](./media/iot-edge-runtime/module-endpoints-routing.png)
::: moniker-end

<!-- <1.2> -->
::: moniker range=">=iotedge-2020-11"

### <a name="local-communication"></a>Comunicación local

El centro de IoT Edge facilita la comunicación local. Permite la comunicación de dispositivos a módulos, entre módulos y entre dispositivos mediante la administración de mensajes para mantener a los dispositivos y módulos independientes entre sí.

>[!NOTE]
> La característica del agente MQTT se encuentra en versión preliminar pública con IoT Edge versión 1.2. Debe habilitarse explícitamente.

El centro de IoT Edge admite dos mecanismos de administración:

1. Las [características de enrutamiento de mensajes compatibles con IoT Hub](../iot-hub/iot-hub-devguide-messages-d2c.md) y,
2. Un agente de MQTT de uso general que cumple el [estándar de MQTT versión 3.1.1](https://docs.oasis-open.org/mqtt/mqtt/v3.1.1/os/mqtt-v3.1.1-os.html).

#### <a name="using-routing"></a>Uso del enrutamiento

El primer mecanismo de administración usa las mismas características de enrutamiento que IoT Hub para especificar cómo se pasan los mensajes entre dispositivos o módulos. En primer lugar, los módulos o dispositivos especifican las entradas en las que aceptan mensajes y las salidas en las que los escriben. Después, un desarrollador de soluciones puede enrutar los mensajes entre un origen (por ejemplo, las salidas) y un destino (por ejemplo, las entradas) con posibles filtros.

![Las rutas entre los módulos pasan por el centro de IoT Edge](./media/iot-edge-runtime/module-endpoints-routing.png)

Los dispositivos o módulos creados con los SDK de dispositivo IoT de Azure pueden usar el enrutamiento mediante el protocolo AMQP o MQTT. Se admiten todos los elementos primitivos de IoT Hub (por ejemplo, la telemetría, los métodos directos, C2D, los gemelos), aunque no se admite la comunicación a través de temas definidos por el usuario.

Para más información sobre las rutas, consulte [Obtenga información sobre cómo implementar módulos y establecer rutas en IoT Edge](module-composition.md).

#### <a name="using-the-mqtt-broker"></a>Uso del agente de MQTT

El segundo mecanismo de administración está basado en un agente de MQTT estándar. MQTT es un protocolo ligero de transferencia de mensajes que garantiza un rendimiento óptimo en los dispositivos con recursos restringidos y es un estándar popular de publicación y suscripción. Los dispositivos o módulos se suscriben a temas para recibir los mensajes que publican otros dispositivos o módulos. El centro de IoT Edge implementa su propio agente de MQTT que sigue las [especificaciones de la versión 3.1.1 de MQTT](https://docs.oasis-open.org/mqtt/mqtt/v3.1.1/os/mqtt-v3.1.1-os.html).

El agente de MQTT habilita dos patrones de comunicación adicionales en comparación con el enrutamiento: difusión local y comunicación punto a punto. La difusión local resulta útil cuando un dispositivo o un módulo deben enviar alertas de manera local a varios dispositivos o módulos. La comunicación punto a punto permite que dos dispositivos IoT Edge o dos dispositivos IoT se comuniquen de manera local sin necesidad de pasar por la nube.

![Publicación y suscripción local con el centro de IoT Edge](./media/iot-edge-runtime/local-communnication-mqtt-broker.png)

Los dispositivos o módulos creados con los SDK de dispositivo IoT de Azure que se comunican a través del protocolo MQTT o de cualquier cliente MQTT de uso general pueden usar el agente MQTT. A excepción de C2D, se admiten todos los elementos primitivos de IoT Hub para mensajería, como la telemetría, los métodos directos y los gemelos. Se admiten los temas especiales de IoT Hub que se usan en los elementos primitivos de IoT Hub y, por lo tanto, también se admiten los temas definidos por el usuario.
Este tema puede ser un tema especial de IoT Hub o un tema definido por el usuario.

A diferencia de lo que sucede con el mecanismo de enrutamiento, el orden de los mensajes solo es según la mejor opción y no está garantizado, y el agente no admite el filtrado de mensajes. No obstante, la ausencia de estas características permite que el agente de MQTT sea más rápido que el enrutamiento.

Para obtener más información sobre el agente de MQTT, consulte [Publicación y suscripción con IoT Edge](how-to-publish-subscribe.md)

#### <a name="comparison-between-brokering-mechanisms"></a>Comparación entre mecanismos de administración

Estas son las características disponibles con cada mecanismo de administración:

|Características  | Enrutamiento  | Agente de MQTT  |
|---------|---------|---------|
|Telemetría de D2C    |     &#10004;    |         |
|Telemetría local     |     &#10004;    |    &#10004;     |
|DirectMethods     |    &#10004;     |    &#10004;     |
|Gemelo     |    &#10004;     |    &#10004;     |
|C2D para dispositivos     |   &#10004;      |         |
|Ordenación     |    &#10004;     |         |
|Filtrado     |     &#10004;    |         |
|Temas definidos por el usuario     |         |    &#10004;     |
|Dispositivo a dispositivo     |         |    &#10004;     |
|Difusión local     |         |    &#10004;     |
|Rendimiento     |         |    &#10004;     |

### <a name="connecting-to-the-iot-edge-hub"></a>Conexión al centro de IoT Edge

El centro de IoT Edge acepta conexiones de los clientes de dispositivos o módulos a través del protocolo MQTT o del protocolo AMQP.

>[!NOTE]
> El centro de IoT Edge admite clientes que se conectan mediante MQTT o AMQP. No admite los clientes que usan HTTP.

Cuando un cliente se conecta al centro de IoT Edge, ocurre lo siguiente:

1. Si se usa la Seguridad de la capa de transporte (TLS) (recomendado), se crea un canal TLS para establecer una comunicación cifrada entre el cliente y el centro de IoT Edge.
2. La información de autenticación se envía desde el cliente al centro de IoT Edge para identificarse.
3. El centro de IoT Edge autoriza o rechaza la conexión en función de la directiva de autorización.

#### <a name="secure-connections-tls"></a>Conexiones seguras (TLS)

De manera predeterminada, el centro de IoT Edge solo acepta conexiones protegidas con la seguridad de la capa de transporte (TLS); por ejemplo, conexiones cifradas que un tercero no puede descifrar.

Si un cliente se conecta con el puerto 8883 (MQTTS) o 5671 (AMQPS) al centro de IoT Edge, se debe crear un canal TLS. Durante el protocolo de enlace TLS, el centro de IoT Edge envía su cadena de certificados que el cliente debe validar. Para validar la cadena de certificados, el certificado raíz del centro de IoT Edge debe estar instalado como certificado de confianza en el cliente. Si el certificado raíz no es de confianza, el centro de IoT Edge rechazará la biblioteca cliente con un error de comprobación de certificados.

Los pasos que deben seguirse para instalar este certificado raíz del agente en los clientes del dispositivo se describen en la documentación de la [puerta de enlace transparente](how-to-create-transparent-gateway.md) y de la [preparación de un dispositivo de nivel inferior](how-to-connect-downstream-device.md#prepare-a-downstream-device). Los módulos pueden usar el mismo certificado raíz que el centro de IoT Edge si aprovechan la API de demonio de IoT Edge.

#### <a name="authentication"></a>Autenticación

El centro de IoT Edge solo acepta conexiones de los dispositivos o módulos que tienen una identidad de IoT Hub; por ejemplo, que se han registrado en IoT Hub y tienen uno de los tres métodos de autenticación de cliente que admite IoT Hub para demostrar su identidad: [Autenticación de clave simétrica](how-to-authenticate-downstream-device.md#symmetric-key-authentication), [autenticación autofirmada X.509](how-to-authenticate-downstream-device.md#x509-self-signed-authentication) o [autenticación firmada por entidad de certificación X.509](how-to-authenticate-downstream-device.md#x509-ca-signed-authentication).  El centro de IoT Edge puede comprobar estas identidades de IoT Hub de manera local para que las conexiones se sigan realizando sin conexión.

Notas:

* Actualmente, los módulos de IoT Edge solo admiten la autenticación de clave simétrica.
* El agente de MQTT del centro de IoT Edge no acepta los clientes de MQTT que solo tienen un nombre de usuario y contraseña locales; estos deben usar identidades de IoT Hub.

#### <a name="authorization"></a>Authorization

Una vez autenticado, el centro de IoT Edge tiene dos maneras de autorizar las conexiones de cliente:

* Comprobar que un cliente pertenece al conjunto de clientes de confianza definidos en IoT Hub. El conjunto de clientes de confianza se especifica mediante la configuración de relaciones entre elementos primarios y secundarios o dispositivos y módulos en IoT Hub. Cuando se crea un módulo en IoT Edge, se establece automáticamente una relación de confianza entre dicho módulo y el dispositivo IoT Edge. Este es el único modelo de autorización que admite el mecanismo de administración de enrutamiento.

* Configurar una directiva de autorización. Esta directiva de autorización es un documento en el que se enumeran todas las identidades de cliente con autorización para acceder a los recursos en el centro de IoT Edge. Este es el modelo de autorización principal que usa el agente de MQTT del centro de IoT Edge, aunque las relaciones entre elementos primarios y secundarios y dispositivos y módulos también las puede comprender el agente de MQTT para temas de IoT Hub.

### <a name="remote-configuration"></a>Configuración remota

La nube controla por completo al centro de IoT Edge. Dicho centro obtiene su configuración de IoT Hub a través de su [módulo gemelo](iot-edge-modules.md#module-twins). Incluye:

* Configuración de rutas
* Directivas de autorización
* Configuración del puente de MQTT

Además, se pueden realizar varias configuraciones mediante el establecimiento de [variables de entorno en el centro de IoT Edge](https://github.com/Azure/iotedge/blob/master/doc/EnvironmentVariables.md).
<!-- </1.2> -->
::: moniker-end

## <a name="runtime-quality-telemetry"></a>Telemetría de calidad en tiempo de ejecución

IoT Edge recopila la telemetría anónima del entorno en tiempo de ejecución del host y de los módulos del sistema para mejorar la calidad del producto. Esta información se denomina telemetría de calidad en tiempo de ejecución. La telemetría recopilada se envía periódicamente como mensajes de dispositivo a nube a IoT Hub desde el agente de IoT Edge. Estos mensajes no aparecen en la telemetría normal del cliente y no consumen ninguna cuota de mensajes.

El agente de IoT Edge y el concentrador generan métricas que se pueden recopilar para comprender el rendimiento del dispositivo. El agente de IoT Edge recopila un subconjunto de estas métricas como parte de la telemetría de calidad en tiempo de ejecución. Las métricas recopiladas para la telemetría de calidad en tiempo de ejecución se etiquetan con la etiqueta `ms_telemetry`. Para obtener información sobre todas las métricas disponibles, consulte [Acceso a las métricas integradas](how-to-access-built-in-metrics.md).

Cualquier información que pueda identificar a una persona u organización, como los nombres de dispositivo y módulos, se quita antes de la carga para garantizar la naturaleza anónima de la telemetría de calidad en tiempo de ejecución.

El agente de IoT Edge recopila la telemetría cada hora y envía un mensaje a IoT Hub cada 24 horas.

Si quiere dejar de enviar telemetría en tiempo de ejecución desde sus dispositivos, hay dos maneras de hacerlo:

* Establezca la variable de entorno `SendRuntimeQualityTelemetry` en `false` para **edgeAgent**, o
* Desactive la opción en Azure Portal durante la implementación.

## <a name="next-steps"></a>Pasos siguientes

* [Información sobre los módulos de Azure IoT Edge](iot-edge-modules.md)
* [Obtenga información sobre cómo implementar módulos y establecer rutas en IoT Edge](module-composition.md)
* [Obtenga información sobre cómo publicar y suscribirse con IoT Edge](how-to-publish-subscribe.md)
* [Más información sobre las métricas del entorno de ejecución de IoT Edge](how-to-access-built-in-metrics.md)
