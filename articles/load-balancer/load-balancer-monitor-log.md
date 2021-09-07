---
title: Supervisión de operaciones, eventos y contadores para una instancia pública de Load Balancer
titleSuffix: Azure Load Balancer
description: Aprenda a habilitar el registro para Azure Load Balancer.
services: load-balancer
documentationcenter: na
author: asudbring
ms.service: load-balancer
ms.devlang: na
ms.topic: how-to
ms.custom: seodec18
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/05/2020
ms.author: allensu
ms.openlocfilehash: 9eacbcbb73acffb5625d2f2a7e8594c27992307e
ms.sourcegitcommit: 30e3eaaa8852a2fe9c454c0dd1967d824e5d6f81
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 06/22/2021
ms.locfileid: "112466616"
---
# <a name="azure-monitor-logs-for-azure-standard-load-balancer"></a>Registros de Azure Monitor para Azure Standard Load Balancer

Puede usar diferentes tipos de registros de Azure Monitor para administrar y solucionar problemas de Azure Standard Load Balancer. Los registros se pueden transmitir a un centro de eventos o a un área de trabajo de Log Analytics. Puede extraer todos los registros de Azure Blob Storage y verlos en herramientas como Excel y Power BI. 

Los tipos de registro son:

* **Registros de actividad:** puede ver todas las actividades que se envían a las suscripciones de Azure, junto con su estado. Para más información, consulte [Visualización de registros de actividad para supervisar acciones sobre recursos](../azure-resource-manager/management/view-activity-logs.md). Los registros de actividad están habilitados de manera predeterminada y se pueden ver en Azure Portal. Estos registros están disponibles para Azure Basic Load Balancer y para Standard Load Balancer.
* **Métricas de Standard Load Balancer:** puede usar este registro para consultar las métricas exportadas como registros de Standard Load Balancer. Estos registros solo están disponibles para Standard Load Balancer.

> [!IMPORTANT]
> Los registros de eventos de sondeo de estado o de alertas de Load Balancer no funcionan en la actualidad y aparecen listados en los [problemas conocidos de Azure Load Balancer](whats-new.md#known-issues). 

> [!IMPORTANT]
> Los registros solo están disponibles para los recursos implementados en el modelo de implementación de Azure Resource Manager. No puede usar los registros de recursos del modelo de implementación clásica. Para más información sobre estos modelos de implementación, consulte [Understanding Resource Manager deployment and classic deployment](../azure-resource-manager/management/deployment-models.md) (Descripción de la implementación de Resource Manager y la implementación clásica).

## <a name="enable-logging"></a>Habilitar registro

El registro de actividades se habilita automáticamente para todos los recursos de Resource Manager. Habilite el registro de eventos y de sondeos de estado para iniciar la recopilación de los datos disponibles a través de esos registros. Siga estos pasos:

1. Inicie sesión en [Azure Portal](https://portal.azure.com). Si aún no tiene un equilibrador de carga, [cree uno](./quickstart-load-balancer-standard-public-portal.md) antes de continuar.
1. En el portal, seleccione **Grupos de recursos**.
2. Seleccione el grupo de recursos **\<resource-group-name>** donde esté el equilibrador de carga.
3. Seleccione el equilibrador de carga.
4. Seleccione **Registro de actividades** > **Configuración de diagnóstico**.
5. En el panel **Configuración de diagnóstico**, en **Configuración de diagnóstico**, seleccione **+ Agregar configuración de diagnóstico**.
6. En el panel de creación **Configuración de diagnóstico**, escriba **myLBDiagnostics** en el cuadro **Nombre**.
7. Tiene tres opciones para la **Configuración de diagnóstico**. Puede elegir una, dos o las tres y configurar cada una de ellas según sus requisitos:

   * **Archivar en una cuenta de almacenamiento**. Necesitará una cuenta de almacenamiento ya creada para este proceso. Para crear una cuenta de almacenamiento, consulte [Creación de una cuenta de almacenamiento](../storage/common/storage-account-create.md?tabs=azure-portal).
     1. Active la casilla **Archivar en una cuenta de almacenamiento**.
     2. Seleccione **Configurar** para abrir el panel **Selección de una cuenta de almacenamiento**.
     3. En la lista desplegable **Suscripción**, seleccione la suscripción donde se creó la cuenta de almacenamiento.
     4. En la lista desplegable **Cuenta de almacenamiento**, seleccione el nombre de la cuenta de almacenamiento.
     5. Seleccione **Aceptar**.

   * **Transmitir a un centro de eventos**. Necesitará un centro de eventos ya creado para este proceso. Para crear un centro de eventos, consulte [Inicio rápido: Creación de un centro de eventos desde Azure Portal](../event-hubs/event-hubs-create.md).
     1. Active la casilla **Transmitir a un centro de eventos**.
     2. Seleccione **Configurar** para abrir el panel **Select event hub** (Selección del centro de eventos).
     3. En la lista desplegable **Suscripción**, seleccione la suscripción donde se creó el centro de eventos.
     4. En la lista **Seleccionar el espacio de nombres del Centro de eventos**, seleccione el espacio de nombres.
     5. En la lista **Seleccionar el espacio de nombres del Centro de eventos**, seleccione el nombre.
     6. Seleccione **Aceptar**.

   * **Enviar a Log Analytics**. Deberá tener un área de trabajo de Log Analytics creada y configurada para este proceso. Para crear un área de trabajo de Log Analytics, consulte [Creación de un área de trabajo de Log Analytics en Azure Portal](../azure-monitor/logs/quick-create-workspace.md).
     1. Active la casilla **Enviar a Log Analytics**.
     2. En la lista desplegable **Suscripción**, seleccione la suscripción en la que se encuentra el área de trabajo de Log Analytics.
     3. En la lista desplegable **Área de trabajo de Log Analytics**, seleccione el área de trabajo.

8. En la sección **METRIC** del panel **Configuración de diagnóstico**, active la casilla **AllMetrics**.

9. Compruebe que todo esté correcto y seleccione **Guardar** en la parte superior del panel de creación de **Configuración de diagnóstico**.

## <a name="view-and-analyze-the-activity-log"></a>Visualización y análisis del registro de actividades

El registro de actividad se genera de manera predeterminada. Se puede configurar para que se exporte en un nivel de suscripción [siguiendo las instrucciones de este artículo](../azure-monitor/essentials/activity-log.md). Obtenga más información sobre estos registros en el artículo [Visualización de registros de actividad para supervisar acciones sobre recursos](../azure-resource-manager/management/view-activity-logs.md).

Puede ver y analizar los datos del registro de actividad con cualquiera de los métodos siguientes:

* **Herramientas de Azure:** permiten recuperar información de los registros de actividades mediante Azure PowerShell, la CLI de Azure, la API REST de Azure o Azure Portal. En el artículo [Operaciones de auditoría con Resource Manager](../azure-resource-manager/management/view-activity-logs.md) se proporcionan instrucciones detalladas de cada método.
* **Power BI:** si todavía no tiene una cuenta de [Power BI](https://powerbi.microsoft.com/pricing), puede probarlo gratis. Con la [integración de los registros de auditoría de Azure para Power BI](https://powerbi.microsoft.com/integrations/azure-audit-logs/) puede analizar los datos con paneles preconfigurados. O puede personalizar las vistas para que se adapten a sus requisitos.

## <a name="view-and-analyze-metrics-as-logs"></a>Visualización y análisis de las métricas como registros
Con la funcionalidad de exportación de Azure Monitor puede exportar las métricas de Load Balancer. Estas métricas generarán una entrada de registro para cada intervalo de muestreo de un minuto.

La exportación de métricas a registros está habilitada por recursos. Para habilitar estos registros:

1. Vaya al panel **Configuración de diagnóstico**.
1. Filtre por grupo de recursos y, a continuación, seleccione la instancia de Load Balancer para la que desea habilitar la exportación de métricas. 
1. Cuando esté activa la página de configuración de diagnóstico de Load Balancer, seleccione **AllMetrics** para exportar las métricas válidas como registros.

Consulte la sección [Limitaciones](#limitations) de este artículo para conocer las limitaciones de la exportación de métricas.

Después de habilitar **AllMetrics** en la configuración de diagnóstico de Standard Load Balancer, si usa un centro de eventos o un área de trabajo de Log Analytics, estos registros se rellenarán en la tabla **AzureMonitor**. 

Si va a exportar al almacenamiento, conéctese a la cuenta de almacenamiento y recupere las entradas del registro JSON para los registros de eventos y de sondeos de estado. Después de descargar los archivos JSON, se pueden convertir a CSV y consultarlos en Excel, Power BI o cualquier otra herramienta de visualización de datos. 

> [!TIP]
> Si está familiarizado con Visual Studio y con los conceptos básicos de cambio de los valores de constantes y variables de C#, puede usar las [herramientas convertidoras de registros](https://github.com/Azure-Samples/networking-dotnet-log-converter) que encontrará en GitHub.

## <a name="stream-to-an-event-hub"></a>Transmitir a un centro de eventos
Cuando se transmite información de diagnóstico a un centro de eventos, esta se puede usar para el análisis de registros centralizado en una herramienta SIEM de asociados con la integración de Azure Monitor. Para más información, consulte [Transmisión de datos de supervisión de Azure a un centro de eventos](../azure-monitor/essentials/stream-monitoring-data-event-hubs.md#partner-tools-with-azure-monitor-integration).

## <a name="send-to-log-analytics"></a>Enviar a Log Analytics
Puede enviar información de diagnóstico de los recursos de Azure directamente a un área de trabajo de Log Analytics. En esa área de trabajo, puede ejecutar consultas complejas en la información para la solución de problemas y el análisis. Para más información, consulte [Recopilación de registros de recursos de Azure en un área de trabajo de Log Analytics en Azure Monitor](../azure-monitor/essentials/resource-logs.md#send-to-log-analytics-workspace).

## <a name="limitations"></a>Limitaciones
La característica de exportación de métricas a registros para Load Balancer tiene las siguientes limitaciones:
* Las métricas se muestran actualmente mediante nombres internos cuando se exportan como registros. Puede encontrar la asignación en la tabla siguiente.
* No se conserva la dimensionalidad de las métricas. Por ejemplo, con métricas como **DipAvailability** (estado del sondeo de estado), no podrá dividir o ver por dirección IP de back-end.
* Las métricas de los puertos SNAT usados y las métricas de puertos SNAT asignados no están disponibles actualmente para exportarse como registros.

## <a name="next-steps"></a>Pasos siguientes
* [Revise las métricas disponibles de Load Balancer](./load-balancer-standard-diagnostics.md)
* [Cree y pruebe consultas siguiendo las instrucciones de Azure Monitor](../azure-monitor/logs/log-query-overview.md)
