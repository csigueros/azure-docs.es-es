---
title: Procedimientos para usar parámetros y expresiones en Azure Data Factory
description: En este artículo de procedimientos se proporciona información sobre las expresiones y las funciones que se pueden usar al crear entidades de Data Factory.
author: ssabat
ms.author: susabat
ms.reviewer: jburchel
ms.service: data-factory
ms.subservice: orchestration
ms.topic: conceptual
ms.date: 03/08/2020
ms.openlocfilehash: 41689a842bff067fb36dd97f980c10b71e533f8d
ms.sourcegitcommit: 8000045c09d3b091314b4a73db20e99ddc825d91
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/19/2021
ms.locfileid: "122446214"
---
# <a name="how-to-use-parameters-expressions-and-functions-in-azure-data-factory"></a>Procedimientos para usar parámetros, expresiones y funciones en Azure Data Factory

> [!div class="op_single_selector" title1="Seleccione la versión del servicio Data Factory que usa:"]
> * [Versión 1](v1/data-factory-functions-variables.md)
> * [Versión actual](how-to-expression-language-functions.md)
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

En este documento, nos centraremos principalmente en aprender conceptos fundamentales con varios ejemplos para explorar la capacidad de crear canalizaciones de datos parametrizados en Azure Data Factory. La parametrización y las expresiones dinámicas son importantes adiciones a ADF porque pueden ahorrar mucho tiempo y permiten una solución de extracción, transformación y carga de datos (ETL) o de extracción, carga de datos y transformación (ELT) mucho más flexible, lo que reduce drásticamente el coste del mantenimiento de la solución y acelera la implementación de nuevas características en las canalizaciones existentes. Estas mejoras se deben a que la parametrización reduce la cantidad de codificación de forma rígida y aumenta el número de objetos y procesos reutilizables en una solución.

## <a name="azure-data-factory-ui-and-parameters"></a>Interfaz de usuario y parámetros de Azure Data Factory

Si no está familiarizado con el uso de parámetros de Azure Data Factory en la interfaz de usuario de ADF, revise [Interfaz de usuario de Data Factory para los servicios vinculados con parámetros](./parameterize-linked-services.md#ui-experience) e [Interfaz de usuario de Data Factory para la canalización controlada por metadatos con parámetros](./how-to-use-trigger-parameterization.md#data-factory-ui) para obtener una explicación visual.

## <a name="parameter-and-expression-concepts"></a>Conceptos de parámetros y expresiones 

Puede usar parámetros para pasar valores externos a canalizaciones, conjuntos de datos, servicios vinculados y flujos de datos. Una vez que el parámetro se ha pasado al recurso, no se puede cambiar. Mediante la parametrización de recursos, puede reutilizarlos con valores diferentes cada vez. Los parámetros se pueden utilizar individualmente o como parte de expresiones. Los valores JSON de la definición pueden ser literales o expresiones que se evalúan en tiempo de ejecución.

Por ejemplo:  
  
```json
"name": "value"
```

 or  
  
```json
"name": "@pipeline().parameters.password"
```

Las expresiones pueden aparecer en cualquier lugar de un valor de cadena JSON y devolver siempre otro valor JSON. Aquí, *password* es un parámetro de canalización de la expresión. Si un valor JSON es una expresión, se extrae el cuerpo de la expresión quitando el signo arroba (\@). Si se necesita una cadena literal que empiece por \@, debe convertirse con \@\@. Los ejemplos siguientes muestran cómo se evalúan las expresiones.  
  
|Valor JSON|Resultado|  
|----------------|------------|  
|"parameters"|Se devuelven los caracteres de "parameters".|  
|"parameters[1]"|Se devuelven los caracteres de "parameters[1]".|  
|"\@\@"|Se devuelve una cadena de 1 carácter que contiene·"\@".|  
|" \@"|Se devuelve una cadena de 2 caracteres que contiene "\@".|  
  
 Las expresiones también pueden aparecer dentro de las cadenas mediante una característica llamada *interpolación de cadenas*, donde las expresiones se ajustan en `@{ ... }`. Por ejemplo: `"name" : "First Name: @{pipeline().parameters.firstName} Last Name: @{pipeline().parameters.lastName}"`  
  
 Con la interpolación de cadena, el resultado siempre es una cadena. Supongamos que se ha definido `myNumber` como `42` y `myString` como `foo`:  
  
|Valor JSON|Resultado|  
|----------------|------------|  
|"\@pipeline().parameters.myString"| Devuelve `foo` como una cadena.|  
|"\@{pipeline().parameters.myString}"| Devuelve `foo` como una cadena.|  
|"\@pipeline().parameters.myNumber"| Devuelve `42` como un *número*.|  
|"\@{pipeline().parameters.myNumber}"| Devuelve `42` como una *cadena*.|  
|"Answer is: @{pipeline().parameters.myNumber}"| Devuelve la cadena `Answer is: 42`.|  
|"\@concat('Answer is: ', string(pipeline().parameters.myNumber))"| Devuelve la cadena `Answer is: 42`.|  
|"Answer is: \@\@{pipeline().parameters.myNumber}"| Devuelve la cadena `Answer is: @{pipeline().parameters.myNumber}`.|  

## <a name="examples-of-using-parameters-in-expressions"></a>Ejemplos de uso de parámetros en expresiones 

### <a name="complex-expression-example"></a>Ejemplo de expresión compleja
En el ejemplo siguiente se muestra un ejemplo complejo que hace referencia a un subcampo profundo de la salida de la actividad. Para hacer referencia a un parámetro de canalización que se evalúa como un subcampo, use la sintaxis [] en lugar del operador punto (.) (como en el caso de subfield1 y subfield2)

`@activity('*activityName*').output.*subfield1*.*subfield2*[pipeline().parameters.*subfield3*].*subfield4*`

### <a name="dynamic-content-editor"></a>Editor de contenido dinámico

El editor de contenido dinámico convierte automáticamente los caracteres de escape en el contenido cuando finaliza la edición. Por ejemplo, el contenido siguiente del editor de contenido es una interpolación de cadenas con dos funciones de expresión. 

```json
{ 
  "type": "@{if(equals(1, 2), 'Blob', 'Table' )}",
  "name": "@{toUpper('myData')}"
}
```

El editor de contenido dinámico convierte el contenido anterior en la expresión `"{ \n  \"type\": \"@{if(equals(1, 2), 'Blob', 'Table' )}\",\n  \"name\": \"@{toUpper('myData')}\"\n}"`. El resultado de esta expresión es la cadena de formato JSON que se muestra a continuación.

```json
{
  "type": "Table",
  "name": "MYDATA"
}
```

### <a name="a-dataset-with--parameters"></a>Un conjunto de datos con parámetros

En el ejemplo siguiente, BlobDataset toma un parámetro llamado **path**. Su valor se usa para establecer un valor para la propiedad **folderPath** mediante la expresión: `dataset().path`. 

```json
{
    "name": "BlobDataset",
    "properties": {
        "type": "AzureBlob",
        "typeProperties": {
            "folderPath": "@dataset().path"
        },
        "linkedServiceName": {
            "referenceName": "AzureStorageLinkedService",
            "type": "LinkedServiceReference"
        },
        "parameters": {
            "path": {
                "type": "String"
            }
        }
    }
}
```

### <a name="a-pipeline-with--parameters"></a>Una canalización con parámetros

En el ejemplo siguiente, la canalización toma los parámetros **inputPath** y **outputPath**. El valor de **path** para el conjunto de datos del blob con parámetros se establece mediante el uso de los valores de estos parámetros. La sintaxis utilizada aquí es: `pipeline().parameters.parametername`. 

```json
{
    "name": "Adfv2QuickStartPipeline",
    "properties": {
        "activities": [
            {
                "name": "CopyFromBlobToBlob",
                "type": "Copy",
                "inputs": [
                    {
                        "referenceName": "BlobDataset",
                        "parameters": {
                            "path": "@pipeline().parameters.inputPath"
                        },
                        "type": "DatasetReference"
                    }
                ],
                "outputs": [
                    {
                        "referenceName": "BlobDataset",
                        "parameters": {
                            "path": "@pipeline().parameters.outputPath"
                        },
                        "type": "DatasetReference"
                    }
                ],
                "typeProperties": {
                    "source": {
                        "type": "BlobSource"
                    },
                    "sink": {
                        "type": "BlobSink"
                    }
                }
            }
        ],
        "parameters": {
            "inputPath": {
                "type": "String"
            },
            "outputPath": {
                "type": "String"
            }
        }
    }
}
```

  
## <a name="calling-functions-within-expressions"></a>Llamada a funciones dentro de expresiones 

Puede llamar a funciones dentro de expresiones. En las siguientes secciones se proporciona información sobre las funciones que se pueden usar en una expresión.  

### <a name="string-functions"></a>Funciones de cadena  

Para trabajar con cadenas, puede usar estas funciones de cadena y también algunas [funciones de colección](#collection-functions).
Las funciones de cadena solo funcionan en cadenas.

| Función de cadena | Tarea |
| --------------- | ---- |
| [concat](control-flow-expression-language-functions.md#concat) | Combina dos o más cadenas y devuelve la cadena combinada. |
| [endsWith](control-flow-expression-language-functions.md#endswith) | Comprueba si una cadena termina con la subcadena especificada. |
| [guid](control-flow-expression-language-functions.md#guid) | Genera un identificador único global (GUID) como una cadena. |
| [indexOf](control-flow-expression-language-functions.md#indexof) | Devuelve la posición inicial de una subcadena. |
| [lastIndexOf](control-flow-expression-language-functions.md#lastindexof) | Devuelve la posición inicial de la última repetición de una subcadena. |
| [replace](control-flow-expression-language-functions.md#replace) | Reemplaza una subcadena por la cadena especificada y devuelve la cadena actualizada. |
| [split](control-flow-expression-language-functions.md#split) | Devuelve una matriz que contiene subcadenas, separadas por comas, de una cadena mayor en función de un carácter delimitador especificado en la cadena original. |
| [startsWith](control-flow-expression-language-functions.md#startswith) | Comprueba si una cadena comienza con una subcadena especificada. |
| [substring](control-flow-expression-language-functions.md#substring) | Devuelve caracteres de una cadena, a partir de la posición especificada. |
| [toLower](control-flow-expression-language-functions.md#toLower) | Devuelve una cadena en formato de minúsculas. |
| [toUpper](control-flow-expression-language-functions.md#toUpper) | Devuelve una cadena en formato de mayúsculas. |
| [trim](control-flow-expression-language-functions.md#trim) | Quita el espacio en blanco inicial y final de una cadena y devuelve la cadena actualizada. |

### <a name="collection-functions"></a>Funciones de colección

Para trabajar con colecciones, por lo general matrices, cadenas y, en ocasiones, diccionarios, puede usar estas funciones de colección.

| Función de colección | Tarea |
| ------------------- | ---- |
| [contains](control-flow-expression-language-functions.md#contains) | Comprueba si una colección contiene un elemento específico. |
| [empty](control-flow-expression-language-functions.md#empty) | Comprueba si una colección está vacía. |
| [first](control-flow-expression-language-functions.md#first) | Devuelve el primer elemento de una colección. |
| [intersection](control-flow-expression-language-functions.md#intersection) | Devuelve una colección que tiene *solo* los elementos comunes en las colecciones especificadas. |
| [join](control-flow-expression-language-functions.md#join) | Devuelve una cadena que tiene *todos* los elementos de una matriz, separados por el carácter especificado. |
| [last](control-flow-expression-language-functions.md#last) | Devuelve el último elemento de una colección. |
| [length](control-flow-expression-language-functions.md#length) | Devuelve el número de elementos de una cadena o una matriz. |
| [skip](control-flow-expression-language-functions.md#skip) | Quita elementos del principio de una colección y devuelve *todos los demás* elementos. |
| [take](control-flow-expression-language-functions.md#take) | Devuelve elementos del principio de una colección. |
| [union](control-flow-expression-language-functions.md#union) | Devuelve una colección que tiene *todos* los elementos de las colecciones especificadas. | 

### <a name="logical-functions"></a>Funciones lógicas  

Estas funciones son útiles en las condiciones y se pueden usar para evaluar cualquier tipo de lógica.  
  
| Función de comparación lógica | Tarea |
| --------------------------- | ---- |
| [and](control-flow-expression-language-functions.md#and) | Comprueba si todas las expresiones son true. |
| [equals](control-flow-expression-language-functions.md#equals) | Comprueba si ambos valores son equivalentes. |
| [greater](control-flow-expression-language-functions.md#greater) | Comprueba si el primer valor es mayor que el segundo. |
| [greaterOrEquals](control-flow-expression-language-functions.md#greaterOrEquals) | Comprueba si el primer valor es mayor o igual que el segundo. |
| [if](control-flow-expression-language-functions.md#if) | Comprueba si una expresión es true o false. En función del resultado, devuelve un valor especificado. |
| [less](control-flow-expression-language-functions.md#less) | Comprueba si el primer valor es menor que el segundo. |
| [lessOrEquals](control-flow-expression-language-functions.md#lessOrEquals) | Compruebe si el primer valor es menor o igual que el segundo valor. |
| [not](control-flow-expression-language-functions.md#not) | Comprueba si una expresión es false. |
| [or](control-flow-expression-language-functions.md#or) | Comprueba si al menos una expresión es true. |
  
### <a name="conversion-functions"></a>Funciones de conversión  

 Estas funciones se utilizan para convertir en cada uno de los tipos nativos del idioma:  
-   string
-   integer
-   FLOAT
-   boolean
-   arrays
-   dictionaries

| Función de conversión | Tarea |
| ------------------- | ---- |
| [array](control-flow-expression-language-functions.md#array) | Devuelve una matriz a partir de una única entrada especificada. Para varias entradas, consulte [createArray](control-flow-expression-language-functions.md#createArray). |
| [base64](control-flow-expression-language-functions.md#base64) | Devuelve la versión de una cadena codificada en base64. |
| [base64ToBinary](control-flow-expression-language-functions.md#base64ToBinary) | Devuelve la versión binaria de una cadena codificada en base64. |
| [base64ToString](control-flow-expression-language-functions.md#base64ToString) | Devuelve la versión de cadena de una cadena codificada en base64. |
| [binary](control-flow-expression-language-functions.md#binary) | Devuelve la versión binaria de un valor de entrada. |
| [bool](control-flow-expression-language-functions.md#bool) | Devuelve la versión booleana de un valor de entrada. |
| [coalesce](control-flow-expression-language-functions.md#coalesce) | Devuelve el primer valor distinto de null de uno o más parámetros. |
| [createArray](control-flow-expression-language-functions.md#createArray) | Devuelve una matriz a partir de varias entradas. |
| [dataUri](control-flow-expression-language-functions.md#dataUri) | Devuelve el URI de datos de un valor de entrada. |
| [dataUriToBinary](control-flow-expression-language-functions.md#dataUriToBinary) | Devuelve la versión binaria de un URI de datos. |
| [dataUriToString](control-flow-expression-language-functions.md#dataUriToString) | Devuelve la versión de cadena de un URI de datos. |
| [decodeBase64](control-flow-expression-language-functions.md#decodeBase64) | Devuelve la versión de cadena de una cadena codificada en base64. |
| [decodeDataUri](control-flow-expression-language-functions.md#decodeDataUri) | Devuelve la versión binaria de un URI de datos. |
| [decodeUriComponent](control-flow-expression-language-functions.md#decodeUriComponent) | Devuelve una cadena que reemplaza los caracteres de escape por versiones descodificadas. |
| [encodeUriComponent](control-flow-expression-language-functions.md#encodeUriComponent) | Devuelve una cadena que reemplaza los caracteres no seguros de la dirección URL por caracteres de escape. |
| [float](control-flow-expression-language-functions.md#float) | Devuelve un número de punto flotante de un valor de entrada. |
| [int](control-flow-expression-language-functions.md#int) | Devuelve la versión como número entero de una cadena. |
| [json](control-flow-expression-language-functions.md#json) | Devuelve el valor o el objeto de tipo Notación de objetos JavaScript (JSON) de una cadena o XML. |
| [string](control-flow-expression-language-functions.md#string) | Devuelve la versión de cadena de un valor de entrada. |
| [uriComponent](control-flow-expression-language-functions.md#uriComponent) | Devuelve la versión codificada con el URI de un valor de entrada mediante la sustitución de los caracteres no seguros de la dirección URL por caracteres de escape. |
| [uriComponentToBinary](control-flow-expression-language-functions.md#uriComponentToBinary) | Devuelve la versión binaria de una cadena codificada con el URI. |
| [uriComponentToString](control-flow-expression-language-functions.md#uriComponentToString) | Devuelve la versión de cadena de una cadena codificada con el URI. |
| [xml](control-flow-expression-language-functions.md#xml) | Devuelve la versión XML de una cadena. |
| [xpath](control-flow-expression-language-functions.md#xpath) | Comprueba el código XML de los nodos o valores que coinciden con una expresión XPath (XML Path Language) y devuelve los nodos o valores coincidentes. |

### <a name="math-functions"></a>Funciones matemáticas  
 Estas funciones pueden utilizarse para ambos tipos de números: **enteros** y **flotantes**.  

| Función matemática | Tarea |
| ------------- | ---- |
| [add](control-flow-expression-language-functions.md#add) | Devuelve el resultado de sumar dos números. |
| [div](control-flow-expression-language-functions.md#div) | Devuelve el resultado de dividir dos números. |
| [max](control-flow-expression-language-functions.md#max) | Devuelve el valor más alto de un conjunto de números o una matriz. |
| [min](control-flow-expression-language-functions.md#min) | Devuelve el valor más bajo de un conjunto de números o una matriz. |
| [mod](control-flow-expression-language-functions.md#mod) | Devuelve el resto de dividir dos números. |
| [mul](control-flow-expression-language-functions.md#mul) | Devuelve el producto de multiplicar dos números. |
| [rand](control-flow-expression-language-functions.md#rand) | Devuelve un entero aleatorio desde un intervalo especificado. |
| [range](control-flow-expression-language-functions.md#range) | Devuelve una matriz de enteros que comienza en un entero especificado. |
| [sub](control-flow-expression-language-functions.md#sub) | Devuelve el resultado de restar el segundo número del primero. |
  
### <a name="date-functions"></a>Funciones de fecha  

| Función de fecha u hora | Tarea |
| --------------------- | ---- |
| [addDays](control-flow-expression-language-functions.md#addDays) | Agrega un número de días a una marca de tiempo. |
| [addHours](control-flow-expression-language-functions.md#addHours) | Agrega un número de horas a una marca de tiempo. |
| [addMinutes](control-flow-expression-language-functions.md#addMinutes) | Agrega un número de minutos a una marca de tiempo. |
| [addSeconds](control-flow-expression-language-functions.md#addSeconds) | Agrega un número de segundos a una marca de tiempo. |
| [addToTime](control-flow-expression-language-functions.md#addToTime) | Agrega un número de unidades de tiempo a una marca de tiempo. Consulte también [getFutureTime](control-flow-expression-language-functions.md#getFutureTime). |
| [convertFromUtc](control-flow-expression-language-functions.md#convertFromUtc) | Convierte una marca de tiempo del formato Hora Universal Coordinada (UTC) a la zona horaria de destino. |
| [convertTimeZone](control-flow-expression-language-functions.md#convertTimeZone) | Convierte una marca de tiempo de la zona horaria de origen a la zona horaria de destino. |
| [convertToUtc](control-flow-expression-language-functions.md#convertToUtc) | Convierte una marca de tiempo de la zona horaria de origen al formato Hora Universal Coordinada (UTC). |
| [dayOfMonth](control-flow-expression-language-functions.md#dayOfMonth) | Devuelve el día del componente de mes de una marca de tiempo. |
| [dayOfWeek](control-flow-expression-language-functions.md#dayOfWeek) | Devuelve el día del componente de semana de una marca de tiempo. |
| [dayOfYear](control-flow-expression-language-functions.md#dayOfYear) | Devuelve el día del componente de año de una marca de tiempo. |
| [formatDateTime](control-flow-expression-language-functions.md#formatDateTime) | Devuelve la marca de tiempo como cadena en formato opcional. |
| [getFutureTime](control-flow-expression-language-functions.md#getFutureTime) | Devuelve la marca de tiempo actual más las unidades de tiempo especificadas. Consulte también [addToTime](control-flow-expression-language-functions.md#addToTime). |
| [getPastTime](control-flow-expression-language-functions.md#getPastTime) | Devuelve la marca de tiempo actual menos las unidades de tiempo especificadas. Consulte también [subtractFromTime](control-flow-expression-language-functions.md#subtractFromTime). |
| [startOfDay](control-flow-expression-language-functions.md#startOfDay) | Devuelve el inicio del día de una marca de tiempo. |
| [startOfHour](control-flow-expression-language-functions.md#startOfHour) | Devuelve el inicio de la hora de una marca de tiempo. |
| [startOfMonth](control-flow-expression-language-functions.md#startOfMonth) | Devuelve el inicio del mes de una marca de tiempo. |
| [subtractFromTime](control-flow-expression-language-functions.md#subtractFromTime) | Resta un número de unidades de tiempo de una marca de tiempo. Consulte también [getPastTime](control-flow-expression-language-functions.md#getPastTime). |
| [ticks](control-flow-expression-language-functions.md#ticks) | Devuelve el valor de la propiedad `ticks` de una marca de tiempo especificada. |
| [utcNow](control-flow-expression-language-functions.md#utcNow) | Devuelve la marca de tiempo actual como una cadena. |

## <a name="detailed-examples-for-practice"></a>Ejemplos detallados para practicar

### <a name="detailed-azure-data-factory-copy-pipeline-with-parameters"></a>Canalización detallada de copia de Azure Data Factory con parámetros 

Este [tutorial para pasar parámetros de canalización de copia de Azure Data Factory](https://azure.microsoft.com/mediahandler/files/resourcefiles/azure-data-factory-passing-parameters/Azure%20data%20Factory-Whitepaper-PassingParameters.pdf) le guiará en el proceso de pasar parámetros entre una canalización y una actividad, así como entre actividades.

### <a name="detailed--mapping-data-flow-pipeline-with-parameters"></a>Canalización detallada de flujo de datos de asignación con parámetros 

Siga el [flujo de datos de asignación con parámetros](./parameters-data-flow.md) para obtener un ejemplo completo sobre cómo usar los parámetros en el flujo de datos.

### <a name="detailed-metadata-driven-pipeline-with-parameters"></a>Canalización detallada controlada por metadatos con parámetros

Siga la [canalización controlada por metadatos con parámetros](./how-to-use-trigger-parameterization.md) para obtener más información sobre cómo usar los parámetros para diseñar canalizaciones controladas por metadatos. Este es un caso de uso popular para los parámetros.


## <a name="next-steps"></a>Pasos siguientes
Para obtener una lista de las variables del sistema que se pueden usar en las expresiones, vea [Variables del sistema](control-flow-system-variables.md).
