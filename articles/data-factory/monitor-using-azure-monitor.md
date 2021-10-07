---
title: Supervisión de factorías de datos mediante Azure Monitor | Microsoft Docs
description: Aprenda a utilizar Azure Monitor para supervisar las canalizaciones de Azure Data Factory mediante la habilitación de los registros de diagnóstico con la información de Data Factory.
author: minhe-msft
ms.author: hemin
ms.reviewer: jburchel
ms.custom: contperf-fy22q1
ms.service: data-factory
ms.subservice: monitoring
ms.topic: conceptual
ms.date: 09/02/2021
ms.openlocfilehash: 66f6c40ce77c50c18dfe3fca48e8f9367f29390b
ms.sourcegitcommit: 61e7a030463debf6ea614c7ad32f7f0a680f902d
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/28/2021
ms.locfileid: "129093219"
---
# <a name="monitor-and-alert-data-factory-by-using-azure-monitor"></a>Alerta y supervisión de Data Factory mediante Azure Monitor

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

Las aplicaciones en la nube son complejas y tienen muchas partes en movimiento. Los supervisores proporcionan datos para garantizar que las aplicaciones permanecen en funcionamiento en un estado correcto. Los supervisores también ayudan a evitar posibles problemas y a solucionar los problemas anteriores. Puede usar datos de supervisión para extraer conclusiones detalladas sobre las aplicaciones. Este conocimiento le ayuda a mejorar el rendimiento y el mantenimiento de las aplicaciones. También le ayuda a automatizar acciones que, de otro modo, requieren intervención manual.

Azure Monitor ofrece métricas y registros de las infraestructuras a nivel básico para la mayoría de los servicios de Azure. Un recurso emite registros de diagnóstico de Azure que proporcionan datos exhaustivos y frecuentes acerca del funcionamiento de ese recurso. Azure Data Factory (ADF) puede escribir registros de diagnóstico en Azure Monitor. Si desea una demostración y una introducción de siete minutos de esta característica, vea el siguiente vídeo:

> [!VIDEO https://channel9.msdn.com/Shows/Azure-Friday/Monitor-Data-Factory-pipelines-using-Operations-Management-Suite-OMS/player]

Para más información, consulte [Introducción a Azure Monitor](../azure-monitor/overview.md).

## <a name="keeping-azure-data-factory-metrics-and-pipeline-run-data"></a>Mantenimiento de métricas de Azure Data Factory y datos de ejecución de la canalización

Data Factory solo almacena los datos de ejecución de canalización durante 45 días. Use Azure Monitor si desea conservar los datos durante más tiempo. Con Monitor, puede enrutar los registros de diagnóstico a varios destinos diferentes.

* **Storage Account** (Cuenta de almacenamiento): Guarde los registro de diagnóstico en una cuenta de almacenamiento para auditarlos o para inspeccionarlos manualmente. Puede usar la configuración de diagnóstico para especificar el tiempo de retención en días.
* **Centro de eventos**: Transmita los registros a Azure Event Hubs. Los registros se convierten en la entrada de un servicio del asociado o una solución de análisis personalizada como Power BI.
* **Log Analytics**: Analice los registros con Log Analytics. La integración de Data Factory con Azure Monitor es útil en los escenarios siguientes:
  * Quiere escribir consultas complejas en un amplio conjunto de métricas que se publican mediante Data Factory en Monitor. Puede crear alertas personalizadas sobre estas consultas mediante Monitor.
  * Quiere realizar la supervisión entre fábricas de datos. Puede enrutar datos desde varias factorías de datos a una única área de trabajo de Monitor.

Puede usar también una cuenta de almacenamiento o un espacio de nombres de centro de eventos que no esté en la misma suscripción que el recurso que emite los registros. El usuario que configura los ajustes debe tener el control de acceso basado en roles de Azure (Azure RBAC) adecuado a ambas suscripciones.

## <a name="next-steps"></a>Pasos siguientes

- [Métricas y alertas de Azure Data Factory](monitor-metrics-alerts.md)
- [Supervisión y administración de canalizaciones mediante programación](monitor-programmatically.md)
