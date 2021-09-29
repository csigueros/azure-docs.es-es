---
title: Actividad de Power Query en Azure Data Factory
description: Obtenga información sobre cómo usar la actividad de Power Query para las características de limpieza y transformación de datos en una canalización de Data Factory.
author: kromerm
ms.author: makromer
ms.service: data-factory
ms.subservice: data-flows
ms.topic: conceptual
ms.date: 01/18/2021
ms.openlocfilehash: 07e663b7b631cbc083916e48924d740255e46e37
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/24/2021
ms.locfileid: "128638774"
---
# <a name="power-query-activity-in-azure-data-factory"></a>Actividad de Power Query en Azure Data Factory

La actividad de Power Query permite generar y ejecutar mashups de Power Query para ejecutar la limpieza y transformación de datos a escala en una canalización de Data Factory. Puede crear un nuevo mashup de Power Query desde la opción de menú Nuevos recursos o mediante la adición de una actividad de Power a la canalización.

:::image type="content" source="media/data-flow/power-query-wrangling.png" alt-text="Captura de pantalla que muestra Power Query en el panel de recursos de fábrica.":::

Anteriormente, la limpieza y transformación de datos en Azure Data Factory se realizaba desde la opción de menú Flujo de datos. Esto se ha cambiado y se realiza desde una nueva actividad de Power Query. Puede trabajar directamente en el editor de mashups de Power Query para realizar una exploración interactiva de los datos y luego guardar el trabajo. Una vez completado, puede tomar la actividad de Power Query y agregarla a una canalización. Azure Data Factory la escalará horizontalmente de forma automática y pondrá en funcionamiento la limpieza y transformación de datos mediante el entorno Spark de flujo de datos de Azure Data Factory.

> [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE4MFYn]

## <a name="translation-to-data-flow-script"></a>Traducción a un script de flujo de datos

Para lograr la escala con la actividad de Power Query, Azure Data Factory traduce el script ```M``` en un script de flujo de datos para que pueda ejecutar Power Query a escala mediante el entorno Spark de flujo de datos de Azure Data Factory. Genere el flujo de datos de limpieza y transformación con la preparación de datos sin código. Para ver la lista de funciones disponibles, consulte las [funciones de transformación](wrangling-functions.md).

## <a name="next-steps"></a>Pasos siguientes

Más información sobre los conceptos de limpieza y transformación de datos mediante [Power Query en Azure Data Factory](wrangling-tutorial.md)
