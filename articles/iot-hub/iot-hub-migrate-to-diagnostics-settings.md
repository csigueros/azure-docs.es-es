---
title: Migración de la supervisión de operaciones de Azure IoT Hub a los registros de recursos de IoT Hub en Azure Monitor | Microsoft Docs
description: Se describe cómo actualizar Azure IoT Hub para usar Azure Monitor en lugar de la supervisión de operaciones para controlar el estado de las operaciones de IoT Hub en tiempo real.
author: kgremban
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 03/11/2019
ms.author: kgremban
ms.openlocfilehash: c41b3298e265fb3dab41a6fc1af51e3ff0829489
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/13/2021
ms.locfileid: "121733822"
---
# <a name="migrate-your-iot-hub-from-operations-monitoring-to-azure-monitor-resource-logs"></a>Migración de la supervisión de operaciones de IoT Hub a los registros de recursos de Azure Monitor

Los clientes que usan la [supervisión de operaciones](iot-hub-operations-monitoring.md) para realizar un seguimiento del estado de las operaciones en IoT Hub pueden migrar dicho flujo de trabajo a los [registros de recursos de Azure Monitor](../azure-monitor/essentials/platform-logs-overview.md), una característica de Azure Monitor. Los registros de recursos proporcionan información de diagnóstico a nivel de recurso para muchos servicios de Azure.

**La funcionalidad de la supervisión de operaciones de IoT Hub está en desuso** y, por tanto, se ha quitado del portal. En este artículo se explican los pasos para migrar las cargas de trabajo de la supervisión de operaciones a los registros de recursos de Azure Monitor. Para más información sobre la escala de tiempo de desuso, consulte [Monitor your Azure IoT solutions with Azure Monitor and Azure Resource Health](https://azure.microsoft.com/blog/monitor-your-azure-iot-solutions-with-azure-monitor-and-azure-resource-health/) (Supervisión de las soluciones de Azure IoT con Azure Monitor y Azure Resource Health).

## <a name="update-iot-hub"></a>Actualización de IoT Hub

Para actualizar IoT Hub en Azure Portal, primero cree la configuración de diagnóstico y luego desactive la supervisión de operaciones.  

### <a name="create-a--diagnostic-setting"></a>Creación de una configuración de diagnóstico

1. Inicie sesión en [Azure Portal](https://portal.azure.com) y vaya a IoT Hub.

1. En el panel de la izquierda, seleccione **Configuración de diagnóstico** en **Supervisión**. Después, seleccione **Agregar configuración de diagnóstico**.

   :::image type="content" source="media/iot-hub-migrate-to-diagnostics-settings/open-diagnostic-settings.png" alt-text="Captura de pantalla que resalta la opción Configuración de diagnóstico en la sección Supervisión.":::

1. En la página **Configuración de diagnóstico**, asigne un nombre a la configuración de diagnóstico.

1. En **Detalles de categoría**, seleccione las categorías de las operaciones que quiere supervisar. Para obtener más información acerca de las categorías de operaciones disponibles con IoT Hub, consulte [Registros de recursos](monitor-iot-hub-reference.md#resource-logs).

1. En **Detalles del destino**, elija dónde quiere enviar los registros. También puede seleccionar cualquier combinación de estos destinos:

   * Archivar en una cuenta de almacenamiento
   * Transmitir a un centro de eventos
   * Enviar a los registros de Azure Monitor mediante un área de trabajo de Log Analytics

   En la captura de pantalla siguiente se muestra una configuración de diagnóstico que enruta las operaciones de las categorías Conexiones y telemetría de dispositivo a un área de trabajo de Log Analytics:

   :::image type="content" source="media/iot-hub-migrate-to-diagnostics-settings/add-diagnostic-setting.png" alt-text="Captura de pantalla que muestra la configuración de diagnóstico completada.":::

1. Haga clic en **Save** (Guardar) para guardar la configuración.

La nueva configuración surte efecto en unos 10 minutos. Después, los registros aparecen en el destino configurado. Para más información sobre la configuración de diagnósticos, consulte [Recopilación y uso de los datos de registro provenientes de los recursos de Azure](../azure-monitor/essentials/platform-logs-overview.md).

Para obtener información más detallada sobre cómo crear la configuración de diagnóstico, incluido con PowerShell y la CLI de Azure, consulte [Configuración de diagnóstico](../azure-monitor/essentials/diagnostic-settings.md) en la documentación de Azure Monitor.

### <a name="turn-off-operations-monitoring"></a>Desactivación de la supervisión de operaciones

> [!NOTE]
> A partir del 11 de marzo de 2019, la característica de supervisión de operaciones se quita de la interfaz de Azure Portal de IoT Hub. Los pasos siguientes ya no se aplican. Para realizar la migración, asegúrese de que las categorías adecuadas se enrutan a un destino con una de las anteriores configuraciones de diagnóstico de Azure Monitor.

Cuando haya probado la nueva configuración de diagnóstico en el flujo de trabajo, puede desactivar la característica de supervisión de operaciones. 

1. En el menú de IoT Hub, seleccione **Supervisión de operaciones**.

2. En cada categoría de supervisión, seleccione **Ninguno**.

3. Guarde los cambios de la supervisión de operaciones.

## <a name="update-applications-that-use-operations-monitoring"></a>Actualización de las aplicaciones que usan la supervisión de operaciones

Los esquemas para la supervisión de operaciones y los registros de recursos varían ligeramente. Es importante que actualice las aplicaciones que usan la supervisión de operaciones actualmente para asignarlas al esquema que usan los registros de recursos.

Además, los registros de recursos de IoT Hub ofrecen cinco categorías nuevas para seguimiento. Después de actualizar las aplicaciones para el esquema existente, agregue también las nuevas categorías:

* Operaciones gemelas de la nube al dispositivo
* Operaciones gemelas del dispositivo a la nube
* Consultas gemelas
* Operaciones de trabajos
* Métodos directos

Para obtener las estructuras de esquema específicas, vea [Registros de recursos](monitor-iot-hub-reference.md#resource-logs).

## <a name="monitoring-device-connect-and-disconnect-events-with-low-latency"></a>Supervisión de los eventos de conexión y desconexión del dispositivo con baja latencia

Para supervisar los eventos de conexión y desconexión de dispositivos, se recomienda suscribirse al [evento **dispositivo desconectado**](iot-hub-event-grid.md#event-types) en Event Grid para obtener alertas y supervisar el estado de conexión del dispositivo. Siga este [tutorial](iot-hub-how-to-order-connection-state-events.md) para aprender a integrar eventos de dispositivo conectado y dispositivo desconectado de IoT Hub en la solución de IoT.

## <a name="next-steps"></a>Pasos siguientes

[Supervisión de IoT Hub](monitor-iot-hub.md)