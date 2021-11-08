---
title: Esquema JSON del módulo gemelo de Azure Video Analyzer
description: En este artículo se proporciona información general sobre el esquema JSON del módulo gemelo para el módulo perimetral de Azure Video Analyzer
ms.topic: how-to
ms.date: 11/04/2021
ms.custom: ignite-fall-2021
ms.openlocfilehash: 360102a551ea6321a4b40063123176fc13672929
ms.sourcegitcommit: e41827d894a4aa12cbff62c51393dfc236297e10
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/04/2021
ms.locfileid: "131559397"
---
# <a name="module-twin-configuration-schema"></a>Esquema de configuración de módulos gemelos

[!INCLUDE [header](includes/edge-env.md)]

Los dispositivos gemelos son documentos JSON que almacenan información acerca del estado del dispositivo, incluidos metadatos, configuraciones y condiciones. Azure IoT Hub mantiene un dispositivo gemelo para cada dispositivo que se conecta a IoT Hub. Si desea una explicación detallada, consulte [Uso de módulos gemelos en IoT Hub](../../../iot-hub/iot-hub-devguide-module-twins.md).

En este tema se describe el esquema JSON de módulo gemelo del módulo perimetral de Azure Video Analyzer.

## <a name="module-twin-properties"></a>Propiedad del módulo gemelo

El módulo perimetral de Video Analyzer expone las siguientes propiedades de módulo gemelo.

| Propiedad                    | Obligatorio | Dinámica | Descripción                                                  |
| :-------------------------- | :------- | :------ | :----------------------------------------------------------- |
| `ProvisioningToken`          | Sí      | No      | Token de autenticación para validar el módulo perimetral y aprovisionar los servicios en la nube (incluido el acceso a la cuenta de Video Analyzer) |
| `ApplicationDataDirectory`    | Sí      | No      | Ruta de acceso dentro del sistema de archivos del módulo que se asigna a un volumen montado para conservar la configuración.       |
| `DiagnosticsEventsOutputName` | No       | Sí     | Salida del centro para los eventos de diagnóstico. (Vacío significa que los diagnósticos no están publicados) |
| `OperationalEventsOutputName` | No       | Sí     | Salida del concentrador para eventos operativos. (Vacío significa que los eventos operativos no se publican) |
| `LogLevel`                    | No       | Sí     | Uno de los siguientes: · Detallado · Información (valor predeterminado) · Advertencia · Error · Ninguno |
| `LogCategories`               | No       | Sí     | Lista separada por comas de los siguientes elementos: Application, MediaPipeline, Events Valor predeterminado: Application, Events |
| `AllowUnsecuredEndpoints`     | No       | Sí     | Valor booleano para permitir la creación de topologías con puntos de conexión no seguros, como `#Microsoft.VideoAnalyzer.UnsecuredEndpoint`; valor predeterminado -true        |
| `TelemetryOptOut`             | No       | No     | Valor booleano para no participar en el envío de datos de telemetría; valor predeterminado -false       |
| `DebugLogsDirectory`          | No       | Sí     | Directorio para los registros de depuración. Si está presente, se generan registros. Si no están presentes, los registros de depuración están deshabilitados.       |

Las propiedades dinámicas se pueden actualizar sin necesidad de reiniciar el módulo. 

Consulte el artículo sobre [la supervisión y el registro](monitor-log-edge.md) para obtener más información sobre el rol de la configuración de diagnóstico opcional.

```json
{
    "properties.desired": {
        "ProvisioningToken": "$AVA_PROVISIONING_TOKEN",
        "ApplicationDataDirectory": "/var/lib/videoanalyzer",
        "DiagnosticsEventsOutputName": "diagnostics",
        "OperationalEventsOutputName": "operational",
        "LogLevel": "information",
        "LogCategories": "Application,Events",
        "DebugLogsDirectory": "/tmp/logs",
        "AllowUnsecuredEndpoints": true,
        "TelemetryOptOut": false    
     
    }
}
```

## <a name="next-steps"></a>Pasos siguientes

[Métodos directos](direct-methods.md)
