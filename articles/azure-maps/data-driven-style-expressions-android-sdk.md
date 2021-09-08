---
title: Expresiones de estilo basadas en datos en mapas de Android | Microsoft Azure Maps
description: Aprenda sobre las expresiones de estilo controladas por datos. Vea cómo usar estas expresiones en Android SDK para Azure Maps para ajustar los estilos de mapas.
author: anastasia-ms
ms.author: v-stharr
ms.date: 2/26/2021
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: cpendle
zone_pivot_groups: azure-maps-android
ms.openlocfilehash: 7c73c52045504dfeeaccf5292aea7ab07b75d8d8
ms.sourcegitcommit: add71a1f7dd82303a1eb3b771af53172726f4144
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/03/2021
ms.locfileid: "123438541"
---
# <a name="data-driven-style-expressions-android-sdk"></a>Expresiones de estilo basadas en datos (Android SDK)

Las expresiones permiten aplicar lógica de negocios a las opciones de estilo que respetan las propiedades definidas en cada forma de un origen de datos. Las expresiones pueden filtrar los datos de un origen o una capa de datos. Pueden contener lógica condicional, como instrucciones if. Además, se pueden usar para manipular los datos mediante operadores de cadena, operadores lógicos y operadores matemáticos.

Los estilos basados en datos reducen la cantidad de código necesario para implementar la lógica de negocios relacionada con los estilos. Cuando se usan con capas, las expresiones se evalúan en tiempo de representación en un subproceso independiente. Esta funcionalidad proporciona mayor rendimiento que cuando la lógica de negocios se evalúa en el subproceso de la interfaz de usuario.

Android SDK para Azure Maps admite casi todas las mismas expresiones de estilo que el SDK web de Azure Maps, por lo que todos los mismos conceptos descritos en las [Expresiones de estilo basadas en datos (SDK web)](data-driven-style-expressions-web-sdk.md) se pueden trasladar a una aplicación Android. Todas las expresiones de estilo de Android SDK para Azure Maps están disponibles en el espacio de nombres `com.microsoft.azure.maps.mapcontrol.options.Expression`. Hay muchos tipos diferentes de expresiones de estilo.

| Tipo de expresiones | Descripción |
|---------------------|-------------|
| [Expresiones booleanas](#boolean-expressions) | Las expresiones booleanas proporcionan un conjunto de expresiones de operadores booleanos para evaluar las comparaciones booleanas. |
| [Expresiones de color](#color-expressions) | Las expresiones de color facilitan la creación y la manipulación de valores de color. |
| [Expresiones condicionales](#conditional-expressions) | Las expresiones condicionales proporcionan operaciones lógicas que son parecidas a las instrucciones if. |
| [Expresiones de datos](#data-expressions) | Proporcionan acceso a los datos de propiedad de una característica. |
| [Expresiones de interpolación y paso](#interpolate-and-step-expressions) | Se pueden usar para calcular valores a lo largo de una curva interpolada o una función de paso. |
| [Expresiones basadas en JSON](#json-based-expressions) | Facilita la reutilización de las expresiones basadas en JSON sin formato de estilo creadas para el SDK web con Android SDK. |  
| [Expresiones específicas de capa](#layer-specific-expressions) | Expresiones especiales que solo son aplicables a una capa. |
| [Expresiones matemáticas](#math-expressions) | Proporcionan operadores matemáticos para llevar a cabo cálculos basados en datos dentro del marco de la expresión. |
| [Expresiones de operador de cadena](#string-operator-expressions) | Las expresiones de operador de cadena realizan operaciones de conversión sobre cadenas, como concatenación y conversión de las mayúsculas y minúsculas. |
| [Expresiones de tipo](#type-expressions) | Las expresiones de tipo proporcionan herramientas para probar y convertir distintos tipos de datos, como cadenas, números y valores booleanos. |
| [Expresiones de enlace de variable](#variable-binding-expressions) | Las expresiones de enlace de variables almacenan los resultados de un cálculo en una variable y permiten utilizar referencias en otra parte de una expresión varias veces sin tener que volver a calcular el valor almacenado. |
| [Expresión de zoom](#zoom-expression) | Recupera el nivel de zoom actual del mapa en tiempo de representación. |

> [!NOTE]
> La sintaxis de las expresiones es casi idéntica en Java y Kotlin. Si tiene la documentación establecida en Kotlin, pero ve bloques de código para Java, tenga en cuenta que el código es idéntico en ambos lenguajes.

En todos los ejemplos de esta sección del documento, se utiliza la siguiente característica para mostrar diferentes maneras en que se pueden usar estas expresiones.

```json
{
    "type": "Feature",
    "geometry": {
        "type": "Point",
        "coordinates": [-122.13284, 47.63699]
    },
    "properties": {
        "id": 123,
        "entityType": "restaurant",
        "revenue": 12345,
        "subTitle": "Building 40", 
        "temperature": 64,
        "title": "Cafeteria", 
        "zoneColor": "purple",
        "abcArray": ["a", "b", "c"],
        "array2d": [["a", "b"], ["x", "y"]],
        "_style": {
            "fillColor": "red"
        }
    }
}
```

En el código siguiente se muestra cómo crear manualmente esta característica GeoJSON en una aplicación.

::: zone pivot="programming-language-java-android"

```Java
//Create a point feature.
Feature feature = Feature.fromGeometry(Point.fromLngLat(-100, 45));

//Add properties to the feature.
feature.addNumberProperty("id", 123);
feature.addStringProperty("entityType", "restaurant");
feature.addNumberProperty("revenue", 12345);
feature.addStringProperty("subTitle", "Building 40");
feature.addNumberProperty("temperature", 64);
feature.addStringProperty("title", "Cafeteria");
feature.addStringProperty("zoneColor", "purple");

JsonArray abcArray = new JsonArray();
abcArray.add("a");
abcArray.add("b");
abcArray.add("c");

feature.addProperty("abcArray", abcArray);

JsonArray array1 = new JsonArray();
array1.add("a");
array1.add("b");

JsonArray array2 = new JsonArray();
array1.add("x");
array1.add("y");

JsonArray array2d = new JsonArray();
array2d.add(array1);
array2d.add(array2);

feature.addProperty("array2d", array2d);

JsonObject style = new JsonObject();
style.addProperty("fillColor", "red");

feature.addProperty("_style", style);
```

::: zone-end

::: zone pivot="programming-language-kotlin"

```kotlin
//Create a point feature.
val feature = Feature.fromGeometry(Point.fromLngLat(-100, 45))

//Add properties to the feature.
feature.addNumberProperty("id", 123)
feature.addStringProperty("entityType", "restaurant")
feature.addNumberProperty("revenue", 12345)
feature.addStringProperty("subTitle", "Building 40")
feature.addNumberProperty("temperature", 64)
feature.addStringProperty("title", "Cafeteria")
feature.addStringProperty("zoneColor", "purple")

val abcArray = JsonArray()
abcArray.add("a")
abcArray.add("b")
abcArray.add("c")

feature.addProperty("abcArray", abcArray)

val array1 = JsonArray()
array1.add("a")
array1.add("b")

val array2 = JsonArray()
array1.add("x")
array1.add("y")

val array2d = JsonArray()
array2d.add(array1)
array2d.add(array2)

feature.addProperty("array2d", array2d)

val style = JsonObject()
style.addProperty("fillColor", "red")

feature.addProperty("_style", style)
```

::: zone-end

En el código siguiente se muestra cómo deserializar la versión con cadenas del objeto JSON en una característica GeoJSON en una aplicación.

::: zone pivot="programming-language-java-android"

```java
String featureString = "{\"type\":\"Feature\",\"geometry\":{\"type\":\"Point\",\"coordinates\":[-122.13284,47.63699]},\"properties\":{\"id\":123,\"entityType\":\"restaurant\",\"revenue\":12345,\"subTitle\":\"Building 40\",\"temperature\":64,\"title\":\"Cafeteria\",\"zoneColor\":\"purple\",\"abcArray\":[\"a\",\"b\",\"c\"],\"array2d\":[[\"a\",\"b\"],[\"x\",\"y\"]],\"_style\":{\"fillColor\":\"red\"}}}";

Feature feature = Feature.fromJson(featureString);
```

::: zone-end

::: zone pivot="programming-language-kotlin"

```kotlin
val featureString = "{\"type\":\"Feature\",\"geometry\":{\"type\":\"Point\",\"coordinates\":[-122.13284,47.63699]},\"properties\":{\"id\":123,\"entityType\":\"restaurant\",\"revenue\":12345,\"subTitle\":\"Building 40\",\"temperature\":64,\"title\":\"Cafeteria\",\"zoneColor\":\"purple\",\"abcArray\":[\"a\",\"b\",\"c\"],\"array2d\":[[\"a\",\"b\"],[\"x\",\"y\"]],\"_style\":{\"fillColor\":\"red\"}}}"

val feature = Feature.fromJson(featureString)
```

::: zone-end

## <a name="json-based-expressions"></a>Expresiones basadas en JSON

El SDK web de Azure Maps también admite expresiones de estilo basadas en datos que se representan mediante una matriz JSON. Estas mismas expresiones se pueden volver a crear mediante la clase `Expression` nativa en Android SDK. Como alternativa, estas expresiones basadas en JSON se pueden convertir en una cadena mediante una función web como `JSON.stringify` y pasarse al método `Expression.raw(String rawExpression)`. Por ejemplo, observe la siguiente expresión JSON.

```javascript
var exp = ['get','title'];
JSON.stringify(exp); // = "['get','title']"
```

La versión con cadenas de la expresión anterior sería `"['get','title']"` y se puede leer en Android SDK como se indica a continuación.

::: zone pivot="programming-language-java-android"

```java
Expression exp = Expression.raw("['get','title']")
```

::: zone-end

::: zone pivot="programming-language-kotlin"

```kotlin
val exp = Expression.raw("['get','title']")
```

::: zone-end

El uso de este enfoque puede facilitar la reutilización de expresiones de estilo entre aplicaciones móviles y web que usan Azure Maps.

En este vídeo encontrará información general sobre la aplicación de estilos basados en datos en Azure Maps.

</br>

>[!VIDEO https://channel9.msdn.com/Shows/Internet-of-Things-Show/Data-Driven-Styling-with-Azure-Maps/player?format=ny]

## <a name="data-expressions"></a>Expresiones de datos

Las expresiones de datos proporcionan acceso a los datos de propiedad de una característica.

| Expression | Tipo de valor devuelto | Descripción |
|------------|-------------|-------------|
| `accumulated()` | number | Obtiene el valor de una propiedad de clúster acumulada hasta el momento. Solo se puede usar en la opción `clusterProperties` de un origen de `DataSource` agrupado. |
| `at(number | Expression, Expression)` | value | Recupera un elemento de una matriz. |
| `geometryType()` | string | Obtiene el tipo de geometría de la característica: Point, MultiPoint, LineString, MultiLineString, Polygon, MultiPolygon. |
| `get(string | Expression)` \| `get(string | Expression, Expression)` | value | Obtiene el valor de propiedad de las propiedades del objeto proporcionado. Devuelve null si no se encuentra la propiedad solicitada. |
| `has(string | Expression)` \| `has(string | Expression, Expression)` | boolean | Determina si las propiedades de una característica tienen la propiedad especificada. |
| `id()` | value | Obtiene el identificador de la característica si lo tiene. |
| `in(string | number | Expression, Expression)` | boolean | Determina si un elemento existe en una matriz. |
| `length(string | Expression)` | number | Obtiene la longitud de una cadena o matriz. |
| `properties()`| value | Obtiene el objeto de propiedades de la característica. |

Las siguientes expresiones de estilo del SDK web no se admiten en Android SDK:

- index-of
- slice

**Ejemplos**

Se puede acceder directamente a las propiedades de una característica en una expresión mediante una expresión `get`. En este ejemplo, el valor `zoneColor` de la característica se utiliza para especificar la propiedad de color de una capa de burbujas.

::: zone pivot="programming-language-java-android"

```java
BubbleLayer layer = new BubbleLayer(source,
    //Get the zoneColor value.
    bubbleColor(get("zoneColor"))
);
```

::: zone-end

::: zone pivot="programming-language-kotlin"

```kotlin
val layer = BubbleLayer(source,
    //Get the zoneColor value.
    bubbleColor(get("zoneColor"))
)
```

::: zone-end

El ejemplo anterior funcionará bien si todas las características de punto tienen la propiedad `zoneColor`. De lo contrario, es probable que se utilice el color de reserva "black". Si desea modificar el color de reserva, utilice una expresión `switchCase` junto con la expresión `has` para comprobar si existe la propiedad. Si la propiedad no existe, se devolverá un color de reserva.

::: zone pivot="programming-language-java-android"

```java
BubbleLayer layer = new BubbleLayer(source,
    bubbleColor(
        //Use a conditional case expression.
        switchCase(
            //Check to see if feature has a "zoneColor" 
            has("zoneColor"), 

            //If it does, use it.
            get("zoneColor"), 

            //If it doesn't, default to blue.
            literal("blue")
        )
    )
);
```

::: zone-end

::: zone pivot="programming-language-kotlin"

```kotlin
val layer = BubbleLayer(source,
    bubbleColor(
        //Use a conditional case expression.
        switchCase(
            //Check to see if feature has a "zoneColor" 
            has("zoneColor"), 

            //If it does, use it.
            get("zoneColor"), 

            //If it doesn't, default to blue.
            literal("blue")
        )
    )
)
```

::: zone-end

De forma predeterminada, las capas de burbujas y símbolos representarán las coordenadas de todas las formas de un origen de datos. Este comportamiento puede resaltar los vértices de una línea o un polígono. La opción `filter` de la capa puede emplearse para limitar el tipo de geometría de las características que representa, utilizando una expresión `geometryType` dentro de una expresión booleana. En el siguiente ejemplo se limita una capa de burbujas para que solo se representen las características `Point`.

::: zone pivot="programming-language-java-android"

```java
BubbleLayer layer = new BubbleLayer(source,
    filter(eq(geometryType(), "Point"))
);
```

::: zone-end

::: zone pivot="programming-language-kotlin"

```kotlin
val layer = BubbleLayer(source,
    filter(eq(geometryType(), "Point"))
)
```

::: zone-end

El siguiente ejemplo permite que se representen las características `Point` y `MultiPoint`.

::: zone pivot="programming-language-java-android"

```java
BubbleLayer layer = new BubbleLayer(source,
    filter(any(eq(geometryType(), "Point"), eq(geometryType(), "MultiPoint")))
);
```

::: zone-end

::: zone pivot="programming-language-kotlin"

```kotlin
val layer = BubbleLayer(source,
    filter(any(eq(geometryType(), "Point"), eq(geometryType(), "MultiPoint")))
)
```

::: zone-end

Igualmente, el contorno de los polígonos se representará en capas de línea. Para deshabilitar este comportamiento en una capa de línea, agregue un filtro que solo permita las características `LineString` y `MultiLineString`.  

A continuación, se muestran algunos ejemplos adicionales de cómo usar expresiones de datos:

```java
//Get item [2] from an array "properties.abcArray[1]" = "c"
at(2, get("abcArray"))

//Get item [0][1] from a 2D array "properties.array2d[0][1]" = "b"
at(1, at(0, get("array2d")))

//Check to see if a value is in an array "properties.abcArray.indexOf('a') !== -1" = true
in("a", get("abcArray"))

//Get the length of an array "properties.abcArray.length" = 3
length(get("abcArray"))

//Get the value of a subproperty "properties._style.fillColor" = "red"
get("fillColor", get("_style"))

//Check that "fillColor" exists as a subproperty of "_style".
has("fillColor", get("_style"))
```

## <a name="math-expressions"></a>Expresiones matemáticas

Las expresiones matemáticas proporcionan operadores matemáticos para llevar a cabo cálculos basados en datos dentro del marco de la expresión.

| Expression | Tipo de valor devuelto | Descripción |
|------------|-------------|-------------|
| `abs(number | Expression)` | number | Calcula el valor absoluto del número especificado. |
| `acos(number | Expression)` | number | Calcula el arcocoseno del número especificado. |
| `asin(number | Expression)` | number | Calcula el arcoseno del número especificado. |
| `atan(number | Expression)` | number | Calcula el arcotangente del número especificado. |
| `ceil(number | Expression)` | number | Redondea el número al siguiente número entero. |
| `cos(number | Expression)` | number | Calcula el coseno del número especificado. |
| `division(number, number)` \| `division(Expression, Expression)` | number | Divide el primer número entre el segundo. Expresión equivalente del SDK web: `/` |
| `e()` | number | Devuelve la constante matemática `e`. |
| `floor(number | Expression)` | number | Redondea el número hacia abajo al número entero anterior. |
| `log10(number | Expression)` | number | Calcula el logaritmo en base 10 del número especificado. |
| `log2(number | Expression)` | number | Calcula el logaritmo en base dos del número especificado. |
| `ln(number | Expression)` | number | Calcula el logaritmo natural del número especificado. |
| `ln2()` | number | Devuelve la constante matemática `ln(2)`. |
| `max(numbers... | expressions...)` | number | Calcula el número máximo en el conjunto especificado de números. |
| `min(numbers... | expressions...)` | number | Calcula el número mínimo en el conjunto especificado de números. |
| `mod(number, number)` \| `mod(Expression, Expression)` | number | Calcula el resto al dividir el primer número entre el segundo. Expresión equivalente del SDK web: `%` |
| `pi()` | number | Devuelve la constante matemática `PI`. |
| `pow(number, number)` \| `pow(Expression, Expression)` | number | Calcula el valor del primer valor elevado a la potencia del segundo número. |
| `product(numbers... | expressions...)` | number | Multiplica los números especificados juntos. Expresión equivalente del SDK web: `*` |
| `round(number | Expression)` | number | Redondea el número al entero más cercano. Los valores a medio camino se redondean al valor más alejado de cero. Por ejemplo, `round(-1.5)` se evalúa como `-2`. |
| `sin(number | Expression)` | number | Calcula el seno del número especificado. |
| `sqrt(number | Expression)` | number | Calcula la raíz cuadrada del número especificado. |
| `subtract(number | Expression` | number | Resta 0 al número especificado. |
| `subtract(number | Expression, number | Expression)` | number | Resta las primeras cifras al segundo número. |
| `sum(numbers... | expressions...)` | number | Calcula la suma de los números especificados. |
| `tan(number | Expression)` | number | Calcula la tangente del número especificado. |

## <a name="boolean-expressions"></a>Expresiones booleanas

Las expresiones booleanas proporcionan un conjunto de expresiones de operadores booleanos para evaluar las comparaciones booleanas.

Cuando se comparan valores, la comparación debe estar estrictamente tipada. Los valores de distintos tipos siempre se consideran desiguales. Los casos en los que se sabe que los tipos son diferentes en tiempo de análisis se consideran no válidos y producirán un error de análisis.

| Expression | Tipo de valor devuelto | Descripción |
|------------|-------------|-------------|
| `all(Expression...)` | boolean | Devuelve `true` si todas las entradas son `true`; de lo contrario, `false`. |
| `any(Expression...)` | boolean | Devuelve `true` si cualquiera de las entradas es `true`; de lo contrario, `false`. |
| `eq(Expression compareOne, Expression | boolean | number | string compareTwo)` \| `eq(Expression compareOne, Expression | string compareTwo, Expression collator)` | boolean | Devuelve `true` si los valores de entrada son iguales; de lo contrario `false`. Es necesario que los argumentos sean ambos cadenas o ambos números. |
| `gt(Expression compareOne, Expression | boolean | number | string compareTwo)` \| `gt(Expression compareOne, Expression | string compareTwo, Expression collator)` | boolean | Devuelve `true` si la primera entrada es estrictamente mayor que la segunda; de lo contrario, `false`. Es necesario que los argumentos sean ambos cadenas o ambos números. |
| `gte(Expression compareOne, Expression | boolean | number | string compareTwo)` \| `gte(Expression compareOne, Expression | string compareTwo, Expression collator)` | boolean | Devuelve `true` si la primera entrada es mayor o igual que la segunda; de lo contrario `false`. Es necesario que los argumentos sean ambos cadenas o ambos números. |
| `lt(Expression compareOne, Expression | boolean | number | string compareTwo)` \| `lt(Expression compareOne, Expression | string compareTwo, Expression collator)` | boolean | Devuelve `true` si la primera entrada es estrictamente menor que el segundo; de lo contrario, `false`. Es necesario que los argumentos sean ambos cadenas o ambos números. |
| `lte(Expression compareOne, Expression | boolean | number | string compareTwo)` \| `lte(Expression compareOne, Expression | string compareTwo, Expression collator)` | boolean | Devuelve `true` si la primera entrada es menor o igual que el segundo; de lo contrario, `false`. Es necesario que los argumentos sean ambos cadenas o ambos números. |
| `neq(Expression compareOne, Expression | boolean | number | string compareTwo)` \| `neq(Expression compareOne, Expression | string compareTwo, Expression collator)` | boolean | Devuelve `true` si los valores de entrada no son iguales; de lo contrario, `false`. |
| `not(Expression | boolean)` | boolean | Negación lógica. Devuelve `true` si la entrada es `false`, y `false` si la entrada es `true`. |

## <a name="conditional-expressions"></a>Expresiones condicionales

Las expresiones condicionales proporcionan operaciones lógicas que son parecidas a las instrucciones if.

Las expresiones siguientes realizan operaciones de lógica condicional sobre los datos de entrada. Por ejemplo, la expresión `switchCase` proporciona la lógica "if/then/else" mientras que la expresión `match` es como una "instrucción swtich".

### <a name="switch-case-expression"></a>Expresión switchCase

Una expresión `switchCase` es un tipo de expresión condicional que proporciona la lógica "if/then/else". Este tipo de expresión recorre paso a paso una lista de condiciones booleanas. Devuelve el valor de salida de la primera condición booleana que se evalúa como true.

El pseudocódigo siguiente define la estructura de la expresión `switchCase`.

```java
switchCase(
    condition1: boolean expression, 
    output1: value,
    condition2: boolean expression, 
    output2: value,
    ...,
    fallback: value
)
```

**Ejemplo**

En el ejemplo siguiente se recorren diferentes condiciones booleanas hasta encontrar una que se evalúe como `true` y, luego, se devuelve ese valor asociado. Si ninguna condición booleana se evalúa como `true`, se devolverá un valor de retroceso.

::: zone pivot="programming-language-java-android"

```java
BubbleLayer layer = new BubbleLayer(source,
    bubbleColor(
        switchCase(
            //Check to see if the first boolean expression is true, and if it is, return its assigned result.
            //If it has a zoneColor property, use its value as a color.
            has("zoneColor"), toColor(get("zoneColor")),

            //Check to see if the second boolean expression is true, and if it is, return its assigned result.
            //If it has a temperature property with a value greater than or equal to 100, make it red.
            all(has("temperature"), gte(get("temperature"), 100)), color(Color.RED),
            
            //Specify a default value to return. In this case green.
            color(Color.GREEN)
        )
    )
);
```

::: zone-end

::: zone pivot="programming-language-kotlin"

```kotlin
val layer = BubbleLayer(source,
    bubbleColor(
        switchCase(
            //Check to see if the first boolean expression is true, and if it is, return its assigned result.
            //If it has a zoneColor property, use its value as a color.
            has("zoneColor"), toColor(get("zoneColor")),

            //Check to see if the second boolean expression is true, and if it is, return its assigned result.
            //If it has a temperature property with a value greater than or equal to 100, make it red.
            all(has("temperature"), gte(get("temperature"), 100)), color(Color.RED),
            
            //Specify a default value to return. In this case green.
            color(Color.GREEN)
        )
    )
)
```

::: zone-end

### <a name="match-expression"></a>Expresión match

Una expresión `match` es un tipo de expresión condicional que proporciona la instrucción switch como lógica. La entrada puede ser cualquier expresión, como `get( "entityType")` que devuelve una cadena o un número. Cada parada debe tener una etiqueta que debe ser un único valor literal o una matriz de valores literales, cuyos valores deben ser todos cadenas o todos números. La entrada coincide si coincide alguno de los valores de la matriz. Cada etiqueta de parada debe ser única. Si el tipo de entrada no coincide con el tipo de las etiquetas, el resultado será el valor de reserva predeterminado.

El pseudocódigo siguiente define la estructura de la expresión `match`.

```java
match(Expression input, Expression defaultOutput, Expression.Stop... stops)
```

**Ejemplos**

En el ejemplo siguiente se examina la propiedad `entityType` de una característica de punto en una capa de burbuja que busca una coincidencia. Si se encuentra una coincidencia, se devuelve ese valor especificado o se devuelve el valor de retroceso.

::: zone pivot="programming-language-java-android"

```java
BubbleLayer layer = new BubbleLayer(source,
    bubbleColor(
        match(
            //Get the input value to match.
            get("entityType"),

            //Specify a default value to return if no match is found.
            color(Color.BLACK),

            //List the values to match and the result to return for each match.

            //If value is "restaurant" return "red".
            stop("restaurant", color(Color.RED)),

            //If value is "park" return "green".
            stop("park", color(Color.GREEN))
        )
    )
);
```

::: zone-end

::: zone pivot="programming-language-kotlin"

```kotlin
val layer = BubbleLayer(source,
    bubbleColor(
        match(
            //Get the input value to match.
            get("entityType"),

            //Specify a default value to return if no match is found.
            color(Color.BLACK),

            //List the values to match and the result to return for each match.

            //If value is "restaurant" return "red".
            stop("restaurant", color(Color.RED)),

            //If value is "park" return "green".
            stop("park", color(Color.GREEN))
        )
    )
)
```

::: zone-end

En el ejemplo siguiente se usa una matriz para enumerar un conjunto de etiquetas que deben devolver el mismo valor. Este enfoque es mucho más eficiente que enumerar cada etiqueta por separado. En este caso, si la propiedad `entityType` es "restaurant" o "grocery_store", se devolverá el color "red".

::: zone pivot="programming-language-java-android"

```java
BubbleLayer layer = new BubbleLayer(source,
    bubbleColor(
        match(
            //Get the input value to match.
            get("entityType"),

            //Specify a default value to return if no match is found.
            color(Color.BLACK),

            //List the values to match and the result to return for each match.

            //If value is "restaurant" or "grocery_store" return "red".
            stop(Arrays.asList("restaurant", "grocery_store"), color(Color.RED)),

            //If value is "park" return "green".
            stop("park", color(Color.GREEN))
        )
    )
);
```

::: zone-end

::: zone pivot="programming-language-kotlin"

```kotlin
val layer = BubbleLayer(source,
    bubbleColor(
        match(
            //Get the input value to match.
            get("entityType"),

            //Specify a default value to return if no match is found.
            color(Color.BLACK),

            //List the values to match and the result to return for each match.

            //If value is "restaurant" or "grocery_store" return "red".
            stop(arrayOf("restaurant", "grocery_store"), color(Color.RED)),

            //If value is "park" return "green".
            stop("park", color(Color.GREEN))
        )
    )
)
```

::: zone-end

### <a name="coalesce-expression"></a>Expresión coalesce

Una expresión `coalesce` recorre un conjunto de expresiones hasta que se obtiene el primer valor distinto de null y devuelve ese valor.

El pseudocódigo siguiente define la estructura de la expresión `coalesce`.

```java
coalesce(Expression... input)
```

**Ejemplo**

En el ejemplo siguiente se usa una expresión `coalesce` para establecer la opción `textField` de una capa de símbolos. Si falta la propiedad `title` de la característica o está establecida en `null`, la expresión intentará buscar la propiedad `subTitle` y, si falta o es `null`, retrocede a una cadena vacía.

::: zone pivot="programming-language-java-android"

```java
SymbolLayer layer = new SymbolLayer(source,
    textField(
        coalesce(
            //Try getting the title property.
            get("title"),

            //If there is no title, try getting the subTitle. 
            get("subTitle"),

            //Default to an empty string.
            literal("")
        )
    )
);
```

::: zone-end

::: zone pivot="programming-language-kotlin"

```kotlin
val layer = SymbolLayer(source,
    textField(
        coalesce(
            //Try getting the title property.
            get("title"),

            //If there is no title, try getting the subTitle. 
            get("subTitle"),

            //Default to an empty string.
            literal("")
        )
    )
)
```

::: zone-end

## <a name="type-expressions"></a>Expresiones de tipo

Las expresiones de tipo proporcionan herramientas para probar y convertir distintos tipos de datos, como cadenas, números y valores booleanos.

| Expression | Tipo de valor devuelto | Descripción |
|------------|-------------|-------------|
| `array(Expression)` | Object[] | Valida que la entrada es una matriz. |
| `bool(Expression)` | boolean | Valida que el valor de entrada es un valor booleano. |
| `collator(boolean caseSensitive, boolean diacriticSensitive)` \| `collator(boolean caseSensitive, boolean diacriticSensitive, java.util.Locale locale)` \| `collator(Expression caseSensitive, Expression diacriticSensitive)` \| `collator(Expression caseSensitive, Expression diacriticSensitive, Expression locale)` | collator | Devuelve un intercalador para su uso en operaciones de comparación que dependen de la configuración regional. Las opciones con distinción de mayúsculas y minúsculas y con distinción de diacríticos tienen como valor predeterminado "false". El argumento de configuración regional especifica la etiqueta de idioma IETF de la configuración regional que se va a usar. Si no se proporciona ninguno, se usa el valor predeterminado de configuración regional. Si la configuración regional solicitada no está disponible, el intercalador usará una configuración regional de reserva definida por el sistema. Utilice la configuración regional resuelta para probar los resultados del comportamiento de reserva de la configuración regional.  |
| `literal(boolean \| number \| string \| Object \| Object[])` | boolean \| number \| string \| Object \| Object[] | Devuelve una matriz literal o un valor de objeto. Use esta expresión para impedir que una matriz o un objeto se evalúen como una expresión. Esto es necesario cuando una matriz o un objeto se deben devolver mediante una expresión. |
| `number(Expression)` | number | Valida que el valor de entrada es un número. |
| `object(Expression)` | Object | Valida que el valor de entrada es un objeto. |
| `string(Expression)` | string | Valida que el valor de entrada es una cadena. |
| `toArray(Expression)` | Object[] | Convierte la expresión en una matriz de objeto JSON. |
| `toBool(Expression)` | boolean | Convierte el valor de entrada en un valor booleano. |
| `toNumber(Expression)` | number | Convierte el valor de entrada en un número, si es posible. |
| `toString(Expression)` | string | Convierte el valor de entrada en una cadena. |
| `typeoOf(Expression)` | string | Devuelve una cadena que describe el tipo del valor especificado. |

## <a name="color-expressions"></a>Expresiones de color

Las expresiones de color facilitan la creación y la manipulación de valores de color.

| Expression | Tipo de valor devuelto | Descripción |
|------------|-------------|-------------|
| `color(int)` | color | Convierte un valor entero de color en una expresión de color. |
| `rgb(Expression red, Expression green, Expression blue)` \| `rgb(number red, number green, number blue)` | color | Crea un valor de color a partir de los componentes *red*, *green* y *blue* que deben oscilar entre`0` y `255`, y un componente alfa de `1`. Si alguno de los componentes está fuera del intervalo, la expresión es un error. |
| `rgba(Expression red, Expression green, Expression blue, Expression alpha)` \| `rgba(number red, number green, number blue, number alpha)` | color | Crea un valor de color a partir de los componentes *red*, *green* y *blue* que deben oscilar entre `0` y `255`, y un componente alfa dentro de un intervalo de `0` y `1`. Si alguno de los componentes está fuera del intervalo, la expresión es un error. |
| `toColor(Expression)` | color  | Convierte el valor de entrada en un color. |
| `toRgba(Expression)` | color | Devuelve una matriz de cuatro elementos que contiene los componentes red, green, blue y alfa componentes, en ese orden. |

**Ejemplo**

En el ejemplo siguiente, se crea un valor de color RGB que tiene el valor `255` en *red* y valores *green* y *blue* que se calculan multiplicando `2.5` por el valor de la propiedad `temperature`. A medida que cambia la temperatura, el color cambiará a diferentes tonalidades de *red*.

::: zone pivot="programming-language-java-android"

```java
BubbleLayer layer = new BubbleLayer(source,
    bubbleColor(
        //Create a RGB color value.
        rgb(
            //Set red value to 255. Wrap with literal expression since using expressions for other values.
            literal(255f),    

            //Multiple the temperature by 2.5 and set the green value.
            product(literal(2.5f), get("temperature")), 

            //Multiple the temperature by 2.5 and set the blue value.
            product(literal(2.5f), get("temperature")) 
        )
    )
);
```

::: zone-end

::: zone pivot="programming-language-kotlin"

```kotlin
val layer = BubbleLayer(source,
    bubbleColor(
        //Create a RGB color value.
        rgb(
            //Set red value to 255. Wrap with literal expression since using expressions for other values.
            literal(255f),    

            //Multiple the temperature by 2.5 and set the green value.
            product(literal(2.5f), get("temperature")), 

            //Multiple the temperature by 2.5 and set the blue value.
            product(literal(2.5f), get("temperature")) 
        )
    )
)
```

::: zone-end

Si todos los parámetros de color son números, no es necesario ajustarlos con la expresión `literal`. Por ejemplo:

::: zone pivot="programming-language-java-android"

```java
BubbleLayer layer = new BubbleLayer(source,
    bubbleColor(
        //Create a RGB color value.
        rgb(
            255f,  //Set red value to 255.

            150f,  //Set green value to 150.

            0f     //Set blue value to 0.
        )
    )
);
```

::: zone-end

::: zone pivot="programming-language-kotlin"

```kotlin
val layer = BubbleLayer(source,
    bubbleColor(
        //Create a RGB color value.
        rgb(
            255f,  //Set red value to 255.

            150f,  //Set green value to 150.

            0f     //Set blue value to 0.
        )
    )
)
```

::: zone-end

> [!TIP]
> Los valores de color de cadena se pueden convertir en un color mediante el método `android.graphics.Color.parseColor`. Lo siguiente convierte una cadena de color hexadecimal en una expresión de color que se puede usar con una capa.
>
> ```java
> color(parseColor("#ff00ff"))
> ```

## <a name="string-operator-expressions"></a>Expresiones de operador de cadena

Las expresiones de operador de cadena realizan operaciones de conversión sobre cadenas, como concatenación y conversión de las mayúsculas y minúsculas.

| Expression | Tipo de valor devuelto | Descripción |
|------------|-------------|-------------|
| `concat(string...)` \| `concat(Expression...)` | string | Concatena varias cadenas juntas. Cada valor debe ser una cadena. Use la expresión de tipo `toString` para convertir otros tipos de valor en cadena, si es necesario. |
| `downcase(string)` \| `downcase(Expression)` | string | Convierte la cadena especificada a minúsculas. |
| `isSupportedScript(string)` \| `isSupportedScript(Expression)`| boolean | Determina si la cadena de entrada utiliza un juego de caracteres admitido por la pila de fuentes actual. Por ejemplo: `isSupportedScript("ಗೌರವಾರ್ಥವಾಗಿ")` |
| `resolvedLocale(Expression collator)` | string | Devuelve la etiqueta de idioma IETF de la configuración regional utilizada por el intercalador proporcionado. Se puede usar para determinar la configuración regional predeterminada del sistema o para determinar si se ha cargado correctamente una configuración regional solicitada. |
| `upcase(string)` \| `upcase(Expression)` | string | Convierte la cadena especificada a mayúsculas. |

**Ejemplo**

En el ejemplo siguiente se convierte la propiedad `temperature` de la característica de punto en una cadena y, luego, se concatena "° F" al final de esta.

::: zone pivot="programming-language-java-android"

```java
SymbolLayer layer = new SymbolLayer(source,
    textField(
        concat(Expression.toString(get("temperature")), literal("°F"))
    ),

    //Some additional style options.
    textOffset(new Float[] { 0f, -1.5f }),
    textSize(12f),
    textColor("white")
);
```

::: zone-end

::: zone pivot="programming-language-kotlin"

```kotlin
val layer = SymbolLayer(source,
    textField(
        concat(Expression.toString(get("temperature")), literal("°F"))
    ),

    //Some additional style options.
    textOffset(new Float[] { 0f, -1.5f }),
    textSize(12f),
    textColor("white")
)
```

::: zone-end

La expresión anterior representa una chincheta en el mapa con el texto "64° F" superpuesto sobre ella, como se muestra en la imagen siguiente.

![Ejemplo de expresión de operador de cadena](media/how-to-expressions/string-operator-expression.png)

## <a name="interpolate-and-step-expressions&quot;></a>Expresiones de interpolación y paso

Se pueden usar para calcular valores a lo largo de una curva interpolada o una función de paso. Estas expresiones toman una expresión que devuelve un valor numérico como entrada, por ejemplo `get(&quot;temperature")`. El valor de entrada se evalúa con pares de valores de entrada y salida para determinar el valor que mejor se adapta a la curva interpolada o a la función de paso. Los valores de salida se denominan "paradas". Los valores de entrada de cada parada deben ser un número y estar en orden ascendente. Los valores de salida deben ser un número y una matriz de números, o un color.

### <a name="interpolate-expression"></a>Expresión de interpolación

Una expresión `interpolate` puede usarse para calcular un conjunto continuo y fluido de valores mediante la interpolación entre valores de parada. Una expresión `interpolate` que devuelve valores de color produce un degradado de color del que se seleccionan valores de resultados. La expresión `interpolate` tiene los siguientes formatos:

```java
//Stops consist of two expressions.
interpolate(Expression.Interpolator interpolation, Expression number, Expression... stops)

//Stop expression wraps two values.
interpolate(Expression.Interpolator interpolation, Expression number, Expression.Stop... stops)
```

Hay tres tipos de métodos de interpolación que se pueden usar en una expresión `interpolate`:

| Nombre | Descripción |
|------|-------------|
| `linear()` | Interpola linealmente entre el par de paradas.  |
| `exponential(number)` \| `exponential(Expression)` | Interpola exponencialmente entre las paradas. Se especifica una "base" y controla la velocidad a la que aumenta la salida. Los valores más altos hacen que aumente la salida más hacia el extremo superior del intervalo. Un valor de "base" cercano a 1 genera una salida que aumenta más linealmente.|
| `cubicBezier(number x1, number y1, number x2, number y2)` \| `cubicBezier(Expression x1, Expression y1, Expression x2, Expression y2)` | Interpola mediante una [curva Bézier cúbica](https://developer.mozilla.org/docs/Web/CSS/timing-function) definida por los puntos de control especificados. |

La expresión `stop` tiene el formato `stop(stop, value)`.

Este es un ejemplo del aspecto de estos diferentes tipos de interpolaciones.

| Lineal  | Exponencial | Bézier cúbica |
|---------|-------------|--------------|
| ![Gráfico de interpolación lineal](media/how-to-expressions/linear-interpolation.png) | ![Gráfico de interpolación exponencial](media/how-to-expressions/exponential-interpolation.png) | ![Gráfico de interpolación de Bézier cúbica](media/how-to-expressions/bezier-curve-interpolation.png) |

**Ejemplo**

En el ejemplo siguiente se usa una expresión `linear interpolate` para establecer la propiedad `bubbleColor` de una capa de burbuja según la propiedad `temperature` de la característica de punto. Si el valor de `temperature` es inferior a 60, se devolverá "blue". Si es mayor que 60 y menor que 70, se devolverá "yellow". Si es mayor que 70 y menor que 80, se devolverá "orange" (`#FFA500`). Si es 80 o superior, se devolverá "red".

::: zone pivot="programming-language-java-android"

```java
BubbleLayer layer = new BubbleLayer(source,
    bubbleColor(
        interpolate(
            linear(),
            get("temperature"),
            stop(50, color(Color.BLUE)),
            stop(60, color(Color.YELLOW)),
            stop(70, color(parseColor("#FFA500"))),
            stop(80, color(Color.RED))
        )
    )
);
```

::: zone-end

::: zone pivot="programming-language-kotlin"

```kotlin
val layer = BubbleLayer(source,
    bubbleColor(
        interpolate(
            linear(),
            get("temperature"),
            stop(50, color(Color.BLUE)),
            stop(60, color(Color.YELLOW)),
            stop(70, color(parseColor("#FFA500"))),
            stop(80, color(Color.RED))
        )
    )
)
```

::: zone-end

En la siguiente imagen se muestra cómo se eligen los colores de la expresión anterior.

![Ejemplo de expresión de interpolación](media/how-to-expressions/interpolate-expression-example.png)

### <a name="step-expression"></a>Expresión de paso

Una expresión `step` puede usarse para calcular valores de resultados escalonados discretos mediante la evaluación de una [función constante a trozos](http://mathworld.wolfram.com/PiecewiseConstantFunction.html) definida por paradas.

La expresión `interpolate` tiene los siguientes formatos:

```java
step(Expression input, Expression defaultOutput, Expression... stops)

step(Expression input, Expression defaultOutput, Expression.Stop... stops)

step(Expression input, number defaultOutput, Expression... stops)

step(Expression input, number defaultOutput, Expression.Stop... stops)

step(number input, Expression defaultOutput, Expression... stops)

step(number input, Expression defaultOutput, Expression.Stop... stops)

step(number input, number defaultOutput, Expression... stops)

step(number input, number defaultOutput, Expression.Stop... stops)
```

Las expresiones de paso devuelven el valor de salida de la parada justo antes del valor de entrada, o el primer valor de entrada si la entrada es menor que la primera parada.

**Ejemplo**

En el ejemplo siguiente se usa una expresión `step` para establecer la propiedad `bubbleColor` de una capa de burbuja según la propiedad `temperature` de la característica de punto. Si el valor de `temperature` es inferior a 60, se devolverá "blue". Si es mayor que 60 y menor que 70, se devolverá "yellow". Si es mayor que 70 y menor que 80, se devolverá "orange". Si es 80 o superior, se devolverá "red".

::: zone pivot="programming-language-java-android"

```java
BubbleLayer layer = new BubbleLayer(source,
    bubbleColor(
        step(
            get("temperature"),
            color(Color.BLUE),
            stop(60, color(Color.YELLOW)),
            stop(70, color(parseColor("#FFA500"))),
            stop(80, color(Color.RED))
        )
    )
);
```

::: zone-end

::: zone pivot="programming-language-kotlin"

```kotlin
val layer = BubbleLayer(source,
    bubbleColor(
        step(
            get("temperature"),
            color(Color.BLUE),
            stop(60, color(Color.YELLOW)),
            stop(70, color(parseColor("#FFA500"))),
            stop(80, color(Color.RED))
        )
    )
)
```

::: zone-end

En la siguiente imagen se muestra cómo se eligen los colores de la expresión anterior.

![Ejemplo de expresión de paso](media/how-to-expressions/step-expression-example.png)

## <a name="layer-specific-expressions"></a>Expresiones específicas de una capa

Expresiones especiales que solo se aplican a capas específicas.

### <a name="heat-map-density-expression"></a>Expresión de densidad de mapa térmico

Una expresión de densidad de mapa término recupera el valor de densidad de mapa término para cada píxel de una capa de mapa térmico y se define como `heatmapDensity`. Este valor es un número comprendido entre `0` y `1`. Se usa en combinación con una expresión `interpolation` o `step` para definir el degradado de color empleado para colorear el mapa térmico. Esta expresión solo se puede usar en la opción `heatmapColor` de la capa de mapa térmico.

> [!TIP]
> El color del índice 0 de una expresión de interpolación o el color predeterminado de un paso definen el color del área donde no hay ningún dato. El color del índice 0 se puede usar para definir el color de fondo. Muchos prefieren establecer este valor en transparente o en un negro semitransparente.

**Ejemplo**

En este ejemplo se usa una expresión de interpolación lineal para crear un degradado de color suave para representar el mapa térmico.

::: zone pivot="programming-language-java-android"

```java
HeatMapLayer layer = new HeatMapLayer(source,
    heatmapColor(
        interpolate(
            linear(),
            heatmapDensity(),
            stop(0, color(Color.TRANSPARENT)),
            stop(0.01, color(Color.MAGENTA)),
            stop(0.5, color(parseColor("#fb00fb"))),
            stop(1, color(parseColor("#00c3ff")))
        )
    )
);
```

::: zone-end

::: zone pivot="programming-language-kotlin"

```kotlin
val layer = HeatMapLayer(source,
    heatmapColor(
        interpolate(
            linear(),
            heatmapDensity(),
            stop(0, color(Color.TRANSPARENT)),
            stop(0.01, color(Color.MAGENTA)),
            stop(0.5, color(parseColor("#fb00fb"))),
            stop(1, color(parseColor("#00c3ff")))
        )
    )
)
```

::: zone-end

Además de usar un degradado suave para colorear un mapa térmico, se pueden especificar colores dentro de un conjunto de intervalos mediante una expresión `step`. El uso de una expresión `step` para colorear el mapa térmico separa visualmente la densidad en intervalos, lo que recuerda a un mapa de estilo de contorno o radar.

::: zone pivot="programming-language-java-android"

```java
HeatMapLayer layer = new HeatMapLayer(source,
    heatmapColor(
        step(
            heatmapDensity(),
            color(Color.TRANSPARENT),
            stop(0.01, color(parseColor("#000080"))),
            stop(0.25, color(parseColor("#000080"))),
            stop(0.5, color(Color.GREEN)),
            stop(0.5, color(Color.YELLOW)),
            stop(1, color(Color.RED))
        )
    )
);
```

::: zone-end

::: zone pivot="programming-language-kotlin"

```kotlin
val layer = HeatMapLayer(source,
    heatmapColor(
        step(
            heatmapDensity(),
            color(Color.TRANSPARENT),
            stop(0.01, color(parseColor("#000080"))),
            stop(0.25, color(parseColor("#000080"))),
            stop(0.5, color(Color.GREEN)),
            stop(0.5, color(Color.YELLOW)),
            stop(1, color(Color.RED))
        )
    )
)
```

::: zone-end

Para más información, consulte el artículo [Adición de una capa de mapa térmico](map-add-heat-map-layer-android.md).

### <a name="line-progress-expression"></a>Expresión de progreso lineal

Una expresión de progreso lineal recupera el progreso a lo largo de una línea de degradado en una capa de línea y se define como `lineProgress()`. Este valor es un número comprendido entre 0 y 1. Se usa en combinación con una expresión `interpolation` o `step`. Esta expresión solo puede usarse con la opción `strokeGradient` de la capa de línea.

> [!NOTE]
> La opción `strokeGradient` de la capa de línea requiere que la opción `lineMetrics` del origen de datos esté establecida en `true`.

**Ejemplo**

En este ejemplo, se usa la expresión `lineProgress()` para aplicar un degradado de color al trazo de una línea.

::: zone pivot="programming-language-java-android"

```java
LineLayer layer = new LineLayer(source,
    strokeGradient(
        interpolate(
            linear(),
            lineProgress(),
            stop(0, color(Color.BLUE)),
            stop(0.1, color(Color.argb(255, 65, 105, 225))), //Royal Blue
            stop(0.3, color(Color.CYAN)),
            stop(0.5, color(Color.argb(255,0, 255, 0))), //Lime
            stop(0.7, color(Color.YELLOW)),
            stop(1, color(Color.RED))
        )
    )
);
```

::: zone-end

::: zone pivot="programming-language-kotlin"

```kotlin
val layer = LineLayer(source,
    strokeGradient(
        interpolate(
            linear(),
            lineProgress(),
            stop(0, color(Color.BLUE)),
            stop(0.1, color(Color.argb(255, 65, 105, 225))), //Royal Blue
            stop(0.3, color(Color.CYAN)),
            stop(0.5, color(Color.argb(255,0, 255, 0))), //Lime
            stop(0.7, color(Color.YELLOW)),
            stop(1, color(Color.RED))
        )
    )
)
```

::: zone-end

[Vea un ejemplo en vivo](map-add-line-layer.md#line-stroke-gradient).

### <a name="text-field-format-expression"></a>Expresión de formato de campo de texto

La expresión `format` puede usarse con la opción `textField` de la capa de símbolos para proporcionar formato de texto mixto. Esta expresión adopta una o varias expresiones `formatEntry` que especifican una cadena y un conjunto de `formatOptions` que se van a anexar al campo de texto.

| Expresión | Descripción |
|------------|-------------|
| `format(Expression...)` | Devuelve texto con formato que contiene anotaciones para su uso en entradas de campos de texto con formato mixto. |
| `formatEntry(Expression text)` \| `formatEntry(Expression text, Expression.FormatOption... formatOptions)` \| `formatEntry(String text)` \| `formatEntry(String text, Expression.FormatOption... formatOptions)` | Devuelve una entrada de cadena con formato para su uso en la expresión `format`. |

Las siguientes opciones de formato disponibles son:

| Expresión | Descripción |
|------------|-------------|
| `formatFontScale(number)` \| `formatFontScale(Expression)` | Especifica el factor de escala del tamaño de fuente. Si se especifica, este valor invalidará la propiedad `textSize` para la cadena individual. |
| `formatTextFont(string[])` \| `formatTextFont(Expression)` | Especifica el color que se va a aplicar a un texto al representarlo. |

**Ejemplo**

En el ejemplo siguiente se aplica formato al campo de texto, para lo cual se agrega una fuente negrita y se amplía el tamaño de fuente de la propiedad `title` de la característica. En este ejemplo también se agrega la propiedad `subTitle` de la característica en una nueva línea, con un tamaño de fuente reducido.

::: zone pivot="programming-language-java-android"

```java
SymbolLayer layer = new SymbolLayer(source,
    textField(
        format(
            //Bold the title property and scale its font size up.
            formatEntry(
                get("title"),
                formatTextFont(new String[] { "StandardFont-Bold" }),
                formatFontScale(1.25)),

            //Add a new line without any formatting.
            formatEntry("\n"),

            //Scale the font size down of the subTitle property.
            formatEntry(
                get("subTitle"),
                formatFontScale(0.75))
        )
    )
);
```

::: zone-end

::: zone pivot="programming-language-kotlin"

```kotlin
val layer = SymbolLayer(source,
    textField(
        format(
            //Bold the title property and scale its font size up.
            formatEntry(
                get("title"),
                formatTextFont(arrayOf("StandardFont-Bold")),
                formatFontScale(1.25)),

            //Add a new line without any formatting.
            formatEntry("\n"),

            //Scale the font size down of the subTitle property.
            formatEntry(
                get("subTitle"),
                formatFontScale(0.75))
        )
    )
)
```

::: zone-end

Esta capa representará la característica punto, tal como se muestra en la imagen siguiente:

![Imagen de la característica de punto con el campo de texto con formato](media/how-to-expressions/text-field-format-expression.png)

## <a name="zoom-expression"></a>Expresión de zoom

Una expresión `zoom` se usa para recuperar el nivel de zoom actual del mapa e tiempo de representación y se define como `zoom()`. Esta expresión devuelve un número entre el intervalo de nivel de zoom mínimo y máximo del mapa. Los controles de mapa interactivo de Azure Maps para web y Android admiten 25 niveles de zoom, numerados de 0 a 24. El uso de la expresión `zoom` permite modificar dinámicamente los estilos a medida que cambia el nivel de zoom del mapa. La expresión `zoom` solo puede usarse con expresiones `interpolate` y `step`.

**Ejemplo**

De forma predeterminada, los radios de los puntos de datos representados en la capa del mapa térmico tienen un radio de píxel fijo para todos los niveles de zoom. A medida que se amplía el mapa, los datos se agregan juntos y la capa de mapa térmico parece diferente. Una expresión `zoom` puede usarse para ampliar el radio de cada nivel de zoom de forma que cada punto de datos cubra el mismo área físico del mapa. Esto hará que la capa del mapa térmico parezca más estática y coherente. Cada nivel de zoom del mapa tiene dos veces tantos píxeles vertical y horizontalmente que nivel de zoom anterior. Si se amplía el radio de forma que se duplique con cada nivel de zoom, se creará un mapa térmico que parecerá coherente en todos los niveles de zoom. Puede realizarse mediante la expresión `zoom` con una expresión `base 2 exponential interpolation`, con el radio de píxel establecido para el nivel mínimo de zoom y un radio escalado para el nivel máximo de zoom calculado como `2 * Math.pow(2, minZoom - maxZoom)`, tal como se muestra a continuación.

::: zone pivot="programming-language-java-android"

```java
HeatMapLayer layer = new HeatMapLayer(source,
    heatmapRadius(
        interpolate(
            exponential(2),
            zoom(),

            //For zoom level 1 set the radius to 2 pixels.
            stop(1, 2),

            //Between zoom level 1 and 19, exponentially scale the radius from 2 pixels to 2 * (maxZoom - minZoom)^2 pixels.
            stop(19, 2 * Math.pow(2, 19 - 1))
        )
    )
);
```

::: zone-end

::: zone pivot="programming-language-kotlin"

```kotlin
val layer = HeatMapLayer(source,
    heatmapRadius(
        interpolate(
            exponential(2),
            zoom(),

            //For zoom level 1 set the radius to 2 pixels.
            stop(1, 2),

            //Between zoom level 1 and 19, exponentially scale the radius from 2 pixels to 2 * (maxZoom - minZoom)^2 pixels.
            stop(19, 2 * Math.pow(2, 19 - 1))
        )
    )
)
```

::: zone-end

## <a name="variable-binding-expressions"></a>Expresiones de enlace de variable

Las expresiones de enlace de variables almacenan los resultados de un cálculo en una variable. De ese modo, se pueden incluir varias veces referencias a esos resultados de cálculo en cualquier parte de una expresión. Esta optimización resulta muy útil para las expresiones en las que intervienen muchos cálculos.

| Expression | Tipo de valor devuelto | Descripción |
|--------------|---------------|--------------|
| `let(Expression... input)` | | Almacena uno o más valores como variables para su uso por la expresión `var` en la expresión secundaria que devuelve el resultado. |
| `var(Expression expression)` \| `var(string variableName)` | Object | Hace referencia a una variable que se creó con la expresión `let`. |

**Ejemplo**

En este ejemplo se usa una expresión que calcula los ingresos relativos a la proporción de temperatura y, luego, usa una expresión `case` para evaluar diferentes operaciones booleanas sobre este valor. La expresión `let` se usa para almacenar los ingresos en relación con el índice de temperatura, de modo que solo debe calcularse una vez. La expresión `var` hace referencia a esta variable tantas veces como sean necesarias sin necesidad de volver a calcularla.

::: zone pivot="programming-language-java-android"

```java
BubbleLayer layer = new BubbleLayer(source,
    bubbleColor(           
        let(
            //Divide the point features `revenue` property by the `temperature` property and store it in a variable called `ratio`.
            literal("ratio"), division(get("revenue"), get("temperature")),

            //Evaluate the child expression in which the stored variable will be used.
            switchCase(
                //Check to see if the ratio is less than 100, return 'red'.
                lt(var("ratio"), 100), color(Color.RED),

                //Check to see if the ratio is less than 200, return 'green'.
                lt(var("ratio"), 200), color(Color.GREEN),

                //Return `blue` for values greater or equal to 200.
                color(Color.BLUE)
            )
        )
    )
);
```

::: zone-end

::: zone pivot="programming-language-kotlin"

```kotlin
val layer = BubbleLayer(source,
    bubbleColor(           
        let(
            //Divide the point features `revenue` property by the `temperature` property and store it in a variable called `ratio`.
            literal("ratio"), division(get("revenue"), get("temperature")),

            //Evaluate the child expression in which the stored variable will be used.
            switchCase(
                //Check to see if the ratio is less than 100, return 'red'.
                lt(var("ratio"), 100), color(Color.RED),

                //Check to see if the ratio is less than 200, return 'green'.
                lt(var("ratio"), 200), color(Color.GREEN),

                //Return `blue` for values greater or equal to 200.
                color(Color.BLUE)
            )
        )
    )
)
```

::: zone-end

## <a name="next-steps"></a>Pasos siguientes

Más información sobre las capas que admiten expresiones:

> [!div class="nextstepaction"]
> [Adición de una capa de símbolo](how-to-add-symbol-to-android-map.md)

> [!div class="nextstepaction"]
> [Adición de una capa de burbuja](map-add-bubble-layer-android.md)

> [!div class="nextstepaction"]
> [Adición de una capa de línea](android-map-add-line-layer.md)

> [!div class="nextstepaction"]
> [Adición de una capa de polígono](how-to-add-shapes-to-android-map.md)

> [!div class="nextstepaction"]
> [Adición de un mapa térmico](map-add-heat-map-layer-android.md)
