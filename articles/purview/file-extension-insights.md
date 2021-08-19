---
title: Informes de extensión de archivos sobre los datos de Azure Purview mediante las conclusiones de Purview
description: En esta guía paso a paso se describe cómo ver y usar los informes de extensión de archivos de Purview sobre los datos.
author: batamig
ms.author: bagol
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: how-to
ms.date: 01/17/2021
ms.openlocfilehash: f581594e6830efcf849f4ba51b218ce849bf3f9c
ms.sourcegitcommit: ddac53ddc870643585f4a1f6dc24e13db25a6ed6
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/18/2021
ms.locfileid: "122396957"
---
# <a name="file-extension-insights-about-your-data-from-azure-purview"></a>Conclusiones de extensión de archivo sobre los datos en Azure Purview 

En esta guía paso a paso se describe cómo acceder a las conclusiones sobre las extensiones de archivo o los tipos de archivo que se encuentran en los datos. En la guía también se describe cómo ver y filtrar esas conclusiones.

Los orígenes de datos compatibles incluyen: Azure Blob Storage, Azure Data Lake Storage (ADLS) GEN 1, Azure Data Lake Storage (ADLS) GEN 2 y cubos de Amazon S3

En esta guía paso a paso, aprenderá a:
> [!div class="checklist"]
> * Iniciar su cuenta de Purview desde Azure. 
> - Ver conclusiones de extensión de archivo en los datos.
> - Explorar en profundidad para obtener más detalles de extensión de archivo de los datos.

## <a name="prerequisites"></a>Requisitos previos 

Antes de empezar con las conclusiones de Purview, asegúrese de que ha completado los pasos siguientes:

- Configurar los recursos de Azure y rellenar las cuentas pertinentes con los datos de prueba.

- Configurar y completar un examen de los datos de prueba en cada origen de datos. Para más información, consulte [Administración de orígenes de datos en Azure Purview (versión preliminar)](manage-data-sources.md) y [Creación de un conjunto de reglas de examen](create-a-scan-rule-set.md).

- Ha iniciado sesión en Purview con una cuenta con el [rol de lector o conservador de datos](catalog-permissions.md#roles).


Para más información, consulte [Administración de orígenes de datos en Azure Purview (versión preliminar)](manage-data-sources.md).

## <a name="use-purview-file-extension-insights"></a>Uso de las conclusiones de extensión de archivo de Purview

Al examinar los recursos, Azure Purview puede detectar los tipos de archivo que se encuentran en el patrimonio de datos y proporcionar más detalles sobre cada tipo de archivo. Los detalles incluyen cuántos archivos de cada tipo tiene, dónde se encuentran esos archivos y si se pueden analizar para comprobar si tienen datos confidenciales.

> [!NOTE]
> Una vez que haya examinado los tipos de origen, conceda a las conclusiones de **extensión de archivo** un par de horas para reflejar los nuevos recursos.

**Para ver las conclusiones de extensión de archivo:**

1. Vaya a la pantalla de instancia **Azure Purview** [de Azure Portal](https://aka.ms/purviewportal) y seleccione su cuenta de Purview.

1. En la página **Información general**, en la sección **Inicio**, seleccione el icono **Launch Purview account** (Iniciar cuenta de Purview).

1. En Purview, seleccione el elemento de menú **Conclusiones** :::image type="icon" source="media/insights/ico-insights.png" border="false"::: de la izquierda para acceder al área **Conclusiones**.
    
1. En **Conclusiones**, seleccione la pestaña **Extensiones de archivo**.

    El informe muestra un resumen del número de extensiones de archivo únicas encontradas, así como un gráfico de las diez extensiones principales encontradas, en el período de tiempo seleccionado (valor predeterminado: 30 días).

    :::image type="content" source="media/file-extension-insights/file-extension-overview-small.png" alt-text="Informe de extensión de archivo: información general" lightbox="media/file-extension-insights/file-extension-overview.png":::

    Para obtener más información, ejecute cualquiera de las opciones siguientes:

    - Elija el selector **Tiempo** situado en la parte superior del informe para cambiar el intervalo de tiempo para el que se encontraron las extensiones de archivo.
    
    - Seleccione **Ver más** debajo del gráfico para ver una lista completa de las extensiones de archivo encontradas. Para más información, vea [Exploración en profundidad de conclusiones de extensión de archivo](#file-extension-insights-drilldown). 

### <a name="file-extension-insights-drilldown"></a>Exploración en profundidad de conclusiones de extensión de archivo

Una vez que haya visto la información de alto nivel acerca de los tipos de archivo que se encuentran en el patrimonio de datos, explore en profundidad para obtener más detalles sobre dónde se encuentran y si se pueden examinar datos confidenciales en ellos.

Por ejemplo:

:::image type="content" source="media/file-extension-insights/file-extension-drilldown-small.png" alt-text="Informe de extensión de archivo: explorar en profundidad" lightbox="media/file-extension-insights/file-extension-drilldown.png":::

En la cuadrícula se muestran los detalles de cada extensión de archivo encontrada, incluidos:

- **Recuento de archivos**. El número de archivos con la extensión especificada.
- **Examen de contenido**. Indica si se admite o no la extensión de archivo para buscar datos confidenciales.
- **Suscripciones**. Número de suscripciones en las que se encontró la extensión especificada.
- **Orígenes**. El número de orígenes encontrados con la extensión de archivo especificada.



Use los filtros anteriores de la cuadrícula para filtrar los datos mostrados:

|Opción  |Descripción  |
|---------|---------|
|**Filtrar por palabra clave**     |    Escriba el texto en el cuadro **Filtrar por palabra clave**  para filtrar los tipos de archivo por nombre. Por ejemplo, para ver solo los archivos PDF, escriba `PDF`.     |
|**Time**        | Seleccione esta opción para filtrar por un intervalo de tiempo específico para el momento en que se crearon los datos. <br>**Valor predeterminado:** 30 días  |
|**Extensión de archivo**     |Seleccione esta opción para filtrar la cuadrícula por uno o varios tipos de archivo.        |
|**Sources**    |Seleccione esta opción para filtrar la cuadrícula por los orígenes de datos específicos. |
|**Examen de contenido**     |Seleccione esta opción para elegir **Se admite** o **No admitido**, para mostrar solo los tipos de archivo que se pueden examinar para ver si hay datos confidenciales o datos que no se pueden examinar, como archivos **.cert** o **.jpg**. |
| | |

Encima de los filtros, seleccione **Editar columnas** :::image type="icon" source="media/insights/ico-columns.png" border="false"::: para mostrar más o menos columnas en la cuadrícula, o para cambiar el orden. 

Para ordenar la cuadrícula, seleccione un encabezado de columna para ordenar por esa columna.
## <a name="next-steps"></a>Pasos siguientes

Obtenga más información sobre los informes de conclusiones de Azure Purview
> [!div class="nextstepaction"]
> [Conclusiones de glosarios](glossary-insights.md)

> [!div class="nextstepaction"]
> [Conclusiones de exámenes](scan-insights.md)

> [!div class="nextstepaction"]
> [Conclusiones de clasificaciones](./classification-insights.md)

> [!div class="nextstepaction"]
> [Conclusiones de etiquetas de confidencialidad](sensitivity-insights.md)
