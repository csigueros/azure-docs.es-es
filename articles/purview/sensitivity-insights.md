---
title: Informes de etiquetas de confidencialidad sobre los datos de Azure Purview mediante las conclusiones de Purview
description: En esta guía paso a paso se describe cómo ver y usar los informes de etiqueta de confidencialidad de Purview de los datos.
author: batamig
ms.author: bagol
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: how-to
ms.date: 01/17/2021
ms.openlocfilehash: d3b828aab13a822c307119dca64dac81c9201d5b
ms.sourcegitcommit: ddac53ddc870643585f4a1f6dc24e13db25a6ed6
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/18/2021
ms.locfileid: "122397804"
---
# <a name="sensitivity-label-insights-about-your-data-in-azure-purview"></a>Conclusiones de la etiqueta de confidencialidad sobre los datos de Azure Purview

En esta guía paso a paso se describe cómo acceder a conclusiones de seguridad que las etiquetas de confidencialidad que se aplican a los datos proporcionan. En la guía también se describe cómo ver y filtrar esas conclusiones.

Los orígenes de datos compatibles incluyen: Azure Blob Storage, Azure Data Lake Storage (ADLS) GEN 1, Azure Data Lake Storage (ADLS) GEN 2, SQL Server, Azure SQL Database, Azure SQL Managed Instance y cubos de Amazon S3

En esta guía paso a paso, aprenderá a:

> [!div class="checklist"]
> - Iniciar su cuenta de Purview desde Azure.
> - Ver conclusiones del etiquetado de confidencialidad sobre los datos
> - Explorar en profundidad para obtener más información sobre cómo el etiquetado de confidencialidad de los datos

> [!NOTE]
> Las etiquetas de confidencialidad que se encuentran en los [recursos de Power BI](register-scan-power-bi-tenant.md) que Purview examina no se muestran actualmente en el informe de conclusiones de etiquetas de confidencialidad. 
>
> Para ver las etiquetas de confidencialidad de los recursos de Power BI, consulte el recurso en [Purview Data Catalog](how-to-search-catalog.md).
> 
## <a name="prerequisites"></a>Requisitos previos

Antes de empezar con las conclusiones de Purview, asegúrese de que ha completado los pasos siguientes:

- Configurar los recursos de Azure y rellenar las cuentas pertinentes con los datos de prueba

- [Etiquetas de confidencialidad de Microsoft 365 extendidas a los recursos de Azure ámbito](create-sensitivity-label.md), y crear o seleccionar las etiquetas que desea aplicar a los datos.

- Configurar y completar un examen de los datos de prueba en cada origen de datos. Para más información, consulte [Administración de orígenes de datos en Azure Purview (versión preliminar)](manage-data-sources.md) y [Creación de un conjunto de reglas de examen](create-a-scan-rule-set.md).

- Iniciar sesión en Purview con una cuenta con un [rol de lector o conservador de datos](catalog-permissions.md#roles).

Para obtener más información, consulte [Administrar orígenes de datos en Azure Purview (versión preliminar)](manage-data-sources.md) y [Etiquetar automáticamente los datos en Azure Purview](create-sensitivity-label.md).

## <a name="use-purview-sensitivity-labeling-insights"></a>Uso de conclusiones del etiquetado de confidencialidad de Purview

En Purview, las clasificaciones son similares a las etiquetas de asunto y se usan para marcar e identificar los datos de un tipo específico que se encuentra dentro de la información de datos durante el análisis.

Las etiquetas de confidencialidad permiten indicar el grado de confidencialidad de determinados datos de la organización. Por ejemplo, un nombre de proyecto específico puede ser muy confidencial dentro de la organización, mientras que el mismo término no es confidencial para otras organizaciones. 

Las clasificaciones se hacen coincidir directamente, como un número del seguro social, que tiene una clasificación de **Número del seguro social**. 

En cambio, las etiquetas de confidencialidad se aplican cuando una o varias clasificaciones y condiciones se encuentran juntas. En este contexto, las [condiciones](/microsoft-365/compliance/apply-sensitivity-label-automatically) hacen referencia a todos los parámetros que se pueden definir para los datos no estructurados, como la **proximidad a otra clasificación** y el **% de confianza**. 

Purview usa las mismas clasificaciones que Microsoft 365, también conocidas como [tipos de información confidencial](/microsoft-365/compliance/sensitive-information-type-entity-definitions). Esto le permite extender las etiquetas de confidencialidad existentes a través de los recursos de Azure Purview.

> [!NOTE]
> Una vez que haya examinado los tipos de origen, conceda a las conclusiones del **etiquetado de confidencialidad** un par de horas para reflejar los nuevos recursos.

**Para ver conclusiones del etiquetado de confidencialidad:**

1. Vaya a la página principal de **Azure Purview**.

1. En la página **Información general**, en la sección **Inicio**, seleccione el icono **Launch Purview account** (Iniciar cuenta de Purview).

1. En Purview, seleccione el elemento de menú **Conclusiones** :::image type="icon" source="media/insights/ico-insights.png" border="false"::: de la izquierda para acceder al área **Conclusiones**.

1. En el área **Conclusiones** :::image type="icon" source="media/insights/ico-insights.png" border="false":::, seleccione **Etiquetas de confidencialidad** para mostrar el informe **Sensitivity labeling insights** (Conclusiones del etiquetado de confidencialidad) de Purview.

    > [!NOTE]
    > Si este informe está vacío, es posible que no haya extendido las etiquetas de confidencialidad a Azure Purview. Para obtener más información, vea [Etiquetado automático de los datos en Azure Purview](create-sensitivity-label.md).

   :::image type="content" source="media/insights/sensitivity-labeling-insights-small.png" alt-text="Conclusiones del etiquetado de confidencialidad" lightbox="media/insights/sensitivity-labeling-insights.png":::

   La página **Sensitivity labeling insights** (Conclusiones del etiquetado de confidencialidad) muestra las áreas siguientes:

   |Área  |Descripción  |
   |---------|---------|
   |**Overview of sources with sensitivity labels** (Introducción a los orígenes con etiquetas de confidencialidad)     |Muestra iconos que proporcionan: <br>- El número de suscripciones encontradas en los datos. <br>- El número de etiquetas de confidencialidad exclusivas aplicadas a los datos <br>- El número de orígenes con etiquetas de confidencialidad aplicadas <br>- El número de archivos y tablas encontrados con etiquetas de confidencialidad aplicadas|
   |**Top sources with labeled data (last 30 days)** (Principales orígenes con datos etiquetados [últimos 30 días])     | Muestra la tendencia, en los últimos 30 días, del número de orígenes con etiquetas de confidencialidad aplicadas.       |
   |**Top labels applied across sources** (Etiquetas principales aplicadas en los orígenes)     |Muestra las etiquetas principales que se aplican a todos los recursos de datos de Purview. |
   |**Top labels applied on files** (Etiquetas principales aplicadas a los archivos)     |Muestra las etiquetas de confidencialidad principales aplicadas a los archivos en los datos.          |
   |**Top labels applied on files** (Etiquetas principales aplicadas a las tablas)     | Muestra las etiquetas de confidencialidad principales aplicadas a las tablas de base de datos en los datos. |   
   |  **Labeling activity** (Actividad de etiquetado)  |  Muestra gráficos independientes para archivos y tablas, cada uno de los cuales muestra el número de archivos o tablas etiquetados sobre el período de tiempo seleccionado. <br>**Valor predeterminado**: 30 días<br>Seleccione el filtro **Tiempo** que se encuentra sobre los gráficos para seleccionar otro intervalo de tiempo para mostrar.    |
   |    |    |

## <a name="sensitivity-labeling-insights-drilldown"></a>Exploración en profundidad de conclusiones del etiquetado de confidencialidad

En cualquiera de los siguientes grafos de **Sensitivity labeling insights** (Conclusiones de etiquetado de confidencialidad), seleccione el vínculo **Ver más** para explorar en profundidad y obtener más detalles:

- **Top labels applied across sources** (Etiquetas principales aplicadas en los orígenes)
- **Top labels applied on files** (Etiquetas principales aplicadas a los archivos)
- **Top labels applied on files** (Etiquetas principales aplicadas a las tablas)
- **Labeling activity (Etiquetado de actividad) > Labeled data (Datos etiquetados)**

Por ejemplo:

:::image type="content" source="media/insights/sensitivity-label-drilldown-small.png" alt-text="Exploración en profundidad de etiquetas de confidencialidad" lightbox="media/insights/sensitivity-label-drilldown.png":::

Para obtener más información, ejecute cualquiera de las opciones siguientes:

|Opción  |Descripción  |
|---------|---------|
|**Filtrado de los datos**     |  Use los filtros que se encuentran sobre la cuadrícula para filtrar los datos mostrados, incluido el nombre de la etiqueta, el nombre de la suscripción o el tipo de origen. <br><br>Si no está seguro del nombre exacto de la etiqueta, puede escribir parte o todo el nombre en el cuadro **Filtrar por palabra clave**.       |
|**Sort the grid** (Ordenar la cuadrícula) |Seleccione un encabezado de columna para ordenar la cuadrícula por esa columna. | 
|**Editar columnas**     |  Para mostrar más o menos columnas en la cuadrícula, seleccione **Editar columnas** :::image type="icon" source="media/insights/ico-columns.png" border="false"::: y luego seleccione las columnas que desea ver o cuyo orden desea cambiar.    <br><br>Seleccione un encabezado de columna para ordenar la cuadrícula por esa columna.   |
|**Drill down further** (Explorar en profundidad)     | Para explorar en profundidad una etiqueta específica, seleccione un nombre en la columna **Etiqueta de confidencialidad** para ver el informe **Label by source** (Etiqueta por informe) de código fuente. <br><br>Este informe muestra los datos de la etiqueta seleccionada, incluido el nombre del origen, el tipo de origen, el identificador de la suscripción y los números de archivos y tablas clasificados.      |
|**Examinar los activos**     |  Para examinar los recursos encontrados con una etiqueta u origen específicos, seleccione una o varias etiquetas u orígenes, en función del informe que esté viendo y, luego seleccione la opción **Examinar los activos** :::image type="icon" source="media/insights/ico-browse-assets.png" border="false"::: que se encuentra sobre los filtros. <br><br>Los resultados de la búsqueda muestran todos los recursos etiquetados encontrados para el filtro seleccionado.  Para obtener más información, consulte [Buscar en el catálogo de datos de Azure Purview](how-to-search-catalog.md).       |
| | |

## <a name="sensitivity-label-integration-with-microsoft-365-compliance"></a>Integración de etiquetas de confidencialidad con el cumplimiento de Microsoft 365

La estrecha integración con [Microsoft Information Protection](/microsoft-365/compliance/information-protection) que se ofrece en Microsoft 365 significa que Purview posibilita formas directas de extender la visibilidad de los datos y clasificar y etiquetar estos.

Para que las etiquetas de confidencialidad de Microsoft 365 se extiendan a los recursos de Azure Purview, debe activar activamente Information Protection paras Azure Purview en el centro de cumplimiento de Microsoft 365.

Para obtener más información, vea [Etiquetado automático de los datos en Azure Purview](create-sensitivity-label.md).

## <a name="next-steps"></a>Pasos siguientes

Obtenga más información sobre estos informes de conclusiones de Azure Purview:

- [Conclusiones de glosarios](glossary-insights.md)
- [Conclusiones de exámenes](scan-insights.md)
- [Conclusiones de clasificaciones](./classification-insights.md)
- [Conclusiones de extensiones de archivo](file-extension-insights.md)
