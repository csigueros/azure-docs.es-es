---
title: Introducción al flujo de datos de limpieza y transformación en Azure Data Factory
description: Tutorial sobre cómo preparar los datos en Azure Data Factory mediante el flujo de datos de limpieza y transformación
author: kromerm
ms.author: makromer
ms.service: data-factory
ms.subservice: data-flows
ms.topic: conceptual
ms.date: 06/08/2021
ms.openlocfilehash: 43ff1591730f240e1ae3ebcfc4066fdc34835372
ms.sourcegitcommit: 87de14fe9fdee75ea64f30ebb516cf7edad0cf87
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/01/2021
ms.locfileid: "129355563"
---
# <a name="prepare-data-with-data-wrangling"></a>Preparación de los datos con una limpieza y transformación de datos

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

La limpieza y transformación de datos en una factoría de datos permite crear mashups de Power Query interactivas de forma nativa en ADF y, después, ejecutarlas a escala dentro de una canalización de ADF.

## <a name="create-a-power-query-activity"></a>Creación de una actividad de Power Query

Hay dos maneras de crear una Power Query en Azure Data Factory. Una forma consiste en hacer clic en el icono del signo más y seleccionar **Power Query** en el panel de recursos de Factory.

:::image type="content" source="media/data-flow/power-query-wrangling.png" alt-text="Captura de pantalla que muestra Power Query en el panel de recursos de fábrica.":::

El otro método está en el panel de actividades del lienzo de la canalización. Abra el acordeón **Power Query** y arrastre la actividad **Power Query** al lienzo.

:::image type="content" source="media/data-flow/power-query-activity.png" alt-text="Captura de pantalla que resalta la opción de limpieza y transformación de datos.":::

## <a name="author-a-power-query-data-wrangling-activity"></a>Creación de una actividad de limpieza y transformación de datos de Power Query

> [!VIDEO https://www.microsoft.com/videoplayer/embed/RWJd3T]
> 
Agregue un **Conjunto de datos de origen** para el mashup de Power Query. Puede elegir un conjunto de datos existente o crear uno. Después de guardar el mashup, puede agregar la actividad de limpieza y transformación de datos de Power Query a su canalización y seleccionar un conjunto de datos de receptor para indicarle a ADF dónde colocar los datos. Si bien puede elegir uno o más conjuntos de datos de origen, en este momento solo se permite un receptor. Elegir un conjunto de datos de receptor es opcional, pero se requiere al menos un conjunto de datos de origen.

:::image type="content" source="media/wrangling-data-flow/tutorial4.png" alt-text="Limpieza y transformación":::

Haga clic en **Crear** para abrir el editor de mashup Power Query Online.

En primer lugar, elegirá un origen de conjunto de datos para el editor de mashup.

:::image type="content" source="media/wrangling-data-flow/power-query-new-source.png" alt-text="Origen de Power Query.":::

Una vez que haya completado la compilación de Power Query, se puede guardar y agregar el mashup como una actividad a la canalización. Es cuando establecerá las propiedades del conjunto de datos de receptor.

:::image type="content" source="media/wrangling-data-flow/power-query-new-sink.png" alt-text="Receptor de Power Query.":::

Cree la Power Query de limpieza y transformación con la preparación de datos sin código. Para ver la lista de funciones disponibles, consulte las [funciones de transformación](wrangling-functions.md). ADF traduce el script M en un script de flujo de datos para que pueda ejecutar Power Query a escala mediante el entorno Spark de flujo de datos de Azure Data Factory.

:::image type="content" source="media/wrangling-data-flow/tutorial6.png" alt-text="Captura de pantalla que muestra el proceso para creación de Power Query de limpieza y transformación de datos.":::

## <a name="running-and-monitoring-a-power-query-data-wrangling-activity"></a>Ejecución y supervisión de una actividad de limpieza y transformación de datos de Power Query

Para una ejecución de depuración de canalización de una actividad de Power Query, haga clic en **Depurar** en el lienzo de la canalización. Una vez publicada la canalización, **Trigger now** (Desencadenar ahora) ejecuta a petición la última canalización publicada. Las canalizaciones de Power Query se pueden programar con todos los desencadenadores de Azure Data Factory existentes.

:::image type="content" source="media/data-flow/power-query-activity.png" alt-text="Captura de pantalla que muestra cómo agregar una actividad de limpieza y transformación de datos de Power Query.":::

Vaya a la pestaña **Supervisión** para visualizar la salida de una ejecución de actividad de Power Query desencadenada.

:::image type="content" source="media/wrangling-data-flow/tutorial2.png" alt-text="Captura de pantalla que muestra la salida de una ejecución de actividad de limpieza y transformación de datos de Power Query desencadenada.":::

## <a name="next-steps"></a>Pasos siguientes

Obtenga información sobre cómo [crear un flujo de datos de asignación](tutorial-data-flow.md).
