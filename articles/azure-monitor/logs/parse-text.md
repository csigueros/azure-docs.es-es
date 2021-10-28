---
title: Análisis de datos de texto en registros de Azure Monitor | Microsoft Docs
description: Se describen distintas opciones para analizar los datos de los registros de Azure Monitor cuando estos se ingieren y cuando se recuperan en una consulta, y se comparan las ventajas relativas para cada uno.
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 10/20/2021
ms.openlocfilehash: 4898174fda8dd597a1260211ed28d4719779c073
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/22/2021
ms.locfileid: "130219970"
---
# <a name="parse-text-data-in-azure-monitor-logs"></a>Análisis de datos de texto en registros de Azure Monitor
Algunos datos de registro recopilados por Azure Monitor incluyen varios fragmentos de información en una sola propiedad. El análisis de estos datos en varias propiedades facilita su uso en las consultas. Un ejemplo común es un [registro personalizado](../agents/data-sources-custom-logs.md) que recopila una entrada de registro completo con varios valores en una sola propiedad. Al crear propiedades independientes para los distintos valores, puede buscar y agregar en cada uno.

En este artículo se describen distintas opciones para analizar los datos de Azure Monitor cuando estos se ingieren y cuando se recuperan en una consulta, y se comparan las ventajas relativas para cada uno.


## <a name="parsing-methods"></a>Análisis de métodos
Puede analizar los datos ya sea en el momento de la ingesta cuando se recopilan o bien al realizar la consulta cuando los datos se analizan con una consulta. Cada estrategia tiene unas ventajas únicas, como se describe a continuación.

### <a name="parse-data-at-collection-time"></a>Análisis de los datos en el momento de la recopilación
Cuando se analizan los datos en el momento de la recopilación, se configuran [campos personalizados](../logs/custom-fields.md) que crean nuevas propiedades en la tabla. Las consultas no tienen que incluir ninguna lógica de análisis y simplemente se usan estas propiedades como cualquier otro campo de la tabla.

Las ventajas que presenta este método son, entre otras:

- Facilita la consulta de los datos recopilados, ya que no es necesario incluir en ella comandos de análisis.
- Mejor rendimiento de las consultas dado que no precisan llevar a cabo el análisis.
 
Las desventajas que presenta este método son, entre otras:

- Deben definirse de antemano. No puede incluir datos que ya se hayan recopilado.
- Si cambia la lógica de análisis, solo se aplicará a los nuevos datos.
- Menos opciones de análisis de las disponibles en las consultas.
- Aumenta el tiempo de latencia para la recopilación de datos.
- Los errores pueden resultar difíciles de controlar.


### <a name="parse-data-at-query-time"></a>Análisis de los datos en el momento de la consulta
Cuando se analizan los datos al realizar las consultas, se incluye la lógica en la consulta para analizar los datos de varios campos. No se modifica la propia tabla.

Las ventajas que presenta este método son, entre otras:

- Se aplica a los datos, incluidos los que ya se han recopilado.
- Los cambios en la lógica se pueden aplicar inmediatamente a todos los datos.
- Opciones flexibles de opciones que incluyen la lógica predefinida para estructuras de datos determinadas.
 
Las desventajas que presenta este método son, entre otras:

- Requiere las consultas más complejas. Esto se puede mitigar mediante el uso de [funciones para simular una tabla](#use-function-to-simulate-a-table).
- La lógica de análisis se debe replicar en varias consultas. Se puede compartir cierta lógica mediante funciones.
- Puede crear una sobrecarga al ejecutar una lógica compleja en conjuntos de registros muy grandes (miles de millones).

## <a name="parse-data-as-its-collected"></a>Los datos se analizan a medida que se recopilan.
Consulte [Creación de campos personalizados en Azure Monitor](../logs/custom-fields.md) para obtener más información sobre el análisis de datos a medida que se recopilan. De esta forma se crean propiedades personalizadas en la tabla que las consultas pueden usar como cualquier otra propiedad.

## <a name="parse-data-in-query-using-patterns"></a>Análisis de los datos en consultas con patrones
Cuando los datos que desea analizar pueden identificarse mediante un patrón que se repite en todos los registros, puede usar distintos operadores en el [lenguaje de consulta de Kusto](/azure/kusto/query/) para extraer un dato específico de una o varias propiedades nuevas.

### <a name="simple-text-patterns"></a>Patrones de texto simple

Use el operador [parse](/azure/kusto/query/parseoperator) en la consulta para crear una o varias propiedades personalizadas que se puedan extraer de una expresión de cadena. Especifique el patrón de identificación y los nombres de las propiedades que se van a crear. Esto es especialmente útil para los datos con pares clave-valor de cadenas con un formato similar a _clave=valor_.

Considere un registro personalizado con datos en el formato siguiente.

```
Time=2018-03-10 01:34:36 Event Code=207 Status=Success Message=Client 05a26a97-272a-4bc9-8f64-269d154b0e39 connected
Time=2018-03-10 01:33:33 Event Code=208 Status=Warning Message=Client ec53d95c-1c88-41ae-8174-92104212de5d disconnected
Time=2018-03-10 01:35:44 Event Code=209 Status=Success Message=Transaction 10d65890-b003-48f8-9cfc-9c74b51189c8 succeeded
Time=2018-03-10 01:38:22 Event Code=302 Status=Error Message=Application could not connect to database
Time=2018-03-10 01:31:34 Event Code=303 Status=Error Message=Application lost connection to database
```

La consulta siguiente analizaría estos datos en propiedades individuales. Se agrega la línea con _project_ para devolver solo las propiedades calculadas y no _RawData_, que es la única propiedad que contiene toda la entrada del registro personalizado.

```Kusto
MyCustomLog_CL
| parse RawData with * "Time=" EventTime " Event Code=" Code " Status=" Status " Message=" Message
| project EventTime, Code, Status, Message
```

El siguiente es otro ejemplo que divide el nombre de usuario de un nombre principal de usuario en la tabla _AzureActivity_.

```Kusto
AzureActivity
| parse  Caller with UPNUserPart "@" * 
| where UPNUserPart != "" //Remove non UPN callers (apps, SPNs, etc)
| distinct UPNUserPart, Caller
```


### <a name="regular-expressions"></a>Expresiones regulares
Si los datos se pueden identificar con una expresión regular, puede usar [funciones que usan expresiones regulares](/azure/kusto/query/re2) para extraer los valores individuales. En el ejemplo siguiente, se usa [extract](/azure/kusto/query/extractfunction) para separar el campo _UPN_ de los registros _AzureActivity_ y, a continuación, devolver usuarios distintos.

```Kusto
AzureActivity
| extend UPNUserPart = extract("([a-z.]*)@", 1, Caller) 
| distinct UPNUserPart, Caller
```

Para habilitar un análisis eficiente a gran escala, Azure Monitor utiliza la versión re2 de Expresiones regulares, que es similar, pero no idéntica a algunas de las otras variantes de las expresiones regulares. Consulte la [sintaxis de las expresiones re2](https://aka.ms/kql_re2syntax) para obtener más información.


## <a name="parse-delimited-data-in-a-query"></a>Análisis de datos delimitados en una consulta
Los datos delimitados separan los campos con un carácter común, como una coma, en un archivo CSV. Use la función [split](/azure/kusto/query/splitfunction) para analizar los datos delimitados con el delimitador que especifique. Puede utilizarlo con el operador [extend](/azure/kusto/query/extendoperator) para devolver todos los campos de los datos o para especificar los campos individuales que se incluirán en la salida.

> [!NOTE]
> Dado que split devuelve un objeto dinámico, es posible que los resultados tengan que convertirse explícitamente a un tipo de datos como es el tipo cadena para usarse en los operadores y en los filtros.

Considere un registro personalizado con datos en el formato CSV siguiente.

```
2018-03-10 01:34:36, 207,Success,Client 05a26a97-272a-4bc9-8f64-269d154b0e39 connected
2018-03-10 01:33:33, 208,Warning,Client ec53d95c-1c88-41ae-8174-92104212de5d disconnected
2018-03-10 01:35:44, 209,Success,Transaction 10d65890-b003-48f8-9cfc-9c74b51189c8 succeeded
2018-03-10 01:38:22, 302,Error,Application could not connect to database
2018-03-10 01:31:34, 303,Error,Application lost connection to database
```

La consulta siguiente analizaría estos datos y los resumiría en dos según las propiedades calculadas. La primera línea divide la propiedad _RawData_ en una matriz de cadena. Cada una de las siguientes líneas da nombre a propiedades individuales y las agrega a la salida usando funciones para convertirlas al tipo de datos adecuado.

```Kusto
MyCustomCSVLog_CL
| extend CSVFields  = split(RawData, ',')
| extend EventTime  = todatetime(CSVFields[0])
| extend Code       = toint(CSVFields[1]) 
| extend Status     = tostring(CSVFields[2]) 
| extend Message    = tostring(CSVFields[3]) 
| where getyear(EventTime) == 2018
| summarize count() by Status,Code
```

## <a name="parse-predefined-structures-in-a-query"></a>Análisis de estructuras predefinidas en una consulta
Si se da formato a los datos en una estructura conocida, es posible que se pueda usar una de las funciones en el [lenguaje de consulta de Kusto](/azure/kusto/query/) para analizar las estructuras predefinidas:

- [JSON](/azure/kusto/query/parsejsonfunction)
- [XML](/azure/kusto/query/parse-xmlfunction)
- [IPv4](/azure/kusto/query/parse-ipv4function)
- [URL](/azure/kusto/query/parseurlfunction)
- [Consulta URL](/azure/kusto/query/parseurlqueryfunction)
- [Ruta de acceso del archivo](/azure/kusto/query/parsepathfunction)
- [Agente de usuario](/azure/kusto/query/parse-useragentfunction)
- [Cadena de versión](/azure/kusto/query/parse-versionfunction)

En la consulta de ejemplo siguiente, se analiza el campo _Properties_ (Propiedades) de la tabla _AzureActivity_ (Actividad de Azure), que se estructura en JSON. Guarda los resultados en una propiedad dinámica denominada _parsedProp_, que incluye el valor con el nombre individual en JSON. Estos valores se usan para filtrar y resumir los resultados de la consulta.

```Kusto
AzureActivity
| extend parsedProp = parse_json(Properties) 
| where parsedProp.isComplianceCheck == "True" 
| summarize count() by ResourceGroup, tostring(parsedProp.tags.businessowner)
```

Estas funciones de análisis pueden utilizar en gran medida el procesador, por lo que deben usarse solo cuando la consulta emplee varias propiedades de los datos con formato. En caso contrario, el simple procesamiento de la coincidencia de patrones será más rápido.

En el ejemplo siguiente se muestra el desglose del tipo de autenticación previa de TGT para el controlador de dominio. El tipo existe solo en el campo EventData, que es una cadena XML, pero no ningún otro dato de este campo es necesario. En este caso, se usa [parse](/azure/kusto/query/parseoperator) para seleccionar el elemento de datos requerido.

```Kusto
SecurityEvent
| where EventID == 4768
| parse EventData with * 'PreAuthType">' PreAuthType '</Data>' * 
| summarize count() by PreAuthType
```

## <a name="use-function-to-simulate-a-table"></a>Uso de la función para simular una tabla
Puede tener varias consultas que realizan el mismo análisis de una tabla determinada. En este caso, [cree una función](../logs/functions.md) que devuelva los datos analizados, en lugar de replicar la lógica de análisis en cada consulta. A continuación, puede utilizar el alias de función en lugar de la tabla original en otras consultas.

Considere el ejemplo de registro personalizado delimitado por comas anterior. Para poder usar los datos analizados en varias consultas, cree una función con la consulta siguiente y guárdela con el alias _MyCustomCSVLog_.

```Kusto
MyCustomCSVLog_CL
| extend CSVFields = split(RawData, ',')
| extend DateTime  = tostring(CSVFields[0])
| extend Code      = toint(CSVFields[1]) 
| extend Status    = tostring(CSVFields[2]) 
| extend Message   = tostring(CSVFields[3]) 
```

Ahora puede usar el alias _MyCustomCSVLog_ en lugar del nombre de tabla real en consultas similares a la siguiente.

```Kusto
MyCustomCSVLog
| summarize count() by Status,Code
```


## <a name="next-steps"></a>Pasos siguientes
* Obtenga información acerca de las [consultas de registros](./log-query-overview.md) para analizar los datos recopilados de soluciones y orígenes de datos.