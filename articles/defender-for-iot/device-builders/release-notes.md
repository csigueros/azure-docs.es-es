---
title: Novedades de Azure Defender para IoT para creadores de dispositivos
description: Obtenga información sobre las últimas versiones y las características más recientes de Defender para IoT para creadores de dispositivos.
ms.topic: overview
ms.date: 11/02/2021
ms.openlocfilehash: 4d6d931082f55a08667da20283d28c655a9fb584
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/03/2021
ms.locfileid: "131438595"
---
# <a name="whats-new-in-azure-defender-for-iot-for-device-builders"></a>Novedades de Azure Defender para IoT para creadores de dispositivos  

[!INCLUDE [Banner for top of topics](../includes/banner.md)]

En este artículo se enumeran las nuevas características y mejoras de características de Defender para IoT.

Las características mencionadas se encuentran en VERSIÓN PRELIMINAR. Los [Términos de uso complementarios para las versiones preliminares de Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) incluyen otros términos legales que se aplican a las características de Azure que se encuentran en la versión beta, en versión preliminar o que todavía no se han publicado con disponibilidad general.

## <a name="versioning-and-support-for-azure-defender-for-iot"></a>Control de versiones y compatibilidad con Azure Defender para IoT

A continuación se enumeran la compatibilidad, las directivas de cambios importantes de Defender para IoT y las versiones de Azure Defender para IoT que están disponibles actualmente.

## <a name="september-2021"></a>Septiembre de 2021

**Versión 3.11**:

- **[Recopilador de inicios de sesión](concept-event-aggregation.md#login-collector-event-based-collector)** : los recopiladores de inicios de sesión recopilan inicios de sesión de usuario, cierres de sesión e intentos de inicio de sesión con errores. Por ejemplo, SSH y telnet.

- **[Recopilador de información del sistema](concept-event-aggregation.md#system-information-trigger-based-collector)** : el recopilador de información del sistema recopila información relacionada con el sistema operativo y los detalles de hardware del dispositivo.

- **[Agregación de eventos](concept-event-aggregation.md#how-does-event-aggregation-work)** : el agente de Defender para IoT agrega eventos como procesos, inicios de sesión y eventos de red, lo que reduce el número de mensajes enviados y los costos, todo ello manteniendo la seguridad del dispositivo.  

- **[Configuración de gemelos](concept-micro-agent-configuration.md)** : el comportamiento del microagente se configura mediante un conjunto de propiedades del módulo gemelo. (Por ejemplo, la frecuencia de envío de eventos y el modo de agregación). Puede configurar el microagente para que se adapte mejor a sus necesidades.

## <a name="march-2021"></a>Marzo de 2021

### <a name="device-builder---new-micro-agent-public-preview"></a>Generador de dispositivos: nuevo microagente (versión preliminar pública)

Hay disponible un nuevo módulo del generador de dispositivos. El módulo, denominado microagente, permite lo siguiente:

- **Integración con Azure IoT Hub y Azure Defender para IoT**: logre una mejor seguridad para el punto de conexión directamente en los dispositivos de IoT mediante la integración con la opción de supervisión que proporcionan Azure IoT Hub y Azure Defender para IoT.

- **Opciones de implementación flexibles compatibles con los sistemas operativos de IoT estándar**: se pueden implementar como un paquete binario o como código fuente modificable, compatibles con los sistemas operativos de IoT estándar, como Linux y Azure RTOS.

- **Requisitos mínimos de recursos sin dependencias del kernel de sistema operativo**: superficie de memoria pequeña, consumo de CPU bajo y ninguna dependencia del kernel del sistema operativo.

- **Administración de la postura de seguridad**: supervise de forma proactiva la posición de seguridad de los dispositivos IoT.

- **Detección de amenazas de IoT/OT continua y en tiempo real**: detecte amenazas como redes de robots (botnets), intentos por fuerza bruta, mineros de criptomonedas y actividad de red sospechosa.

La documentación de Defender-IoT-micro-agent se transferirá a la carpeta *Agent-based solution for device builders>Classic* (Solución basada en agentes para generadores de dispositivos > Clásica).

Este conjunto de características está disponible con la versión preliminar pública en la nube actual.

## <a name="next-steps"></a>Pasos siguientes

[Qué es una solución basada en agente para los generadores de dispositivos](architecture-agent-based.md)
