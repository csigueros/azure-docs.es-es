---
title: Asignaciones de IotCentralJsonPathContentTemplate en IoT Connector device mappings - Azure Healthcare API
description: En este artículo se describe cómo se asigna IotCentralJsonPathContent con IoT Connector de asignaciones de dispositivos.
author: msjasteppe
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: how-to
ms.date: 11/16/2021
ms.author: jasteppe
ms.openlocfilehash: 50c4d8bad51998f8077e2d5915748d0a19bd0a00
ms.sourcegitcommit: 0415f4d064530e0d7799fe295f1d8dc003f17202
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 11/17/2021
ms.locfileid: "132733657"
---
# <a name="how-to-use-iotcentraljsonpathcontenttemplate-mappings"></a>Uso de asignaciones IotCentralJsonPathContentTemplate

> [!IMPORTANT]
> Azure Healthcare APIs se encuentra actualmente en VERSIÓN PRELIMINAR. Los [Términos de uso complementarios para las versiones preliminares de Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) incluyen términos legales adicionales que se aplican a las características de Azure que se encuentran en la versión beta, en versión preliminar o que todavía no se han publicado con disponibilidad general.

> [!TIP]
> Consulte la herramienta [Data Mapper](https://github.com/microsoft/iomt-fhir/tree/master/tools/data-mapper) del conector IoMT para editar, probar y solucionar problemas de las asignaciones de destino de dispositivos y FHIR del conector ioT. Exporte las asignaciones para cargar en el conector de IoT Azure Portal use con la versión [de código abierto](https://github.com/microsoft/iomt-fhir) del conector de IoT.

En este artículo se describe cómo usar asignaciones IoTCentralJsonPathContentTemplate con asignaciones de dispositivos del conector de IoT.

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
>[Uso de asignaciones de destino de FHIR](how-to-use-fhir-mappings.md)

(FHIR&#174;) es una marca comercial registrada [de HL7](https://hl7.org/fhir/) y se usa con el permiso HL7.
