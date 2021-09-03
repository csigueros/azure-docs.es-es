---
title: Aptitud cognitiva condicional
titleSuffix: Azure Cognitive Search
description: La aptitud condicional en Búsqueda cognitiva de Azure habilita el filtrado, la creación de valores predeterminados y la combinación de valores en una definición del conjunto de aptitudes.
author: LiamCavanagh
ms.author: liamca
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 08/12/2021
ms.openlocfilehash: acd3a42051fa42821013424ab0410814968b6131
ms.sourcegitcommit: 6c6b8ba688a7cc699b68615c92adb550fbd0610f
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/13/2021
ms.locfileid: "121860565"
---
# <a name="conditional-cognitive-skill"></a>Aptitud cognitiva condicional

La aptitud **condicional** habilita los escenarios de Búsqueda cognitiva de Azure que requieren una operación booleana para determinar los datos que deben asignarse a una salida. Estos escenarios incluyen el filtrado, la asignación de un valor predeterminado y la combinación de datos según una condición.

El siguiente pseudocódigo muestra lo que lleva a cabo la aptitud condicional:

```
if (condition) 
    { output = whenTrue } 
else 
    { output = whenFalse } 
```

> [!NOTE]
> Esta capacidad no está enlazada a Cognitive Services. No es facturable y no tiene ningún requisito de clave de Cognitive Services.

## <a name="odatatype"></a>@odata.type  
Microsoft.Skills.Util.ConditionalSkill


## <a name="evaluated-fields"></a>Campos evaluados

Esta aptitud es especial porque sus entradas son campos evaluados.

Los elementos siguientes son valores válidos de una expresión:

-   Las rutas de anotación (las rutas en las expresiones deben delimitarse con "$(" and ")")
 <br/>
    Ejemplos:
    ```
        "= $(/document)"
        "= $(/document/content)"
    ```

-  Literales (cadenas, números, true, false, null) <br/>
    Ejemplos:
    ```
       "= 'this is a string'"   // string (note the single quotation marks)
       "= 34"                   // number
       "= true"                 // Boolean
       "= null"                 // null value
    ```

-  Expresiones que utilizan operadores de comparación (==, !=, >=, >, <=, <) <br/>
    Ejemplos:
    ```
        "= $(/document/language) == 'en'"
        "= $(/document/sentiment) >= 0.5"
    ```

-   Expresiones que usan operadores booleanos (&&, ||, !, ^) <br/>
    Ejemplos:
    ```
        "= $(/document/language) == 'en' && $(/document/sentiment) > 0.5"
        "= !true"
    ```

-   Expresiones que usan operadores numéricos (+, -, \*, /, %) <br/>
    Ejemplos: 
    ```
        "= $(/document/sentiment) + 0.5"         // addition
        "= $(/document/totalValue) * 1.10"       // multiplication
        "= $(/document/lengthInMeters) / 0.3049" // division
    ```

Dado que la aptitud condicional admite la evaluación, puede usarla en escenarios de transformaciones menores. Por ejemplo, consulte la [definición 4 de la aptitud](#transformation-example).

## <a name="skill-inputs"></a>Entradas de la aptitud
Las entradas distinguen mayúsculas de minúsculas.

| Entrada   | Descripción |
|-------------|-------------|
| condición   | Esta entrada es un [campo evaluado](#evaluated-fields) que representa la condición que se va a evaluar. Esta condición debe evaluarse con un valor booleano (*true* o *false*).   <br/>  Ejemplos: <br/> "= true" <br/> "= $(/document/language) =='fr'" <br/> "= $(/document/pages/\*/language) == $(/document/expectedLanguage)" <br/> |
| whenTrue    | Esta entrada es un [campo evaluado](#evaluated-fields) que representa el valor que se devolverá si la condición se evalúa en *true*. Las cadenas de constantes deben devolverse entre comillas simples (' y '). <br/>Valores de ejemplo: <br/> "= 'contract'"<br/>"= $(/document/contractType)" <br/> "= $(/document/entities/\*)" <br/> |
| whenFalse   | Esta entrada es un [campo evaluado](#evaluated-fields) que representa el valor que se devolverá si la condición se evalúa en *false*. <br/>Valores de ejemplo: <br/> "= 'contract'"<br/>"= $(/document/contractType)" <br/> "= $(/document/entities/\*)" <br/>

## <a name="skill-outputs"></a>Salidas de la aptitud
Hay una única salida que se denomina simplemente "salida". Devuelve el valor *whenFalse* si la condición es false o *whenTrue* si la condición es true.

## <a name="examples"></a>Ejemplos

### <a name="sample-skill-definition-1-filter-documents-to-return-only-french-documents"></a>Ejemplo de definición de aptitud 1: filtrar documentos para devolver solo documentos en francés

La salida siguiente devuelve una matriz de oraciones ("/document/frenchSentences") si el idioma del documento es el francés. Si el idioma no es el francés, el valor se establece en *null*.

```json
{
    "@odata.type": "#Microsoft.Skills.Util.ConditionalSkill",
    "context": "/document",
    "inputs": [
        { "name": "condition", "source": "= $(/document/language) == 'fr'" },
        { "name": "whenTrue", "source": "/document/sentences" },
        { "name": "whenFalse", "source": "= null" }
    ],
    "outputs": [ { "name": "output", "targetName": "frenchSentences" } ]
}
```
Si se usa "/document/frenchSentences" como *contexto* de otra aptitud, dicha aptitud se ejecuta solo si "/document/frenchSentences" no está establecido en *null*.


### <a name="sample-skill-definition-2-set-a-default-value-for-a-value-that-doesnt-exist"></a>Ejemplo de definición de aptitud 2: establecer un valor predeterminado para un valor que no existe

La salida siguiente crea una anotación ("/document/languageWithDefault") que se establece en el idioma del documento o en "es" si no se establece el idioma.

```json
{
    "@odata.type": "#Microsoft.Skills.Util.ConditionalSkill",
    "context": "/document",
    "inputs": [
        { "name": "condition", "source": "= $(/document/language) == null" },
        { "name": "whenTrue", "source": "= 'es'" },
        { "name": "whenFalse", "source": "= $(/document/language)" }
    ],
    "outputs": [ { "name": "output", "targetName": "languageWithDefault" } ]
}
```

### <a name="sample-skill-definition-3-merge-values-from-two-fields-into-one"></a>Ejemplo de definición de aptitud 3: combinar valores de dos campos en uno

En este ejemplo, algunas oraciones tienen una propiedad *frenchSentiment*. Cada vez que la propiedad *frenchSentiment* sea null, querremos usar el valor *englishSentiment*. Asignamos la salida a un miembro llamado *sentiment* ("/document/sentences/*/sentiment").

```json
{
    "@odata.type": "#Microsoft.Skills.Util.ConditionalSkill",
    "context": "/document/sentences/*",
    "inputs": [
        { "name": "condition", "source": "= $(/document/sentences/*/frenchSentiment) == null" },
        { "name": "whenTrue", "source": "/document/sentences/*/englishSentiment" },
        { "name": "whenFalse", "source": "/document/sentences/*/frenchSentiment" }
    ],
    "outputs": [ { "name": "output", "targetName": "sentiment" } ]
}
```

## <a name="transformation-example"></a>Ejemplo de transformación
### <a name="sample-skill-definition-4-data-transformation-on-a-single-field"></a>Ejemplo de definición de aptitud 4: transformación de datos en un único campo

En este ejemplo, recibimos un *sentiment* que está entre 0 y 1. Queremos transformarlo para que esté entre -1 y 1. Podemos usar la aptitud condicional para realizar esta transformación menor.

En este ejemplo, no usamos el aspecto condicional de la aptitud porque la condición siempre es *true*.

```json
{
    "@odata.type": "#Microsoft.Skills.Util.ConditionalSkill",
    "context": "/document/sentences/*",
    "inputs": [
        { "name": "condition", "source": "= true" },
        { "name": "whenTrue", "source": "= $(/document/sentences/*/sentiment) * 2 - 1" },
        { "name": "whenFalse", "source": "= 0" }
    ],
    "outputs": [ { "name": "output", "targetName": "normalizedSentiment" } ]
}
```

## <a name="special-considerations"></a>Consideraciones especiales
Algunos parámetros se evalúan, por lo que deberá tener mucho cuidado de seguir el patrón documentado. Las expresiones deben comenzar con un signo igual. Una ruta debe estar delimitada por "$(" and ")". Asegúrese de poner las cadenas entre comillas simples. Esto permite al evaluador distinguir entre las cadenas, por un lado, y las rutas y los operadores, por el otro. Además, asegúrese de colocar un espacio en blanco alrededor de los operadores (por ejemplo, un "*" en una ruta significa algo distinto a multiplicar).


## <a name="next-steps"></a>Pasos siguientes

+ [Aptitudes integradas](cognitive-search-predefined-skills.md)
+ [Definición de un conjunto de aptitudes](cognitive-search-defining-skillset.md)
