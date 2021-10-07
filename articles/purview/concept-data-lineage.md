---
title: Linaje de datos en Azure Purview
description: Aquí se describen los conceptos del linaje de datos.
author: chanuengg
ms.author: csugunan
ms.service: purview
ms.topic: conceptual
ms.date: 09/27/2021
ms.openlocfilehash: 37cffe89fc0e0cb10aaadbb8d63f9aa12d0589eb
ms.sourcegitcommit: e8c34354266d00e85364cf07e1e39600f7eb71cd
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/29/2021
ms.locfileid: "129207457"
---
# <a name="data-lineage-in-azure-purview-data-catalog-client"></a>Linaje de datos en el cliente de Azure Purview Data Catalog

En este artículo se proporciona información general sobre el linaje de datos en Azure Purview Data Catalog. También se detallan los sistemas de datos que se pueden integrar con el catálogo para capturar así el linaje de los datos. Purview puede capturar el linaje de los datos en diferentes partes del patrimonio de datos de su organización y en diferentes niveles de preparación, que incluyen lo siguiente:

- Datos completamente sin procesar de varias plataformas almacenados provisionalmente.
- Datos transformados y preparados.
- Datos que usan las plataformas de visualización.

## <a name="use-cases"></a>Casos de uso

El linaje de datos es, en términos generales, el ciclo de vida que abarca el origen de los datos y que se mueve con el tiempo a través del patrimonio de datos. Se usa en diferentes tipos de escenarios de búsqueda retroactiva, como la solución de problemas, el seguimiento de la causa principal de las canalizaciones de datos y la depuración. El linaje también se usa en el análisis de calidad de los datos, el cumplimiento y los escenarios de tipo "what if", a los que a menudo se hace referencia como análisis de impacto. El linaje se representa visualmente para mostrar los datos que se transfieren del origen al destino; asimismo, también se incluye el modo en que se transforman los datos. Dada la complejidad de la mayoría de los entornos de datos empresariales, estas vistas pueden ser difíciles de entender si no se consolidan ni enmascaran los puntos de datos periféricos.

## <a name="lineage-experience-in-azure-purview-data-catalog"></a>Experiencia del linaje de datos en Azure Purview Data Catalog

Purview Data Catalog se conectará con otros sistemas de procesamiento, almacenamiento y análisis de datos para extraer información de linaje. La información se combina para representar una experiencia de linaje genérica y específica del escenario en el catálogo.

:::image type="content" source="media/concept-lineage/lineage-end-end.png" alt-text="linaje final que muestra todos los pasos de los datos copiados desde almacén de blobs hasta el panel de Power BI":::

El patrimonio de datos puede incluir sistemas que se encargan de la extracción, transformación (sistemas ETL/ELT), análisis y visualización de datos. Cada uno de estos sistemas captura metadatos estáticos y operativos enriquecidos que describen el estado y la calidad de los datos del límite del sistema. El objetivo de linaje en un catálogo de datos es extraer los metadatos de transferencia, transformación y operaciones de cada sistema de datos con el menor detalle posible.

En el ejemplo siguiente se muestra un caso de uso típico de los datos que se transfieren a través de varios sistemas, donde Data Catalog se conecta a cada uno de los sistemas de linaje.

- Data Factory copia los datos de la zona local o sin formato en una zona de aterrizaje en la nube. 
- En los sistemas de procesamiento de datos como Synapse, Databricks procesa y transforma los datos de la zona de aterrizaje a la zona mantenida mediante cuadernos.
- Los procesamientos adicionales de datos en modelos analíticos le permiten obtener un rendimiento óptimo de las consultas y agregaciones. 
- Los sistemas de visualización de datos usarán los conjuntos de datos y el proceso a través de su metamodelo para crear un panel de BI, experimentos de Machine Learning, etc.

## <a name="lineage-granularity"></a>Granularidad del linaje

En la sección siguiente se detalla la granularidad con la que Purview recopila la información de linaje. Esta granularidad puede variar en función de los sistemas de datos compatibles con Purview.

### <a name="entity-level-lineage-sources--process--targets"></a>Linaje de nivel de entidad: Orígenes > Proceso > Destinos 

- El linaje se representa como un gráfico que normalmente contiene entidades de origen y de destino en sistemas de almacenamiento de datos, que a su vez están conectados mediante un proceso que invoca un sistema de proceso. 
- Los sistemas de datos se conectan al catálogo de datos para generar y proporcionar un objeto único que hace referencia al objeto físico del sistema de datos subyacente; por ejemplo: procedimiento almacenado de SQL, cuadernos, etc.
- El linaje de alta fidelidad con metadatos adicionales, como la propiedad, se captura para mostrar el linaje en un formato legible del origen y las entidades de destino. Por ejemplo: linaje en el nivel de tabla de subárbol, en lugar de particiones o el nivel de archivo.

### <a name="column-or-attribute-level-lineage"></a>Linaje de nivel de columna o atributo

Identifique los atributos de una entidad de origen que se usa para crear o derivar atributos en la entidad de destino. El nombre del atributo de origen se puede guardar o cambiar de nombre en el destino. Los sistemas como ADF pueden realizar una copia de tipo "uno a uno" desde el entorno local a la nube. Por ejemplo: `Table1/ColumnA -> Table2/ColumnA`.

### <a name="process-execution-status"></a>Estado de la ejecución del proceso

Para admitir el análisis de la causa principal y los escenarios de calidad de los datos, es necesario capturar el estado de ejecución de los trabajos en los sistemas de procesamiento de datos. Tenga en cuenta que este requisito no tiene nada que ver con la sustitución de las capacidades de supervisión de otros sistemas de procesamiento de datos; asimismo, el objetivo no es reemplazarlos. 

## <a name="summary"></a>Resumen

El linaje es una característica fundamental de Purview Data Catalog, ya que le permite admitir escenarios de calidad, confianza y auditoría. El objetivo de un catálogo de datos es crear un marco sólido en el que todos los sistemas de datos del entorno puedan conectarse de forma natural y generar informes de linaje. Una vez que los metadatos están disponibles, el catálogo de datos puede reunir los metadatos que hayan proporcionado los sistemas de datos para mejorar los casos de uso del gobierno de datos.

## <a name="next-steps"></a>Pasos siguientes

* [Inicio rápido: creación de una cuenta de almacenamiento de Azure Purview en Azure Portal](create-catalog-portal.md).
* [Inicio rápido: creación de una cuenta de Azure Purview mediante Azure PowerShell o la CLI de Azure](create-catalog-powershell.md).
* [Uso de Purview Studio](use-purview-studio.md)
