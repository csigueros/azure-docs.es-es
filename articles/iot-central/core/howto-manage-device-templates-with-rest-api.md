---
title: Uso de la API de REST para agregar plantillas de dispositivo en Azure IoT Central
description: Cómo usar la API de REST de IoT Central para agregar plantillas de dispositivo en una aplicación
author: v-krishnag
ms.author: v-krishnag
ms.date: 09/28/2021
ms.topic: how-to
ms.service: iot-central
services: iot-central
ms.openlocfilehash: be2c84ed1b825fcc257dfce444c043a58a3efbc3
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/02/2021
ms.locfileid: "131093569"
---
# <a name="how-to-use-the-iot-central-rest-api-to-manage-device-templates"></a>Uso de la API de REST de IoT Central para administrar plantillas de dispositivo

La API de REST de IoT Central permite desarrollar aplicaciones cliente que se integran con aplicaciones de IoT Central. Puede usar la API de REST para administrar [plantillas de dispositivo](concepts-device-templates.md) en la aplicación de IoT Central.

Cada llamada API de REST de IoT Central requiere un encabezado de autorización. Para obtener más información, vea los [procedimientos de autenticación y autorización de llamadas a la API REST de IoT Central](howto-authorize-rest-api.md).

Para ver la documentación de referencia sobre la API REST de IoT Central, consulte [Referencia de la API REST de Azure IoT Central](/rest/api/iotcentral/).

## <a name="device-templates"></a>Plantillas de dispositivo

Una plantilla de dispositivo contiene un modelo de dispositivo, definiciones de propiedades en la nube, personalizaciones y definiciones de vistas. La API de REST permite administrar el modelo de dispositivo, las definiciones de propiedades en la nube y las personalizaciones. Use la interfaz de usuario para crear y administrar las vistas.

La sección de modelo de dispositivo de una plantilla de dispositivo especifica las funcionalidades de un dispositivo que se quiere conectar a la aplicación. Las capacidades incluyen la telemetría, las propiedades y los comandos. El modelo se define mediante [DTDL](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v2/dtdlv2.md).

## <a name="device-templates-rest-api"></a>API de REST de plantillas de dispositivo

La API de REST de IoT Central le permite:

* Agregar una plantilla de dispositivo a la aplicación
* Actualizar una plantilla de dispositivo en la aplicación
* Obtener una lista de las plantillas de dispositivo en la aplicación
* Obtener una plantilla de dispositivo por identificador
* Eliminar una plantilla de dispositivo de la aplicación

### <a name="add-a-device-template"></a>Incorporación de una plantilla de dispositivo

Use la siguiente solicitud para crear y publicar una nueva plantilla de dispositivo. Se generan automáticamente vistas predeterminadas para las plantillas de dispositivo creadas de esta manera.

```http
PUT https://{subdomain}.{baseDomain}/api/deviceTemplates/{deviceTemplateId}?api-version=1.0
```

>[!NOTE]
>Los identificadores de plantilla de dispositivo siguen la convención de nomenclatura [DTDL](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v2/dtdlv2.md#digital-twin-model-identifier), por ejemplo: `dtmi:contoso:mythermostattemplate;1`

En el ejemplo siguiente se muestra un cuerpo de solicitud que agrega una plantilla de dispositivo de un dispositivo de termostato. `capabilityModel` incluye telemetría de temperatura, dos propiedades y un comando. La plantilla de dispositivo define la propiedad en la nube `CustomerName` y personaliza la propiedad `targetTemperature` con `decimalPlaces`, `displayUnit`, `maxValue` y `minValue`. El valor de la plantilla de dispositivo `@id` debe coincidir con el valor `deviceTemplateId` de la dirección URL. El valor de la plantilla de dispositivo `@id` no es el mismo que el valor de `capabilityModel` `@id`.

```json
{
    "displayName": "Thermostat",

    "@id": "dtmi:contoso:mythermostattemplate;1",
    "@type": [
        "ModelDefinition",
        "DeviceModel"
    ],
    "@context": [
        "dtmi:iotcentral:context;2",
        "dtmi:dtdl:context;2"
    ],
    "capabilityModel": {
        "@id": "dtmi:contoso:Thermostat;1",
        "@type": "Interface",
        "contents": [
            {
                "@type": [
                    "Telemetry",
                    "Temperature"
                ],
                "description": "Temperature in degrees Celsius.",
                "displayName": "Temperature",
                "name": "temperature",
                "schema": "double",
                "unit": "degreeCelsius"
            },
            {
                "@type": [
                    "Property",
                    "Temperature"
                ],
                "description": "Allows to remotely specify the desired target temperature.",
                "displayName": "Target Temperature",
                "name": "targetTemperature",
                "schema": "double",
                "unit": "degreeCelsius",
                "writable": true,
                "decimalPlaces": 1,
                "displayUnit": "C",
                "maxValue": 80,
                "minValue": 50
            },
            {
                "@type": [
                    "Property",
                    "Temperature"
                ],
                "description": "Returns the max temperature since last device reboot.",
                "displayName": "Max temperature since last reboot.",
                "name": "maxTempSinceLastReboot",
                "schema": "double",
                "unit": "degreeCelsius"
            },
            {
                "@type": "Command",
                "description": "This command returns the max, min and average temperature from the specified time to the current time.",
                "displayName": "Get report",
                "name": "getMaxMinReport",
                "request": {
                    "@type": "CommandPayload",
                    "description": "Period to return the max-min report.",
                    "displayName": "Since",
                    "name": "since",
                    "schema": "dateTime"
                },
                "response": {
                    "@type": "CommandPayload",
                    "displayName": "Temperature Report",
                    "name": "tempReport",
                    "schema": {
                        "@type": "Object",
                        "fields": [
                            {
                                "displayName": "Max temperature",
                                "name": "maxTemp",
                                "schema": "double"
                            },
                            {
                                "displayName": "Min temperature",
                                "name": "minTemp",
                                "schema": "double"
                            },
                            {
                                "displayName": "Average Temperature",
                                "name": "avgTemp",
                                "schema": "double"
                            },
                            {
                                "displayName": "Start Time",
                                "name": "startTime",
                                "schema": "dateTime"
                            },
                            {
                                "displayName": "End Time",
                                "name": "endTime",
                                "schema": "dateTime"
                            }
                        ]
                    }
                }
            },
            {
                "@type": [
                    "Property",
                    "Cloud",
                    "StringValue"
                ],
                "displayName": "Customer Name",
                "name": "CustomerName",
                "schema": "string"
            }
        ],
        "description": "Reports current temperature and provides desired temperature control.",
        "displayName": "Thermostat"
    }
}
```

El cuerpo de la solicitud tiene algunos campos obligatorios:

* `@id`: un identificador único en forma de nombre de recursos uniforme simple.
* `@type`: declara que este objeto es una interfaz.
* `@context`: especifica la versión de DTDL que se usa para la interfaz.
* `contents`: enumera las propiedades, la telemetría y los comandos que componen el dispositivo. Las funcionalidades pueden definirse en varias interfaces.
* `capabilityModel`: cada plantilla de dispositivo tiene un modelo de capacidad. Se establece una relación entre cada modelo de funcionalidad del módulo y un modelo de dispositivo. Un modelo de capacidad implementa una o varias interfaces de módulo.

Hay algunos campos opcionales que puede usar para agregar más detalles al modelo de funcionalidad, como el de nombre para mostrar y el de descripción.

Todas las entradas de la lista de interfaces de la sección de implementaciones tienen:

* `name`: el nombre de programación de la interfaz.
* `schema`: la interfaz que implementa el modelo de funcionalidad.

La respuesta a esta solicitud es similar al ejemplo siguiente: 

```json
{
    "etag": "\"~F27cqSo0ON3bfOzwgZxAl89/JVvM80+dds6y8+mZh5M=\"",
    "displayName": "Thermostat",
    "capabilityModel": {
        "@id": "dtmi:contoso:Thermostat;1",
        "@type": "Interface",
        "contents": [
            {
                "@type": [
                    "Telemetry",
                    "Temperature"
                ],
                "description": "Temperature in degrees Celsius.",
                "displayName": "Temperature",
                "name": "temperature",
                "schema": "double",
                "unit": "degreeCelsius"
            },
            {
                "@type": [
                    "Property",
                    "Temperature",
                    "NumberValue"
                ],
                "description": "Allows to remotely specify the desired target temperature.",
                "displayName": "Target Temperature",
                "name": "targetTemperature",
                "schema": "double",
                "unit": "degreeCelsius",
                "writable": true,
                "decimalPlaces": 1,
                "displayUnit": "C",
                "maxValue": 80,
                "minValue": 50
            },
            {
                "@type": [
                    "Property",
                    "Temperature"
                ],
                "description": "Returns the max temperature since last device reboot.",
                "displayName": "Max temperature since last reboot.",
                "name": "maxTempSinceLastReboot",
                "schema": "double",
                "unit": "degreeCelsius"
            },
            {
                "@type": "Command",
                "description": "This command returns the max, min and average temperature from the specified time to the current time.",
                "displayName": "Get report",
                "name": "getMaxMinReport",
                "request": {
                    "@type": "CommandPayload",
                    "description": "Period to return the max-min report.",
                    "displayName": "Since",
                    "name": "since",
                    "schema": "dateTime"
                },
                "response": {
                    "@type": "CommandPayload",
                    "displayName": "Temperature Report",
                    "name": "tempReport",
                    "schema": {
                        "@type": "Object",
                        "fields": [
                            {
                                "displayName": "Max temperature",
                                "name": "maxTemp",
                                "schema": "double"
                            },
                            {
                                "displayName": "Min temperature",
                                "name": "minTemp",
                                "schema": "double"
                            },
                            {
                                "displayName": "Average Temperature",
                                "name": "avgTemp",
                                "schema": "double"
                            },
                            {
                                "displayName": "Start Time",
                                "name": "startTime",
                                "schema": "dateTime"
                            },
                            {
                                "displayName": "End Time",
                                "name": "endTime",
                                "schema": "dateTime"
                            }
                        ]
                    }
                }
            },
            {
                "@type": [
                    "Property",
                    "Cloud",
                    "StringValue"
                ],
                "displayName": "Customer Name",
                "name": "CustomerName",
                "schema": "string"
            }
        ],
        "description": "Reports current temperature and provides desired temperature control.",
        "displayName": "Thermostat"
    },
    "@id": "dtmi:modelDefinition:spzeut3n:n2lteu39u6",
    "@type": [
        "ModelDefinition",
        "DeviceModel"
    ],
    "@context": [
        "dtmi:iotcentral:context;2",
        "dtmi:dtdl:context;2"
    ]
}

```

### <a name="get-a-device-template"></a>Obtención de una plantilla de dispositivo

Use la siguiente solicitud para recuperar detalles de una plantilla de dispositivo desde la aplicación:

```http
GET https://{subdomain}.{baseDomain}/api/deviceTemplates/{deviceTemplateId}?api-version=1.0
```

>[!NOTE]
> Puede obtener `deviceTemplateId` desde la interfaz de usuario de la aplicación de IoT Central si mantiene el mouse sobre un dispositivo.

La respuesta a esta solicitud es similar al ejemplo siguiente:

```json
{
    "etag": "\"~F27cqSo0ON3bfOzwgZxAl89/JVvM80+dds6y8+mZh5M=\"",
    "displayName": "Thermostat",
    "capabilityModel": {
        "@id": "dtmi:contoso:Thermostat;1",
        "@type": "Interface",
        "contents": [
            {
                "@type": [
                    "Telemetry",
                    "Temperature"
                ],
                "description": "Temperature in degrees Celsius.",
                "displayName": "Temperature",
                "name": "temperature",
                "schema": "double",
                "unit": "degreeCelsius"
            },
            {
                "@type": [
                    "Property",
                    "Temperature",
                    "NumberValue"
                ],
                "description": "Allows to remotely specify the desired target temperature.",
                "displayName": "Target Temperature",
                "name": "targetTemperature",
                "schema": "double",
                "unit": "degreeCelsius",
                "writable": true,
                "decimalPlaces": 1,
                "displayUnit": "C",
                "maxValue": 80,
                "minValue": 50
            },
            {
                "@type": [
                    "Property",
                    "Temperature"
                ],
                "description": "Returns the max temperature since last device reboot.",
                "displayName": "Max temperature since last reboot.",
                "name": "maxTempSinceLastReboot",
                "schema": "double",
                "unit": "degreeCelsius"
            },
            {
                "@type": "Command",
                "description": "This command returns the max, min and average temperature from the specified time to the current time.",
                "displayName": "Get report",
                "name": "getMaxMinReport",
                "request": {
                    "@type": "CommandPayload",
                    "description": "Period to return the max-min report.",
                    "displayName": "Since",
                    "name": "since",
                    "schema": "dateTime"
                },
                "response": {
                    "@type": "CommandPayload",
                    "displayName": "Temperature Report",
                    "name": "tempReport",
                    "schema": {
                        "@type": "Object",
                        "fields": [
                            {
                                "displayName": "Max temperature",
                                "name": "maxTemp",
                                "schema": "double"
                            },
                            {
                                "displayName": "Min temperature",
                                "name": "minTemp",
                                "schema": "double"
                            },
                            {
                                "displayName": "Average Temperature",
                                "name": "avgTemp",
                                "schema": "double"
                            },
                            {
                                "displayName": "Start Time",
                                "name": "startTime",
                                "schema": "dateTime"
                            },
                            {
                                "displayName": "End Time",
                                "name": "endTime",
                                "schema": "dateTime"
                            }
                        ]
                    }
                }
            },
            {
                "@type": [
                    "Property",
                    "Cloud",
                    "StringValue"
                ],
                "displayName": "Customer Name",
                "name": "CustomerName",
                "schema": "string"
            }
        ],
        "description": "Reports current temperature and provides desired temperature control.",
        "displayName": "Thermostat"
    },
    "@id": "dtmi:modelDefinition:spzeut3n:n2lteu39u6",
    "@type": [
        "ModelDefinition",
        "DeviceModel"
    ],
    "@context": [
        "dtmi:iotcentral:context;2",
        "dtmi:dtdl:context;2"
    ]
}
```

### <a name="list-device-templates"></a>Lista de plantillas de dispositivo

Use la siguiente solicitud para recuperar una lista de plantillas de dispositivo desde la aplicación:

```http
GET https://{subdomain}.{baseDomain}/api/deviceTemplates?api-version=1.0
```

La respuesta a esta solicitud es similar al ejemplo siguiente: 

```json
{
    "value": [
        {
            "etag": "\"~F27cqSo0ON3bfOzwgZxAl89/JVvM80+dds6y8+mZh5M=\"",
            "displayName": "Thermostat",
            "capabilityModel": {
                "@id": "dtmi:contoso:Thermostat;1",
                "@type": "Interface",
                "contents": [
                    {
                        "@type": [
                            "Telemetry",
                            "Temperature"
                        ],
                        "description": "Temperature in degrees Celsius.",
                        "displayName": "Temperature",
                        "name": "temperature",
                        "schema": "double",
                        "unit": "degreeCelsius"
                    },
                    {
                        "@type": [
                            "Property",
                            "Temperature",
                            "NumberValue"
                        ],
                        "description": "Allows to remotely specify the desired target temperature.",
                        "displayName": "Target Temperature",
                        "name": "targetTemperature",
                        "schema": "double",
                        "unit": "degreeCelsius",
                        "writable": true,
                        "decimalPlaces": 1,
                        "displayUnit": "C",
                        "maxValue": 80,
                        "minValue": 50
                    },
                    {
                        "@type": [
                            "Property",
                            "Temperature"
                        ],
                        "description": "Returns the max temperature since last device reboot.",
                        "displayName": "Max temperature since last reboot.",
                        "name": "maxTempSinceLastReboot",
                        "schema": "double",
                        "unit": "degreeCelsius"
                    },
                    {
                        "@type": "Command",
                        "description": "This command returns the max, min and average temperature from the specified time to the current time.",
                        "displayName": "Get report",
                        "name": "getMaxMinReport",
                        "request": {
                            "@type": "CommandPayload",
                            "description": "Period to return the max-min report.",
                            "displayName": "Since",
                            "name": "since",
                            "schema": "dateTime"
                        },
                        "response": {
                            "@type": "CommandPayload",
                            "displayName": "Temperature Report",
                            "name": "tempReport",
                            "schema": {
                                "@type": "Object",
                                "fields": [
                                    {
                                        "displayName": "Max temperature",
                                        "name": "maxTemp",
                                        "schema": "double"
                                    },
                                    {
                                        "displayName": "Min temperature",
                                        "name": "minTemp",
                                        "schema": "double"
                                    },
                                    {
                                        "displayName": "Average Temperature",
                                        "name": "avgTemp",
                                        "schema": "double"
                                    },
                                    {
                                        "displayName": "Start Time",
                                        "name": "startTime",
                                        "schema": "dateTime"
                                    },
                                    {
                                        "displayName": "End Time",
                                        "name": "endTime",
                                        "schema": "dateTime"
                                    }
                                ]
                            }
                        }
                    },
                    {
                        "@type": [
                            "Property",
                            "Cloud",
                            "StringValue"
                        ],
                        "displayName": "Customer Name",
                        "name": "CustomerName",
                        "schema": "string"
                    }
                ],
                "description": "Reports current temperature and provides desired temperature control.",
                "displayName": "Thermostat"
            },
            "@id": "dtmi:modelDefinition:spzeut3n:n2lteu39u6",
            "@type": [
                "ModelDefinition",
                "DeviceModel"
            ],
            "@context": [
                "dtmi:iotcentral:context;2",
                "dtmi:dtdl:context;2"
            ]
        },
        {
            "etag": "\"~XS5GovPNzJqFIwkkV/vyWW5U/6if2NwC/NqUlDxExAY=\"",
            "displayName": "Thermostat2",
            "capabilityModel": {
                "@id": "dtmi:contoso:Thermostat2;1",
                "@type": "Interface",
                "contents": [
                    {
                        "@type": [
                            "Telemetry",
                            "Temperature"
                        ],
                        "description": "Temperature in degrees Celsius.",
                        "displayName": "Temperature",
                        "name": "temperature",
                        "schema": "double",
                        "unit": "degreeCelsius"
                    },
                    {
                        "@type": [
                            "Property",
                            "Temperature",
                            "NumberValue"
                        ],
                        "description": "Allows to remotely specify the desired target temperature.",
                        "displayName": "Target Temperature",
                        "name": "targetTemperature",
                        "schema": "double",
                        "unit": "degreeCelsius",
                        "writable": true,
                        "decimalPlaces": 1,
                        "displayUnit": "C",
                        "maxValue": 80,
                        "minValue": 50
                    },
                    {
                        "@type": [
                            "Property",
                            "Temperature"
                        ],
                        "description": "Returns the max temperature since last device reboot.",
                        "displayName": "Max temperature since last reboot.",
                        "name": "maxTempSinceLastReboot",
                        "schema": "double",
                        "unit": "degreeCelsius"
                    },
                    {
                        "@type": "Command",
                        "description": "This command returns the max, min and average temperature from the specified time to the current time.",
                        "displayName": "Get report",
                        "name": "getMaxMinReport",
                        "request": {
                            "@type": "CommandPayload",
                            "description": "Period to return the max-min report.",
                            "displayName": "Since",
                            "name": "since",
                            "schema": "dateTime"
                        },
                        "response": {
                            "@type": "CommandPayload",
                            "displayName": "Temperature Report",
                            "name": "tempReport",
                            "schema": {
                                "@type": "Object",
                                "fields": [
                                    {
                                        "displayName": "Max temperature",
                                        "name": "maxTemp",
                                        "schema": "double"
                                    },
                                    {
                                        "displayName": "Min temperature",
                                        "name": "minTemp",
                                        "schema": "double"
                                    },
                                    {
                                        "displayName": "Average Temperature",
                                        "name": "avgTemp",
                                        "schema": "double"
                                    },
                                    {
                                        "displayName": "Start Time",
                                        "name": "startTime",
                                        "schema": "dateTime"
                                    },
                                    {
                                        "displayName": "End Time",
                                        "name": "endTime",
                                        "schema": "dateTime"
                                    }
                                ]
                            }
                        }
                    },
                    {
                        "@type": [
                            "Property",
                            "Cloud",
                            "StringValue"
                        ],
                        "displayName": "Customer Name",
                        "name": "CustomerName",
                        "schema": "string"
                    }
                ],
                "description": "Reports current temperature and provides desired temperature control.",
                "displayName": "Thermostat"
            },
            "@id": "dtmi:modelDefinition:spzeut3n:n2lteu39u67",
            "@type": [
                "ModelDefinition",
                "DeviceModel"
            ],
            "@context": [
                "dtmi:iotcentral:context;2",
                "dtmi:dtdl:context;2"
            ]
        }
    ]
}
```

### <a name="update-a-device-template"></a>Actualizar una plantilla de dispositivo

```http
PATCH https://{subdomain}.{baseDomain}/api/deviceTemplates/{deviceTemplateId}?api-version=1.0
```

>[!NOTE] 
>`{deviceTemplateId}` debe ser igual que el elemento `@id` de la carga.

El cuerpo de la solicitud de ejemplo es similar al ejemplo siguiente, que agrega una propiedad en la nube `LastMaintenanceDate` a la plantilla de dispositivo:

```json
{
    "displayName": "Thermostat",

    "@id": "dtmi:contoso:mythermostattemplate",
    "@type": [
        "ModelDefinition",
        "DeviceModel"
    ],
    "@context": [
        "dtmi:iotcentral:context;2",
        "dtmi:dtdl:context;2"
    ],
    "capabilityModel": {
        "@id": "dtmi:contoso:Thermostat;1",
        "@type": "Interface",
        "contents": [
            {
                "@type": [
                    "Telemetry",
                    "Temperature"
                ],
                "description": "Temperature in degrees Celsius.",
                "displayName": "Temperature",
                "name": "temperature",
                "schema": "double",
                "unit": "degreeCelsius"
            },
            {
                "@type": [
                    "Property",
                    "Temperature"
                ],
                "description": "Allows to remotely specify the desired target temperature.",
                "displayName": "Target Temperature",
                "name": "targetTemperature",
                "schema": "double",
                "unit": "degreeCelsius",
                "writable": true,
                "decimalPlaces": 1,
                "displayUnit": "C",
                "maxValue": 80.0,
                "minValue": 50.0
            },
            {
                "@type": [
                    "Property",
                    "Temperature"
                ],
                "description": "Returns the max temperature since last device reboot.",
                "displayName": "Max temperature since last reboot.",
                "name": "maxTempSinceLastReboot",
                "schema": "double",
                "unit": "degreeCelsius"
            },
            {
                "@type": "Command",
                "description": "This command returns the max, min and average temperature from the specified time to the current time.",
                "displayName": "Get report",
                "name": "getMaxMinReport",
                "request": {
                    "@type": "CommandPayload",
                    "description": "Period to return the max-min report.",
                    "displayName": "Since",
                    "name": "since",
                    "schema": "dateTime"
                },
                "response": {
                    "@type": "CommandPayload",
                    "displayName": "Temperature Report",
                    "name": "tempReport",
                    "schema": {
                        "@type": "Object",
                        "fields": [
                            {
                                "displayName": "Max temperature",
                                "name": "maxTemp",
                                "schema": "double"
                            },
                            {
                                "displayName": "Min temperature",
                                "name": "minTemp",
                                "schema": "double"
                            },
                            {
                                "displayName": "Average Temperature",
                                "name": "avgTemp",
                                "schema": "double"
                            },
                            {
                                "displayName": "Start Time",
                                "name": "startTime",
                                "schema": "dateTime"
                            },
                            {
                                "displayName": "End Time",
                                "name": "endTime",
                                "schema": "dateTime"
                            }
                        ]
                    }
                }
            },
            {
                "@type": [
                    "Property",
                    "Cloud",
                    "StringValue"
                ],
                "displayName": "Customer Name",
                "name": "CustomerName",
                "schema": "string"
            },
            {
                "@type": [
                    "Property",
                    "Cloud",
                    "StringValue"
                ],
                "displayName": "Last Maintenance Date",
                "name": "LastMaintenanceDate",
                "schema": "dateTime"
            }
        ],
        "description": "Reports current temperature and provides desired temperature control.",
        "displayName": "Thermostat"
    }
}

```

La respuesta a esta solicitud es similar al ejemplo siguiente:

```json
{
    "etag": "\"~6Ku691rHAgw/yw8u+ygZJGAKjSN4P4q/KxCU2xskrmk=\"",
    "displayName": "Thermostat",
    "capabilityModel": {
        "@id": "dtmi:contoso:Thermostat;1",
        "@type": "Interface",
        "contents": [
            {
                "@type": [
                    "Telemetry",
                    "Temperature"
                ],
                "description": "Temperature in degrees Celsius.",
                "displayName": "Temperature",
                "name": "temperature",
                "schema": "double",
                "unit": "degreeCelsius"
            },
            {
                "@type": [
                    "Property",
                    "Temperature"
                ],
                "description": "Allows to remotely specify the desired target temperature.",
                "displayName": "Target Temperature",
                "name": "targetTemperature",
                "schema": "double",
                "unit": "degreeCelsius",
                "writable": true,
                "decimalPlaces": 1,
                "displayUnit": "C",
                "maxValue": 80,
                "minValue": 50
            },
            {
                "@type": [
                    "Property",
                    "Temperature"
                ],
                "description": "Returns the max temperature since last device reboot.",
                "displayName": "Max temperature since last reboot.",
                "name": "maxTempSinceLastReboot",
                "schema": "double",
                "unit": "degreeCelsius"
            },
            {
                "@type": "Command",
                "description": "This command returns the max, min and average temperature from the specified time to the current time.",
                "displayName": "Get report",
                "name": "getMaxMinReport",
                "request": {
                    "@type": "CommandPayload",
                    "description": "Period to return the max-min report.",
                    "displayName": "Since",
                    "name": "since",
                    "schema": "dateTime"
                },
                "response": {
                    "@type": "CommandPayload",
                    "displayName": "Temperature Report",
                    "name": "tempReport",
                    "schema": {
                        "@type": "Object",
                        "fields": [
                            {
                                "displayName": "Max temperature",
                                "name": "maxTemp",
                                "schema": "double"
                            },
                            {
                                "displayName": "Min temperature",
                                "name": "minTemp",
                                "schema": "double"
                            },
                            {
                                "displayName": "Average Temperature",
                                "name": "avgTemp",
                                "schema": "double"
                            },
                            {
                                "displayName": "Start Time",
                                "name": "startTime",
                                "schema": "dateTime"
                            },
                            {
                                "displayName": "End Time",
                                "name": "endTime",
                                "schema": "dateTime"
                            }
                        ]
                    }
                }
            },
            {
                "@type": [
                    "Property",
                    "Cloud",
                    "StringValue"
                ],
                "displayName": "Customer Name",
                "name": "CustomerName",
                "schema": "string"
            },
            {
                "@type": [
                    "Property",
                    "Cloud",
                    "DateTimeValue"
                ],
                "displayName": "Last Maintenance Date",
                "name": "LastMaintenanceDate",
                "schema": "dateTime"
            }
        ],
        "description": "Reports current temperature and provides desired temperature control.",
        "displayName": "Thermostat"
    },
    "@id": "dtmi:modelDefinition:spzeut3n:n2lteu39u6",
    "@type": [
        "ModelDefinition",
        "DeviceModel"
    ],
    "@context": [
        "dtmi:iotcentral:context;2",
        "dtmi:dtdl:context;2"
    ]
}
```

### <a name="delete-a-device-template"></a>Eliminar una plantilla de dispositivo

Use la siguiente solicitud para eliminar una plantilla de dispositivo:

```http
DELETE https://{subdomain}.{baseDomain}/api/deviceTemplates/{deviceTemplateId}?api-version=1.0
```

## <a name="next-steps"></a>Pasos siguientes

Ahora que ha aprendido a administrar plantillas de dispositivo con la API de REST, el siguiente paso que se sugiere es [crear plantillas de dispositivo desde la GUI de IoT Central](howto-set-up-template.md#create-a-device-template).
