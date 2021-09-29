---
title: Transformación Columna derivada en flujos de datos de asignación
titleSuffix: Azure Data Factory & Azure Synapse
description: Aprenda a transformar datos a gran escala en Azure Data Factory y Azure Synapse Analytics con la transformación Columna derivada del flujo de datos de asignación.
author: kromerm
ms.author: makromer
ms.service: data-factory
ms.subservice: data-flows
ms.topic: conceptual
ms.custom: synapse
ms.date: 09/09/2021
ms.openlocfilehash: 51656330a0d849ed61d7f1cae88f7bbfc0610c49
ms.sourcegitcommit: 48500a6a9002b48ed94c65e9598f049f3d6db60c
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/26/2021
ms.locfileid: "129058792"
---
# <a name="derived-column-transformation-in-mapping-data-flow"></a>Transformación Columna derivada en Asignación de Data Flow

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

[!INCLUDE[data-flow-preamble](includes/data-flow-preamble.md)]

Use la transformación Columna derivada para generar nuevas columnas en el flujo de datos o para modificar los campos existentes.

## <a name="create-and-update-columns"></a>Creación y actualización de columnas

Para crear una columna derivada, puede generar una nueva columna o actualizar una existente. En el cuadro de texto **Columna**, especifique la columna que está creando. Para reemplazar una columna existente en el esquema, puede usar la lista desplegable de columnas. Para generar la expresión de la columna derivada, haga clic en el cuadro de texto **Escribir expresión**. Puede empezar a escribir la expresión o abrir el generador de expresiones para crear la lógica.

:::image type="content" source="media/data-flow/create-derive-column.png" alt-text="Configuración de columna derivada":::

Para agregar más columnas derivadas, haga clic en **Agregar** encima de la lista de columnas o en el icono con el signo más situado junto a una columna derivada existente. Elija **Agregar columna** o **Add column pattern** (Agregar patrón de columna).

:::image type="content" source="media/data-flow/add-derived-column.png" alt-text="Nueva selección de columna derivada":::

### <a name="column-patterns"></a>Patrones de columnas

En aquellos casos en los que el esquema no esté definido explícitamente o en los que desee actualizar en bloque un conjunto de columnas, le vendrá bien crear patrones de columnas. Los patrones de columnas permiten establecer correspondencias utilizando reglas basadas en los metadatos de las columnas y crear columnas derivadas en cada correspondencia. Para más información, vea [cómo se crean patrones de columnas](concepts-data-flow-column-pattern.md#column-patterns-in-derived-column-and-aggregate) en la transformación de columnas derivadas.

:::image type="content" source="media/data-flow/column-pattern-derive.png" alt-text="Patrones de columnas":::

## <a name="building-schemas-using-the-expression-builder"></a>Creación de esquemas mediante el generador de expresiones

Si utiliza el [generador de expresiones](concepts-data-flow-expression-builder.md) del flujo de datos de asignación, puede crear, editar y administrar las columnas derivadas en la sección **Columnas derivadas**. Aparecerán todas las columnas que se crearon o modificaron en la transformación. Para elegir de forma interactiva qué columna o patrón está editando, haga clic en el nombre de la columna. Para agregar otra columna, seleccione **Crear nueva** y elija si desea agregar una sola columna o un patrón.

:::image type="content" source="media/data-flow/derive-add-column.png" alt-text="Creación de una nueva columna":::

Si trabaja con columnas complejas, puede crear subcolumnas. Para ello, haga clic en el icono con el signo más situado junto a una columna y seleccione **Add subcolumn** (Agregar subcolumna). Para obtener más información sobre cómo controlar tipos complejos en el flujo de datos, consulte [Control de JSON en un flujo de datos de asignación](format-json.md#mapping-data-flow-properties).

:::image type="content" source="media/data-flow/derive-add-subcolumn.png" alt-text="Add subcolumn (Agregar subcolumna)":::

Para obtener más información sobre cómo controlar tipos complejos en el flujo de datos, consulte [Control de JSON en un flujo de datos de asignación](format-json.md#mapping-data-flow-properties).

:::image type="content" source="media/data-flow/derive-complex-column.png" alt-text="Agregar columna compleja":::

### <a name="locals"></a>Locals

Si varias columnas comparten la misma lógica o si desea compartimentar la lógica, puede crear un conjunto local al transformar una columna derivada. Un conjunto local es un conjunto de la lógica que no se propaga a los elementos de nivel inferior durante la siguiente transformación. Los conjuntos locales pueden crearse en el generador de expresiones; para ello, vaya a **Expression elements** (Elementos de expresión) y seleccione **Locals** (Locales). Seleccione **Create new** (Crear nuevo) para crear un nuevo conjunto local.

:::image type="content" source="media/data-flow/create-local.png" alt-text="Creación de un conjunto local":::

Los conjuntos locales pueden hacer referencia a cualquier elemento de expresión de una columna derivada, como funciones, el esquema de entrada, parámetros y otros conjuntos locales. Cuando se hace referencia a otros conjuntos locales, el orden es importante, ya que el conjunto local al que se hace referencia debe ser "anterior" al actual.

:::image type="content" source="media/data-flow/create-local-2.png" alt-text="Creación de un conjunto local 2":::

Para hacer referencia al conjunto local de una columna derivada, haga clic en él en la vista **Expression elements** (Elementos de expresión) o escriba el signo de dos puntos delante del nombre. Por ejemplo, `:local1` haría referencia a un conjunto local llamado local1. Para modificar una definición local, mantenga el mouse sobre ella en la vista de elementos de expresión y haga clic en el icono de lápiz.

:::image type="content" source="media/data-flow/using-locals.png" alt-text="Uso de conjuntos locales":::

## <a name="data-flow-script"></a>Script de flujo de datos

### <a name="syntax"></a>Sintaxis

```
<incomingStream>
    derive(
           <columnName1> = <expression1>,
           <columnName2> = <expression2>,
           each(
                match(matchExpression),
                <metadataColumn1> = <metadataExpression1>,
                <metadataColumn2> = <metadataExpression2>
               )
          ) ~> <deriveTransformationName>
```

### <a name="example"></a>Ejemplo

El ejemplo siguiente es una columna derivada denominada `CleanData` que toma un flujo entrante `MoviesYear` y crea dos columnas derivadas. La primera columna derivada reemplaza la columna `Rating` por el valor de clasificación como un tipo entero. La segunda columna derivada es un patrón que coincide con cada columna cuyo nombre empieza con "movies". Para cada columna coincidente, crea una columna `movie` que es igual al valor de la columna coincidente con el prefijo "movie_". 

En la interfaz de usuario, esta transformación es similar a la siguiente imagen:

:::image type="content" source="media/data-flow/derive-script.png" alt-text="Ejemplo de derivación":::

En el siguiente fragmento de código se muestra el script del flujo de datos para esta transformación:

```
MoviesYear derive(
                Rating = toInteger(Rating),
                each(
                    match(startsWith(name,'movies')),
                    'movie' = 'movie_' + toString($$)
                )
            ) ~> CleanData
```

## <a name="next-steps"></a>Pasos siguientes

- Obtenga más información sobre el [lenguaje de expresiones de Mapping Data Flow](data-flow-expression-functions.md).
