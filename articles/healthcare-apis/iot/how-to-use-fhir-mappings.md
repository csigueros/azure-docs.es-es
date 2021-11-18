---
title: 'Asignaciones de destino de FHIR en el conector de IoT: API de Azure Healthcare'
description: En este artículo se describe cómo configurar y usar las asignaciones de destino de FHIR en el conector ioT de las API de Azure Healthcare.
author: msjasteppe
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: how-to
ms.date: 11/16/2021
ms.author: jasteppe
ms.openlocfilehash: c2c7043cd7dc5f234886144a97228db21a218621
ms.sourcegitcommit: 0415f4d064530e0d7799fe295f1d8dc003f17202
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 11/17/2021
ms.locfileid: "132733634"
---
# <a name="how-to-use-the-fhir-destination-mappings"></a>Uso de las asignaciones de destino de FHIR

> [!IMPORTANT]
> Azure Healthcare APIs se encuentra actualmente en VERSIÓN PRELIMINAR. Los [Términos de uso complementarios para las versiones preliminares de Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) incluyen términos legales adicionales que se aplican a las características de Azure que se encuentran en la versión beta, en versión preliminar o que todavía no se han publicado con disponibilidad general.

En este artículo se describe cómo configurar el conector de IoT mediante las asignaciones de destino Recursos Rápidos de Interoperabilidad en Salud (FHIR&#174;).

> [!TIP]
> Consulte la herramienta [Data Mapper](https://github.com/microsoft/iomt-fhir/tree/master/tools/data-mapper) del conector ioMT para editar, probar y solucionar problemas de asignaciones de dispositivos y destinos de FHIR del conector de IoT. Exporte asignaciones para cargar en el conector de IoT Azure Portal use con la versión [de código](https://github.com/microsoft/iomt-fhir) abierto del conector de IoT.

A continuación se muestra un ejemplo conceptual de lo que sucede durante el proceso de normalización y transformación en el conector de IoT:

:::image type="content" source="media/iot-data-normalization-high-level.png" alt-text="Ejemplo de flujo de normalización de datos de IoT1" lightbox="media/iot-data-normalization-high-level.png":::

## <a name="fhir-destination-mappings"></a>Asignaciones de destino de FHIR

Una vez extraído el contenido del dispositivo en un modelo normalizado, los datos se recopilan y agrupan según el identificador de dispositivo, el tipo de medida y el período de tiempo. La salida de esta agrupación se envía para su conversión en un recurso FHIR (actualmente [Observation](https://www.hl7.org/fhir/observation.html) [Observación]). La plantilla de asignación de destino de FHIR controla cómo se asignan los datos a una observación de FHIR. ¿Debe crearse un recurso de observación para un momento o para un período de una hora? ¿Qué códigos se deben agregar al recurso de observación? ¿Debe representarse el valor [como SampledData](https://www.hl7.org/fhir/datatypes.html#SampledData) o [quantity](https://www.hl7.org/fhir/datatypes.html#Quantity)? Estos tipos de datos son todas las opciones que los controles de configuración de asignaciones de destino de FHIR.

> [!NOTE]
> Las asignaciones se almacenan en un almacenamiento de blobs subyacente y se cargan desde la ejecución de blobs por proceso. Al actualizarse, se aplican inmediatamente. 

### <a name="codevaluefhirtemplate"></a>CodeValueFhirTemplate

CodeValueFhirTemplate es actualmente la única plantilla admitida en la asignación de destino de FHIR en este momento.  Permite definir los códigos, el período efectivo y el valor del recurso de observación. Se admiten varios tipos de valor: [SampledData](https://www.hl7.org/fhir/datatypes.html#SampledData), [CodeableConcept](https://www.hl7.org/fhir/datatypes.html#CodeableConcept) y [Quantity](https://www.hl7.org/fhir/datatypes.html#Quantity). Junto con estos valores configurables, el identificador del recurso Observation (Observación) y la vinculación a los recursos de dispositivo y paciente adecuados se administran automáticamente.

| Propiedad | Descripción 
| --- | ---
|**TypeName**| Tipo de medida a la que se debe enlazar esta plantilla. Debe haber al menos una plantilla Device mapping (Asignación de dispositivos) que genere este tipo.
|**PeriodInterval**|Tiempo que debe representar el recurso de observación creado. Los valores admitidos son 0 (una instancia), 60 (una hora), 1440 (un día).
|**Categoría**|Cualquier número de [CodeableConcepts](http://hl7.org/fhir/datatypes-definitions.html#codeableconcept) para clasificar el tipo de recurso de observación que se ha creado.
|**Codes**|Uno o varios valores de [Coding](http://hl7.org/fhir/datatypes-definitions.html#coding) que aplicar al recurso de observación creado.
|**Codes[].Code**|Código para [Coding](http://hl7.org/fhir/datatypes-definitions.html#coding).
|**Codes[].System**|Sistema de [Coding](http://hl7.org/fhir/datatypes-definitions.html#coding).
|**Codes[].Display**|Presentación de [Coding](http://hl7.org/fhir/datatypes-definitions.html#coding).
|**Valor**|Valor que se va a extraer y representar en el recurso de observación. Para más información, consulte [Plantillas de tipo de valor](#value-type-templates).
|**Componentes**|*Opcional:* uno o más componentes que se van a crear en el recurso de observación.
|**Components[].Codes**|Uno o varios valores de [Coding](http://hl7.org/fhir/datatypes-definitions.html#coding) que aplicar al componente.
|**Components[].Value**|Valor que se va a extraer y representar en el componente. Para más información, consulte [Plantillas de tipo de valor](#value-type-templates).

### <a name="value-type-templates"></a>Plantillas de tipo de valor 

A continuación se muestran las de tipo de valor admitidas:

#### <a name="sampleddata"></a>SampledData

Representa el [tipo de datos SampledData](http://hl7.org/fhir/datatypes.html#SampledData) FHIR. Las medidas de observación se escriben en una secuencia de valores a partir de un momento dado y se incrementan hacia delante mediante el período definido. Si no hay ningún valor, se escribirá `E` en el flujo de datos. Si en el período dos valores más ocupan la misma posición en el flujo de datos, se usa el más reciente. La misma lógica se aplica cuando se actualiza un recurso de observación mediante SampledData.

| Propiedad | Descripción 
| --- | ---
|**DefaultPeriod**|Período predeterminado, en milisegundos, que se va a usar. 
|**Unidad**|Unidad que se va a establecer en el origen de SampledData. 

#### <a name="quantity"></a>Cantidad

Representa el tipo de datos [Quantity](http://hl7.org/fhir/datatypes.html#Quantity) de FHIR. Si hay más de un valor en la agrupación, solo se usa el primero. Cuando llega un nuevo valor que se asigna al mismo recurso de observación, sobrescribe el valor anterior.

| Propiedad | Descripción 
| --- | --- 
|**Unidad**| Representación de la unidad.
|**Código**| Formato codificado de la unidad.
|**Sistema**| Sistema que define el formato de unidad codificado.

### <a name="codeableconcept"></a>CodeableConcept

Representa el tipo de datos [CodeableConcept](http://hl7.org/fhir/datatypes.html#CodeableConcept) de FHIR. No se usa el valor real.

| Propiedad | Descripción 
| --- | --- 
|**Texto**|Representación de texto sin formato. 
|**Codes**|Uno o varios valores de [Coding](http://hl7.org/fhir/datatypes-definitions.html#coding) que aplicar al recurso de observación creado.
|**Codes[].Code**|Código para [Coding](http://hl7.org/fhir/datatypes-definitions.html#coding).
|**Codes[].System**|Sistema de [Coding](http://hl7.org/fhir/datatypes-definitions.html#coding).
|**Codes[].Display**|Presentación de [Coding](http://hl7.org/fhir/datatypes-definitions.html#coding).

### <a name="examples"></a>Ejemplos

**Frecuencia cardíaca: SampledData**

```json
{
    "templateType": "CodeValueFhir",
    "template": {
        "codes": [
            {
                "code": "8867-4",
                "system": "http://loinc.org",
                "display": "Heart rate"
            }
        ],
        "periodInterval": 60,
        "typeName": "heartrate",
        "value": {
            "defaultPeriod": 5000,
            "unit": "count/min",
            "valueName": "hr",
            "valueType": "SampledData"
        }
    }
}
```

**Pasos: SampledData**

```json
{
    "templateType": "CodeValueFhir",
    "template": {
        "codes": [
            {
                "code": "55423-8",
                "system": "http://loinc.org",
                "display": "Number of steps"
            }
        ],        
        "periodInterval": 60,
        "typeName": "stepsCount",
        "value": {
            "defaultPeriod": 5000,
            "unit": "",
            "valueName": "steps",
            "valueType": "SampledData"
        }
    }
}
```

**Presión sanguínea: SampledData**

```json
{
    "templateType": "CodeValueFhir",
    "template": {
        "codes": [
            {
                "code": "85354-9",
                "display": "Blood pressure panel with all children optional",
                "system": "http://loinc.org"
            }
        ],
        "periodInterval": 60,
        "typeName": "bloodpressure",
        "components": [
            {
                "codes": [
                    {
                        "code": "8867-4",
                        "display": "Diastolic blood pressure",
                        "system": "http://loinc.org"
                    }
                ],
                "value": {
                    "defaultPeriod": 5000,
                    "unit": "mmHg",
                    "valueName": "diastolic",
                    "valueType": "SampledData"
                }
            },
            {
                "codes": [
                    {
                        "code": "8480-6",
                        "display": "Systolic blood pressure",
                        "system": "http://loinc.org"
                    }
                ],
                "value": {
                    "defaultPeriod": 5000,
                    "unit": "mmHg",
                    "valueName": "systolic",
                    "valueType": "SampledData"
                }
            }
        ]
    }
}
```

**Presión sanguínea: Quantity**

```json
{
    "templateType": "CodeValueFhir",
    "template": {
        "codes": [
            {
                "code": "85354-9",
                "display": "Blood pressure panel with all children optional",
                "system": "http://loinc.org"
            }
        ],
        "periodInterval": 0,
        "typeName": "bloodpressure",
        "components": [
            {
                "codes": [
                    {
                        "code": "8867-4",
                        "display": "Diastolic blood pressure",
                        "system": "http://loinc.org"
                    }
                ],
                "value": {
                    "unit": "mmHg",
                    "valueName": "diastolic",
                    "valueType": "Quantity"
                }
            },
            {
                "codes": [
                    {
                        "code": "8480-6",
                        "display": "Systolic blood pressure",
                        "system": "http://loinc.org"
                    }
                ],
                "value": {
                    "unit": "mmHg",
                    "valueName": "systolic",
                    "valueType": "Quantity"
                }
            }
        ]
    }
}
```

**Dispositivo eliminado: CodeableConcept**

```json
{
    "templateType": "CodeValueFhir",
    "template": {
        "codes": [
            {
                "code": "deviceEvent",
                "system": "https://www.mydevice.com/v1",
                "display": "Device Event"
            }
        ],
        "periodInterval": 0,
        "typeName": "deviceRemoved",
        "value": {
            "text": "Device Removed",
            "codes": [
                {
                    "code": "deviceRemoved",
                    "system": "https://www.mydevice.com/v1",
                    "display": "Device Removed"
                }
            ],
            "valueName": "deviceRemoved",
            "valueType": "CodeableConcept"
        }
    }
}
```

## <a name="next-steps"></a>Pasos siguientes

En este artículo, ha aprendido a usar las asignaciones de destino de FHIR. Para obtener información sobre cómo usar asignaciones de dispositivos, consulte

>[!div class="nextstepaction"]
>[Uso de asignaciones de dispositivos](how-to-use-device-mappings.md)

(FHIR&#174;) es una marca comercial registrada de [HL7](https://hl7.org/fhir/) y se usa con el permiso hl7.
