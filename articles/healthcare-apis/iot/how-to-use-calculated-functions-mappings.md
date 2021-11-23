---
title: 'Asignaciones CalculatedContentTemplate en IoT Connector Device Mappings: API de Azure Healthcare'
description: En este artículo se describe cómo usar asignaciones CalculatedContentTemplate con plantillas de asignaciones de dispositivos del conector de IoT.
author: msjasteppe
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: how-to
ms.date: 11/22/2021
ms.author: jasteppe
ms.openlocfilehash: 6e92acd7cc537b9723b1b114b11d8408cf941443
ms.sourcegitcommit: 3d04177023a3136832adb561da831ccc8e9910c7
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 11/23/2021
ms.locfileid: "132940992"
---
# <a name="how-to-use-calculatedcontenttemplate-mappings"></a>Uso de asignaciones CalculatedContentTemplate

> [!IMPORTANT]
> Azure Healthcare APIs se encuentra actualmente en VERSIÓN PRELIMINAR. Los [Términos de uso complementarios para las versiones preliminares de Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) incluyen términos legales adicionales que se aplican a las características de Azure que se encuentran en la versión beta, en versión preliminar o que todavía no se han publicado con disponibilidad general.

> [!TIP]
> Consulte la herramienta [Data Mapper](https://github.com/microsoft/iomt-fhir/tree/master/tools/data-mapper) del conector IoMT para editar, probar y solucionar problemas de las asignaciones de destino de dispositivos y FHIR del conector ioT. Exporte las asignaciones para cargar en el conector de IoT Azure Portal use con la versión [de código abierto](https://github.com/microsoft/iomt-fhir) del conector de IoT.

En este artículo se describe cómo usar asignaciones CalculatedContentTemplate con plantillas de asignaciones de dispositivos del conector de IoT.

## <a name="calculatedcontenttemplate"></a>CalculatedContentTemplate

El conector de IoT proporciona una plantilla de contenido basada en expresiones para que coincida con la plantilla buscada y extraiga valores. **JSONPath** o JmesPath pueden usar expresiones. Cada expresión dentro de la plantilla puede elegir su propio lenguaje de expresiones. 

> [!NOTE]
> Si no se define un lenguaje de expresiones, se usará el idioma de expresión predeterminado configurado para la plantilla. El valor predeterminado es JSONPath, pero se puede sobrescribir si es necesario.

Una expresión se define como:

```json
<name of expression> : {
        "value" : <the expression>,
        "language": <the expression language>
    }
```

En el ejemplo siguiente, *typeMatchExpression* se define como:

```json
"templateType": "CalculatedContent",
    "template": {
        "typeName": "heartrate",
        "typeMatchExpression": {
            "value" : "$..[?(@heartRate)]",
            "language": "JsonPath"
        },
        ...
    }
```
> [!TIP]
> El lenguaje de expresiones predeterminado que se usará para una plantilla de asignación de dispositivos es JsonPath. Si desea usar JsonPath, se puede proporcionar la expresión por sí sola.

```json
"templateType": "CalculatedContent",
    "template": {
        "typeName": "heartrate",
        "typeMatchExpression": "$..[?(@heartRate)]",
        ...
    }
```

El lenguaje de expresiones predeterminado que se va a usar para una plantilla se puede establecer explícitamente mediante el `defaultExpressionLanguage` parámetro :

```json
"templateType": "CalculatedContent",
    "template": {
        "typeName": "heartrate",
        "defaultExpressionLanguage": "JsonPath",
        "typeMatchExpression": "$..[?(@heartRate)]",
        ...
    }
```

CalculatedContentTemplate permite hacer coincidir y extraer valores de un mensaje de Azure Event Hubs mediante **expresiones** como se define a continuación:

|Propiedad|Descripción|Ejemplo|
|--------|-----------|-------|
|TypeName|Tipo que se asociará a las medidas que coinciden con la plantilla.|`heartrate`|
|TypeMatchExpression|Expresión que se evalúa con la carga de EventData. Si se encuentra un JToken coincidente, la plantilla se considera una coincidencia. Todas las expresiones posteriores se evalúan con el JToken extraído coincidente aquí.|`$..[?(@heartRate)]`|
|TimestampExpression|Expresión para extraer el valor de marca de tiempo para occurrenceTimeUtc de la medida.|`$.matchedToken.endDate`|
|DeviceIdExpression|Expresión para extraer el identificador del dispositivo.|`$.matchedToken.deviceId`|
|PatientIdExpression|*Obligatorio* cuando IdentityResolution está en **modo de** creación y *Opcional* cuando IdentityResolution está en **modo de** búsqueda. Expresión para extraer el identificador del paciente.|`$.matchedToken.patientId`|
|EncounterIdExpression|*Opcional:* expresión para extraer el identificador de encuentro.|`$.matchedToken.encounterId`|
|CorrelationIdExpression|*Opcional:* expresión para extraer el identificador de correlación. Esta salida se puede usar para agrupar valores en una sola observación en las asignaciones de destino de FHIR.|`$.matchedToken.correlationId`|
|Values[].ValueName|Nombre que se asociará al valor extraído por la expresión siguiente. Se usa para enlazar el valor o componente deseado en la plantilla de asignación de destino de FHIR.|`hr`|
|Values[].ValueExpression|Expresión para extraer el valor deseado.|`$.matchedToken.heartRate`|
|Values[].Required|Requerirá que el valor esté presente en la carga; Si no se encuentra, no se generará una medida y se creará una excepción InvalidOperationException.|`true`|

### <a name="expression-languages"></a>Lenguajes de expresiones

Al especificar el idioma que se usará para la expresión, los valores siguientes son válidos:

| Lenguaje de expresiones | Value        |
|---------------------|--------------|
| JSONPath            | **JsonPath** |
| JmesPath            | **JmesPath** |

>[!TIP]
>Para más información sobre JSONPath, consulte [JSONPath.](https://goessner.net/articles/JsonPath/) [CalculatedContentTemplate usa](#calculatedcontenttemplate) la implementación [json de .NET](https://www.newtonsoft.com/json/help/html/QueryJsonSelectTokenJsonPath.htm) para resolver expresiones JSONPath.
>
>Para obtener más información sobre JmesPath, vea [JmesPath](https://jmespath.org/specification.html). [CalculatedContentTemplate usa](#calculatedcontenttemplate) la implementación de .NET de [JmesPath](https://github.com/jdevillard/JmesPath.Net) para resolver expresiones JmesPath.

### <a name="custom-functions"></a>Funciones personalizadas

También hay disponible un conjunto de funciones personalizadas del conector de IoT. Estas funciones personalizadas están fuera de las funciones proporcionadas como parte de la especificación JmesPath. Para más información sobre Las funciones personalizadas, consulte Funciones personalizadas del conector [de IoT.](./how-to-use-custom-functions.md)

### <a name="matched-token"></a>Token coincidente

**TypeMatchExpression se** evalúa con respecto a la carga de EventData entrante. Si se encuentra un JToken coincidente, la plantilla se considera una coincidencia. 

Todas las expresiones posteriores se evalúan con un nuevo JToken. Este nuevo JToken contiene la carga de EventData original y el JToken extraído coincidente aquí. 

De esta manera, la carga original y el objeto coincidente están disponibles para cada expresión posterior. El JToken extraído estará disponible como propiedad **matchedToken.**

Dado este mensaje de ejemplo:

*Message*

```json
{
  "Body": {
    "deviceId": "device123",
    "data": [
      {
        "systolic": "120", // Match
        "diastolic": "80", // Match 
        "date": "2021-07-13T17:29:01.061144Z"
      },
      {
        "systolic": "122", // Match
        "diastolic": "82", // Match
        "date": "2021-07-13T17:28:01.061122Z"
      }
    ]
  },
  "Properties": {},
  "SystemProperties": {}
}
```

*Plantilla*

```json
{
  "templateType": "CollectionContent",
  "template": [
    {
      "templateType": "CalculatedContent",
      "template": {
        "typeName": "heartrate",
        "typeMatchExpression": "$..[?(@systolic && @diastolic)]", // Expression
        "deviceIdExpression": "$.Body.deviceId", // This accesses the attribute 'deviceId' which belongs to the original event data
        "timestampExpression": "$.matchedToken.date", 
        "values": [
          {
            "required": "true",
            "valueExpression": "$.matchedToken.systolic",
            "valueName": "systolic"
          },
          {
            "required": "true",
            "valueExpression": "$.matchedToken.diastolic",
            "valueName": "diastolic"
          }
        ]
      }
    }
  ]
}
```

Se extraerán dos coincidencias mediante la expresión anterior y se usarán para crear JTokens. Las expresiones posteriores se evaluarán mediante los siguientes JTokens:

```json
{
  "Body": {
    "deviceId": "device123",
    "data": [
      {
        "systolic": "120", 
        "diastolic": "80",
        "date": "2021-07-13T17:29:01.061144Z"
      },
      {
        "systolic": "122",
        "diastolic": "82",
        "date": "2021-07-13T17:28:01.061122Z"
      }
    ]
  },
  "Properties": {},
  "SystemProperties": {},
  "matchedToken" : {
      "systolic": "120",
      "diastolic": "80",
      "date": "2021-07-13T17:29:01.061144Z"
  }
}
```

And

```json
{
  "Body": {
    "deviceId": "device123",
    "data": [
      {
        "systolic": "120",
        "diastolic": "80",
        "date": "2021-07-13T17:29:01.061144Z"
      },
      {
        "systolic": "122", 
        "diastolic": "82", 
        "date": "2021-07-13T17:28:01.061122Z"
      }
    ]
  },
  "Properties": {},
  "SystemProperties": {},
  "matchedToken" : {
      "systolic": "122",
      "diastolic": "82",
      "date": "2021-07-13T17:28:01.061122Z"
    }
  }
}
```

### <a name="examples"></a>Ejemplos

**Frecuencia del corazón**

*Message*

```json
{
  "Body": {
    "heartRate": "78",
    "endDate": "2019-02-01T22:46:01.8750000Z",
    "deviceId": "device123"
  },
  "Properties": {},
  "SystemProperties": {}
}
```

*Plantilla*

```json
    {
      "templateType": "CalculatedContent",
      "template": {
        "typeName": "heartrate",
        "typeMatchExpression": "$..[?(@heartRate)]",
        "deviceIdExpression": "$.matchedToken.deviceId",
        "timestampExpression": "$.matchedToken.endDate",
        "values": [
          {
            "required": "true",
            "valueExpression": "$.matchedToken.heartRate",
            "valueName": "hr"
          }
        ]
      }
    }
```

**Presión de sangre**

*Message*

```json
{
    "Body": {
        "systolic": "123", // Match
        "diastolic" : "87", // Match
        "endDate": "2019-02-01T22:46:01.8750000Z",
        "deviceId": "device123"
    },
    "Properties": {},
    "SystemProperties": {}
}
```

*Plantilla*

```json
    {
      "templateType": "CalculatedContent",
      "template": {
        "typeName": "bloodpressure",
        "typeMatchExpression": "$..[?(@systolic && @diastolic)]", // Expression
        "deviceIdExpression": "$.matchedToken.deviceId",
        "timestampExpression": "$.matchedToken.endDate",
        "values": [
          {
            "required": "true",
            "valueExpression": "$.matchedToken.systolic",
            "valueName": "systolic"
          },
          {
            "required": "true",
            "valueExpression": "$.matchedToken.diastolic",
            "valueName": "diastolic"
          }
        ]
      }
    }
```

**Project varias medidas a partir de un solo mensaje**

*Message*

```json
{
    "Body": {
        "heartRate": "78", // Match (Template 1)
        "steps": "2", // Match (Template 2)
        "endDate": "2019-02-01T22:46:01.8750000Z",
        "deviceId": "device123"
    },
    "Properties": {},
    "SystemProperties": {}
}
```

*Plantilla 1*

```json
    {
      "templateType": "CalculatedContent",
      "template": {
        "typeName": "heartrate",
        "typeMatchExpression": "$..[?(@heartRate)]", // Expression
        "deviceIdExpression": "$.matchedToken.deviceId",
        "timestampExpression": "$.matchedToken.endDate",
        "values": [
          {
            "required": "true",
            "valueExpression": "$.matchedToken.heartRate",
            "valueName": "hr"
          }
        ]
      }
    },
```

*Plantilla 2*

```json
    {
      "templateType": "CalculatedContent",
      "template": {
        "typeName": "stepcount",
        "typeMatchExpression": "$..[?(@steps)]", // Expression
        "deviceIdExpression": "$.matchedToken.deviceId",
        "timestampExpression": "$.matchedToken.endDate",
        "values": [
          {
            "required": "true",
            "valueExpression": "$.matchedToken.steps",
            "valueName": "steps"
          }
        ]
      }
    }
```

**Project varias medidas de la matriz en el mensaje**

*Message*

```json
{
  "Body": [
    {
      "heartRate": "78", // Match
      "endDate": "2019-02-01T20:46:01.8750000Z",
      "deviceId": "device123"
    },
    {
      "heartRate": "81", // Match
      "endDate": "2019-02-01T21:46:01.8750000Z",
      "deviceId": "device123"
    },
    {
      "heartRate": "72", // Match
      "endDate": "2019-02-01T22:46:01.8750000Z",
      "deviceId": "device123"
    }
  ],
  "Properties": {},
  "SystemProperties": {}
}
```

*Plantilla*

```json
    {
      "templateType": "CalculatedContent",
      "template": {
        "typeName": "heartrate",
        "typeMatchExpression": "$..[?(@heartRate)]", // Expression
        "deviceIdExpression": "$.matchedToken.deviceId",
        "timestampExpression": "$.matchedToken.endDate",
        "values": [
          {
            "required": "true",
            "valueExpression": "$.matchedToken.heartRate",
            "valueName": "hr"
          }
        ]
      }
    }
```

**Project datos del token coincidente y el evento original**

*Message*

```json
{
  "Body": {
    "deviceId": "device123",
    "data": [
      {
        "systolic": "120", // Match
        "diastolic": "80", // Match 
        "date": "2021-07-13T17:29:01.061144Z"
      },
      {
        "systolic": "122", // Match
        "diastolic": "82", // Match
        "date": "2021-07-13T17:28:01.061122Z"
      }
    ]
  },
  "Properties": {},
  "SystemProperties": {}
}
```

*Plantilla*

```json
    {
      "templateType": "CalculatedContent",
      "template": {
        "typeName": "heartrate",
        "typeMatchExpression": "$..[?(@systolic && @diastolic)]", // Expression
        "deviceIdExpression": "$.Body.deviceId", // This accesses the attribute 'deviceId' which belongs to the original event data
        "timestampExpression": "$.matchedToken.date", 
        "values": [
          {
            "required": "true",
            "valueExpression": "$.matchedToken.systolic",
            "valueName": "systolic"
          },
          {
            "required": "true",
            "valueExpression": "$.matchedToken.diastolic",
            "valueName": "diastolic"
          }
        ]
      }
    }
```

**Selección y transformación de los datos entrantes**

En el ejemplo siguiente, los datos de altura llegan en pulgadas o metros. Queremos que todos los datos de alto normalizado se ajusten a metros. Para lograr este resultado, creamos una plantilla que tiene como destino solo los datos de altura en pulgadas y los transforma en metros. Otra plantilla tiene como destino los datos de altura en metros y simplemente los almacena tal y como están.

*Message*

```json
{
  "Body": [
    {
      "height": "78",
      "unit": "inches", // Match (Template 1)
      "endDate": "2019-02-01T22:46:01.8750000Z",
      "deviceId": "device123"
    },
    {
      "height": "1.9304",
      "unit": "meters", // Match (Template 2)
      "endDate": "2019-02-01T23:46:01.8750000Z",
      "deviceId": "device123"
    }
  ],
  "Properties": {},
  "SystemProperties": {}
}
```

*Plantilla 1*

```json
    {
      "templateType": "CalculatedContent",
      "template": {
        "typeName": "heightInMeters",
        "typeMatchExpression": "$..[?(@unit == 'inches')]",
        "deviceIdExpression": "$.matchedToken.deviceId",
        "timestampExpression": "$.matchedToken.endDate",
        "values": [
          {
            "required": "true",
            "valueExpression": {
              "value": "multiply(to_number(matchedToken.height), `0.0254`)", // Convert inches to meters. Notice we utilize JmesPath as that gives us access to transformation functions
              "language": "JmesPath"
            },
            "valueName": "height"
          }
        ]
      }
    }
```

*Plantilla 2*

```json
    {
      "templateType": "CalculatedContent",
      "template": {
        "typeName": "heightInMeters",
        "typeMatchExpression": "$..[?(@unit == 'meters')]",
        "deviceIdExpression": "$.matchedToken.deviceId",
        "timestampExpression": "$.matchedToken.endDate",
        "values": [
          {
            "required": "true",
            "valueExpression": "$.matchedToken.height", // Simply extract the height as it is already in meters
            "valueName": "height"
          }
        ]
      }
    }
```

> [!TIP]
> Consulte la Guía de solución [de problemas del conector de](./iot-troubleshoot-guide.md) IoT para obtener ayuda para corregir errores y problemas comunes.

## <a name="next-steps"></a>Pasos siguientes

En este artículo, ha aprendido a usar asignaciones de dispositivos. Para obtener información sobre cómo usar las asignaciones de destino de FHIR, consulte

>[!div class="nextstepaction"]
>[Uso de asignaciones de destino de FHIR](how-to-use-fhir-mappings.md)

(FHIR&#174;) es una marca comercial registrada de [HL7](https://hl7.org/fhir/) y se usa con el permiso hl7.
