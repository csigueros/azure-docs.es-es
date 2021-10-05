---
title: Conceptos de arquitectura en Azure IoT Central | Microsoft Docs
description: En este artículo se presentan conceptos clave relacionados con la arquitectura de Azure IoT Central
author: dominicbetts
ms.author: dobett
ms.date: 08/31/2021
ms.topic: conceptual
ms.service: iot-central
services: iot-central
ms.openlocfilehash: 724c7e65d04b635dcaa635d29483da756cf47bfc
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/24/2021
ms.locfileid: "128675884"
---
# <a name="azure-iot-central-architecture"></a>Arquitectura de Azure IoT Central

En este artículo se proporciona información general sobre los conceptos clave de una arquitectura de la solución de IoT Central.

:::image type="content" source="media/concepts-architecture/architecture.png" alt-text="Arquitectura de alto nivel de una solución de IoT Central" border="false":::

Una aplicación de IoT Central:

- Permite administrar los dispositivos IoT de la solución.
- Permite ver y analizar los datos de los dispositivos.
- Puede exportar a otros servicios que forman parte de la solución e integrarse con ellos.

## <a name="iot-central"></a>IoT Central

IoT Central es un entorno listo para el desarrollo de soluciones de IoT. Se trata de una solución de IoT de plataforma como servicio (PaaS), cuya interfaz principal es una interfaz de usuario web. También hay una [API REST](#rest-api) que le permite interactuar con la aplicación mediante programación.

En esta sección se describen las funcionalidades clave de una aplicación de IoT Central.

### <a name="manage-devices"></a>Administrar los dispositivos

IoT Central permite administrar la flota de [dispositivos IoT](#devices) que envían datos a la solución. Por ejemplo, puede:

- Controlar los dispositivos que se pueden [conectar](concepts-get-connected.md) a la aplicación y cómo se autentican.
- Usar [plantillas de dispositivo](concepts-device-templates.md) para definir los tipos de dispositivo que se pueden conectar a la aplicación.
- Establecer propiedades o llamar comandos en los dispositivos conectados para poder administrarlos. Por ejemplo, establezca una propiedad de temperatura de destino para un dispositivo termostato o llame a un comando para desencadenar un dispositivo a fin de actualizar el firmware. Puede establecer propiedades y llamar comandos en:
  - Dispositivos individuales a través de una interfaz de usuario web [personalizable](concepts-device-templates.md#views).
  - Varios dispositivos con [trabajos](howto-manage-devices-in-bulk.md) programados o a petición.
- Conservar [metadatos de dispositivo como](concepts-device-templates.md#cloud-properties) la dirección del cliente o la fecha de la última revisión.

### <a name="view-and-analyze-data"></a>Visualización y análisis de los datos

En una aplicación de IoT Central, puede ver y analizar datos para dispositivos individuales o para datos agregados de varios dispositivos:

- Use plantillas de dispositivo para definir [vistas personalizadas](howto-set-up-template.md#views) para dispositivos individuales de tipos específicos. Por ejemplo, puede trazar la temperatura a lo largo del tiempo para un termostato individual o mostrar la ubicación en vivo de un camión de entrega.
- Use el [análisis](tutorial-use-device-groups.md) integrado para ver los datos agregados de varios dispositivos. Por ejemplo, puede ver la ocupación total de varias tiendas minoristas o identificar las tiendas que tienen las tasas de ocupación más altas o más bajas.
- Cree paneles [personalizados](howto-manage-dashboards.md) que lo ayuden a administrar los dispositivos. Por ejemplo, puede agregar mapas, iconos y gráficos para mostrar la telemetría de los dispositivos.  

### <a name="secure-your-solution"></a>Protección de una solución

En una aplicación de IoT Central puede administrar estos aspectos de seguridad de la solución:

- [Conectividad de dispositivos](concepts-get-connected.md): cree, revoque y actualice las claves de seguridad que los dispositivos usan para establecer una conexión a la aplicación.
- [Integraciones de aplicaciones](howto-authorize-rest-api.md#get-an-api-token): cree, revoque y actualice las claves de seguridad que otras aplicaciones usan para establecer conexiones seguras con la aplicación en cuestión.
- [Administración de usuarios](howto-manage-users-roles.md): administre los usuarios que pueden iniciar sesión en la aplicación y los roles que determinan los permisos con los que cuentan dichos usuarios.
- [Organizaciones](howto-create-organizations.md): defina una jerarquía con la finalidad de administrar qué usuarios pueden ver qué dispositivos en la aplicación de IoT Central.

### <a name="rest-api"></a>API DE REST

Cree integraciones que permitan que otras aplicaciones y servicios administren la aplicación. Por ejemplo, [administre los dispositivos](howto-control-devices-with-rest-api.md) mediante programación en la aplicación o sincronice la [información del usuario](howto-manage-users-roles-with-rest-api.md) con un sistema externo.

## <a name="devices"></a>Dispositivos

Los dispositivos recopilan datos de sensores para enviarlos como un flujo de telemetría a una aplicación de IoT Central. Por ejemplo, una unidad de refrigeración envía un flujo de valores de temperatura o un camión de entrega transmite su ubicación.

Un dispositivo puede usar propiedades para notificar su estado, como puede ser si una válvula está abierta o cerrada. Una aplicación de IoT Central también puede usar propiedades para establecer el estado del dispositivo; por ejemplo, establecer una temperatura de destino para un termostato.

IoT Central también puede llamar comandos en el dispositivo con el fin de controlarlo. Por ejemplo, indicar a un dispositivo que descargue e instale una actualización de firmware.

La [telemetría, las propiedades y los comandos](concepts-telemetry-properties-commands.md) que un dispositivo implementa se conocen, de manera conjunta, como las funcionalidades del dispositivo. Estas funcionalidades se definen en un modelo compartido entre el dispositivo y la aplicación de IoT Central. En IoT Central, este modelo forma parte de la plantilla de dispositivo que define un tipo específico de dispositivo.

La [implementación del dispositivo](tutorial-connect-device.md) debe seguir las [convenciones de IoT Plug and Play](../../iot-develop/concepts-convention.md) para asegurarse de que se puede comunicar con IoT Central. Para más información, consulte los [SDK y ejemplos](../../iot-develop/libraries-sdks.md) de los distintos lenguajes.

Los dispositivos se conectan a IoT Central mediante uno de estos protocolos admitidos: [MQTT, AMQP o HTTP](../../iot-hub/iot-hub-devguide-protocols.md).

## <a name="gateways"></a>Puertas de enlace

Las puertas de enlace de dispositivos locales resultan útiles en varios escenarios, como:

- Es posible que los dispositivos no puedan conectarse directamente a IoT Central porque no se pueden conectar a Internet. Por ejemplo, puede que tenga una colección de sensores de ocupación Bluetooth habilitados que se deben conectar a través de una puerta de enlace.
- Es posible que los dispositivos generen grandes cantidades de datos. Si desea reducir los costos, puede combinar o agregar los datos en una puerta de enlace local antes de enviarlos a la aplicación de IoT Central.
- La solución puede requerir respuestas rápidas a cualquier anomalía en los datos. En una puerta de enlace, puede ejecutar reglas para identificar anomalías y realizar una acción de manera local sin necesidad de enviar datos a la aplicación de IoT Central.

Para obtener más información, consulte [Conexión de dispositivos de Azure IoT Edge a una aplicación de Azure IoT Central](concepts-iot-edge.md).

## <a name="data-export"></a>Exportación de datos

Si bien IoT Central tiene características integradas de análisis, puede exportar los datos a otros servicios y aplicaciones. Entre los motivos para exportar los datos se encuentran los siguientes:

### <a name="storage-and-analysis"></a>Almacenamiento y análisis

En el caso de almacenamiento a largo plazo y control sobre las directivas de archivado y retención, puede [exportar sus datos de manera continua](howto-export-data.md) a otros destinos de almacenamiento. El uso de almacenamiento independiente también le permite usar otras herramientas de análisis para obtener información y ver los datos de la solución.

### <a name="business-automation"></a>Automatización de la empresa

Las [reglas](howto-configure-rules-advanced.md) de IoT Central le permiten desencadenar acciones externas, como enviar un correo electrónico o activar un evento, como respuesta a ciertas condiciones dentro de IoT Central. Por ejemplo, puede enviar una notificación a un ingeniero en caso de que la temperatura ambiente de un dispositivo alcance cierto umbral.

### <a name="additional-computation"></a>Cálculo adicional

Puede que necesite [transformar los datos o hacer cálculos en ellos](howto-transform-data.md) antes de que pueda usarlos en IoT Central o en otro servicio. Por ejemplo, podría agregar información meteorológica local a los datos de ubicación que notificó un camión de entrega.

## <a name="next-steps"></a>Pasos siguientes

Ahora que ha aprendido sobre la arquitectura de Azure IoT Central, el siguiente paso sugerido es obtener información sobre [la conectividad de dispositivos](concepts-get-connected.md) en Azure IoT Central.
