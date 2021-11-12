---
title: 'Conector de IoT Microsoft Power BI: API de Azure Healthcare'
description: En este artículo, aprenderá a usar el conector de IoT y Power BI
author: msjasteppe
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: conceptual
ms.date: 11/10/2021
ms.author: jasteppe
ms.openlocfilehash: b099a085041664ed00788720eb3245dd9518c020
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 11/11/2021
ms.locfileid: "132315413"
---
# <a name="iot-connector-and-microsoft-power-bi"></a>Conector de IoT y Microsoft Power BI

En este artículo, exploraremos el uso del conector de IoT y Microsoft Power Business Intelligence (BI).

## <a name="iot-connector-and-power-bi-reference-architecture"></a>Conector de IoT Power BI arquitectura de referencia

La arquitectura de referencia siguiente muestra los componentes básicos del uso de servicios en la nube de Microsoft para habilitar Power BI sobre los datos de Internet de las cosas médicas (IoMT) y Recursos Rápidos de Interoperabilidad en Salud (FHIR&#174;). 

Incluso puede insertar paneles de Power BI en el cliente Microsoft Teams para mejorar aún más la coordinación del equipo de atención. Para obtener más información sobre la inserción de Power BI en Teams, visite [aquí](/power-bi/collaborate-share/service-embed-report-microsoft-teams).

:::image type="content" source="media/iot-concepts/iot-connector-power-bi.png" alt-text="Captura de pantalla del conector de IoT Power BI." lightbox="media/iot-concepts/iot-connector-power-bi.png":::

El conector de IoT puede ingerir datos de IoT de la mayoría de los dispositivos o puertas de enlace de IoT, independientemente de la ubicación, el centro de datos o la nube. 

Se recomienda el uso de servicios de Azure IoT para ayudar con la conectividad del dispositivo o la puerta de enlace.

:::image type="content" source="media/iot-concepts/iot-connector-iot-hub-power-bi.png" alt-text="Captura de pantalla del conector de IoT, IoT Hub y Power BI." lightbox="media/iot-concepts/iot-connector-iot-hub-power-bi.png":::

Para algunas soluciones, Azure IoT Central se puede usar en lugar de Azure IoT Hub.

Azure IoT Edge se puede usar en con IoT Hub para crear un punto de conexión local para dispositivos o conectividad en el dispositivo.

:::image type="content" source="media/iot-concepts/iot-connector-iot-edge-power-bi.png" alt-text="Captura de pantalla del conector de IoT, IoT Hub, IoT Edge y Power BI." lightbox="media/iot-concepts/iot-connector-iot-edge-power-bi.png":::

## <a name="next-steps"></a>Pasos siguientes

En este artículo, ha aprendido sobre el conector de IoT y Power BI integración. Para obtener información general sobre el conector de IoT, consulte

>[!div class="nextstepaction"]
>[Introducción al conector de IoT](iot-connector-overview.md)

(FHIR&#174;) es una marca registrada [de HL7](https://hl7.org/fhir/) y se usa con el permiso HL7.
