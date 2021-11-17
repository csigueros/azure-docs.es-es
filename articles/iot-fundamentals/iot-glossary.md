---
title: Glosario de términos de Azure IoT | Microsoft Docs
description: 'Guía del desarrollador: glosario en el que se explican algunos de los términos comunes empleados en los artículos de Azure IoT.'
author: dominicbetts
ms.author: dobett
ms.service: iot-fundamentals
services: iot-fundamentals
ms.topic: conceptual
ms.date: 11/02/2021
ms.openlocfilehash: eb2e48629e07271fcc151a3de97659c97cbb6719
ms.sourcegitcommit: 362359c2a00a6827353395416aae9db492005613
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/15/2021
ms.locfileid: "132491191"
---
# <a name="glossary-of-iot-terms"></a>Glosario de términos de IoT

En este artículo se enumeran varios de los términos que se usan de forma habitual en los artículos de IoT.

## <a name="a"></a>A

### <a name="advanced-message-queueing-protocol"></a>Advanced Message Queueing Protocol

Uno de los protocolos de mensajería compatible con [IoT Hub](#iot-hub) e IoT Central para la comunicación con [dispositivos](#device).

[Más información](../iot-hub/iot-hub-devguide-protocols.md)

Se aplica a: IoT Hub, IoT Central, desarrollador de dispositivos

### <a name="allocation-policy"></a>Directiva de asignación

En el [servicio Device Provisioning](#device-provisioning-service), la directiva de asignación determina la forma en que el servicio asigna [dispositivos](#device) a un [centro de IoT Hub vinculado](#linked-iot-hub).

Se aplica a: servicio Device Provisioning

### <a name="attestation-mechanism"></a>Mecanismo de atestación

En el [servicio Device Provisioning ](#device-provisioning-service), el mecanismo de atestación es el método que se usa para confirmar la identidad de un [dispositivo](#device). El mecanismo de atestación se configura en una [inscripción](#enrollment).

Los mecanismos de atestación incluyen certificados X.509, [módulos](#module) de plataforma segura y claves simétricas.

Se aplica a: servicio Device Provisioning

### <a name="automatic-deployment"></a>Implementación automática

Una característica de [IoT Edge](#iot-edge) que configura un conjunto de destino de [dispositivos de IoT Edge](#iot-edge-device) para ejecutar un conjunto de [módulos](#module) de IoT Edge. Cada implementación garantiza continuamente que todos los [dispositivos](#device) que coinciden con su [condición de destino](#target-condition) ejecutan el conjunto especificado de módulos, incluso cuando se crean nuevos dispositivos o se modifican para que coincidan con la condición de destino. Cada dispositivo IoT Edge solo recibe la implementación de prioridad más alta cuya condición de destino cumple.

[Más información](../iot-edge/module-deployment-monitoring.md)

Se aplica a: IoT Edge

### <a name="automatic-device-configuration"></a>Configuración automática de dispositivos

Una característica de [IoT Hub](#iot-hub) que permite que el back-end de la [solución](#solution) asigne las [propiedades deseadas](#desired-properties) a un conjunto de [dispositivos gemelos](#device-twin) e informe del estado del [dispositivo](#device) mediante métricas del sistema y personalizadas.

[Más información](../iot-hub/iot-hub-automatic-device-management.md)

Se aplica a: IoT Hub

### <a name="automatic-device-management"></a>Administración automática de dispositivos

Una característica de [IoT Hub](#iot-hub) que automatiza muchas de las tareas repetitivas y complejas de administración de grandes flotas de [dispositivos](#device) durante su ciclo de vida completo. La característica le permite tener como destino un conjunto de dispositivos según sus [propiedades](#properties), definir una [configuración deseada](#desired-configuration) y permitir que IoT Hub actualice los dispositivos en cuanto están dentro del ámbito.

Consta de [configuraciones automáticas de dispositivos](../iot-hub/iot-hub-automatic-device-management.md) e [implementaciones automáticas de IoT Edge](../iot-edge/how-to-deploy-at-scale.md).

Se aplica a: IoT Hub

### <a name="azure-certified-device-program"></a>Programa Azure Certified Device

Azure Certified [Device](#device) es un programa gratuito que le permite diferenciar, certificar y promover los dispositivos IoT creados para ejecutarse en Azure.

[Más información](../certification/overview.md)

Se aplica a: IoT Hub, IoT Central

### <a name="azure-digital-twins"></a>Azure Digital Twins

Una oferta de plataforma como servicio (PaaS) para crear representaciones digitales de cosas, lugares, procesos empresariales y personas reales. Cree gráficos de gemelos que representen entornos completos y úselos para obtener información para impulsar mejores productos, optimizar operaciones y costos, y crear innovadoras experiencias para los clientes.

[Más información](../digital-twins/overview.md)

Se aplica a: Digital Twins

### <a name="azure-digital-twins-instance"></a>Instancia de Azure Digital Twins

Una única instancia del servicio [Azure Digital Twins](#azure-digital-twins) en la suscripción de un cliente. Aunque Azure [Digital Twins](#digital-twin) hace referencia al servicio de Azure en su totalidad, su *instancia* de Azure Digital Twins es el recurso individual de Azure Digital Twins.

Se aplica a: Digital Twins

### <a name="azure-iot-explorer"></a>Azure IoT Explorer

Una herramienta que puede usar para ver la [telemetría](#telemetry) que el [dispositivo](#device) envía, trabajar con [propiedades](#properties) de dispositivo y llamar a [comandos](#command). También puede usar el explorador para interactuar con los dispositivos y probarlos y para administrar [dispositivos IoT Plug and Play](#iot-plug-and-play-device).

[Más información](https://github.com/Azure/azure-iot-explorer)

Se aplica a: IoT Hub, desarrollador de dispositivos

### <a name="azure-iot-tools"></a>Herramientas de Azure IoT

Una extensión de Visual Studio Code multiplataforma y de código abierto que le ayuda a administrar Azure [IoT Hub](#iot-hub) y [dispositivos](#device) en VS Code. Con Azure IoT Tools, los desarrolladores de IoT podrían desarrollar proyectos de IoT en VS Code con facilidad.

Se aplica a: IoT Hub, IoT Edge, IoT Central, desarrollador de dispositivos

### <a name="azure-iot-device-sdks"></a>SDK de dispositivos IoT de Azure

Estos SDK, disponibles para varios idiomas, permiten crear [aplicaciones de dispositivo](#device-app) que interactúan con un [centro de IoT](#iot-hub) o una aplicación de IoT Central.

[Más información](../iot-develop/about-iot-sdks.md)

Se aplica a: IoT Hub, IoT Central, desarrollador de dispositivos

### <a name="azure-iot-service-sdks"></a>SDK de servicios IoT de Azure

Estos SDK, disponibles para varios idiomas, permiten crear [aplicaciones de back-end](#back-end-app) que interactúan con un [centro de IoT](#iot-hub).

[Más información](/azure/iot-hub/iot-hub-devguide-sdks.md#azure-iot-hub-service-sdks)

Se aplica a: IoT Hub

## <a name="b"></a>B

### <a name="back-end-app"></a>Aplicación de back-end

En el contexto de [IoT Hub](#iot-hub), una aplicación que se conecta a uno de los [puntos de conexión](#endpoint) orientados al servicio en un centro de IoT. Por ejemplo, una aplicación de back-end podría recuperar mensajes [del dispositivo a la nube](#device-to-cloud) o administrar el [registro de identidades](#identity-registry). Normalmente, una aplicación de back-end se ejecuta en la nube, pero para simplificar, en muchos de los tutoriales, se muestran las aplicaciones de back-end como aplicaciones de consola que se ejecutan en la máquina de desarrollo local.

Se aplica a: IoT Hub

### <a name="built-in-endpoints"></a>Puntos de conexión integrados

[Puntos de conexión](#endpoint) integrados en [IoT Hub](#iot-hub). Por ejemplo, todos los centros de IoT incluyen un punto de conexión integrado que es compatible con Event Hubs.

Se aplica a: IoT Hub

## <a name="c"></a>C

### <a name="cloud-gateway"></a>Puerta de enlace en la nube

Una aplicación hospedada en la nube que permite la conectividad de [dispositivos](#device) que no se pueden conectar directamente a [IoT Hub](#iot-hub) o IoT Central. Una [puerta de enlace](#gateway) en la nube se hospeda en la nube a diferencia de una [puerta de enlace de campo](#field-gateway) que se ejecuta localmente en los dispositivos. Un caso de uso común de una puerta de enlace en la nube es la implementación de la traducción de protocolos para los dispositivos.

Se aplica a: IoT Hub, IoT Central

### <a name="cloud-property"></a>Propiedad de nube

Una característica de IoT Central que permite almacenar los metadatos del [dispositivo](#device) en la aplicación IoT Central. Las [propiedades](#properties) de la nube se definen en la [plantilla de dispositivo](#device-template), pero no forman parte del [modelo de dispositivo](#device-model). Las propiedades de la nube nunca se sincronizan con un dispositivo.

Se aplica a: IoT Central

### <a name="cloud-to-device"></a>De la nube al dispositivo

Mensajes enviados desde un [centro de IoT](#iot-hub) a un [dispositivo](#device) conectado. A menudo, estos mensajes son [comandos](#command) que indican al dispositivo que realice una acción.

Se aplica a: IoT Hub

### <a name="command"></a>Get-Help

Un comando se define en una [interfaz](#interface) de IoT Plug and Play para representar los métodos que se pueden llamar en el [gemelo digital](#digital-twin). Por ejemplo, un comando para reiniciar un [dispositivo](#device). En IoT Central, los comandos se definen en la [plantilla de dispositivo](#device-template).

Se aplica a: IoT Hub, IoT Central, desarrollador de dispositivos

### <a name="component"></a>Componente

En IoT Plug and Play y [Azure Digital Twins](#azure-digital-twins), los componentes permiten crear una [interfaz](#interface) del [modelo](#model) como un ensamblado de otras interfaces. Un [modelo de dispositivo](#device-model) puede combinar varias interfaces como componentes. Por ejemplo, un modelo podría incluir un componente interruptor y un componente termostato. Varios componentes de un modelo pueden usar también el mismo tipo de interfaz. Por ejemplo, un modelo puede incluir dos componentes termostato.

Se aplica a: IoT Hub, Digital Twins, desarrollador de dispositivos

### <a name="configuration"></a>Configuración

En el contexto de la [configuración automática de dispositivos](#automatic-device-configuration) en [IoT Hub](#iot-hub), define la [configuración deseada](#desired-configuration) de un conjunto de [dispositivos](#device) gemelos y ofrece un conjunto de métricas para informar del estado y el progreso.

Se aplica a: IoT Hub

### <a name="connection-string"></a>Cadena de conexión

Se usan en el código de aplicación a fin de encapsular la información necesaria para conectarse a un [punto de conexión](#endpoint). Normalmente, una cadena de conexión incluye la dirección de la información del punto de conexión y de seguridad, pero los formatos de la cadena de conexión varían en función de los servicios. Hay dos tipos de cadena de conexión asociadas con el servicio de [IoT Hub](#iot-hub):

- *Las cadenas de conexión de [dispositivo](#device)* permiten que los dispositivos se conecten a puntos de conexión orientados a dispositivos de un centro de IoT.
- *Las cadenas de conexión de IoT Hub* permiten que las [aplicaciones de back-end](#back-end-app) se conecten a puntos de conexión orientados a servicios de un centro de IoT.

Se aplica a: IoT Hub, desarrollador de dispositivos

### <a name="custom-endpoints"></a>Puntos de conexión personalizados

[Puntos de conexión](#endpoint) definidos por el usuario en un [centro de IoT](#iot-hub) que entregan los mensajes que distribuye una [regla de enrutamiento](#routing-rule). Estos puntos de conexión se conectan directamente a un centro de Event, una cola de Service Bus o un tema de Service Bus.

Se aplica a: IoT Hub

### <a name="custom-gateway"></a>Puerta de enlace personalizada

Permite la conectividad de [dispositivos](#device) que no se pueden conectar directamente a [IoT Hub](#iot-hub) o a IoT Central. Puede usar Azure [IoT Edge](#iot-edge) con el fin de crear [puertas de enlace](#gateway) personalizadas que implementan lógica personalizada para controlar mensajes, conversiones de protocolo personalizadas y otros procesos.

Se aplica a: IoT Hub, IoT Central

## <a name="d"></a>D

### <a name="default-component"></a>Componente predeterminado

Todos los [modelos](#model) de [dispositivos de IoT Plug and Play](#iot-plug-and-play-device) tienen un [componente](#component) predeterminado. Un [modelo de dispositivo](#device-model) simple solo tiene un componente predeterminado; este tipo de modelo también se conoce como [dispositivo](#device) sin componente. Un modelo más complejo tiene varios componentes anidados bajo el componente predeterminado.

Se aplica a: IoT Hub, IoT Central, desarrollador de dispositivos

### <a name="deployment-manifest"></a>Manifiesto de implementación

Un documento JSON en [IoT Edge](#iot-edge) que contiene los datos de [configuración](#configuration) de uno o varios [módulos gemelos](#module-twin) de [dispositivo IoT Edge](#iot-edge-device).

Se aplica a: IoT Edge, IoT Central

### <a name="desired-configuration"></a>Configuración deseada

En el contexto de un [dispositivo gemelo](#device-twin), la [configuración deseada](#configuration) hace referencia al conjunto completo de [propiedades](#properties) y metadatos en el [dispositivo](#device) gemelo que se debe sincronizar con el dispositivo.

Se aplica a: IoT Hub

### <a name="desired-properties"></a>Propiedades deseadas

En el contexto de un [dispositivo gemelo](#device-twin), las [propiedades](#properties) deseadas son una subsección del [dispositivo](#device) gemelo que se usa con las [propiedades notificadas](#reported-properties) para sincronizar la [configuración](#configuration) o la condición del dispositivo. Solo una [aplicación de back-end](#back-end-app) puede establecer las propiedades deseadas y solo la [aplicación para dispositivo](#device-app) las puede observar. IoT Central usa el término propiedades grabables.

Se aplica a: IoT Hub

### <a name="device"></a>Dispositivo

En el contexto de IoT, un dispositivo suele ser un dispositivo de computación independiente a pequeña escala que puede recopilar datos o controlar otros dispositivos. Por ejemplo, un dispositivo podría ser un dispositivo de supervisión ambiental o un controlador para los sistemas de riego y ventilación en un invernadero. El catálogo de dispositivos proporciona una lista de dispositivos certificados.

Se aplica a: IoT Hub, IoT Central, IoT Edge, servicio Device Provisioning, desarrollador de dispositivos

### <a name="device-provisioning-service"></a>Servicio de aprovisionamiento de dispositivos

Un servicio auxiliar de [IoT Hub](#iot-hub) e IoT Central que se utiliza para configurar el [aprovisionamiento de dispositivos](#device-provisioning) sin interacción. Con DPS se pueden aprovisionar millones de [dispositivos](#device) de forma segura y escalable.

Se aplica a: IoT Hub, servicio Device Provisioning, IoT Central

### <a name="device-rest-api"></a>API REST de dispositivos

Una API de REST que puede usar en un [dispositivo](#device) para enviar mensajes del [dispositivo a la nube](#device-to-cloud) a un [centro de IoT](#iot-hub) y recibir mensajes de la [nube al dispositivo](#cloud-to-device) desde un centro de IoT. Normalmente, debe usar uno de los [SDK de dispositivo Azure IoT](#azure-iot-device-sdks) de nivel superior.

[Más información](/rest/api/iothub/device)

Se aplica a: IoT Hub

### <a name="device-app"></a>Aplicación de dispositivo

Una aplicación de [dispositivo](#device) se ejecuta en el dispositivo y controla la comunicación con el [centro de IoT](#iot-hub) o la aplicación IoT Central. Por lo general, se suele utilizar uno de los [SDK de dispositivos IoT de Azure](#azure-iot-device-sdks) al implementar una aplicación para dispositivo.

Se aplica a: IoT Hub, IoT Central, desarrollador de dispositivos

### <a name="device-builder"></a>Creador de dispositivos

La persona responsable de crear el código que se ejecutará en los [dispositivos](#device). Los creadores de dispositivos usan normalmente uno de los [SDK de dispositivo IoT de Azure](#azure-iot-device-sdks) para implementar el cliente del dispositivo. Un creador de dispositivos usa un [modelo de dispositivo](#device-model) e [interfaces](#interface) al implementar código para que se ejecute en un [dispositivo IoT Plug and Play](#iot-plug-and-play-device).

Se aplica a: IoT Hub, IoT Central, IoT Edge, desarrollador de dispositivos

### <a name="device-identity"></a>Identidad del dispositivo

El identificador único asignado a cada [dispositivo](#device) registrado en el [registro de identidades](#identity-registry) de [IoT Hub](#iot-hub) o en una aplicación de IoT Central.

Se aplica a: IoT Hub, IoT Central

### <a name="device-management"></a>Administración de dispositivos

La administración de [dispositivos](#device) abarca el ciclo de vida completo asociado a la administración de dispositivos de la solución de [IoT](#solution), incluidos la planeación, el aprovisionamiento, la configuración, la supervisión y la retirada.

Se aplica a: IoT Hub, IoT Central

### <a name="device-model"></a>Modelo de dispositivo

Una descripción que usa el [lenguaje de definición de Digital Twins](#digital-twins-definition-language) de las funcionalidades de un [dispositivo](#device). Las funcionalidades incluyen la [telemetría](#telemetry), las [propiedades](#properties) y los [comandos](#command).

[Más información](../iot-develop/concepts-modeling-guide.md)

Se aplica a: IoT Hub, IoT Central, desarrollador de dispositivos, Digital Twins

### <a name="device-provisioning"></a>Aprovisionamiento de dispositivos

El proceso de agregar los datos iniciales del [dispositivo](#device) en los almacenes de la [solución](#solution). Para permitir que un nuevo dispositivo se conecte al centro, debe agregar un id. de dispositivo y claves al [registro de identidades](#identity-registry) de [IoT Hub](#iot-hub). El [servicio Device Provisioning](#device-provisioning-service) puede aprovisionar automáticamente dispositivos en un centro de IoT o una aplicación de IoT Central.

Se aplica a: IoT Hub, IoT Central, servicio Device Provisioning

### <a name="device-template"></a>Plantilla de dispositivo

En IoT Central, una plantilla de [dispositivo](#device) es un plano técnico que define las características y los comportamientos de un tipo de dispositivo que se conecta a la aplicación.

Por ejemplo, la plantilla de dispositivo define la [telemetría](#telemetry) que un dispositivo envía para que IoT Central pueda crear visualizaciones que usen las unidades y los tipos de datos correctos. Un [modelo de dispositivo](#device-model) forma parte de la plantilla de dispositivo.

Se aplica a: IoT Central

### <a name="device-twin"></a>Dispositivo gemelo

Un [dispositivo](#device) gemelo es un documento JSON que almacena información sobre el estado del dispositivo, como metadatos, [configuraciones](#configuration) y condiciones. [IoT Hub](#iot-hub) conserva un dispositivo gemelo por cada dispositivo que se aprovisiona en el centro de IoT. Los dispositivos gemelos permiten sincronizar las condiciones y las configuraciones del dispositivo entre el dispositivo y el back-end de la [solución](#solution). Puede consultar los dispositivos gemelos para buscar dispositivos específicos y ver el estado de las operaciones de larga duración.

Se aplica a: IoT Hub

### <a name="device-to-cloud"></a>Del dispositivo a la nube

Hace referencia a los mensajes enviados desde un [dispositivo](#device) conectado a [IoT Hub](#iot-hub) o IoT Central.

Se aplica a: IoT Hub

### <a name="digital-twins-definition-language"></a>Lenguaje de definición de Digital Twins

Un lenguaje JSON-LD que sirve para describir los [modelos](#interface) y las [interfaces](#iot-plug-and-play-device) de los [dispositivos IoT Plug and Play](#model) y las entidades de [Azure Digital Twins](#azure-digital-twins). El lenguaje permite que la plataforma IoT y las [soluciones](#solution) de IoT usen la semántica de la entidad.

[Más información](https://github.com/Azure/opendigitaltwins-dtdl)

Se aplica a: IoT Hub, IoT Central, Digital Twins

### <a name="digital-twin"></a>Gemelo digital

Un gemelo digital es una colección de datos digitales que representa un objeto físico. Los cambios en el objeto físico se reflejan en el gemelo digital. En algunos escenarios, puede usar el gemelo digital para manipular el objeto físico. El [servicio Azure Digital Twins](../digital-twins/index.yml) usa [modelos](#model) expresados en el [lenguaje de definición de Digital Twins](#digital-twins-definition-language) para representar gemelos digitales de [dispositivos físicos](#physical-device) o conceptos empresariales abstractos de alto nivel, lo que permite una amplia gama de [soluciones](#solution) de gemelos digitales basadas en la nube. Un [dispositivo](#device) [IoT Plug and Play](../iot-develop/index.yml) tiene un gemelo digital, descrito por un [modelo de dispositivo](#device-model) en lenguaje de definición de Digital Twins.

Se aplica a: IoT Hub, IoT Central, Digital Twins, desarrollador de dispositivos

### <a name="digital-twin-change-events"></a>Eventos de cambio de gemelo digital

Cuando un [dispositivo IoT Plug and Play](#iot-plug-and-play-device) se conecta a un [centro de IoT](#iot-hub), el centro puede usar su funcionalidad de enrutamiento para enviar notificaciones de cambios de [gemelos digitales](#digital-twin). La característica de exportación de datos de IoT Central también puede reenviar eventos de cambios de gemelos digitales a otros servicios. Por ejemplo, cada vez que un valor de propiedad cambia en un [dispositivo](#device), IoT Hub puede enviar una notificación a un [punto de conexión](#endpoint), como un centro de eventos.

Se aplica a: IoT Hub, IoT Central

### <a name="digital-twin-graph"></a>Grafo de gemelo digital

En el servicio [Azure Digital Twins](#azure-digital-twins), es posible conectar [gemelos digitales](#digital-twin) a [relaciones](#relationship) para crear grafos de conocimiento que representan digitalmente todo un entorno físico. Una sola [instancia de Azure Digital Twins](#azure-digital-twins-instance) puede hospedar muchos grafos desconectados o un solo grafo interconectado.

Se aplica a: IoT Hub

### <a name="direct-method"></a>Método directo

Una manera de desencadenar un método para ejecutarse en un [dispositivo](#device) mediante la invocación de una API en el [centro de IoT](#iot-hub).

Se aplica a: IoT Hub

### <a name="downstream-service"></a>Servicio de nivel inferior

Un término relativo que describe los servicios que reciben datos del contexto actual. Por ejemplo, en el contexto de [Azure Digital Twins](#azure-digital-twins), Time Series Insights se considera un servicio descendente si ha configurado los datos para que fluyan desde Azure [Digital Twins](#digital-twin) a Time Series Insights.

Se aplica a: IoT Hub, IoT Central, Digital Twins

## <a name="e"></a>E

### <a name="endpoint"></a>Punto de conexión

Representación con nombre de un servicio de enrutamiento de datos que puede recibir datos de otros servicios.

Un [centro de IoT](#iot-hub) expone varios puntos de conexión que permiten a las aplicaciones conectarse con el centro de IoT. Hay puntos de conexión orientados a los [dispositivos](#device) que les permiten realizar operaciones como enviar de mensajes del [dispositivo a la nube](#device-to-cloud). Hay puntos de conexión orientados al servicio que permiten a las [aplicaciones de back-end](#back-end-app) realizar operaciones como la administración de [identidades de dispositivo](#device-identity). Hay [puntos de conexión integrados](#built-in-endpoints) orientados al servicio para leer mensajes del dispositivo a la nube. Puede crear [puntos de conexión](#custom-endpoints) personalizados para recibir mensajes del dispositivo a la nube que distribuye una [regla de enrutamiento](#routing-rule).

Se aplica a: IoT Hub

### <a name="enrollment"></a>Inscripción

En el [servicio Device Provisioning](#device-provisioning-service), una inscripción es el registro de [dispositivos](#device) individuales o grupos de dispositivos que se pueden registrar en un [centro de IoT vinculado](#linked-iot-hub) a través del aprovisionamiento automático.

Se aplica a: IoT Hub, IoT Central, servicio Device Provisioning

### <a name="enrollment-group"></a>Grupo de inscripción

En el [servicio Device Provisioning](#device-provisioning-service) y IoT Central, un grupo de [inscripción](#enrollment) identifica un grupo de [dispositivos](#device) que comparten un [mecanismo de atestación](#attestation-mechanism) de clave simétrica o X.509.

Se aplica a: IoT Hub, servicio Device Provisioning, IoT Central

### <a name="event-hubs-compatible-endpoint"></a>Punto de conexión compatible con Event Hubs

Un [punto de conexión](#endpoint) de [IoT Hub](#iot-hub) que permite usar cualquier método compatible con Event Hubs para leer los mensajes del [dispositivo](#device) enviados a la nube. Los métodos compatibles con el centro de eventos incluyen los [SDK de Event Hubs](../event-hubs/event-hubs-programming-guide.md) y [Azure Stream Analytics](../stream-analytics/stream-analytics-introduction.md).

Se aplica a: IoT Hub

### <a name="event-handler"></a>Controlador de eventos

Proceso desencadenado por la llegada de un evento. Por ejemplo, puede crear controladores de eventos agregando código de procesamiento de eventos a una función de Azure y enviándole datos mediante [puntos de conexión](#endpoint) y el [enrutamiento de eventos](#event-routing).

Se aplica a: IoT Hub

### <a name="event-routing"></a>Enrutamiento de eventos

El proceso de envío de eventos y sus datos desde un [dispositivo](#device) o servicio al [punto de conexión](#endpoint) de otro.

En [IoT Hub](#iot-hub), puede definir [reglas de enrutamiento](#routing-rule) para describir cómo se deben enviar los mensajes. En [Azure Digital Twins](#azure-digital-twins), las rutas de eventos son entidades que se crean para este fin. Las rutas de eventos de Azure [Digital Twins](#digital-twin) pueden contener filtros para limitar los tipos de eventos que se envían a cada punto de conexión.

Se aplica a: IoT Hub, Digital Twins

## <a name="f"></a>F

### <a name="field-gateway"></a>Puerta de enlace de campo

Permite la conectividad de [dispositivos](#device) que no se pueden conectar directamente a [IoT Hub](#iot-hub) y se suele implementar localmente con los dispositivos.

Se aplica a: IoT Hub, IoT Central

## <a name="g"></a>G

### <a name="gateway"></a>Puerta de enlace

Una puerta de enlace permite la conectividad para [dispositivos](#device) que no se pueden conectar directamente a [IoT Hub](#iot-hub). Consulte también [puerta de enlace de campo](#field-gateway), [puerta de enlace en la nube](#cloud-gateway) y [puerta de enlace personalizada](#custom-gateway).

Se aplica a: IoT Hub, IoT Central

### <a name="gateway-device"></a>Dispositivo de puerta de enlace

Un ejemplo de una [puerta de enlace de campo](#field-gateway). Un [dispositivo](#device) de [puerta de enlace](#gateway) puede ser un dispositivo IoT estándar o un [dispositivo IoT Edge](#iot-edge-device).

Un dispositivo de puerta de enlace habilita la conectividad para dispositivos descendentes que no se pueden conectar directamente a [IoT Hub](#iot-hub).

Se aplica a: IoT Hub, IoT Central, IoT Edge

## <a name="h"></a>H

### <a name="hardware-security-module"></a>Módulo de seguridad de hardware

Se usa para el almacenamiento seguro basado en hardware de los secretos del [dispositivo](#device). Es la forma más segura de almacenamiento secreto para un dispositivo. Un [módulo](#module) de seguridad de hardware puede almacenar certificados X.509 y claves simétricas. En el [servicio Device Provisioning](#device-provisioning-service), un [mecanismo de atestación](#attestation-mechanism) puede usar un módulo de seguridad de hardware.

Se aplica a: IoT Hub, desarrollador de dispositivos, servicio Device Provisioning

## <a name="i"></a>I

### <a name="id-scope"></a>Ámbito de identificador

Un valor único asignado a una instancia de [servicio Device Provisioning](#device-provisioning-service) cuando se crea.

Las aplicaciones de IoT Central usan las instancias de DPS y hacen que el ámbito del id. esté disponible a través de la interfaz de usuario de IoT Central.

Se aplica a: IoT Hub, IoT Central, servicio Device Provisioning

### <a name="identity-registry"></a>Registro de identidad

Un [componente](#component) integrado de un [centro de IoT](#iot-hub) que almacena información sobre los [dispositivos](#device) individuales que tienen permitido conectarse al centro.

Se aplica a: IoT Hub

### <a name="individual-enrollment"></a>Inscripción individual

Identifica un único [dispositivo](#device) que el [servicio Device Provisioning](#device-provisioning-service) puede aprovisionar para un [centro de IoT](#iot-hub).

Se aplica a: Iot Hub, servicio Device Provisioning

### <a name="interface"></a>Interfaz

En IoT Plug and Play, una interfaz describe las funcionalidades relacionadas que un [dispositivo IoT Plug and Play](#iot-plug-and-play-device) o un [gemelo digital](#digital-twin) implementa. Puede reutilizar las interfaces en distintos [modelos de dispositivo](#device-model). Cuando se usa una interfaz en un [modelo](#model) de [dispositivo](#device), define un [componente](#component) del dispositivo. Un dispositivo simple solo contiene una interfaz predeterminada.

En [Azure Digital Twins](#azure-digital-twins), la *interfaz* se puede usar para hacer referencia al elemento de código de nivel superior en una definición de modelo en [lenguaje de definición de Digital Twins](#digital-twins-definition-language).

Se aplica a: desarrollador de dispositivos, Digital Twins

### <a name="iot-edge"></a>IoT Edge

Un servicio, las bibliotecas de cliente relacionadas y el entorno de ejecución que permite la implementación controlada por la nube de los servicios de Azure y el código específico de [solución](#solution) para [dispositivos](#device) locales. Los [dispositivos IoT Edge](#iot-edge-device) pueden agregar datos de otros dispositivos para realizar computación y análisis antes de que se envíen a la nube.

[Más información](../iot-edge/index.yml)

Se aplica a: IoT Edge

### <a name="iot-edge-agent"></a>Agente de IoT Edge

La parte del [entorno de ejecución de IoT Edge](#iot-edge-runtime) responsable de implementar y supervisar los [módulos](#module).

Se aplica a: IoT Edge

### <a name="iot-edge-device"></a>Dispositivo de IoT Edge

Un [dispositivo](#device) que usa [módulos](#module) de [IoT Edge](#iot-edge) para ejecutar servicios de Azure, servicios de terceros o su propio código. En el dispositivo, el [entorno de ejecución de IoT Edge](#iot-edge-runtime) administra los módulos. Puede supervisar y administrar de forma remota un dispositivo IoT Edge desde la nube.

Se aplica a: IoT Edge

### <a name="iot-edge-hub"></a>Centro de IoT Edge

La parte del [entorno de ejecución de IoT Edge](#iot-edge-runtime) responsable de las comunicaciones de [módulo](#module) a módulo, ascendente y descendente.

Se aplica a: IoT Edge

### <a name="iot-edge-runtime"></a>Entorno de tiempo de ejecución de IoT Edge

Incluye todo lo que Microsoft distribuye para instalarse en un [dispositivo IoT Edge](#iot-edge-device). Incluye el agente de Edge, el centro de Edge y el demonio de seguridad de [IoT Edge](#iot-edge).

Se aplica a: IoT Edge

### <a name="iot-hub"></a>IoT Hub

Un servicio de Azure totalmente administrado que permite la comunicación bidireccional confiable y segura entre millones de [dispositivos](#device) y un back-end de [soluciones](#solution). Para obtener más información, vea [¿Qué es IoT Hub de Azure?](../iot-hub/about-iot-hub.md). Con su suscripción de Azure, puede crear centros de IoT para controlar las cargas de trabajo de mensajería de IoT.

[Más información](../iot-hub/about-iot-hub.md)

Se aplica a: IoT Hub

### <a name="iot-hub-resource-rest-api"></a>API REST de recursos de IoT Hub

Una API que puede usar para administrar los [centros de IoT Hub](#iot-hub) en la suscripción de Azure con operaciones como crear, actualizar y eliminar centros.

Se aplica a: IoT Hub

### <a name="iot-hub-metrics"></a>Métricas de IoT Hub

Una característica de Azure Portal que le permite supervisar el estado de los [centros de IoT](#iot-hub). Las métricas de IoT Hub permiten evaluar el estado general del centro de IoT y de los [dispositivos](#device) conectados a él.

Se aplica a: IoT Hub

### <a name="iot-hub-query-language"></a>Lenguaje de consulta de IoT Hub

Un lenguaje de tipo SQL para [IoT Hub](#iot-hub) que permite consultar los [trabajos](#job), los [gemelos digitales](#digital-twin) y los [dispositivos gemelos](#device-twin).

Se aplica a: IoT Hub

### <a name="iot-plug-and-play-bridge"></a>Puente de IoT Plug and Play

Una aplicación de código abierto que permite a los sensores y periféricos existentes conectados a las [puertas de enlace](#gateway) de Windows o Linux conectarse como [dispositivos IoT Plug and Play](#iot-plug-and-play-device).

Se aplica a: IoT Hub, desarrollador de dispositivos, IoT Central

### <a name="iot-plug-and-play-conventions"></a>Convenciones de IoT Plug and Play

Un conjunto de convenciones que los [dispositivos](#device) IoT deben seguir al intercambiar datos con una [solución](#solution).

Se aplica a: IoT Hub, IoT Central, desarrollador de dispositivos

### <a name="iot-plug-and-play-device"></a>Dispositivo IoT Plug and Play

Normalmente un [dispositivo](#device) informático independiente y a pequeña escala que recopila datos o controla otros dispositivos, y que ejecuta software o firmware que implementa un [modelo de dispositivo](#device-model). Por ejemplo, un dispositivo IoT Plug and Play podría ser un dispositivo de supervisión ambiental o un controlador de un sistema de riego de agricultura inteligente. Un dispositivo IoT Plug and Play podría implementarse directamente o como [módulo](#module) [IoT Edge](#iot-edge).

Se aplica a: IoT Hub, IoT Central, desarrollador de dispositivos

### <a name="iot-extension-for-azure-cli"></a>Extensión de IoT para la CLI de Azure

Una extensión para la CLI de Azure. La extensión permite realizar tareas como administrar los [dispositivos](#device) en el [registro de identidades](#identity-registry), enviar y recibir mensajes de dispositivos y supervisar las operaciones del [centro de IoT](#iot-hub).

[Más información](/cli/azure/azure-cli-reference-for-IoT)

Se aplica a: IoT Hub, IoT Central, IoT Edge, servicio Device Provisioning, desarrollador de dispositivos

## <a name="j"></a>J

### <a name="job"></a>Trabajo

En el contexto de [IoT Hub](#iot-hub), los trabajos permiten programar y realizar un seguimiento de las actividades en un conjunto de [dispositivos](#device) registrados en el centro de IoT. Las actividades incluyen actualizar las [propiedades deseadas](#desired-properties) del [dispositivo gemelo](#device-twin), actualizar las [etiquetas](#tag) del dispositivo gemelo e invocar [métodos directos](#direct-method). IoT Hub también utiliza trabajos para importar y exportar desde el [registro de identidad](#identity-registry).

En el contexto de IoT Central, los trabajos permiten administrar los dispositivos conectados de forma masiva mediante el establecimiento de [propiedades](#properties) y la llamada a [comandos](#command). Los trabajos de IoT Central también le permiten actualizar las propiedades de nube de forma masiva.

Se aplica a: IoT Hub, IoT Central

## <a name="l"></a>L

### <a name="leaf-device"></a>Dispositivo hoja

Un [dispositivo](#device) sin dispositivo descendente conectado. Normalmente, los dispositivos hoja están conectados a un [dispositivo de puerta de enlace](#gateway-device).

Se aplica a:IoT Edge, IoT Central, desarrollador de dispositivos

### <a name="lifecycle-event"></a>Evento de ciclo de vida

En [Azure Digital Twins](#azure-digital-twins), este tipo de evento se desencadena cuando un elemento de datos (como un [gemelo digital](#digital-twin), una [relación](#relationship) o un [controlador de eventos](#event-handler)) se crea en una instancia de [Azure Digital Twins](#azure-digital-twins-instance), o se elimina de ella.

Se aplica a: Digital Twins, IoT Hub, IoT Central

### <a name="linked-iot-hub"></a>Instancia de IoT Hub vinculada

Un [centro de IoT](#iot-hub) que está vinculado a instancia de [servicio Device Provisioning](#device-provisioning-service). Una instancia de DPS puede registrar un id. de [dispositivo](#device) y establecer la [configuración](#configuration) inicial en el [dispositivo gemelo](#device-twin) en los centros de IoT vinculados.

Se aplica a: Iot Hub, servicio Device Provisioning

## <a name="m"></a>M

### <a name="mqtt"></a>MQTT

Uno de los protocolos de mensajería compatible con [IoT Hub](#iot-hub) e IoT Central para la comunicación con [dispositivos](#device). MQTT no representa nada.

[Más información](../iot-hub/iot-hub-devguide-protocols.md)

Se aplica a: IoT Hub, IoT Central, desarrollador de dispositivos

### <a name="model"></a>Modelo

Una definición de un tipo de entidad en el entorno físico, incluidas sus [propiedades](#properties), telemetría y [componentes](#component). Los modelos se usan para crear [gemelos digitales](#digital-twin) que representan objetos físicos concretos de este tipo. Los modelos se escriben en el [lenguaje de definición de Digital Twins](#digital-twins-definition-language).

En el [servicio Azure Digital Twins](#azure-digital-twins), los modelos definen [dispositivos](#device) o conceptos empresariales abstractos de nivel superior. En IoT Plug and Play, los [modelos de dispositivo](#device-model) describen dispositivos de forma específica.

Se aplica a: IoT Hub, IoT Central, Digital Twins, desarrollador de dispositivos

### <a name="model-id"></a>Id. de modelo

Cuando un [dispositivo IoT Plug and Play](#iot-plug-and-play-device) se conecta a [IoT Hub](#iot-hub) o a una aplicación de IoT Central, envía el id. del [modelo](#model) de [lenguaje de definición de Digital Twins](#digital-twins-definition-language) que implementa. Cada modelo como un id. de modelo único. Este id. de modelo permite que la [solución](#solution) encuentre el [modelo de dispositivo](#device-model).

Se aplica a: IoT Hub, IoT Central, desarrollador de dispositivos, Digital Twins

### <a name="model-repository"></a>Repositorio de modelos

Almacena los [modelos](#model) e [interfaces](#interface) de [lenguaje de definición de Digital Twins](#digital-twins-definition-language). Una [solución](#solution) usa un [id. de modelo](#model-id) para recuperar un modelo de un repositorio.

Se aplica a: IoT Hub, IoT Central, Digital Twins

### <a name="model-repository-rest-api"></a>API REST del repositorio de modelos

Una API para administrar e interactuar con el [repositorio de modelos](#model-repository). Por ejemplo, puede usar la API para agregar y buscar [modelos de dispositivo](#device-model).

Se aplica a: IoT Hub, IoT Central, Digital Twins

### <a name="module"></a>Módulo

Los SDK de [dispositivo](#device) de [IoT Hub](#iot-hub) le permiten crear instancias de módulos, donde cada uno abra una conexión independiente al centro de IoT. Esto le permite usar espacios de nombres separados para distintos [componentes](#component) del dispositivo.

La [identidad del módulo](#module-identity) y el [módulo gemelo](#module-twin) proporcionan las mismas funcionalidades que la [identidad del dispositivo](#device-identity) y el [dispositivo gemelo](#device-twin), pero con una mayor granularidad.

En [IoT Edge](#iot-edge), un módulo es un contenedor de Docker que se puede implementar en [dispositivos de IoT Edge](#iot-edge-device). Realiza una tarea específica,tal como ingerir un mensaje desde un dispositivo, transformar un mensaje o enviarlo a una instancia de IoT Hub. Se comunica con otros módulos y envía datos al [entorno de ejecución de IoT Edge](#iot-edge-runtime).

Se aplica a: IoT Hub, IoT Edge, desarrollador de dispositivos

### <a name="module-identity"></a>Identidad de módulo

Un identificador único asignado a cada [módulo](#module) que pertenece a un [dispositivo](#device). Las identidades de módulo también se registran en el [registro de identidades](#identity-registry).

La identidad del módulo detalla las credenciales de seguridad que el módulo usa para autenticarse en [IoT Hub](#iot-hub) o, en el caso de los módulos de [IoT Edge](#iot-edge), en el [centro de IoT Edge](#iot-edge-hub).

Se aplica a: IoT Hub, IoT Edge, desarrollador de dispositivos

### <a name="module-image"></a>Imagen de módulo

La imagen de Docker que el [entorno de ejecución de IoT Edge](#iot-edge-runtime) usa para crear instancias del [módulo](#module).

Se aplica a: IoT Edge

### <a name="module-twin"></a>Módulo gemelo

De forma similar al [dispositivo gemelo](#device-twin), un [módulo](#module) gemelo es un documento JSON que almacena información sobre el estado de los módulos, como metadatos, [configuraciones](#configuration) y condiciones. [IoT Hub](#iot-hub) persiste un módulo gemelo para cada [identidad de módulo](#module-identity) que ha aprovisionado en una [identidad del dispositivo](#device-identity) del centro de IoT. Los módulos gemelos permiten sincronizar las condiciones de módulo y las configuraciones entre el módulo y el back-end de [soluciones](#solution). Puede consultar los módulos gemelos para buscar módulos específicos y consultar el estado de las operaciones de larga duración.

Se aplica a: IoT Hub

## <a name="o"></a>O

### <a name="ontology"></a>Ontología

En el contexto de [Digital Twins](#digital-twin), un conjunto de [modelos](#model) para un dominio determinado, como la propiedad inmobiliaria, las ciudades inteligentes, los sistemas de IoT, las redes de energía, etc. Las ontologías se suelen usar como esquemas para gráficos de conocimiento como los de [Azure Digital Twins](#azure-digital-twins), ya que proporcionan un punto de partida basado en los procedimientos recomendados y estándares del sector.

[Más información](../digital-twins/concepts-ontologies.md)

Se aplica a: Digital Twins

### <a name="operations-monitoring"></a>Supervisión de operaciones

Una característica de [IoT Hub](#iot-hub) que permite supervisar el estado de las operaciones de su centro de IoT en tiempo real. IoT Hub realiza el seguimiento de eventos a través de varias categorías de operaciones. Se puede optar por que los eventos de una o varias categorías se envíen a un [punto de conexión](#endpoint) del IoT Hub para su procesamiento. Los usuarios pueden supervisar los datos en busca de errores o configurar un procesamiento más complejo basado en patrones de datos.

Se aplica a: IoT Hub

## <a name="p"></a>P

### <a name="physical-device"></a>Dispositivo físico

Un [dispositivo](#device) IoT real que se conecta a un [centro de IoT](#iot-hub). Para mayor comodidad, muchos tutoriales e inicios rápidos ejecutan código de dispositivo IoT en una máquina de escritorio en lugar de en un dispositivo físico.

Se aplica a: IoT Hub, IoT Central, desarrollador de dispositivos, IoT Edge

### <a name="primary-and-secondary-keys"></a>Claves principales y secundarias

Al conectarse a un [punto de conexión](#endpoint) orientado a [dispositivo](#device) o a servicio en un [centro de IoT](#iot-hub) o aplicación de IoT Central, la [cadena de conexión](#connection-string) incluye la clave para concederle acceso. Al agregar un dispositivo al [registro de identidad](#identity-registry) o una [directiva de acceso compartido](#shared-access-policy) al centro, el servicio genera una clave principal y una secundaria. Tener dos claves permite pasar de una clave a otra cuando se actualiza una clave, sin perder acceso al centro de IoT o aplicación de IoT Central.

Se aplica a: IoT Hub, IoT Central

### <a name="properties"></a>Propiedades

En el contexto de un [gemelo digital](#digital-twin), los campos de datos definidos en una [interfaz](#interface) que representan algún estado persistente del gemelo digital. Las propiedades se pueden declarar como de solo lectura o de escritura. Las propiedades de solo lectura, como el número de serie, se establecen mediante código que se ejecuta en el propio [dispositivo IoT Plug and Play](#iot-plug-and-play-device). Las propiedades grabables, como un umbral de alarma, se establecen normalmente desde la [solución](#solution) de IoT basada en la nube.

Se aplica a: IoT Hub, IoT Central, Digital Twins, desarrollador de dispositivos

### <a name="property-change-event"></a>Evento de cambio de propiedad

Evento que es el resultado de un cambio de propiedad en un [gemela digital](#digital-twin).

Se aplica a: IoT Hub, IoT Central, Digital Twins

### <a name="protocol-gateway"></a>Puerta de enlace de protocolo

Una [puerta de enlace](#gateway) implementada normalmente en la nube para proporcionar servicios de traducción de protocolo para [dispositivos](#device) que se conectan al [centro de IoT](#iot-hub) o aplicación de IoT Central.

Se aplica a: IoT Hub, IoT Central

## <a name="r"></a>R

### <a name="registration"></a>Registro

Un registro de un [dispositivo](#device) que se encuentra en el [registro de identidades](#identity-registry) de [IoT Hub](#iot-hub). El dispositivo se puede registrar directamente, o bien usar [Device Provisioning Service](#device-provisioning-service) para automatizar el registro de dispositivos.

Se aplica a: IoT Hub, IoT Central, servicio Device Provisioning

### <a name="registration-id"></a>Identificador de registro

Una [identidad del dispositivo](#device-identity) en el [servicio Device Provisioning](#device-provisioning-service). El id. de [registro](#registration) puede tener el mismo valor que la identidad del [dispositivo](#device).

Se aplica a: IoT Hub, IoT Central, servicio Device Provisioning

### <a name="relationship"></a>Relación

Se usa en el servicio [Azure Digital Twins](#azure-digital-twins) para conectar [gemelos digitales](#digital-twin) a los grafos de conocimiento que representan digitalmente todo el entorno físico. Los tipos de relaciones que pueden tener los gemelos se definen en el [modelo](#digital-twins-definition-language) de [lenguaje de definición de Digital Twins](#model).

Se aplica a: Digital Twins

### <a name="reported-configuration"></a>Configuración notificada

En el contexto de un [dispositivo gemelo](#device-twin), hace referencia al conjunto completo de [propiedades](#properties) y metadatos en el [dispositivo](#device) gemelo que deben notificarse al back-end de la [solución](#solution).

Se aplica a: IoT Hub, desarrollador de dispositivos

### <a name="reported-properties"></a>Propiedades notificadas

En el contexto de un [dispositivo gemelo](#device-twin), las [propiedades](#properties) notificadas son una subsección del [dispositivo](#device) gemelo. Solo el dispositivo puede establecer las propiedades notificadas, pero la [aplicación de back-end](#back-end-app) las puede leer y consultar.

Se aplica a: IoT Hub, desarrollador de dispositivos

### <a name="retry-policy"></a>Directiva de reintentos

Una manera para controlar los errores transitorios al conectarse a un servicio en la nube.

Se aplica a: IoT Hub, IoT Central, desarrollador de dispositivos

### <a name="routing-rule"></a>Regla de enrutamiento

Una característica de [IoT Hub](#iot-hub) que se usa para enrutar mensajes del [dispositivo a la nube](#device-to-cloud) a un [punto de conexión](#endpoint) integrado o a [puntos de conexión personalizados](#custom-endpoints) para que los procese el back-end de su [solución](#solution).

Se aplica a: IoT Hub

## <a name="s"></a>S

### <a name="saslplain"></a>SASL/PLAIN

Un protocolo que [Advanced Message Queueing Protocol](#advanced-message-queueing-protocol) usa para transferir tokens de seguridad.

[Más información](https://wikipedia.org/wiki/Simple_Authentication_and_Security_Layer)

Se aplica a: IoT Hub

### <a name="service-rest-api"></a>API de REST de servicio

Una API de REST puede usar en el back-end de la [solución](#solution) para administrar sus [dispositivos](#device). Por ejemplo, puede usar la API del servicio [IoT Hub](#iot-hub) para recuperar y actualizar las [propiedades](#properties) de [dispositivo gemelo](#device-twin), invocar [métodos directos](#direct-method) y programar [trabajos](#job). Normalmente, debe usar uno de los SDK de servicio de nivel superior.

Se aplica a: IoT Hub, IoT Central, servicio Device Provisioning, IoT Edge

### <a name="service-operations-endpoint"></a>Punto de conexión de las operaciones del servicio

Un [punto de conexión](#endpoint) que un administrador usa para administrar la configuración del servicio. Por ejemplo, en el [servicio Device Provisioning](#device-provisioning-service), se usa el punto de conexión de servicio para administrar las [inscripciones](#enrollment).

Se aplica a: IoT Hub, servicio Device Provisioning, IoT Edge, Digital Twins

### <a name="shared-access-policy"></a>Directiva de acceso compartido

Una manera de definir los permisos concedidos a cualquier persona que tenga una clave principal o secundaria válida asociada a esa directiva. Se pueden administrar las directivas de acceso compartido y las claves para el centro en el portal.

Se aplica a: IoT Hub, IoT Edge, servicio Device Provisioning

### <a name="shared-access-signature"></a>Firma de acceso compartido

Una firma de acceso compartido es un URI firmado que apunta a uno o varios recursos, como un [punto de conexión](#endpoint) de [centro de IoT](#iot-hub). El URI incluye un token que indica cómo el cliente puede acceder a los recursos. Uno de los parámetros de consulta, la firma, se construye a partir de parámetros SAS y se firma con la clave que se usó para crear la SAS. Azure Storage utiliza esta firma para autorizar el acceso al recurso de almacenamiento.

Se aplica a: IoT Hub, Digital Twins, IoT Central, IoT Edge

### <a name="simulated-device"></a>Dispositivo simulado

Para mayor comodidad, muchos de los tutoriales e inicios rápidos ejecutan código de [dispositivo](#device) con sensores simulados en la máquina de desarrollo local. En cambio, un [dispositivo físico](#physical-device), como un MXCHIP, tiene sensores reales y se conecta a un [centro de IoT](#iot-hub).

Se aplica a: IoT Hub, IoT Central, desarrollador de dispositivos, IoT Edge, Digital Twins, servicio Device Provisioning

### <a name="solution"></a>Solución

En el contexto de IoT, una *solución* normalmente hace referencia a una solución de IoT que incluye elementos como [dispositivos](#device), [aplicaciones de dispositivo](#device-app), un [centro de IoT](#iot-hub), otros servicios de Azure y [aplicaciones de back-end](#back-end-app).

Se aplica a: IoT Hub, IoT Central, servicio Device Provisioning, IoT Edge, Digital Twins

### <a name="system-properties"></a>Propiedades del sistema

En el contexto de un [dispositivo gemelo](#device-twin), las [propiedades](#properties) de solo lectura que incluyen información sobre el uso de [dispositivos](#device), como el último estado de conexión y tiempo de actividad.

Se aplica a: IoT Hub

## <a name="t"></a>T

### <a name="tag"></a>Etiqueta

En el contexto de un [dispositivo gemelo](#device-twin), las etiquetas son metadatos de [dispositivos](#device) almacenados y recuperados por el back-end de [solución](#solution) en forma de documento JSON. Las etiquetas no son visibles para las aplicaciones en un dispositivo.

Se aplica a: IoT Hub

### <a name="target-condition"></a>Condición de destino

En una implementación de [IoT Edge](#iot-edge), la condición de destino selecciona los [dispositivos](#device) de destino de la implementación. La condición de destino se evalúa continuamente para incluir todos los nuevos dispositivos que cumplen los requisitos o desconectar los dispositivos que ya no lo hacen.

Se aplica a: IoT Edge

### <a name="telemetry"></a>Telemetría

Los datos, como la velocidad del viento o la temperatura, enviados a un [centro de IoT](#iot-hub) recopilados por un [dispositivo](#device) de sus sensores.

A diferencia de las [propiedades](#properties), la telemetría no se almacena en un [gemelo digital](#digital-twin); es una secuencia de eventos de datos con límites temporales que se deben controlar a medida que se producen.

En IoT Plug and Play y [Azure Digital Twins](#azure-digital-twins), los campos de telemetría definidos en una [interfaz](#interface) representan medidas. Estas medidas suelen ser valores, como por ejemplo las lecturas de los sensores, que envían los dispositivos, como los [dispositivos IoT Plug and Play](#iot-plug-and-play-device) en forma de flujo de datos.

Se aplica a: IoT Hub, IoT Central, Digital Twins, IoT Edge, desarrollador de dispositivos

### <a name="telemetry-event"></a>Evento de telemetría

Un evento de un [centro de IoT](#iot-hub) que indica la llegada de datos de [telemetría](#telemetry).

Se aplica a: IoT Hub

### <a name="twin-queries"></a>Consultas gemelas

Una característica de [IoT Hub](#iot-hub) que le permite usar un lenguaje de consulta parecido a SQL para recuperar información de los [dispositivos gemelos](#device-twin) o [módulos gemelos](#module-twin).

Se aplica a: IoT Hub

### <a name="twin-synchronization"></a>Sincronización de gemelos

El proceso de [IoT Hub](#iot-hub) que usa las [propiedades deseadas](#desired-properties) en los [dispositivos gemelos](#device-twin) o [módulos gemelos](#module-twin) para configurar los [dispositivos](#device) o [módulos](#module) y recuperar las [propiedades notificadas](#reported-properties) para almacenarlas en el gemelo.

Se aplica a: IoT Hub

## <a name="u"></a>U

### <a name="upstream-service"></a>Servicios ascendentes

Un término relativo que describe los servicios que aportan datos al contexto actual. Por ejemplo, en el contexto de [Azure Digital Twins](#azure-digital-twins), [IoT Hub](#iot-hub) se considera un servicio ascendente porque los datos fluyen de IoT Hub a Azure [Digital Twins](#digital-twin).

Se aplica a: IoT Hub, IoT Central, Digital Twins

