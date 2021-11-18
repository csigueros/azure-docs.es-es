---
title: Conceptos de IoT y Azure IoT Hub | Microsoft Docs
description: En este artículo se describen los conceptos básicos para los usuarios nuevos de Azure IoT Hub
author: eross-msft
ms.author: lizross
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 07/07/2021
ms.openlocfilehash: d04f5b8ace6bfb331e9460745271d15b82a3fb98
ms.sourcegitcommit: 05c8e50a5df87707b6c687c6d4a2133dc1af6583
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/16/2021
ms.locfileid: "132553471"
---
# <a name="iot-concepts-and-azure-iot-hub"></a>Conceptos de IoT y Azure IoT Hub

En este artículo se analizan Internet de las cosas (IoT), Azure IoT Hub y los dispositivos IoT.

## <a name="iot-concepts"></a>Conceptos de IoT

Por lo general, Internet de las cosas (IoT) se define como una red de dispositivos físicos que se conectan a otros dispositivos y servicios a través de Internet u otra red de comunicación e intercambian datos con ellos. Actualmente, hay más de diez mil millones de dispositivos conectados en el mundo y se agregan más cada año. Todo lo que se pueda insertar con los sensores y software necesarios se puede conectar a través de Internet. IoT es posible gracias a estas tecnologías:

- Acceso a sensores de bajo costo y bajo consumo.
- Diversos protocolos que permiten conectarse a Internet.
- Plataformas de informática en la nube, como Azure.
- Macrodatos.
- Machine Learning
- La inteligencia artificial.

## <a name="azure-iot-hub"></a>Azure IoT Hub

IoT Hub es un servicio administrado, hospedado en la nube, que actúa como centro de mensajes para la comunicación entre una aplicación de IoT y los dispositivos conectados. Puede conectar millones de dispositivos y sus soluciones de back-end con confianza y de forma segura. La mayoría de dispositivos se pueden conectar a un centro de IoT. 

Se admiten varios patrones de mensajería, como telemetría del dispositivo a la nube, carga de archivos desde dispositivos y métodos de solicitud-respuesta para controlar los dispositivos desde la nube. IoT Hub también admite la supervisión para ayudarlo a realizar un seguimiento de la creación de dispositivos, la conexión de dispositivos y los errores de los dispositivos.

IoT Hub escala a millones de dispositivos conectados de manera simultánea y a millones de eventos por segundo para admitir las cargas de trabajo de IoT. Para obtener más información sobre cómo escalar el IoT Hub, vea [Escalado de IOT Hub](iot-hub-scaling.md). Para más información sobre los distintos niveles de servicio ofrecidos por IoT Hub y cómo ajustarlos mejor a sus necesidades de escalabilidad, consulte la [página de precios](https://azure.microsoft.com/pricing/details/iot-hub/).

Puede integrar IoT Hub con otros servicios de Azure para compilar soluciones completas de un extremo a otro. Por ejemplo, use:

* [Azure Event Grid](../event-grid/index.yml) para permitir que la empresa responda rápidamente a eventos críticos de forma confiable, escalable y segura.

* [Azure Logic Apps](../logic-apps/index.yml) para automatizar procesos de negocio.

* [Azure Machine Learning](iot-hub-weather-forecast-machine-learning.md) para agregar aprendizaje automático y modelos de AI a la solución.

* [Azure Stream Analytics](../stream-analytics/index.yml) para ejecutar cálculos de análisis en tiempo real en los flujos de datos de los dispositivos.

El [Acuerdo de Nivel de Servicio de IoT Hub](https://azure.microsoft.com/support/legal/sla/iot-hub/) es del 99,9 %. En el [SLA de Azure](https://azure.microsoft.com/support/legal/sla/) completo se explica la disponibilidad garantizada de Azure como un conjunto.

Las suscripciones de Azure disponen de límites de cuota predeterminados para evitar el uso abusivo del servicio. Estos límites podrían afectar el ámbito de su solución IoT. El límite actual es de 50 de centros de IoT Hub por suscripción. Puede solicitar un aumento de la cuota si se pone en contacto con el soporte técnico. Para obtener más información, consulte las [cuotas y limitaciones de IoT Hub](iot-hub-devguide-quotas-throttling.md). Para obtener más información sobre los límites de cuota, vea alguno de los siguientes artículos:

* [Límites de servicio de suscripción de Azure](../azure-resource-manager/management/azure-subscription-service-limits.md)

* [IoT Hub throttling and you](https://azure.microsoft.com/blog/iot-hub-throttling-and-you/) (Limitación de IoT Hub)

## <a name="iot-devices"></a>Dispositivos IoT

Los dispositivos IoT son distintos de otros clientes como exploradores y aplicaciones móviles. En concreto, los dispositivos de IoT:

- A menudo son sistemas insertados sin operador humano.
- Se pueden implementar en ubicaciones remotas, donde el acceso físico resulta costoso.
- Es posible que solo sean accesibles a través del back-end de soluciones.
- Es posible que tengan limitaciones de recursos de procesamiento y alimentación.
- Es posible que tengan conectividad de red intermitente, lenta o costosa.
- Es posible que necesiten usar protocolos de aplicación propios, personalizados o específicos de determinados sectores.

### <a name="device-identity"></a>Identidad del dispositivo

Cada centro de IoT tiene un registro de identidades que almacena información acerca de los dispositivos y módulos que pueden conectarse a él. Para que un dispositivo o un módulo se pueda conectar, debe haber una entrada para ese dispositivo o módulo en el registro de identidades del centro de IoT. También se debe autenticar un dispositivo o un módulo en el centro de IoT basándose en las credenciales almacenadas en el registro de identidades.

Se admiten dos métodos de autenticación entre el dispositivo e IoT Hub. Puede usar una autenticación basada en tokens de SAS o una autenticación de certificado X.509.

El método de token basado en SAS proporciona autenticación para cada llamada realizada por el dispositivo a IoT Hub mediante la asociación de la clave simétrica a cada llamada. La autenticación basada en X.509 permite la autenticación de un dispositivo IoT en la capa física como parte del establecimiento de la conexión estándar mediante Seguridad de la capa de transporte (TLS). El método basado en tokens de seguridad se puede usar sin la autenticación de X.509, que es un patrón menos seguro. La elección de un método u otro viene determinada principalmente por lo segura que deba ser la autenticación del dispositivo y la disponibilidad de almacenamiento seguro en este (para almacenar la clave privada de forma segura).

Puede configurar y aprovisionar muchos dispositivos a la vez mediante el [servicio IoT Hub Device Provisioning](../iot-dps/index.yml).

### <a name="device-communication"></a>Comunicación de dispositivos

Después de seleccionar el método de autenticación, la conexión a Internet entre el dispositivo IoT e IoT Hub se protege con el estándar de Seguridad de la capa de transporte (TLS). Azure IoT admite TLS 1.2, TLS 1.1 y TLS 1.0, en este orden. La compatibilidad con TLS 1.0 solo se proporciona para permitir versiones anteriores. Compruebe la compatibilidad con TLS en IoT Hub para ver cómo configurar el centro a fin de usar TLS 1.2, ya que proporciona la máxima seguridad.

### <a name="device-communication-patterns"></a>Patrones de comunicación de los dispositivos

Habitualmente, los dispositivos IoT envían datos de telemetría desde sus sensores a los servicios back-end en la nube. Pero hay otros tipos de comunicación posibles, como un servicio de back-end que envía comandos a los dispositivos. Algunos ejemplos de otros tipos de comunicación son los siguientes: 

*  Un camión refrigerado que envía datos de temperatura cada 5 minutos a un centro de IoT
*  Un servicio de back-end que envía un comando a un dispositivo para cambiar la frecuencia con la que envía los datos de telemetría que ayudan a diagnosticar un problema
*  Un dispositivo que supervisa un reactor por lotes en una planta química y envía una alerta cuando la temperatura supera un valor determinado

### <a name="device-telemetry"></a>Telemetría de dispositivo

Entre los ejemplos de telemetría recibidos de un dispositivo se incluyen datos de sensor como los de velocidad o temperatura, un mensaje de error como un evento perdido o un mensaje de información para indicar que el dispositivo está en buen estado. Los dispositivos IoT envían eventos a una aplicación a fin de obtener información. Las aplicaciones pueden necesitar subconjuntos específicos de eventos para el procesamiento o el almacenamiento en distintos puntos de conexión.

### <a name="device-properties"></a>Propiedades de dispositivo

Las propiedades se pueden leer o establecer desde la instancia de IoT Hub y se pueden usar para enviar notificaciones cuando se ha completado una acción. Un ejemplo de una propiedad específica en un dispositivo es la temperatura. Puede ser una propiedad grabable que se puede actualizar en el dispositivo o leer desde un sensor de temperatura conectado al dispositivo. 

En IoT Hub puede habilitar propiedades mediante [dispositivos gemelos](iot-hub-devguide-device-twins.md) o [Plug and Play](../iot-develop/overview-iot-plug-and-play.md).

Para obtener más información sobre las diferencias entre los dispositivos gemelos y Plug and Play, vea [Plug and Play](../iot-develop/concepts-digital-twin.md#device-twins-and-digital-twins).

### <a name="device-commands"></a>Comandos de dispositivo

Un ejemplo de un comando es el reinicio de un dispositivo. Para implementar comandos, IoT Hub le permite invocar métodos directos en los dispositivos. Los [métodos directos](iot-hub-devguide-direct-methods.md) representan una interacción entre solicitudes y respuestas con un dispositivo similar a una llamada HTTP en la que se completan correctamente o generan un error de inmediato (tras un tiempo de espera especificado por el usuario). Este enfoque es útil en escenarios donde el curso de una acción inmediata es distinto en función de si el dispositivo pudo responder.

### <a name="act-on-device-data"></a>Acción respecto de los datos del dispositivo

IoT Hub ofrece la capacidad de desbloquear el valor de los datos del dispositivo con otros servicios de Azure para que pueda cambiar a la solución predictiva de problemas, en lugar de la administración reactiva. Conecte la instancia de IoT Hub con otros servicios de Azure para realizar aprendizaje automático, análisis e inteligencia artificial a fin de actuar sobre los datos en tiempo real, optimizar el procesamiento y obtener conclusiones más detalladas.

#### <a name="built-in-endpoint-collects-device-data-by-default"></a>Un punto de conexión integrado recopila datos del dispositivo de manera predeterminada

Un punto de conexión integrado recopila datos del dispositivo de forma predeterminada. Los datos se recopilan mediante un patrón de solicitud-respuesta por medio de puntos de conexión de dispositivo IoT dedicados, están disponibles por una duración máxima de siete días y se pueden usar para realizar acciones en un dispositivo. Estos son los datos aceptados por el punto de conexión del dispositivo:

*    Envío de mensajes de dispositivo a nube.
*    Recepción de mensajes de nube a dispositivo
*    Iniciar cargas de archivos.
*    Recuperación y actualización de las propiedades del dispositivo gemelo.
*    Recepción de solicitudes de métodos directos.

Para obtener más información sobre los puntos de conexión de IoT Hub, vea [Puntos de conexión de la guía de desarrollo de IoT Hub](
iot-hub-devguide-endpoints.md#list-of-built-in-iot-hub-endpoints).

#### <a name="message-routing-sends-data-to-other-endpoints"></a>El enrutamiento de mensajes envía datos a otros puntos de conexión

Los datos también se pueden enrutar a otros servicios para el procesamiento posterior. A medida que la solución de IoT se escala horizontalmente, el número de dispositivos, el volumen y la variedad de los eventos y los distintos servicios también varían. Se necesita un método flexible, escalable, coherente y confiable para enrutar los eventos que se ajusten a este patrón. Una vez que se crea una ruta de mensaje, los datos dejan de fluir al punto de conexión integrado a menos que se haya configurado una ruta de reserva. Para obtener un tutorial en el que se muestran varios usos del enrutamiento de mensajes, vea el [Tutorial de enrutamiento](tutorial-routing.md).

IoT Hub también se integra con Event Grid, lo que permite distribuir los datos a varios suscriptores. Event Grid es un servicio de eventos totalmente administrado que le permite administrar fácilmente eventos en muchos servicios y aplicaciones de Azure diferentes. Diseñado para el rendimiento y la escala, simplifica la creación de aplicaciones controladas por eventos y arquitecturas sin servidor. Las diferencias entre el enrutamiento de mensajes y el uso de Event Grid se explican en [Comparación entre el enrutamiento de mensajes y Event Grid](iot-hub-event-grid-routing-comparison.md)

## <a name="next-steps"></a>Pasos siguientes

Para probar una solución de IoT de un extremo a otro, consulte los siguientes inicios rápidos de IoT Hub:

* [Quickstart: Send telemetry from a device to an IoT hub](../iot-develop/quickstart-send-telemetry-iot-hub.md?pivots=programming-language-nodejs) (Inicio rápido: enviar telemetría desde un dispositivo a IoT Hub)

Para más información sobre las distintas formas en que puede compilar e implementar soluciones de IoT con IoT de Azure, visite:

* [Aspectos básicos: Soluciones y tecnologías de IoT de Azure](../iot-fundamentals/iot-services-and-technologies.md).