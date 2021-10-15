---
title: Archivo de inclusión
description: archivo de inclusión
services: service-bus-messaging
author: spelluru
ms.service: service-bus-messaging
ms.topic: include
ms.date: 09/29/2021
ms.author: spelluru
ms.custom: include file
ms.openlocfilehash: c6c6a92848b91f32dec612abe7c16ab73b71cccd
ms.sourcegitcommit: 613789059b275cfae44f2a983906cca06a8706ad
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/29/2021
ms.locfileid: "129300207"
---
## <a name="trusted-microsoft-services"></a>Servicios de Microsoft de confianza
Al activar la opción **¿Quiere permitir que los servicios de confianza de Microsoft puedan omitir este firewall?** , los siguientes servicios adquieren acceso a los recursos de Service Bus.

| Servicio de confianza | Escenarios de uso admitidos | 
| --------------- | ------------------------- | 
| Azure Event Grid | Permite a Azure Event Grid enviar eventos a colas o temas en el espacio de nombres de Service Bus. También debe seguir estos pasos: <ul><li>Habilite la identidad asignada por el sistema para un tema o un dominio</li><li>Agregue la identidad al rol Remitente de datos de Azure Service Bus en el espacio de nombres de Service Bus</li><li>A continuación, configure la suscripción de eventos que emplea una cola o tema de Service Bus como punto de conexión para usar la identidad asignada por el sistema.</li></ul> <p>Para obtener más información, vea [Entrega de evento con una identidad administrada](../../event-grid/managed-service-identity.md)</p>|
| Azure API Management | <p>El servicio API Management le permite enviar mensajes a una cola o un tema de Service Bus en el espacio de nombres de Service Bus.</p><ul><li>Puede desencadenar flujos de trabajo personalizados enviando mensajes a la cola o el tema de Service Bus cuando se invoca una API mediante la [directiva send-request](../../api-management/api-management-sample-send-request.md).</li><li>También puede tratar una cola o un tema de Service Bus como back-end en una API. Para ver una directiva de ejemplo, vea [Autenticación mediante una identidad administrada para acceder a una cola o un tema de Service Bus](https://github.com/Azure/api-management-policy-snippets/blob/master/examples/Authenticate%20using%20Managed%20Identity%20to%20access%20Service%20Bus.xml). También debe seguir estos pasos:<ol><li>Habilite una identidad asignada por el sistema en una instancia de API Management. Para instrucciones, vea [Uso de identidades administradas en Azure API Management](../../api-management/api-management-howto-use-managed-service-identity.md).</li><li>Agregue la identidad al rol **Remitente de datos de Azure Service Bus** en el espacio de nombres de Service Bus.</li></ol></li></ul> | 
| Azure IoT Central | <p>Permite a IoT Central exportar datos a colas o temas de Service Bus en el espacio de nombres de Service Bus. También debe seguir estos pasos:</p><ul><li>Habilitación de la identidad asignada por el sistema para la aplicación IoT Central</li><li>Agregue la identidad al rol **Remitente de datos de Azure Service Bus** en el espacio de nombres de Service Bus. </li><li>A continuación, configure el [destino de exportación de Service Bus en la aplicación IoT Central](../../iot-central/core/howto-export-data.md) para usar la autenticación basada en identidades. </li>
