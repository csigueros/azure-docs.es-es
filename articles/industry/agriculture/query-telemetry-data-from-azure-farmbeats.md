---
title: Consulta de los datos de telemetría ingeridos
description: En este artículo se describe cómo consultar datos de telemetría ingeridos.
author: sunasing
ms.topic: article
ms.date: 03/11/2020
ms.author: sunasing
ms.openlocfilehash: 88cf9236ca33eddd9f86a60c210aae253f8d9c2c
ms.sourcegitcommit: 87de14fe9fdee75ea64f30ebb516cf7edad0cf87
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/01/2021
ms.locfileid: "129361005"
---
# <a name="query-ingested-telemetry-data"></a>Consulta de los datos de telemetría ingeridos

En este artículo se describe cómo consultar datos de sensor ingeridos en Azure FarmBeats.

Uno de los escenarios comunes de FarmBeats es la ingesta de datos de Internet de las cosas (IoT) en recursos como dispositivos y sensores. Se crean metadatos para dispositivos y sensores y, luego, se ingieren los datos históricos en FarmBeats en formato canónico. Una vez que los datos de sensor están disponibles en el centro de datos de FarmBeats, podemos consultarlos para generar información procesable o modelos de compilación.

## <a name="before-you-begin"></a>Antes de empezar

Antes de continuar con este artículo, asegúrese de que ha instalado FarmBeats y se han ingerido datos de telemetría de sensores desde los dispositivos IoT en FarmBeats.

Para ingerir datos de telemetría de sensores, visite [Ingesta de datos de telemetría históricos](ingest-historical-telemetry-data-in-azure-farmbeats.md)

Antes de continuar, también debe asegurarse de estar familiarizado con las API de REST de FarmBeats, ya que consultará la telemetría ingerida mediante las API. Para obtener más información sobre las API de FarmBeats, consulte [API de REST de FarmBeats](rest-api-in-azure-farmbeats.md). **Asegúrese de que puede realizar solicitudes de API a su punto de conexión del centro de datos de FarmBeats**.

## <a name="query-ingested-sensor-telemetry-data"></a>Consulta de datos de telemetría de sensores ingeridos

Hay dos maneras de acceder a los datos de telemetría y consultarlos desde FarmBeats:

- La API y
- Time Series Insights (TSI).

### <a name="query-using-rest-api"></a>Consulta mediante API REST

Siga los pasos para consultar los datos de telemetría de sensores ingeridos mediante las API de REST de FarmBeats:

1. Identifique el sensor que le interese. Para ello, puede realizar una solicitud GET en la API /Sensor.

> [!NOTE]
> Valores de **id** y **sensorModelId** del objeto de sensor que le interesa.

2. Cree una llamada GET/{id} en la API /SensorModel para el valor de **sensorModelId** anotado en el paso 1. El "Modelo de sensor" tiene todos los metadatos y los detalles sobre la telemetría ingerida del sensor. Por ejemplo, **Medida de sensor** del objeto **Modelo de sensor** tiene detalles sobre qué medidas envía el sensor y en qué tipos y unidades. Por ejemplo,

  ```json
  {
      "name": "moist_soil_last <name of the sensor measure - this is what we will receive as part of the queried telemetry data>",
      "dataType": "Double <Data Type - eg. Double>",
      "type": "SoilMoisture <Type of measure eg. temperature, soil moisture etc.>",
      "unit": "Percentage <Unit of measure eg. Celsius, Percentage etc.>",
      "aggregationType": "None <either of None, Average, Maximum, Minimum, StandardDeviation>",
      "description": "<Description of the measure>"
  }
  ```
Tome nota de la respuesta de la llamada GET/{id} para el modelo de sensor.

3. Realice una llamada POST en la API/Telemetry con la carga de entrada siguiente:

  ```json
  {
    "sensorId": "<id of the sensor as noted in step 1>",
    "searchSpan": {
      "from": "<desired start timestamp in ISO 8601 format; default is UTC>",
      "to": "<desired end timestamp in ISO 8601 format; default is UTC>"
    },
    "filter": {
      "tsx": "string"
    },
    "projectedProperties": [
      {
        "additionalProp1": "string",
        "additionalProp2": "string",
        "additionalProp3": "string"
      }
    ]
  }
  ```
4. La respuesta de la API /Telemetry tendrá un aspecto similar al siguiente:

  ```json
  {
    "timestamps": [
      "2020-XX-XXT07:30:00Z",
      "2020-XX-XXT07:45:00Z"
    ],
    "properties": [
      {
        "values": [
          "<id of the sensor>",
          "<id of the sensor>"
        ],
        "name": "Id",
        "type": "String"
      },
      {
        "values": [
          2.1,
          2.2
        ],
        "name": "moist_soil_last <name of the SensorMeasure as defined in the SensorModel object>",
        "type": "Double <Data Type of the value - eg. Double>"
      }
    ]
  }
  ```
En la respuesta del ejemplo anterior, la telemetría del sensor consultada proporciona datos para dos marcas de tiempo junto con el nombre de la medida ("moist_soil_last") y los valores de la telemetría notificada en las dos marcas de tiempo. Tendrá que consultar el modelo de sensor asociado (como se describe en el paso 2) para interpretar el tipo y la unidad de los valores notificados.

### <a name="query-using-azure-time-series-insights-tsi"></a>Consulta mediante Azure Time Series Insights (TSI)

FarmBeats utiliza [Azure Time Series Insights (TSI)](https://azure.microsoft.com/services/time-series-insights/) para ingerir, almacenar, consultar y visualizar datos a escala de IoT, muy contextualizados y optimizados para series temporales.

Los datos de telemetría se reciben en EventHub y luego se procesan y se insertan en un entorno de TSI dentro del grupo de recursos de FarmBeats. Después, los datos se pueden consultar directamente desde TSI. Para obtener más información, consulte la [documentación de TSI](../../time-series-insights/time-series-insights-explorer.md).

Siga los pasos para visualizar datos en TSI:

1. Vaya a **Azure Portal** > **FarmBeats DataHub resource group** (Grupo de recursos de centro de datos de FarmBeats) > seleccione el entorno de **Time Series Insights** (tsi-xxxx) > **Directivas de acceso a datos**. Agregue un usuario con acceso de lector o colaborador.
2. Vaya a la página **Información general** del entorno de **Time Series Insights** (tsi-xxxx) y seleccione **URL del Explorador de Time Series Insights**. Ahora podrá visualizar la telemetría ingerida.

Además del almacenamiento, la consulta y la visualización de la telemetría, TSI también permite la integración en un panel de Power BI. Para más información, consulte [aquí](../../time-series-insights/how-to-connect-power-bi.md).

## <a name="next-steps"></a>Pasos siguientes

Ya ha consultado datos de sensores de la instancia de Azure FarmBeats. A continuación, aprenda a [generar mapas](generate-maps-in-azure-farmbeats.md#generate-maps) para las granjas.