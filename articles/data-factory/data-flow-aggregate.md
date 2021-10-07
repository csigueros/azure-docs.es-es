---
title: Transformación Agregar en Asignación de Data Flow
titleSuffix: Azure Data Factory & Azure Synapse
description: Obtenga información sobre cómo agregar datos a gran escala en Azure Data Factory y Synapse Analyatics con la transformación Agregar del flujo de datos de asignación.
author: kromerm
ms.author: makromer
ms.reviewer: daperlov
ms.service: data-factory
ms.subservice: data-flows
ms.topic: conceptual
ms.custom: synapse
ms.date: 09/09/2021
ms.openlocfilehash: 7edf2ededec8ff15f50ea25f274d0163bc8b0980
ms.sourcegitcommit: 48500a6a9002b48ed94c65e9598f049f3d6db60c
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/26/2021
ms.locfileid: "129060373"
---
# <a name="aggregate-transformation-in-mapping-data-flow"></a>Transformación Agregar en Asignación de Data Flow

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

[!INCLUDE[data-flow-preamble](includes/data-flow-preamble.md)]

En la transformación Agregar se definen las agregaciones de columnas de flujos de datos. Mediante el Generador de expresiones, puede definir diferentes tipos de agregaciones, tales como SUM, MIN, MAX y COUNT agrupados por columnas calculadas o existentes.

## <a name="group-by"></a>Agrupar por

Seleccione una columna existente o cree una nueva columna calculada para usarla como una cláusula Agrupar por para la agregación. Para usar una columna existente, selecciónela en la lista desplegable. Para crear una nueva columna calculada, mantenga el mouse sobre la cláusula y haga clic en **Columna calculada**. Se abrirá el [generador de expresiones de flujo de datos](concepts-data-flow-expression-builder.md). Una vez creada la columna calculada, escriba el nombre de la columna de salida en el campo **Name as** (Nombre como). Si desea agregar una cláusula Agrupar por adicional, mantenga el mouse sobre una cláusula existente y haga clic en el icono "más".

:::image type="content" source="media/data-flow/agg.png" alt-text="Configuración de Agrupar por de una transformación Agregar":::

Una cláusula Agrupar por es opcional en una transformación Agregar.

## <a name="aggregate-columns"></a>Columnas agregadas

Vaya a la pestaña **Agregados** para generar las expresiones de agregación. Puede sobrescribir una columna existente con una agregación, o bien crear un campo nuevo con un nombre nuevo. La expresión de agregación se escribirá en el cuadro de la derecha, junto al selector de nombre de columna. Para editar la expresión, haga clic en el cuadro de texto y abra el generador de expresiones. Para agregar más columnas agregadas, haga clic en **Agregar** encima de la lista de columnas o en el icono de signo más junto a una columna agregada existente. Elija **Agregar columna** o **Add column pattern** (Agregar patrón de columna). Cada expresión de agregación debe contener al menos una función de agregado.

:::image type="content" source="media/data-flow/aggregate-columns.png" alt-text="Configuración de agregación":::

> [!NOTE]
> En el modo Depurar, el Generador de expresiones no puede generar vistas previas de los datos con las funciones de agregado. Para ver las vistas previas de los datos para las transformaciones Agregar, cierre el Generador de expresiones y vea los datos a través de la pestaña "Vista previa de los datos".

### <a name="column-patterns"></a>Patrones de columnas

Utilice [patrones de columna](concepts-data-flow-column-pattern.md) para aplicar la misma agregación a un conjunto de columnas. Esto resulta útil si desea que persistan muchas columnas del esquema de entrada, ya que se anulan de manera predeterminada. Use una heurística como `first()` para que las columnas de entrada persistan en la agregación.

## <a name="reconnect-rows-and-columns"></a>Reconexión de filas y columnas

Las transformaciones Agregar son similares a las consultas de selección de agregado de SQL. Las columnas que no estén incluidas en la cláusula Agrupar por o en las funciones de agregado no fluirán a través de la salida de la transformación Agregar. Si desea incluir otras columnas en la salida agregada, siga uno de estos métodos:

* Use una función de agregado como `last()` o `first()` para incluir esa columna adicional.
* Vuelva a unir las columnas al flujo de salida con el [patrón self join](https://mssqldude.wordpress.com/2018/12/20/adf-data-flows-self-join/).

## <a name="removing-duplicate-rows"></a>Eliminación de filas duplicadas

Un uso común de la transformación de agregados es quitar o identificar entradas duplicadas en los datos de origen. Este proceso se conoce como desduplicación. En función de un conjunto de claves Group by, utilice una heurística de su elección para determinar qué fila duplicada desea conservar. Las heurísticas comunes son `first()`, `last()`, `max()` y `min()`. Use [patrones de columna](concepts-data-flow-column-pattern.md) para aplicar la regla a todas las columnas excepto para las columnas Group by.

:::image type="content" source="media/data-flow/agg-dedupe.png" alt-text="Desduplicación":::

En el ejemplo anterior, las columnas `ProductID` y `Name` se usan para la agrupación. Si dos filas tienen los mismos valores para esas dos columnas, se consideran duplicadas. En esta transformación de agregados, se conservarán los valores de la primera fila coincidente y se quitarán todos los demás. Con la sintaxis de patrón de columnas, todas las columnas cuyos nombres no son `ProductID` y `Name` se asignan a su nombre de columna existente y se les proporciona el valor de las primeras filas coincidentes. El esquema de salida es el mismo que el esquema de entrada.

En escenarios de validación de datos, la función `count()` se puede usar para contar el número de duplicados que hay.

## <a name="data-flow-script"></a>Script de flujo de datos

### <a name="syntax"></a>Sintaxis

```
<incomingStream>
    aggregate(
           groupBy(
                <groupByColumnName> = <groupByExpression1>,
                <groupByExpression2>
               ),
           <aggregateColumn1> = <aggregateExpression1>,
           <aggregateColumn2> = <aggregateExpression2>,
           each(
                match(matchExpression),
                <metadataColumn1> = <metadataExpression1>,
                <metadataColumn2> = <metadataExpression2>
               )
          ) ~> <aggregateTransformationName>
```

### <a name="example"></a>Ejemplo

En el ejemplo siguiente se toma un flujo entrante `MoviesYear` y las filas se agrupan por columna `year`. La transformación crea una columna de agregado `avgrating` que se evalúa como el promedio de la columna `Rating`. Esta transformación Agregar se denomina `AvgComedyRatingsByYear`.

En la interfaz de usuario, esta transformación es similar a la siguiente imagen:

:::image type="content" source="media/data-flow/agg-script1.png" alt-text="Ejemplo de Agrupar por":::

:::image type="content" source="media/data-flow/agg-script2.png" alt-text="Ejemplo de Agregar":::

En el siguiente fragmento de código se muestra el script del flujo de datos para esta transformación.

```
MoviesYear aggregate(
                groupBy(year),
                avgrating = avg(toInteger(Rating))
            ) ~> AvgComedyRatingByYear
```

:::image type="content" source="media/data-flow/aggdfs1.png" alt-text="Script del flujo de datos de agregado":::

```MoviesYear```: columna derivada que define las columnas year y title ```AvgComedyRatingByYear```: transformación de agregados para la clasificación media de comedias agrupadas por año ```avgrating```: nombre de la nueva columna que se va a crear para contener el valor agregado

```
MoviesYear aggregate(groupBy(year),
    avgrating = avg(toInteger(Rating))) ~> AvgComedyRatingByYear
```

## <a name="next-steps"></a>Pasos siguientes

* Definir un agregación basada en ventanas mediante la [transformación Ventana](data-flow-window.md).
