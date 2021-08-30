---
title: Entrada y salida de datos
titleSuffix: Azure Digital Twins
description: Comprenda los requisitos de entrada y salida para integrar Azure Digital Twins con otros servicios.
author: baanders
ms.author: baanders
ms.date: 6/1/2021
ms.topic: conceptual
ms.service: digital-twins
ms.openlocfilehash: 55aa3f69976a413e1c23e9d935ad99fde88065ec
ms.sourcegitcommit: 05dd6452632e00645ec0716a5943c7ac6c9bec7c
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/17/2021
ms.locfileid: "122252763"
---
# <a name="data-ingress-and-egress-for-azure-digital-twins"></a>Entrada y salida de datos para Azure Digital Twins

Azure Digital Twins se suele usar junto con otros servicios para crear soluciones flexibles y conectadas que usan los datos de diversas maneras.

Mediante [rutas de eventos](concepts-route-events.md), Azure Digital Twins puede recibir datos de servicios ascendentes, como [IoT Hub](../iot-hub/about-iot-hub.md) o [Logic Apps](../logic-apps/logic-apps-overview.md), que se usan para proporcionar telemetría y notificaciones. 

Azure Digital Twins también puede enrutar datos a servicios de bajada, como [Azure Maps](../azure-maps/about-azure-maps.md) y [Time Series Insights](../time-series-insights/overview-what-is-tsi.md) con fines de almacenamiento, integración del flujo de trabajo, análisis, etc. 

## <a name="data-ingress"></a>Entrada de datos

Azure Digital Twins se puede controlar con datos y eventos de cualquier servicio: [IoT Hub](../iot-hub/about-iot-hub.md), [Logic Apps](../logic-apps/logic-apps-overview.md), su propio servicio personalizado, etc. Esto le permite recopilar datos de telemetría de dispositivos físicos en el entorno y procesarlos mediante el grafo de Azure Digital Twins en la nube.

En lugar de tener una instancia de IoT Hub integrada en segundo plano, Azure Digital Twins le permite "traer su propia" instancia de IoT Hub para usarla con el servicio. Puede usar un centro de IoT Hub existente que tenga actualmente en producción o bien implementar uno nuevo para usarlo con este fin. Así, obtiene acceso completo a todas las funcionalidades de administración de los dispositivos de IoT Hub.

Para ingerir datos de cualquier origen en Azure Digital Twins, use una [función de Azure Functions](../azure-functions/functions-overview.md). Puede encontrar más información sobre este patrón en [Ingesta de telemetría de IoT Hub](how-to-ingest-iot-hub-data.md) o puede probarlo usted mismo en el tutorial de Azure Digital Twins [Conexión de una solución de un extremo a otro](tutorial-end-to-end.md). 

También puede aprender a conectar Azure Digital Twins a un desencadenador de Logic Apps en [Integración con Logic Apps](how-to-integrate-logic-apps.md).

## <a name="data-egress-services"></a>Servicios de salida de datos

Azure Digital Twins puede enviar datos a los **puntos de conexión** conectados. Los puntos de conexión admitidos pueden ser:
* [Event Hubs](../event-hubs/event-hubs-about.md)
* [Event Grid](../event-grid/overview.md)
* [Service Bus](../service-bus-messaging/service-bus-messaging-overview.md)

Los puntos de conexión se asocian a Azure Digital Twins mediante las API de administración o Azure Portal. Conozca cómo asociar un punto de conexión a Azure Digital Twins en [Administración de puntos de conexión y rutas](how-to-manage-routes.md).

Hay muchos otros servicios donde es posible que quiera dirigir los datos en última instancia, como [Azure Storage](../storage/common/storage-introduction.md), [Azure Maps](../azure-maps/about-azure-maps.md), [Azure Data Explorer](/azure/data-explorer/data-explorer-overview) o [Time Series Insights](../time-series-insights/overview-what-is-tsi.md). Para enviar los datos a servicios como estos, asocie el servicio de destino a un punto de conexión.

Por ejemplo, si también usa Azure Maps y desea poner en correlación la ubicación con el [grafo gemelo](concepts-twins-graph.md) de Azure Digital Twins, puede usar Azure Functions con Event Grid para establecer la comunicación entre todos los servicios de la implementación. Puede encontrar más información al respecto en [Uso de Azure Digital Twins para actualizar un mapa de interiores de Azure Maps](how-to-integrate-maps.md).

También puede aprender a enrutar los datos de forma similar a Time Series Insights en [Integración con Time Series Insights](how-to-integrate-time-series-insights.md).

## <a name="next-steps"></a>Pasos siguientes

Más información sobre los puntos de conexión y el enrutamiento de eventos a servicios externos:
* [Enrutamiento de eventos de Azure Digital Twins](concepts-route-events.md)

Vea cómo configurar Azure Digital Twins para ingerir datos de IoT Hub:
* [Ingesta de telemetría desde IoT Hub](how-to-ingest-iot-hub-data.md)