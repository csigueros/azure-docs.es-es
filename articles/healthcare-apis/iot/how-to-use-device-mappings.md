---
title: 'Asignaciones de dispositivos IoT Connector: API de Azure Healthcare'
description: En este artículo se describe cómo configurar y usar plantillas de asignación de dispositivos con las API de Azure Healthcare IoT Connector.
author: msjasteppe
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: how-to
ms.date: 11/22/2021
ms.author: jasteppe
ms.openlocfilehash: 4b86742fb3bb5cc5c642b4d97b96c6aebc7c887c
ms.sourcegitcommit: 3d04177023a3136832adb561da831ccc8e9910c7
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 11/23/2021
ms.locfileid: "132936587"
---
# <a name="how-to-use-device-mappings"></a>Uso de asignaciones de dispositivos

> [!IMPORTANT]
> Azure Healthcare APIs se encuentra actualmente en VERSIÓN PRELIMINAR. Los [Términos de uso complementarios para las versiones preliminares de Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) incluyen términos legales adicionales que se aplican a las características de Azure que se encuentran en la versión beta, en versión preliminar o que todavía no se han publicado con disponibilidad general.

> [!TIP]
> Consulte la herramienta [Data Mapper](https://github.com/microsoft/iomt-fhir/tree/master/tools/data-mapper) del conector ioMT para editar, probar y solucionar problemas de asignaciones de dispositivos y destinos de FHIR del conector de IoT. Exporte asignaciones para cargar en el conector de IoT Azure Portal use con la versión [de código abierto](https://github.com/microsoft/iomt-fhir) del conector de IoT.

En este artículo se describe cómo configurar el conector de IoT mediante asignaciones de dispositivos.

El conector de IoT requiere dos tipos de asignaciones basadas en JSON. El primer tipo, **Device mapping** (Asignación de dispositivos), es responsable de la asignación de las cargas de dispositivo enviadas al punto de conexión `devicedata` de Azure Event Hub. Extrae tipos, identificadores de dispositivo, fecha y hora de medición, y valores de medida. 

El segundo tipo, **Recursos Rápidos de Interoperabilidad en Salud de destino (FHIR&#174;),** controla la asignación del recurso de FHIR. Permite configurar la duración del período de observación, el tipo de datos FHIR que se usa para almacenar los valores y los códigos terminológicos. 

Los dos tipos de asignaciones se componen en un documento JSON en función de su tipo. A continuación, estos documentos JSON se agregan al conector de IoT a través del Azure Portal. El documento Asignación de dispositivos se agrega a través de la **página Asignación de** dispositivos y el documento de asignación de destino de FHIR a través de **la página Destino.**

> [!NOTE]
> Las asignaciones se almacenan en un almacenamiento de blobs subyacente y se cargan desde la ejecución de blobs por proceso. Al actualizarse, se aplican inmediatamente. 

## <a name="device-mappings-overview"></a>Introducción a las asignaciones de dispositivos

Las asignaciones de dispositivos proporcionan funcionalidad para extraer el contenido del mensaje del dispositivo en un formato común para su posterior evaluación. Cada mensaje de dispositivo recibido se evalúa con todas las plantillas de asignación de dispositivos. 

Un único mensaje de dispositivo de entrada se puede separar en varios mensajes salientes que posteriormente se asignan a diferentes observaciones en el servicio FHIR. 

El resultado es un objeto de datos normalizado que representa los valores analizados con las plantillas. 

Es necesario buscar y extraer algunas propiedades obligatorias en el modelo de datos normalizado:

|Propiedad|Descripción|
|--------|-----------|
|**Type**|Nombre/tipo para clasificar la medida. Este valor se usa para enlazar a la asignación de destino de FHIR necesaria. Varias asignaciones pueden generarse en el mismo tipo, lo que le permite asignar diferentes representaciones en varios dispositivos a una única salida común.|
|**OccurenceTimeUtc**|Hora a la que se realizó la medida.|
|**DeviceId**|Identificador del dispositivo. Este valor debe coincidir con un identificador en el recurso de dispositivo que existe en el servicio FHIR de destino.|
|**Propiedades**|Extraiga al menos una propiedad para que el valor se pueda guardar en el recurso de observación creado. Las propiedades son una colección de pares clave-valor que se extraen durante la normalización.|

> [!IMPORTANT]
> La interfaz [IMeasurement](https://github.com/microsoft/iomt-fhir/blob/master/src/lib/Microsoft.Health.Fhir.Ingest.Schema/IMeasurement.cs) define el modelo normalizado completo.

A continuación se muestran ejemplos conceptuales de lo que sucede durante el proceso de normalización y transformación en el conector de IoT:

:::image type="content" source="media/iot-data-normalization-high-level.png" alt-text="Ejemplo de flujo de normalización de datos de IoT1" lightbox="media/iot-data-normalization-high-level.png":::

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

Los cinco tipos de asignación de contenido de dispositivo admitidos actualmente se basan en JSONPath para que coincidan con la asignación necesaria y con los valores extraídos. Puede encontrar más información sobre JSONPath [aquí.](https://goessner.net/articles/JsonPath/) Los cinco tipos de plantilla usan la [implementación de .NET de JSON](https://www.newtonsoft.com/json/help/html/QueryJsonSelectTokenJsonPath.htm) para resolver expresiones JSONPath.

Puede definir una o varias plantillas dentro de la plantilla Asignación de dispositivos. Cada mensaje de dispositivo del centro de eventos recibido se evalúa con todas las plantillas de asignación de dispositivos. 

Un único mensaje de dispositivo de entrada se puede separar en varios mensajes salientes que posteriormente se asignan a diferentes observaciones en el servicio FHIR. 

Existen varios tipos de plantilla y se pueden usar al compilar el archivo de asignación de dispositivos.

|Nombre                                                                     | Descripción                                                                   |  
|-------------------------------------------------------------------------|-------------------------------------------------------------------------------|
|[JsonPathContentTemplate](./how-to-use-jsonpath-content-mappings.md)     |Plantilla que admite la escritura de expresiones mediante JsonPath                  
|[CollectionContentTemplate](./how-to-use-collection-content-mappings.md) |Plantilla que se usa para representar una lista de plantillas que se usarán durante la normalización.                                                            |                                                           
|[CalculatedContentTemplate](./how-to-use-calculated-functions-mappings.md)|Plantilla que admite la escritura de expresiones mediante uno de varios lenguajes de expresiones. Admite la transformación de datos mediante el uso de funciones JmesPath.|
|[IotJsonPathContentTemplate](./how-to-use-iot-jsonpath-content-mappings.md)|Plantilla que admite los mensajes enviados desde Azure Iot Hub o la característica Exportación de datos heredada de Azure IoT Central.|
|[IotCentralJsonPathContentTemplate](./how-to-use-iot-central-json-content-mappings.md)|Plantilla que admite los mensajes enviados a través de la característica Exportar datos de Azure Iot Central.| 

> [!TIP]
> Consulte la Guía de solución [de problemas del conector de](./iot-troubleshoot-guide.md) IoT para obtener ayuda para corregir errores y problemas comunes. 

## <a name="next-steps"></a>Pasos siguientes

En este artículo, ha aprendido a usar asignaciones de dispositivos. Para obtener información sobre cómo usar las asignaciones de destino de FHIR, consulte

>[!div class="nextstepaction"]
>[Uso de asignaciones de destino de FHIR](how-to-use-fhir-mappings.md)

(FHIR&#174;) es una marca comercial registrada de [HL7](https://hl7.org/fhir/) y se usa con el permiso hl7.
