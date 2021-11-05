---
title: Empleo de la API de REST para consultar dispositivos en Azure IoT Central
description: Cómo usar la API de REST de IoT Central para consultar dispositivos en una aplicación
author: dominicbetts
ms.author: dobett
ms.date: 10/12/2021
ms.topic: how-to
ms.service: iot-central
services: iot-central
ms.openlocfilehash: b287bb463519d4b6ef76997dfb778ac4827014c2
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/02/2021
ms.locfileid: "131093568"
---
# <a name="how-to-use-the-iot-central-rest-api-to-query-devices"></a>Empleo de la API de REST de IoT Central para consultar dispositivos

La API de REST de IoT Central permite desarrollar aplicaciones cliente que se integran con aplicaciones de IoT Central. Puede usar la API de REST para consultar dispositivos en la aplicación de IoT Central. Estos son ejemplos de cómo se puede usar la API de REST de consulta:

- Obtener los diez últimos valores de telemetría notificados por un dispositivo.
- Obtener las últimas 24 horas de datos de dispositivos que se encuentran en la misma sala. Una sala es un dispositivo o una propiedad en la nube.
- Buscar todos los dispositivos que tienen un estado de error y firmware obsoleto.
- Tendencias de telemetría de dispositivos, calculadas como promedio en ventanas de 10 minutos.
- Obtener la versión de firmware actual de todos los dispositivos de termostato.

En este artículo se explica cómo usar la API `/query` para consultar dispositivos.

Un dispositivo puede agrupar las propiedades, la telemetría y los comandos que admite en _componentes_ y _módulos_.

Cada llamada API de REST de IoT Central requiere un encabezado de autorización. Para obtener más información, vea los [procedimientos de autenticación y autorización de llamadas a la API REST de IoT Central](howto-authorize-rest-api.md).

Para ver la documentación de referencia sobre la API REST de IoT Central, consulte [Referencia de la API REST de Azure IoT Central](/rest/api/iotcentral/).

## <a name="run-a-query"></a>Ejecución de una consulta

Use la siguiente solicitud para ejecutar una consulta:

```http
POST https://{your app subdomain}.azureiotcentral.com/api/query?api-version=1.1-preview
```

La consulta está en el cuerpo de la solicitud y es similar al ejemplo siguiente:

```json
{
  "query": "SELECT $id, $ts, temperature, humidity FROM urn:modelDefinition:fupmoiu28b:ymju9efv9 WHERE WITHIN_WINDOW(P1D)"
}
```

El valor `urn:modelDefinition:fupmoiu28b:ymju9efv9` de la cláusula `FROM` es un *identificador de plantilla de dispositivo*. Para buscar un identificador de plantilla de dispositivo, vaya a la página **Dispositivos** de la aplicación de IoT Central y mantenga el puntero sobre un dispositivo que use la plantilla. La tarjeta incluye el identificador de plantilla de dispositivo:

:::image type="content" source="media/howto-query-with-rest-api/show-device-template-id.png" alt-text="Captura de pantalla que muestra cómo buscar el identificador de plantilla de dispositivo en la dirección URL de la página.":::

La respuesta a esta solicitud es similar al ejemplo siguiente:

```json
{
  "results": [
    {
      "$id": "sample-003",
      "$ts": "2021-09-10T12:59:52.015Z",
      "temperature": 47.632160152311016,
      "humidity": 49.726422005390816
    },
    {
      "$id": "sample-001",
      "$ts": "2021-09-10T13:01:34.286Z",
      "temperature": 58.898120617808495,
      "humidity": 44.66125772328022
    },
    {
      "$id": "sample-001",
      "$ts": "2021-09-10T13:04:04.96Z",
      "temperature": 52.79601469228174,
      "humidity": 71.5067230188416
    },
    {
      "$id": "sample-002",
      "$ts": "2021-09-10T13:04:36.877Z",
      "temperature": 49.610062789623264,
      "humidity": 52.78538601804491
    }
  ]
}
```

## <a name="syntax"></a>Sintaxis

La sintaxis de consulta es similar a la sintaxis SQL y consta de las cláusulas siguientes:

- `SELECT` es necesaria y define los datos que se quieren recuperar, como los valores de telemetría del dispositivo.
- `FROM` es necesaria e identifica el tipo de dispositivo que se está consultando. Esta cláusula especifica el identificador de plantilla de dispositivo.
- `WHERE` es opcional y permite filtrar los resultados.
- `ORDER BY` es opcional y permite ordenar los resultados.
- `GROUP BY` es opcional y permite agregar resultados.

En las siguientes secciones se describen estas cláusulas más detalladamente.

## <a name="select-clause"></a>Cláusula SELECT

La cláusula `SELECT` enumera los valores de datos que se van a incluir en la salida de la consulta y puede incluir los siguientes elementos:

- Telemetría. Use los nombres de telemetría de la plantilla de dispositivo.
- Propiedades notificadas. Use los nombres de propiedad de la plantilla de dispositivo.
- Propiedades en la nube. Use los nombres de propiedad en la nube de la plantilla de dispositivo.
- `$id`. El identificador del dispositivo.
- `$provisioned`. Valor booleano que muestra si el dispositivo se ha aprovisionado.
- `$simulated`. Valor booleano que muestra si el dispositivo es un dispositivo simulado.
- `$ts`. Marca de tiempo asociada a un valor de telemetría.

Si la plantilla de dispositivo usa componentes como **Información del dispositivo**, se hace referencia a la telemetría o a las propiedades definidas en el componente como se muestra a continuación:

```json
{
  "query": "SELECT deviceInformation.model, deviceInformation.swVersion FROM urn:modelDefinition:fupmoiu28b:ymju9efv9"
}
```

Puede encontrar el nombre del componente en la plantilla de dispositivo:

:::image type="content" source="media/howto-query-with-rest-api/show-component-name.png" alt-text="Captura de pantalla que muestra cómo buscar el nombre del componente.":::

En la cláusula `SELECT` se aplican los límites siguientes:

- No hay ningún operador comodín.
- No puede haber más de 15 elementos en la lista de selección.
- En una sola consulta se puede seleccionar telemetría o propiedades, pero no ambas. Una consulta de propiedades puede incluir propiedades notificadas y propiedades en la nube.

### <a name="aliases"></a>Alias

Use la palabra clave `AS` para definir un alias para un elemento de la cláusula `SELECT`. El alias se usa en la salida de la consulta. También puede usarlo en cualquier otra parte de la consulta. Por ejemplo:

```json
{
  "query": "SELECT $id as ID, $ts as timestamp, temperature as t, pressure as p FROM urn:modelDefinition:fupmoiu28b:ymju9efv9 WHERE WITHIN_WINDOW(P1D) AND t > 0 AND p > 50"
}
```

> [!TIP]
> No se puede usar otro elemento de la lista de selección como alias. Por ejemplo, lo siguiente no se permite `SELECT id, temp AS id...`.

El resultado es similar a la salida siguiente:

```json
{
  "results": [
    {
      "ID": "sample-002",
      "timestamp": "2021-09-10T11:40:29.188Z",
      "t": 40.20355053736378,
      "p": 79.26806508746755
    },
    {
      "ID": "sample-001",
      "timestamp": "2021-09-10T11:43:42.61Z",
      "t": 68.03536237975348,
      "p": 58.33517075380311
    }
  ]
}
```

### <a name="top"></a>TOP

Use `TOP` para limitar el número de resultados que devuelve la consulta. Por ejemplo, la siguiente consulta devuelve los diez primeros resultados:

```json
{
    "query": "SELECT TOP 10 $id as ID, $ts as timestamp, temperature, humidity FROM urn:modelDefinition:fupmoiu28b:ymju9efv9"
}
```

Si no usa `TOP`, la consulta devuelve un máximo de 10 000 resultados.

Para ordenar los resultados antes de que `TOP` limite el número de resultados, use [ORDER BY](#order-by-clause).

## <a name="from-clause"></a>Cláusula FROM

La cláusula `FROM` debe contener un identificador de plantilla de dispositivo. La cláusula `FROM` especifica el tipo de dispositivo que se está consultando.

Para buscar un identificador de plantilla de dispositivo, vaya a la página **Dispositivos** de la aplicación de IoT Central y mantenga el puntero sobre un dispositivo que use la plantilla. La tarjeta incluye el identificador de plantilla de dispositivo:

:::image type="content" source="media/howto-query-with-rest-api/show-device-template-id.png" alt-text="Captura de pantalla que muestra cómo buscar el identificador de plantilla de dispositivo en la dirección URL de la página.":::

También puede usar la llamada API de REST [Devices - Get](/rest/api/iotcentral/1.1-previewdataplane/devices/get) para obtener el identificador de plantilla de dispositivo de un dispositivo.

## <a name="where-clause"></a>WHERE, cláusula

La cláusula `WHERE` permite usar valores y ventanas temporales para filtrar los resultados:

### <a name="time-windows"></a>Ventanas de tiempo

Para obtener telemetría recibida por la aplicación dentro de una ventana temporal especificada, use `WITHIN_WINDOW` como parte de la cláusula `WHERE`. Por ejemplo, para recuperar la telemetría de temperatura y humedad del último día, use la consulta siguiente:

```json
{
  "query": "SELECT $id, $ts, temperature, humidity FROM urn:modelDefinition:fupmoiu28b:ymju9efv9 WHERE WITHIN_WINDOW(P1D)"
}
```

El valor de la ventana de tiempo usa el [formato de duraciones ISO 8601](https://en.wikipedia.org/wiki/ISO_8601#Durations). La tabla siguiente proporciona algunos ejemplos:

| Ejemplo | Descripción                |
| ------- | -------------------------- |
| PT10M   | Últimos 10 minutos           |
| P1D     | Último día                   |
| P2DT12H | Últimos 2 días y 12 horas |
| P1W     | Última semana                  |
| PT5H    | Últimas cinco horas            |
| '2021-06-13T13:00:00Z/2021-06-13T15:30:00Z' | Intervalo de tiempo específico |

> [!NOTE]
> Solo se pueden usar ventanas de tiempo cuando se consulte la telemetría.

### <a name="value-comparisons"></a>Comparaciones de valores

Puede obtener valores de telemetría o de propiedad basados en valores específicos. Por ejemplo, la consulta siguiente devuelve todos los mensajes donde la temperatura es mayor que cero, la presión es mayor que 50 y el identificador de dispositivo es uno de **sample-002** y **sample-003**:

```json
{
  "query": "SELECT $id, $ts, temperature AS t, pressure AS p FROM urn:modelDefinition:fupmoiu28b:ymju9efv9 WHERE WITHIN_WINDOW(P1D) AND t > 0 AND p > 50 AND $id IN ['sample-002', 'sample-003']"
}
```

Se admiten los siguientes operadores:

- Operadores lógicos `AND` y `OR`.
- Operadores de comparación `=`, `!=`, `>`, `<`, `>=`, `<=`, `<>` e `IN`.

> [!NOTE]
> El operador `IN` solo funciona con telemetría y `$id`.

En la cláusula `WHERE` se aplican los límites siguientes:

- Puede usar un máximo de 10 operadores en una sola consulta.
- En una consulta de telemetría, la cláusula `WHERE` solo puede contener telemetría y filtros de metadatos de dispositivo.
- En una consulta de propiedades, la cláusula `WHERE` solo puede contener propiedades notificadas, propiedades en la nube y filtros de metadatos de dispositivo.

## <a name="aggregations-and-group-by-clause"></a>Agregaciones y cláusula GROUP BY

Las funciones de agregación permiten calcular valores como el promedio, el máximo y el mínimo de los datos de telemetría de una ventana de tiempo. Por ejemplo, la consulta siguiente calcula la temperatura y la humedad medias del dispositivo `sample-001` en ventanas de 10 minutos:

```json
{
  "query": "SELECT AVG(temperature), AVG(pressure) FROM urn:modelDefinition:fupmoiu28b:ymju9efv9 WHERE WITHIN_WINDOW(P1D) AND $id='{{DEVICE_ID}}' GROUP BY WINDOW(PT10M)"
}
```

Los resultados tienen un aspecto similar a la salida siguiente:

```json
{
    "results": [
        {
            "$ts": "2021-09-14T11:40:00Z",
            "avg_temperature": 49.212146114456104,
            "avg_pressure": 48.590304135023764
        },
        {
            "$ts": "2021-09-14T11:30:00Z",
            "avg_temperature": 52.44844454703927,
            "avg_pressure": 52.25973211022142
        },
        {
            "$ts": "2021-09-14T11:20:00Z",
            "avg_temperature": 50.14626272506926,
            "avg_pressure": 48.98400386898757
        }
    ]
}
```

Se admiten las siguientes funciones de agregación: `SUM`, `MAX`, `MIN`, `COUNT`, `AVG`, `FIRST` y `LAST`.

Use `GROUP BY WINDOW` para especificar el tamaño de la ventana. Si no usa `GROUP BY WINDOW`, la consulta agrega la telemetría de los últimos 30 días.

> [!NOTE]
> Solo se pueden agregar valores de telemetría.

## <a name="order-by-clause"></a>Cláusula ORDER BY

La cláusula `ORDER BY` permite ordenar los resultados de la consulta por un valor de telemetría, la marca de tiempo o el id. de dispositivo. Se puede ordenar en orden ascendente o descendente. Por ejemplo, la consulta siguiente devuelve primero los resultados más recientes:

```json
{
  "query": "SELECT $id as ID, $ts as timestamp, temperature, humidity FROM urn:modelDefinition:fupmoiu28b:ymju9efv9 ORDER BY timestamp DESC"
}
```

> [!TIP]
> Combine `ORDER BY` con `TOP` para limitar el número de resultados que devuelve la consulta después de ordenar.

## <a name="limits"></a>Límites

Los límites actuales de las consultas son:

- No más de 15 elementos en la lista de la cláusula `SELECT`.
- No más de 10 operaciones lógicas en la cláusula `WHERE`.
- Las consultas devuelven un máximo de 10 000 registros.
- La longitud máxima de una cadena de consulta es de 350 caracteres.
- No se puede usar el carácter comodín (`*`) en la lista de la cláusula `SELECT`.

## <a name="next-steps"></a>Pasos siguientes

Ahora que ha aprendido a consultar dispositivos con la API de REST, el siguiente paso que se sugiere es obtener información sobre el [Uso de la API de REST de IoT Central para administrar usuarios y roles](howto-manage-users-roles-with-rest-api.md).
