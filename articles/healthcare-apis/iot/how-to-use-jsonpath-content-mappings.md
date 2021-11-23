---
title: 'Asignaciones de JsonPathContentTemplate en asignaciones de dispositivos del conector de IoT: API de Azure Healthcare'
description: En este artículo se describe cómo usar asignaciones JsonPathContentTemplate con plantillas de asignaciones de dispositivos del conector de IoT.
author: msjasteppe
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: how-to
ms.date: 11/22/2021
ms.author: jasteppe
ms.openlocfilehash: 9c8ce02be3301bdfaf8d151ef84e1475edaa6b03
ms.sourcegitcommit: 3d04177023a3136832adb561da831ccc8e9910c7
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 11/23/2021
ms.locfileid: "132937267"
---
# <a name="how-to-use-jsonpathcontenttemplate-mappings"></a>Uso de asignaciones JsonPathContentTemplate

> [!IMPORTANT]
> Azure Healthcare APIs se encuentra actualmente en VERSIÓN PRELIMINAR. Los [Términos de uso complementarios para las versiones preliminares de Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) incluyen términos legales adicionales que se aplican a las características de Azure que se encuentran en la versión beta, en versión preliminar o que todavía no se han publicado con disponibilidad general.

> [!TIP]
> Consulte la herramienta [Data Mapper](https://github.com/microsoft/iomt-fhir/tree/master/tools/data-mapper) del conector ioMT para editar, probar y solucionar problemas de asignaciones de dispositivos y destinos de FHIR del conector de IoT. Exporte asignaciones para cargar en el conector de IoT Azure Portal use con la versión [de código abierto](https://github.com/microsoft/iomt-fhir) del conector de IoT.

En este artículo se describe cómo usar asignaciones JsonPathContentTemplate con plantillas de asignaciones de dispositivos del conector de IoT.

## <a name="jsonpathcontenttemplate"></a>JsonPathContentTemplate

JsonPathContentTemplate permite hacer coincidir y extraer valores de un mensaje de Azure Event Hub mediante JSONPath.

|Propiedad|Descripción|Ejemplo|
|--------|-----------|-------|
|TypeName|Tipo que se asociará a las medidas que coinciden con la plantilla.|`heartrate`|
|TypeMatchExpression|Expresión JSONPath que se evalúa con respecto a la carga de EventData. Si se encuentra un JToken coincidente, la plantilla se considera una coincidencia. Todas las expresiones posteriores se evalúan con el JToken extraído coincidente aquí.|`$..[?(@heartRate)]`|
|TimestampExpression|Expresión JSONPath para extraer el valor de marca de tiempo de occurrenceTimeUtc de la medida.|`$.matchedToken.endDate`|
|DeviceIdExpression|Expresión JSONPath para extraer el identificador del dispositivo.|`$.matchedToken.deviceId`|
|PatientIdExpression|*Se* requiere cuando IdentityResolution está en **modo De** creación y *Opcional* cuando IdentityResolution está en **modo de** búsqueda. Expresión para extraer el identificador del paciente.|`$.matchedToken.patientId`|
|EncounterIdExpression|*Opcional:* expresión para extraer el identificador de encuentro.|`$.matchedToken.encounterId`|
|CorrelationIdExpression|*Opcional:* expresión para extraer el identificador de correlación. Esta salida se puede usar para agrupar valores en una sola observación en las asignaciones de destino de FHIR.|`$.matchedToken.correlationId`|
|Values[].ValueName|Nombre que se asociará al valor extraído por la expresión siguiente. Se usa para enlazar el valor o componente deseados en la plantilla de asignación de destino de FHIR.|`hr`|
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

> [!TIP]
> Consulte la Guía de solución [de problemas del conector de](./iot-troubleshoot-guide.md) IoT para obtener ayuda para corregir errores y problemas comunes.

## <a name="next-steps"></a>Pasos siguientes

En este artículo, ha aprendido a usar asignaciones de dispositivos. Para obtener información sobre cómo usar las asignaciones de destino de FHIR, consulte

>[!div class="nextstepaction"]
>[Uso de asignaciones de destino de FHIR](how-to-use-fhir-mappings.md)

(FHIR&#174;) es una marca comercial registrada de [HL7](https://hl7.org/fhir/) y se usa con el permiso hl7.
