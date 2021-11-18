---
title: 'Azure Communication Services: habilitación de Azure Monitor'
titleSuffix: An Azure Communication Services concept document
description: Configuración de métricas y registros de Communications Services con Configuración de diagnóstico
author: timmitchell
services: azure-communication-services
ms.author: timmitchell
ms.date: 10/25/2021
ms.topic: conceptual
ms.service: azure-communication-services
ms.subservice: data
ms.openlocfilehash: 145e5d09552c39fe6ff69c3b22152f8357427fc2
ms.sourcegitcommit: 0415f4d064530e0d7799fe295f1d8dc003f17202
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/17/2021
ms.locfileid: "132716512"
---
# <a name="enable-logs-via-diagnostic-settings-in-azure-monitor"></a>Habilitación de registros mediante Configuración de diagnóstico en Azure Monitor

## <a name="overview"></a>Información general

Communications Services proporciona características de supervisión y análisis a través de [Información general de registros de Azure Monitor](../../../azure-monitor/logs/data-platform-logs.md) y [Métricas de Azure Monitor](../../../azure-monitor/essentials/data-platform-metrics.md). Cada recurso de Azure necesita su propia configuración de diagnóstico, que establece los siguientes criterios:

- Categorías de los datos de los registros y las métricas que se envían a los destinos definidos en la configuración. Las categorías disponibles varían para los distintos tipos de recursos.
- Uno o más destinos para enviar los registros. Los destinos actuales incluyen el área de trabajo de Log Analytics, Event Hubs y Azure Storage.
- Cada configuración de diagnóstico puede definir un único destino. Si desea enviar datos a más de un tipo de destino determinado (por ejemplo, dos áreas de trabajo de Log Analytics diferentes), cree varias configuraciones. Cada recurso puede tener hasta cinco configuraciones de diagnóstico.

A continuación, se indican instrucciones para configurar el recurso Azure Monitor para empezar a crear registros y métricas para Communication Services. Puede encontrar documentación detallada sobre el uso de Configuración de diagnóstico en todos los recursos de Azure en [Configuración de diagnóstico](../../../azure-monitor/essentials/diagnostic-settings.md).

Estas instrucciones se aplican a los siguientes registros de Communications Services:

- [Registros de resumen de llamadas y diagnóstico de llamadas](call-logs-azure-monitor.md) (versión preliminar)

## <a name="access-diagnostic-settings"></a>Acceso a Configuración de diagnóstico

Para acceder a Configuración de diagnóstico en Communication Services, vaya a la página principal de su recurso Communications Services en Azure Portal:

:::image type="content" source="media\enable-logging\portal-home-go-to-acs-resource.png" alt-text="Recurso Communications Services":::

Desde allí, haga clic en "Configuración de diagnóstico" en la sección Supervisión del panel de navegación izquierdo:

:::image type="content" source="media\enable-logging\resource-diagnostic-settings-nav.png" alt-text="Configuración de diagnóstico en el panel de navegación":::

Haga clic en el vínculo "Agregar configuración de diagnóstico" (tenga en cuenta los distintos orígenes de registros y métricas disponibles para Communications Services):

:::image type="content" source="media\enable-logging\diagnostic-setting-add.png" alt-text="Detalles de la categoría Configuración de diagnóstico":::

## <a name="adding-a-diagnostic-setting"></a>Adición de una configuración de diagnóstico

A continuación, se le pedirá que elija un nombre para la configuración de diagnóstico, lo que resulta útil si tiene muchos recursos de Azure que está supervisando. También se le pedirá que seleccione los orígenes de datos de registros y métricas que quiere supervisar como registros o métricas. Para más información sobre la diferencia, consulte [Plataforma de datos de Azure Monitor](../../../azure-monitor/data-platform.md).

:::image type="content" source="media\enable-logging\diagnostic-setting-categories-details-acs.png" alt-text="Adición de una configuración de diagnóstico":::

## <a name="choose-destinations"></a>Selección de los destinos

También se le pedirá que seleccione un destino para almacenar los registros. Los registros y las métricas de la plataforma se pueden enviar a los destinos de la tabla siguiente, que también se trata con más detalle en la documentación de Azure Monitor [Creación de una configuración de diagnóstico para enviar registros y métricas de la plataforma a distintos destinos](../../../azure-monitor/essentials/diagnostic-settings.md?tabs=CMD).

| Destination | Descripción |
|:------------|:------------|
| [Área de trabajo de Log Analytics](../../../azure-monitor/logs/design-logs-deployment.md) | El envío de registros y métricas a un área de trabajo de Log Analytics le permite analizarlos con otros datos de supervisión recopilados por Azure Monitor mediante consultas de registro eficaces, y también usar otras características de Azure Monitor, como las alertas y las visualizaciones. |
| [Event Hubs](../../../event-hubs/index.yml) | El envío de registros y métricas a Event Hubs permite transmitir datos a sistemas externos, como SIEM de terceros y otras soluciones de análisis de registros. |
| [Cuenta de Almacenamiento de Azure](../../../storage/blobs/index.yml) | Archivar los registros y las métricas en una cuenta de almacenamiento de Azure resulta útil para realizar auditorías, análisis estáticos o copias de seguridad. En comparación con los registros de Azure Monitor y las áreas de trabajo de Log Analytics, Azure Storage es más económico y los registros se pueden mantener indefinidamente. |

La siguiente configuración es un ejemplo de lo que vería en su recurso Communications Services:

:::image type="content" source="media\enable-logging\diagnostic-setting-destination-acs.png" alt-text="Detalles del destino de la configuración de diagnóstico":::

Todas son opciones viables y flexibles que se pueden adaptar a sus necesidades de almacenamiento específicas; sin embargo, se proporcionan otras características e información de análisis integrada cuando se selecciona la opción Área de trabajo de Log Analytics.

## <a name="log-analytics-workspace-for-additional-analytics-features"></a>Área de trabajo de Log Analytics para obtener características de análisis adicionales

Al decidir enviar los registros a un destino de [área de trabajo de Log Analytics](../../../azure-monitor/logs/log-analytics-overview.md), se habilitan más características de Azure Monitor generales y otras específicas de Communication Services. Log Analytics es una herramienta que pertenece a Azure Portal que se usa para crear, editar y ejecutar [consultas](../../../azure-monitor/logs/queries.md) con datos de sus registros y métricas de Azure Monitor, así como de [libros](../../../azure-monitor/visualize/workbooks-overview.md), [alertas](../../../azure-monitor/alerts/alerts-log.md), [acciones de notificación](../../../azure-monitor/alerts/action-groups.md), [accesos a API REST](https://dev.loganalytics.io/), entre muchos otros.

Para los registros de Communications Services, hemos proporcionado un [paquete de consulta predeterminado](../../../azure-monitor/logs/query-packs.md#default-query-pack) muy útil que constituye un conjunto inicial de conclusiones para analizar y comprender rápidamente los datos. Estos paquetes de consulta se describen en [Log Analytics para Communications Services](log-analytics.md). También hemos creado muchas conclusiones y visualizaciones mediante libros, que se describen en [Libros para registros de Communications Services](insights.md).