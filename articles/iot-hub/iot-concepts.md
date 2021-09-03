---
title: Conceptos básicos para los nuevos usuarios de Azure IoT Hub | Microsoft Docs
description: En este artículo se muestran los conceptos básicos para los nuevos usuarios de Azure IoT Hub
author: robinsh
ms.author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 07/07/2021
ms.openlocfilehash: 28c91ee86a2657e5483d6d0287be8d42bd1fac23
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/13/2021
ms.locfileid: "121725303"
---
# <a name="iot-concepts"></a>Conceptos de IoT

IoT Hub es un servicio administrado, hospedado en la nube, que actúa como centro de mensajes para comunicaciones bidireccionales entre una aplicación de IoT y los dispositivos conectados. Puede conectar millones de dispositivos y sus soluciones de back-end con confianza y de forma segura. La mayoría de dispositivos se pueden conectar a un centro de IoT. 

Se admiten varios patrones de mensajería, como telemetría del dispositivo a la nube, carga de archivos desde dispositivos y métodos de solicitud-respuesta para controlar los dispositivos desde la nube. IoT Hub también admite la supervisión para ayudarle a realizar un seguimiento de la creación de dispositivos, la conexión de dispositivos y los errores de los dispositivos.

Las funcionalidades de IoT Hub le ayudan a compilar soluciones de IoT escalables y completas, como la administración de equipos industriales que se usan en fabricación, el seguimiento de activos valiosos en el sector sanitario y la supervisión de edificios de oficinas.

Los dispositivos IoT tienen características diferentes cuando se comparan con otros clientes, como los exploradores y las aplicaciones móviles. Los [SDK de dispositivo](iot-hub-devguide-sdks.md) ayudan a solucionar los desafíos relacionados con la conexión de dispositivos de forma segura y confiable al servicio de back-end. 

En concreto, los dispositivos de IoT:

- A menudo son sistemas insertados sin operador humano (a diferencia de un teléfono).
- Se pueden implementar en ubicaciones remotas, donde el acceso físico resulta costoso.
- Es posible que solo sean accesibles a través del back-end de soluciones.
- Es posible que tengan limitaciones de recursos de procesamiento y alimentación.
- Es posible que tengan conectividad de red intermitente, lenta o costosa.
- Es posible que necesiten usar protocolos de aplicación propios, personalizados o específicos de determinados sectores.

## <a name="securely-connect-and-communicate"></a>Conexión y comunicación seguras

La autenticación por dispositivo permite que cada dispositivo se conecte a IoT Hub y se administre de manera segura. Tiene todo el control sobre el acceso de los dispositivos y puede controlar las conexiones por dispositivo.

### <a name="devices-have-a-secure-identity"></a>Los dispositivos tienen una identidad segura

Cada centro de IoT tiene un registro de identidades que almacena información acerca de los dispositivos y módulos que pueden conectarse a IoT Hub. Para que un dispositivo o un módulo puedan conectarse a un centro de IoT, debe haber una entrada para ese dispositivo o módulo en el registro de identidades del centro de IoT. También se debe autenticar un dispositivo o un módulo en el centro de IoT basándose en las credenciales almacenadas en el registro de identidades.

Se admiten dos métodos de autenticación entre el dispositivo e IoT Hub. En un caso, puede usar una autenticación basada en tokens de SAS. El otro método admitido usa la autenticación de certificados X.509.

El método de token basado en SAS proporciona autenticación para cada llamada realizada por el dispositivo a IoT Hub mediante la asociación de la clave simétrica a cada llamada. La autenticación basada en X.509 permite la autenticación de un dispositivo IoT en la capa física como parte del establecimiento de la conexión estándar mediante Seguridad de la capa de transporte (TLS). El método basado en tokens de seguridad se puede usar sin la autenticación de X.509, que es un patrón menos seguro. La elección de un método u otro viene determinada principalmente por lo segura que deba ser la autenticación del dispositivo y la disponibilidad de almacenamiento seguro en este (para almacenar la clave privada de forma segura).

Los SDK de IoT de Azure generan automáticamente tokens sin necesidad de ninguna configuración especial. Si no usa el SDK, tendrá que generar los tokens de seguridad.

Puede configurar y aprovisionar muchos dispositivos a la vez mediante el [servicio IoT Hub Device Provisioning](../iot-dps/index.yml).

### <a name="devices-can-securely-communicate-with-an-iot-hub"></a>Los dispositivos se pueden comunicar de forma segura con una instancia de IoT Hub

Después de seleccionar el método de autenticación, la conexión a Internet entre el dispositivo IoT e IoT Hub se protege mediante el estándar de Seguridad de capa de transporte (TLS). Azure IoT admite TLS 1.2, TLS 1.1 y TLS 1.0, en este orden. La compatibilidad con TLS 1.0 solo se proporciona para permitir versiones anteriores. Compruebe la compatibilidad con TLS en IoT Hub para ver cómo configurar el centro a fin de usar TLS 1.2, ya que proporciona la máxima seguridad.

## <a name="communication-patterns-with-a-device"></a>Patrones de comunicación con un dispositivo

Habitualmente, los dispositivos IoT envían datos de telemetría desde sus sensores a los servicios back-end en la nube. Pero hay otros tipos de comunicación posibles, como un servicio de back-end que envía comandos a los dispositivos. Algunos ejemplos de otros tipos de comunicación son los siguientes: 

*  Un camión refrigerado que envía datos de temperatura cada 5 minutos a un centro de IoT
*  Un servicio de back-end que envía un comando a un dispositivo para cambiar la frecuencia con la que envía los datos de telemetría que ayudan a diagnosticar un problema
*  Un dispositivo que supervisa un reactor por lotes en una planta química y envía una alerta cuando la temperatura supera un valor determinado

### <a name="telemetry-is-data-emitted-by-a-device"></a>La telemetría son datos emitidos por un dispositivo 

Entre los ejemplos de telemetría recibidos de un dispositivo se incluyen datos de sensor como los de velocidad o temperatura, un mensaje de error como un evento perdido o un mensaje de información para indicar que el dispositivo está en buen estado. Los dispositivos IoT envían eventos (notificaciones, confirmaciones, telemetría) a una aplicación para obtener conclusiones. Las aplicaciones pueden necesitar subconjuntos específicos de eventos para el procesamiento o el almacenamiento en distintos puntos de conexión.

### <a name="properties-are-state-values-or-data-that-applications-can-access"></a>Las propiedades son valores de estado o datos a los que las aplicaciones pueden acceder. 

Por ejemplo, la versión actual del firmware del dispositivo o las propiedades grabables que se pueden actualizar, como un valor de temperatura, son propiedades. Las propiedades se pueden leer o establecer desde la instancia de IoT Hub y se pueden usar para enviar notificaciones cuando se ha completado una acción. Un ejemplo de una propiedad específica en un dispositivo es la temperatura. Puede ser una propiedad grabable que se puede actualizar en el dispositivo o leer desde un sensor de temperatura conectado al dispositivo. 

En IoT Hub puede habilitar propiedades mediante [dispositivos gemelos](iot-hub-devguide-device-twins.md) o [Plug and Play](../iot-develop/overview-iot-plug-and-play.md).

    
Para obtener más información sobre las diferencias entre los dispositivos gemelos y Plug and Play, vea [Plug and Play](../iot-develop/concepts-digital-twin.md#device-twins-and-digital-twins).

### <a name="commands-can-be-used-to-execute-methods-directly-on-connected-devices"></a>Se pueden usar comandos para ejecutar métodos directamente en dispositivos conectados. 

Un ejemplo de un comando es el reinicio del dispositivo. Para implementar comandos, IoT Hub le permite invocar métodos directos en los dispositivos desde la nube. Los [métodos directos](iot-hub-devguide-direct-methods.md) representan una interacción entre solicitudes y respuestas con un dispositivo similar a una llamada HTTP en la que se completan correctamente o generan un error de inmediato (tras un tiempo de espera especificado por el usuario). Este enfoque es útil en escenarios donde el curso de una acción inmediata es distinto en función de si el dispositivo pudo responder.

## <a name="view-and-act-on-data-collected-from-your-devices"></a>Visualización y actuación sobre datos recopilados de los dispositivos

IoT Hub ofrece la capacidad de desbloquear el valor de los datos del dispositivo con otros servicios de Azure para que pueda cambiar a la solución predictiva de problemas, en lugar de a la administración reactiva. Conecte la instancia de IoT Hub con otros servicios de Azure para realizar aprendizaje automático, análisis e inteligencia artificial a fin de actuar sobre los datos en tiempo real, optimizar el procesamiento y obtener conclusiones más detalladas.

### <a name="built-in-endpoint-collects-data-from-your-devices-by-default"></a>El punto de conexión integrado recopila datos de los dispositivos de forma predeterminada

Un punto de conexión integrado recopila datos del dispositivo de forma predeterminada. Los datos se recopilan mediante un patrón de solicitud-respuesta por medio de puntos de conexión de dispositivo IoT dedicados, están disponibles durante un máximo de siete días y se pueden usar para realizar acciones en un dispositivo. Estos son los datos aceptados por el punto de conexión del dispositivo:

*    Envío de mensajes de dispositivo a nube. Un dispositivo usa este punto de conexión para enviar mensajes de dispositivo a la nube.

*    Recepción de mensajes de nube a dispositivo El dispositivo usa ese punto de conexión para recibir mensajes de nube a dispositivos dirigidos.

*    Iniciar cargas de archivos. Un dispositivo usa este punto de conexión para recibir un URI de SAS de Azure Storage de IoT Hub para cargar un archivo.

*    Recuperación y actualización de las propiedades del dispositivo gemelo. Un dispositivo usa este punto de conexión para tener acceso a las propiedades del dispositivo gemelo. 

*    Recepción de solicitudes de métodos directos. Un dispositivo usa este punto de conexión para escuchar solicitudes de métodos directos. 

Para obtener más información sobre los puntos de conexión de IoT Hub, vea [Puntos de conexión de la guía de desarrollo de IoT Hub](
iot-hub-devguide-endpoints.md#list-of-built-in-iot-hub-endpoints).

### <a name="use-message-routing-to-send-data-to-other-endpoints-for-processing"></a>Uso del enrutamiento de mensajes para enviar datos a otros puntos de conexión para su procesamiento

Los datos también se pueden enrutar a otros servicios para el procesamiento posterior. A medida que la solución de IoT se escala horizontalmente, el número de dispositivos, el volumen y la variedad de los eventos y los distintos servicios también varían. Se necesita un método flexible, escalable, coherente y confiable para enrutar los eventos que se ajusten a este patrón. Los datos también se pueden filtrar para enviarlos a otros servicios. Una vez que se crea una ruta de mensaje, los datos dejan de fluir al punto de conexión integrado a menos que se haya configurado una ruta de reserva. Para obtener un tutorial en el que se muestran varios usos del enrutamiento de mensajes, vea el [Tutorial de enrutamiento](tutorial-routing.md).

IoT Hub también se integra con Event Grid, lo que permite distribuir los datos a varios suscriptores. Event Grid es un servicio de eventos totalmente administrado que le permite administrar fácilmente eventos en muchos servicios y aplicaciones de Azure diferentes. Diseñado para el rendimiento y la escala, simplifica la creación de aplicaciones controladas por eventos y arquitecturas sin servidor. Más información sobre Event Grid. Las diferencias entre el enrutamiento de mensajes y el uso de Event Grid se explican en [Comparación entre el enrutamiento de mensajes y Event Grid](iot-hub-event-grid-routing-comparison.md)