---
title: Recomendaciones de seguridad para IoT Hub
description: Sepa en qué consiste el concepto de recomendaciones de seguridad y cómo se usan en Defender para IoT Hub.
ms.topic: conceptual
ms.date: 09/26/2021
ms.openlocfilehash: 6e4af6e4cadbd45e91219d2fce6d87c4fb555ece
ms.sourcegitcommit: 10029520c69258ad4be29146ffc139ae62ccddc7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/27/2021
ms.locfileid: "129083341"
---
# <a name="security-recommendations-for-iot-hub"></a>Recomendaciones de seguridad para IoT Hub

Defender para IoT analiza los dispositivos IoT y los recursos de Azure y hace recomendaciones de seguridad para reducir la superficie expuesta a ataques.
Las recomendaciones de seguridad requieren una acción y su objetivo es ayudar a los clientes a cumplir los procedimientos recomendados de seguridad.

En este artículo, encontrará una lista de recomendaciones que se pueden activar en IoT Hub.

## <a name="built-in-recommendations-in-iot-hub"></a>Recomendaciones integradas en IoT Hub

Las alertas de recomendación proporcionan información y sugerencias relativas a las acciones para mejorar la posición de seguridad del entorno.

| severity | Nombre | Origen de datos | Descripción |
|--|--|--|--|
| Alto | Credenciales de autenticación idénticas utilizadas por varios dispositivos | IoT Hub | Hay varios dispositivos que usan las mismas credenciales de autenticación de IoT Hub. Este proceso puede indicar que hay un dispositivo ilegítimo que suplanta un dispositivo legítimo. El uso de credenciales duplicadas aumenta el riesgo de suplantación del dispositivo a manos de un individuo malintencionado. |
| Media | La directiva del filtro de IP predeterminada debe ser Denegar. | IoT Hub | La configuración del filtro IP debe tener reglas definidas para el tráfico permitido y debe denegar el resto del tráfico de forma predeterminada. |
| Media | La regla del filtro IP incluye un amplio intervalo de IP | IoT Hub | Un intervalo IP de origen de la regla de filtro IP permitido es demasiado grande. Las reglas excesivamente permisivas podrían exponer su instancia de IoT Hub a agentes malintencionados. |
| Bajo | Habilitar registros de diagnóstico en IoT Hub | IoT Hub | Habilite los registros y consérvelos por hasta un año. Conservar los registros le permite volver a crear seguimientos de actividad con fines de investigación cuando se produce un incidente de seguridad o se pone en peligro la red. |
| Alto | Varios dispositivos usan las mismas credenciales de autenticación | IoT Hub | Hay varios dispositivos que usan las mismas credenciales de autenticación de IoT Hub. Podría ser indicativo de que un dispositivo ilegítimo está suplantando uno legítimo, y también expone el riesgo de suplantación de dispositivo por parte de un actor malintencionado. |
| Alto | Permisos de alto nivel configurados en el modelo gemelo de IoT Edge para el módulo de IoT Edge | IoT Hub | El módulo de IoT Edge está configurado para ejecutarse en modo privilegiado, con amplias capacidades de Linux o con acceso de red de nivel de host (enviar y recibir datos a la máquina host). |
| Media | Entidad de servicio no usada con el repositorio de ACR | IoT Hub | El esquema de autenticación utilizado para extraer el módulo de IoT Edge de un repositorio de ACR no usa la autenticación de entidad de servicio. |
| Media | Actualización necesaria del conjunto de cifrado de TLS | IoT Hub | Se han detectado configuraciones de TLS no seguras. Se recomienda actualizar el conjunto de cifrado de TLS inmediatamente. |
| Media | La directiva del filtro de IP predeterminada debe ser Denegar. | IoT Hub | De manera predeterminada, la configuración del filtro de IP necesita reglas definidas para el tráfico permitido y debe denegar el resto del tráfico. |
| Media | La regla del filtro IP incluye un amplio intervalo de IP. | IoT Hub | Un intervalo IP de origen de la regla de filtro IP permitido es demasiado grande. Las reglas excesivamente permisivas podrían exponer su instancia de IoT Hub a actores malintencionados. |
| Media | La configuración de módulo de SecurityGroup es incoherente | IoT Hub | Dentro de este grupo de seguridad de dispositivos, un dispositivo con anomalías tiene una configuración de módulo de IoT Edge incoherente en comparación con el resto del grupo de seguridad. |
| Bajo | La memoria de IoT Edge Hub se puede optimizar | IoT Hub | Optimice el uso de memoria de su instancia de IoT Edge Hub desactivando los encabezados de protocolo para los protocolos que no utilicen los módulos de Edge de la solución. |
| Bajo | Sin registro configurado para el módulo de IoT Edge | IoT Hub | El registro está deshabilitado para este módulo de IoT Edge. |

## <a name="next-steps"></a>Pasos siguientes

- [Información general](overview.md) del servicio Defender para IoT
- Aprenda a [acceder a los datos de seguridad](how-to-security-data-access.md)
- Más información sobre cómo [investigar un dispositivo](how-to-investigate-device.md)
