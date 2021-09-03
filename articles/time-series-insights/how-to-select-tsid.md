---
title: 'Procedimientos recomendados para elegir un identificador de serie temporal: Azure Time Series Insights | Microsoft Docs'
description: Conozca los procedimientos recomendados para elegir un identificador de serie temporal en Azure Time Series Insights Gen2.
author: tedvilutis
ms.author: tvilutis
manager: cnovak
ms.workload: big-data
ms.service: time-series-insights
services: time-series-insights
ms.topic: conceptual
ms.date: 03/23/2021
ms.custom: seodec18
ms.openlocfilehash: 3a15b0c74ebe9a4696c2b61b6dd3b16d9f4b9d10
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/13/2021
ms.locfileid: "121730914"
---
# <a name="best-practices-for-choosing-a-time-series-id"></a>Procedimientos recomendados al elegir un id. de serie temporal

En este artículo se resume la importancia del identificador de serie temporal en el entorno de Azure Time Series Insights Gen2 y los procedimiento recomendados para elegir uno.

## <a name="choose-a-time-series-id"></a>Elección de un identificador de Time Series

Es fundamental seleccionar un identificador de serie temporal adecuado. Elegir un id. de serie temporal es como elegir una clave de partición para una base de datos. Se requiere al crear un entorno de Azure Time Series Insights Gen2.

Vea el tutorial de aprovisionamiento de entorno para obtener una explicación detallada del identificador de serie temporal. Verá dos ejemplos diferentes de carga de telemetría de JSON y la selección del identificador de serie temporal correcto para cada uno.</br>

> [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RWzk3P]


> [!IMPORTANT]
> Los identificadores de serie temporal son los siguientes:
>
> * Propiedad *case-sensitive*: se hace distinción de mayúsculas y minúsculas en búsquedas, comparaciones, y actualizaciones, así como al crear particiones.
> * Propiedad *immutable*: una vez creada, no se puede cambiar.

> [!TIP]
> Si el origen del evento es un centro de IoT, es probable que el id. de serie temporal sea ***iothub-connection-device-id***. Si planea usar modelos de dispositivo IoT Plug and Play o los usa sin componentes, debe incluir ***dt-subject*** como parte de la clave compuesta por si lo necesita en el futuro.

Los principales procedimientos recomendados que han de seguirse incluyen lo siguiente:

* Elija una clave de partición con muchos valores distintos (por ejemplo, centenares o miles). En muchos casos, puede ser el identificador de dispositivo, identificador de sensor o identificador de etiqueta de JSON.
* El id. de serie temporal debe ser único en el nivel de nodo hoja de su [Modelo de serie temporal](./concepts-model-overview.md).
* El límite de caracteres de la cadena de nombre de la propiedad del identificador de serie temporal es 128. En el caso del valor de la propiedad del identificador de serie temporal, el límite de caracteres es 1.024.
* Si falta un valor de propiedad único para el identificador de serie temporal, se trata como un valor NULL y sigue la misma regla de la restricción de unicidad.
* Si el identificador de serie temporal está anidado dentro de un objeto JSON complejo, asegúrese de seguir las [reglas de acoplamiento](./concepts-json-flattening-escaping-rules.md) de entrada al proporcionar el nombre de la propiedad. Modificación del ejemplo [B](concepts-json-flattening-escaping-rules.md#example-b).
* Puede seleccionar hasta *tres* propiedades clave como identificador de serie temporal. Su combinación será una clave compuesta que representa el identificador de serie temporal.  
  > [!NOTE]
  > Las tres propiedades clave deben ser cadenas.
  > Tendría que realizar consultas en esta clave compuesta, en lugar de hacerlo propiedad a propiedad.

## <a name="select-more-than-one-key-property"></a>Selección de más de una propiedad de clave

En los siguientes escenarios se describe cómo seleccionar más de una propiedad clave como identificador de serie temporal.  

### <a name="example-1-time-series-id-with-a-unique-key"></a>Ejemplo 1: identificador de serie temporal con una clave única

* Tiene flotas de recursos heredadas. Cada una tiene una clave única.
* Cada flota se identifica de forma única mediante la propiedad **deviceId**. En el caso de otra flota, la propiedad única es **objectId**. Ninguna de las flotas contiene la propiedad única de la otra. En este ejemplo, debe seleccionar dos claves, **deviceId** y **objectId**, como claves únicas.
* Aceptamos valores NULL y la falta de la presencia de una propiedad en la carga del evento cuenta como un valor NULL. Esta es también la forma adecuada de controlar el envío de datos a dos orígenes de eventos diferentes, donde los datos de cada origen de eventos tienen un identificador de serie temporal único.

### <a name="example-2-time-series-id-with-a-composite-key"></a>Ejemplo 2: identificador de serie temporal con una clave compuesta

* Necesita que varias propiedades sean únicas dentro del mismo tipo de activos.
* Es fabricante de edificios inteligentes e implementa sensores en todas las habitaciones. En cada una de ellas, lo habitual es que tenga los mismos valores para **sensorId**. Algunos ejemplos son **sensor1**, **sensor2** y **sensor3**.
* Los números de las plantas y de las estancias del edificio se solapan en la propiedad **flrRm**. Estos números tienen valores como **1a**, **2b** y **3a**.
* Tiene una propiedad, **location**, que contiene valores como **Redmond**, **Barcelona** y **Tokio**. Para que los valores sean únicos, designe las tres propiedades siguientes como sus claves del identificador de serie temporal: **sensorId**, **flrRm** y **location**.

Ejemplo de evento sin procesar:

```JSON
{
  "sensorId": "sensor1",
  "flrRm": "1a",
  "location": "Redmond",
  "temperature": 78
}
```

En Azure Portal, puede especificar después la clave compuesta de la manera siguiente:

[![Configuración del identificador de serie temporal para el entorno.](media/v2-how-to-tsid/configure-environment-key.png)](media/v2-how-to-tsid/configure-environment-key.png#lightbox)

  > [!NOTE]
  > En Azure Portal, no escriba nombres separados por comas para las propiedades en un cuadro de texto; de lo contrario, se tratarán como un nombre de propiedad único que contiene comas.
  > Escriba cada nombre de propiedad en su propio cuadro de texto.

## <a name="next-steps"></a>Pasos siguientes

* Lea las [Reglas de acoplamiento y de escape de JSON](./concepts-json-flattening-escaping-rules.md) para comprender cómo se almacenarán los eventos.

* Planeamiento del [entorno de Azure Time Series Insights Gen2](./how-to-plan-your-environment.md).
