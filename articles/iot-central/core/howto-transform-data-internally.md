---
title: Transformación de datos en Azure IoT Central | Microsoft Docs
description: Los dispositivos de IoT envían datos en varios formatos que puede que necesite transformar. En este artículo se describe cómo transformar datos en IoT Central antes de exportarlos.
author: dominicbetts
ms.author: dobett
ms.date: 10/28/2021
ms.topic: how-to
ms.service: iot-central
services: iot-central
ms.openlocfilehash: c886fd71399f84f04e7ac83ff24059dd658f0a4b
ms.sourcegitcommit: 96deccc7988fca3218378a92b3ab685a5123fb73
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/04/2021
ms.locfileid: "131579384"
---
# <a name="transform-data-inside-your-iot-central-application-for-export"></a>Transformación de datos en la aplicación de IoT Central para su exportación

:::image type="content" source="media/howto-transform-data-internally/transformations.png" alt-text="Diagrama que muestra las opciones de transformación para la exportación de datos." border="false":::

Los dispositivos IoT envían datos en varios formatos. Para usar los datos del dispositivo en la solución de IoT, es posible que tenga que transformar los datos del dispositivo antes de exportarlos a otros servicios.

En este artículo se muestra cómo transformar los datos del dispositivo como parte de una definición de exportación de datos en la aplicación IoT Central.

Las transformaciones de la definición para la exportación de datos de IoT Central permiten manipular el formato y la estructura de los datos del dispositivo antes de exportarlos a un destino. Puede especificar una transformación para cada destino en una definición de exportación. Cada mensaje pasa a través de la transformación para crear un registro de salida que se exporta al destino.

Use transformaciones para reestructurar cargas JSON, cambiar el nombre de los campos, filtrar campos y ejecutar cálculos sencillos en los valores de telemetría. Por ejemplo, use una transformación para convertir los mensajes en un formato tabular que coincida con el esquema de un destino, como una tabla de Azure Data Explorer.

## <a name="add-a-transformation"></a>Adición de una transformación

Para agregar una transformación para un destino en la exportación de datos, seleccione **+ Transformar**, tal como se muestra en la captura de pantalla siguiente:

:::image type="content" source="media/howto-transform-data-internally/add-transformation.png" alt-text="Captura de pantalla que muestra cómo agregar una transformación a un destino.":::

El panel **Transformación de datos** le permite especificar la transformación. En la sección **1. Agregar el mensaje de entrada**, puede escribir el mensaje de ejemplo que quiere pasar a través de la transformación. También puede generar un mensaje de ejemplo seleccionando una plantilla de dispositivo. En la sección **2.Compilar una consulta de transformación**, puede escribir la consulta que transforma el mensaje de entrada. La sección **3. Vista previa de los mensajes de salida** muestra el resultado de la transformación:

:::image type="content" source="media/howto-transform-data-internally/transformation-editor.png" alt-text="Captura de pantalla del editor de transformación en IoT Central.":::

> [!TIP]
> Si no conoce el formato del mensaje de entrada, use `.` como consulta para exportar el mensaje tal y como está a un destino, como un webhook. A continuación, pegue el mensaje que haya recibido el webhook en ***1. Agregar el mensaje de entrada**. Después, cree una consulta de transformación para procesar este mensaje en el formato de salida necesario.

## <a name="build-a-transformation-query"></a>Compilación de una consulta de transformación

El motor de transformación usa el procesador JSON de [JQ](https://stedolan.github.io/jq/) de código abierto para reestructurar y dar formato a las cargas JSON. Para especificar una transformación, escriba una consulta JQ, que puede usar los filtros, funciones y características integrados de JQ. Para ver algunos ejemplos de consultas, vea [Consultas de transformación de ejemplo](#example-transformation-queries). Para obtener más información sobre cómo escribir consultas JQ, consulte el [manual de JQ](https://stedolan.github.io/jq/manual/).

## <a name="pre-transformation-message-structure"></a>Estructura de mensajes previa a la transformación

Puede exportar los siguientes flujos de datos de IoT Central: telemetría, cambios de propiedad, eventos de conectividad de dispositivos, eventos de ciclo de vida de dispositivos y eventos de ciclo de vida de la plantilla de dispositivo. Cada tipo de datos tiene una estructura específica que incluye información como los valores de telemetría, la información de la aplicación, los metadatos del dispositivo y los valores de propiedad.

En el ejemplo siguiente se muestra el formato de un mensaje de telemetría. Todos estos datos están disponibles para la transformación. La estructura del mensaje es similar para otros tipos de mensaje, pero hay algunos campos específicos del tipo. Puede usar la característica **Agregar el mensaje de entrada** para generar un mensaje de ejemplo basado en una plantilla de dispositivo en la aplicación.

```json
{
  "applicationId": "93d68c98-9a22-4b28-94d1-06625d4c3d0f",
  "device": {
    "id": "31edabe6-e0b9-4c83-b0df-d12e95745b9f",
    "name": "Scripted Device - 31edabe6-e0b9-4c83-b0df-d12e95745b9f",
    "cloudProperties": [],
    "properties": {
      "reported": [
        {
          "id": "urn:smartKneeBrace:Smart_Vitals_Patch_wr:FirmwareVersion:1",
          "name": "FirmwareVersion",
          "value": 1.0
        }
      ]
    },
    "templateId": "urn:sbq3croo:modelDefinition:nf7st1wn3",
    "templateName": "Smart Knee Brace"
  },
  "telemetry": [
      {
        "id": "urn:continuousPatientMonitoringTemplate:Smart_Knee_Brace_6wm:Acceleration:1",
        "name": "Acceleration",
        "value": {
          "x": 19.212770659918583,
          "y": 20.596296675217335,
          "z": 54.04859440697045
        }
      },
      {
        "id": "urn:continuousPatientMonitoringTemplate:Smart_Knee_Brace_6wm:RangeOfMotion:1",
        "name": "RangeOfMotion",
        "value": 110
      }
  ],
  "enqueuedTime": "2021-03-23T19:55:56.971Z",
  "enrichments": {
      "your-enrichment-key": "enrichment-value"
  },
  "messageProperties": {
      "prop1": "prop-value"
  },
  "messageSource": "telemetry"
}
```

> [!TIP]
> Use la característica **Agregar el mensaje de entrada** en la interfaz de usuario de la aplicación IoT Central para ver estructuras de mensajes de ejemplo para otros tipos de exportación de datos, como los cambios de propiedad.

## <a name="example-transformation-queries"></a>Consultas de transformación de ejemplo

En los ejemplos de consulta siguientes se usa el mensaje de telemetría que se muestra en la sección anterior.

**Ejemplo 1**: la siguiente consulta JQ genera cada parte de telemetría del mensaje de entrada como un mensaje de salida independiente:

```jq
.telemetry[]
```

Salida JSON:

```json
{
  "id": "urn:continuousPatientMonitoringTemplate:Smart_Knee_Brace_6wm:Acceleration:1",
  "name": "Acceleration",
  "value": {
    "x": 19.212770659918583,
    "y": 20.596296675217335,
    "z": 54.04859440697045
  }
},
{
  "id": "urn:continuousPatientMonitoringTemplate:Smart_Knee_Brace_6wm:RangeOfMotion:1",
  "name": "RangeOfMotion",
  "value": 110
}
```

> [!TIP]
> Para cambiar la salida a un único mensaje con una matriz de tipos de telemetría, use la consulta `.telemetry`.

**Ejemplo 2**: la siguiente consulta JQ convierte la matriz de telemetría de entrada en un objeto con nombres de telemetría como claves:

```jq
.telemetry | map({ key: .name, value: .value }) | from_entries
```

Salida JSON:

```json
{
  "Acceleration": {
    "x": 19.212770659918583,
    "y": 20.596296675217335,
    "z": 54.04859440697045
  },
  "RangeOfMotion": 110
}
```

**Ejemplo 3**: la siguiente consulta JQ busca el valor de telemetría **RangeOfMotion** y lo convierte de grados a radianes mediante la fórmula `rad = degree * pi / 180`. Esta consulta también muestra cómo importar y usar el módulo `iotc`:

```jq
import "iotc" as iotc;
{
  rangeOfMotion: (
    .telemetry
    | iotc::find(.name == "RangeOfMotion").value
    | . * 3.14159265358979323846 / 180
  )
}
```

Salida JSON:

```json
{
  "rangeOfMotion": 1.9198621771937625
}
```

**Ejemplo 4**: para manipular el mensaje de entrada en un formato tabular, puede asignar cada mensaje exportado en una o varias *filas*. La salida de fila se representa lógicamente como un objeto JSON donde el nombre de columna es la clave y el valor de la columna es el valor:

```json
{
    "<column 1 name>": "<column 1 value>",
    "<column 2 name>": "<column 2 value>",
    ...
}
```

> [!TIP]
> Use un formato tabular al exportar a Azure Data Explorer.

La siguiente consulta JQ escribe filas en una tabla que almacena la telemetría de **rangeOfMotion** en distintos dispositivos. La consulta asigna el id. de dispositivo, el tiempo de puesta en cola y el intervalo de movimiento en una tabla con estas columnas:

```jq
import "iotc" as iotc;
{
    deviceId: .deviceId,
    timestamp: .enqueuedTime,
    rangeOfMotion: .telemetry | iotc::find(.name == "RangeOfMotion").value
}
```

Salidas en formato JSON:

```json
{
  "deviceId": "31edabe6-e0b9-4c83-b0df-d12e95745b9f",
  "timestamp": "2021-03-23T19:55:56.971Z",
  "rangeOfMotion": 110
}
```

## <a name="iot-central-module"></a>Módulo de IoT Central

Un módulo JQ es una colección de funciones personalizadas. Como parte de la consulta de transformación, puede importar un módulo específico integrado de IoT Central que contenga funciones para facilitar la escritura de las consultas. Para importar el módulo de IoT Central, use la siguiente directiva:

```jq
import "iotc" as iotc;
```

El módulo de IoT Central incluye las siguientes funciones:

`find(expression)`: la función `find` le permite encontrar un elemento de matriz específico, como el valor de telemetría o la entrada de propiedad en la carga. La entrada de función es una matriz y el parámetro define un filtro JQ que se ejecuta en cada elemento de la matriz. La función devuelve cada elemento de matriz donde el filtro se evalúa como "true":

Por ejemplo, para buscar un valor de telemetría específico denominado `RangeOfMotion`:

```jq
.telemetry | iotc::find(.name == "RangeOfMotion")
```

## <a name="scenarios"></a>Escenarios

En los escenarios siguientes se usa la funcionalidad de transformación para personalizar el formato de datos del dispositivo para un destino específico.

### <a name="scenario-1-export-device-data-to-azure-data-explorer"></a>Escenario 1: Exportación de datos de dispositivo a Azure Data Explorer

En este escenario, se transforman los datos del dispositivo para que coincidan con el esquema fijo de Azure Data Explorer, donde cada valor de telemetría aparece como una columna en la tabla y cada fila representa un único mensaje. Por ejemplo:

| deviceId | Timestamp | T1 | T2 |T3 |
| :------------- | :---------- | :----------- | :---------- | :----------- |
| "31edabe6-e0b9-4c83-b0df-d12e95745b9f" |"2021-03-23T19:55:56.971Z | 1.18898 | 1.434709 | 2.97008 |

Para exportar datos compatibles con esta tabla, cada mensaje exportado debe tener un aspecto parecido al del objeto siguiente. El objeto representa una sola fila, donde las claves son nombres de columna y los valores son el valor que se va a colocar en cada columna:

```json
{
    "Timestamp": <value-of-Timestamp>,
    "DeviceId": <value-of-deviceId>,
    "T1": <value-of-T1>,
    "T2": <value-of-T2>,
    "T3": <value-of-T3>,
}
```

En este escenario, el dispositivo envía los valores de telemetría `t1`, `t2` y `t3` en un mensaje de entrada similar al ejemplo siguiente:

```json
{
  "applicationId": "c57fe8d9-d15d-4659-9814-d3cc38ca9e1b",
  "enqueuedTime": "1933-01-26T03:10:44.480001324Z",
  "messageSource": "telemetry",
  "telemetry": [
    {
      "id": "dtmi:temperaturesensor288:sensors1lr:t1;1",
      "name": "t1",
      "value": 1.1889838348731093e+308
    },
    {
      "id": "dtmi:temperaturesensor288:sensors1lr:t2;1",
      "name": "t2",
      "value": 1.4347093391531383e+308
    },
    {
      "id": "dtmi:temperaturesensor288:sensors1lr:t3;1",
      "name": "t3",
      "value": 2.9700885230380616e+307
    }
  ],
  "device": {
    "id": "oozrnl1zs857",
    "name": "haptic alarm",
    "templateId": "dtmi:modelDefinition:nhhbjotee:qytxnp8hi",
    "templateName": "hapticsensors",
    "properties": {
      "reported": []
    },
    "cloudProperties": [],
    "simulated": true,
    "approved": false,
    "blocked": false,
    "provisioned": true
  }
}
```

La consulta JQ siguiente genera los valores de telemetría `T1`, `T2` y `T3`, así como `Timestamp` y `deviceId` como un mensaje con pares clave-valor que coinciden con el esquema de tabla de Azure Data Explorer:

```jq
import "iotc" as iotc;
{
  deviceId: .device.id,
  Timestamp: .enqueuedTime,
  T1: .telemetry | iotc::find(.name == "t1").value,
  T2: .telemetry | iotc::find(.name == "t2").value,
  T3: .telemetry | iotc::find(.name == "t3").value,
}
```

Salida JSON:

```json
{
  "T1": 1.1889838348731093e+308,
  "T2": 1.4347093391531383e+308,
  "T3": 2.9700885230380616e+307,
  "Timestamp": "1933-01-26T03:10:44.480001324Z",
  "deviceId": "oozrnl1zs857"
}
```

Para obtener más información sobre cómo agregar un clúster y una base de datos de Azure Data Explorer como destino de exportación, consulte [Creación de un destino de Azure Data Explorer](howto-export-data.md#create-an-azure-data-explorer-destination).

### <a name="scenario-2-breaking-apart-a-telemetry-array"></a>Escenario 2: Separación de una matriz de telemetría

En este escenario, el dispositivo envía la siguiente matriz de telemetría en un mensaje:

```json
{
  "applicationId": "570c2d7b-d72e-4ad1-aaf4-ad9b727daa47",
  "enqueuedTime": "1909-10-10T07:11:56.078161042Z",
  "messageSource": "telemetry",
  "telemetry": [
    {
      "id": "dtmi:sample1:data;1",
      "name": "data",
      "value": [
        {
          "id": "subdevice1",
          "values": {
              "running": true,
              "cycleCount": 2315
          }
        },
        {
          "id": "subdevice2",
          "values": {
              "running": false,
              "cycleCount": 824567
          }
        }
      ]
    },
    {
      "id": "dtmi:sample1:parentStatus;1",
      "name": "parentStatus",
      "value": "healthy"
    }
  ],
  "device": {
    "id": "9xwhr7khkfri",
    "name": "wireless port",
    "templateId": "dtmi:hpzy1kfcbt2:umua7dplmbd",
    "templateName": "Smart Vitals Patch",
    "properties": {
      "reported": [
        {
          "id": "dtmi:sample1:prop;1",
          "name": "Connectivity",
          "value": "Tenetur ut quasi minus ratione voluptatem."
        }
      ]
    },
    "cloudProperties": [],
    "simulated": true,
    "approved": true,
    "blocked": false,
    "provisioned": false
  }
}
```

Quiere transformar estos datos del dispositivo para que coincidan con el esquema de tabla siguiente:

| cycleCount | deviceId | enqueuedTime | parentStatus | en ejecución | subdeviceId |
| :--------- | :------- | :----------- | :----------- | :------ | :---------- |
| 2315   | "9xwhr7khkfri" | "1909-10-10T07:11:56.078161042Z" | "healthy" | true | "subdevice1" |
| 824567 | "9xwhr7khkfri" | "1909-10-10T07:11:56.078161042Z" | "healthy" | false | "subdevice2" |

La siguiente consulta JQ crea un mensaje de salida independiente para cada entrada de subdispositivo del mensaje e incluye información común del mensaje base y el dispositivo primario. Esta consulta simplifica la salida y separa las divisiones lógicas en los datos que llegaron como un único mensaje:

```jq
import "iotc" as iotc;
{
    enqueuedTime: .enqueuedTime,
    deviceId: .device.id,
    parentStatus: .telemetry | iotc::find(.name == "parentStatus").value
} + (
    .telemetry
    | iotc::find(.name == "data").value[]
    | {
        subdeviceId: .id,
        running: .values.running,
        cycleCount: .values.cycleCount
    }
)
```

Salida JSON:

```json
{
    "cycleCount": 2315,
    "deviceId": "9xwhr7khkfri",
    "enqueuedTime": "1909-10-10T07:11:56.078161042Z",
    "parentStatus": "healthy",
    "running": true,
    "subdeviceId": "subdevice1"
},
{
    "cycleCount": 824567,
    "deviceId": "9xwhr7khkfri",
    "enqueuedTime": "1909-10-10T07:11:56.078161042Z",
    "parentStatus": "healthy",
    "running": false,
    "subdeviceId": "subdevice2"
}
```

### <a name="scenario-3-power-bi-streaming"></a>Escenario 3: Streaming de Power BI

La característica de streaming en tiempo real de Power BI le permite ver datos en un panel que se actualiza en tiempo real con baja latencia. Para obtener más información, consulte [Streaming en tiempo real en Power BI](/power-bi/connect-data/service-real-time-streaming).

Para usar IoT Central con el Streaming de Power BI, configure una exportación de webhook que envíe cuerpos de solicitud en un formato específico. En este ejemplo se supone que tiene un conjunto de datos para hacer streaming en Power BI con el esquema siguiente:

```json
[
  {
    "bloodPressureDiastolic": 161438124,
    "bloodPressureSystolic": -966387879,
    "deviceId": "9xwhr7khkfri",
    "deviceName": "wireless port",
    "heartRate": -633994413,
    "heartRateVariability": -37514094,
    "respiratoryRate": 1582211310,
    "timestamp": "1909-10-10T07:11:56.078161042Z"
  }
]
```

Para crear el destino de exportación de webhook, necesita el punto de conexión de la dirección URL de la API de REST para el conjunto de datos de streaming de Power BI.

En este escenario, el dispositivo envía mensajes de telemetría que son parecidos al ejemplo siguiente:

```json
{
  "applicationId": "570c2d7b-d72e-4ad1-aaf4-ad9b727daa47",
  "enqueuedTime": "1909-10-10T07:11:56.078161042Z",
  "messageSource": "telemetry",
  "telemetry": [
    {
      "id": "dtmi:smartVitalsPatch:Smart_Vitals_Patch_37p:HeartRate;1",
      "name": "HeartRate",
      "value": -633994413
    },
    {
      "id": "dtmi:smartVitalsPatch:Smart_Vitals_Patch_37p:RespiratoryRate;1",
      "name": "RespiratoryRate",
      "value": 1582211310
    },
    {
      "id": "dtmi:smartVitalsPatch:Smart_Vitals_Patch_37p:HeartRateVariability;1",
      "name": "HeartRateVariability",
      "value": -37514094
    },
    {
      "id": "dtmi:smartVitalsPatch:Smart_Vitals_Patch_37p:BodyTemperature;1",
      "name": "BodyTemperature",
      "value": 5.323322666478241e+307
    },
    {
      "id": "dtmi:smartVitalsPatch:Smart_Vitals_Patch_37p:FallDetection;1",
      "name": "FallDetection",
      "value": "Earum est nobis at voluptas id qui."
    },
    {
      "id": "dtmi:smartVitalsPatch:Smart_Vitals_Patch_37p:BloodPressure;1",
      "name": "BloodPressure",
      "value": {
        "Diastolic": 161438124,
        "Systolic": -966387879
      }
    }
  ],
  "device": {
    "id": "9xwhr7khkfri",
    "name": "wireless port",
    "templateId": "dtmi:hpzy1kfcbt2:umua7dplmbd",
    "templateName": "Smart Vitals Patch",
    "properties": {
      "reported": [
        {
          "id": "dtmi:smartVitalsPatch:Smart_Vitals_Patch_wr:DeviceStatus;1",
          "name": "DeviceStatus",
          "value": "Id optio iste vero et neque sit."
        }
      ]
    },
    "cloudProperties": [],
    "simulated": true,
    "approved": true,
    "blocked": false,
    "provisioned": false
  }
}
```

La siguiente consulta JQ transforma el mensaje de entrada en un formato adecuado para que el webhook la envíe al conjunto de datos de streaming de Power BI. En este ejemplo se incluye una condición de filtro para generar solo mensajes para una plantilla de dispositivo específica. Puede usar la característica de filtro de exportación de datos para filtrar por plantilla de dispositivo:

```jq
import "iotc" as iotc;
if .device.templateId == "dtmi:hpzy1kfcbt2:umua7dplmbd" then 
    [{
        deviceId: .device.id,
        timestamp: .enqueuedTime,
        deviceName: .device.name,
        bloodPressureSystolic: .telemetry | iotc::find(.name == "BloodPressure").value.Systolic,
        bloodPressureDiastolic: .telemetry | iotc::find(.name == "BloodPressure").value.Diastolic,
        heartRate: .telemetry | iotc::find(.name == "HeartRate").value,
        heartRateVariability: .telemetry | iotc::find(.name == "HeartRateVariability").value,
        respiratoryRate: .telemetry | iotc::find(.name == "RespiratoryRate").value
    }]
else
    empty
end
```

Salida JSON:

```json
{
  "bloodPressureDiastolic": 161438124,
  "bloodPressureSystolic": -966387879,
  "deviceId": "9xwhr7khkfri",
  "deviceName": "wireless port",
  "heartRate": -633994413,
  "heartRateVariability": -37514094,
  "respiratoryRate": 1582211310,
  "timestamp": "1909-10-10T07:11:56.078161042Z"
}
```

### <a name="scenario-4-export-data-to-azure-data-explorer-and-visualize-it-in-power-bi"></a>Escenario 4: Exportar datos a Azure Data Explorer y visualizarlos en Power BI

En este escenario, exportará datos a Azure Data Explorer y, a continuación, usará un conector para visualizarlos en Power BI. Para obtener más información sobre cómo agregar un clúster y una base de datos de Azure Data Explorer como destino de exportación, consulte [Creación de un destino de Azure Data Explorer](howto-export-data.md#create-an-azure-data-explorer-destination).

En este escenario se usa la tabla de Azure Data Explorer con el esquema siguiente:

``` kusto
.create table smartvitalspatch (
  EnqueuedTime:datetime,
  Message:string,
  Application:string,
  Device:string,
  Simulated:boolean,
  Template:string,
  Module:string,
  Component:string,
  Capability:string,
  Value:dynamic
)
```

En este escenario, el dispositivo envía mensajes de telemetría que son parecidos al ejemplo siguiente:

```json
{
    "applicationId": "570c2d7b-d72e-4ad1-aaf4-ad9b727daa47",
    "enqueuedTime": "1909-10-10T07:11:56.078161042Z",
    "messageSource": "telemetry",
    "telemetry": [
        {
            "id": "dtmi:smartVitalsPatch:Smart_Vitals_Patch_37p:HeartRate;1",
            "name": "HeartRate",
            "value": -633994413
        },
        {
            "id": "dtmi:smartVitalsPatch:Smart_Vitals_Patch_37p:RespiratoryRate;1",
            "name": "RespiratoryRate",
            "value": 1582211310
        },
        {
            "id": "dtmi:smartVitalsPatch:Smart_Vitals_Patch_37p:HeartRateVariability;1",
            "name": "HeartRateVariability",
            "value": -37514094
        },
        {
            "id": "dtmi:smartVitalsPatch:Smart_Vitals_Patch_37p:BodyTemperature;1",
            "name": "BodyTemperature",
            "value": 5.323322666478241e+307
        },
        {
            "id": "dtmi:smartVitalsPatch:Smart_Vitals_Patch_37p:FallDetection;1",
            "name": "FallDetection",
            "value": "Earum est nobis at voluptas id qui."
        },
        {
            "id": "dtmi:smartVitalsPatch:Smart_Vitals_Patch_37p:BloodPressure;1",
            "name": "BloodPressure",
            "value": {
                "Diastolic": 161438124,
                "Systolic": -966387879
            }
        }
    ],
    "device": {
        "id": "9xwhr7khkfri",
        "name": "wireless port",
        "templateId": "dtmi:hpzy1kfcbt2:umua7dplmbd",
        "templateName": "Smart Vitals Patch",
        "properties": {
            "reported": [
                {
                    "id": "dtmi:smartVitalsPatch:Smart_Vitals_Patch_wr:DeviceStatus;1",
                    "name": "DeviceStatus",
                    "value": "Id optio iste vero et neque sit."
                }
            ]
        },
        "cloudProperties": [],
        "simulated": true,
        "approved": true,
        "blocked": false,
        "provisioned": false
    }
}
```

La siguiente consulta JQ transforma el mensaje de entrada en un mensaje de salida independiente para cada valor de telemetría. Esta transformación genera una salida que coincide con el esquema de tabla de Azure Data Explorer. La transformación usa un esquema de tipo "entity-attribute-value" donde cada fila contiene un único valor de telemetría y el nombre de la telemetría es un valor de una columna independiente de la misma fila:

```jq
. as $in | .telemetry[] | {
  EnqueuedTime: $in.enqueuedTime,
  Message: $in.messageId,
  Application: $in.applicationId,
  Device: $in.device.id,
  Simulated: $in.device.simulated,
  Template: ($in.device.templateName // ""),
  Module: ($in.module // ""),
  Component: ($in.component // ""),
  Capability: .name,
  Value: .value
}
```

Salida JSON:

```json
{
  "Application": "570c2d7b-d72e-4ad1-aaf4-ad9b727daa47",
  "Capability": "HeartRate",
  "Component": "",
  "Device": "9xwhr7khkfri",
  "EnqueuedTime": "1909-10-10T07:11:56.078161042Z",
  "Message": null,
  "Module": "",
  "Simulated": true,
  "Template": "Smart Vitals Patch",
  "Value": -633994413
},
{
  "Application": "570c2d7b-d72e-4ad1-aaf4-ad9b727daa47",
  "Capability": "RespiratoryRate",
  "Component": "",
  "Device": "9xwhr7khkfri",
  "EnqueuedTime": "1909-10-10T07:11:56.078161042Z",
  "Message": null,
  "Module": "",
  "Simulated": true,
  "Template": "Smart Vitals Patch",
  "Value": 1582211310
},
{
  "Application": "570c2d7b-d72e-4ad1-aaf4-ad9b727daa47",
  "Capability": "HeartRateVariability",
  "Component": "",
  "Device": "9xwhr7khkfri",
  "EnqueuedTime": "1909-10-10T07:11:56.078161042Z",
  "Message": null,
  "Module": "",
  "Simulated": true,
  "Template": "Smart Vitals Patch",
  "Value": -37514094
},
{
  "Application": "570c2d7b-d72e-4ad1-aaf4-ad9b727daa47",
  "Capability": "BodyTemperature",
  "Component": "",
  "Device": "9xwhr7khkfri",
  "EnqueuedTime": "1909-10-10T07:11:56.078161042Z",
  "Message": null,
  "Module": "",
  "Simulated": true,
  "Template": "Smart Vitals Patch",
  "Value": 5.323322666478241e+307
},
{
  "Application": "570c2d7b-d72e-4ad1-aaf4-ad9b727daa47",
  "Capability": "FallDetection",
  "Component": "",
  "Device": "9xwhr7khkfri",
  "EnqueuedTime": "1909-10-10T07:11:56.078161042Z",
  "Message": null,
  "Module": "",
  "Simulated": true,
  "Template": "Smart Vitals Patch",
  "Value": "Earum est nobis at voluptas id qui."
},
{
  "Application": "570c2d7b-d72e-4ad1-aaf4-ad9b727daa47",
  "Capability": "BloodPressure",
  "Component": "",
  "Device": "9xwhr7khkfri",
  "EnqueuedTime": "1909-10-10T07:11:56.078161042Z",
  "Message": null,
  "Module": "",
  "Simulated": true,
  "Template": "Smart Vitals Patch",
  "Value": {
      "Diastolic": 161438124,
      "Systolic": -966387879
  }
}
```

Los datos de salida se exportan al clúster de Azure Data Explorer. Para visualizar los datos exportados en Power BI, complete los pasos siguientes:

1. Instale la aplicación Power BI. Puede descargar la aplicación de escritorio de Power BI de [Pase de los datos al conocimiento y la acción con Power BI Desktop](https://powerbi.microsoft.com/desktop/).
1. Descargue el archivo [IoT Central ADX Connector.pbit](https://github.com/Azure-Samples/iot-central-docs-samples/raw/master/azure-data-explorer-power-bi/IoT%20Central%20ADX%20Connector.pbit) de Power BI Desktop desde GitHub.
1. Use la aplicación Power BI Desktop para abrir el archivo *IoT Central ADX Connector.pbit* que descargó en el paso anterior. Cuando se le solicite, escriba la información del clúster, base de datos y tabla de Azure Data Explorer que anotó anteriormente.

Ahora ya puede visualizar los datos en Power BI:

:::image type="content" source="media/howto-transform-data-internally/powerbi-report.png" alt-text="Captura de pantalla del informe de Power BI que muestra los datos de IoT Central." border="false":::

## <a name="next-steps"></a>Pasos siguientes

Ahora que sabe cómo usar la transformación de datos en IoT Central, puede realizar el siguiente paso para aprender a [usar el análisis en IoT Central](./howto-create-analytics.md).
