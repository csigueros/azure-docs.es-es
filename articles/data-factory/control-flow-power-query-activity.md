---
title: Actividad de Power Query en Azure Data Factory
description: Obtenga información sobre cómo usar la actividad de Power Query para las características de limpieza y transformación de datos en una canalización de Data Factory.
author: kromerm
ms.author: makromer
ms.service: data-factory
ms.subservice: data-flows
ms.topic: conceptual
ms.date: 08/12/2021
ms.openlocfilehash: be5c2cbe0d4f364abb68ad8b01f00ca2c8396806
ms.sourcegitcommit: 87de14fe9fdee75ea64f30ebb516cf7edad0cf87
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/01/2021
ms.locfileid: "129358549"
---
# <a name="power-query-activity-in-azure-data-factory"></a>Actividad de Power Query en Azure Data Factory

La actividad de Power Query permite generar y ejecutar mashups de Power Query para ejecutar la limpieza y transformación de datos a escala en una canalización de Data Factory. Puede crear un nuevo mashup de Power Query desde la opción de menú Nuevos recursos o mediante la adición de una actividad de Power a la canalización.

![Captura de pantalla que muestra Power Query en el panel de recursos de fábrica.](media/data-flow/power-query-activity-1.png)

Puede trabajar directamente en el editor de mashups de Power Query para realizar una exploración interactiva de los datos y luego guardar el trabajo. Una vez completado, puede tomar la actividad de Power Query y agregarla a una canalización. Azure Data Factory la escalará horizontalmente de forma automática y pondrá en funcionamiento la limpieza y transformación de datos mediante el entorno Spark de flujo de datos de Azure Data Factory.

> [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE4MFYn]

## <a name="translation-to-data-flow-script"></a>Traducción a un script de flujo de datos

Para lograr la escala con la actividad de Power Query, Azure Data Factory traduce el script ```M``` en un script de flujo de datos para que pueda ejecutar Power Query a escala mediante el entorno Spark de flujo de datos de Azure Data Factory. Genere el flujo de datos de limpieza y transformación con la preparación de datos sin código. Para ver la lista de funciones disponibles, consulte las [funciones de transformación](wrangling-functions.md).

## <a name="settings"></a>Configuración

* Power Query: elija una instancia de Power Query existente para ejecutar o crear una nueva.
* Ejecutar en Azure IR: elija una instancia existente de Azure Integration Runtime existentes para definir el entorno de proceso para Power Query o cree una nueva.
* Tipo de proceso: si elige el entorno de ejecución de integración de resolución automática predeterminado, puede seleccionar el tipo de proceso que se va a aplicar al proceso del clúster de Spark para la ejecución de Power Query.
* Recuento de núcleos: si elige el entorno de ejecución de integración de resolución automática predeterminado, puede seleccionar el número de núcleos que se aplicarán al proceso del clúster de Spark para la ejecución de Power Query.

## <a name="sink"></a>Receptor

Elija el conjunto de datos que desea usar para el aterrizaje de los datos transformados una vez que el script de M de Power Query se haya ejecutado en Spark. Para más información sobre la configuración de receptores, visite la documentación de los [receptores de flujo de datos](data-flow-sink.md).

## <a name="mapping"></a>Asignación

En la pestaña Asignación, puede configurar la asignación de columnas desde la salida de la actividad de Power Query al esquema de destino del receptor elegido. Obtenga más información sobre la asignación de columnas en la [documentación de asignación del receptor del flujo de datos](data-flow-sink.md#field-mapping).

## <a name="next-steps"></a>Pasos siguientes

Más información sobre los conceptos de limpieza y transformación de datos mediante [Power Query en Azure Data Factory](wrangling-tutorial.md)
