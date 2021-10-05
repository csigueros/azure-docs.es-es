---
title: Analizadores del modelo de información de Azure Sentinel (ASIM) | Microsoft Docs
description: En este artículo se explica cómo usar las funciones de KQL como analizadores en tiempo de consulta para implementar el modelo de información de Azure Sentinel (ASIM).
services: sentinel
cloud: na
documentationcenter: na
author: oshezaf
manager: rkarlin
ms.assetid: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 08/11/2021
ms.author: ofshezaf
ms.openlocfilehash: b3dc1575258c495a2be63eced6aa141c60bb6631
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/24/2021
ms.locfileid: "128659445"
---
# <a name="azure-sentinel-information-model-asim-parsers-public-preview"></a>Analizadores del modelo de información de Azure Sentinel (ASIM) (versión preliminar pública)

En Azure Sentinel, el análisis y la [normalización](normalization.md) tienen lugar en el tiempo de consulta. Los analizadores se crean como [funciones definidas por el usuario de KQL](/azure/data-explorer/kusto/query/functions/user-defined-functions) que transforman los datos de tablas existentes, como **CommonSecurityLog**, tablas de registros personalizados o Syslog, al esquema normalizado. Una vez que el analizador se guarda como una función de área de trabajo, se puede usar como cualquier tabla de Azure Sentinel.

> [!TIP]
> Vea también el [seminario web de profundización sobre los analizadores de normalización de Azure Sentinel y el contenido normalizado](https://www.youtube.com/watch?v=zaqblyjQW6k) o revise las [diapositivas](https://1drv.ms/b/s!AnEPjr8tHcNmjGtoRPQ2XYe3wQDz?e=R3dWeM). Para más información, consulte la sección [Pasos siguientes](#next-steps).
>

> [!IMPORTANT]
> ASIM está actualmente en versión preliminar. En la página [Términos de uso complementarios para las Versiones preliminares de Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) se incluyen términos legales adicionales que se aplican a las características de Azure que se encuentran en versión beta, versión preliminar o que todavía no se han publicado para su disponibilidad general.
>

## <a name="source-agnostic-and-source-specific-parsers"></a>Analizadores independientes de origen y específicos de origen

ASIM incluye dos niveles de analizadores: **independientes de origen** y **específicos del origen**.

### <a name="source-agnostic-parsers"></a>Analizadores independientes de origen

Un **analizador independiente de origen** combina todos los orígenes normalizados en el mismo esquema y se puede usar para consultar todos ellos mediante campos normalizados. El nombre del analizador independiente de origen es `im<schema>`, donde `<schema>` representa el esquema específico al que sirve.

Por ejemplo, en la siguiente consulta se usa el analizador de DNS independiente de origen para consultar eventos de DNS mediante los campos normalizados `ResponseCodeName`, `SrcIpAddr` y `TimeGenerated`:

```kusto
imDns
  | where isnotempty(ResponseCodeName)
  | where ResponseCodeName =~ "NXDOMAIN"
  | summarize count() by SrcIpAddr, bin(TimeGenerated,15m)
```

Un analizador independiente de origen puede combinar varios analizadores normalizados específicos de origen usando el operador de KQL `union`. El nombre de un analizador normalizado específico de origen es `vim<schema><vendor><product>`. Por lo tanto, el analizador `imDns` tiene el siguiente aspecto:

```kusto
union isfuzzy=true
vimDnsEmpty,
vimDnsCiscoUmbrella,
vimDnsInfobloxNIOS,
vimDnsMicrosoftOMS
```

> [!NOTE]
> Al usar los analizadores independientes de origen de ASIM que comienzan por `im` en la página **Registros**, el selector de intervalo de tiempo se establece en `custom`. Puede seguir estableciendo el intervalo de tiempo por sí mismo. Como alternativa, puede especificar el intervalo de tiempo mediante parámetros de analizador.
>
> O bien, use un analizador `ASim`, que no admite parámetros, y tampoco establece el selector de intervalos de tiempo en `custom` de forma predeterminada.
>

### <a name="source-specific-parsers"></a>Analizadores específicos del origen

Si agrega analizadores normalizados **específicos de origen** al analizador independiente de origen, puede incluir orígenes personalizados en consultas integradas en las que se usan los analizadores independientes de origen.

Los analizadores específicos de origen permiten obtener valor inmediato de contenido integrado, como análisis, libros o información de los datos personalizados.

Los analizadores específicos de origen también se pueden usar de forma independiente. Por ejemplo, en un libro específico de Infoblox, use el analizador `vimDnsInfobloxNIOS`.

## <a name="optimizing-parsing-using-parameters"></a><a name="optimized-parsers"></a>Optimización del análisis mediante parámetros

El uso de analizadores puede afectar al rendimiento de las consultas, principalmente por tener que filtrar los resultados después del análisis. Por este motivo, muchos analizadores tienen parámetros de filtrado opcionales que le permiten filtrar antes de analizar y mejoran el rendimiento de las consultas. Junto con los esfuerzos de optimización de consultas y filtrado previo, los analizadores de ASIM a menudo proporcionan un mejor rendimiento en comparación con la no utilización de la normalización en absoluto.

Para usar parámetros de filtrado, agregue uno o varios parámetros con nombre al invocar el analizador. Por ejemplo, el siguiente inicio de consulta garantiza que solo se devuelvan consultas DNS para dominios inexistentes:

```kusto
imDns(responsecodename='NXDOMAIN')
```

El ejemplo anterior es parecido a la consulta siguiente, pero es mucho más eficaz.

```kusto
imDns | where ResponseCodeName == 'NXDOMAIN'
```

Cada esquema tiene un conjunto estándar de parámetros de filtrado que se documentan en el documento de esquema. Los parámetros de filtrado son completamente opcionales y actualmente solo son totalmente compatibles con el esquema DNS. Otros esquemas admiten parámetros de filtrado estándar sin optimización de filtrado previo.

## <a name="writing-source-specific-parsers"></a>Escritura de analizadores específicos del origen

Un analizador es una consulta de KQL guardada como función de área de trabajo. Una vez guardado, se puede usar como tablas integradas. La consulta del analizador incluye las siguientes partes:

**Filtro** > **Análisis** > **Preparación de campos**

### <a name="filtering"></a>Filtrado

#### <a name="filtering-the-relevant-records"></a>Filtrado de los registros pertinentes

En muchos casos, una tabla incluye varios tipos de eventos. Por ejemplo:
* La tabla de Syslog tiene datos de varios orígenes.
* Las tablas personalizadas pueden incluir información de un único origen que proporciona más de un tipo de evento y puede ajustarse a varios esquemas.

Por lo tanto, un analizador debe filtrar primero solo los registros pertinentes para el esquema de destino.

El filtrado en KQL se realiza usando el operador `where`. Por ejemplo, el **evento 1 de Sysmon** notifica la creación de un proceso y debe normalizarse al esquema **ProcessEvent**. El **evento 1 de Sysmon** forma parte de la tabla `Event`, y se debe usar el siguiente filtro:

```kusto
Event | where Source == "Microsoft-Windows-Sysmon" and EventID == 1
```

#### <a name="filtering-based-on-parser-parameters"></a>Filtrado basado en parámetros del analizador

Cuando use [analizadores con parámetros](#optimized-parsers), asegúrese de que el analizador acepta los parámetros de filtrado para el esquema correspondiente como se documenta en el artículo de referencia de ese esquema.

El artículo de función es idéntico para cada esquema. Por ejemplo, para la firma del analizador con parámetros de consulta de DNS:

```kusto
let DNSQuery_MS=(
    starttime:datetime=datetime(null), 
    endtime:datetime=datetime(null), 
    srcipaddr:string='*', 
    domain_has_any:dynamic=dynamic([]),
    responsecodename:string='*', 
    dnsresponsename:string='*', 
    response_has_any_prefix:dynamic=dynamic([]),
    eventtype:string='lookup'
    )
```

Agregue los filtros, en función de los valores de los parámetros. Al filtrar, asegúrese de que:

- **Filtra antes de analizar mediante campos físicos**. Si los resultados filtrados no son lo suficientemente precisos, repita la prueba después del análisis para ajustar los resultados. Para más información, consulte la sección ["Optimización del filtrado"](#optimization).
 - **No filtra si el parámetro no está definido y sigue teniendo el valor predeterminado**. En los ejemplos siguientes se muestra cómo implementar el filtrado para un parámetro de cadena, en el que el valor predeterminado suele ser "\*", y para un parámetro de lista, en el que el valor predeterminado suele ser una lista vacía.

``` kusto
srcipaddr=='*' or ClientIP==srcipaddr
array_length(domain_has_any) == 0 or Name has_any (domain_has_any)
```

> [!TIP]
> Un analizador existente del mismo tipo es un excelente punto de partida para implementar el filtrado de parámetros.
>

#### <a name="filtering-optimization"></a><a name="optimization"></a>Optimización del filtrado


Para garantizar el rendimiento del analizador, tenga presentes las siguientes recomendaciones de filtrado:

-   Filtre siempre por campos integrados en lugar de por campos analizados. Aunque a veces es más fácil filtrar usando campos analizados, tiene un efecto drástico en el rendimiento.
-   Use operadores que proporcionen un rendimiento optimizado, en concreto, `==`, `has` y `startswith`. El uso de operadores como `contains` o `matches regex` también afecta drásticamente al rendimiento.

Las recomendaciones de filtrado relacionadas con el rendimiento pueden no ser siempre intrascendentes. Por ejemplo, el uso de `has` es menos preciso que `contains`. En otros casos, buscar una coincidencia del campo integrado, como `SyslogMessage`, es menos preciso que comparar un campo extraído, como `DvcAction`. En tales casos, se recomienda seguir filtrando previamente usando un operador de optimización del rendimiento sobre un campo integrado y repetir el filtrado usando condiciones más precisas después del análisis.

Para obtener un ejemplo, consulte el siguiente fragmento de código del analizador de [DNS de Infoblox](https://aka.ms/AzSentinelInfobloxParser). El analizador comprueba primero que el campo SyslogMessage `has` la palabra `client`. Sin embargo, el término podría usarse en un lugar diferente del mensaje. Por lo tanto, después de analizar el campo `Log_Type`, el analizador comprueba de nuevo que la palabra `client` era realmente el valor del campo.

```kusto
Syslog | where ProcessName == "named" and SyslogMessage has "client"
…
      | extend Log_Type = tostring(Parser[1]),
      | where Log_Type == "client"
```

> [!NOTE]
> Los analizadores no deben filtrar por hora, puesto que ya lo hace la consulta que usa el analizador.
>

### <a name="parsing"></a>Análisis

Una vez que la consulta selecciona los registros pertinentes, puede que tenga que analizarlos. Normalmente, es necesario el análisis si gran parte de la información del evento se transmite en un solo campo de texto.

A continuación se enumeran los operadores de KQL que realizan el análisis, ordenados por su optimización del rendimiento. El primero proporciona el rendimiento más optimizado y el último, el menos optimizado.

|Operador  |Descripción  |
|---------|---------|
|[split](/azure/data-explorer/kusto/query/splitfunction)     |    Analiza una cadena de valores delimitados por un delimitador.     |
|[parse_csv](/azure/data-explorer/kusto/query/parsecsvfunction)     |     Analiza una cadena de valores con formato de línea CSV (valores separados por comas).    |
|[parse](/azure/data-explorer/kusto/query/parseoperator)     |    Analiza varios valores de una cadena arbitraria usando un patrón, que puede ser un patrón simplificado con un mejor rendimiento, o una expresión regular.     |
|[extract_all](/azure/data-explorer/kusto/query/extractallfunction)     | Analiza valores únicos de una cadena arbitraria usando una expresión regular. `extract_all` tiene un rendimiento similar a `parse` si este último usa una expresión regular.        |
|[extract](/azure/data-explorer/kusto/query/extractfunction)     |    Analiza un valor único de una cadena arbitraria usando una expresión regular. <br><br>`extract` proporciona un mejor rendimiento que `parse` o `extract_all` si se necesita un valor único. Sin embargo, usar varias activaciones de `extract` en la misma cadena de origen es menos eficaz que hacerlo con un solo operador `parse` o `extract_all`, por lo que debe evitarse hacerlo.      |
|[parse_json](/azure/data-explorer/kusto/query/parsejsonfunction)  | Analiza los valores de una cadena con formato JSON. Si solo se necesitan algunos valores del código JSON, el uso de `parse`, `extract` o `extract_all` proporciona un mejor rendimiento.        |
|[parse_xml](/azure/data-explorer/kusto/query/parse-xmlfunction)     |    Analiza los valores de una cadena con formato XML. Si solo se necesitan algunos valores del código XML, el uso de `parse`, `extract` o `extract_all` proporciona un mejor rendimiento.     |
| | |

Además de analizar la cadena, la fase de análisis puede requerir un procesamiento adicional de los valores originales, incluido lo siguiente:

- **Formato y conversión de tipos**. Una vez extraído el campo de origen, es posible que deba formatearse para ajustarse al campo de esquema de destino. Por ejemplo, puede que tenga que convertir una cadena que representa la fecha y hora en un campo datetime.     En estos casos resultan útiles funciones como `todatetime` y `tohex`.

- **Búsqueda de valores**. Una vez extraído el valor del campo de origen, es posible que deba asignarse al conjunto de valores especificado para el campo de esquema de destino. Por ejemplo, algunos orígenes informan de códigos de respuesta de DNS numéricos, mientras que el esquema exige códigos de respuesta de texto, más comunes. Las funciones `iff` y `case` pueden ser útiles para asignar algunos valores.

    Por ejemplo, el analizador de DNS de Microsoft asigna el campo `EventResult` en función del Id. de evento y el código de respuesta usando una instrucción `iff`, tal y como se muestra a continuación:

    ```kusto
    extend EventResult = iff(EventId==257 and ResponseCode==0 ,'Success','Failure')
    ```

    Para varios valores, use `datatable` y `lookup`, tal y como se muestra en el mismo analizador de DNS:

    ```kusto
    let RCodeTable = datatable(ResponseCode:int,ResponseCodeName:string) [ 0, 'NOERROR', 1, 'FORMERR'....];
    ...
     | lookup RCodeTable on ResponseCode
     | extend EventResultDetails = case (
         isnotempty(ResponseCodeName), ResponseCodeName,
         ResponseCode between (3841 .. 4095), 'Reserved for Private Use',
         'Unassigned')
    ```

> [!NOTE]
> La transformación no permite usar solo `lookup`, ya que se asignan varios valores a `Reserved for Private Use` o `Unassigned` y, por tanto, la consulta usa tanto lookup como case.
> Aun así, la consulta sigue siendo mucho más eficaz que usar `case` para todos los valores.
>

### <a name="prepare-fields-in-the-result-set"></a>Preparación de campos en el conjunto de resultados

El analizador tiene que preparar los campos del conjunto de resultados para asegurarse de que se usen los campos normalizados. A modo de guía, los campos originales que no se normalizan no deben quitarse del conjunto de resultados, a menos que haya una razón imperiosa para hacerlo, por ejemplo, si generan confusión.

Los siguientes operadores de KQL se usan para preparar campos:

|Operador  | Descripción  | Cuándo se debe usar en un analizador  |
|---------|---------|---------|
|**extend**     | Crea campos calculados y los agrega al registro.        |  `Extend` se usa si los campos normalizados se analizan o se transforman a partir de los datos originales. Para obtener más información, consulte el ejemplo de la sección [Análisis](#parsing) anterior.     |
|**project-rename**     | Cambia el nombre de los campos.        |     Si ya existe un campo en el evento real y solo es necesario cambiarlo de nombre, use `project-rename`. <br><br>El campo con el nombre cambiado se comporta como un campo integrado, y las operaciones que se realizan con él tienen un rendimiento mucho mejor.   |
|**project-away**     |      Elimina campos.   |Use `project-away` para campos específicos que desee eliminar del conjunto de resultados.         |
|**project**     |  Selecciona los campos que ya existían antes o que se han creado como parte de la instrucción. Elimina todos los demás campos.       | No se recomienda su uso en un analizador, ya que este no debe eliminar otros campos que no estén normalizados. <br><br>Si necesita eliminar campos específicos, como valores temporales usados durante el análisis, use `project-away` para eliminarlos de los resultados.      |
| | | |

### <a name="handle-parsing-variants"></a>Control de variantes de análisis

En muchos casos, los eventos de una secuencia incluyen variantes que requieren una lógica de análisis diferente.

A menudo resulta tentador crear un analizador a partir de diferentes subanalizadores, cada uno controlando otra variante del evento que necesita una lógica de análisis diferente. Dichos subanalizadores, cada uno de ellos una consulta por sí mismo, se unifican posteriormente usando el operador `union`. Aunque resulta práctica, *no* se recomienda usar esta estrategia, ya que afecta considerablemente al rendimiento del analizador.

Al controlar variantes, use las siguientes directrices:

|Escenario  |Controlar  |
|---------|---------|
|Las distintas variantes representan *diferentes* tipos de eventos, normalmente asignados a esquemas diferentes.     |  Use analizadores independientes.       |
|Las diferentes variantes representan el *mismo* tipo de evento, pero se estructuran de forma diferente.     |   Si se conocen las variantes, como cuando hay un método para diferenciar entre los eventos antes del análisis, use el operador `case` para seleccionar el operador `extract_all` correcto que ejecutar y la asignación de campos, tal y como se muestra en el [analizador de DNS de Infoblox](https://aka.ms/AzSentinelInfobloxParser).      |
|`union` es inevitable.     |  Cuando sea inevitable usar `union`, asegúrese de aplicar las siguientes directrices:<br><br>-    Realice un filtrado previo usando campos integrados en cada una de las subconsultas. <br>-   Asegúrese de que los filtros se excluyan mutuamente. <br>-   Considere la posibilidad de no analizar información menos crítica, con el fin de reducir el número de subconsultas.       |
| | |

## <a name="add-your-parser-to-the-schema-source-agnostic-parser"></a><a name="include"></a>Agregar el analizador al analizador independiente de origen del esquema

Mediante la normalización puede usar su propio contenido y contenido integrado con sus datos personalizados.

Por ejemplo, si tiene un conector personalizado que recibe registros de actividad de consultas de DNS, puede asegurarse de que los registros aprovechen cualquier contenido de DNS normalizado del siguiente modo.

Para ello, modifique el analizador independiente de origen pertinente para incluir el analizador específico de origen que ha creado. Por ejemplo, modifique el analizador independiente de origen `imDns`, de modo que incluya el suyo. Para ello, agréguelo a la lista de analizadores en la instrucción `union`. Si el analizador admite parámetros, inclúyalo con la firma de parámetro, como el analizador vimDnsYyyXxx siguiente. Si no es así, inclúyalo sin una firma, como el analizador vimDnsWwwZzz siguiente.

```kusto
let DnsGeneric=(starttime:datetime=datetime(null), endtime:datetime=datetime(null)... ){
  union isfuzzy=true
    vimDnsEmpty, 
    vimDnsCiscoUmbrella (starttime, endtime, srcipaddr...),
    vimDnsInfobloxNIOS (starttime, endtime, srcipaddr...),
    vimDnsMicrosoftOMS (starttime, endtime, srcipaddr...),
    vimDnsYyyXxx (starttime, endtime, srcipaddr...),
    vimDnsWwwZzz
  };
  DnsGeneric( starttime, endtime, srcipaddr...)
```


## <a name="deploy-parsers"></a>Implementación de analizadores

Para implementar analizadores manualmente, cópielos en la página Registro de Azure Monitor y guarde el cambio. Este método es útil para realizar pruebas. Para obtener más información, consulte [Creación de una función](../azure-monitor/logs/functions.md).

Sin embargo, para implementar un gran número de analizadores, se recomienda usar una plantilla de ARM. Por ejemplo, puede que le interese usar una plantilla de ARM al implementar una solución de normalización completa que incluya un analizador independiente de origen y varios analizadores específicos de origen, o al implementar varios analizadores para esquemas diferentes con relación a un origen.

Para obtener más información, consulte la [plantilla de ARM de analizador genérico](https://github.com/Azure/Azure-Sentinel/tree/master/Tools/ARM-Templates/ParserQuery). Use esta plantilla como punto de partida e implemente el analizador pegándolo en el lugar pertinente durante el proceso de implementación de la plantilla. Por ejemplo, consulte la [plantilla de ARM de analizadores de DNS](https://github.com/Azure/Azure-Sentinel/tree/master/Parsers/ASimDns/ARM).

> [!TIP]
> Las plantillas de ARM pueden incluir diferentes recursos, por lo que sus analizadores se pueden implementar junto con conectores, reglas de análisis o listas de reproducción, por nombrar algunas opciones útiles. Por ejemplo, su analizador puede hacer referencia a una lista de reproducción que se implementará junto con él.
>


## <a name="next-steps"></a><a name="next-steps"></a>Pasos siguientes

En este artículo se describen los analizadores del modelo de información de Azure Sentinel (ASIM).

Para más información, consulte:

- Vea el [seminario web de profundización sobre los analizadores de normalización de Azure Sentinel y el contenido normalizado](https://www.youtube.com/watch?v=zaqblyjQW6k) o revise las [diapositivas](https://1drv.ms/b/s!AnEPjr8tHcNmjGtoRPQ2XYe3wQDz?e=R3dWeM).
- [Información general del modelo de información de Azure Sentinel](normalization.md)
- [Esquemas del modelo de información de Azure Sentinel](normalization-about-schemas.md)
- [Contenido del modelo de información de Azure Sentinel](normalization-content.md)
