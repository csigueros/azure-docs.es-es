---
title: 'Asignaciones de CollectionContentTemplate en IoT Connector Device Mappings: API de Azure Healthcare'
description: En este artículo se describe cómo usar asignaciones CollectionContentTemplate con asignaciones de dispositivos del conector de IoT.
author: msjasteppe
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: how-to
ms.date: 11/16/2021
ms.author: jasteppe
ms.openlocfilehash: 5fc15f07e2263598b758fe18945ae1b6f1323292
ms.sourcegitcommit: 0415f4d064530e0d7799fe295f1d8dc003f17202
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 11/17/2021
ms.locfileid: "132733641"
---
# <a name="how-to-use-collectioncontenttemplate-mappings"></a>Uso de asignaciones CollectionContentTemplate

> [!IMPORTANT]
> Azure Healthcare APIs se encuentra actualmente en VERSIÓN PRELIMINAR. Los [Términos de uso complementarios para las versiones preliminares de Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) incluyen términos legales adicionales que se aplican a las características de Azure que se encuentran en la versión beta, en versión preliminar o que todavía no se han publicado con disponibilidad general.

> [!TIP]
> Consulte la herramienta [Data Mapper](https://github.com/microsoft/iomt-fhir/tree/master/tools/data-mapper) del conector IoMT para editar, probar y solucionar problemas de las asignaciones de destino de dispositivos y FHIR del conector ioT. Exporte las asignaciones para cargar en el conector de IoT Azure Portal use con la versión [de código abierto](https://github.com/microsoft/iomt-fhir) del conector de IoT.

En este artículo se describe cómo usar asignaciones CollectionContentTemplate con plantillas de asignaciones de dispositivos del conector de IoT.

## <a name="collectioncontenttemplate"></a>CollectionContentTemplate

CollectionContentTemplate se puede usar para representar una lista de plantillas que se usarán durante la normalización.
                                                             
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

## <a name="next-steps"></a>Pasos siguientes

En este artículo, ha aprendido a usar asignaciones de dispositivos. Para obtener información sobre cómo usar las asignaciones de destino de FHIR, consulte

>[!div class="nextstepaction"]
>[Uso de asignaciones de destino de FHIR](how-to-use-fhir-mappings.md)

(FHIR&#174;) es una marca registrada [de HL7](https://hl7.org/fhir/) y se usa con el permiso HL7.
