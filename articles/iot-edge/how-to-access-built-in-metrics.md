---
title: 'Acceso a las métricas integradas: Azure IoT Edge'
description: Acceso remoto a métricas integradas desde los componentes de entorno de ejecución de Azure IoT Edge
author: v-tcassi
ms.author: v-tcassi
ms.date: 06/25/2021
ms.topic: conceptual
ms.reviewer: veyalla
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: ee4c39b7dfc4097480588620465eedd40eba53f6
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/13/2021
ms.locfileid: "121743322"
---
# <a name="access-built-in-metrics"></a>Acceso a las métricas integradas

[!INCLUDE [iot-edge-version-all-supported](../../includes/iot-edge-version-all-supported.md)]

Los componentes de entorno de ejecución de Azure IoT Edge, centro de IoT Edge y agente de IoT Edge generan métricas integradas en el [formato de exposición de Prometheus](https://prometheus.io/docs/instrumenting/exposition_formats/). Obtenga acceso a estas métricas de forma remota para supervisar y comprender el estado de un dispositivo IoT Edge.

Puede usar su propia solución para acceder a estas métricas. O bien, puede usar el [módulo del recopilador de métricas](https://azuremarketplace.microsoft.com/marketplace/apps/microsoft_iot_edge.metrics-collector), que controla la recopilación de las métricas integradas y su envío a Azure Monitor o Azure IoT Hub. Para más información, consulte [Recopilación y transporte de métricas](how-to-collect-and-transport-metrics.md).

A partir de la versión 1.0.10, las métricas se exponen automáticamente de manera predeterminada en el **puerto 9600** de los módulos **edgeHub** y **edgeAgent** (`http://edgeHub:9600/metrics` y `http://edgeAgent:9600/metrics`). No se asignan por puertos al host de manera predeterminada.

Para acceder a las métricas desde el host, exponga y asigne el puerto de las métricas desde `createOptions` del módulo. En el ejemplo siguiente se asigna el puerto de métricas predeterminado al puerto 9601 en el host:

```json
{
  "ExposedPorts": {
    "9600/tcp": {}
  },
  "HostConfig": {
    "PortBindings": {
      "9600/tcp": [
        {
          "HostPort": "9601"
        }
      ]
    }
  }
}
```

Elija números de puerto de host diferentes y únicos si va a asignar los puntos de conexión de métricas de edgeHub y edgeAgent.

> [!NOTE]
> La variable de entorno `httpSettings__enabled` no se debe establecer en `false` para que las métricas integradas estén disponibles para la recopilación.
>
> Las variables de entorno que se pueden usar para deshabilitar las métricas se muestran en [Documentación del repositorio azure/iotedge](https://github.com/Azure/iotedge/blob/master/doc/EnvironmentVariables.md).

## <a name="available-metrics"></a>Métricas disponibles

Las métricas contienen etiquetas para ayudar a identificar la naturaleza de la métrica que se está recopilando. Todas las métricas contienen las etiquetas siguientes:

| Etiqueta | Descripción |
|-|-|
| iothub | El centro con el que se comunica el dispositivo |
| edge_device | Identificador del dispositivo actual |
| instance_number | GUID que representa el entorno de ejecución actual. Al reiniciar, se restablecerán todas las métricas. Este GUID facilita la conciliación de los reinicios. |

En el formato de exposición de Prometheus, hay cuatro tipos de métricas principales: contador, medidor, histograma y resumen. Para obtener más información sobre los diferentes tipos de métricas, consulte la [documentación sobre los tipos de métricas de Prometheus](https://prometheus.io/docs/concepts/metric_types/).

Los cuantiles proporcionados para las métricas integradas de histograma y resumen son 0,1; 0,5; 0,9 y 0,99.

El módulo **edgeHub** genera las métricas siguientes:

| Nombre | Dimensions | Descripción |
|-|-|-|
| `edgehub_gettwin_total` | `source` (origen de la operación)<br> `id` (id. de módulo) | Tipo: contador<br> Número total de llamadas de GetTwin |
| `edgehub_messages_received_total` | `route_output` (salida que envió el mensaje)<br> `id` | Tipo: contador<br> Número total de mensajes recibidos de los clientes |
| `edgehub_messages_sent_total` | `from` (origen del mensaje)<br> `to` (destino del mensaje)<br>`from_route_output`<br> `to_route_input` (entrada de destino del mensaje)<br> `priority` (prioridad del mensaje al destino) | Tipo: contador<br> Número total de mensajes enviados a clientes o ascendentes<br> `to_route_input` está vacío cuando `to` es $upstream |
| `edgehub_reported_properties_total` | `target` (actualizar destino)<br> `id` | Tipo: contador<br> Total de llamadas de actualización de propiedades notificadas |
| `edgehub_message_size_bytes` | `id`<br> | Tipo: resumen<br> Tamaño del mensaje de los clientes<br> Es posible que se notifiquen los valores como `NaN` si no se notifica ninguna nueva medida para un determinado período de tiempo (actualmente 10 minutos); para el tipo `summary`, se emitirán los contadores `_count` y `_sum` correspondientes. |
| `edgehub_gettwin_duration_seconds` | `source` <br> `id` | Tipo: resumen<br> Tiempo necesario para las operaciones GetTwin |
| `edgehub_message_send_duration_seconds` | `from`<br> `to`<br> `from_route_output`<br> `to_route_input` | Tipo: resumen<br> Tiempo necesario para enviar un mensaje |
| `edgehub_message_process_duration_seconds` | `from` <br> `to` <br> `priority` | Tipo: resumen<br> Tiempo necesario para procesar un mensaje de la cola |
| `edgehub_reported_properties_update_duration_seconds` | `target`<br> `id` | Tipo: resumen<br> Tiempo necesario para actualizar las propiedades notificadas |
| `edgehub_direct_method_duration_seconds` | `from` (autor de llamada)<br> `to` (receptor) | Tipo: resumen<br> Tiempo necesario para resolver un mensaje directo |
| `edgehub_direct_methods_total` | `from`<br> `to` | Tipo: contador<br> Número total de mensajes directos enviados |
| `edgehub_queue_length` | `endpoint` (origen del mensaje)<br> `priority` (prioridad de cola) | Tipo: medidor<br> Longitud actual de la cola de edgeHub para una prioridad determinada |
| `edgehub_messages_dropped_total` | `reason` (no_route, ttl_expiry)<br> `from` <br> `from_route_output` | Tipo: contador<br> Número total de mensajes eliminados debido al motivo |
| `edgehub_messages_unack_total` | `reason` (storage_failure)<br> `from`<br> `from_route_output` | Tipo: contador<br> Número total de mensajes no confirmados debido a un error de almacenamiento |
| `edgehub_offline_count_total` | `id` | Tipo: contador<br> Número total de veces que edgeHub se desconectó |
| `edgehub_offline_duration_seconds`| `id` | Tipo: resumen<br> Tiempo que edgeHub estuvo sin conexión |
| `edgehub_operation_retry_total` | `id`<br> `operation` (nombre de la operación) | Tipo: contador<br> Número total de veces que se reintentaron las operaciones de edgeHub |
| `edgehub_client_connect_failed_total` | `id` <br> `reason` (sin autenticar)<br> | Tipo: contador<br> Número total de veces que los clientes no pudieron conectarse a edgeHub |

El módulo **edgeAgent** genera las métricas siguientes:

| Nombre | Dimensions | Descripción |
|-|-|-|
| `edgeAgent_total_time_running_correctly_seconds` | `module_name` | Tipo: medidor<br> Cantidad de tiempo que se especificó el módulo en la implementación y que se encontraba en estado de ejecución. |
| `edgeAgent_total_time_expected_running_seconds` | `module_name` | Tipo: medidor<br> Cantidad de tiempo que se especificó el módulo en la implementación |
| `edgeAgent_module_start_total` | `module_name`, `module_version` | Tipo: contador<br> Número de veces que edgeAgent pidió a Docker iniciar el módulo |
| `edgeAgent_module_stop_total` | `module_name`, `module_version` | Tipo: contador<br> Número de veces que edgeAgent pidió a Docker detener el módulo |
| `edgeAgent_command_latency_seconds` | `command` | Tipo: medidor<br> Cuánto tiempo tardó Docker en ejecutar el comando especificado. Los comandos posibles son: create, update, remove, start, stop, restart. |
| `edgeAgent_iothub_syncs_total` | | Tipo: contador<br> Número de veces que edgeAgent intentó sincronizar su gemelo con iotHub, correctamente e incorrectamente. Este número incluye tanto el agente que solicita un gemelo como una hub que notifica de la actualización de un gemelo. |
| `edgeAgent_unsuccessful_iothub_syncs_total` | | Tipo: contador<br> Número de veces que edgeAgent no pudo sincronizar su gemelo con iotHub. |
| `edgeAgent_deployment_time_seconds` | | Tipo: contador<br> Cantidad de tiempo que se tardó en completar una nueva implementación después de recibir un cambio |
| `edgeagent_direct_method_invocations_count` | `method_name` | Tipo: contador<br> Número de veces que se llama a un método directo edgeAgent integrado, como Ping o Restart |
| `edgeAgent_host_uptime_seconds` | | Tipo: medidor<br> Cuánto tiempo ha estado activado el host |
| `edgeAgent_iotedged_uptime_seconds` | | Tipo: medidor<br> Cuánto tiempo ha estado iotedged en ejecución |
| `edgeAgent_available_disk_space_bytes` | `disk_name`, `disk_filesystem`, `disk_filetype` | Tipo: medidor<br> Cantidad de espacio restante en el disco |
| `edgeAgent_total_disk_space_bytes` | `disk_name`, `disk_filesystem`, `disk_filetype`| Tipo: medidor<br> Tamaño del disco |
| `edgeAgent_used_memory_bytes` | `module_name` | Tipo: medidor<br> Cantidad de RAM usada por todos los procesos |
| `edgeAgent_total_memory_bytes` | `module_name` | Tipo: medidor<br> RAM disponible |
| `edgeAgent_used_cpu_percent` | `module_name` | Tipo: histograma<br> Cantidad de CPU usada por todos los procesos |
| `edgeAgent_created_pids_total` | `module_name` | Tipo: medidor<br> Número de procesos o subprocesos que ha creado el contenedor |
| `edgeAgent_total_network_in_bytes` | `module_name` | Tipo: medidor<br> Número de bytes recibidos de la red |
| `edgeAgent_total_network_out_bytes` | `module_name` | Tipo: medidor<br> Número de bytes enviados a la red |
| `edgeAgent_total_disk_read_bytes` | `module_name` | Tipo: medidor<br> Número de bytes leídos del disco |
| `edgeAgent_total_disk_write_bytes` | `module_name` | Tipo: medidor<br> Número de bytes escritos en el disco |
| `edgeAgent_metadata` | `edge_agent_version`, `experimental_features`, `host_information` | Tipo: medidor<br> Metadatos generales sobre el dispositivo. El valor siempre es 0, la información está codificada en las etiquetas. Tenga en cuenta que `experimental_features` y `host_information` son objetos JSON. `host_information` tiene un aspecto similar a ```{"OperatingSystemType": "linux", "Architecture": "x86_64", "Version": "1.0.10~dev20200803.4", "Provisioning": {"Type": "dps.tpm", "DynamicReprovisioning": false, "AlwaysReprovisionOnStartup": true}, "ServerVersion": "19.03.6", "KernelVersion": "5.0.0-25-generic", "OperatingSystem": "Ubuntu 18.04.4 LTS", "NumCpus": 6, "Virtualized": "yes"}```. Tenga en cuenta `ServerVersion` es la versión de Docker, y `Version` es la versión del demonio de seguridad de IoT Edge. |

## <a name="next-steps"></a>Pasos siguientes

* [Recopilación y transporte de métricas](how-to-collect-and-transport-metrics.md)
* [Información del entorno de ejecución de Azure IoT Edge y su arquitectura](iot-edge-runtime.md)
* [Propiedades de los módulos gemelos del agente de IoT Edge y del centro de IoT Edge](module-edgeagent-edgehub.md)
