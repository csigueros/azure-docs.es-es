---
title: Comprobación del estado de los servicios y recursos de Azure IoT Hub | Microsoft Docs
description: Uso de Azure Service Health y Azure Resource Health para supervisar el centro de IoT
author: eross-msft
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 04/21/2020
ms.author: lizross
ms.custom:
- amqp
- 'Role: Cloud Development'
- 'Role: Technical Support'
- devx-track-csharp
ms.openlocfilehash: f370d87f2c17185fb4c340dc0da4f2f7d9027264
ms.sourcegitcommit: 05c8e50a5df87707b6c687c6d4a2133dc1af6583
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/16/2021
ms.locfileid: "132555307"
---
# <a name="check-iot-hub-service-and-resource-health"></a>Comprobación del estado de los servicios y recursos de IoT Hub

Azure IoT Hub se integra con el [servicio Azure Service Health](../service-health/overview.md) para ofrecer la posibilidad de supervisar el estado en el nivel de servicio del servicio IoT Hub y de los centros de IoT individuales. También puede configurar alertas para recibir una notificación cuando cambie el estado del servicio IoT Hub o de un centro de IoT. El servicio Azure Service Health es una combinación de tres servicios más pequeños: Azure Resource Health, Azure Service Health y la página Estado de Azure. En las secciones de este artículo se describe con más detalle cada servicio y su relación con IoT Hub.

El servicio Azure Service Health le ayuda a supervisar los eventos de nivel de servicio, como interrupciones y actualizaciones, que pueden afectar a la disponibilidad del servicio IoT Hub y de los centros de IoT individuales. IoT Hub también se integra con Azure Monitor para proporcionar métricas de la plataforma y registros de recursos de IoT Hub que se pueden usar para supervisar los errores operativos y las condiciones que se producen en un centro de IoT específico. Para más información, consulte [Supervisión de IoT Hub](monitor-iot-hub.md).

## <a name="check-health-of-an-iot-hub-with-azure-resource-health"></a>Comprobación del estado de un centro de IoT con Azure Resource Health

Azure Resource Health es una parte del servicio Azure Service Health que realiza un seguimiento del estado de los recursos individuales. Puede comprobar el estado de mantenimiento del centro de IoT directamente desde el portal.

Para ver el estado y el historial de estado del centro de IoT mediante el portal, siga estos pasos:

1. En [Azure Portal](https://portal.azure.com), vaya a su centro de IoT.

1. En el panel izquierdo, en **Soporte técnico y solución de problemas**, seleccione **Resource Health**.

    :::image type="content" source="./media/iot-hub-azure-service-health-integration/iot-hub-resource-health.png" alt-text="Página Resource Health de un centro de IoT":::

Para más información sobre Azure Resource Health y cómo interpretar los datos de estado, consulte [Introducción a Resource Health](../service-health/resource-health-overview.md) en la documentación de Azure Service Health.

También puede seleccionar **Add resource health alert** (Agregar alerta de mantenimiento del recurso) para configurar alertas que se desencadenen cuando cambie el estado de mantenimiento del centro de IoT. Para más información, consulte [Configuración de alertas para eventos de Service Health](../service-health/alerts-activity-log-service-notifications-portal.md) y temas relacionados en la documentación de Azure Service Health.

## <a name="check-health-of-iot-hubs-in-your-subscription-with-azure-service-health"></a>Comprobación del estado de los centros de IoT de la suscripción con Azure Service Health

Con Azure Service Health, puede comprobar el estado de mantenimiento de todos los centros de IoT de la suscripción.

Para comprobar el mantenimiento de los recursos de IoT Hub, siga estos pasos:

1. Inicie sesión en [Azure Portal](https://portal.azure.com).

2. Vaya a **Estado del servicio** > **Resource Health**.

3. En los cuadros de lista desplegable, seleccione su suscripción y, luego, seleccione **IoT Hub** como el tipo de recurso.

Para más información sobre Azure Service Health y cómo interpretar los datos de mantenimiento, consulte [Introducción a Service Health](../service-health/service-health-overview.md) en la documentación de Azure Service Health.

Para aprender a configurar alertas con Azure Service Health, consulte [Configuración de alertas para eventos de Service Health](../service-health/alerts-activity-log-service-notifications-portal.md) y temas relacionados en la documentación de Azure Service Health.

## <a name="check-health-of-the-iot-hub-service-by-region-on-azure-status-page"></a>Comprobación del estado del servicio IoT Hub por región en la página Estado de Azure

Para ver el estado de IoT Hub y otros servicios por región en todo el mundo, puede usar la [página Estado de Azure](https://status.azure.com/status). Para más información sobre la página Estado de Azure, consulte [Introducción al estado de Azure](../service-health/azure-status-overview.md) en la documentación de Azure Service Health.

## <a name="next-steps"></a>Pasos siguientes

* Consulte [Servicio Azure Service Health](../service-health/overview.md) para más información sobre Azure Service Health, Azure Resource Health y la página Estado de Azure.
* Consulte [Supervisión de Azure IoT Hub](monitor-iot-hub.md) para ver una descripción de la supervisión de Azure IoT Hub.
