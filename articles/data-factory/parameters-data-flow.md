---
title: Parametrización de flujos de datos de asignación
titleSuffix: Azure Data Factory & Azure Synapse
description: Aprenda a parametrizar un flujo de datos de asignación desde canalizaciones de Azure Data Factory y Azure Synapse Analytics
author: kromerm
ms.author: makromer
ms.reviewer: daperlov
ms.service: data-factory
ms.subservice: data-flows
ms.custom: synapse
ms.topic: conceptual
ms.date: 09/09/2021
ms.openlocfilehash: d01e1ca8d3eb0ca2e345d42118ad5f1463c3139f
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/13/2021
ms.locfileid: "124750471"
---
# <a name="parameterizing-mapping-data-flows"></a>Parametrización de flujos de datos de asignación

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)] 

Los flujos de datos de asignación de las canalizaciones de Azure Data Factory y Synapse admiten el uso de parámetros. Defina parámetros en la definición del flujo de datos y úselos después en las expresiones. La canalización que realiza la llamada puede establecer los valores de parámetro mediante la actividad de ejecución de Data Flow. Dispone de tres opciones para establecer los valores en las expresiones de la actividad del flujo de datos:

* Usar el lenguaje de expresiones del flujo de control de canalizaciones para establecer un valor dinámico
* Usar el lenguaje de expresiones del flujo de datos para establecer un valor dinámico
* Usar cualquiera de estos dos lenguajes de expresiones para establecer un valor literal estático

Use esta funcionalidad para hacer que los flujos de datos sean flexibles y reutilizables y estén destinados para un uso general. Puede parametrizar las expresiones y los valores del flujo de datos con estos parámetros.

## <a name="create-parameters-in-a-mapping-data-flow"></a>Creación de parámetros en un flujo de datos de asignación

Para agregar parámetros a un flujo de datos, haga clic en la parte en blanco del lienzo del flujo de datos para ver las propiedades generales. En el panel de configuración, verá una pestaña denominada **Parámetro**. Seleccione **Nuevo** para generar un nuevo parámetro. Para cada parámetro, debe asignar un nombre, seleccionar un tipo y opcionalmente establecer un valor predeterminado.

:::image type="content" source="media/data-flow/create-params.png" alt-text="Creación de parámetros de Data Flow":::

## <a name="use-parameters-in-a-mapping-data-flow"></a>Uso de parámetros en un flujo de datos de asignación 

Se puede hacer referencia a los parámetros en cualquier expresión de flujo de datos. Los parámetros comienzan por $ y son inmutables. Encontrará la lista de parámetros disponibles en el Generador de expresiones en la pestaña **Parámetros**.

:::image type="content" source="media/data-flow/parameter-expression.png" alt-text="Captura de pantalla que muestra los parámetros disponibles en la pestaña Parámetros.":::

Para agregar parámetros adicionales rápidamente, puede seleccionar **Nuevo parámetro** y especificar el nombre y el tipo.

:::image type="content" source="media/data-flow/new-parameter-expression.png" alt-text="Captura de pantalla muestra los parámetros en la pestaña Parámetros con los nuevos parámetros agregados.":::

## <a name="assign-parameter-values-from-a-pipeline"></a>Asignación de valores de parámetros de una canalización

Una vez que haya creado un flujo de datos con parámetros, podrá ejecutarlo desde una canalización con la actividad de ejecución de Data Flow. Después de agregar la actividad al lienzo de la canalización, se le mostrarán los parámetros de flujo de datos disponibles en la pestaña **Parámetros** de la actividad.

Al asignar valores de parámetro, puede usar el [lenguaje de expresiones de canalización](control-flow-expression-language-functions.md) o el [lenguaje de expresiones de flujo de datos](data-flow-expression-functions.md) basado en tipos de Spark. Cada flujo de datos de asignación puede tener cualquier combinación de parámetros de expresión de canalización y flujo de datos.

:::image type="content" source="media/data-flow/parameter-assign.png" alt-text="Captura de pantalla que muestra la pestaña Parámetros con la expresión Flujo de datos seleccionada para el valor de myparam.":::

### <a name="pipeline-expression-parameters"></a>Parámetros de expresión de canalización

Los parámetros de expresión de canalización permiten hacer referencia a variables del sistema, funciones, parámetros de canalización y variables similares a otras actividades de canalización. Al hacer clic en **Pipeline expression** (Expresión de canalización), se abre un panel de navegación lateral que le permite escribir una expresión mediante el generador de expresiones.

:::image type="content" source="media/data-flow/parameter-pipeline.png" alt-text="Captura de pantalla que muestra el panel del generador de expresiones.":::

Al hacer referencia a los parámetros de canalización, estos se evalúan y, a continuación, se usa su valor en el lenguaje de expresiones de flujo de datos. No es necesario que el tipo de expresión de canalización coincida con el tipo de parámetro de flujo de datos. 

#### <a name="string-literals-vs-expressions"></a>Literales de cadena y expresiones

Al asignar un parámetro de expresión de canalización de tipo string, se agregarán comillas de forma predeterminada y el valor se evaluará como literal. Para leer el valor del parámetro como una expresión de flujo de datos, active la casilla de expresión situada junto al parámetro.

:::image type="content" source="media/data-flow/string-parameter.png" alt-text="Captura de pantalla que muestra la opción Expresión en el panel Parámetros de flujo de datos seleccionada para un parámetro.":::

Si el parámetro de flujo de datos `stringParam` hace referencia a un parámetro de canalización con el valor `upper(column1)`. 

- Si se comprueba la expresión, `$stringParam` se evalúa como el valor de column1 en mayúsculas.
- Si no se comprueba la expresión (comportamiento predeterminado), `$stringParam` se evalúa como `'upper(column1)'`

#### <a name="passing-in-timestamps"></a>Pasar marcas de tiempo

En el lenguaje de expresiones de canalización, las variables del sistema como `pipeline().TriggerTime` y las funciones como `utcNow()` devuelven marcas de tiempo como cadenas con el formato "aaaa-MM-dd\'T\'HH:mm:ss.SSSSSSZ". Para convertirlas en parámetros de flujo de datos de tipo timestamp, use la interpolación de cadenas para incluir la marca de tiempo deseada en una función `toTimestamp()`. Por ejemplo, para convertir la hora del desencadenador de la canalización en un parámetro de flujo de datos, puede utilizar `toTimestamp(left('@{pipeline().TriggerTime}', 23), 'yyyy-MM-dd\'T\'HH:mm:ss.SSS')`. 

:::image type="content" source="media/data-flow/parameter-timestamp.png" alt-text="Captura de pantalla que muestra la pestaña Parámetros, donde puede especificar una hora de desencadenamiento.":::

> [!NOTE]
> Los flujos de datos solo admiten un máximo 3 dígitos de milisegundo. La función `left()` se usa para recortar los dígitos adicionales.

#### <a name="pipeline-parameter-example"></a>Ejemplo de parámetro de canalización

Supongamos que tiene un parámetro de entero `intParam` que hace referencia a un parámetro de canalización de tipo string, `@pipeline.parameters.pipelineParam`. 

:::image type="content" source="media/data-flow/parameter-pipeline-2.png" alt-text="Captura de pantalla que muestra la pestaña Parámetros con los parámetros denominados stringParam e intParam.":::

A `@pipeline.parameters.pipelineParam` se le asigna un valor de `abs(1)` en tiempo de ejecución.

:::image type="content" source="media/data-flow/parameter-pipeline-4.png" alt-text="Captura de pantalla que muestra la pestaña Parámetros con el valor de abs (1) seleccionado.":::

Cuando se hace referencia a `$intParam` en una expresión como una columna derivada, evaluará `abs(1)` y devolverá `1`. 

:::image type="content" source="media/data-flow/parameter-pipeline-3.png" alt-text="Captura de pantalla que muestra el valor de las columnas.":::

### <a name="data-flow-expression-parameters"></a>Parámetros de expresiones de flujo de datos

Al seleccionar **Data flow expression** (Expresión de flujo de datos), se abrirá el generador de expresiones de flujo de datos. Podrá hacer referencia a funciones, a otros parámetros y a cualquier columna de esquema definida en el flujo de datos. Esta expresión se evaluará tal cual cuando se haga referencia a ella.

> [!NOTE]
> Si pasa una expresión no válida o hace referencia a una columna de esquema que no existe en esa transformación, el parámetro se evaluará como null.


### <a name="passing-in-a-column-name-as-a-parameter"></a>Paso de un nombre de columna como un parámetro

Un patrón común es pasar un nombre de columna como un valor de parámetro. Si la columna está definida en el esquema de flujo de datos, puede hacer referencia a ella directamente como una expresión de cadena. Si la columna no está definida en el esquema, use la función `byName()`. Recuerde convertir la columna a su tipo adecuado con una función de conversión como `toString()`.

Por ejemplo, si desea asignar una columna de cadena basada en un parámetro `columnName`, puede agregar una transformación de columna derivada igual a `toString(byName($columnName))`.

:::image type="content" source="media/data-flow/parameterize-column-name.png" alt-text="Paso de un nombre de columna como un parámetro":::

## <a name="next-steps"></a>Pasos siguientes
* [Actividad de ejecución de Data Flow](control-flow-execute-data-flow-activity.md)
* [Expresiones del flujo de control](control-flow-expression-language-functions.md)
