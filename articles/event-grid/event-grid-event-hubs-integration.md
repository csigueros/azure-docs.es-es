---
title: 'Tutorial: Envío de datos de Event Hubs al almacenamiento de datos: Event Grid'
description: Describe cómo almacenar datos capturados de Event Hubs en Azure Synapse Analytics mediante desencadenadores de Azure Functions y Event Grid.
ms.topic: tutorial
ms.date: 12/07/2020
ms.custom: devx-track-csharp
ms.openlocfilehash: 2887cdc1b4666daf73a21a76907f80d50d345d82
ms.sourcegitcommit: 5163ebd8257281e7e724c072f169d4165441c326
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 06/21/2021
ms.locfileid: "112414260"
---
# <a name="tutorial-stream-big-data-into-a-data-warehouse"></a>Tutorial: Transmitir macrodatos a un almacenamiento de datos
Azure [Event Grid](overview.md) es un servicio inteligente de enrutamiento de eventos que permite responder a las notificaciones o eventos procedentes de aplicaciones y servicios. Por ejemplo, puede desencadenar una función de Azure para procesar datos de Event Hubs que se capturan en Blob Storage o Data Lake Storage. En este [ejemplo](https://github.com/Azure/azure-event-hubs/tree/master/samples/e2e/EventHubsCaptureEventGridDemo), se muestra cómo usar Event Grid y Azure Functions para migrar datos capturados de Event Hubs desde Blob Storage a Azure Synapse Analytics, específicamente un grupo de SQL dedicado.

[!INCLUDE [event-grid-event-hubs-functions-synapse-analytics.md](./includes/event-grid-event-hubs-functions-synapse-analytics.md)]

## <a name="next-steps"></a>Pasos siguientes

* Para información sobre las diferencias en los servicios de mensajería de Azure, consulte [Elija entre los servicios de Azure de entrega de mensajes](compare-messaging-services.md).
* Para obtener una introducción a Event Grid, vea [Acerca de Event Grid](overview.md).
* Para obtener una introducción a la función de captura de Event Hubs, vea [Habilitación de la funcionalidad de captura de Event Hubs mediante Azure Portal](../event-hubs/event-hubs-capture-enable-through-portal.md).
* Para más información sobre cómo configurar y ejecutar el ejemplo, vea el [ejemplo de la función de captura de Event Hubs y Event Grid](https://github.com/Azure/azure-event-hubs/tree/master/samples/e2e/EventHubsCaptureEventGridDemo).
