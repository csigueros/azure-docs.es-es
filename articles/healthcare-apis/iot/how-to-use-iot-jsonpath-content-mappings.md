---
title: Asignaciones de IotJsonPathContentTemplate en IoT Connector device mappings - Azure Healthcare API
description: En este artículo se describe cómo usar asignaciones IotJsonPathContentTemplate con IoT Connector de asignación de dispositivos.
author: msjasteppe
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: how-to
ms.date: 11/16/2021
ms.author: jasteppe
ms.openlocfilehash: 6a20c1130156a18da7b18a9454b5a37232a609be
ms.sourcegitcommit: 0415f4d064530e0d7799fe295f1d8dc003f17202
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 11/17/2021
ms.locfileid: "132733650"
---
# <a name="how-to-use-iotjsonpathcontenttemplate-mappings"></a>Uso de asignaciones IotJsonPathContentTemplate

> [!IMPORTANT]
> Azure Healthcare APIs se encuentra actualmente en VERSIÓN PRELIMINAR. Los [Términos de uso complementarios para las versiones preliminares de Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) incluyen términos legales adicionales que se aplican a las características de Azure que se encuentran en la versión beta, en versión preliminar o que todavía no se han publicado con disponibilidad general.

> [!TIP]
> Consulte la herramienta [Data Mapper](https://github.com/microsoft/iomt-fhir/tree/master/tools/data-mapper) del conector IoMT para editar, probar y solucionar problemas de las asignaciones de destino de dispositivos y FHIR del conector ioT. Exporte las asignaciones para cargar en el conector de IoT Azure Portal use con la versión [de código abierto](https://github.com/microsoft/iomt-fhir) del conector de IoT.

En este artículo se describe cómo usar las asignaciones de IoTJsonPathContentTemplate con plantillas de asignaciones de dispositivos del conector de IoT.

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

## <a name="next-steps"></a>Pasos siguientes

En este artículo, ha aprendido a usar asignaciones de dispositivos. Para obtener información sobre cómo usar las asignaciones de destino de FHIR, consulte

>[!div class="nextstepaction"]
>[Uso de asignaciones de destino de FHIR](how-to-use-fhir-mappings.md)

(FHIR&#174;) es una marca registrada [de HL7](https://hl7.org/fhir/) y se usa con el permiso HL7.
