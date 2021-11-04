---
title: 'Plantilla de asignación de dispositivo en conector de IoT: Azure Healthcare APIs'
description: En este artículo se describe cómo usar la plantilla asignación de dispositivos en el IoT Connector.
author: msjasteppe
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: conceptual
ms.date: 10/26/2021
ms.author: jasteppe
ms.openlocfilehash: 3550bab190c2b5bbec5a03faa8131094003ee85b
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 11/03/2021
ms.locfileid: "131446018"
---
# <a name="how-to-use-device-mapping"></a>Uso de la asignación de dispositivos

> [!IMPORTANT]
> Azure Healthcare APIs se encuentra actualmente en VERSIÓN PRELIMINAR. Los [Términos de uso complementarios para las versiones preliminares de Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) incluyen términos legales adicionales que se aplican a las características de Azure que se encuentran en la versión beta, en versión preliminar o que todavía no se han publicado con disponibilidad general.

El conector de IoT requiere dos tipos de asignaciones basadas en JSON. El primer tipo, **Device mapping** (Asignación de dispositivos), es responsable de la asignación de las cargas de dispositivo enviadas al punto de conexión `devicedata` de Azure Event Hub. Extrae tipos, identificadores de dispositivo, fecha y hora de medición, y valores de medida. 

El segundo tipo, **Recursos Rápidos de Interoperabilidad en Salud de destino (FHIR&#174;),** controla la asignación del recurso de FHIR. Permite configurar la duración del período de observación, el tipo de datos FHIR que se usa para almacenar los valores y los códigos terminológicos. 

Los dos tipos de asignaciones se componen en un documento JSON en función de su tipo. A continuación, estos documentos JSON se agregan al conector de IoT a través del Azure Portal. El documento Asignación de dispositivos se agrega a través de la **página Asignación de** dispositivos y el documento de asignación de destino de FHIR a través de **la página Destino.**

> [!NOTE]
> Las asignaciones se almacenan en un almacenamiento de blobs subyacente y se cargan desde la ejecución de blobs por proceso. Al actualizarse, se aplican inmediatamente. 

> [!TIP]
> Consulte la herramienta [Data Mapper](https://github.com/microsoft/iomt-fhir/tree/master/tools/data-mapper) del conector ioMT para editar, probar y solucionar problemas de asignaciones de dispositivos y destinos de FHIR del conector de IoT. Exporte asignaciones para cargar en el conector de IoT Azure Portal use con la versión [de código](https://github.com/microsoft/iomt-fhir) abierto del conector de IoT.

## <a name="device-mapping"></a>Asignación de dispositivos

La asignación de dispositivos proporciona funcionalidad para extraer el contenido del dispositivo en un formato común para su evaluación. Los mensajes que se reciben se comparan con todas las plantillas. Este enfoque permite que un único mensaje entrante se proyecte en varios mensajes salientes, que después se asignan a diferentes observaciones en FHIR. El resultado es un objeto de datos normalizado que representa los valores analizados con las plantillas. Es necesario buscar y extraer algunas propiedades obligatorias en el modelo de datos normalizado:

| Propiedad             | Descripción                                                                                                                                                                                                                                                   |
|----------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| **Type**             | Nombre/tipo para clasificar la medida. Este valor se usa para enlazar a la asignación de destino de FHIR necesaria.  Varias asignaciones pueden generarse en el mismo tipo, lo que le permite asignar diferentes representaciones en varios dispositivos a una única salida común. |
| **OccurenceTimeUtc** | Hora a la que se realizó la medida.                                                                                                                                                                                                                            |
| **DeviceId**         | Identificador del dispositivo. Este valor debe coincidir con un identificador en el recurso de dispositivo que existe en el servicio FHIR de destino.                                                                                                                       |
| **Propiedades**       | Extraiga al menos una propiedad para que el valor se pueda guardar en el recurso de observación creado.  Las propiedades son una colección de pares clave-valor que se extraen durante la normalización.                                                                                  |

A continuación se muestra un ejemplo conceptual de lo que sucede durante la normalización.

![Ejemplos de normalización](media/concepts-iot-mapping-templates/normalization-example.png#lightbox)

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
### <a name="mapping-with-json-path"></a>Asignación con ruta de acceso JSON

Los tres tipos de asignación de contenido de dispositivo admitidos hoy en día se basan en json Path para que coincidan con la asignación necesaria y los valores extraídos. Más información sobre la ruta de acceso JSON [aquí](https://goessner.net/articles/JsonPath/). Los tres tipos de plantilla usan la [implementación de .NET de JSON](https://www.newtonsoft.com/json/help/html/QueryJsonSelectTokenJsonPath.htm) para resolver expresiones de ruta de acceso JSON.

#### <a name="jsonpathcontenttemplate"></a>JsonPathContentTemplate

JsonPathContentTemplate permite hacer coincidir valores de un mensaje del centro de eventos mediante la ruta de acceso JSON y extraerlos.

| Propiedad | Descripción |Ejemplo |
| --- | --- | --- |
|**TypeName**|Tipo que se va a asociar a las medidas que coincidan con la plantilla.|`heartrate`
|**TypeMatchExpression**|Expresión de la ruta de acceso JSON que se evalúa con la carga del centro de eventos. Si se encuentra un JToken coincidente, la plantilla se considera una coincidencia. Todas las expresiones subsiguientes se evalúan con los valores JToken coincidentes de aquí.|`$..[?(@heartRate)]`
|**TimestampExpression**|Expresión de la ruta de acceso JSON para extraer el valor de marca de tiempo para OccurenceTimeUtc de la medida.|`$.endDate`
|**DeviceIdExpression**|Expresión de la ruta de acceso JSON para extraer el identificador de dispositivo.|`$.deviceId`
|**PatientIdExpression**|*Opcional*: expresión de la ruta de acceso JSON para extraer el identificador del paciente.|`$.patientId`
|**EncounterIdExpression**|*Opcional*: expresión de la ruta de acceso JSON para extraer el identificador del encuentro.|`$.encounterId`
|**Values[].ValueName**|Nombre que se va a asociar al valor extraído por la expresión subsiguiente. Se usa para enlazar el valor o componente necesarios en la asignación de destino de FHIR. |`hr`
|**Values[].ValueExpression**|Expresión de la ruta de acceso JSON para extraer el valor necesario.|`$.heartRate`
|**Values[].Required**|Requerirá que el valor esté presente en la carga;  si no se encuentra, no se generará ninguna medida y se producirá una excepción InvalidOperationException.|`true`

##### <a name="examples"></a>Ejemplos

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
#### <a name="iotjsonpathcontenttemplate"></a>IotJsonPathContentTemplate

IotJsonPathContentTemplate es similar a JsonPathContentTemplate, salvo que DeviceIdExpression y TimestampExpression no son necesarios.

Al usar esta plantilla se supone que los mensajes que se están evaluando se enviaron mediante los [SDK de dispositivo de Azure IoT Hub](../../iot-hub/iot-hub-devguide-sdks.md#azure-iot-hub-device-sdks) o la característica [Exportar datos (heredado)](../../iot-central/core/howto-export-data-legacy.md) de [Azure IoT Central](../../iot-central/core/overview-iot-central.md). Cuando se usan estos SDK, la identidad del dispositivo (suponiendo que el identificador de dispositivo de Azure Iot Hub/Central se registra como identificador de un recurso de dispositivo en el servicio FHIR de destino) y la marca de tiempo del mensaje se conocen. Puede usar JsonPathContentTemplate aunque use el SDK de dispositivo de Azure IoT Hub, si hay propiedades personalizadas en el cuerpo del mensaje correspondientes a la identidad del dispositivo o a la marca de tiempo de la medida.

> [!NOTE]
> Cuando se usa `IotJsonPathContentTemplate`, `TypeMatchExpression` debe resolverse en el mensaje completo como JToken. Consulte los siguientes ejemplos para obtener más información.

##### <a name="examples"></a>Ejemplos

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
#### <a name="iotcentraljsonpathcontenttemplate"></a>IotCentralJsonPathContentTemplate

IotCentralJsonPathContentTemplate no requiere DeviceIdExpression ni TimestampExpression. Se usa cuando los mensajes que se evalúan se envían a través de la característica [Exportar datos](../../iot-central/core/howto-export-data.md) de [Azure IoT Central](../../iot-central/core/overview-iot-central.md). Al usar esta característica, se conocen la identidad del dispositivo (suponiendo que el identificador de dispositivo de Azure IoT Central se haya registrado como identificador de un recurso de dispositivo en el servidor de FHIR de destino) y la marca de tiempo del mensaje. Si usa la característica de exportación de datos de Azure IoT Central, pero usa propiedades personalizadas en el cuerpo del mensaje correspondientes a la identidad del dispositivo o a la marca de tiempo de la medida, todavía puede usar JsonPathContentTemplate.

> [!NOTE]
> Cuando se usa IotCentralJsonPathContentTemplate, TypeMatchExpression debe resolverse en el mensaje completo como valor JToken. Consulte los siguientes ejemplos para obtener más información.
 
##### <a name="examples"></a>Ejemplos

**Frecuencia cardíaca**

*Message*

```json
{
    "applicationId": "1dffa667-9bee-4f16-b243-25ad4151475e",
    "messageSource": "telemetry",
    "deviceId": "1vzb5ghlsg1",
    "schema": "default@v1",
    "templateId": "urn:qugj6vbw5:___qbj_27r",
    "enqueuedTime": "2021-08-05T22:26:55.455Z",
    "telemetry": {
        "HeartRate": "88",
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
    "enqueuedTime": "2021-08-05T22:26:55.455Z",
    "telemetry": {
        "BloodPressure": {
            "Diastolic": "87",
            "Systolic": "123"
        }
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

>[!div class="nextstepaction"]
>[Uso de la asignación de destino de FHIR](how-to-use-fhir-mapping-iot.md)

(FHIR&#174;) es una marca comercial registrada de [HL7](https://hl7.org/fhir/) y se usa con el permiso de HL7.