---
title: 'Priorización de las acciones de seguridad por confidencialidad de datos: Microsoft Defender for Cloud'
description: Uso de las clasificaciones de confidencialidad de datos de Azure Purview en Microsoft Defender for Cloud
author: memildin
ms.author: memildin
ms.date: 11/02/2021
ms.topic: overview
ms.service: security-center
manager: rkarlin
ms.openlocfilehash: 7f82750d9b02bba6e843390562ae2eee060cef47
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/03/2021
ms.locfileid: "131478124"
---
# <a name="prioritize-security-actions-by-data-sensitivity"></a>Priorización de las acciones de seguridad por confidencialidad de datos

[!INCLUDE [Banner for top of topics](./includes/banner.md)]

[Azure Purview](../purview/overview.md), el servicio de gobernanza de datos de Microsoft, proporciona información detallada sobre la *confidencialidad de los datos*. Con la detección de datos automatizada, la clasificación de datos confidenciales y el linaje de datos de un extremo a otro, Purview ayuda a las organizaciones a administrar y controlar los datos en entornos híbridos y de varias nubes.

Los clientes de Microsoft Defender for Cloud que usan Azure Purview pueden beneficiarse de un nivel esencial adicional de metadatos en alertas y recomendaciones: información sobre todos los datos potencialmente confidenciales implicados. Este conocimiento ayuda a resolver el desafío de la evaluación de prioridades y garantiza que los profesionales de seguridad puedan centrar su atención en las amenazas a la información confidencial.

En esta página se explica la integración de las etiquetas de clasificación de confidencialidad de datos de Purview en Defender for Cloud.

## <a name="availability"></a>Disponibilidad
|Aspecto|Detalles|
|----|:----|
|Estado de la versión:|Versión preliminar.<br>[!INCLUDE [Legalese](../../includes/security-center-preview-legal-text.md)]|
|Precios:|Necesitará una cuenta de Azure Purview para crear las clasificaciones de confidencialidad de datos y ejecutar los exámenes. Los usuarios de Defender for Cloud pueden ver y usar los resultados de los exámenes de manera gratuita.|
|Roles y permisos necesarios:|**Administrador de seguridad** y **colaborador de seguridad**|
|Nubes:|:::image type="icon" source="./media/icons/yes-icon.png"::: Nubes comerciales<br>:::image type="icon" source="./media/icons/no-icon.png"::: Azure Government<br>:::image type="icon" source="./media/icons/no-icon.png"::: Azure China 21Vianet (**parcial**: subconjunto de alertas y evaluación de vulnerabilidades en servidores SQL. No hay disponible protección contra amenazas de comportamiento).|
|||


## <a name="the-triage-problem-and-defender-for-clouds-solution"></a>El problema de la evaluación de prioridades y la solución de Defender for Cloud
Los equipos de seguridad se enfrentan habitualmente al desafío de cómo evaluar los problemas entrantes. 

Defender for Cloud incluye dos mecanismos para ayudar a priorizar las recomendaciones y las alertas de seguridad:

- Para las recomendaciones, proporcionamos **controles de seguridad** para ayudarle a comprender la importancia que cada una de ellas tiene para la posición de seguridad general. Defender for Cloud incluye un valor de **puntuación de seguridad** para cada control que le ayudará a priorizar el trabajo de seguridad. Obtenga más información en [Controles de seguridad y sus recomendaciones](secure-score-security-controls.md#security-controls-and-their-recommendations).

- Para las alertas, hemos asignado **etiquetas de gravedad** a cada alerta para ayudarle a priorizar el orden en el que atender cada alerta. Obtenga más información en [¿Cómo se clasifican las alertas?](alerts-overview.md#how-are-alerts-classified).

No obstante, siempre que sea posible, conviene centrar los esfuerzos del equipo de seguridad en los riesgos para los **datos** de la organización. Si dos recomendaciones tienen el mismo impacto en la puntuación de seguridad, pero una se relaciona con un recurso con datos confidenciales, lo ideal sería incluir ese conocimiento al determinar la priorización.

Las clasificaciones de confidencialidad de datos y las etiquetas de confidencialidad de datos de Azure Purview proporcionan ese conocimiento.

## <a name="discover-resources-with-sensitive-data"></a>Detección de recursos con datos confidenciales
Para proporcionar la información fundamental sobre los datos confidenciales detectados y garantizar que dispone de esa información cuando la necesita, Defender for Cloud muestra información de Purview en varias ubicaciones.

> [!TIP]
> Si varias cuentas de Purview examinan un recurso, la información que se muestra en Defender for Cloud está relacionada con el examen más reciente.


### <a name="alerts-and-recommendations-pages"></a>Páginas de alertas y recomendaciones
Al revisar una recomendación o investigar una alerta, la información sobre los datos potencialmente confidenciales implicados se incluye en la página.

Este nivel esencial adicional de metadatos ayuda a resolver el desafío de la evaluación de prioridades y garantiza que el equipo de seguridad pueda centrar su atención en las amenazas a la información confidencial.



### <a name="inventory-filters"></a>Filtros de inventario
La [página de inventario de recursos](asset-inventory.md) tiene una colección de filtros eficaces para agrupar los recursos con alertas y recomendaciones pendientes según los criterios pertinentes para cada escenario. Estos filtros incluyen **clasificaciones de confidencialidad de datos** y **etiquetas de confidencialidad de datos**. Use estos filtros para evaluar la posición de seguridad de los recursos en los que Purview ha detectado datos confidenciales.

:::image type="content" source="./media/information-protection/information-protection-inventory-filters.png" alt-text="Captura de pantalla de filtros de protección de información en la página de inventario de recursos de Microsoft Defender for Cloud." lightbox="./media/information-protection/information-protection-inventory-filters.png":::

### <a name="resource-health"></a>Estado de los recursos 
Al seleccionar un único recurso, ya sea de una alerta, una recomendación o la página de inventario, se llega a una página de estado detallada que muestra una vista centrada en el recurso con información de seguridad importante relacionada con ese recurso. 

La página de estado de los recursos proporciona una vista de instantánea del estado general de un único recurso. Puede revisar información detallada sobre el recurso y todas las recomendaciones que se aplican a ese recurso. Además, si usa alguno de los planes de Microsoft Defender, también puede ver alertas de seguridad pendientes para ese recurso específico.

Al revisar el estado de un recurso específico, verá la información de Purview en esta página y podrá usarla para determinar qué datos se han detectado en este recurso junto con la cuenta de Purview usada para examinar el recurso.

:::image type="content" source="./media/information-protection/information-protection-resource-health.png" alt-text="Captura de pantalla de la página de estado del recurso de Defender for Cloud que muestra las etiquetas y clasificaciones de protección de información de Azure Purview." lightbox="./media/information-protection/information-protection-resource-health.png":::

### <a name="overview-tile"></a>Icono de información general
El icono **Information Protection** dedicado del [panel de información general](overview-page.md) de Defender for Cloud muestra la cobertura de Azure Purview. También muestra los tipos de recursos con los datos más confidenciales detectados.

Un gráfico muestra el número de recomendaciones y alertas por tipos de recursos clasificados. El icono también incluye un vínculo a Azure Purview para examinar recursos adicionales. Seleccione el icono para ver los recursos clasificados en la página de inventario de recursos de Defender for Cloud.

:::image type="content" source="./media/information-protection/overview-dashboard-information-protection.png" alt-text="Captura de pantalla del icono Information Protection en el panel de información general de Microsoft Defender for Cloud." lightbox="./media/information-protection/overview-dashboard-information-protection.png":::


## <a name="next-steps"></a>Pasos siguientes

Para obtener información relacionada, consulte:

- [¿Qué es Azure Purview?](../purview/overview.md)
- [Orígenes de datos y tipos de archivo admitidos de Purview](../purview/sources-and-scans.md) y [almacenes de datos admitidos](../purview/purview-connector-overview.md)
- [Procedimientos recomendados de implementación de Azure Purview](../purview/deployment-best-practices.md)
- [Etiquetado de los datos en Azure Purview](../purview/how-to-automatically-label-your-content.md)
