---
title: Informes de clasificación sobre los datos en Azure Purview mediante las conclusiones de Purview
description: En esta guía paso a paso se describe cómo ver y usar los informes de clasificación de Purview sobre los datos.
author: batamig
ms.author: bagol
ms.service: purview
ms.topic: how-to
ms.date: 09/27/2021
ms.openlocfilehash: 642611a680e0848208aa7e42493fee6fc16fb4d7
ms.sourcegitcommit: e8c34354266d00e85364cf07e1e39600f7eb71cd
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/29/2021
ms.locfileid: "129208861"
---
# <a name="classification-insights-about-your-data-from-azure-purview"></a>Conclusiones de la clasificación sobre los datos de Azure Purview

En esta guía paso a paso se describe cómo obtener acceso a los informes de información de clasificación de Purview de sus datos, y cómo verlos y filtrarlos.

> [!IMPORTANT]
> Azure Purview Insights se encuentra actualmente en versión preliminar. Los [Términos de uso complementarios para las versiones preliminares de Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) incluyen términos legales adicionales que se aplican a las características de Azure que se encuentran en la versión beta, en versión preliminar o que todavía no se han publicado para que estén disponibles con carácter general.

Los orígenes de datos compatibles incluyen: Azure Blob Storage, Azure Data Lake Storage (ADLS) GEN 1, Azure Data Lake Storage (ADLS) GEN 2, Azure Cosmos DB (SQL API), Azure Synapse Analytics (anteriormente, SQL DW), Azure SQL Database, Azure SQL Managed Instance, SQL Server, cubos de Amazon S3

En esta guía paso a paso, aprenderá a:

> [!div class="checklist"]
> - Iniciar su cuenta de Purview desde Azure.
> - Visualizar las conclusiones de la clasificación en los datos.
> - Explorar en profundidad para obtener más detalles de la clasificación de los datos.

## <a name="prerequisites"></a>Requisitos previos

Antes de empezar con las conclusiones de Purview, asegúrese de que ha completado los pasos siguientes:

- Configurar los recursos de Azure y rellenar las cuentas pertinentes con los datos de prueba.

- Configurar y completar un examen de los datos de prueba en cada origen de datos. Para más información, consulte [Administración de orígenes de datos en Azure Purview](manage-data-sources.md) y [Creación de un conjunto de reglas de examen](create-a-scan-rule-set.md).

- Ha iniciado sesión en Purview con una cuenta con el [rol de lector o conservador de datos](catalog-permissions.md#roles).

Para más información, consulte [Administración de orígenes de datos en Azure Purview](manage-data-sources.md).

## <a name="use-purview-classification-insights"></a>Uso de las conclusiones de la clasificación de Purview

En Azure Purview, las clasificaciones son similares a las etiquetas de asunto y se usan para marcar e identificar los datos de un tipo específico que se encuentra dentro en su patrimonio de datos durante el examen.

Purview usa los mismos tipos de información confidencial que Microsoft 365, lo que permite ampliar las directivas de seguridad y la protección existentes en todo su patrimonio de datos.

> [!NOTE]
> Una vez que haya examinado los tipos de origen, conceda a las conclusiones de **clasificación** un par de horas para reflejar los nuevos recursos.

**Visualización de conclusiones de la clasificación:**

1. Vaya a la [pantalla de la instancia de **Azure Purview** en Azure Portal](https://aka.ms/purviewportal) y seleccione su cuenta de Purview.

1. En la página **Información general**, en la sección **Inicio**, seleccione el icono **Purview Studio**.

1. En Purview, seleccione el elemento de menú **Conclusiones** :::image type="icon" source="media/insights/ico-insights.png" border="false"::: de la izquierda para acceder al área **Conclusiones**.

1. En el área **Conclusiones** :::image type="icon" source="media/insights/ico-insights.png" border="false":::, seleccione **Clasificación** para mostrar el informe **Classification insights** (Conclusiones de la clasificación) de Purview.

   :::image type="content" source="./media/insights/select-classification-labeling.png" alt-text="Informe de conclusiones de la clasificación" lightbox="media/insights/select-classification-labeling.png":::

   La página **Classification insights** (Conclusiones de la clasificación) principal muestra las áreas siguientes:

   |Área  |Descripción  |
   |---------|---------|
   |**Overview of sources with classifications** (Información general de los orígenes con clasificaciones)     |Muestra iconos que proporcionan: <br>- El número de suscripciones encontradas en los datos. <br>- El número de clasificaciones únicas encontradas en los datos. <br>- El número de orígenes clasificados encontrados. <br>- El número de archivos clasificados encontrados. <br>- El número de tablas clasificadas encontradas.         |
   |**Top sources with classified data (last 30 days)** (Principales orígenes con datos clasificados [últimos 30 días])     |Muestra la tendencia, en los últimos 30 días, del número de orígenes encontrados con datos clasificados.            |
   |**Top classification categories by sources** (Principales categorías de clasificación por orígenes)     |Muestra el número de orígenes encontrados por categoría de clasificación, como **Finanzas** o **Gubernamental**.      |
   |**Top classifications for files** (Principales clasificaciones de archivos)     |Muestra las clasificaciones principales aplicadas a los archivos de los datos, como números de tarjeta de crédito o números de identificación nacional.         |
   |**Top classifications for tables** (Principales clasificaciones de tablas)     | Muestra las clasificaciones principales aplicadas a tablas de los datos, como la información de identificación personal. |   
   |  **Classification activity** (Actividad de clasificación) <br>(archivos y tablas) |  Muestra gráficos independientes para archivos y tablas, cada uno de los cuales muestra el número de archivos o tablas clasificados durante el período de tiempo seleccionado. <br>**Valor predeterminado**: 30 días<br>Seleccione el filtro **Tiempo** que se encuentra sobre los gráficos para seleccionar otro intervalo de tiempo para mostrar.    |
   |    |    |

## <a name="classification-insights-drilldown"></a>Desglose de conclusiones de la clasificación

En cualquiera de los siguientes grafos de **Classification insights** (Conclusiones de la clasificación), seleccione el vínculo **Ver más** para explorar en profundidad y obtener más detalles:

- **Top classification categories by sources** (Principales categorías de clasificación por orígenes)
- **Top classifications for files** (Principales clasificaciones de archivos)
- **Top classifications for tables** (Principales clasificaciones de tablas)
- **Classification activity > Classification data** (Actividad de clasificación > Datos de clasificación)

Por ejemplo:

:::image type="content" source="media/insights/view-classifications-small.png" alt-text="Ver todas las clasificaciones":::

Para obtener más información, realiza cualquiera de las acciones siguientes:

|Opción  |Descripción  |
|---------|---------|
|**Filtrado de los datos**     |  Use los filtros que se encuentran sobre la cuadrícula para filtrar los datos mostrados, incluido el nombre de la clasificación, el nombre de la suscripción o el tipo de origen. <br><br>Si no está seguro del nombre exacto de la clasificación, puede escribir parte o todo el nombre en el cuadro **Filtrar por palabra clave**.       |
|**Sort the grid** (Ordenar la cuadrícula) |Seleccione un encabezado de columna para ordenar la cuadrícula por esa columna. | 
|**Editar columnas**     |  Para mostrar más o menos columnas en la cuadrícula, seleccione **Editar columnas** :::image type="icon" source="media/insights/ico-columns.png" border="false"::: y luego seleccione las columnas que desea ver o cuyo orden desea cambiar.   |
|**Drill down further** (Explorar en profundidad)     | Para profundizar en una clasificación específica, seleccione un nombre en la columna **Clasificación** para ver el informe **Classification by source** (Clasificación por origen). <br><br>Este informe muestra los datos de la clasificación seleccionada, incluido el nombre del origen, el tipo de origen, el identificador de la suscripción y los números de archivos y tablas clasificados.      |
|**Examinar los activos**     |  Para examinar los recursos encontrados con una clasificación o un origen específicos, seleccione una clasificación o un origen, según el informe que esté visualizando y, a continuación, seleccione **Examinar los activos** :::image type="icon" source="media/insights/ico-browse-assets.png" border="false"::: encima de los filtros. <br><br>Los resultados de la búsqueda muestran todos los recursos clasificados que se encontraron para el filtro seleccionado.  Para obtener más información, consulte [Buscar en el catálogo de datos de Azure Purview](how-to-search-catalog.md).       |
| | |

## <a name="next-steps"></a>Pasos siguientes

Obtenga más información sobre los informes de conclusiones de Azure Purview
> [!div class="nextstepaction"]
> [Conclusiones de glosarios](glossary-insights.md)

> [!div class="nextstepaction"]
> [Conclusiones de exámenes](scan-insights.md)

> [!div class="nextstepaction"]
> [Conclusiones del etiquetado de confidencialidad](./sensitivity-insights.md)

> [!div class="nextstepaction"]
> [Conclusiones de extensiones de archivo](file-extension-insights.md)
