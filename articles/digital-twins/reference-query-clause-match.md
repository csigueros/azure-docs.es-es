---
title: 'Referencia del lenguaje de consulta de Azure Digital Twins: cláusula MATCH'
titleSuffix: Azure Digital Twins
description: Documentación de referencia de la cláusula MATCH del lenguaje de consulta de Azure Digital Twins
author: baanders
ms.author: baanders
ms.date: 10/07/2021
ms.topic: article
ms.service: digital-twins
ms.openlocfilehash: 8c553d0df1f194c1de9f77e21341cb1022f98158
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/22/2021
ms.locfileid: "130269932"
---
# <a name="azure-digital-twins-query-language-reference-match-clause"></a>Referencia del lenguaje de consulta de Azure Digital Twins: cláusula MATCH

Este documento contiene información de referencia sobre la **cláusula MATCH** para el [lenguaje de consulta de Azure Digital Twins](concepts-query-language.md).

La cláusula `MATCH` se usa en el lenguaje de consulta de Azure Digital Twins como parte de la [cláusula FROM](reference-query-clause-from.md). `MATCH` permite especificar el patrón que debe seguirse al recorrer las relaciones en el grafo de Azure Digital Twins. Esto se conoce también como patrón de consulta de "salto variable".

Esta cláusula es opcional durante la consulta.

## <a name="core-syntax-match"></a>Sintaxis básica: MATCH

`MATCH` permite cualquier consulta que busque una ruta de acceso entre gemelos con un número impredecible de saltos, basada en determinadas condiciones de relación. 

Una **condición de relación** puede incluir uno o varios de los siguientes datos:
* [Dirección de la relación](#specify-relationship-direction) (de izquierda a derecha, de derecha a izquierda o no direccional).
* [Nombre de la relación](#specify-relationship-name) (un nombre único o una lista de posibilidades).
* [Número de "saltos"](#specify-number-of-hops) de un gemelo a otro (un número exacto o un intervalo).
* [Una asignación de variable de consulta](#assign-query-variable-to-relationship-and-specify-relationship-properties) para representar la relación en el texto de la consulta. Esto también permite filtrar por las propiedades de la relación.

Una consulta con una cláusula `MATCH` también debe usar la  [cláusula WHERE](reference-query-clause-where.md) para especificar el `$dtId` de al menos uno de los gemelos a los que hace referencia.

>[!NOTE]
>`MATCH` es un superconjunto de todas las consultas `JOIN` que se pueden realizar en el almacén de consultas.

### <a name="syntax"></a>Sintaxis

Ésta es la sintaxis de `MATCH` básica. 

Los valores de marcador de posición que se muestran en la cláusula `MATCH` que deben reemplazarse por sus valores son `twin_1`, `relationship_condition` y `twin_2`. Los valores de marcador de posición de la cláusula `WHERE` que deben reemplazarse por sus valores son `twin_or_twin_collection` y `twin_ID`.

:::code language="sql" source="~/digital-twins-docs-samples/queries/reference.sql" id="MatchSyntax":::

Puede omitir el nombre de uno de los gemelos para permitir que cualquier nombre de gemelo funcione en ese lugar.

También puede cambiar el número de condiciones de relación para tener varias condiciones [encadenadas](#combine-match-operations) o ninguna condición de relación:

:::code language="sql" source="~/digital-twins-docs-samples/queries/reference.sql" id="MatchChainSyntax":::

:::code language="sql" source="~/digital-twins-docs-samples/queries/reference.sql" id="MatchNodeSyntax":::

Para obtener más información sobre cada tipo de condición de relación y cómo combinarlos, consulte las demás secciones de este documento.

### <a name="example"></a>Ejemplo

Esta es una consulta de ejemplo donde se utiliza `MATCH`.

La consulta especifica una [dirección de relación](#specify-relationship-direction) y busca gemelos de edificios y sensores donde...
* el sensor es el objetivo de cualquier relación de un edificio con el `$dtId` Building21 y 
* el sensor tiene una temperatura superior a 50.
El edificio y el sensor se incluyen en el resultado de la consulta.

:::code language="sql" source="~/digital-twins-docs-samples/queries/reference.sql" id="MatchExample":::

## <a name="specify-relationship-direction"></a>Especificación de la dirección de la relación

Use la condición de relación de la cláusula `MATCH` para especificar una dirección de relación entre los gemelos. Las direcciones posibles incluyen son de izquierda a derecha, de derecha a izquierda o no direccional. Las relaciones cíclicas se detectan automáticamente, de modo que una relación se recorre solo una vez.

> [!NOTE]
> Se pueden representar relaciones bidireccionales mediante el [encadenamiento](#combine-match-operations).

### <a name="syntax"></a>Sintaxis

>[!NOTE]
> Los ejemplos de esta sección se centran en la dirección de la relación. No especifican nombres de relación, tienen como valor predeterminado un único salto y no asignan variables de consulta a las relaciones. Para obtener instrucciones sobre cómo hacer más cosas con estas otras condiciones, vea [Especificación del nombre de la relación](#specify-relationship-name), [Especificación del número de saltos](#specify-number-of-hops) y [Asignación de una variable de consulta a una relación](#assign-query-variable-to-relationship-and-specify-relationship-properties). Para obtener información sobre cómo usar varias de estas condiciones juntas en la misma consulta, vea [Combinación de operaciones MATCH](#combine-match-operations).

Las descripciones de relaciones direccionales usan una representación visual de una flecha para indicar la dirección de la relación. La flecha incluye un espacio delimitado por corchetes (`[]`) para un [nombre de relación](#specify-number-of-hops) opcional. 

En esta sección, se muestra la sintaxis de distintas direcciones de las relaciones. Los valores de marcador de posición que deben reemplazarse por sus valores son `source_twin` y `target_twin`.

Para una relación **de izquierda a derecha**, utilice la siguiente sintaxis.

:::code language="sql" source="~/digital-twins-docs-samples/queries/reference.sql" id="MatchDirectionLRSyntax":::

Para una relación **de derecha a izquierda**, utilice la siguiente sintaxis.

:::code language="sql" source="~/digital-twins-docs-samples/queries/reference.sql" id="MatchDirectionRLSyntax":::

Para una relación **no direccional**, utilice la siguiente sintaxis. Esto no especificará una dirección para la relación, por lo que se incluirán en el resultado las relaciones de cualquier dirección.

:::code language="sql" source="~/digital-twins-docs-samples/queries/reference.sql" id="MatchDirectionNDSyntax":::

>[!TIP]
>Las consultas no direccionales requieren procesamiento adicional, lo que puede suponer una mayor latencia y un costo superior.

### <a name="examples"></a>Ejemplos

En el primer ejemplo, se muestra un recorrido direccional **de izquierda a derecha**. Esta consulta busca los gemelos *room* y *factory*, donde...
* *room* tiene *factory* como destino (con cualquier nombre de relación);
* *room* tiene un valor de temperatura superior a 50;
* *factory* tiene el `$dtId` "ABC".

:::code language="sql" source="~/digital-twins-docs-samples/queries/reference.sql" id="MatchDirectionLRExample":::

En el ejemplo siguiente, se muestra un recorrido direccional **de derecha a izquierda**. Esta consulta es similar a la anterior, pero se invierte la dirección de la relación entre *room* y *factory*. Esta consulta busca los gemelos *room* y *factory*, donde...
* *factory* tiene *room* como destino (con cualquier nombre de relación);
* *factory* tiene el `$dtId` "ABC";
* *room* tiene un valor de temperatura superior a 50.

:::code language="sql" source="~/digital-twins-docs-samples/queries/reference.sql" id="MatchDirectionRLExample":::

En el ejemplo siguiente, se muestra un recorrido **no direccional**. Esta consulta busca los gemelos *room* y *factory*, donde...
* *room* y *factory* comparten cualquier nombre de relación, en cualquier dirección;
* *factory* tiene el `$dtId` "ABC";
* *room* tiene un valor de humedad superior a 70.

:::code language="sql" source="~/digital-twins-docs-samples/queries/reference.sql" id="MatchDirectionNDExample":::

## <a name="specify-relationship-name"></a>Especificación del nombre de la relación

Opcionalmente, puede usar la condición de relación de la cláusula `MATCH` para especificar nombres para las relaciones entre los gemelos. Puede especificar un único nombre o una lista de posibles nombres. El nombre de relación opcional se incluye como parte de la [sintaxis de flecha para especificar la dirección de la relación](#specify-relationship-direction).

Si no proporciona un nombre de relación, la consulta incluye todos los nombres de relación de forma predeterminada.

>[!TIP]
>La especificación de nombres de relación en la consulta puede mejorar el rendimiento y hacer que los resultados sea más predecibles.

### <a name="syntax"></a>Sintaxis

>[!NOTE]
> Los ejemplos de esta sección se centran en el nombre de la relación. Todos muestran relaciones no direccionales, tienen un único salto de forma predeterminada y no asignan variables de consulta a las relaciones. Para obtener instrucciones sobre cómo hacer más cosas con estas otras condiciones, vea [Especificación de la dirección de la relación](#specify-relationship-direction), [Especificación del número de saltos](#specify-number-of-hops) y [Asignación de una variable de consulta a una relación](#assign-query-variable-to-relationship-and-specify-relationship-properties). Para obtener información sobre cómo usar varias de estas condiciones juntas en la misma consulta, vea [Combinación de operaciones MATCH](#combine-match-operations).

Especifique el nombre de una relación para recorrerla en la cláusula `MATCH` entre corchetes (`[]`). En esta sección, se muestra la sintaxis para especificar relaciones con nombre.

Para un **único nombre**, utilice la siguiente sintaxis. Los valores de marcador de posición que deben reemplazarse por sus valores son `twin_1`, `relationship_name` y `twin_2`.

:::code language="sql" source="~/digital-twins-docs-samples/queries/reference.sql" id="MatchNameSingleSyntax":::

Para **varios nombres posibles**, utilice la siguiente sintaxis. Los valores de marcador de posición que deben reemplazarse por sus valores son `twin_1`, `relationship_name_option_1`, `relationship_name_option_2`, `twin_2` y la nota para continuar el patrón según sea necesario para el número de nombres de relación que desee especificar.

:::code language="sql" source="~/digital-twins-docs-samples/queries/reference.sql" id="MatchNameMultiSyntax":::

(Valor predeterminado) Para dejar el nombre **sin especificar**, deje los corchetes vacíos, sin información de nombre, de la siguiente forma:

:::code language="sql" source="~/digital-twins-docs-samples/queries/reference.sql" id="MatchNameAllSyntax":::

### <a name="examples"></a>Ejemplos

En el ejemplo siguiente, se muestra **un único nombre de relación**. Esta consulta busca los gemelos *building* y *sensor*, donde...
* *building* tiene una relación "contains" con *sensor* (en cualquier dirección);
* *building* tiene el `$dtId` "Seattle21".

:::code language="sql" source="~/digital-twins-docs-samples/queries/reference.sql" id="MatchNameSingleExample":::

En el ejemplo siguiente, se muestran **varios nombres de relación posibles**. Esta consulta es similar a la anterior, pero hay varios nombres de relación posibles que se incluyen en el resultado. Esta consulta busca los gemelos *building* y *sensor*, donde...
* *building* tiene una relación "contains" o "isAssociatedWith" con *sensor* (en cualquier dirección);
* *building* tiene el `$dtId` "Seattle21".

:::code language="sql" source="~/digital-twins-docs-samples/queries/reference.sql" id="MatchNameMultiExample":::

En el ejemplo siguiente, **no se especifica ningún nombre de relación**. Como resultado, se incluirán las relaciones con cualquier nombre en el resultado de la consulta. Esta consulta busca los gemelos *building* y *sensor*, donde...
* *building* tiene una relación con *sensor* con cualquier nombre (y en cualquier dirección);
* *building* tiene el `$dtId` "Seattle21".

:::code language="sql" source="~/digital-twins-docs-samples/queries/reference.sql" id="MatchNameAllExample":::

## <a name="specify-number-of-hops"></a>Especificación del número de saltos

Opcionalmente, puede usar la condición de relación de la cláusula `MATCH` para especificar el número de saltos de las relaciones entre los gemelos. Puede especificar un número exacto o un intervalo. Este valor opcional se incluye como parte de la [sintaxis de flecha para especificar la dirección de la relación](#specify-relationship-direction).

Si no proporciona un número de saltos, la consulta tendrá como valor predeterminado un salto.

>[!IMPORTANT]
>Si especifica un número de saltos mayor que uno, no puede [asignar una variable de consulta a la relación](#assign-query-variable-to-relationship-and-specify-relationship-properties). Solo se puede usar una de estas condiciones dentro de la misma consulta. 

### <a name="syntax"></a>Sintaxis

>[!NOTE]
>Los ejemplos de esta sección se centran en el número de saltos. Todos muestran relaciones no direccionales sin especificar nombres. Para obtener instrucciones sobre cómo hacer más cosas con estas otras condiciones, vea [Especificación de la dirección de la relación](#specify-relationship-direction) y [Especificación del nombre de la relación](#specify-relationship-name). Para obtener información sobre cómo usar varias de estas condiciones juntas en la misma consulta, vea [Combinación de operaciones MATCH](#combine-match-operations).

Especifique el número de saltos que se recorrerán en la cláusula `MATCH` entre corchetes (`[]`).

Para especificar un **número exacto de saltos**, utilice la siguiente sintaxis. Los valores de marcador de posición que deben reemplazarse por sus valores son `twin_1`, `number_of_hops` y `twin_2`.

:::code language="sql" source="~/digital-twins-docs-samples/queries/reference.sql" id="MatchHopsExactSyntax":::

Para especificar un **intervalo de saltos**, utilice la siguiente sintaxis. Los valores de marcador de posición que deben reemplazarse por sus valores son `twin_1`, `starting_limit`, `ending_limit` y `twin_2`. El límite inicial **no** se incluye en el intervalo, mientras que el límite final **sí** se incluye.

:::code language="sql" source="~/digital-twins-docs-samples/queries/reference.sql" id="MatchHopsRangeSyntax":::

También puede omitir el límite inicial para indicar "cualquier cosa hasta" (e incluyendo) el límite final. Siempre debe proporcionarse un límite final.

:::code language="sql" source="~/digital-twins-docs-samples/queries/reference.sql" id="MatchHopsRangeEndingSyntax":::

(Valor predeterminado) Para usar el valor predeterminado de **un salto**, deje los corchetes vacíos, sin información de saltos, de la siguiente manera:

:::code language="sql" source="~/digital-twins-docs-samples/queries/reference.sql" id="MatchHopsOneSyntax":::

### <a name="examples"></a>Ejemplos

En el ejemplo siguiente, se especifica un **número exacto de saltos**. La consulta solo devolverá las relaciones entre los gemelos *floor* y *room* que tengan exactamente 3 saltos.

:::code language="sql" source="~/digital-twins-docs-samples/queries/reference.sql" id="MatchHopsExactExample":::

En el ejemplo siguiente, se especifica un **intervalo de saltos**. La consulta devolverá las relaciones entre los gemelos *floor* y *room* que tengan entre 1 y 3 saltos (lo que significa que el número de saltos es 2 o 3).

:::code language="sql" source="~/digital-twins-docs-samples/queries/reference.sql" id="MatchHopsRangeExample1":::

También puede mostrar un intervalo proporcionando solo un límite. En el ejemplo siguiente, la consulta devolverá las relaciones entre los gemelos *floor* y *room* que tengan, como máximo, 2 saltos (lo que significa que el número de saltos es 1 o 2).

:::code language="sql" source="~/digital-twins-docs-samples/queries/reference.sql" id="MatchHopsRangeEndingExample":::

En el ejemplo siguiente, no se especifica el número de saltos, por lo que se usará el valor predeterminado de **un salto** entre los gemelos *floor* y *room*.

:::code language="sql" source="~/digital-twins-docs-samples/queries/reference.sql" id="MatchHopsOneExample":::

## <a name="assign-query-variable-to-relationship-and-specify-relationship-properties"></a>Asignación de una variable de consulta a una relación (y especificación de propiedades de relación)

Opcionalmente, puede asignar una variable de consulta a la relación a la que se hace referencia en la cláusula `MATCH`, de modo que podrá hacer referencia a ella por nombre en el texto de la consulta.

Una ventaja de hacer esto es la capacidad de filtrar por las propiedades de la relación en la cláusula `WHERE`.

>[!IMPORTANT]
> La asignación de una variable de consulta a la relación se permite solamente cuando la consulta especifica un único salto. En una consulta, debe elegir entre especificar una variable de relación y [especificar un número mayor de saltos](#specify-number-of-hops).

### <a name="syntax"></a>Sintaxis

>[!NOTE]
>Los ejemplos de esta sección se centran en una variable de consulta para la relación. Todos muestran relaciones no direccionales sin especificar nombres. Para obtener instrucciones sobre cómo hacer más cosas con estas otras condiciones, vea [Especificación de la dirección de la relación](#specify-relationship-direction) y [Especificación del nombre de la relación](#specify-relationship-name). Para obtener información sobre cómo usar varias de estas condiciones juntas en la misma consulta, vea [Combinación de operaciones MATCH](#combine-match-operations).

Para asignar una variable de consulta a la relación, ponga el nombre de la variable entre corchetes (`[]`). Los valores de marcador de posición que deben reemplazarse por sus valores son `twin_1`, `relationship_variable` y `twin_2`.

:::code language="sql" source="~/digital-twins-docs-samples/queries/reference.sql" id="MatchVariableSyntax":::

### <a name="examples"></a>Ejemplos

En el ejemplo siguiente, se asigna una variable de consulta "r" a la relación. Más adelante, en la cláusula `WHERE`, se utiliza la variable para especificar que la relación *rel* debe tener una propiedad name con el valor "child".

:::code language="sql" source="~/digital-twins-docs-samples/queries/reference.sql" id="MatchVariableExample":::

## <a name="combine-match-operations"></a>Combinación de operaciones MATCH

Puede combinar varias condiciones de relación en la misma consulta. También puede encadenar varias condiciones de relación para expresar relaciones bidireccionales u otras combinaciones más grandes.

### <a name="syntax"></a>Sintaxis

En una sola consulta, puede combinar la [dirección de la relación](#specify-relationship-direction), el [nombre de la relación](#specify-number-of-hops) y el [número de saltos](#specify-number-of-hops) o [una asignación de variable de consulta](#assign-query-variable-to-relationship-and-specify-relationship-properties) (**uno de los dos**).

En los ejemplos de sintaxis siguientes, se muestra cómo se pueden combinar estos atributos. También puede omitir cualquiera de los detalles opcionales que se muestran en los marcadores de posición para omitir esa parte de la condición.

Para especificar **la dirección de la relación, el nombre de la relación y el número de saltos** en una sola consulta, utilice la siguiente sintaxis en la condición de relación. Los valores de marcador de posición que deben reemplazarse por sus valores son `twin_1` y `twin_2`, `optional_left_angle_bracket` y `optional_right_angle_bracket`, `relationship_name(s)` y `number_of_hops`.

:::code language="sql" source="~/digital-twins-docs-samples/queries/reference.sql" id="MatchCombinedHopsSyntax":::

Para especificar **la dirección de la relación, el nombre de la relación y una variable de consulta para la relación** en una sola consulta, utilice la siguiente sintaxis en la condición de relación. Los valores de marcador de posición que deben reemplazarse por sus valores son `twin_1` y `twin_2`, `optional_left_angle_bracket` y `optional_right_angle_bracket`, `relationship_variable` y `relationship_name(s)`.

:::code language="sql" source="~/digital-twins-docs-samples/queries/reference.sql" id="MatchCombinedVariableSyntax":::

>[!NOTE]
>Según las opciones para [especificar la dirección de la relación](#specify-relationship-direction), debe elegir entre un paréntesis angular izquierdo para una relación de izquierda a derecha o un paréntesis angular derecho para una relación de derecha a izquierda. No puede incluir los dos en la misma flecha, pero puede representar relaciones bidireccionales mediante encadenamiento.

Puede **encadenar** varias condiciones de relación como se muestra a continuación. Los valores de marcador de posición que deben reemplazarse por sus valores son `twin_1`, todas las instancias de `relationship_condition` y `twin_2`.

:::code language="sql" source="~/digital-twins-docs-samples/queries/reference.sql" id="MatchChainSyntax":::

### <a name="examples"></a>Ejemplos

Este es un ejemplo que combina la **dirección de la relación, el nombre de la relación y el número de saltos**. En la siguiente consulta, se buscan los gemelos *floor* y *room*, donde la relación entre *floor* y *room* cumple estas condiciones:
* la relación es de izquierda a derecha, con *floor* como origen y *room* como destino;
* la relación tiene el nombre "contains" o "isAssociatedWith";
* la relación tiene 4 o 5 saltos.

La consulta también especifica que el gemelo *floor* tiene el `$dtId` "thermostat-15".

:::code language="sql" source="~/digital-twins-docs-samples/queries/reference.sql" id="MatchCombinedHopsExample":::

Este es un ejemplo donde se combinan **la dirección de la relación, el nombre de la relación y una variable de consulta con nombre para la relación**. En la siguiente consulta, se buscan los gemelos *floor* y *room*, donde se asigna la relación entre *floor* y *room* a una variable de consulta *r* y se cumplen estas condiciones:
* la relación es de izquierda a derecha, con *floor* como origen y *room* como destino;
* la relación tiene el nombre "contains" o "isAssociatedWith";
* la relación, a la que se le da una variable de consulta *r*, tiene una propiedad length igual a 10;

La consulta también especifica que el gemelo *floor* tiene el `$dtId` "thermostat-15".

:::code language="sql" source="~/digital-twins-docs-samples/queries/reference.sql" id="MatchCombinedVariableExample":::

En el ejemplo siguiente, se muestran condiciones de relación **encadenadas**. La consulta busca los gemelos *floor*, *cafe* y *room*, donde...
* la relación entre *floor* y *room* cumple estas condiciones:
    - la relación es de izquierda a derecha, con *floor* como origen y *cafe* como destino;
    - la relación tiene el nombre "contains" o "isAssociatedWith";
    - la relación, a la que se le da la variable de consulta *r*, tiene una propiedad length igual a 10.
* la relación entre *cafe* y *room* cumple estas condiciones:
    - la relación es de derecha a izquierda, con *room* como origen y *café* como destino;
    - la relación tiene el nombre "has" o "includes";
    - la relación tiene un máximo de 3 saltos, es decir, 1, 2 o 3 saltos.

La consulta también especifica que el gemelo *floor* tiene el `$dtId` "thermostat-15" y el gemelo *cafe* tiene una temperatura de 55.

:::code language="sql" source="~/digital-twins-docs-samples/queries/reference.sql" id="MatchCombinedChainExample":::

También puede usar condiciones de relación encadenadas para expresar **relaciones bidireccionales**. La siguiente consulta busca los gemelos *floor,* , *room* y *building*, donde...
* la relación entre *building* y *floor* cumple estas condiciones:
    - la relación es de izquierda a derecha, con *building* como origen y *floor* como destino;
    - la relación tiene el nombre "isAssociatedWith";
    - a la relación se le da una variable de consulta *r1*;
* la relación entre *floor* y *room* cumple estas condiciones:
    - la relación es de derecha a izquierda, con *room* como origen y *floor* como destino;
    - la relación tiene el nombre "isAssociatedWith";
    - a la relación se le da una variable de consulta *r2*.

La consulta también especifica que el gemelo *building* tiene el `$dtId` "building-3" y el gemelo *room* tiene una temperatura superior a 50.

:::code language="sql" source="~/digital-twins-docs-samples/queries/reference.sql" id="MatchCombinedChainBDExample":::

## <a name="limitations"></a>Limitaciones

Los límites siguientes se aplican a las consultas que utilizan `MATCH`:
* Solo se admite una expresión `MATCH` por instrucción de consulta.
* Se requiere `$dtId` en la cláusula `WHERE`.
* Solo se permite asignar una variable de consulta a la relación cuando la consulta especifica un único salto.
* El número máximo de saltos admitidos en una consulta es 10.