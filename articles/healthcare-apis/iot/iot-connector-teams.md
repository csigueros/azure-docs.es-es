---
title: 'Conector de IoT y Teams notificaciones: API de Azure Healthcare'
description: En este artículo, aprenderá a usar el conector de IoT y Teams notificaciones
author: msjasteppe
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: conceptual
ms.date: 11/05/2021
ms.author: jasteppe
ms.openlocfilehash: 5f2cb9c9a4e4ef20e489c36a92285a6f96f74282
ms.sourcegitcommit: 591ffa464618b8bb3c6caec49a0aa9c91aa5e882
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 11/06/2021
ms.locfileid: "131894809"
---
# <a name="iot-connector-and-microsoft-teams-notifications"></a>Conector de IoT y Microsoft Teams notificaciones

En este artículo, exploraremos el uso del conector de IoT y Microsoft Teams para las notificaciones.

## <a name="iot-connector-and-teams-notifications-reference-architecture"></a>Arquitectura de referencia de conectores Teams IoT y notificaciones de conexión

Al combinar el conector de IoT, un servicio Recursos Rápidos de Interoperabilidad en Salud (FHIR&#174;) y Teams, puede habilitar varias soluciones de atención. 

A continuación se muestra el conector de IoT Teams arquitectura conceptual de notificaciones para habilitar el conector de IoT, FHIR y Teams patient app. 

Incluso puede insertar paneles Power BI en el cliente Microsoft Teams cliente. Para obtener más información sobre la inserción de Power BI en el equipo de Microsoft, [visite aquí](/power-bi/collaborate-share/service-embed-report-microsoft-teams).

:::image type="content" source="media/iot-concepts/iot-connector-teams.png" alt-text="Captura de pantalla del conector de IoT Teams." lightbox="media/iot-concepts/iot-connector-teams.png":::

El conector de IoT para puede ingerir datos de IoT de la mayoría de los dispositivos o puertas de enlace de IoT, independientemente de la ubicación, el centro de datos o la nube. 

Se recomienda el uso de servicios de Azure IoT para ayudar con la conectividad de dispositivos o puertas de enlace.

:::image type="content" source="media/iot-concepts/iot-connector-iot-hub-teams.png" alt-text="Captura de pantalla del conector de IoT IoT Hub." lightbox="media/iot-concepts/iot-connector-iot-hub-teams.png":::

Para algunas soluciones, Azure IoT Central se puede usar en lugar de Azure IoT Hub.

Azure IoT Edge se puede usar en con IoT Hub para crear un punto de conexión local para dispositivos o conectividad en el dispositivo.

:::image type="content" source="media/iot-concepts/iot-connector-iot-edge-teams.png" alt-text="Captura de pantalla del conector de IoT IoT Edge." lightbox="media/iot-concepts/iot-connector-iot-edge-teams.png":::

## <a name="next-steps"></a>Pasos siguientes

En este artículo, ha aprendido sobre el conector de IoT y Teams de notificaciones. Para obtener información general sobre el conector de IoT, consulte

>[!div class="nextstepaction"]
>[Introducción al conector de IoT](iot-connector-overview.md)

(FHIR&#174;) es una marca comercial registrada de [HL7](https://hl7.org/fhir/) y se usa con el permiso de HL7.
