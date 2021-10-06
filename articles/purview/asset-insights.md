---
title: Información detallada de recursos en los datos de Azure Purview
description: En esta guía paso a paso se describe cómo ver y usar los informes de información detallada de recursos de Purview en los datos.
author: SunetraVirdi
ms.author: suvirdi
ms.service: purview
ms.subservice: purview-insights
ms.topic: how-to
ms.date: 09/27/2021
ms.openlocfilehash: 8879edc7d1858cff5871c5339da4857d81f5c2d8
ms.sourcegitcommit: e8c34354266d00e85364cf07e1e39600f7eb71cd
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/29/2021
ms.locfileid: "129217086"
---
# <a name="asset-insights-on-your-data-in-azure-purview"></a>Información detallada de recursos en los datos de Azure Purview

En esta guía paso a paso se describe cómo obtener acceso a los informes de información detallada de recursos de Purview en los datos, y cómo verlos y filtrarlos.

> [!IMPORTANT]
> Azure Purview Insights se encuentra actualmente en versión preliminar. Los [Términos de uso complementarios para las versiones preliminares de Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) incluyen términos legales adicionales que se aplican a las características de Azure que se encuentran en la versión beta, en versión preliminar o que todavía no se han publicado para que estén disponibles con carácter general.

En esta guía paso a paso, aprenderá a:

> [!div class="checklist"]
> * Ver información desde la cuenta de Purview.
> * Obtener una visión general de los datos.
> * Explorar en profundidad para obtener más detalles sobre el recuento de recursos.

## <a name="prerequisites"></a>Requisitos previos

Antes de empezar a usar la información detallada de Purview, asegúrese de que ha completado los pasos siguientes:

* Ha configurado los recursos de Azure y ha rellenado la cuenta con datos.

* Ha configurado y realizado un examen en el tipo de origen.

Para más información, consulte [Administración de orígenes de datos en Azure Purview](manage-data-sources.md).

## <a name="use-purview-asset-insights"></a>Uso de la información detallada de recursos de Purview

En Azure Purview, puede registrar y examinar los tipos de origen. Una vez completado el examen, puede ver la distribución de recursos en la sección de información detallada de recursos, que le indica el estado de los datos en función de la clasificación y los conjuntos de recursos. También indica si hay algún cambio en el tamaño de los datos.

> [!NOTE]
> Después de haber examinado los tipos de origen, la información detallada de recursos tarda entre tres y ocho horas en reflejar los nuevos recursos. El retraso puede deberse a un tráfico elevado en la región de implementación o al tamaño de la carga de trabajo. Para obtener más información, póngase en contacto con el equipo de soporte técnico de campo.

1. Vaya al recurso de Azure Purview en Azure Portal.

1. En la página **Información general**, en la sección **Inicio**, seleccione el icono **Open Purview Studio** (Abrir Purview Studio).

   :::image type="content" source="./media/asset-insights/portal-access.png" alt-text="Inicio de Purview desde Azure Portal":::

1. En la página **Inicio** de Purview, seleccione **Información** en el menú de la izquierda.

   :::image type="content" source="./media/asset-insights/view-insights.png" alt-text="Visualización de información detallada en Azure Portal":::

1. En el área **Información**, seleccione **Recursos** para mostrar el informe **Asset insights** (Información de recursos) de Purview.

### <a name="view-asset-insights"></a>Visualización de información detallada de recursos

1. La página **Asset Insights** (Información detallada de recursos) muestra las áreas siguientes:

2. KPI de alto nivel para mostrar los tipos de origen, los recursos clasificados y los recursos detectados
 
3. En el primer gráfico se muestran los **recursos por tipo de origen**.

4. Vea la distribución de recursos por tipo de origen. Elija una clasificación o una categoría de clasificación completa para ver la distribución de recursos por tipo de origen. 
 
5. Para más información, seleccione **View more** (Ver más), que muestra un formulario tabular de los tipos de origen y los recuentos de recursos. Los filtros de clasificación se transfieren a esta página.

   :::image type="content" source="./media/asset-insights/highlight-kpis.png" alt-text="Visualización de KPI y gráficos en la información detallada de recursos":::
 
6. Seleccione un origen específico del que desea ver las carpetas superiores con los recuentos de recursos. 

   :::image type="content" source="./media/asset-insights/select-data-source.png" alt-text="Selección del tipo de origen":::
 
7. Seleccione el número total de recursos de la carpeta superior dentro del tipo de origen que ha seleccionado anteriormente.

   :::image type="content" source="./media/asset-insights/file-path.png" alt-text="Visualización de las rutas de acceso a archivos":::

8. Vea la lista de archivos que contiene la carpeta. Vuelva a la información detallada mediante las barras de ruta de navegación.

   :::image type="content" source="./media/asset-insights/list-page.png" alt-text="Visualización de la lista de recursos":::  

### <a name="file-based-source-types"></a>Tipos de origen basados en archivos
El siguiente par de gráficos de la información detallada de recursos muestra una distribución de los tipos de origen basados en archivos. El primer gráfico, denominado **Size trend (GB) of file type within source types** (Tendencia de tamaño [GB] del tipo de archivo dentro de los tipos de origen) muestra la tendencia del tamaño del tipo de archivo durante los últimos 30 días. 
 
1. Elija el tipo de origen para ver el tipo de archivo dentro del origen. 
 
1. Seleccione **View more** (Ver más) para ver el tamaño actual de los datos, el cambio de tamaño, el número actual de recursos y el cambio en el recuento de recursos.
 
   > [!NOTE]
   > Si el examen se ha ejecutado solo una vez en los últimos 30 días o se ha producido algún cambio en el catálogo, como la adición o eliminación de una clasificación, solo una vez en ese período, la información sobre los cambios aparece en blanco.

1. Al seleccionar el tipo de origen, verá las carpetas superiores con los cambios en los recuentos de los recursos principales.

1. Seleccione la ruta de acceso para ver la lista de recursos.

El segundo gráfico de los tipos de origen basados en archivos es ***Files not associated with a resource set*** (Archivos no asociados con un conjunto de recursos). Si espera que todos los archivos se acumulen en un conjunto de recursos, este gráfico puede ayudarle a entender qué recursos no se han acumulado. Los recursos que faltan pueden indicar un patrón de archivo incorrecto en la carpeta. Siga los mismos pasos que en otros gráficos para ver más detalles sobre los archivos.

   :::image type="content" source="./media/asset-insights/file-based-assets.png" alt-text="Visualización de recursos basados en archivos":::  

## <a name="next-steps"></a>Pasos siguientes

Más información sobre los informes de información detallada de Azure Purview con [Información detallada de exámenes](./scan-insights.md).
