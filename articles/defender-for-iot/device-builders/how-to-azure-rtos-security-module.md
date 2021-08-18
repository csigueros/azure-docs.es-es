---
title: Configuración y personalización del microagente de Defender para IoT para Azure RTOS
description: Aprenda a configurar y personalizar el microagente de Defender para IoT para Azure RTOS.
ms.topic: how-to
ms.date: 03/07/2021
ms.openlocfilehash: 38e0b7cd1226d26caf45ed454c93e253a2ead240
ms.sourcegitcommit: a2540262e05ffd4a4b059df0976940d60fabd125
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 07/01/2021
ms.locfileid: "113138430"
---
# <a name="configure-and-customize-defender-iot-micro-agent-for-azure-rtos"></a>Configuración y personalización del microagente de Defender para IoT para Azure RTOS

En este artículo se describe cómo configurar Defender-IoT-micro-agent para el dispositivo de Azure RTOS a fin de satisfacer los requisitos de red, ancho de banda y memoria.

Debe seleccionar un archivo de distribución de destino que tenga una extensión `*.dist`, en el directorio `netxduo/addons/azure_iot/azure_iot_security_module/configs`.  

Al utilizar un entorno de compilación de CMake, debe establecer un parámetro de línea de comandos en `IOT_SECURITY_MODULE_DIST_TARGET` para el valor elegido. Por ejemplo, `-DIOT_SECURITY_MODULE_DIST_TARGET=RTOS_BASE`.

En IAR u otro entorno de compilación que no sea CMake, debe agregar la ruta de acceso `netxduo/addons/azure_iot/azure_iot_security_module/inc/configs/<target distribution>/` a las rutas de acceso incluidas conocidas. Por ejemplo, `netxduo/addons/azure_iot/azure_iot_security_module/inc/configs/RTOS_BASE`.

Use el siguiente archivo para configurar el comportamiento del dispositivo.

**netxduo/addons/azure_iot/azure_iot_security_module/inc/configs/\<target distribution>/asc_config.h**

En un entorno de compilación de CMake, debe modificar la configuración predeterminada editando el archivo `netxduo/addons/azure_iot/azure_iot_security_module/configs/<target distribution>.dist`. Utilice el siguiente formato `set(ASC_XXX ON)` de CMake o el archivo siguiente `netxduo/addons/azure_iot/azure_iot_security_module/inc/configs/<target distribution>/asc_config.h` para todos los demás entornos. Por ejemplo, `#define ASC_XXX`.

El comportamiento predeterminado de cada configuración se proporciona en las tablas siguientes: 

## <a name="general"></a>General

| Nombre | Tipo | Valor predeterminado | Detalles |
| - | - | - | - |
| ASC_SECURITY_MODULE_ID | String | defender-iot-micro-agent | Identificador único del dispositivo.  |
| SECURITY_MODULE_VERSION_(MAJOR)(MINOR)(PATCH)  | Number | 3.2.1 | La versión. |
| ASC_SECURITY_MODULE_SEND_MESSAGE_RETRY_TIME  | Number  | 3 | Cantidad de tiempo que tarda Defender-IoT-micro-agent en enviar el mensaje de seguridad después de un error. (en segundos) |
| ASC_SECURITY_MODULE_PENDING_TIME  | Number | 300 | Tiempo de espera de Defender-IoT-micro-agent (en segundos). El estado cambiará a suspender si se supera el tiempo. |

## <a name="collection"></a>Colección

| Nombre | Tipo | Valor predeterminado | Detalles |
| - | - | - | - |
| ASC_FIRST_COLLECTION_INTERVAL | Number  | 30  | Desplazamiento del intervalo de recopilación de inicio del recopilador. Durante el inicio, el valor se agregará a la recopilación del sistema para evitar el envío simultáneo de mensajes desde varios dispositivos.  |
| ASC_HIGH_PRIORITY_INTERVAL | Number | 10 | Intervalo de grupo de prioridad alta del recopilador (en segundos). |
| ASC_MEDIUM_PRIORITY_INTERVAL | Number | 30 | Intervalo de grupo de prioridad media del recopilador (en segundos). |
| ASC_LOW_PRIORITY_INTERVAL | Number | 145,440  | Intervalo de grupo de prioridad baja del recopilador (en segundos). |

#### <a name="collector-network-activity"></a>Actividad de red del recopilador

Para personalizar la configuración de la actividad de red del recopilador, utilice lo siguiente:

| Nombre | Tipo | Valor predeterminado | Detalles |
| - | - | - | - |
| ASC_COLLECTOR_NETWORK_ACTIVITY_TCP_DISABLED | Boolean | false | Filtra la actividad de red `TCP`. |
| ASC_COLLECTOR_NETWORK_ACTIVITY_UDP_DISABLED | Boolean | false | Filtra los eventos de actividad de red `UDP`. |
| ASC_COLLECTOR_NETWORK_ACTIVITY_ICMP_DISABLED | Boolean | false | Filtra los eventos de actividad de red `ICMP`. |
| ASC_COLLECTOR_NETWORK_ACTIVITY_CAPTURE_UNICAST_ONLY | Boolean | true | Captura solo los paquetes entrantes de unidifusión. Cuando se establece en false, captura tanto la difusión como la multidifusión. |
| ASC_COLLECTOR_NETWORK_ACTIVITY_SEND_EMPTY_EVENTS  | Boolean  | false  | Envía eventos vacíos del recopilador. |
| ASC_COLLECTOR_NETWORK_ACTIVITY_MAX_IPV4_OBJECTS_IN_CACHE | Number | 64 | Número máximo de eventos de red IPv4 para almacenar en memoria. |
| ASC_COLLECTOR_NETWORK_ACTIVITY_MAX_IPV6_OBJECTS_IN_CACHE | Number | 64  | Número máximo de eventos de red IPv6 para almacenar en memoria. |

### <a name="collectors"></a>Recopiladores
| Nombre | Tipo | Valor predeterminado | Detalles |
| - | - | - | - |
| ASC_COLLECTOR_HEARTBEAT_ENABLED | Boolean | ACTIVAR | Habilita el recopilador de latidos. |
| ASC_COLLECTOR_NETWORK_ACTIVITY_ENABLED  | Boolean | ACTIVAR | Habilita el recopilador de actividades de red. |
| ASC_COLLECTOR_SYSTEM_INFORMATION_ENABLED | Boolean | ACTIVAR | Habilita el recopilador de información del sistema.  |

Otras marcas de configuración son avanzadas y tienen características no admitidas. Póngase en contacto con el soporte técnico para cambiarlas o para obtener más información.
 
## <a name="supported-security-alerts-and-recommendations"></a>Alertas y recomendaciones de seguridad admitidas

Defender-IoT-micro-agent para Azure RTOS admite alertas y recomendaciones de seguridad específicas. Asegúrese de [revisar y personalizar los valores de alerta y recomendación pertinentes](concept-rtos-security-alerts-recommendations.md) para el servicio.

## <a name="log-analytics-optional"></a>Log Analytics (opcional)

Puede habilitar y configurar Log Analytics para investigar eventos y actividades de dispositivo. Para más información, consulte cómo configurar y usar [Log Analytics](how-to-security-data-access.md#log-analytics) con el servicio Defender para IoT. 

## <a name="next-steps"></a>Pasos siguientes


- Revisión y personalización del microagente de Defender para IoT [para alertas y recomendaciones de seguridad de Azure RTOS](concept-rtos-security-alerts-recommendations.md)
- Consulte la [API del microagente de Defender para IoT para Azure RTOS](azure-rtos-security-module-api.md) según convenga.
