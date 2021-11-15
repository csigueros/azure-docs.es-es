---
title: Recomendaciones de seguridad para IoT Hub
description: Sepa en qué consiste el concepto de recomendaciones de seguridad y cómo se usan en Defender para IoT Hub.
ms.topic: conceptual
ms.date: 11/09/2021
ms.openlocfilehash: 5a4d93c30a01cc0fa93cbb3e4835bf8a86ccc622
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/11/2021
ms.locfileid: "132331592"
---
# <a name="security-recommendations-for-iot-hub"></a>Recomendaciones de seguridad para IoT Hub

Defender para IoT analiza los dispositivos IoT y los recursos de Azure y hace recomendaciones de seguridad para reducir la superficie expuesta a ataques.
Las recomendaciones de seguridad requieren una acción y su objetivo es ayudar a los clientes a cumplir los procedimientos recomendados de seguridad.

En este artículo, encontrará una lista de recomendaciones que se pueden activar en IoT Hub.

## <a name="built-in-recommendations-in-iot-hub"></a>Recomendaciones integradas en IoT Hub

Las alertas de recomendación proporcionan información y sugerencias relativas a las acciones para mejorar la posición de seguridad del entorno.

### <a name="high-severity"></a>Gravedad alta

| severity | Nombre | Origen de datos | Descripción | RecommendationType |
|--|--|--|--|--|
| Alto | Varios dispositivos usan las mismas credenciales de autenticación | IoT Hub | Hay varios dispositivos que usan las mismas credenciales de autenticación de IoT Hub. Podría ser indicativo de que un dispositivo ilegítimo está suplantando uno legítimo, y también expone el riesgo de suplantación de dispositivo por parte de un actor malintencionado. | IoT_SharedCredentials |
| Alto | Permisos de alto nivel configurados en el modelo gemelo de IoT Edge para el módulo de IoT Edge | IoT Hub | El módulo de IoT Edge está configurado para ejecutarse en modo privilegiado, con amplias capacidades de Linux o con acceso de red de nivel de host (enviar y recibir datos a la máquina host). | IoT_PrivilegedDockerOptions |

### <a name="medium-severity"></a>Gravedad media

| severity | Nombre | Origen de datos | Descripción | RecommendationType |
|--|--|--|--|--|
| Media | Entidad de servicio no usada con el repositorio de ACR | IoT Hub | El esquema de autenticación utilizado para extraer el módulo de IoT Edge de un repositorio de ACR no usa la autenticación de entidad de servicio. | IoT_ACRAuthentication |
| Media | Actualización necesaria del conjunto de cifrado de TLS | IoT Hub | Se han detectado configuraciones de TLS no seguras. Se recomienda actualizar el conjunto de cifrado de TLS inmediatamente. | IoT_VulnerableTLSCipherSuite |
| Media | La directiva del filtro de IP predeterminada debe ser Denegar. | IoT Hub | De manera predeterminada, la configuración del filtro de IP necesita reglas definidas para el tráfico permitido y debe denegar el resto del tráfico. | IoT_IPFilter_DenyAll |
| Media | La regla del filtro IP incluye un amplio intervalo de IP. | IoT Hub | Un intervalo IP de origen de la regla de filtro IP permitido es demasiado grande. Las reglas excesivamente permisivas podrían exponer su instancia de IoT Hub a actores malintencionados. | IoT_IPFilter_PermissiveRule |
| Media | Reglas recomendadas para el filtro de IP | IoT Hub | Le recomendamos que cambie el filtro de IP a las siguientes reglas, las reglas que se obtienen con el comportamiento de IoT Hub. | IoT_RecommendedIpRulesByBaseLine |
| Media | La configuración de módulo de SecurityGroup es incoherente | IoT Hub | Dentro de este grupo de seguridad de dispositivos, un dispositivo con anomalías tiene una configuración de módulo de IoT Edge incoherente en comparación con el resto del grupo de seguridad. | IoT_InconsistentModuleSettings |

### <a name="low-severity"></a>Low severity

| severity | Nombre | Origen de datos | Descripción | RecommendationType |
|--|--|--|--|--|
| Bajo | La memoria de IoT Edge Hub se puede optimizar | IoT Hub | Optimice el uso de memoria de su instancia de IoT Edge Hub desactivando los encabezados de protocolo para los protocolos que no utilicen los módulos de Edge de la solución. | IoT_EdgeHubMemOptimize |
| Bajo | Sin registro configurado para el módulo de IoT Edge | IoT Hub | El registro está deshabilitado para este módulo de IoT Edge. | IoT_EdgeLoggingOptions |

## <a name="next-steps"></a>Pasos siguientes

- Más información sobre [Alertas de seguridad clásicas de dispositivos de Defender para IoT](agent-based-security-alerts.md)
