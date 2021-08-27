---
author: robinsh
ms.author: robinsh
ms.topic: include
ms.date: 07/07/2021
ms.openlocfilehash: eacee8b325317e98cd05093b71a21889f9a933ed
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/13/2021
ms.locfileid: "121739248"
---
## <a name="add-a-consumer-group-to-your-iot-hub"></a>Adición de un grupo de consumidores a IoT Hub

Los [grupos de consumidores](../articles/event-hubs/event-hubs-features.md#event-consumers) proporcionan vistas independientes en la secuencia de eventos que permiten a las aplicaciones y a los servicios de Azure consumir datos de forma independiente desde el mismo punto de conexión del centro de eventos. En esta sección, agregará un grupo de consumidores al punto de conexión integrado de su instancia de IoT Hub que se usará posteriormente en este tutorial para extraer datos del punto de conexión.

Para agregar un grupo de consumidores a su centro de IoT, siga estos pasos:

1. En [Azure Portal](https://portal.azure.com/), abra su centro de IoT.

1. En el panel izquierdo, seleccione **Puntos de conexión integrados**. Escriba un nombre para el nuevo grupo de consumidores en el cuadro de texto de **Grupos de consumidores**. 

   :::image type="content" source="./media/iot-hub-get-started-create-consumer-group/iot-hub-create-consumer-group-azure.png" alt-text="Creación de un grupo de consumidores para el IoT Hub":::

1. Haga clic en cualquier lugar fuera del cuadro de texto para guardar el grupo de consumidores.