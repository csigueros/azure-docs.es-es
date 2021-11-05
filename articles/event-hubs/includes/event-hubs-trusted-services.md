---
title: Archivo de inclusión
description: archivo de inclusión
services: event-hubs
author: spelluru
ms.service: event-hubs
ms.topic: include
ms.date: 10/20/2021
ms.author: spelluru
ms.custom: include file
ms.openlocfilehash: 1e9d61eca35cc9ea9fe20731fa093354c0eac5f8
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/02/2021
ms.locfileid: "131253300"
---
## <a name="trusted-microsoft-services"></a>Servicios de Microsoft de confianza
Al activar la opción **¿Quiere permitir que los servicios de confianza de Microsoft puedan omitir este firewall?** , los siguientes servicios adquieren acceso a los recursos de Event Hubs.

| Servicio de confianza | Escenarios de uso admitidos | 
| --------------- | ------------------------- | 
| Azure Event Grid | Permite que Azure Event Grid envíe eventos a Event Hubs en el espacio de nombres de Event Hubs. También debe seguir estos pasos: <ul><li>Habilite la identidad asignada por el sistema para un tema o un dominio</li><li>Agregue la identidad al rol Remitente de datos de Azure Event Hubs en el espacio de nombres de Event Hubs</li><li>A continuación, configure la suscripción de eventos que emplea un centro de eventos como punto de conexión para usar la identidad asignada por el sistema.</li></ul> <p>Para obtener más información, vea [Entrega de evento con una identidad administrada](../../event-grid/managed-service-identity.md)</p>|
| Azure Monitor (configuración de diagnóstico y grupos de acciones) | Permite que Azure Monitor envíe información de diagnóstico y notificaciones de alertas a Event Hubs en el espacio de nombres de Event Hubs. Azure Monitor puede leer desde el centro de eventos y escribir datos en el centro de eventos. |
| Azure Stream Analytics | Permite que un trabajo de Azure Stream Analytics lea datos de centros de eventos del espacio de nombres de Event Hubs ([entrada](../../stream-analytics/stream-analytics-add-inputs.md)) o escriba datos en ellos ([salida](../../stream-analytics/event-hubs-output.md)). <p>**Importante**: El trabajo de Stream Analytics debe estar configurado para usar una **identidad administrada** para tener acceso al centro de eventos. Para obtener más información, consulte [Uso de identidades administradas para acceder a Event Hubs desde un trabajo de Azure Stream Analytics (versión preliminar)](../../stream-analytics/event-hubs-managed-identity.md). </p>|
| Azure IoT Hub | Permite que IoT Hub envíe mensajes a centros de eventos del espacio de nombres de Event Hubs. También debe seguir estos pasos: <ul><li>Habilitación de la identidad asignada por el sistema para el centro de IoT</li><li>Agregue la identidad al rol Remitente de datos de Azure Event Hubs en el espacio de nombres de Event Hubs.</li><li>A continuación, configure la instancia de IoT Hub que usa un centro de eventos como punto de conexión personalizado para utilizar la autenticación basada en identidad.</li></ul>
| Azure API Management | <p>El servicio API Management le permite enviar eventos a un centro de eventos en el espacio de nombres de Event Hubs.</p> <ul><li>Puede desencadenar flujos de trabajo personalizados enviando eventos al centro de eventos cuando se invoca una API mediante la [directiva send-request](../../api-management/api-management-sample-send-request.md).</li><li>También puede tratar un centro de eventos como back-end en una API. Para ver una directiva de ejemplo, consulte [Autenticación mediante una identidad administrada para acceder a un centro de eventos](https://github.com/Azure/api-management-policy-snippets/blob/master/examples/Authenticate%20using%20Managed%20Identity%20to%20access%20Event%20Hub.xml). También debe seguir estos pasos:<ol><li>Habilite una identidad asignada por el sistema en una instancia de API Management. Para instrucciones, vea [Uso de identidades administradas en Azure API Management](../../api-management/api-management-howto-use-managed-service-identity.md).</li><li>Agregue la identidad al rol **Remitente de datos de Azure Event Hubs** en el espacio de nombres de Event Hubs.</li></ol></li></ul> | 
| Azure IoT Central | <p>Permite que IoT Central exporte datos a centros de eventos del espacio de nombres de Event Hubs. También debe seguir estos pasos:</p><ul><li>Habilitación de la identidad asignada por el sistema para la aplicación IoT Central.</li><li>Agregue la identidad al rol **Remitente de datos de Azure Event Hubs** en el espacio de nombres de Event Hubs.</li><li>A continuación, configure el [destino de exportación de Event Hubs en la aplicación IoT Central](../../iot-central/core/howto-export-data.md) para usar la autenticación basada en identidades.</li>
