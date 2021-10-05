---
author: dominicbetts
ms.author: dobett
ms.service: iot-develop
ms.topic: include
ms.date: 09/07/2021
ms.openlocfilehash: e80149a89c38124a6789b5e74e1c73f647465ae6
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/24/2021
ms.locfileid: "128580546"
---
## <a name="sdks"></a>SDK

Los fragmentos de código de este artículo se basan en ejemplos que usan el [complemento Azure IoT Middleware para Azure RTOS](https://github.com/azure-rtos/netxduo/tree/master/addons/azure_iot). El complemento es una capa de enlace entre [Azure RTOS](/azure/rtos/overview-rtos) y el [SDK de Azure para C insertado](https://github.com/Azure/azure-sdk-for-c/tree/master/sdk/docs/iot).

Los fragmentos de código de este artículo se basan en los siguientes ejemplos:

- [Termostato IoT Plug and Play de ejemplo](https://github.com/azure-rtos/netxduo/blob/master/addons/azure_iot/samples/sample_azure_iot_embedded_sdk_pnp.c)
- [Controlador de temperatura IoT Plug and Play de ejemplo](https://github.com/azure-rtos/netxduo/tree/master/addons/azure_iot/samples/sample_pnp_temperature_controller)

## <a name="model-id-announcement"></a>Anuncio del id. de modelo

Para anunciar el id. de modelo, el dispositivo debe incluirlo en la información de conexión:

```c
#include "nx_azure_iot_hub_client.h"

// ...

#define SAMPLE_PNP_MODEL_ID "dtmi:com:example:Thermostat;1"

// ...

status = nx_azure_iot_hub_client_model_id_set(iothub_client_ptr, (UCHAR *)SAMPLE_PNP_MODEL_ID, sizeof(SAMPLE_PNP_MODEL_ID) - 1);
```

> [!TIP]
> Esta es la única vez que un dispositivo puede establecer el identificador de modelo; después de que el dispositivo se conecte, no se puede actualizar.

## <a name="dps-payload"></a>Carga de DPS

Los dispositivos que usan [Device Provisioning Service (DPS)](../articles/iot-dps/about-iot-dps.md) pueden incluir el elemento `modelId` que se usará durante el proceso de aprovisionamiento con la siguiente carga JSON:

```json
{
    "modelId" : "dtmi:com:example:Thermostat;1"
}
```

En el ejemplo se usa el código siguiente para enviar esta carga:

```c
#include "nx_azure_iot_provisioning_client.h"

// ...

#define SAMPLE_PNP_MODEL_ID "dtmi:com:example:Thermostat;1"
#define SAMPLE_PNP_DPS_PAYLOAD "{\"modelId\":\"" SAMPLE_PNP_MODEL_ID "\"}"

// ...

status = nx_azure_iot_provisioning_client_registration_payload_set(prov_client_ptr, (UCHAR *)SAMPLE_PNP_DPS_PAYLOAD, sizeof(SAMPLE_PNP_DPS_PAYLOAD) - 1);
```

## <a name="use-components"></a>Uso de componentes

Como se describe en [Descripción de componentes de los modelos de IoT Plug and Play](../articles/iot-develop/concepts-modeling-guide.md), los creadores de dispositivos deben decidir si quieren usar componentes para describir sus dispositivos. Cuando se usan componentes, los dispositivos deben seguir las reglas descritas en las secciones siguientes. Para simplificar el trabajo con las convenciones de IoT Plug and Play para los componentes, los ejemplos usan las funciones auxiliares en [nx_azure_iot_pnp_helpers.h](https://github.com/azure-rtos/netxduo/blob/master/addons/azure_iot/samples/common/nx_azure_iot_pnp_helpers.h).

## <a name="telemetry"></a>Telemetría

Un componente predeterminado no necesita ninguna propiedad especial agregada al mensaje de telemetría.

Cuando se usan componentes anidados, los dispositivos deben establecer una propiedad de mensaje con el nombre del componente. En el fragmento de código siguiente, `component_name_ptr` es el nombre de un componente como `thermostat1`. La función auxiliar `nx_azure_iot_pnp_helper_telemetry_message_create` definida en *nx_azure_iot_pnp_helpers.h* agrega la propiedad message con el nombre del componente:

```c
#include "nx_azure_iot_pnp_helpers.h"

// ...

static const CHAR telemetry_name[] = "temperature";

// ...

UINT sample_pnp_thermostat_telemetry_send(SAMPLE_PNP_THERMOSTAT_COMPONENT *handle, NX_AZURE_IOT_HUB_CLIENT *iothub_client_ptr)
{
UINT status;
NX_PACKET *packet_ptr;
NX_AZURE_IOT_JSON_WRITER json_writer;
UINT buffer_length;

    // ...

    /* Create a telemetry message packet. */
    if ((status = nx_azure_iot_pnp_helper_telemetry_message_create(iothub_client_ptr, handle -> component_name_ptr,
        handle -> component_name_length,
        &packet_ptr, NX_WAIT_FOREVER)))
    {
        // ...
    }

    // ...

    if ((status = nx_azure_iot_hub_client_telemetry_send(iothub_client_ptr, packet_ptr,
        (UCHAR *)scratch_buffer, buffer_length, NX_WAIT_FOREVER)))
    {
        // ...
    }

    // ...

    return(status);
}
```

## <a name="read-only-properties"></a>Propiedades de solo lectura

La notificación de una propiedad del componente predeterminado no necesita ninguna construcción especial:

```c
#include "nx_azure_iot_hub_client.h"
#include "nx_azure_iot_json_writer.h"

// ...

static const CHAR reported_max_temp_since_last_reboot[] = "maxTempSinceLastReboot";

// ...

static UINT sample_build_reported_property(NX_AZURE_IOT_JSON_WRITER *json_builder_ptr, double temp)
{
UINT ret;

    if (nx_azure_iot_json_writer_append_begin_object(json_builder_ptr) ||
        nx_azure_iot_json_writer_append_property_with_double_value(json_builder_ptr,
            (UCHAR *)reported_max_temp_since_last_reboot,
            sizeof(reported_max_temp_since_last_reboot) - 1,
            temp, DOUBLE_DECIMAL_PLACE_DIGITS) ||
        nx_azure_iot_json_writer_append_end_object(json_builder_ptr))
    {
        ret = 1;
        printf("Failed to build reported property\r\n");
    }
    else
    {
        ret = 0;
    }

    return(ret);
}

// ...

if ((status = sample_build_reported_property(&json_builder, device_max_temp)))
{
    // ...
}

reported_properties_length = nx_azure_iot_json_writer_get_bytes_used(&json_builder);
if ((status = nx_azure_iot_hub_client_device_twin_reported_properties_send(&(context -> iothub_client),
    scratch_buffer,
    reported_properties_length,
    &request_id, &response_status,
    &reported_property_version,
    (5 * NX_IP_PERIODIC_RATE))))
{
    // ...
}
```

El dispositivo gemelo se actualiza con la siguiente propiedad notificada:

```json
{
  "reported": {
      "maxTempSinceLastReboot" : 38.7
  }
}
```

Al usar componentes anidados, se deben crear propiedades dentro del nombre del componente e incluir un marcador. En el fragmento de código siguiente, `component_name_ptr` es el nombre de un componente como `thermostat1`. La función auxiliar `nx_azure_iot_pnp_helper_build_reported_property` definida en *nx_azure_iot_pnp_helpers.h* crea la propiedad notificada en el formato correcto:

```c
#include "nx_azure_iot_pnp_helpers.h"

// ...

static const CHAR reported_max_temp_since_last_reboot[] = "maxTempSinceLastReboot";

UINT sample_pnp_thermostat_report_max_temp_since_last_reboot_property(SAMPLE_PNP_THERMOSTAT_COMPONENT *handle, NX_AZURE_IOT_HUB_CLIENT *iothub_client_ptr)
{
UINT reported_properties_length;
UINT status;
UINT response_status;
UINT request_id;
NX_AZURE_IOT_JSON_WRITER json_builder;
ULONG reported_property_version;

    // ...

    if ((status = nx_azure_iot_pnp_helper_build_reported_property(handle -> component_name_ptr,
        handle -> component_name_length,
        append_max_temp, (VOID *)handle,
        &json_builder)))
    {
        // ...
    }

    reported_properties_length = nx_azure_iot_json_writer_get_bytes_used(&json_builder);
    if ((status = nx_azure_iot_hub_client_device_twin_reported_properties_send(iothub_client_ptr,
        scratch_buffer,
        reported_properties_length,
        &request_id, &response_status,
        &reported_property_version,
        (5 * NX_IP_PERIODIC_RATE))))
    {
        // ...
    }

    // ...
}
```

El dispositivo gemelo se actualiza con la siguiente propiedad notificada:

```json
{
    "reported": {
        "thermostat1" : {  
            "__t" : "c",  
            "maxTemperature" : 38.7
        } 
    }
}
```

## <a name="writable-properties"></a>Propiedades editables

Estas propiedades pueden establecerse por el dispositivo o actualizarse por la solución. Para seguir las convenciones de IoT Plug and Play, el dispositivo debe informar al servicio de que la propiedad se ha recibido correctamente.

### <a name="report-a-writable-property"></a>Notificación de una propiedad editable

Cuando un dispositivo notifica una propiedad editable, debe incluir los valores `ack` definidos en las convenciones.

Para notificar una propiedad grabable del componente predeterminado:

```c
#include "nx_azure_iot_hub_client.h"
#include "nx_azure_iot_json_writer.h"

// ...

static const CHAR reported_temp_property_name[] = "targetTemperature";
static const CHAR reported_value_property_name[] = "value";
static const CHAR reported_status_property_name[] = "ac";
static const CHAR reported_version_property_name[] = "av";
static const CHAR reported_description_property_name[] = "ad";

// ...

static VOID sample_send_target_temperature_report(SAMPLE_CONTEXT *context, double current_device_temp_value,
    UINT status, UINT version, UCHAR *description_ptr,
    UINT description_len)
{
NX_AZURE_IOT_JSON_WRITER json_builder;
UINT bytes_copied;
UINT response_status;
UINT request_id;
ULONG reported_property_version;

    // ...

    if (nx_azure_iot_json_writer_append_begin_object(&json_builder) ||
        nx_azure_iot_json_writer_append_property_name(&json_builder,
            (UCHAR *)reported_temp_property_name,
            sizeof(reported_temp_property_name) - 1) ||
        nx_azure_iot_json_writer_append_begin_object(&json_builder) ||
        nx_azure_iot_json_writer_append_property_with_double_value(&json_builder,
            (UCHAR *)reported_value_property_name,
            sizeof(reported_value_property_name) - 1,
            current_device_temp_value, DOUBLE_DECIMAL_PLACE_DIGITS) ||
        nx_azure_iot_json_writer_append_property_with_int32_value(&json_builder,
            (UCHAR *)reported_status_property_name,
            sizeof(reported_status_property_name) - 1,
            (int32_t)status) ||
        nx_azure_iot_json_writer_append_property_with_int32_value(&json_builder,
            (UCHAR *)reported_version_property_name,
            sizeof(reported_version_property_name) - 1,
            (int32_t)version) ||
        nx_azure_iot_json_writer_append_property_with_string_value(&json_builder,
            (UCHAR *)reported_description_property_name,
            sizeof(reported_description_property_name) - 1,
            description_ptr, description_len) ||
        nx_azure_iot_json_writer_append_end_object(&json_builder) ||
        nx_azure_iot_json_writer_append_end_object(&json_builder))
    {
        // ...
    }
    else
    // ...
}
```

El dispositivo gemelo se actualiza con la siguiente propiedad notificada:

```json
{
  "reported": {
      "targetTemperature": {
          "value": 23.2,
          "ac": 200,
          "av": 3,
          "ad": "success"
      }
  }
}
```

Para notificar una propiedad grabable desde un componente anidado, el gemelo debe incluir un marcador y las propiedades deben crearse dentro del nombre del componente. En el fragmento de código siguiente, `component_name_ptr` es el nombre de un componente como `thermostat1`. La función auxiliar `nx_azure_iot_pnp_helper_build_reported_property_with_status` definida en *nx_azure_iot_pnp_helpers.h* crea la carga de la propiedad notificada:

```c
#include "nx_azure_iot_pnp_helpers.h"

// ...

static VOID sample_send_target_temperature_report(SAMPLE_PNP_THERMOSTAT_COMPONENT *handle,
    NX_AZURE_IOT_HUB_CLIENT *iothub_client_ptr, double temp,
    INT status_code, UINT version, const CHAR *description)
{
UINT bytes_copied;
UINT response_status;
UINT request_id;
NX_AZURE_IOT_JSON_WRITER json_writer;
ULONG reported_property_version;

    // ...

    if (nx_azure_iot_pnp_helper_build_reported_property_with_status(handle -> component_name_ptr, handle -> component_name_length,
        (UCHAR *)target_temp_property_name,
        sizeof(target_temp_property_name) - 1,
        append_temp, (VOID *)&temp, status_code,
        (UCHAR *)description,
        strlen(description), version, &json_writer))
    {
        // ...
    }
    else
    {
        // ...
    }

    // ...
}
```

El dispositivo gemelo se actualiza con la siguiente propiedad notificada:

```json
{
  "reported": {
    "thermostat1": {
      "__t" : "c",
      "targetTemperature": {
          "value": 23.2,
          "ac": 200,
          "av": 3,
          "ad": "success"
      }
    }
  }
}
```

### <a name="subscribe-to-desired-property-updates"></a>Suscripción a las actualizaciones de propiedades deseadas

Los servicios pueden actualizar las propiedades deseadas que desencadenan una notificación en los dispositivos conectados. Esta notificación incluye las propiedades deseadas actualizadas y el número de versión que identifica la actualización. Los dispositivos deben incluir este número de versión en el mensaje `ack` que se devuelve al servicio.

Los componentes predeterminados ven la propiedad única y crean el mensaje `ack` notificado con la versión recibida:

```c
#include "nx_azure_iot_hub_client.h"
#include "nx_azure_iot_json_writer.h"

// ...

static const CHAR temp_response_description[] = "success";

// ...

static UINT sample_parse_desired_temp_property(SAMPLE_CONTEXT *context,
    NX_AZURE_IOT_JSON_READER *json_reader_ptr,
    UINT is_partial)
{
double parsed_value;
UINT version;
NX_AZURE_IOT_JSON_READER copy_json_reader;
UINT status;

    // ...

    copy_json_reader = *json_reader_ptr;
    if (sample_json_child_token_move(&copy_json_reader,
            (UCHAR *)desired_version_property_name,
            sizeof(desired_version_property_name) - 1) ||
        nx_azure_iot_json_reader_token_int32_get(&copy_json_reader, (int32_t *)&version))
    {
        // ...
    }

    // ...

    sample_send_target_temperature_report(context, current_device_temp, 200,
        (UINT)version, (UCHAR *)temp_response_description,
        sizeof(temp_response_description) - 1);

    // ...
}
```

Un componente anidado recibe las propiedades deseadas encapsuladas con el nombre del componente y crea la propiedad `ack` notificada con la versión recibida:

```c
#include "nx_azure_iot_pnp_helpers.h"

// ...

static const CHAR target_temp_property_name[] = "targetTemperature";
static const CHAR temp_response_description_success[] = "success";
static const CHAR temp_response_description_failed[] = "failed";

// ...

UINT sample_pnp_thermostat_process_property_update(SAMPLE_PNP_THERMOSTAT_COMPONENT *handle,
    NX_AZURE_IOT_HUB_CLIENT *iothub_client_ptr,
    UCHAR *component_name_ptr, UINT component_name_length,
    UCHAR *property_name_ptr, UINT property_name_length,
    NX_AZURE_IOT_JSON_READER *property_value_reader_ptr, UINT version)
{
double parsed_value = 0;
INT status_code;
const CHAR *description;

    // ...

    if (property_name_length != (sizeof(target_temp_property_name) - 1) ||
        strncmp((CHAR *)property_name_ptr, (CHAR *)target_temp_property_name, property_name_length) != 0)
    {
        // ...
    }
    else if (nx_azure_iot_json_reader_token_double_get(property_value_reader_ptr, &parsed_value))
    {
        status_code = 401;
        description = temp_response_description_failed;
    }
    else
    {
        status_code = 200;
        description = temp_response_description_success;

        // ...
    }

    sample_send_target_temperature_report(handle, iothub_client_ptr, parsed_value,
                                          status_code, version, description);

    // ...
}
```

El dispositivo gemelo de un componente anidado muestra las secciones de propiedades deseadas y notificadas de la siguiente manera:

```json
{
  "desired" : {
    "thermostat1" : {
        "__t" : "c",
        "targetTemperature": 23.2,
    }
    "$version" : 3
  },
  "reported": {
    "thermostat1" : {
        "__t" : "c",
      "targetTemperature": {
          "value": 23.2,
          "ac": 200,
          "av": 3,
          "ad": "success"
      }
    }
  }
}
```

## <a name="commands"></a>Comandos:

Un componente predeterminado recibe el nombre del comando tal como lo invocó el servicio.

Un componente anidado recibe el nombre del comando precedido por el nombre del componente y el separador `*`. En el fragmento de código siguiente, la función auxiliar `nx_azure_iot_pnp_helper_command_name_parse` definida en *nx_azure_iot_pnp_helpers.h* analiza el nombre del componente y el nombre del comando del mensaje que el dispositivo recibe del servicio:

```c
#include "nx_azure_iot_hub_client.h"
#include "nx_azure_iot_pnp_helpers.h"

// ...

static VOID sample_direct_method_action(SAMPLE_CONTEXT *sample_context_ptr)
{
NX_PACKET *packet_ptr;
UINT status;
USHORT method_name_length;
const UCHAR *method_name_ptr;
USHORT context_length;
VOID *context_ptr;
UINT component_name_length;
const UCHAR *component_name_ptr;
UINT pnp_command_name_length;
const UCHAR *pnp_command_name_ptr;
NX_AZURE_IOT_JSON_WRITER json_writer;
NX_AZURE_IOT_JSON_READER json_reader;
NX_AZURE_IOT_JSON_READER *json_reader_ptr;
UINT status_code;
UINT response_length;

    // ...

    if ((status = nx_azure_iot_hub_client_direct_method_message_receive(&(sample_context_ptr -> iothub_client),
        &method_name_ptr, &method_name_length,
        &context_ptr, &context_length,
        &packet_ptr, NX_WAIT_FOREVER)))
    {
        // ...
    }

    // ...

    if ((status = nx_azure_iot_pnp_helper_command_name_parse(method_name_ptr, method_name_length,
        &component_name_ptr, &component_name_length,
        &pnp_command_name_ptr,
        &pnp_command_name_length)) != NX_AZURE_IOT_SUCCESS)
    {
        // ...
    }
    
    // ...

    else
    {
        // ...

        if ((status = sample_pnp_thermostat_process_command(&sample_thermostat_1, component_name_ptr,
            component_name_length, pnp_command_name_ptr,
            pnp_command_name_length, json_reader_ptr,
            &json_writer, &status_code)) == NX_AZURE_IOT_SUCCESS)
        {
            // ...
        }
        else if ((status = sample_pnp_thermostat_process_command(&sample_thermostat_2, component_name_ptr,
            component_name_length, pnp_command_name_ptr,
            pnp_command_name_length, json_reader_ptr,
            &json_writer, &status_code)) == NX_AZURE_IOT_SUCCESS)
        {
            // ...
        }
        else if((status = sample_pnp_temp_controller_process_command(component_name_ptr, component_name_length,
            pnp_command_name_ptr, pnp_command_name_length,
            json_reader_ptr, &json_writer,
            &status_code)) == NX_AZURE_IOT_SUCCESS)
        {
            // ...
        }
        else
        {
            printf("Failed to find any handler for method %.*s\r\n", method_name_length, method_name_ptr);
            status_code = SAMPLE_COMMAND_NOT_FOUND_STATUS;
            response_length = 0;
        }

        // ...
    }
}
```

### <a name="request-and-response-payloads"></a>Cargas de solicitud y respuesta

Los comandos usan tipos para definir sus cargas de solicitud y respuesta. Un dispositivo debe deserializar el parámetro de entrada y serializar la respuesta.

En el ejemplo siguiente se muestra cómo implementar un comando con tipos complejos definidos en las cargas:

```json
{
  "@type": "Command",
  "name": "getMaxMinReport",
  "displayName": "Get Max-Min report.",
  "description": "This command returns the max, min and average temperature from the specified time to the current time.",
  "request": {
    "name": "since",
    "displayName": "Since",
    "description": "Period to return the max-min report.",
    "schema": "dateTime"
  },
  "response": {
    "name" : "tempReport",
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
        },
        {
          "name" : "avgTemp",
          "displayName": "Average Temperature",
          "schema": "double"
        },
        {
          "name" : "startTime",
          "displayName": "Start Time",
          "schema": "dateTime"
        },
        {
          "name" : "endTime",
          "displayName": "End Time",
          "schema": "dateTime"
        }
      ]
    }
  }
}
```

Los fragmentos de código siguientes muestran cómo un dispositivo implementa esta definición de comando, incluidos los tipos que se usan para habilitar la serialización y deserialización:

```c
#include "nx_azure_iot_pnp_helpers.h"

// ...

static const CHAR report_max_temp_name[] = "maxTemp";
static const CHAR report_min_temp_name[] = "minTemp";
static const CHAR report_avg_temp_name[] = "avgTemp";
static const CHAR report_start_time_name[] = "startTime";
static const CHAR report_end_time_name[] = "endTime";
static const CHAR fake_start_report_time[] = "2020-01-10T10:00:00Z";
static const CHAR fake_end_report_time[] = "2023-01-10T10:00:00Z";

// ...

static UINT sample_get_maxmin_report(SAMPLE_PNP_THERMOSTAT_COMPONENT *handle,
    NX_AZURE_IOT_JSON_READER *json_reader_ptr,
    NX_AZURE_IOT_JSON_WRITER *out_json_builder_ptr)
{
UINT status;
UCHAR *start_time = (UCHAR *)fake_start_report_time;
UINT start_time_len = sizeof(fake_start_report_time) - 1;
UCHAR time_buf[32];

    // ...

    /* Build the method response payload */
    if (nx_azure_iot_json_writer_append_begin_object(out_json_builder_ptr) ||
        nx_azure_iot_json_writer_append_property_with_double_value(out_json_builder_ptr,
            (UCHAR *)report_max_temp_name,
            sizeof(report_max_temp_name) - 1,
            handle -> maxTemperature,
            DOUBLE_DECIMAL_PLACE_DIGITS) ||
        nx_azure_iot_json_writer_append_property_with_double_value(out_json_builder_ptr,
            (UCHAR *)report_min_temp_name,
            sizeof(report_min_temp_name) - 1,
            handle -> minTemperature,
            DOUBLE_DECIMAL_PLACE_DIGITS) ||
        nx_azure_iot_json_writer_append_property_with_double_value(out_json_builder_ptr,
            (UCHAR *)report_avg_temp_name,
            sizeof(report_avg_temp_name) - 1,
            handle -> avgTemperature,
            DOUBLE_DECIMAL_PLACE_DIGITS) ||
        nx_azure_iot_json_writer_append_property_with_string_value(out_json_builder_ptr,
            (UCHAR *)report_start_time_name,
            sizeof(report_start_time_name) - 1,
            (UCHAR *)start_time, start_time_len) ||
        nx_azure_iot_json_writer_append_property_with_string_value(out_json_builder_ptr,
            (UCHAR *)report_end_time_name,
            sizeof(report_end_time_name) - 1,
            (UCHAR *)fake_end_report_time,
            sizeof(fake_end_report_time) - 1) ||
        nx_azure_iot_json_writer_append_end_object(out_json_builder_ptr))
    {
        status = NX_NOT_SUCCESSFUL;
    }
    else
    {
        status = NX_AZURE_IOT_SUCCESS;
    }

    return(status);
}
```

> [!Tip]
> Los nombres de solicitud y respuesta no están presentes en las cargas serializadas transmitidas a través de la conexión.
