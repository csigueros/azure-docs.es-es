---
title: 'Funciones personalizadas en el conector de IoT: API de Azure Healthcare'
description: En este artículo se describe cómo usar Funciones personalizadas con IoT Connector de asignación de dispositivos.
author: msjasteppe
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: how-to
ms.date: 11/22/2021
ms.author: jasteppe
ms.openlocfilehash: 2bb0d3c17de870017733291bee4680f740afc1ad
ms.sourcegitcommit: 3d04177023a3136832adb561da831ccc8e9910c7
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 11/23/2021
ms.locfileid: "132933845"
---
# <a name="how-to-use-custom-functions"></a>Uso de funciones personalizadas

> [!IMPORTANT]
> Azure Healthcare APIs se encuentra actualmente en VERSIÓN PRELIMINAR. Los [Términos de uso complementarios para las versiones preliminares de Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) incluyen términos legales adicionales que se aplican a las características de Azure que se encuentran en la versión beta, en versión preliminar o que todavía no se han publicado con disponibilidad general.

> [!TIP]
> Consulte la herramienta [Data Mapper](https://github.com/microsoft/iomt-fhir/tree/master/tools/data-mapper) del conector ioMT para editar, probar y solucionar problemas de asignaciones de dispositivos y destinos de FHIR del conector de IoT. Exporte asignaciones para cargar en el conector de IoT Azure Portal use con la versión [de código](https://github.com/microsoft/iomt-fhir) abierto del conector de IoT.

En este artículo se describe cómo usar las funciones de cliente del conector de IoT.

Hay muchas funciones disponibles cuando se **usa JmesPath** como lenguaje de expresiones. Además de las funciones disponibles como parte de la especificación JmesPath, también se pueden usar muchas funciones personalizadas. En este artículo se describen las funciones personalizadas específicas del conector de IoT para usarlas con la plantilla Asignaciones de dispositivos durante el proceso de normalización.

> [!TIP]
> Para obtener más información sobre las funciones JmesPath, vea la especificación JmesPath [](https://jmespath.org/specification.html#built-in-functions).

## <a name="function-signature"></a>Signatura de función

Cada función tiene una firma que sigue la especificación JmesPath. Esta firma se puede representar como:

```jmespath
return_type function_name(type $argname)
```

La firma indica los tipos válidos para los argumentos. Si se pasa un tipo no válido para un argumento, se producirá un error.

> [!NOTE]
> Cuando se realizan funciones relacionadas con matemáticas, el resultado final **debe** ser capaz de caber dentro de un valor [largo de](/dotnet/csharp/language-reference/builtin-types/integral-numeric-types#characteristics-of-the-integral-types) C#. Si el resultado final no puede caber dentro de un valor largo de C#, se producirá un error matemático.

## <a name="exception-handling"></a>Control de excepciones

Pueden producirse excepciones en varios puntos del ciclo de vida de procesamiento de eventos. Estos son los distintos puntos donde se pueden producir:

|Acción|Cuando|Excepciones que pueden producirse durante el análisis de plantillas|Resultado|
|------|----|-------------------------------------------------|-------|
|**Análisis de plantillas**|Cada vez que se recibe un nuevo lote de mensajes, se carga y analiza la plantilla asignación de dispositivos.|Error al analizar la plantilla.|El sistema intentará volver a cargar y analizar la plantilla de asignación de dispositivos más reciente hasta que el análisis se realiza correctamente. No se procesará ningún mensaje nuevo hasta que el análisis sea correcto.|
|**Análisis de plantillas**|Cada vez que se recibe un nuevo lote de mensajes, se carga y analiza la plantilla asignación de dispositivos.|No se puede analizar ninguna expresión.|El sistema intentará volver a cargar y analizar la plantilla de asignación de dispositivos más reciente hasta que el análisis se realiza correctamente. No se procesará ningún mensaje nuevo hasta que el análisis sea correcto.|
|**Ejecución de funciones**|Cada vez que se ejecuta una función en datos dentro de un mensaje.|Los datos de entrada no coinciden con los de la firma de la función.|El sistema detiene el procesamiento de ese mensaje. El mensaje no se reinterio.|
|**Ejecución de funciones**|Cada vez que se ejecuta una función en datos dentro de un mensaje.|Cualquier otra excepción enumerada en la descripción de la función.|El sistema detiene el procesamiento de ese mensaje. El mensaje no se reinterio.|

## <a name="mathematical-functions"></a>Funciones matemáticas

### <a name="add"></a>add

```jmespath
number add(number $left, number $right)
```

Devuelve el resultado de agregar el argumento izquierdo a la derecha.

Ejemplos:

| Dado...                       | Expresión       | Resultado |
|-----------------------------|------------------|--------|
| n/d                         | add(10, 10)      | 20     |
| {"left": 40, "right": 50}   | add(left, right) | 90     |
| {"left": 0, "right": 50}    | add(left, right) | 50     |

### <a name="divide"></a>Dividir

```jmespath
number divide(number $left, number $right)
```

Devuelve el resultado de dividir el argumento izquierdo por la derecha.

Ejemplos:

| Dado...                       | Expresión          | Resultado                           |
|-----------------------------|---------------------|----------------------------------|
| n/d                         | divide(10, 10)      | 1                                |
| {"left": 40, "right": 50}   | divide(left, right) | 0.8                              |
| {"left": 0, "right": 50}    | divide(left, right) | 0                                |
| {"left": 50, "right": 0}    | divide(left, right) | error matemático: dividir por cero |

### <a name="multiply"></a>Multiplicar

```jmespath
number multiply(number $left, number $right)
```

Devuelve el resultado de multiplicar el argumento izquierdo por el derecho.

Ejemplos:

| Dado...                       | Expresión            | Resultado |
|-----------------------------|-----------------------|--------|
| n/d                         | multiply(10, 10)      | 100    |
| {"left": 40, "right": 50}   | multiply(left, right) | 2000   |
| {"left": 0, "right": 50}    | multiply(left, right) | 0      |

### <a name="pow"></a>pow

```jmespath
number pow(number $left, number $right)
```

Devuelve el resultado de elevar el argumento izquierdo a la potencia de la derecha.

Ejemplos:

| Dado...                         | Expresión       | Resultado                     |
|-------------------------------|------------------|----------------------------|
| n/d                           | pow(10, 10)      | 10000000000                |
| {"left": 40, "right": 50}     | pow(left, right) | error matemático: desbordamiento |
| {"left": 0, "right": 50}      | pow(left, right) | 0                          |
| {"left": 100, "right": 0,5}   | pow(left, right) | 10                         |

### <a name="subtract"></a>restar

```jmespath
number subtract(number $left, number $right)
```

Devuelve el resultado de restar el argumento derecho de la izquierda.

Ejemplos:

| Dado...                       | Expresión            | Resultado |
|-----------------------------|-----------------------|--------|
| n/d                         | subtract(10, 10)      | 0      |
| {"left": 40, "right": 50}   | subtract(left, right) | -10    |
| {"left": 0, "right": 50}    | subtract(left, right) | -50    |

## <a name="string-functions"></a>Funciones de cadena

### <a name="insertstring"></a>insertString

```jmespath
string insertString(string $original, string $toInsert, number pos)
```

Genera una nueva cadena insertando el valor de *toInsert* en la cadena *original.* La cadena se insertará en la posición *pos* dentro de la cadena *original.*

Si el argumento posicional está basado en cero, la posición de cero hace referencia al primer carácter dentro de la cadena. 

Si el argumento posicional proporcionado está fuera del intervalo de la longitud *de original,* se producirá un error.

Ejemplos:

| Dado...                                                     | Expresión                                         | Resultado              |
|-----------------------------------------------------------|----------------------------------------------------|---------------------|
| {"original": "mple", "toInsert": "sa", "pos": 0}          | insertString(original, toInsert, pos)              | "sample"            |
| {"original": "suess", "toInsert": "cc", "pos": 2}         | insertString(original, toInsert, pos)              | "correcto"           |
| {"original": "myString", "toInsert": "!!", "pos": 8}      | insertString(original, toInsert, pos)              | "myString!".        |
| {"original": "myString", "toInsert": "!!"}                | insertString(original, toInsert, length(original)) | "myString!".        |
| {"original": "myString", "toInsert": "!!", "pos": 100}    | insertString(original, toInsert, pos)              | error: fuera del intervalo |
| {"original": "myString", "toInsert": "!!", "pos": -1}     | insertString(original, toInsert, pos)              | error: fuera del intervalo |

## <a name="date-functions"></a>Funciones de fecha

### <a name="fromunixtimestamp"></a>fromUnixTimestamp

```jmespath
string fromUnixTimestamp(number $unixTimestampInSeconds)
```

Genera una marca de [tiempo compatible con ISO 8061](https://en.wikipedia.org/wiki/ISO_8601) a partir de la marca de tiempo de Unix especificada. La marca de tiempo se representa como el número de segundos desde la época (1 de enero de 1970).

Ejemplos:

| Dado...                 | Expresión              | Resultado                  |
|-----------------------|-------------------------|-------------------------|
| {"unix": 1625677200} | fromUnixTimestamp(unix)  | "2021-07-07T17:00:00+0" |
| {"unix": 0}          | fromUnixTimestamp(unix)  | "1970-01-01T00:00:00+0" |

### <a name="fromunixtimestampms"></a>fromUnixTimestampMs

```jmespath
string fromUnixTimestampMs(number $unixTimestampInMs)
```

Genera una marca de [tiempo compatible con ISO 8061](https://en.wikipedia.org/wiki/ISO_8601) a partir de la marca de tiempo de Unix especificada. La marca de tiempo se representa como el número de milisegundos desde la época (1 de enero de 1970).

Ejemplos:

| Dado...                    | Expresión                | Resultado                  |
|--------------------------|---------------------------|-------------------------|
| {"unix": 1626799080000}  | fromUnixTimestampMs(unix) | "2021-07-20T16:38:00+0" |
| {"unix": 0}              | fromUnixTimestampMs(unix) | "1970-01-01T00:00:00+0" |

> [!TIP]
> Consulte la Guía de solución [de problemas del conector de](./iot-troubleshoot-guide.md) IoT para obtener ayuda para solucionar errores y problemas comunes.

## <a name="next-steps"></a>Pasos siguientes

En este artículo, ha aprendido a usar funciones personalizadas del conector de IoT. Para obtener información sobre cómo usar Funciones personalizadas con asignaciones de dispositivos, consulte

>[!div class="nextstepaction"]
>[Uso de asignaciones de dispositivos](how-to-use-device-mappings.md)

(FHIR&#174;) es una marca registrada [de HL7](https://hl7.org/fhir/) y se usa con el permiso HL7.
