---
title: Transformación Anular dinamización en el flujo de datos de asignación
titleSuffix: Azure Data Factory & Azure Synapse
description: Obtenga información sobre la transformación Anular dinamización del flujo de datos de asignación en Azure Data Factory y Synapse Analytics.
author: kromerm
ms.author: makromer
ms.service: data-factory
ms.subservice: data-flows
ms.topic: conceptual
ms.custom: synapse
ms.date: 09/09/2021
ms.openlocfilehash: 28c89622702435f178e241125545f8f0ac86eb18
ms.sourcegitcommit: 48500a6a9002b48ed94c65e9598f049f3d6db60c
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/26/2021
ms.locfileid: "129059594"
---
# <a name="unpivot-transformation-in-mapping-data-flow"></a>Transformación Anular dinamización en el flujo de datos de asignación

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

[!INCLUDE[data-flow-preamble](includes/data-flow-preamble.md)]

Use Anular dinamización en un flujo de datos de asignación como una forma de convertir un conjunto de datos sin normalizar en una versión más normalizada, ampliando los valores de varias columnas en un único registro a varios registros con los mismos valores en una sola columna.

:::image type="content" source="media/data-flow/unpivot1.png" alt-text="Captura de pantalla que muestra la opción Anulación de dinamización seleccionada en el menú.":::

> [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE4B1RR]

## <a name="ungroup-by"></a>Ungroup By

:::image type="content" source="media/data-flow/unpivot5.png" alt-text="Captura de pantalla que muestra la configuración de anulación de dinamización con la pestaña Desagrupar por seleccionada.":::

En primer lugar, establezca las columnas por las que desea desagrupar la agregación de anulación de dinamización. Establezca una o varias columnas para desagrupar con el + situado junto a la lista de columnas.

## <a name="unpivot-key"></a>Clave de anulación de dinamización

:::image type="content" source="media/data-flow/unpivot6.png" alt-text="Captura de pantalla que muestra la configuración de anulación de dinamización con la pestaña Clave de anulación de dinamización seleccionada.":::

La clave de anulación de dinamización es la columna que el servicio utilizará para dinamizar de columna a fila. De forma predeterminada, cada valor único del conjunto de datos de este campo se dinamizará en una fila. Sin embargo, también puede escribir los valores del conjunto de datos que desea dinamizar en valores de fila.

## <a name="unpivoted-columns"></a>Columnas con dinamización anulada

:::image type="content" source="media/data-flow//unpivot7.png" alt-text="Captura de pantalla que muestra la configuración de anulación de dinamización con la pestaña Vista previa de los datos seleccionada.":::

Por último, elija el nombre de columna para almacenar los valores de las columnas no dinamizadas que se transforman en filas.

(Opcional) Puede eliminar las filas con valores NULL.

Por ejemplo, SumCost es el nombre de columna que se eligió en el ejemplo compartido anterior.

:::image type="content" source="media/data-flow/unpivot3.png" alt-text="Imagen que muestra las columnas PO, Vendor y Fruit antes y después de una transformación de tipo unipivot que usa la columna Fruit como clave unipivot.":::

Al establecer la organización de la columna en "Normal", se agruparán todas las nuevas columnas no dinamizadas a partir de un único valor. Al establecer la organización de las columnas en "Lateral", se agruparán las nuevas columnas no dinamizadas generadas a partir de una columna existente.

:::image type="content" source="media/data-flow//unpivot7.png" alt-text="Captura de pantalla que muestra el resultado de la transformación.":::

El conjunto de resultados final de los datos con dinamización anulada muestra los totales de columna ahora sin dinamizar en valores de fila independientes.

## <a name="next-steps"></a>Pasos siguientes

Use la [transformación Dinamizar](data-flow-pivot.md) para dinamizar las filas en columnas.
