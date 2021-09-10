---
title: Conversión de un dispositivo existente para usar IoT Plug and Play | Microsoft Docs
description: En este artículo se describe cómo convertir el código de dispositivo existente para que funcione con IoT Plug and Play mediante la creación de un modelo de dispositivo y, a continuación, enviando el identificador de modelo cuando se conecta el dispositivo.
author: dominicbetts
ms.author: dobett
ms.date: 05/14/2021
ms.topic: how-to
ms.service: iot-develop
services: iot-develop
ms.openlocfilehash: 225b999053824513e41d29d80a47a596fdedf991
ms.sourcegitcommit: 7d63ce88bfe8188b1ae70c3d006a29068d066287
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 07/22/2021
ms.locfileid: "114442575"
---
# <a name="how-to-convert-an-existing-device-to-be-an-iot-plug-and-play-device"></a>Cómo convertir un dispositivo existente para que sea un dispositivo IoT Plug and Play

En este artículo se describen los pasos que debe seguir para convertir un dispositivo existente en un dispositivo IoT Plug and Play. Describe cómo crear el modelo que requiere cada dispositivo IoT Plug and Play y los cambios de código necesarios para permitir que el dispositivo funcione como tal.

En el caso de los ejemplos de código, en este artículo se muestra código de C que usa una biblioteca MQTT para conectarse a un centro de IoT. Puede aplicar los cambios descritos en este artículo a los dispositivos implementados con otros lenguajes y SDK.

Para convertir un dispositivo existente para que sea un dispositivo IoT Plug and Play:

1. Revise el código del dispositivo para comprender la telemetría, las propiedades y los comandos que implementa.
1. Cree un modelo que describa la telemetría, las propiedades y los comandos que implementa el dispositivo.
1. Modifique el código del dispositivo para anunciar el identificador de modelo cuando se conecte al servicio.

## <a name="review-your-device-code"></a>Revisión del código del dispositivo

Antes de crear un modelo para el dispositivo, debe comprender las funcionalidades existentes de este:

- La telemetría que el dispositivo envía periódicamente.
- Las propiedades de solo lectura y escritura que el dispositivo sincroniza con el servicio.
- Comandos invocados desde el servicio al que responde el dispositivo.

Por ejemplo, revise los siguientes fragmentos de código de dispositivo que implementan diferentes funcionalidades del dispositivo. Estos ejemplos se basan en el ejemplo de [PnPMQTTWin32-Before](https://github.com/Azure-Samples/IoTMQTTSample/tree/master/src/Windows/PnPMQTTWin32-Before) anterior:

El fragmento de código siguiente muestra el dispositivo que envía telemetría de temperatura:

```c
#define TOPIC "devices/" DEVICEID "/messages/events/"

// ...

void Thermostat_SendCurrentTemperature()
{
  char msg[] = "{\"temperature\":25.6}";
  int msgId = rand();
  int rc = mosquitto_publish(mosq, &msgId, TOPIC, sizeof(msg) - 1, msg, 1, true);
  if (rc != MOSQ_ERR_SUCCESS)
  {
    printf("Error: %s\r\n", mosquitto_strerror(rc));
  }
}
```

El nombre del campo de telemetría es `temperature` y su tipo es flotante o doble. La definición del modelo para este tipo de telemetría es similar al siguiente JSON. Para aprender el modo, consulte [Diseño de un modelo](#design-a-model) a continuación:

```json
{
  "@type": [
    "Telemetry"
  ],
  "name": "temperature",
  "displayName": "Temperature",
  "description": "Temperature in degrees Celsius.",
  "schema": "double"
}
```

El fragmento de código siguiente muestra el dispositivo que informa de un valor de propiedad:

```c
#define DEVICETWIN_MESSAGE_PATCH "$iothub/twin/PATCH/properties/reported/?$rid=patch_temp"

static void SendMaxTemperatureSinceReboot()
{
  char msg[] = "{\"maxTempSinceLastReboot\": 42.500}";
  int msgId = rand();
  int rc = mosquitto_publish(mosq, &msgId, DEVICETWIN_MESSAGE_PATCH, sizeof(msg) - 1, msg, 1, true);
  if (rc != MOSQ_ERR_SUCCESS)
  {
    printf("Error: %s\r\n", mosquitto_strerror(rc));
  }
}
```

El nombre de la propiedad es `maxTempSinceLastReboot` y su tipo es flotante o doble. El dispositivo notifica esta propiedad; el dispositivo nunca recibe una actualización de este valor del servicio. La definición del modelo para esta propiedad es similar al siguiente JSON. Para aprender el modo, consulte [Diseño de un modelo](#design-a-model) a continuación:

```json
{
  "@type": [
    "Property"
  ],
  "name": "maxTempSinceLastReboot",
  "schema": "double",
  "displayName": "Max temperature since last reboot.",
  "description": "Returns the max temperature since last device reboot."
}
```

El fragmento de código siguiente muestra el dispositivo que responde a los mensajes del servicio:

```c
void message_callback(struct mosquitto* mosq, void* obj, const struct mosquitto_message* message)
{
  printf("Message received: %s payload: %s \r\n", message->topic, (char*)message->payload);
  
  if (strncmp(message->topic, "$iothub/methods/POST/getMaxMinReport/?$rid=1",37) == 0)
  {
    char* pch;
    char* context;
    int msgId = 0;
    pch = strtok_s((char*)message->topic, "=",&context);
    while (pch != NULL)
    {
      pch = strtok_s(NULL, "=", &context);
      if (pch != NULL) {
        char * pEnd;
        msgId = strtol(pch,&pEnd,16 );
      }
    }
    char topic[64];
    sprintf_s(topic, "$iothub/methods/res/200/?$rid=%d", msgId);
    char msg[] = "{\"maxTemp\":83.51,\"minTemp\":77.68}";
    int rc = mosquitto_publish(mosq, &msgId, topic, sizeof(msg) - 1, msg, 1, true);
    if (rc != MOSQ_ERR_SUCCESS)
    {
      printf("Error: %s\r\n", mosquitto_strerror(rc));
    }
    delete pch;
  }

  if (strncmp(message->topic, "$iothub/twin/PATCH/properties/desired/?$version=1", 38) == 0)
  {
    char* pch;
    char* context;
    int version = 0; 
    pch = strtok_s((char*)message->topic, "=", &context);
    while (pch != NULL)
    {
      pch = strtok_s(NULL, "=", &context);
      if (pch != NULL) {
        char* pEnd;
        version = strtol(pch, &pEnd, 10);
      }
    }
    // To do: Parse payload and extract target value
    char msg[128];
    int value = 46;
    sprintf_s(msg, "{\"targetTemperature\":{\"value\":%d,\"ac\":200,\"av\":%d,\"ad\":\"success\"}}", value, version);
    int rc = mosquitto_publish(mosq, &version, DEVICETWIN_MESSAGE_PATCH, strlen(msg), msg, 1, true);
    if (rc != MOSQ_ERR_SUCCESS)
    {
      printf("Error: %s\r\n", mosquitto_strerror(rc));
    }
    delete pch;
  }
}
```

El tema `$iothub/methods/POST/getMaxMinReport/` recibe una solicitud de comando llamada `getMaxMinReport` desde el servicio; esta solicitud podría incluir una carga con parámetros de comando. El dispositivo envía una respuesta con una carga que incluye los valores `maxTemp` y `minTemp`.

El tema `$iothub/twin/PATCH/properties/desired/` recibe actualizaciones de propiedades del servicio. En este ejemplo se supone que la actualización de la propiedad es para la propiedad `targetTemperature`. Responde con una confirmación similar a `{\"targetTemperature\":{\"value\":46,\"ac\":200,\"av\":12,\"ad\":\"success\"}}`.

En resumen, el ejemplo implementa las siguientes funcionalidades:

| Nombre                   | Tipo de funcionalidad   | Detalles |
| ---------------------- | ----------------- | ------- |
| temperatura            | Telemetría         | Se asume que el tipo de datos es doble |
| maxTempSinceLastReboot | Propiedad          | Se asume que el tipo de datos es doble |
| targetTemperature      | Propiedad grabable | Tipo de datos entero |
| getMaxMinReport        | Get-Help           | Devuelve JSON con los campos `maxTemp` y `minTemp` de tipo doble |

## <a name="design-a-model"></a>Diseño de un modelo

Cada dispositivo IoT Plug and Play tiene un modelo que describe las características y funcionalidades del dispositivo. El modelo usa el[lenguaje de definición de gemelos digitales (DTDL)](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v2/dtdlv2.md) para describir las funcionalidades del dispositivo.

Para un modelo simple que asigna las funcionalidades existentes del dispositivo, use los elementos DTDL *Telemetry*, *Property* y *Command*.

Un modelo DTDL para el ejemplo descrito en la sección anterior es similar al ejemplo siguiente:

```json
{
  "@context": "dtmi:dtdl:context;2",
  "@id": "dtmi:com:example:ConvertSample;1",
  "@type": "Interface",
  "displayName": "Simple device",
  "description": "Example that shows model for simple device converted to act as an IoT Plug and Play device.",
  "contents": [
    {
      "@type": [
        "Telemetry",
        "Temperature"
      ],
      "name": "temperature",
      "displayName": "Temperature",
      "description": "Temperature in degrees Celsius.",
      "schema": "double",
      "unit": "degreeCelsius"
    },
    {
      "@type": [
        "Property",
        "Temperature"
      ],
      "name": "targetTemperature",
      "schema": "double",
      "displayName": "Target Temperature",
      "description": "Allows to remotely specify the desired target temperature.",
      "unit": "degreeCelsius",
      "writable": true
    },
    {
      "@type": [
        "Property",
        "Temperature"
      ],
      "name": "maxTempSinceLastReboot",
      "schema": "double",
      "unit": "degreeCelsius",
      "displayName": "Max temperature since last reboot.",
      "description": "Returns the max temperature since last device reboot."
    },
    {
      "@type": "Command",
      "name": "getMaxMinReport",
      "displayName": "Get Max-Min report.",
      "description": "This command returns the max and min temperature.",
      "request": {
      },
      "response": {
        "name": "tempReport",
        "displayName": "Temperature Report",
        "schema": {
          "@type": "Object",
          "fields": [
            {
              "name": "maxTemp",
              "displayName": "Max temperature",
              "schema": "double"
            },
            {
              "name": "minTemp",
              "displayName": "Min temperature",
              "schema": "double"
            }
          ]
        }
      }
    }
  ]
}
```

Con este modelo:

- Los valores `name` y `schema` se asignan a los datos que el dispositivo envía y recibe.
- Todas las funcionalidades se agrupan en una sola interfaz.
- Los campos `@type` identifican los tipos DTDL, como **Property** y **Command**.
- Campos como `unit`, `displayName` y `description` proporcionan información adicional para que el servicio la use. Por ejemplo, IoT Central usa estos valores cuando muestra datos en los paneles del dispositivo.

Para más información, consulte [Convenciones de IoT Plug and Play](concepts-convention.md) y [Guía de modelado](concepts-modeling-guide.md).

## <a name="update-the-code"></a>Actualización del código

Si el dispositivo ya está trabajando con IoT Hub o IoT Central, no es necesario realizar ningún cambio en la implementación de sus funcionalidades de telemetría, propiedad y comando. Para que el dispositivo siga las convenciones de IoT Plug and Play, modifique la forma en que el dispositivo se conecta al servicio para que anuncie el identificador del modelo que creó. A continuación, el servicio puede usar el modelo para comprender las funcionalidades del dispositivo. Por ejemplo, IoT Central puede usar el identificador de modelo para recuperar automáticamente el modelo de un repositorio y generar una plantilla de dispositivo para el dispositivo.

Los dispositivos IoT se conectan al servicio IoT a través del servicio de aprovisionamiento de dispositivos (DPS) o directamente con una cadena de conexión.

Si el dispositivo usa DPS para conectarse, incluya el identificador de modelo en la carga que envía al registrar el dispositivo. Para el modelo de ejemplo mostrado anteriormente, la carga útil tiene el siguiente aspecto:

```json
{
  "modelId" : "dtmi:com:example:ConvertSample;1"
}
```

Para más información, consulte [Registro en tiempo de ejecución: registro de dispositivos](/rest/api/iot-dps/device/runtime-registration/register-device).

Si el dispositivo usa DPS para conectarse o se conecta directamente con una cadena de conexión, incluya el identificador de modelo cuando el código se conecte a IoT Hub. Por ejemplo:

```c
#define USERNAME IOTHUBNAME ".azure-devices.net/" DEVICEID "/?api-version=2020-09-30&model-id=dtmi:com:example:ConvertSample;1"

// ...

mosquitto_username_pw_set(mosq, USERNAME, PWD);

// ...

rc = mosquitto_connect(mosq, HOST, PORT, 10);
```

## <a name="next-steps"></a>Pasos siguientes

Ahora que sabe cómo convertir un dispositivo existente para que sea un dispositivo IoT Plug and Play, se recomienda leer la [guía de modelado IoT Plug and Play](concepts-modeling-guide.md).
