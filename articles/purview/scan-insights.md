---
title: Información de exámenes sobre los datos de Azure Purview
description: En esta guía paso a paso se describe cómo ver y usar los informes de información de exámenes de Purview sobre los datos.
author: SunetraVirdi
ms.author: suvirdi
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: how-to
ms.date: 11/20/2020
ms.openlocfilehash: 3c5a3b9b312e093d7bee5c0c555e8e2eab0c531b
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/13/2021
ms.locfileid: "121731142"
---
# <a name="scan-insights-on-your-data-in-azure-purview"></a>Información de exámenes sobre los datos de Azure Purview

En esta guía paso a paso se describe cómo obtener acceso, ver y filtrar los informes de información detallada de exámenes de Azure Purview para los datos.

En esta guía paso a paso, aprenderá a:

> [!div class="checklist"]
> * Ver información desde la cuenta de Purview.
> * Obtener una visión general de los exámenes.

## <a name="prerequisites"></a>Requisitos previos

Antes de empezar a usar la información detallada de Purview, asegúrese de que ha completado los pasos siguientes:

* Ha configurado los recursos de Azure y ha rellenado la cuenta con datos.
* Ha configurado y realizado un examen en el origen de datos.

Para más información, consulte [Administración de orígenes de datos en Azure Purview (versión preliminar)](manage-data-sources.md).

## <a name="use-purview-scan-insights"></a>Usar la información de exámenes de Purview

En Azure Purview, puede registrar y examinar los tipos de origen. Puede ver el estado de los exámenes a lo largo del tiempo en Información de exámenes. La información indica el número de exámenes erróneos, correctos o cancelados dentro de un período de tiempo determinado.

### <a name="view-scan-insights"></a>Visualización de información de exámenes

1. Vaya a la pantalla de instancia **Azure Purview** de Azure Portal y seleccione su cuenta de Purview.

1. En la página **Información general**, en la sección **Inicio**, seleccione el icono **Open Purview Studio** (Abrir Purview Studio).

   :::image type="content" source="./media/scan-insights/portal-access.png" alt-text="Inicio de Purview desde Azure Portal":::

1. En la página **Home** (Inicio) de Purview, seleccione el icono **View insights** (Ver información) para acceder al área **Insights** :::image type="icon" source="media/scan-insights/ico-insights.png" border="false"::: (Información detallada).

   :::image type="content" source="./media/scan-insights/view-insights.png" alt-text="Visualización de información detallada en Azure Portal":::

1. En el área **Insights** (Información detallada) :::image type="icon" source="media/scan-insights/ico-insights.png" border="false":::, seleccione **Scans** (Exámenes) para mostrar el informe **Scan insights** (Información detallada de exámenes).

### <a name="view-high-level-kpis-to-show-count-of-scans-by-status-and-deep-dive-into-each-scan"></a>Visualización de los indicadores clave de rendimiento de alto nivel para mostrar el recuento de exámenes por estado y profundizar en cada examen
 
1. Los KPI de alto nivel muestran los exámenes totales que se ejecutan dentro de un período. El período se toma como valor predeterminado en los últimos 30 días. Sin embargo, también puede seleccionar los últimos siete días. Según el filtro de tiempo, los valores de KPI reflejan el número de exámenes correctamente.


1. Según el valor del filtro de tiempo seleccionado, puede ver la distribución de los exámenes correctos, con errores y cancelados por semana o por día en el gráfico.

1. En la parte inferior del grafo, hay un vínculo **Ver más** para que pueda explorar más. El vínculo abre la página **Estado de exploración** en la experiencia de información de exámenes. Aquí puede ver el nombre de un examen y el número de veces que se ha realizado correctamente, se ha producido un error o se ha cancelado en los últimos 30 días.

    :::image type="content" source="./media/scan-insights/main-graph.png" alt-text="Ver el estado del examen a lo largo del tiempo":::

4. Para explorar más detalladamente un examen específico, haga clic en el **nombre del examen**, lo que le conectará con el historial de exámenes dentro de la experiencia de **Mapa de datos** de Azure Purview. En la página Historial de ejecución, puede obtener el identificador de la ejecución que le ayudará en la investigación de errores.

    :::image type="content" source="./media/scan-insights/scan-status.png" alt-text="Ver detalles del examen":::

5. Por último, para volver a la página **Estado de exploración** de la información de exámenes puede seguir las rutas de navegación de la esquina superior izquierda de la página Historial de ejecución.

    :::image type="content" source="./media/scan-insights/scan-history.png" alt-text="Visualización del historial de exámenes"::: 

## <a name="next-steps"></a>Pasos siguientes

* Más información sobre los **informes** de Azure Purview con [información de datos](./concept-insights.md)

* Más información sobre la experiencia de **Mapa de datos** de Azure Purview con [Administrar orígenes de datos](./manage-data-sources.md)