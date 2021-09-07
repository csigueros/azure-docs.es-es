---
title: Descripción de los modelos de dispositivo IoT Plug and Play | Microsoft Docs
description: Comprenda el lenguaje de modelado del lenguaje de definición de Digital Twins (DTDL) para dispositivos IoT Plug and Play. En el artículo se describen los tipos de datos primitivos y complejos, los patrones de reutilización que usan componentes y herencia, y los tipos semánticos. El artículo ofrece una guía sobre la elección del identificador de modelo gemelo del dispositivo y la compatibilidad con las herramientas para la creación de modelos.
author: dominicbetts
ms.author: dobett
ms.date: 03/09/2021
ms.topic: conceptual
ms.service: iot-develop
services: iot-develop
ms.openlocfilehash: b79c72f6711519c87cb671ea5b7d72eaffabe7e2
ms.sourcegitcommit: 8669087bcbda39e3377296c54014ce7b58909746
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 07/18/2021
ms.locfileid: "114406582"
---
# <a name="iot-plug-and-play-modeling-guide"></a>Guía de modelado de IoT Plug and Play

IoT Plug and Play se basa en un _modelo_ de dispositivo que describe las funcionalidades del dispositivo para una aplicación compatible con IoT Plug and Play. Este modelo se estructura como un conjunto de interfaces que definen:

- _Propiedades_ que representan el estado de solo lectura y grabable de un dispositivo o de otra entidad. Por ejemplo, el número de serie de un dispositivo puede ser una propiedad de solo lectura, y la temperatura objetivo de un termostato puede ser una propiedad grabable.
- Campos de _telemetría_ que definen los datos que emite un dispositivo, independientemente de que sean una secuencia normal de lecturas de un sensor, un error ocasional o un mensaje informativo.
- _Comandos_ que describen una función u operación que se puede realizar en un dispositivo. Por ejemplo, un comando puede reiniciar una puerta de enlace o tomar una imagen mediante una cámara remota.

Para más información sobre cómo IoT Plug and Play usa los modelos de dispositivo, consulte la [Guía para desarrolladores de dispositivos IoT Plug and Play](concepts-developer-guide-device.md) y la [Guía para desarrolladores de servicios IoT Plug and Play](concepts-developer-guide-service.md).

Para definir un modelo, se usa el lenguaje de definición de Digital Twins (DTDL). DTDL usa una variante de JSON llamada [JSON-LD](https://json-ld.org/). En el fragmento de código siguiente se muestra el modelo de dispositivo de un termostato que:

- Tiene un identificador de modelo único: `dtmi:com:example:Thermostat;1`.
- Envía datos de telemetría de temperatura.
- Tiene una propiedad de grabable para establecer la temperatura de destino.
- Tiene una propiedad de solo lectura para notificar la temperatura máxima desde el último reinicio.
- Responde a un comando que solicita las temperaturas máxima, mínima y media a lo largo de un período de tiempo.

```json
{
  "@context": "dtmi:dtdl:context;2",
  "@id": "dtmi:com:example:Thermostat;1",
  "@type": "Interface",
  "displayName": "Thermostat",
  "description": "Reports current temperature and provides desired temperature control.",
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
      "description": "This command returns the max, min and average temperature from the specified time to the current time.",
      "request": {
        "name": "since",
        "displayName": "Since",
        "description": "Period to return the max-min report.",
        "schema": "dateTime"
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
            },
            {
              "name": "avgTemp",
              "displayName": "Average Temperature",
              "schema": "double"
            },
            {
              "name": "startTime",
              "displayName": "Start Time",
              "schema": "dateTime"
            },
            {
              "name": "endTime",
              "displayName": "End Time",
              "schema": "dateTime"
            }
          ]
        }
      }
    }
  ]
}
```

El modelo del termostato tiene una sola interfaz. En ejemplos posteriores de este artículo se muestran modelos más complejos que usan componentes y herencia.

En este artículo se describe cómo diseñar y crear sus propios modelos; además, se tratan temas como los tipos de datos, la estructura del modelo y las herramientas.

Para obtener más información, consulte la especificación [Lenguaje de definición de Digital Twins v2](https://github.com/Azure/opendigitaltwins-dtdl).

## <a name="model-structure"></a>Estructura del modelo

Las propiedades, la telemetría y los comandos se agrupan en interfaces. En esta sección se describe cómo puede utilizar las interfaces para describir modelos simples y complejos mediante el uso de componentes y herencia.

### <a name="model-ids"></a>Identificadores de modelo

Cada interfaz tiene un identificador de modelo de Digital Twin (DTMI) único. Los modelos complejos usan DTMI para identificar los componentes. Las aplicaciones pueden usar los DTMI que los dispositivos envían para buscar definiciones de modelo en un repositorio.

Los DTMI deben seguir la convención de nomenclatura que requiere el [repositorio de modelos IoT Plug and Play](https://github.com/Azure/iot-plugandplay-models):

- El prefijo del DTMI es `dtmi:`.
- El sufijo del DTMI es el número de versión del modelo como `;2`.
- El cuerpo del DTMI se asigna a la carpeta y al archivo del repositorio del modelo donde se almacena el modelo. El número de versión forma parte del nombre de archivo.

Por ejemplo, el modelo identificado por el DTMI `dtmi:com:Example:Thermostat;2` se almacena en el archivo *dtmi/com/example/thermostat-2.json*.

En el fragmento de código siguiente se muestra el esquema de una definición de interfaz con su DTMI único:

```json
{
  "@context": "dtmi:dtdl:context;2",
  "@id": "dtmi:com:example:Thermostat;2",
  "@type": "Interface",
  "displayName": "Thermostat",
  "description": "Reports current temperature and provides desired temperature control.",
  "contents": [
    ...
  ]
}
```

### <a name="no-components"></a>Sin componentes

Un modelo simple, como el termostato mostrado anteriormente, no utiliza componentes incrustados o en cascada. La telemetría, las propiedades y los comandos se definen en el nodo `contents` de la interfaz.

En el ejemplo siguiente se muestra parte de un modelo sencillo que no utiliza componentes:

```json
{
  "@context": "dtmi:dtdl:context;2",
  "@id": "dtmi:com:example:Thermostat;1",
  "@type": "Interface",
  "displayName": "Thermostat",
  "description": "Reports current temperature and provides desired temperature control.",
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
...
```

Herramientas como Azure IoT Explorer y el diseñador de plantillas de dispositivos IoT Central etiquetan una interfaz independiente como el termostato como _componente predeterminado_.

En la captura de pantalla siguiente se muestra cómo se visualiza el modelo en la herramienta Azure IoT Explorer:

:::image type="content" source="media/concepts-modeling-guide/default-component.png" alt-text="Componente predeterminado en Azure IoT Explorer":::

En la captura de pantalla siguiente puede ver cómo se muestra el modelo como componente predeterminado en el diseñador de plantillas de dispositivos IoT Central. Seleccione **Ver identidad** para ver el DTMI del modelo:

:::image type="content" source="media/concepts-modeling-guide/iot-central-model.png" alt-text="Captura de pantalla que muestra el modelo de termostato en el diseñador de IoT Central":::

El identificador de modelo se almacena en una propiedad de dispositivo gemelo, como muestra la captura de pantalla siguiente:

:::image type="content" source="media/concepts-modeling-guide/twin-model-id.png" alt-text="Identificador de modelo en la propiedad de gemelo digital":::

Un modelo de DTDL sin componentes es una simplificación útil de un dispositivo o de un módulo de IoT Edge con un único conjunto de telemetría, propiedades y comandos. Un modelo de este tipo facilita la migración de los dispositivos o los módulos existentes a dispositivos o módulos IoT Plug and Play; se crea un modelo de DTDL que describe el dispositivo o el módulo reales sin necesidad de definir ningún componente.

> [!TIP]
> Un [módulo](../iot-hub/iot-hub-devguide-module-twins.md) puede ser de dispositivo o de [IoT Edge](../iot-edge/about-iot-edge.md).

### <a name="reuse"></a>Reutilización

Hay dos maneras de reutilizar las definiciones de interfaz. Usar varios componentes en un modelo para hacer referencia a otras definiciones de interfaz. Usar la herencia para ampliar las definiciones de interfaz existentes.

### <a name="multiple-components"></a>Varios componentes

Los componentes permiten compilar una interfaz del modelo como un ensamblado de otras interfaces.

Por ejemplo, la interfaz [Thermostat](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v2/samples/Thermostat.json) se define como un modelo. Puede incorporar esta interfaz como uno o varios componentes cuando defina el [modelo de controlador de temperatura](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v2/samples/TemperatureController.json). En el ejemplo siguiente, estos componentes se denominan `thermostat1` y `thermostat2`.

En el caso de un modelo de DTDL con varios componentes, hay dos o más secciones de componentes. Cada sección tiene `@type` establecido en `Component` y hace referencia explícita a un esquema, como se muestra en el siguiente fragmento de código:

```json
{
  "@context": "dtmi:dtdl:context;2",
  "@id": "dtmi:com:example:TemperatureController;1",
  "@type": "Interface",
  "displayName": "Temperature Controller",
  "description": "Device with two thermostats and remote reboot.",
  "contents": [
    {
      "@type": [
        "Telemetry",
        "DataSize"
      ],
      "name": "workingSet",
      "displayName": "Working Set",
      "description": "Current working set of the device memory in KiB.",
      "schema": "double",
      "unit": "kibibyte"
    },
    {
      "@type": "Property",
      "name": "serialNumber",
      "displayName": "Serial Number",
      "description": "Serial number of the device.",
      "schema": "string"
    },
    {
      "@type": "Command",
      "name": "reboot",
      "displayName": "Reboot",
      "description": "Reboots the device after waiting the number of seconds specified.",
      "request": {
        "name": "delay",
        "displayName": "Delay",
        "description": "Number of seconds to wait before rebooting the device.",
        "schema": "integer"
      }
    },
    {
      "@type" : "Component",
      "schema": "dtmi:com:example:Thermostat;1",
      "name": "thermostat1",
      "displayName": "Thermostat One",
      "description": "Thermostat One of Two."
    },
    {
      "@type" : "Component",
      "schema": "dtmi:com:example:Thermostat;1",
      "name": "thermostat2",
      "displayName": "Thermostat Two",
      "description": "Thermostat Two of Two."
    },
    {
      "@type": "Component",
      "schema": "dtmi:azure:DeviceManagement:DeviceInformation;1",
      "name": "deviceInformation",
      "displayName": "Device Information interface",
      "description": "Optional interface with basic device hardware information."
    }
  ]
}
```

Este modelo tiene tres componentes definidos en la sección de contenido: dos componentes `Thermostat` y un componente `DeviceInformation`. La sección de contenido también incluye definiciones de propiedades, telemetría y comandos.

Las capturas de pantallas siguientes muestran cómo aparece este modelo en IoT Central. Las definiciones de propiedades, telemetría y comandos del controlador de temperatura aparecen en el **componente predeterminado** de nivel superior. Las definiciones de propiedades, telemetría y comandos de cada termostato aparecen en las definiciones de componentes:

:::image type="content" source="media/concepts-modeling-guide/temperature-controller.png" alt-text="Captura de pantalla que muestra la plantilla de dispositivo del controlador de temperatura en IoT Central.":::

:::image type="content" source="media/concepts-modeling-guide/temperature-controller-components.png" alt-text="Captura de pantalla que muestra los componentes del termostato en la plantilla de dispositivo del controlador de temperatura en IoT Central.":::

Para obtener información sobre cómo escribir código de dispositivo que interactúe con los componentes, consulte la [Guía para desarrolladores de dispositivos IoT Plug and Play](concepts-developer-guide-device.md).

Para obtener información sobre cómo escribir código de servicio que interactúe con los componentes de un dispositivo, consulte la [Guía para desarrolladores de servicios IoT Plug and Play](concepts-developer-guide-service.md).

### <a name="inheritance"></a>Herencia

La herencia permite volver a usar las funcionalidades de una interfaz base para ampliar las capacidades de una interfaz. Por ejemplo, varios modelos de dispositivo pueden compartir funcionalidades comunes, como un número de serie:

:::image type="content" source="media/concepts-modeling-guide/inheritance.png" alt-text="Ejemplo de herencia en un modelo de dispositivo que muestra un termostato y un controlador de flujo que comparten funciones de una interfaz base." border="false":::

En el fragmento de código siguiente se muestra un modelo DTML que usa la palabra clave `extends` para definir la relación de herencia que se muestra en el diagrama anterior:

```json
[
  {
    "@context": "dtmi:dtdl:context;2",
    "@id": "dtmi:com:example:Thermostat;1",
    "@type": "Interface",
    "contents": [
      {
        "@type": "Telemetry",
        "name": "temperature",
        "schema": "double",
        "unit": "degreeCelsius"
      },
      {
        "@type": "Property",
        "name": "targetTemperature",
        "schema": "double",
        "unit": "degreeCelsius",
        "writable": true
      }
    ],
    "extends": [
      "dtmi:com:example:baseDevice;1"
    ]
  },
  {
    "@context": "dtmi:dtdl:context;2",
    "@id": "dtmi:com:example:baseDevice;1",
    "@type": "Interface",
    "contents": [
      {
        "@type": "Property",
        "name": "SerialNumber",
        "schema": "double",
        "writable": false
      }
    ]
  }
]
```

En la captura de pantalla siguiente se puede ver este modelo en el entorno de la plantilla de dispositivos de IoT Central:

:::image type="content" source="media/concepts-modeling-guide/iot-central-inheritance.png" alt-text="Captura de pantalla que muestra la herencia de interfaz en IoT Central":::

Al escribir el código del dispositivo o del servicio, el código no necesita hacer nada especial para controlar las interfaces heredadas. En el ejemplo que se muestra en esta sección, el código del dispositivo informa del número de serie como si formara parte de la interfaz del termostato.

### <a name="tips"></a>Sugerencias

Puede combinar componentes y herencia al crear un modelo. En el diagrama siguiente se muestra un modelo `thermostat` que hereda de una interfaz `baseDevice`. La interfaz `baseDevice` tiene un componente, que a su vez hereda de otra interfaz:

:::image type="content" source="media/concepts-modeling-guide/inheritance-components.png" alt-text="Diagrama que muestra un modelo que utiliza los componentes y la herencia." border="false":::

En el fragmento de código siguiente se muestra un modelo DTML que usa las palabras clave `extends` y `component` para definir la relación de herencia y el uso de componentes que se muestra en el diagrama anterior:

```json
[
  {
    "@context": "dtmi:dtdl:context;2",
    "@id": "dtmi:com:example:Thermostat;1",
    "@type": "Interface",
    "contents": [
      {
        "@type": "Telemetry",
        "name": "temperature",
        "schema": "double",
        "unit": "degreeCelsius"
      },
      {
        "@type": "Property",
        "name": "targetTemperature",
        "schema": "double",
        "unit": "degreeCelsius",
        "writable": true
      }
    ],
    "extends": [
      "dtmi:com:example:baseDevice;1"
    ]
  },
  {
    "@context": "dtmi:dtdl:context;2",
    "@id": "dtmi:com:example:baseDevice;1",
    "@type": "Interface",
    "contents": [
      {
        "@type": "Property",
        "name": "SerialNumber",
        "schema": "double",
        "writable": false
      },
      {
        "@type" : "Component",
        "schema": "dtmi:com:example:baseComponent;1",
        "name": "baseComponent"
      }
    ]
  }
]
```

## <a name="data-types"></a>Tipos de datos

Use tipos de datos para definir la telemetría, las propiedades y los parámetros de comandos. Los tipos de datos pueden ser primitivos o complejos. Los tipos de datos complejos usan tipos primitivos u otros tipos complejos. La profundidad máxima de los tipos complejos es de cinco niveles.

### <a name="primitive-types"></a>Tipos primitivos

En la tabla siguiente se muestra el conjunto de tipos primitivos que puede utilizar:

| Tipo primitivo | Descripción |
| --- | --- |
| `boolean` | Un valor booleano |
| `date` | Una fecha completa según se define en la [sección 5.6 de RFC 3339](https://tools.ietf.org/html/rfc3339#section-5.6) |
| `dateTime` | Una fecha y hora según se define en [RFC 3339](https://tools.ietf.org/html/rfc3339) |
| `double` | Un punto flotante de 8 bytes de IEEE |
| `duration` | Una duración en formato ISO 8601 |
| `float` | Un punto flotante de 4 bytes de IEEE |
| `integer` | Un entero de 4 bytes con signo |
| `long` | Un entero de 8 bytes con signo |
| `string` | Una cadena UTF8 |
| `time` | Una hora completa según se define en la [sección 5.6 de RFC 3339](https://tools.ietf.org/html/rfc3339#section-5.6) |

En el fragmento de código siguiente se muestra una definición de telemetría de ejemplo que usa el tipo `double` en el campo `schema`:

```json
{
  "@type": "Telemetry",
  "name": "temperature",
  "displayName": "Temperature",
  "schema": "double"
}
```

### <a name="complex-datatypes"></a>Tipos de datos complejos

Los tipos de datos complejos son una *matriz*, una *enumeración*, una *asignación*, un *objeto* o uno de los tipos geoespaciales.

#### <a name="arrays"></a>Matrices

Una matriz es un tipo de datos indizable en el que todos los elementos son del mismo tipo. El tipo de elemento puede ser un tipo primitivo o complejo.

En el fragmento de código siguiente se muestra una definición de telemetría de ejemplo que usa el tipo `Array` en el campo `schema`. Los elementos de la matriz son booleanos:

```json
{
  "@type": "Telemetry",
  "name": "ledState",
  "schema": {
    "@type": "Array",
    "elementSchema": "boolean"
  }
}
```

#### <a name="enumerations"></a>Enumeraciones

Una enumeración describe un tipo con un conjunto de etiquetas con nombre que se asignan a valores. Los valores pueden ser números enteros o cadenas, pero las etiquetas siempre son cadenas.

En el fragmento de código siguiente se muestra una definición de telemetría de ejemplo que usa el tipo `Enum` en el campo `schema`. Los valores de la enumeración son enteros:

```json
{
  "@type": "Telemetry",
  "name": "state",
  "schema": {
    "@type": "Enum",
    "valueSchema": "integer",
    "enumValues": [
      {
        "name": "offline",
        "displayName": "Offline",
        "enumValue": 1
      },
      {
        "name": "online",
        "displayName": "Online",
        "enumValue": 2
      }
    ]
  }
}
```

#### <a name="maps"></a>Mapas

Una asignación es un tipo con pares clave-valor donde todos los valores tienen el mismo tipo. La clave de una asignación debe ser una cadena. Los valores de una asignación pueden ser de cualquier tipo, incluido otro tipo complejo.

En el fragmento de código siguiente se muestra una definición de propiedad de ejemplo que usa el tipo `Map` en el campo `schema`. Los valores de la asignación son cadenas:

```json
{
  "@type": "Property",
  "name": "modules",
  "writable": true,
  "schema": {
    "@type": "Map",
    "mapKey": {
      "name": "moduleName",
      "schema": "string"
    },
    "mapValue": {
      "name": "moduleState",
      "schema": "string"
    }
  }
}
```

### <a name="objects"></a>Objetos

Un objeto se compone de campos con nombre. Los tipos de los campos de un mapa de objetos pueden ser tipos primitivos o complejos.

En el fragmento de código siguiente se muestra una definición de telemetría de ejemplo que usa el tipo `Object` en el campo `schema`. Los campos del objeto son los tipos `dateTime`, `duration` y `string`:

```json
{
  "@type": "Telemetry",
  "name": "monitor",
  "schema": {
    "@type": "Object",
    "fields": [
      {
        "name": "start",
        "schema": "dateTime"
      },
      {
        "name": "interval",
        "schema": "duration"
      },
      {
        "name": "status",
        "schema": "string"
      }
    ]
  }
}
```

#### <a name="geospatial-types"></a>Tipos geoespaciales

DTDL proporciona un conjunto de tipos geoespaciales, basado en [GeoJSON](https://geojson.org/) para el modelado de estructuras de datos geográficos: `point`, `multiPoint`, `lineString`, `multiLineString`, `polygon` y `multiPolygon`. Estos tipos son estructuras anidadas predefinidas de matrices, objetos y enumeraciones.

En el fragmento de código siguiente se muestra una definición de telemetría de ejemplo que usa el tipo `point` en el campo `schema`:

```json
{
  "@type": "Telemetry",
  "name": "location",
  "schema": "point"
}
```

Dado que los tipos geoespaciales se basan en una matriz, no se pueden usar actualmente en definiciones de propiedad.

## <a name="semantic-types"></a>Tipos semánticos

El tipo de datos de una definición de propiedad o de telemetría especifica el formato de los datos que un dispositivo intercambia con un servicio. El tipo semántico proporciona información sobre la telemetría y las propiedades que una aplicación puede usar para determinar cómo procesar o mostrar un valor. Cada tipo semántico tiene una o más unidades asociadas. Por ejemplo, Celsius y Fahrenheit son unidades para el tipo semántico de temperatura. Los paneles y el análisis de IoT Central pueden usar la información de tipos semánticos para determinar cómo trazar los datos de telemetría o de propiedad y las unidades de visualización. Para obtener información sobre cómo usar el analizador de modelos para leer los tipos semánticos, consulte [Comprender el analizador de modelos de Digital Twins](concepts-model-parser.md).

En el fragmento de código siguiente se muestra una definición de telemetría de ejemplo que incluye información de tipo semántico. El tipo semántico `Temperature` se agrega a la `@type` matriz y en el valor `unit`, `degreeCelsius` es una de las unidades válidas para el tipo semántico:

```json
{
  "@type": [
    "Telemetry",
    "Temperature"
  ],
  "name": "temperature",
  "schema": "double",
  "unit": "degreeCelsius"
}
```

## <a name="localization"></a>Localización

Las aplicaciones, como IoT Central, utilizan la información del modelo para crear dinámicamente una interfaz de usuario en torno a los datos que se intercambian con un dispositivo IoT Plug and Play. Por ejemplo, los mosaicos de un panel pueden mostrar nombres y descripciones de telemetría, propiedades y comandos.

Los campos opcionales `description` y `displayName` del modelo contienen cadenas que se van a usar en una interfaz de usuario. Estos campos pueden contener cadenas localizadas que una aplicación puede usar para representar una interfaz de usuario localizada.

En el fragmento de código siguiente se muestra una definición de telemetría de temperatura de ejemplo que incluye cadenas localizadas:

```json
{
  "@type": [
    "Telemetry",
    "Temperature"
  ],
  "description": {
    "en": "Temperature in degrees Celsius.",
    "it": "Temperatura in gradi Celsius."
  },
  "displayName": {
    "en": "Temperature",
    "it": "Temperatura"
  },
  "name": "temperature",
  "schema": "double",
  "unit": "degreeCelsius"
}
```

Agregar cadenas localizadas es opcional. El siguiente ejemplo solo tiene un único idioma predeterminado:

```json
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
}
```

## <a name="lifecycle-and-tools"></a>Ciclo de vida y herramientas

Las cuatro fases del ciclo de vida de un modelo de dispositivo son la *creación*, la *publicación*, el *uso* y el *control de versiones*:

### <a name="author"></a>Autor

Los modelos de dispositivo DTML son documentos JSON que se pueden crear en un editor de texto. Sin embargo, en IoT Central puede usar el entorno GUI de plantillas de dispositivo para crear un modelo de DTML. En IoT Central puede:

- Cree interfaces que definan propiedades, telemetría y comandos.
- Usar componentes para ensamblar varias interfaces juntas.
- Definir las relaciones de herencia entre las interfaces.
- Importar y exportar archivos de modelo DTML.

Para más información, consulte [Define a new IoT device type in your Azure IoT Central application](../iot-central/core/howto-set-up-template.md) (Definición de un nuevo tipo de dispositivo IoT en la aplicación Azure IoT Central).

Hay extensiones de creación de DTDL para VS Code y Visual Studio 2019.

Para instalar la extensión de DTDL para VS Code, vaya al [editor de DTDL para Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.vscode-dtdl). También puede buscar **DTDL** en la vista de **Extensiones** de VS Code.

Cuando haya instalado la extensión, úsela para ayudarlo a crear archivos de modelo de DTDL en VS Code:

- La extensión proporciona validación de la sintaxis en los archivos del modelo DTDL y resalta los errores, tal y como se muestra en la siguiente captura de pantalla:

    :::image type="content" source="media/concepts-modeling-guide/model-validation.png" alt-text="Validación de modelo en VS Code":::

- Use IntelliSense y Autocompletar al editar modelos de DTDL:

    :::image type="content" source="media/concepts-modeling-guide/model-intellisense.png" alt-text="Uso de IntelliSense para los modelos de DTDL en VS Code":::

- Cree una interfaz de DTDL. El comando de **DTDL: Crear interfaz** crea un archivo JSON con una nueva interfaz. La interfaz incluye telemetría de ejemplo, propiedades y definiciones de comandos.

Para instalar la extensión de DTDL para Visual Studio 2019, vaya a [Compatibilidad con lenguaje DTDL para VS 2019](https://marketplace.visualstudio.com/items?itemName=vsc-iot.vs16dtdllanguagesupport). También puede buscar **DTDL** en **Administrar extensiones** en Visual Studio.

Cuando haya instalado la extensión, úsela para ayudarlo a crear archivos de modelo de DTDL en Visual Studio:

- La extensión proporciona validación de la sintaxis en los archivos del modelo DTDL y resalta los errores, tal y como se muestra en la siguiente captura de pantalla:

    :::image type="content" source="media/concepts-modeling-guide/model-validation-2.png" alt-text="Validación de modelos en Visual Studio":::

- Use IntelliSense y Autocompletar al editar modelos de DTDL:

    :::image type="content" source="media/concepts-modeling-guide/model-intellisense-2.png" alt-text="Usar IntelliSense para los modelos de DTDL en Visual Studio":::

### <a name="publish"></a>Publicar

Para que los modelos de DTML se puedan compartir y detectar, debe publicarlos en un repositorio de modelos de dispositivo.

Antes de publicar un modelo en el repositorio público, puede usar las herramientas de `dmr-client` para validar el modelo.

Para más información, consulte [Repositorio de modelos de dispositivo](concepts-model-repository.md).

### <a name="use"></a>Usar

Las aplicaciones, como IoT Central, usan modelos de dispositivo. En IoT Central, un modelo forma parte de la plantilla de dispositivo que describe las funciones del dispositivo. IoT Central usa la plantilla de dispositivo para crear dinámicamente una interfaz de usuario para el dispositivo, incluidos los paneles y el análisis.

Una solución personalizada puede usar el [analizador del modelos de Digital Twins](concepts-model-parser.md) para comprender las capacidades de un dispositivo que implementa el modelo. Para más información, consulte [Uso de los modelos IoT Plug and Play en una solución de IoT](concepts-model-discovery.md).

### <a name="version"></a>Versión

Para asegurarse de que los dispositivos y las soluciones de servidor que usan modelos continúan funcionando, los modelos publicados son inmutables.

DTMI incluye un número de versión que puede usar para crear varias versiones de un modelo. Los dispositivos y las soluciones del lado servidor pueden usar la versión específica para la que se diseñaron.

IoT Central implementa más reglas de control de versiones para los modelos de dispositivo. Si tiene una versión de una plantilla de dispositivo y su modelo en IoT Central, puede migrar los dispositivos de versiones anteriores a versiones posteriores. Sin embargo, los dispositivos migrados no pueden usar nuevas capacidades sin una actualización de firmware. Para obtener más información, consulte [Edición de una plantilla de dispositivo](../iot-central/core/howto-edit-device-template.md).

## <a name="limits-and-constraints"></a>Límites y restricciones

En la lista siguiente se resumen algunas restricciones y límites clave en los modelos:

- Actualmente, la profundidad máxima para matrices, asignaciones y objetos es de cinco niveles de profundidad.
- No se pueden usar matrices en definiciones de propiedad.
- Se pueden ampliar las interfaces a una profundidad de 10 niveles.
- Una interfaz puede ampliarse como máximo a otras dos interfaces.
- Un componente no puede contener otro componente.

## <a name="next-steps"></a>Pasos siguientes

Ahora que ha aprendido acerca del modelado de dispositivos, estos son algunos recursos adicionales:

- [Lenguaje de definición de Digital Twins v2 (DTDL)](https://github.com/Azure/opendigitaltwins-dtdl)
- [Repositorios de modelos](./concepts-model-repository.md)
