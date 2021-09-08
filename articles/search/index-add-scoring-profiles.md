---
title: Adición de perfiles de puntuación para aumentar las puntuaciones de búsqueda
titleSuffix: Azure Cognitive Search
description: Potencie las puntuaciones de relevancia de búsqueda en los resultados de Azure Cognitive Search mediante la incorporación de perfiles de puntuación a un índice de búsqueda.
manager: nitinme
author: shmed
ms.author: ramero
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 06/16/2021
ms.openlocfilehash: 186fa5fe490541ce465eca37cf21a6a63923e2ee
ms.sourcegitcommit: 6a3096e92c5ae2540f2b3fe040bd18b70aa257ae
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 06/17/2021
ms.locfileid: "112321889"
---
# <a name="add-scoring-profiles-to-a-search-index"></a>Incorporación de perfiles de puntuación a un índice de búsqueda

Para las consultas de búsqueda de texto completo, el motor de búsqueda calcula una puntuación de búsqueda para cada documento correspondiente, lo que permite clasificar los resultados de alto a bajo. Azure Cognitive Search usa un algoritmo de puntuación predeterminado para calcular una puntuación inicial, pero puede personalizar el cálculo mediante un *perfil de puntuación*.

Los perfiles de puntuación se insertan en definiciones de índice e incluyen propiedades para aumentar la puntuación de coincidencias, donde los criterios adicionales encontrados en el perfil proporcionan la lógica de potenciación. Por ejemplo, es posible que quiera aumentar las coincidencias según su potencial de ingresos, promover elementos más recientes o quizás aumentar los que han permanecido en el inventario demasiado tiempo.  

¿No está familiarizado con los conceptos de relevancia? El siguiente segmento de vídeo avanza a la parte donde se explica el funcionamiento de los perfiles de puntuación en Azure Cognitive Search, pero también se describen conceptos básicos. También puede revisar [Clasificación y puntuación de similitud](index-similarity-and-scoring.md) para obtener más información.

> [!VIDEO https://www.youtube.com/embed/Y_X6USgvB1g?version=3&start=463&end=970]

## <a name="what-is-a-scoring-profile"></a>¿Qué es un perfil de puntuación?

Un perfil de puntuación es parte de la definición del índice que se compone de campos, funciones y parámetros ponderados. El propósito de un perfil de puntuación es aumentar o ampliar los documentos coincidentes en función de los criterios que proporcione. 

En la definición siguiente se muestra un perfil simple denominado "geo". Potencia los resultados que tengan el término de búsqueda en el campo hotelName. También usa la función `distance` para dar prioridad a los resultados que están a menos de 10 kilómetros de la ubicación actual. Si alguien busca el término "inn" y este término forma parte del nombre del hotel, los documentos que incluyan hoteles con "inn" en un radio de 10 km de la ubicación actual aparecerán primero en los resultados de búsqueda.  

```json
"scoringProfiles": [
  {  
    "name":"geo",
    "text": {  
      "weights": {  
        "hotelName": 5
      }                              
    },
    "functions": [
      {  
        "type": "distance",
        "boost": 5,
        "fieldName": "location",
        "interpolation": "logarithmic",
        "distance": {
          "referencePointParameter": "currentLocation",
          "boostingDistance": 10
        }                        
      }                                      
    ]                     
  }            
]
```  

Para usar este perfil de puntuación, la consulta se formula para especificar el parámetro scoringProfile en la solicitud.

```http
POST /indexes/hotels/docs&api-version=2020-06-30
{
    "search": "inn",
    "scoringProfile": "geo",
    "scoringParameter": currentLocation--122.123,44.77233
}
```  

Esta consulta busca el término "inn" y pasa la ubicación actual. Tenga en cuenta que esta consulta incluye otros parámetros, como scoringParameter. Los parámetros de consulta se describen en [Buscar documentos (API REST)](/rest/api/searchservice/Search-Documents).  

Vea el [Ejemplo ampliado](#bkmk_ex) para revisar un ejemplo más detallado de un perfil de puntuación.  

<a name=what-is-default-scoring></a>

## <a name="how-scores-are-computed"></a>Procedimiento para calcular las puntuaciones

Las puntuaciones se calculan para las consultas de búsqueda de texto completo, con el fin de clasificar las coincidencias más relevantes y devolverlas en la parte superior de la respuesta. La puntuación general de cada documento es una agregación de las puntuaciones individuales de cada campo, donde la puntuación individual de cada campo se calcula en función de la frecuencia del término y la frecuencia del documento de los términos buscados dentro de ese campo (conocido como [TF-IDF](https://en.wikipedia.org/wiki/Tf%E2%80%93idf) o frecuencia de términos-frecuencia de documento inversa). 

> [!Tip]
> Puede usar el parámetro [featuresMode](index-similarity-and-scoring.md#featuresmode-parameter-preview) para solicitar detalles de puntuación adicionales con los resultados de la búsqueda (incluidas las puntuaciones de nivel de campo).

## <a name="when-to-add-scoring-logic"></a>Cuándo agregar lógica de puntuación

Debe crear uno o más perfiles de puntuación cuando el comportamiento de clasificación predeterminado no logre cumplir los objetivos de su empresa. Por ejemplo, podría decidir que la relevancia de la búsqueda debe favorecer a los elementos recién agregados. Asimismo, podría tener un campo que contenga el margen de beneficio, o algún otro campo que indique los ingresos potenciales. El aumento de los resultados que son más significativos para los usuarios o la empresa suele ser el factor determinante en la adopción de perfiles de puntuación.

El orden basado en relevancia de una página de búsqueda también se implementa por medio de perfiles de puntuación. Tenga en cuenta los resultados de búsqueda que ha usado en el pasado que le permiten ordenar por relevancia, fecha, clasificación o precio. En Azure Cognitive Search, los perfiles de puntuación se pueden usar para determinar la opción de "relevancia". La definición de relevancia está controlada por usted, se afirma en los objetivos empresariales y en el tipo de experiencia de búsqueda que desee ofrecer.  

<a name="bkmk_ex"></a>

## <a name="extended-example"></a>Ejemplo extendido

En el ejemplo siguiente se muestra el esquema de un índice con dos perfiles de puntuación (`boostGenre`, `newAndHighlyRated`). Las consultas sobre este índice que incluyen cualquiera de los perfiles como parámetro de consulta usará el perfil para puntuar el conjunto de resultados. 

El perfil `boostGenre` usa campos de texto ponderados, lo que aumenta las coincidencias que se encuentran en los campos albumTitle, genre y artistName. Los campos se aumentan en 1,5, 5 y 2 respectivamente. ¿Por qué se aumenta el género mucho mayor que los demás? Si la búsqueda se realiza sobre datos en cierto modo homogéneos (como es el caso de "genre" en musicstoreindex), es posible que necesite una mayor varianza en los pesos relativos. Por ejemplo, en musicstoreindex, "rock" aparece como género y en las descripciones de género expresadas de forma idéntica. Si desea que el género supere en ponderación a la descripción del género, el campo del género necesitará una ponderación relativa mucho mayor.

```json
{  
  "name": "musicstoreindex",  
  "fields": [  
    { "name": "key", "type": "Edm.String", "key": true },  
    { "name": "albumTitle", "type": "Edm.String" },  
    { "name": "albumUrl", "type": "Edm.String", "filterable": false },  
    { "name": "genre", "type": "Edm.String" },  
    { "name": "genreDescription", "type": "Edm.String", "filterable": false },  
    { "name": "artistName", "type": "Edm.String" },  
    { "name": "orderableOnline", "type": "Edm.Boolean" },  
    { "name": "rating", "type": "Edm.Int32" },  
    { "name": "tags", "type": "Collection(Edm.String)" },  
    { "name": "price", "type": "Edm.Double", "filterable": false },  
    { "name": "margin", "type": "Edm.Int32", "retrievable": false },  
    { "name": "inventory", "type": "Edm.Int32" },  
    { "name": "lastUpdated", "type": "Edm.DateTimeOffset" }  
  ],  
  "scoringProfiles": [  
    {  
      "name": "boostGenre",  
      "text": {  
        "weights": {  
          "albumTitle": 1.5,  
          "genre": 5,  
          "artistName": 2  
        }  
      }  
    },  
    {  
      "name": "newAndHighlyRated",  
      "functions": [  
        {  
          "type": "freshness",  
          "fieldName": "lastUpdated",  
          "boost": 10,  
          "interpolation": "quadratic",  
          "freshness": {  
            "boostingDuration": "P365D"  
          }  
        },  
        {
          "type": "magnitude",  
          "fieldName": "rating",  
          "boost": 10,  
          "interpolation": "linear",  
          "magnitude": {  
            "boostingRangeStart": 1,  
            "boostingRangeEnd": 5,  
            "constantBoostBeyondRange": false  
          }  
        }  
      ]  
    }  
  ],  
  "suggesters": [  
    {  
      "name": "sg",  
      "searchMode": "analyzingInfixMatching",  
      "sourceFields": [ "albumTitle", "artistName" ]  
    }  
  ]   
}  
```  

## <a name="steps-for-adding-a-scoring-profile"></a>Pasos para agregar un perfil de puntuación

Para implementar el comportamiento personalizado de puntuación, agregue un perfil de puntuación al esquema que define el índice. Puede tener hasta 100 perfiles de puntuación en un índice (consulte [Límites de servicio](search-limits-quotas-capacity.md)), pero solo puede especificar un perfil cada vez en una consulta concreta.

1. Comience con una definición de índice. Puede agregar y actualizar perfiles de puntuación en un índice existente sin tener que volver a generarlo. Use una solicitud [Actualizar índice](/rest/api/searchservice/update-index) para publicar la revisión.

1. Pegue la [Plantilla](#bkmk_template) incluida en este tema.  

1. Proporcione un nombre. Los perfiles de puntuación son opcionales, pero si agrega uno, se requerirá el nombre. Asegúrese de seguir las [convenciones de nomenclatura](/rest/api/searchservice/naming-rules) de Cognitive Search para los campos (se empieza con una letra y se evitan caracteres especiales y palabras reservadas).  

1. Especifique los criterios de aumento. Un único perfil puede contener [campos ponderados](#weighted-fields), [funciones](#functions) o ambos. 

Debe trabajar de forma iterativa con un conjunto de datos que ayude a demostrar o desaprobar la eficacia de un perfil determinado.

Los perfiles de puntuación se pueden definir en Azure Portal como se muestra en la captura de pantalla siguiente, mediante programación por medio de [API REST](/rest/api/searchservice/update-index), o bien en los SDK de Azure, como la clase [ScoringProfile](/dotnet/api/azure.search.documents.indexes.models.scoringprofile) del SDK de Azure para .NET.

   :::image type="content" source="media/scoring-profiles/portal-add-scoring-profile-small.png" alt-text="Adición de una página de perfil de puntuación" lightbox="media/scoring-profiles/portal-add-scoring-profile.png" border="true":::

<a name="weighted-fields"></a>

### <a name="using-weighted-fields"></a>Uso de campos ponderados

Use campos ponderados cuando el contexto de campo sea importante y las consultas sean búsquedas de texto completo (también conocidas como búsqueda de texto de forma libre). Por ejemplo, si una consulta incluye el término "airport", es posible que quiera que "airport" en el campo Description tenga más peso que en HotelName. 

Los campos ponderados están formados por un campo que permite búsquedas y un número positivo que se usa como multiplicador. Si la puntuación de campo original de HotelName es 3, la puntuación aumentada para ese campo se convierte en 6, lo que contribuye a una puntuación general más alta para el propio documento primario.

```json
"scoringProfiles": [  
{  
  "name": "boostKeywords",  
  "text": {  
    "weights": {  
      "HotelName": 2,  
      "Description": 5 
    }  
  }  
}
```

<a name="functions"></a>

### <a name="using-functions"></a>Uso de funciones

Use funciones cuando las ponderaciones relativas simples sean insuficientes o no se apliquen, como en el caso de la distancia y la actualización, que son cálculos sobre datos numéricos. Puede especificar varias funciones por perfil de puntuación.

| Función | Descripción |
|-|-|
| "freshness" | Aumenta por valores de un campo datetime (Edm.DataTimeOffset). Esta función tiene un atributo `boostingDuration` para que pueda especificar un valor que represente un intervalo de tiempo durante el que se produce el aumento. | 
| "magnitude" | Realiza el aumento en función de si un valor numérico es alto o bajo. Entre los escenarios que requieren esta función se incluyen aumentar por margen de beneficio, precio máximo, precio mínimo o recuento de descargas. Esta función solo se puede usar con campos Edm.Double y Edm.Int. Para la función magnitude, puede invertir el rango, de mayor a menor, si quiere el patrón inverso (por ejemplo, aumentar los artículos de precio inferior más que los de precio superior). Dado un intervalo de precios de 100 a 1 USD, tendría que establecer "boostingRangeStart" en 100 y "boostingRangeEnd" en 1 para aumentar los artículos con precios más bajos. | 
| "distance"  | Realiza el aumento por proximidad o ubicación geográfica. Esta función solo se puede usar con campos Edm.GeographyPoint. | 
| "tag"  | Realiza el aumento por las etiquetas comunes a los documentos de búsqueda y las cadenas de consulta. Las etiquetas se proporcionan en un objeto `tagsParameter`. Esta función solo se puede usar con campos Edm.String y Collection(Edm.String). | 

### <a name="rules-for-using-functions"></a>Reglas de uso de funciones

+ Las funciones solo se pueden aplicar a campos con atributos filtrables.
+ El tipo de la función ("freshness", "magnitude", "distance", "tag") debe estar en minúsculas.
+ Las funciones no pueden incluir valores nulos ni estar vacías.

<a name="bkmk_template"></a>

## <a name="template"></a>Plantilla

 En esta sección se muestra la sintaxis y la plantilla de perfiles de puntuación. Consulte [Referencia de propiedades](#bkmk_indexref) en la sección siguiente para ver las descripciones de los atributos de los perfiles de puntuación.  

```json
"scoringProfiles": [  
  {   
    "name": "name of scoring profile",   
    "text": (optional, only applies to searchable fields) {   
      "weights": {   
        "searchable_field_name": relative_weight_value (positive #'s),   
        ...   
      }   
    },   
    "functions": (optional) [  
      {   
        "type": "magnitude | freshness | distance | tag",   
        "boost": # (positive number used as multiplier for raw score != 1),   
        "fieldName": "(...)",   
        "interpolation": "constant | linear (default) | quadratic | logarithmic",   

        "magnitude": {
          "boostingRangeStart": #,   
          "boostingRangeEnd": #,   
          "constantBoostBeyondRange": true | false (default)
        }  

        // ( - or -)  

        "freshness": {
          "boostingDuration": "..." (value representing timespan over which boosting occurs)   
        }  

        // ( - or -)  

        "distance": {
          "referencePointParameter": "...", (parameter to be passed in queries to use as reference location)   
          "boostingDistance": # (the distance in kilometers from the reference location where the boosting range ends)   
        }   

        // ( - or -)  

        "tag": {
          "tagsParameter":  "..."(parameter to be passed in queries to specify a list of tags to compare against target field)   
        }
      }
    ],   
    "functionAggregation": (optional, applies only when functions are specified) "sum (default) | average | minimum | maximum | firstMatching"   
  }   
],   
"defaultScoringProfile": (optional) "...", 
```  

<a name="bkmk_indexref"></a> 

## <a name="property-reference"></a>Referencia de propiedades

|Atributo|Descripción|  
|---------------|-----------------|  
| name | Necesario. Este es el nombre del perfil de puntuación. Sigue las mismas convenciones de nomenclatura de un campo. Debe comenzar por una letra, no puede contener puntos, dos puntos o símbolos @, y no puede comenzar con la frase "azureSearch" (distingue mayúsculas de minúsculas).|  
| texto | Contiene la propiedad weights.|  
| weights | Opcional. Pares nombre-valor que especifican un campo que permite búsquedas y un número entero positivo o de punto flotante por el que se va a aumentar la puntuación de un campo. El número o entero positivo se convierte en un multiplicador para la puntuación de campo original generada por el algoritmo de clasificación. Por ejemplo, si una puntuación de campo es 2 y el valor de ponderación es 3, la puntuación aumentada para el campo se convierte en 6. Después, se agregan puntuaciones de campo individuales para crear una puntuación de campo de documento, que luego se usa para clasificar el documento en el conjunto de resultados. |  
| functions | Opcional. Una función de puntuación solo puede aplicarse a campos filtrables.|  
| functions > type | Obligatorio para las funciones de puntuación. Indica el tipo de función que se usará. Entre los valores válidos se incluyen magnitud, índice de actualización, distancia y etiqueta. Puede incluir más de una función en cada perfil de puntuación. El nombre de la función debe estar en minúsculas.|  
| functions > boost | Obligatorio para las funciones de puntuación. Número positivo usado como multiplicador para el resultado sin formato. No puede ser igual a 1.|  
| functions > fieldname | Obligatorio para las funciones de puntuación. Una función de puntuación solo puede aplicarse a los campos que forman parte de la colección de campos del índice y que son filtrables. Además, cada tipo de función introduce restricciones adicionales (índice de actualización se usa con campos de fecha y hora, magnitud con número entero o campos dobles y distancia con campos de ubicación). Solo puede especificar un campo único por cada definición de función. Por ejemplo, para usar magnitud dos veces en el mismo perfil, necesitaría incluir dos magnitudes de definiciones, una para cada campo.|  
| functions > interpolation | Obligatorio para las funciones de puntuación. Define la pendiente con la que la potenciación de la puntuación aumenta desde el inicio del rango hasta su final. Entre los valores válidos se incluyen Lineal (valor predeterminado), Constante, Cuadrática y Logarítmico. Consulte [Establecer interpolaciones](#bkmk_interpolation) para más información.|  
| functions > magnitude | La función de puntuación de la magnitud se usa para modificar las clasificaciones según el intervalo de valores de un campo numérico. Algunos de los ejemplos de uso más habituales sobre esto son: </br></br>"Clasificaciones por estrellas": se modifica la puntuación en función del valor del campo "Clasificación por estrellas". Cuando dos elementos son pertinentes, en primer lugar se mostrará el elemento con la clasificación superior. </br>"Margen": cuando dos documentos son relevantes, es posible que un distribuidor aumente primero los que tengan mayores márgenes. </br>"Recuentos de clics": en las aplicaciones que hacen un seguimiento de los clics en productos o páginas, puede usar la magnitud para aumentar los elementos que tienden a recibir el máximo tráfico. </br>"Recuentos de descargas": para las aplicaciones que hacen el seguimiento de descargas, la función de magnitud permite aumentar los elementos que tienen más descargas.|  
| functions > magnitude > boostingRangeStart | Establece el valor inicial del intervalo en el que se puntúa la magnitud. El valor debe ser un número entero o un número de punto flotante. En cuanto a las clasificaciones por estrellas de 1 a 4, esto equivaldría a 1. Para los márgenes superiores al 50%, esto equivaldría a 50.|  
| functions > magnitude > boostingRangeEnd | Establece el valor final del intervalo en el que se puntúa la magnitud. El valor debe ser un número entero o un número de punto flotante. En cuanto a las clasificaciones por estrellas de 1 a 4, esto equivaldría a 4.|  
| functions > magnitude > constantBoostBeyondRange | Los valores válidos son true o false (valor predeterminado). Cuando se establece en true, la potenciación completa continuará aplicándose a los documentos que tienen un valor para el campo de destino superior al extremo superior del intervalo. Si es false, la potenciación de esta función no se aplicará a los documentos que tengan un valor para el campo de destino que se encuentre fuera del intervalo.|  
| functions > freshness | La función de puntuación del índice de actualización se usa para modificar las puntuaciones de clasificación de los elementos basados en valores de campos de DateTimeOffset. Por ejemplo, un elemento con una fecha más reciente puede clasificarse por encima de los elementos más antiguos. </br></br>También es posible clasificar elementos, como eventos del calendario con fechas futuras, de manera que los elementos más cercanos al presente tengan una clasificación más alta que aquellos más alejados en el futuro. </br></br>En la versión actual del servicio, uno de los extremos del intervalo se corregirá a la hora actual. El otro extremo es una hora en el pasado basada en boostingDuration. Para aumentar un intervalo de horas en el futuro, use un valor boostingDuration negativo. </br></br>La velocidad a la que cambia la potenciación desde un intervalo máximo y mínimo viene determinada por la interpolación aplicada al perfil de puntuación (consulte la figura siguiente). Para invertir el factor de potenciación aplicado, seleccione un factor de potenciación de menos de 1.|  
| functions > freshness > boostingDuration | Establece un período de caducidad después del que se detendrá la potenciación de un documento determinado. Consulte [Establecer boostingDuration](#bkmk_boostdur) en la sección siguiente para obtener información sobre la sintaxis y ejemplos.|  
| functions > distance | La función de puntuación de la distancia se usa para afectar a la puntuación de documentos en función de la cercanía o distancia respecto a una ubicación geográfica de referencia. La ubicación de referencia se proporciona como parte de la consulta en un parámetro (mediante el parámetro de consulta scoringParameter) como argumento lon,lat.|  
|functions >  distance > referencePointParameter | Un parámetro que se pasa en las consultas como ubicación de referencia (con el parámetro de consulta scoringParameter). Vea [Buscar documentos (API REST)](/rest/api/searchservice/Search-Documents) para obtener descripciones de los parámetros de consulta.|  
| functions > distance > boostingDistance | Número que indica la distancia en kilómetros desde la ubicación de referencia donde finaliza el intervalo de potenciación.|  
| functions > tag | La función de puntuación de etiquetas se usa para afectar a la puntuación de documentos basados en las etiquetas de documentos y las consultas de búsqueda. Los documentos que tienen etiquetas en común con la consulta de búsqueda se potenciarán. Las etiquetas de la consulta de búsqueda se proporcionan como un parámetro de puntuación en cada solicitud de búsqueda (mediante el parámetro de consulta scoringParameter). |  
| functions > tag > tagsParameter | Parámetro que se pasa en consultas para especificar etiquetas para una solicitud concreta (mediante el parámetro de consulta scoringParameter). Vea [Buscar documentos (API REST)](/rest/api/searchservice/Search-Documents) para obtener descripciones de los parámetros de consulta.|  
| functions > functionAggregation | Opcional. Se aplica solo cuando se especifican las funciones. Los valores válidos son: suma (valor predeterminado), promedio, mínimo, máximo y firstMatching. Una puntuación de búsqueda es un valor único que se calcula a partir de varias variables, incluidas varias funciones. Este atributo indica cómo se combinan los valores de potenciación de todas las funciones en una única potenciación total que, luego, se aplica a la puntuación del documento base. La puntuación base se basa en el valor [tf-idf](http://www.tfidf.com/) calculado a partir del documento y la consulta de búsqueda.|  
| defaultScoringProfile | Al ejecutar una solicitud de búsqueda, si no se especifica ningún perfil de puntuación, se usará la puntuación predeterminada ([tf-idf](http://www.tfidf.com/) solamente). </br></br>Puede invalidar el valor predeterminado integrado, y sustituir un perfil personalizado como el que se va a usar cuando no se especifica ningún perfil específico en la solicitud de búsqueda.|  

<a name="bkmk_interpolation"></a> 

## <a name="set-interpolations"></a>Establecer interpolaciones

Las interpolaciones permiten establecer la forma de la pendiente usada para la puntuación. Dado que la puntuación es de alta a baja, la pendiente siempre disminuye, pero la interpolación determina la curva de la pendiente descendente. Es posible usar las siguientes interpolaciones:  

| Interpolación | Descripción |  
|-|-|  
|`linear`|En los artículos que están dentro del intervalo máximo y mínimo, la potenciación aplicada al artículo se realizará en un grado que va decreciendo de manera constante. Lineal es la interpolación predeterminada de un perfil de puntuación.|  
|`constant`|En los artículos que se encuentran dentro del intervalo de inicio y finalización, se aplicará una potenciación constante a los resultados de la clasificación.|  
|`quadratic`|En comparación con una interpolación lineal que tiene una potentiación en reducción constante, Cuadrática provoca inicialmente una reducción a un ritmo inferior y, a continuación, a medida que se aproxima el final del intervalo, se reduce a un intervalo muy superior. Esta opción de interpolación no se permite en funciones de puntuación de etiquetas.|  
|`logarithmic`|En comparación con una interpolación lineal que tiene una potenciación en reducción constante, Logarítmica provoca inicialmente una reducción a un ritmo superior y, a continuación, a medida que se aproxima el final del intervalo, se reduce a un intervalo muy inferior. Esta opción de interpolación no se permite en funciones de puntuación de etiquetas.|  

 ![Líneas de constante, lineal, cuadrática y log10 en el gráfico](media/scoring-profiles/azuresearch_scorefunctioninterpolationgrapht.png "AzureSearch_ScoreFunctionInterpolationGrapht")  

<a name="bkmk_boostdur"></a> 

## <a name="set-boostingduration"></a>Establecer boostingDuration

`boostingDuration` es un atributo de la función `freshness`. Se usa para establecer un período de caducidad después del que se detendrá la potenciación de un documento determinado. Por ejemplo, para potenciar una línea de productos o marca durante un período de promoción de 10 días, debe especificar el período de 10 días como "P10D" para dichos documentos.  

`boostingDuration` debe tener el formato de un valor "dayTimeDuration" XSD (subconjunto restringido de un valor de duración ISO 8601). El patrón de este valor es: "P[nD][T[nH][nM][nS]]".  

La tabla siguiente proporciona varios ejemplos.  

|Duration|boostingDuration|  
|--------------|----------------------|  
|1 día|"P1D"|  
|2 días, 12 horas|"P2DT12H"|  
|15 minutos|"PT15M"|  
|30 días, 5 horas, 10 minutos y 6,334 segundos|"P30DT5H10M6.334S"|  

Para ver más ejemplos, consulte el [esquema XML: Datatypes (sitio web de W3.org)](https://www.w3.org/TR/xmlschema11-2/#dayTimeDuration).  

## <a name="see-also"></a>Consulte también

+ [Clasificación y puntuación de similitud en Azure Cognitive Search](index-similarity-and-scoring.md)
+ [Referencia de API de REST](/rest/api/searchservice/)
+ [Create Index API](/rest/api/searchservice/create-index)
+ [SDK de .NET de Azure Cognitive Search](/dotnet/api/overview/azure/search?)
+ [¿Qué son los perfiles de puntuación?](https://social.technet.microsoft.com/wiki/contents/articles/26706.azure-search-what-are-scoring-profiles.aspx)