---
author: robinsh
ms.author: robinsh
ms.topic: include
ms.date: 07/07/2021
ms.openlocfilehash: 090a7ae8e053b82e157d1e46e740d9b3911fef99
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/02/2021
ms.locfileid: "131077612"
---
## <a name="add-a-consumer-group-to-your-iot-hub"></a>Adición de un grupo de consumidores a IoT Hub

Los [grupos de consumidores](../articles/event-hubs/event-hubs-features.md#event-consumers) proporcionan vistas independientes en la secuencia de eventos que permiten a las aplicaciones y a los servicios de Azure consumir datos de forma independiente desde el mismo punto de conexión del centro de eventos. En esta sección, agregará un grupo de consumidores al punto de conexión integrado de su instancia de IoT Hub que se usará posteriormente en este tutorial para extraer datos del punto de conexión.

Para agregar un grupo de consumidores a su centro de IoT, siga estos pasos:

1. En [Azure Portal](https://portal.azure.com/), abra su centro de IoT.

1. En el panel izquierdo, seleccione **Puntos de conexión integrados**. Escriba un nombre para el nuevo grupo de consumidores en el cuadro de texto de **Grupos de consumidores**.

   :::image type="content" source="./media/iot-hub-get-started-create-consumer-group/iot-hub-create-consumer-group-azure.png" alt-text="Captura de pantalla que muestra cómo crear un grupo de consumidores en el centro de IoT." border="true":::

1. Haga clic en cualquier lugar fuera del cuadro de texto para guardar el grupo de consumidores.