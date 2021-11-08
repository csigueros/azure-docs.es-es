---
title: 'Plantilla de asignación de dispositivo en conector de IoT: Azure Healthcare APIs'
description: En este artículo se describe cómo usar la plantilla Asignación de dispositivos en el IoT Connector.
author: msjasteppe
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: how-to
ms.date: 11/05/2021
ms.author: jasteppe
ms.openlocfilehash: f11770a05d2429c87647b65a828f6477f1b1b8cb
ms.sourcegitcommit: 5af89a2a7b38b266cc3adc389d3a9606420215a9
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 11/08/2021
ms.locfileid: "131988203"
---
# <a name="how-to-use-device-mappings"></a>Uso de asignaciones de dispositivos

> [!IMPORTANT]
> Azure Healthcare APIs se encuentra actualmente en VERSIÓN PRELIMINAR. Los [Términos de uso complementarios para las versiones preliminares de Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) incluyen términos legales adicionales que se aplican a las características de Azure que se encuentran en la versión beta, en versión preliminar o que todavía no se han publicado con disponibilidad general.

El conector de IoT requiere dos tipos de asignaciones basadas en JSON. El primer tipo, **Device mapping** (Asignación de dispositivos), es responsable de la asignación de las cargas de dispositivo enviadas al punto de conexión `devicedata` de Azure Event Hub. Extrae tipos, identificadores de dispositivo, fecha y hora de medición, y valores de medida. 

El segundo tipo, **Recursos Rápidos de Interoperabilidad en Salud de destino (FHIR&#174;),** controla la asignación del recurso de FHIR. Permite configurar la duración del período de observación, el tipo de datos FHIR que se usa para almacenar los valores y los códigos terminológicos. 

Los dos tipos de asignaciones se componen en un documento JSON en función de su tipo. A continuación, estos documentos JSON se agregan al conector de IoT a través del Azure Portal. El documento Asignación de dispositivos se agrega a través de la **página Asignación de** dispositivos y el documento de asignación de destino de FHIR a través de **la página** Destino.

> [!NOTE]
> Las asignaciones se almacenan en un almacenamiento de blobs subyacente y se cargan desde el blob por ejecución de proceso. Al actualizarse, se aplican inmediatamente. 

> [!TIP]
> Consulte la herramienta [Data Mapper](https://github.com/microsoft/iomt-fhir/tree/master/tools/data-mapper) del conector IoMT para editar, probar y solucionar problemas de las asignaciones de destino de dispositivos y FHIR del conector ioT. Exporte las asignaciones para cargar en el conector de IoT Azure Portal use con la versión [de código abierto](https://github.com/microsoft/iomt-fhir) del conector de IoT.

## <a name="device-mappings-overview"></a>Introducción a las asignaciones de dispositivos

Las asignaciones de dispositivos proporcionan funcionalidad para extraer el contenido del mensaje del dispositivo en un formato común para su posterior evaluación. Cada mensaje de dispositivo recibido se evalúa con todas las plantillas de asignación de dispositivos. 

Un único mensaje de dispositivo de entrada se puede separar en varios mensajes salientes que posteriormente se asignan a diferentes observaciones en el servicio FHIR. 

El resultado es un objeto de datos normalizado que representa los valores analizados con las plantillas. 

Es necesario buscar y extraer algunas propiedades obligatorias en el modelo de datos normalizado:

|Propiedad|Descripción|
|--------|-----------|
|**Type**|Nombre/tipo para clasificar la medida. Este valor se usa para enlazar a la asignación de destino de FHIR necesaria. Se pueden generar varias asignaciones al mismo tipo, lo que le permite asignar diferentes representaciones en varios dispositivos a una única salida común.|
|**OccurenceTimeUtc**|Hora a la que se realizó la medida.|
|**DeviceId**|Identificador del dispositivo. Este valor debe coincidir con un identificador en el recurso de dispositivo que existe en el servicio FHIR de destino.|
|**Propiedades**|Extraiga al menos una propiedad para que el valor se pueda guardar en el recurso de observación creado. Las propiedades son una colección de pares clave-valor que se extraen durante la normalización.|

> [!IMPORTANT]
> El modelo normalizado completo se define mediante la [interfaz IMeasurement.](https://github.com/microsoft/iomt-fhir/blob/master/src/lib/Microsoft.Health.Fhir.Ingest.Schema/IMeasurement.cs)

A continuación se muestran ejemplos conceptuales de lo que sucede durante el proceso de normalización y transformación en el conector de IoT:

:::image type="content" source="media/iot-data-normalization-high-level.png" alt-text="Ejemplo 1 del flujo de normalización de datos de IoT" lightbox="media/iot-data-normalization-high-level.png":::

:::image type="content" source="media/concepts-iot-mapping-templates/normalization-example.png" alt-text="Ejemplo de flujo de normalización de datos de IoT2" lightbox="media/concepts-iot-mapping-templates/normalization-example.png":::

La propia carga de contenido es un mensaje de Azure Event Hubs compuesto de tres partes: Body, Properties y SystemProperties. `Body` es una matriz de bytes que representa una cadena con codificación UTF-8. Durante la evaluación de la plantilla, la matriz de bytes se convierte automáticamente en el valor de cadena. `Properties` es una colección de valores clave que usa el creador del mensaje. `SystemProperties` también es una colección de valores clave reservada por el marco Azure Event Hubs con entradas que se rellenan automáticamente.

```json
{
    "Body": {
        "content": "value"
    },
    "Properties": {
        "key1": "value1",
        "key2": "value2"
    },
    "SystemProperties": {
        "x-opt-sequence-number": 1,
        "x-opt-enqueued-time": "2021-02-01T22:46:01.8750000Z",
        "x-opt-offset": 1,
        "x-opt-partition-key": "1"
    }
}
```
## <a name="mapping-with-jsonpath"></a>Asignación con JSONPath

Los cinco tipos de asignación de contenido de dispositivo admitidos hoy en día se basan en JSONPath para que coincidan con la asignación necesaria y los valores extraídos. Puede encontrar más información sobre JSONPath [aquí.](https://goessner.net/articles/JsonPath/) Los cinco tipos de plantilla usan la [implementación de .NET de JSON](https://www.newtonsoft.com/json/help/html/QueryJsonSelectTokenJsonPath.htm) para resolver expresiones JSONPath.

Puede definir una o varias plantillas dentro de la plantilla Asignación de dispositivos. Cada mensaje de dispositivo del centro de eventos recibido se evalúa con todas las plantillas de asignación de dispositivos. 

Un único mensaje de dispositivo de entrada se puede separar en varios mensajes salientes que posteriormente se asignan a diferentes observaciones en el servicio FHIR. 

Existen varios tipos de plantilla y se pueden usar al compilar el archivo de asignación de dispositivos.

|Nombre                                                                     | Descripción                                                                   |  
|-------------------------------------------------------------------------|-------------------------------------------------------------------------------|
|[JsonPathContentTemplate](#jsonpathcontenttemplate)                      |Plantilla que admite la escritura de expresiones mediante JsonPath                  
|[CollectionContentTemplate](#collectioncontenttemplate)                  |Plantilla que se usa para representar una lista de plantillas que se usarán durante la normalización.                                                            |                                                           
|[CalculatedContentTemplate](#calculatedcontenttemplate)                  |Plantilla que admite la escritura de expresiones mediante uno de varios lenguajes de expresiones. Admite la transformación de datos mediante el uso de funciones JmesPath.|
|[IotJsonPathContentTemplate](#iotjsonpathcontenttemplate)                |Plantilla que admite los mensajes enviados desde Azure IoT Hub o la característica Exportar datos heredados de Azure IoT Central.                                        |
|[IotCentralJsonPathContentTemplate](#iotcentraljsonpathcontenttemplate)  |Plantilla que admite los mensajes enviados a través de la característica Exportar datos de Azure Iot Central.|  

## <a name="jsonpathcontenttemplate"></a>JsonPathContentTemplate

JsonPathContentTemplate permite hacer coincidir y extraer valores de un mensaje de Azure Event Hubs mediante JSONPath.

|Propiedad|Descripción|Ejemplo|
|--------|-----------|-------|
|TypeName|Tipo que se asociará a las medidas que coinciden con la plantilla.|`heartrate`|
|TypeMatchExpression|Expresión JSONPath que se evalúa con la carga de EventData. Si se encuentra un JToken coincidente, la plantilla se considera una coincidencia. Todas las expresiones posteriores se evalúan con el JToken extraído coincidente aquí.|`$..[?(@heartRate)]`|
|TimestampExpression|Expresión JSONPath para extraer el valor de marca de tiempo de occurrenceTimeUtc de la medida.|`$.matchedToken.endDate`|
|DeviceIdExpression|Expresión JSONPath para extraer el identificador del dispositivo.|`$.matchedToken.deviceId`|
|PatientIdExpression|*Obligatorio* cuando IdentityResolution está en **modo de** creación y *Opcional* cuando IdentityResolution está en **modo de** búsqueda. Expresión para extraer el identificador del paciente.|`$.matchedToken.patientId`|
|EncounterIdExpression|*Opcional:* expresión para extraer el identificador de encuentro.|`$.matchedToken.encounterId`|
|CorrelationIdExpression|*Opcional:* expresión para extraer el identificador de correlación. Esta salida se puede usar para agrupar valores en una sola observación en las asignaciones de destino de FHIR.|`$.matchedToken.correlationId`|
|Values[].ValueName|Nombre que se asociará al valor extraído por la expresión siguiente. Se usa para enlazar el valor o componente deseado en la plantilla de asignación de destino de FHIR.|`hr`|
|Values[].ValueExpression|Expresión JSONPath para extraer el valor deseado.|`$.matchedToken.heartRate`|
|Values[].Required|Requerirá que el valor esté presente en la carga; Si no se encuentra, no se generará una medida y se creará una excepción InvalidOperationException.|`true`|

### <a name="examples"></a>Ejemplos

**Frecuencia cardíaca**

*Message*

```json
{
    "Body": {
        "heartRate": "78",
        "endDate": "2021-02-01T22:46:01.8750000Z",
        "deviceId": "device123"
    },
    "Properties": {},
    "SystemProperties": {}
}
```

*Plantilla*

```json
{
    "templateType": "JsonPathContent",
    "template": {
        "typeName": "heartrate",
        "typeMatchExpression": "$..[?(@heartRate)]",
        "deviceIdExpression": "$.deviceId",
        "timestampExpression": "$.endDate",
        "values": [
            {
                "required": "true",
                "valueExpression": "$.heartRate",
                "valueName": "hr"
            }
        ]
    }
}
```
**Presión sanguínea**

*Message*

```json
{
    "Body": {
        "systolic": "123",
        "diastolic" : "87",
        "endDate": "2021-02-01T22:46:01.8750000Z",
        "deviceId": "device123"
    },
    "Properties": {},
    "SystemProperties": {}
}
```

*Plantilla*

```json
{
    "typeName": "bloodpressure",
    "typeMatchExpression": "$..[?(@systolic && @diastolic)]",
    "deviceIdExpression": "$.deviceId",
    "timestampExpression": "$.endDate",
    "values": [
        {
            "required": "true",
            "valueExpression": "$.systolic",
            "valueName": "systolic"
        },
        {
            "required": "true",
            "valueExpression": "$.diastolic",
            "valueName": "diastolic"
        }
    ]
}
```
**Proyección de varias medidas de un solo mensaje**

*Message*

```json
{
    "Body": {
        "heartRate": "78",
        "steps": "2",
        "endDate": "2021-02-01T22:46:01.8750000Z",
        "deviceId": "device123"
    },
    "Properties": {},
    "SystemProperties": {}
}
```

*Plantilla 1*

```json
{
    "templateType": "JsonPathContent",
    "template": {
        "typeName": "heartrate",
        "typeMatchExpression": "$..[?(@heartRate)]",
        "deviceIdExpression": "$.deviceId",
        "timestampExpression": "$.endDate",
        "values": [
            {
                "required": "true",
                "valueExpression": "$.heartRate",
                "valueName": "hr"
            }
        ]
    }
}
```

*Plantilla 2*

```json
{
    "templateType": "JsonPathContent",
    "template": {
        "typeName": "stepcount",
        "typeMatchExpression": "$..[?(@steps)]",
        "deviceIdExpression": "$.deviceId",
        "timestampExpression": "$.endDate",
        "values": [
            {
                "required": "true",
                "valueExpression": "$.steps",
                "valueName": "steps"
            }
        ]
    }
}
```

**Proyección de varias medidas de la matriz en el mensaje**

*Message*

```json
{
    "Body": [
        {
            "heartRate": "78",
            "endDate": "2021-02-01T22:46:01.8750000Z",
            "deviceId": "device123"
        },
        {
            "heartRate": "81",
            "endDate": "2021-02-01T23:46:01.8750000Z",
            "deviceId": "device123"
        },
        {
            "heartRate": "72",
            "endDate": "2021-02-01T24:46:01.8750000Z",
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
    "templateType": "JsonPathContent",
    "template": {
        "typeName": "heartrate",
        "typeMatchExpression": "$..[?(@heartRate)]",
        "deviceIdExpression": "$.deviceId",
        "timestampExpression": "$.endDate",
        "values": [
            {
                "required": "true",
                "valueExpression": "$.heartRate",
                "valueName": "hr"
            }
        ]
    }
}
```

## <a name="collectioncontenttemplate"></a>CollectionContentTemplate

CollectionContentTemplate puede usarse para representar una lista de plantillas que se usarán durante la normalización.
                                                             
### <a name="example"></a>Ejemplo

```json
{
  "templateType": "CollectionContent",
  "template": [
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
    },
    {
      "templateType": "CalculatedContent",
      "template": {
        "typeName": "stepcount",
        "typeMatchExpression": "$..[?(@steps)]",
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
  ]
}
```

## <a name="calculatedcontenttemplate"></a>CalculatedContentTemplate

El conector de IoT proporciona una plantilla de contenido basada en expresiones para que coincida con la plantilla que se quiere y extraer valores. JSONPath o JmesPath pueden usar **expresiones.** Cada expresión dentro de la plantilla puede elegir su propio lenguaje de expresiones. 

> [!NOTE]
> Si no se define un lenguaje de expresiones, se usará el lenguaje de expresiones predeterminado configurado para la plantilla. El valor predeterminado es JSONPath, pero se puede sobrescribir si es necesario.

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
> El lenguaje de expresiones predeterminado que se va a usar para una plantilla de asignación de dispositivos es JsonPath. Si desea usar JsonPath, se puede proporcionar la expresión por sí sola.

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

CalculatedContentTemplate permite hacer coincidir y extraer valores de un mensaje de Azure Event Hubs mediante **expresiones,** tal como se define a continuación:

|Propiedad|Descripción|Ejemplo|
|--------|-----------|-------|
|TypeName|Tipo que se asociará a las medidas que coinciden con la plantilla.|`heartrate`|
|TypeMatchExpression|Expresión que se evalúa con respecto a la carga de EventData. Si se encuentra un JToken coincidente, la plantilla se considera una coincidencia. Todas las expresiones posteriores se evalúan con el JToken extraído coincidente aquí.|`$..[?(@heartRate)]`|
|TimestampExpression|Expresión para extraer el valor de marca de tiempo para occurrenceTimeUtc de la medida.|`$.matchedToken.endDate`|
|DeviceIdExpression|Expresión para extraer el identificador del dispositivo.|`$.matchedToken.deviceId`|
|PatientIdExpression|*Se* requiere cuando IdentityResolution está en **modo De** creación y *Opcional* cuando IdentityResolution está en **modo de** búsqueda. Expresión para extraer el identificador del paciente.|`$.matchedToken.patientId`|
|EncounterIdExpression|*Opcional:* expresión para extraer el identificador de encuentro.|`$.matchedToken.encounterId`|
|CorrelationIdExpression|*Opcional:* expresión para extraer el identificador de correlación. Esta salida se puede usar para agrupar valores en una sola observación en las asignaciones de destino de FHIR.|`$.matchedToken.correlationId`|
|Values[].ValueName|Nombre que se asociará al valor extraído por la expresión siguiente. Se usa para enlazar el valor o componente deseados en la plantilla de asignación de destino de FHIR.|`hr`|
|Values[].ValueExpression|Expresión para extraer el valor deseado.|`$.matchedToken.heartRate`|
|Values[].Required|Requerirá que el valor esté presente en la carga; Si no se encuentra, no se generará una medida y se creará una excepción InvalidOperationException.|`true`|

### <a name="expression-languages"></a>Lenguajes de expresiones

Al especificar el lenguaje que se usará para la expresión, los valores siguientes son válidos:

| Lenguaje de expresiones | Value        |
|---------------------|--------------|
| JSONPath            | **JsonPath** |
| JmesPath            | **JmesPath** |

>[!TIP]
>Para obtener más información sobre JSONPath, vea [JSONPath](https://goessner.net/articles/JsonPath/). [CalculatedContentTemplate usa](#calculatedcontenttemplate) la implementación [json de .NET](https://www.newtonsoft.com/json/help/html/QueryJsonSelectTokenJsonPath.htm) para resolver expresiones JSONPath.
>
>Para obtener más información sobre JmesPath, vea [JmesPath](https://jmespath.org/specification.html). [CalculatedContentTemplate usa](#calculatedcontenttemplate) la implementación de .NET de [JmesPath](https://github.com/jdevillard/JmesPath.Net) para resolver expresiones JmesPath.

### <a name="custom-functions"></a>Funciones personalizadas

También hay disponible un conjunto de funciones personalizadas del conector de IoT. Estas funciones personalizadas están fuera de las funciones proporcionadas como parte de la especificación JmesPath. Para más información sobre las funciones personalizadas del conector de IoT, consulte [Funciones de cliente del conector de IoT.](./iot-connector-custom-functions.md)

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

Se extraerán dos coincidencias con la expresión anterior y se usarán para crear JTokens. Las expresiones posteriores se evaluarán mediante los siguientes JTokens:

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

**Project Varias medidas a partir de un solo mensaje**

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

**Project Varias medidas de la matriz en el mensaje**

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

**Project Datos del token coincidente y el evento original**

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
## <a name="iotjsonpathcontenttemplate"></a>IotJsonPathContentTemplate

IotJsonPathContentTemplate es similar a JsonPathContentTemplate, excepto `DeviceIdExpression` y `TimestampExpression` no son necesarios.

La suposición, al usar esta plantilla, es que los mensajes que se evalúan se enviaron mediante los SDK de dispositivo [de Azure IoT Hub](../../iot-hub/iot-hub-devguide-sdks.md#azure-iot-hub-device-sdks) o la característica Exportar datos [(heredado)](../../iot-central/core/howto-export-data-legacy.md) de [Azure IoT Central](../../iot-central/core/overview-iot-central.md). 

Cuando se usan estos SDK, se conocen la identidad del dispositivo y la marca de tiempo del mensaje.

>[!IMPORTANT]
>Asegúrese de que usa un identificador de dispositivo de Azure Iot Hub o Azure IoT Central que está registrado como identificador para un recurso de dispositivo en el servicio FHIR de destino.

Si usa los SDK de dispositivo de Azure IoT Hub, todavía puede usar JsonPathContentTemplate, suponiendo que está usando propiedades personalizadas en el cuerpo del mensaje para la marca de tiempo de la identidad o la medida del dispositivo.

> [!NOTE]
> Cuando se `IotJsonPathContentTemplate` usa , `TypeMatchExpression` debe resolverse en el mensaje completo como JToken. Para obtener más información, vea los ejemplos siguientes:

### <a name="examples"></a>Ejemplos

**Frecuencia cardíaca**

*Message*

```json
{
    "Body": {
        "heartRate": "78"        
    },
    "Properties": {
        "iothub-creation-time-utc" : "2021-02-01T22:46:01.8750000Z"
    },
    "SystemProperties": {
        "iothub-connection-device-id" : "device123"
    }
}
```

*Plantilla*

```json

    "templateType": "JsonPathContent",
    "template": {
        "typeName": "heartrate",
        "typeMatchExpression": "$..[?(@Body.heartRate)]",
        "deviceIdExpression": "$.deviceId",
        "timestampExpression": "$.endDate",
        "values": [
            {
                "required": "true",
                "valueExpression": "$.Body.heartRate",
                "valueName": "hr"
            }
        ]
    }
}
```

**Presión sanguínea**

*Message*

```json
{
    "Body": {
        "systolic": "123",
        "diastolic" : "87"
    },
    "Properties": {
        "iothub-creation-time-utc" : "2021-02-01T22:46:01.8750000Z"
    },
    "SystemProperties": {
        "iothub-connection-device-id" : "device123"
    }
}
```

*Plantilla*

```json
{
    "typeName": "bloodpressure",
    "typeMatchExpression": "$..[?(@Body.systolic && @Body.diastolic)]",
    "values": [
        {
            "required": "true",
            "valueExpression": "$.Body.systolic",
            "valueName": "systolic"
        },
        {
            "required": "true",
            "valueExpression": "$.Body.diastolic",
            "valueName": "diastolic"
        }
    ]
}
```
## <a name="iotcentraljsonpathcontenttemplate"></a>IotCentralJsonPathContentTemplate

IotCentralJsonPathContentTemplate no requiere DeviceIdExpression ni TimestampExpression. Se usa cuando los mensajes que se evalúan se envían a través de la característica [Exportar datos](../../iot-central/core/howto-export-data.md) de [Azure IoT Central](../../iot-central/core/overview-iot-central.md). 

Si usa la característica Exportación de datos de Azure IoT Central y propiedades personalizadas en el cuerpo del mensaje para la identidad del dispositivo o la marca de tiempo de medición, todavía puede usar JsonPathContentTemplate.

> [!NOTE]
> Cuando se `IotCentralJsonPathContentTemplate` usa , `TypeMatchExpression` debe resolverse en el mensaje completo como JToken. Para obtener más información, vea los ejemplos siguientes:
 
### <a name="examples"></a>Ejemplos

**Frecuencia cardíaca**

*Message*

```json
{
    "applicationId": "1dffa667-9bee-4f16-b243-25ad4151475e",
    "messageSource": "telemetry",
    "deviceId": "1vzb5ghlsg1",
    "schema": "default@v1",
    "templateId": "urn:qugj6vbw5:___qbj_27r",
    "enqueuedTime": "2020-08-05T22:26:55.455Z",
    "telemetry": {
        "Activity": "running",
        "BloodPressure": {
            "Diastolic": 7,
            "Systolic": 71
        },
        "BodyTemperature": 98.73447010562934,
        "HeartRate": 88,
        "HeartRateVariability": 17,
        "RespiratoryRate": 13
    },
    "enrichments": {
      "userSpecifiedKey": "sampleValue"
    },
    "messageProperties": {
      "messageProp": "value"
    }
}
```

*Plantilla*

```json
{
    "templateType": "IotCentralJsonPathContent",
    "template": {
        "typeName": "heartrate",
        "typeMatchExpression": "$..[?(@telemetry.HeartRate)]",
        "values": [
            {
                "required": "true",
                "valueExpression": "$.telemetry.HeartRate",
                "valueName": "hr"
            }
        ]
    }
}
```

**Presión sanguínea**

*Message*

```json
{
    "applicationId": "1dffa667-9bee-4f16-b243-25ad4151475e",
    "messageSource": "telemetry",
    "deviceId": "1vzb5ghlsg1",
    "schema": "default@v1",
    "templateId": "urn:qugj6vbw5:___qbj_27r",
    "enqueuedTime": "2020-08-05T22:26:55.455Z",
    "telemetry": {
        "Activity": "running",
        "BloodPressure": {
            "Diastolic": 7,
            "Systolic": 71
        },
        "BodyTemperature": 98.73447010562934,
        "HeartRate": 88,
        "HeartRateVariability": 17,
        "RespiratoryRate": 13
    },
    "enrichments": {
      "userSpecifiedKey": "sampleValue"
    },
    "messageProperties": {
      "messageProp": "value"
    }
}
```

*Plantilla*

```json
{
    "templateType": "IotCentralJsonPathContent",
    "template": {
        "typeName": "bloodPressure",
        "typeMatchExpression": "$..[?(@telemetry.BloodPressure.Diastolic && @telemetry.BloodPressure.Systolic)]",
        "values": [
            {
                "required": "true",
                "valueExpression": "$.telemetry.BloodPressure.Diastolic",
                "valueName": "bp_diastolic"
            },
            {
                "required": "true",
                "valueExpression": "$.telemetry.BloodPressure.Systolic",
                "valueName": "bp_systolic"
            }
        ]
    }
}
```

## <a name="next-steps"></a>Pasos siguientes

En este artículo, ha aprendido a usar asignaciones de dispositivos. Para obtener información sobre cómo usar las asignaciones de destino de FHIR, consulte

>[!div class="nextstepaction"]
>[Uso de asignaciones de destino de FHIR](how-to-use-fhir-mapping-iot.md)

(FHIR&#174;) es una marca registrada [de HL7](https://hl7.org/fhir/) y se usa con el permiso HL7.
