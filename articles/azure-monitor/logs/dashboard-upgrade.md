---
title: Actualización de las visualizaciones del panel de Log Analytics
description: Obtenga información sobre cómo actualizar las visualizaciones del panel de Log Analytics con consultas que pueden proporcionar conclusiones eficaces.
ms.topic: article
author: rboucher
ms.author: robb
ms.date: 10/27/2021
ms.openlocfilehash: 67981c51fc3213d2a2f7e2b9e0834c51874a6a4a
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/03/2021
ms.locfileid: "131465409"
---
# <a name="upgrading-your-log-analytics-dashboard-visualizations"></a>Actualización de las visualizaciones del panel de Log Analytics

En febrero de 2020, presentamos una tecnología de visualización mejorada. Mejora y aumento de la capacidad de visualizar resultados de búsqueda y obtener información eficaz rápido. 

Lea más información al respecto en esta [Actualización de Azure](https://azure.microsoft.com/updates/azure-monitor-log-analytics-upgraded-results-visualization/). 

Esta tecnología de visualización nueva prepara el camino para tener experiencias nuevas y mejoradas con respecto al conjunto de resultados de la consulta. 

## <a name="dashboards-in-azure"></a>Paneles en Azure

Los paneles de Azure son una forma de visualizar el estado de toda el área expuesta de Azure. Están diseñados para proporcionar un panel único del estado de los recursos de Azure y permitir una variedad de accesos directos para las acciones comunes. 

Para más información, consulte [Paneles de Azure](../../azure-portal/azure-portal-dashboards.md).


## <a name="upgrading-log-analytics-dashboard-parts"></a>Actualización de los elementos de los paneles de Log Analytics

La nueva tecnología de visualización soluciona algunos problemas comunes con la implementación anterior y presenta algunas funcionalidades nuevas para los elementos anclados de Log Analytics: 

- **Los mismos tipos disponibles**: todos los tipos de visualización disponibles en Log Analytics están disponibles como elementos anclados en los paneles.

- **Apariencia coherente**: la apariencia de visualización de los elementos anclados ahora es casi idéntica a aquellos de Log Analytics. Las diferencias se deben a las optimizaciones que requieren diferencias sutiles en el contenido de los datos del objeto visual. Consulte la sección de consideraciones de este documento para más información sobre esas diferencias.

- **Información sobre herramientas y etiquetas**: las nuevas visualizaciones ancladas de Log Analytics admiten información sobre herramientas. Los gráficos circulares y de anillos ahora admiten etiquetas.

- **Leyendas interactivas**: al hacer clic en la leyenda de visualización, se permite agregar o quitar dimensiones del objeto visual anclado, tal como ocurre en Log Analytics.

## <a name="stage-1---opt-in-upgrade-message"></a>Fase 1: Mensaje de actualización de participación

Cuando se puede actualizar un elemento anclado de Log Analytics, aparece una notificación de *participación* nueva en los elementos de Log Analytics anclados en los paneles que permite que los usuarios actualicen la visualización. Si quiere experimentar visualizaciones nuevas, actualice las visualizaciones seleccionadas en el panel.

 
![Barra lateral](media/dashboard-upgrade/update-message-1.png)
 
![Captura de pantalla que muestra cómo actualizar la visualización de iconos.](media/dashboard-upgrade/update-message-2.png)

> [!WARNING]
> Una vez que se publica el panel, la actualización es irreversible. Sin embargo, los cambios se descartan si sale del panel sin volver a publicarlo.  

Una vez que se hace clic, la visualización se actualizará a la tecnología nueva. Se pueden producir diferencias sutiles en la visualización con el fin de alinearse con la apariencia en Log Analytics.

Una vez actualizadas las visualizaciones, debe volver a publicar el panel para que el cambio surta efecto.

![Captura de pantalla que muestra las visualizaciones actualizadas.](media/dashboard-upgrade/update-message-3.png)

## <a name="stage-2---migration-of-all-dashboards"></a>Fase 2: Migración de todos los paneles

Una vez finalizado el período de participación inicial, el equipo de Log Analytics actualizará todos los paneles del sistema. La alineación de todos los paneles de Azure permite que el equipo presente más visualizaciones y experimente mejoras en todo el panel.

## <a name="considerations"></a>Consideraciones

Las visualizaciones de Log Analytics ancladas a un panel tienen un comportamiento específico diseñado para obtener una experiencia óptima. Revise las consideraciones de diseño siguientes al anclar una visualización a un panel.

### <a name="query-time-scope---30-day-limit"></a>Ámbito de tiempo de consulta: límite de 30 días

Como los paneles pueden contener varias visualizaciones provenientes de varias consultas, el ámbito de tiempo de una consulta anclada única tiene un límite de 30 días. Una consulta única solo se puede ejecutar en un intervalo de tiempo inferior o igual a 30 días. Esta limitación es para garantizar un tiempo de carga de panel razonable.

### <a name="query-data-values---25-values-and-other-grouping"></a>Valores de datos de consulta: 25 valores y otras agrupaciones

Los paneles pueden ser visualmente densos y complejos. Con el fin de reducir la carga cognitiva al ver un panel, las visualizaciones se optimizan limitando la presentación a 25 tipos de datos distintos. Cuando hay más de 25, Log Analytics optimiza los datos. Muestra individualmente los 25 tipos con la mayoría de los datos como independientes y, a continuación, agrupa los valores restantes en un valor "otro". En el gráfico siguiente se muestra este caso.  

![Captura de pantalla que muestra un panel con 25 tipos de datos diferentes.](media/dashboard-upgrade/values-25-limit.png)

### <a name="dashboard-refresh-on-load"></a>Actualización del panel al cargar

Los paneles se actualizan en la carga. Se ejecutan todas las consultas relacionadas con las visualizaciones de Log Analytics ancladas al panel y el panel se actualiza una vez que se carga. Si la página del panel permanece abierta, los datos del panel se actualizan cada 60 minutos.

## <a name="next-steps"></a>Pasos siguientes

[Creación y uso compartido de paneles de Log Analytics](../visualize/tutorial-logs-dashboards.md)
