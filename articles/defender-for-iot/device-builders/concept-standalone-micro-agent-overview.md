---
title: Introducción al microagenrte independiente (versión preliminar)
description: Los agentes de seguridad de Azure Defender para IoT permiten compilar la seguridad directamente en los nuevos dispositivos IoT y en proyectos de Azure IoT.
ms.date: 1/19/2021
ms.topic: article
ms.openlocfilehash: 5f8621c7d96d802ad2d32a6c71321bcd68854233
ms.sourcegitcommit: a038863c0a99dfda16133bcb08b172b6b4c86db8
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 06/29/2021
ms.locfileid: "113018202"
---
# <a name="standalone-micro-agent-overview-preview"></a>Introducción al microagenrte independiente (versión preliminar)

La seguridad es una preocupación casi universal para los implementadores de IoT. Los dispositivos IoT tienen necesidades únicas de supervisión de puntos de conexión, administración de la posición de seguridad y detección de amenazas, todo ello con requisitos de rendimiento muy concretos. 

Los agentes de seguridad de Azure Defender para IoT permiten compilar la seguridad directamente en los nuevos dispositivos IoT y en proyectos de Azure IoT. El micro-agente tiene opciones de implementación flexibles, incluida la capacidad de implementarse como un paquete binario o de modificar código fuente. Y está disponible para los sistemas operativos de IoT estándar como Linux y Azure RTOS. 

El micro-agente de Azure Defender para IoT proporciona visibilidad de los puntos de conexión en la administración de la posición de seguridad, detección de amenazas e integración en otras herramientas de seguridad de Microsoft para la administración de la seguridad unificada. 

## <a name="security-posture-management"></a>Administración de la posición de seguridad 

Supervise de forma proactiva la posición de seguridad de los dispositivos IoT. Azure Defender para IoT proporciona recomendaciones de posición de seguridad basadas en el banco de pruebas de CIS, junto con recomendaciones específicas del dispositivo. Obtenga visibilidad sobre la seguridad del sistema operativo, incluida la configuración del sistema operativo, la del firewall y los permisos. 

## <a name="endpoint-iot-and-ot-threat-detection"></a>Detección de amenazas de IoT y OT de punto de conexión 

Detecte amenazas como redes de robots (botnets), intentos de fuerza bruta, mineros de criptografía y actividades de red sospechosas. Cree alertas personalizadas para dirigirse a las amenazas más importantes dentro de la organización. 

## <a name="flexible-distribution-and-deployment-models"></a>Modelos flexibles de distribución e implementación 

El microagente de Azure Defender para IoT incluye código fuente, lo que le permite incorporarlo al firmware o personalizarlo para incluir solo lo que necesite. El microagente también está disponible como un paquete binario, o bien se integra directamente en otras soluciones de Azure IoT. 

## <a name="meets-the-needs-of-your-iot-devices-with-minimal-impact"></a>Cubre las necesidades de los dispositivos IoT, con un impacto mínimo 

El microagente de Azure Defender para IoT es fácil de implementar y tiene un impacto mínimo en el rendimiento del punto de conexión. Con el microagente de Defender para IoT se puede:

- **Realizar una optimización para mejorar el rendimiento**: El microagente de Azure Defender para IoT tiene una superficie de memoria pequeña y un bajo consumo de CPU.  

- **Plug and play**: no hay dependencias específicas del kernel del sistema operativo ni compatibilidad necesaria con todos los sistemas operativos de IoT principales. El microagente de Azure Defender para IoT se adapta a los dispositivos, independientemente del lugar en que se encuentren. 

- **Implementación flexible**: como agente independiente, el microagente de Azure Defender para IoT admite diferentes modelos de distribución e implementación flexible.

## <a name="next-steps"></a>Pasos siguientes

Consulte [Métodos de autenticación de microagentes (versión preliminar)](concept-security-agent-authentication.md).
