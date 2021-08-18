---
title: Recomendaciones de seguridad para IoT Hub
description: Sepa en qué consiste el concepto de recomendaciones de seguridad y cómo se usan en Defender para IoT Hub.
ms.topic: conceptual
ms.date: 02/16/2021
ms.openlocfilehash: a9e33248354aab659694e39df605cc070fdaaf73
ms.sourcegitcommit: a038863c0a99dfda16133bcb08b172b6b4c86db8
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 06/29/2021
ms.locfileid: "113018281"
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

## <a name="next-steps"></a>Pasos siguientes

- [Información general](overview.md) del servicio Defender para IoT
- Aprenda a [acceder a los datos de seguridad](how-to-security-data-access.md)
- Más información sobre cómo [investigar un dispositivo](how-to-investigate-device.md)
