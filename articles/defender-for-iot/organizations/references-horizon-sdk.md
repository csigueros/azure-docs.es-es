---
title: SDL de Horizon
description: El SDK de Horizon permite que los desarrolladores de Azure Defender para IoT diseñen complementos de disección que descodifiquen el tráfico de red para que se pueda procesar mediante los programas de análisis de red automatizados de Defender para IoT.
ms.date: 1/13/2021
ms.topic: article
ms.openlocfilehash: a6b9f69daeb39b79ccdabea273efe825f6a9d1ff
ms.sourcegitcommit: 98e126b0948e6971bd1d0ace1b31c3a4d6e71703
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 07/26/2021
ms.locfileid: "114673994"
---
# <a name="horizon-proprietary-protocol-dissector"></a>Disección del protocolo propietario de Horizon

Horizon es un entorno de desarrollo abierto (ODE) que se usa para proteger los dispositivos IoT e ICS que ejecutan protocolos propietarios.

Este entorno ofrece las siguientes soluciones para clientes y partners tecnológicos:

- Compatibilidad total e ilimitada con los protocolos propietarios y personalizados comunes, y con aquellos protocolos que se desvían de cualquier estándar. 

- Un nuevo nivel de flexibilidad y ámbito para el desarrollo de DPI.

- Una herramienta que amplía exponencialmente la visibilidad y el control de OT sin necesidad de actualizar las versiones de la plataforma de Defender para IoT.

- La seguridad de permitir el desarrollo propietario sin divulgar información confidencial.

El SDK de Horizon permite que los desarrolladores de Azure Defender para IoT diseñen complementos de disección que descodifiquen el tráfico de red para que se pueda procesar mediante los programas de análisis de red automatizados de Defender para IoT.

Las herramientas de disección de protocolos se desarrollan como complementos externos y se integran con una amplia gama de servicios de Defender para IoT. Por ejemplo, los servicios que proporcionan funcionalidades de supervisión, alertas e informes.

## <a name="secure-development-environment"></a>Entorno de desarrollo seguro 

El ODE de facilita el desarrollo de protocolos personalizados o propietarios que no se pueden compartir fuera de una organización. Por ejemplo, debido a la normativa legal o a las directivas de la empresa.

Puede desarrollar complementos de disección sin: 

- revelar información propietaria sobre cómo se definen sus protocolos.

- compartir ningún archivo PCAP confidencial.

- infringir la normativa de cumplimiento.

Póngase en contacto con <ms-horizon-support@microsoft.com> para obtener información sobre el desarrollo de complementos de protocolo.
## <a name="customization-and-localization"></a>Personalización y localización  

El SDK admite varias opciones de personalización, entre las que se incluyen las siguientes:

  - Texto para los códigos de función. 

  - Texto de localización completa para las alertas, eventos y parámetros de protocolos. Para más información, consulte [Creación de archivos de asignación (JSON)](#create-mapping-files-json).

  :::image type="content" source="media/references-horizon-sdk/localization.png" alt-text="Visualización de alertas completamente localizadas.":::

## <a name="horizon-architecture"></a>Arquitectura de Horizon

El modelo arquitectónico incluye tres capas de producto.

:::image type="content" source="media/references-horizon-sdk/architecture.png" alt-text="https://lh6.googleusercontent.com/YFePqJv_6jbI_oy3lCQv-hHB1Qly9a3QQ05uMnI8UdTwhOuxpNAedj_55wseYEQQG2lue8egZS-mlnQZPWfFU1dF4wzGQSJIlUqeXEHg9CG4M7ASCZroKgbghv-OaNoxr3AIZtIh":::

## <a name="defender-for-iot-platform-layer"></a>Capa de plataforma de Defender para IoT

Permite la integración inmediata y la supervisión en tiempo real de los complementos de disección personalizados en la plataforma de Defender para IoT, sin necesidad de actualizar la versión de dicha plataforma.

## <a name="defender-for-iot-services-layer"></a>Capa de servicios de Defender para IoT

Cada servicio está diseñado como una canalización, desacoplada de un protocolo específico, lo que permite un desarrollo más eficaz e independiente.

Cada servicio está diseñado como una canalización, desacoplada de un protocolo específico. Los servicios escuchan el tráfico en la canalización. Interactúan con los datos del complemento y con el tráfico capturado por los sensores para indexar los protocolos implementados y analizar la carga útil del tráfico; además, facilitan un desarrollo más eficaz e independiente.

## <a name="custom-dissector-layer"></a>Nivel de disección personalizado 

Permite la creación de complementos mediante el SDK propietario de Defender para IoT (incluida la implementación de C++ y la configuración JSON) para: 

- Definir cómo se identifica el protocolo

- Definir cómo se asignan los campos que quiere extraer del tráfico y extraerlos 

- Definir cómo se integra con los servicios de Defender para IoT

  :::image type="content" source="media/references-horizon-sdk/layers.png" alt-text="Las capas integradas.":::

Defender para IoT ofrece elementos de disección básicos para protocolos comunes. Puede crear sus disecciones basado en estos protocolos.

## <a name="before-you-begin"></a>Antes de empezar

## <a name="what-this-sdk-contains"></a>Contenido de este SDK 

Este kit contiene los archivos de encabezado necesarios para desarrollo. El proceso de desarrollo requiere de pasos básicos y de otros avanzados opcionales, que se describen en este SDK.

Póngase en contacto con <ms-horizon-support@microsoft.com> para obtener información sobre cómo recibir los archivos de encabezado y otros recursos.

## <a name="about-the-environment-and-setup"></a>Acerca del entorno y la configuración 

### <a name="requirements"></a>Requisitos

- El entorno de desarrollo preferido es Linux. Si lleva a cabo el desarrollo en un entorno de Windows, considere la posibilidad de usar una máquina virtual con un sistema Linux.

- Para el proceso de compilación, use GCC 7.4.0 o posterior. Use cualquier clase estándar de stdlib que sea compatible con C++ 17.

- Defender para IoT, versión 3.0 y posteriores.

### <a name="process"></a>Proceso

1. [Descargue](https://www.eclipse.org/) el IDE de Eclipse para desarrolladores de C/ C++. Puede usar otro IDE si lo prefiere. Este documento le guía por la configuración mediante el IDE de Eclipse.

1. Después de iniciar el IDE de Eclipse y configurar el área de trabajo (en la que se almacenarán los proyectos), presione **Ctrl + N** y cree un proyecto de C++.

1. En la siguiente pantalla, establezca el nombre como el protocolo que quiere desarrollar y seleccione el tipo de proyecto como `Shared Library` y `AND Linux GCC`.

1. Edite las propiedades del proyecto en **C/C++ Build** > **Settings** > **Tool Settings** > **GCC C++ Compiler** > **Miscellaneous** > **Position Independent Code** (Compilación C/C++ > Configuración > Configuración de herramienta > Compilador GCC de C++ > Varios > Marque la opción Código independiente de posición).

1. Pegue los códigos de ejemplo que recibió con el SDK y compílelos.

1. Agregue los artefactos (biblioteca, config.json y metadatos) a un archivo tar.gz y cambie la extensión de archivo a \<XXX>.hdp, donde \<XXX> sea el nombre del complemento.

### <a name="research"></a>Investigación 

Antes de empezar, compruebe que haya hecho lo siguiente:

- Leyó la especificación del protocolo, si está disponible.

- Identificó qué campos de protocolo tiene previsto extraer.

- Ha planeado los objetivos de la asignación.

## <a name="about-plugin-files"></a>Acerca de los archivos del complemento 

Durante el proceso de desarrollo se definen tres archivos.

### <a name="json-configuration-file-required"></a>Archivo de configuración JSON (obligatorio) 

Este archivo debe definir el id. de disección y las declaraciones, dependencias, requisitos de integración, parámetros de validación y definiciones de asignación para traducir los valores a nombres, y los números a texto. Para obtener más información, consulte los siguientes vínculos:

- [Preparación del archivo de configuración (JSON)](#prepare-the-configuration-file-json)

- [Preparación de las validaciones del código de implementación](#prepare-implementation-code-validations)

- [Extracción de los metadatos del dispositivo](#extract-device-metadata)

- [Conexión a un servicio de indexación (línea de base)](#connect-to-an-indexing-service-baseline)

### <a name="implementation-code-c-required"></a>Código de implementación: C++ (obligatorio)

El código de implementación (CPP) analiza el tráfico sin procesar y lo asigna a valores como servicios, clases y códigos de función. Extrae los campos de capa y los asigna a sus nombres de índice a partir de los archivos de configuración JSON. Los campos que se van a extraer del CPP se definen en el archivo de configuración. Para obtener más información, consulte [Preparación del código de implementación (C++)](#prepare-the-implementation-code-c).

### <a name="mapping-files-optional"></a>Archivos de asignación (opcional)

Puede personalizar el texto de salida del complemento para cumplir las necesidades de su entorno empresarial.

:::image type="content" source="media/references-horizon-sdk/localization.png" alt-text="migration":::

Puede definir y actualizar los archivos de asignación para actualizar el texto sin cambiar el código. Cada archivo puede asignar uno o varios campos:

  - Asignación de valores de campo a nombres; por ejemplo, 1:Restablecer, 2:Iniciar, 3:Detener.

  - Asignación de texto para admitir varios idiomas.

Para más información, consulte [Creación de archivos de asignación (JSON)](#create-mapping-files-json).

## <a name="create-a-dissector-plugin-overview"></a>Creación de un complemento de disección (introducción)

1. Revise la sección [Acerca del entorno y la configuración](#about-the-environment-and-setup).

2.  [Prepare el código de implementación ( C++)](#prepare-the-implementation-code-c). Copie el archivo **template.json** y edítelo para que cumpla sus necesidades. No cambie las claves. 

3. [Prepare el archivo de configuración (JSON)](#prepare-the-configuration-file-json). Copie el archivo **template.cpp** e implemente un método de invalidación. Para obtener información más detallada, consulte [horizon::protocol::BaseParser](#horizonprotocolbaseparser).

4. [Prepare las validaciones del código de implementación](#prepare-implementation-code-validations).

## <a name="prepare-the-implementation-code-c"></a>Preparación del código de implementación ( C++)

El archivo CPP es un analizador responsable de lo siguiente:

- Validar el encabezado y la carga útil del paquete (por ejemplo, la longitud del encabezado o la estructura de la carga).

- Extraer datos del encabezado y la carga útil en campos definidos.

- Implementar la extracción de los campos configurados mediante el archivo JSON.

### <a name="what-to-do"></a>Qué hacer

Copie el archivo **.cpp** de plantilla e implemente un método de invalidación. Para obtener más información, consulte [horizon::protocol::BaseParser](#horizonprotocolbaseparser).

### <a name="basic-c-template-sample"></a>Ejemplo de plantilla de C++ básica 

En esta sección se proporciona una plantilla de protocolo básica, con funciones estándar para un protocolo de Defender para IoT de ejemplo.

```C++
#include “plugin/plugin.h”
namespace {
 class CyberxHorizonSDK: public horizon::protocol::BaseParser
  public:
   std::vector<uint64_t> processDissectAs(const std::map<std::string,
                                                         std::vector<std::string>> &filters) const override {
     return std::vector<uint64_t>();
   }
   horizon::protocol::ParserResult processLayer(horizon::protocol::management::IProcessingUtils &ctx,
                                                horizon::general::IDataBuffer &data) override {
     return horizon::protocol::ParserResult();
   }
 };
}

extern "C" {
  std::shared_ptr<horizon::protocol::BaseParser> create_parser() {
    return std::make_shared<CyberxHorizonSDK>();
  }
}

```

### <a name="basic-c-template-description"></a>Descripción de la plantilla de C++ básica  

En esta sección se proporciona una plantilla de protocolo básica, con una descripción de las funciones estándar para un protocolo de Defender para IoT de ejemplo. 

### <a name="include-pluginpluginh"></a>#include "plugin/plugin.h"

La definición que usa el complemento. El archivo de encabezado contiene todo lo necesario para completar el desarrollo.

### <a name="horizonprotocolbaseparser"></a>horizon::protocol::BaseParser

La interfaz de comunicación entre la infraestructura de Horizon y la capa de complemento. Para obtener más información, consulte la introducción sobre las capas en [Arquitectura de Horizon](#horizon-architecture).

ProcessLayer es el método que se usa para procesar los datos.

- El primer parámetro del código de la función es la utilidad de procesamiento que se usa para recuperar los datos procesados anteriormente y para crear nuevos campos y capas.

- El segundo parámetro del código de la función son los datos actuales que se pasaron desde el analizador anterior.

```C++
horizon::protocol::ParserResult processLayer(horizon::protocol::management::IProcessingUtils &ctx,
                                               horizon::general::IDataBuffer &data) override {

```

### <a name="create_parser"></a>create_parser

Se usa para crear la instancia del analizador.

:::image type="content" source="media/references-horizon-sdk/code.png" alt-text="https://lh5.googleusercontent.com/bRNtyLpBA3LvDXttSPbxdBK7sHiHXzGXGhLiX3hJ7zCuFhbVsbBhgJlKI6Fd_yniueQqWbClg5EojDwEZSZ219X1Z7osoa849iE9X8enHnUb5to5dzOx2bQ612XOpWh5xqg0c4vR":::

## <a name="protocol-function-code-sample"></a>Ejemplo de código de función de protocolo 

En esta sección se proporciona un ejemplo de cómo se extraen el número de código (2 bytes) y la longitud del mensaje (4 bytes).

Esto se lleva a cabo según el Endian proporcionado en el archivo de configuración JSON, lo que significa que si el protocolo es de *little endian* y el sensor se ejecuta en una máquina con little endian, se convertirá.

También se crea una capa para almacenar los datos. Use el elemento *fieldsManager* de las utilidades de procesamiento para crear nuevos campos. Un campo puede tener solo uno de los siguientes tipos: *STRING*, *NUMBER*, *RAW DATA*, *ARRAY* (de un tipo específico) o *COMPLEX*. Esta capa puede contener un número, un valor sin formato o una cadena con id.

En el ejemplo a continuación, se extraen los dos campos siguientes:

- `function_code_number`

- `headerLength`

Se crea una nueva capa y se copia en ella el campo extraído.

En el ejemplo siguiente se describe una función específica, que es la lógica principal que se implementó para el procesamiento del complemento.

```C++
namespace {
 class CyberxHorizonProtocol: public horizon::protocol::BaseParser {
  public:
   
   horizon::protocol::ParserResult processLayer(horizon::protocol::management::IProcessingUtils &ctx,
                                                horizon::general::IDataBuffer &data) override {
     uint16_t codeNumber = data.readUInt16();
     uint32_t headerLength = data.readUInt32();
 
     auto &layer = ctx.createNewLayer();
 
     ctx.getFieldsManager().create(layer,HORIZON_FIELD("code_number"),codeNumber;    
     ctx.getFieldsManager().create(layer,HORIZON_FIELD("header_length"),headerLength);     
     return horizon::protocol::SuccessResult();
   }
 

```

### <a name="related-json-field"></a>Campo JSON relacionado 

:::image type="content" source="media/references-horizon-sdk/json.png" alt-text="El campo JSON relacionado.":::

## <a name="prepare-the-configuration-file-json"></a>Preparación del archivo de configuración (JSON)

El SDK de Horizon usa notación de objetos JavaScript (JSON) estándar, un formato ligero para almacenar y transportar datos y no requiere lenguajes de scripting propietarios.

En esta sección se describen las declaraciones de configuración JSON mínimas, la estructura relacionada y se proporciona un archivo de configuración de ejemplo que define un protocolo. Este protocolo se integra automáticamente con el servicio de detección de dispositivos.

## <a name="file-structure"></a>Estructura de archivos

En el ejemplo siguiente se describe la estructura del archivo.

:::image type="content" source="media/references-horizon-sdk/structure.png" alt-text="El ejemplo de la estructura de archivo.":::

### <a name="what-to-do"></a>Qué hacer

Copie la plantilla del archivo `config.json` y edítela para que cumpla sus necesidades. No cambie la clave. Las claves están marcadas con rojo en el [archivo de configuración JSON de ejemplo](#sample-json-configuration-file). 

### <a name="file-naming-requirements"></a>Requisitos de nomenclatura del archivo 

El archivo de configuración JSON debe guardarse como `config.json`.

### <a name="json-configuration-file-fields"></a>Campos del archivo de configuración JSON

En esta sección se describen los campos de configuración JSON que se van a definir. No cambie las *etiquetas* de los campos.

### <a name="basic-parameters"></a>Parámetros básicos

En esta sección se describen los parámetros básicos.

| Etiqueta de parámetro | Descripción | Tipo |
|--|--|--|
| **ID** | El nombre del protocolo Elimine el valor predeterminado y agregue el nombre del protocolo tal como aparece. | String |
| **endianess** | Define cómo se codifican los datos de varios bytes. Use solo el término "little" o "big". Se toma de la especificación de protocolo o la grabación del tráfico. | String |
| **sanity_failure_codes** | Estos son los códigos devueltos por el analizador cuando hay un conflicto de estado con respecto a la identidad del código. Consulte la validación del número mágico en la sección de C++. | String |
| **malformed_codes** | Se trata de códigos que se identificaron correctamente, pero se detectó un error. Por ejemplo, si la longitud del campo es demasiado corta o larga, o si un valor no es válido. | String |
| **dissect_as** | Matriz que define la ubicación a la que debe llegar el tráfico del protocolo específico. | TCP o UDP, puerto, etc. |
| **fields** | Declaración de los campos que se extraerán del tráfico. Cada campo tiene su propio id. (nombre) y tipo (numérico, cadena, valor sin formato, matriz, complejo). Por ejemplo, el campo [function](https://docs.google.com/document/d/14nm8cyoGiaE0ODOYQd_xjULxVz9U_bjfPKkcDhOFr5Q/edit#bookmark=id.6s1zcxa9184k) que se extrae en el archivo del analizador de implementación. Los campos escritos en el archivo de configuración son los únicos que se pueden agregar a la capa. |  |

### <a name="other-advanced-fields"></a>Otros campos avanzados 

En esta sección se describen otros campos.

| Etiqueta de parámetro | Descripción |
|-----------------|--------|
| **allow_lists** | Puede indexar los valores de protocolo y mostrarlos en los informes de minería de datos. Estos informes reflejan la línea de base de la red. :::image type="content" source="media/references-horizon-sdk/data-mining.png" alt-text="Ejemplo de la vista de minería de datos."::: <br /> Para obtener información más detallada, consulte [Conexión a un servicio de indexación (línea de base)](#connect-to-an-indexing-service-baseline). |
| **firmware** | Puede extraer información de firmware, definir valores de índice y desencadenar alertas de firmware para el protocolo del complemento. Para obtener información más detallada, consulte [Extracción de datos de firmware](#extract-firmware-data). |
| **value_mapping** | Puede personalizar el texto de salida del complemento para cumplir las necesidades de su entorno empresarial al definir y actualizar archivos de asignación. Por ejemplo, asigne los archivos de idioma. Los cambios se pueden implementar fácilmente en el texto sin cambiar ni afectar al código. Para obtener información más detallada, consulte [Creación de archivos de asignación (JSON)](#create-mapping-files-json). |

## <a name="sample-json-configuration-file"></a>Archivo de configuración JSON de ejemplo

```json
{
  "id":"CyberX Horizon Protocol",
  "endianess": "big",
  "sanity_failure_codes": {
    "wrong magic": 0
  },
  "malformed_codes": {
    "not enough bytes": 0
  },
  "exports_dissect_as": {
  },
  "dissect_as": {
    "UDP": {
      "port": ["12345"]
    }
  },
  "fields": [
{
      "id": "function",
      "type": "numeric"
    },
    {
      "id": "sub_function",
      "type": "numeric"
    },
    {
      "id": "name",
      "type": "string"
    },
    {
      "id": "model",
      "type": "string"
    },
    {
      "id": "version",
      "type": "numeric"
    }
  ]
}


```

## <a name="prepare-implementation-code-validations"></a>Preparación de las validaciones del código de implementación

En esta sección se describen las funciones de validación del código de C++ de la implementación y se proporciona código de ejemplo. Hay disponibles dos capas de validación:

- Comprobación.

- Código con formato incorrecto.

No es necesario crear código de validación para compilar un complemento que funciona. Si no prepara el código de validación, puede revisar los informes de minería de datos del sensor como indicador de que el procesamiento se realiza correctamente.

Los valores de los campos se pueden asignar al texto de los archivos de asignación y se pueden actualizar sin problemas y sin afectar al procesamiento.

## <a name="sanity-code-validations"></a>Validaciones de código para comprobación 

Esto valida que el paquete transmitido coincida con los parámetros de validación del protocolo, lo que le ayuda a identificar al protocolo dentro del tráfico.

Por ejemplo, use los primeros 8 bytes como *número mágico*. Si se produce un error en la comprobación, se devuelve una respuesta de error de comprobación.

Por ejemplo:

```C++
  horizon::protocol::ParserResult 
processLayer(horizon::protocol::management::IProcessingUtils &ctx,
                                               horizon::general::IDataBuffer 
&data) override {

    uint64_t magic = data.readUInt64();

    if (magic != 0xBEEFFEEB) {

      return horizon::protocol::SanityFailureResult(0);

    }
```

Si se han implementado otros complementos pertinentes, el paquete se validará con ellos.

## <a name="malformed-code-validations"></a>Validaciones de código con formato incorrecto 

Se utilizan validaciones de formato incorrecto después de que el protocolo se haya validado de forma positiva.

Si se produce un error al procesar los paquetes basados en el protocolo, se devuelve una respuesta de error.

:::image type="content" source="media/references-horizon-sdk/failure.png" alt-text="código con formato incorrecto":::

## <a name="c-sample-with-validations"></a>Ejemplo de C++ con validaciones

Según la función, el proceso se lleva a cabo tal como se muestra en el ejemplo siguiente.

### <a name="function-20"></a>Función 20 

- Se procesa como firmware.

- Los campos se leen según la función.

- Los campos se agregan a la capa.

### <a name="function-10"></a>Función 10 

- La función contiene otra subfunción, que es una operación más específica.

- Esta subfunción se lee y se agrega a la capa.

Una vez hecho esto, el procesamiento ha finalizado. El valor devuelto indica si la capa de disección se procesó correctamente. Si es así, la capa puede usarse.

```C++
#include "plugin/plugin.h"

#define FUNCTION_FIRMWARE_RESPONSE 20

#define FUNCTION_SUBFUNCTION_REQUEST 10

namespace {

class CyberxHorizonSDK: public horizon::protocol::BaseParser {

 public:

  std::vector<uint64_t> processDissectAs(const std::map<std::string,

                                                        std::vector<std::string>> &filters) const override {

    return std::vector<uint64_t>();

  }

  horizon::protocol::ParserResult processLayer(horizon::protocol::management::IProcessingUtils &ctx,

                                               horizon::general::IDataBuffer &data) override {

    uint64_t magic = data.readUInt64();

    if (magic != 0xBEEFFEEB) {

      return horizon::protocol::SanityFailureResult(0);

    }

    uint16_t function = data.readUInt16();

    uint32_t length = data.readUInt32();

    if (length > data.getRemaningData()) {

      return horizon::protocol::MalformedResult(0);

    }

    auto &layer = ctx.createNewLayer();

    ctx.getFieldsManager().create(layer, HORIZON_FIELD("function"), function);

    switch (function) {

      case FUNCTION_FIRMWARE_RESPONSE: {

        uint8_t modelLength = data.readUInt8();

        std::string model = data.readString(modelLength);

        uint16_t firmwareVersion = data.readUInt16();

        uint8_t nameLength = data.readUInt8();

        std::string name = data.readString(nameLength);

        ctx.getFieldsManager().create(layer, HORIZON_FIELD("model"), model);

        ctx.getFieldsManager().create(layer, HORIZON_FIELD("version"), firmwareVersion);

        ctx.getFieldsManager().create(layer, HORIZON_FIELD("name"), name);

      }

      break;

      case FUNCTION_SUBFUNCTION_REQUEST: {

       uint8_t subFunction = data.readUInt8();

       ctx.getFieldsManager().create(layer, HORIZON_FIELD("sub_function"), subFunction);

      }

      break;

    }

    return horizon::protocol::SuccessResult();

  }

};

}

extern "C" {

 std::shared_ptr<horizon::protocol::BaseParser> create_parser() {

   return std::make_shared<CyberxHorizonSDK>();

 }

}
```

## <a name="extract-device-metadata"></a>Extracción de los metadatos del dispositivo

Puede extraer los siguientes metadatos de los recursos:

  - `Is_distributed_control_system`: indica si el protocolo forma parte del sistema de control distribuido. (Ejemplo: el protocolo SCADA debe ser false).

  - `Has_protocol_address`: indica si hay una dirección de protocolo; la dirección específica del protocolo actual; por ejemplo, el id. de unidad MODBUS.

  - `Is_scada_protocol`: indica si el protocolo es específico de las redes OT.

  - `Is_router_potential`: indica si el protocolo lo usan principalmente los enrutadores. Por ejemplo, LLDP, CDP o STP.

Para lograr esto, el archivo de configuración JSON debe actualizarse con la propiedad metadata.

## <a name="json-sample-with-metadata"></a>Ejemplo de JSON con la propiedad metadata 

```json

{
 "id":"CyberX Horizon Protocol",
 "endianess": "big",
 "metadata": {
   "is_distributed_control_system": false,
   "has_protocol_address": false,
   "is_scada_protocol": true,
   "is_router_potenial": false
 },
 "sanity_failure_codes": {
   "wrong magic": 0
 },
 "malformed_codes": {
   "not enough bytes": 0
 },
 "exports_dissect_as": {
 },
 "dissect_as": {
   "UDP": {
     "port": ["12345"]
   }
 },
 "fields": [
   {
     "id": "function",
     "type": "numeric"
},
   {
     "id": "sub_function",
     "type": "numeric"
   },
   {
     "id": "name",
     "type": "string"
   },
   {
     "id": "model",
     "type": "string"
   },
   {
     "id": "version",
     "type": "numeric"
   }
 ],
}

```

## <a name="extract-programming-code"></a>Extracción del código de programación 

Cuando se produce un evento de programación, puede extraer el contenido del código. El contenido extraído le permite hacer lo siguiente:

- Comparar el contenido del archivo de código en diferentes eventos de programación.

- Desencadenar una alerta sobre programación no autorizada.  

- Desencadenar un evento para recibir el archivo de código de programación.

  :::image type="content" source="media/references-horizon-sdk/change.png" alt-text="Historial de cambios de programación.":::

  :::image type="content" source="media/references-horizon-sdk/view.png" alt-text="Para visualizar la programación, haga clic en el botón.":::

  :::image type="content" source="media/references-horizon-sdk/unauthorized.png" alt-text="Alerta de programación de PLC no autorizada.":::

Para lograr esto, el archivo de configuración JSON debe actualizarse con la propiedad `code_extraction`. 

### <a name="json-configuration-fields"></a>Campos de configuración JSON 

En esta sección se describen los campos de configuración JSON. 

- **method**

  Indica la manera en que se reciben los archivos de eventos de programación. 

  ALL (cada acción de programación hará que se reciban todos los archivos de código, aunque haya archivos sin cambios).

- **file_type**  

  Indica el tipo de contenido del código.  

  TEXT (cada archivo de código contiene información textual).

- **code_data_field**
  
  Indica el campo de implementación que se usará para proporcionar el contenido del código.

  CAMPO.

- **code_name_field**

  Indica el campo de implementación que se usará para proporcionar el nombre del archivo de codificación.

  CAMPO.

- **size_limit**

  Indica el límite de tamaño para el contenido de cada archivo de codificación en BYTES, si un archivo de código supera el límite establecido, se quitará. Si este campo no se especifica, se usa el valor predeterminado, que es 15 000 000; es decir, 15 MB.

  Número.

- **metadata**

  Indica información adicional para un archivo de código.

  Matriz que contiene objetos con dos propiedades:

    - name (cadena): indica que la clave de metadatos de XSense actualmente solo admite la clave de nombre de usuario.
 
    - value (campo): indica el campo de implementación que se usará para proporcionar los metadatos.

## <a name="json-sample-with-programming-code"></a>Ejemplo JSON con código de programación

```json
{
  "id":"CyberXHorizonProtocol",
  "endianess": "big",
  "metadata": {
    "is_distributed_control_system": false,
    "has_protocol_address": false,
    "is_scada_protocol": true,
    "is_router_potenial": false
  },
  "sanity_failure_codes": {
    "wrong magic": 0
  },
  "malformed_codes": {
    "not enough bytes": 0
  },
  "exports_dissect_as": {
  },
  "dissect_as": {
    "UDP": {
      "port": ["12345"]
    }
  },
  "fields": [
    {
      "id": "function",
      "type": "numeric"
    },
    {
      "id": "sub_function",
      "type": "numeric"
    },
    {
      "id": "name",
      "type": "string"
    },
    {
      "id": "model",
      "type": "string"
    },
    {
      "id": "version",
      "type": "numeric"
    },
    {
      "id": "script",
      "type": "string"
    },
    {
      "id": "script_name",
      "type": "string"
    }, 
      "id": "username",
      "type": "string"
    }
  ],
"whitelists": [
    {
      "name": "Functions",
      "alert_title": "New Activity Detected - CyberX Horizon 
                                                Protocol Function",
      "alert_text": "There was an attempt by the source to 
                        invoke a new function on the destination",
       "fields": [
        {
          "name": "Source",
          "value": "IPv4.src"
        },
        {
          "name": "Destination",
          "value": "IPv4.dst"
        },
        {
          "name": "Function",
          "value": "CyberXHorizonProtocol.function"
        }
      ]
    },
"firmware": {
    "alert_text": "Firmware was changed on a network asset. 
                  This may be a planned activity, 
                  for example an authorized maintenance procedure",
    "index_by": [
      {
        "name": "Device",
        "value": "IPv4.src",
        "owner": true
      }
    ],
    "firmware_fields": [,
      {
        "name": "Model",
        "value": "CyberXHorizonProtocol.model",
        "firmware_index": "model"
      },
      {
        "name": "Revision",
        "value": "CyberXHorizonProtocol.version",
        “Firmware_index”: “firmware_version”
      },
      {
        "name": "Name",
        "value": "CyberXHorizonProtocol.name"
      }
    ]
  },
"code_extraction": {
    "method": "ALL",
    "file_type": "TEXT",
    "code_data_field": "script",
    "code_name_field": "script_name",
    "size_limit": 15000000,
    "metadata": [
      {
        "name": "username",
        "value": "username"
      }
    ]
  }
}

```
## <a name="custom-horizon-alerts"></a>Alertas de Horizon personalizadas

El código de función de algunos protocolos puede indicar errores. Por ejemplo, si el protocolo controla un contenedor con una sustancia química específica que siempre debe almacenarse a una temperatura específica. En este caso, puede haber código de función para indicar un error en el termómetro. Por ejemplo, si el código de función es 25, puede desencadenar una alerta en la consola web que indica que hay un problema con el contenedor. En tal caso, puede definir alertas de paquetes profundas.

Agregue el parámetro **alerts** al archivo `config.json` del complemento.

```json
“alerts”: [{
    “id”: 1,
    “message”: “Problem with thermometer at station {IPv4.src}”,
    “title”: “Thermometer problem”,
    “expression”: “{CyberXHorizonProtocol.function} == 25”
}]

```

## <a name="json-configuration-fields"></a>Campos de configuración JSON

En esta sección se describen los campos de configuración JSON. 

| Nombre del campo | Descripción | Valores posibles |
|--|--|--|
| **ID** | Representa un único id. de alerta. Debe ser único en este contexto. | Valor numérico entre 0 y 10000. |
| **message** | Información que se muestra al usuario. Este campo permite usar distintos campos. | Use cualquier campo del protocolo o cualquier protocolo de nivel inferior. |
| **title** | Título de la alerta. |  |
| **expression** | Cuándo quiere que aparezca esta alerta. | Use cualquier campo numérico de los niveles inferiores o del nivel actual.</br></br> Cada campo encapsularse con `{}`, para que el SDK lo detecte como un campo. Los operadores lógicos admitidos son</br> == - Igual que</br> <= - Menor o igual que</br> >= - Mayor o igual que</br> > - Mayor que</br> < - Menor que</br> ~= - No igual que |

## <a name="more-about-expressions"></a>Más acerca de las expresiones

Cada vez que el SDK de Horizon invoca la expresión y se convierte en *true*, se desencadena una alerta en el sensor.

Se pueden incluir varias expresiones en la misma alerta. Por ejemplo,

`{CyberXHorizonProtocol.function} == 25 and {IPv4.src} == 269488144`.

Esta expresión valida el código de función solo cuando el SRC de IPv4 del paquete es 10.10.10.10. Que es una representación sin formato de la dirección IP en representación numérica.

Puede usar los operadores `and` u `or` para conectar expresiones.

## <a name="json-sample-custom-horizon-alerts"></a>Ejemplo de JSON para alertas de Horizon personalizadas

```json
 "id":"CyberX Horizon Protocol",
 "endianess": "big",
 "metadata": {
   "is_distributed_control_system": false,
   "has_protocol_address": false,
   "is_scada_protocol": true,
   "is_router_potenial": false
 },
 "sanity_failure_codes": {
   "wrong magic": 0
 },
 "malformed_codes": {
   "not enough bytes": 0
 },
 "exports_dissect_as": {
 },
 "dissect_as": {
   "UDP": {
     "port": ["12345"]
   }
 },
 …………………………………….
 “alerts”: [{
“id”: 1,
“message”: “Problem with thermometer at station {IPv4.src}”,
“title”: “Thermometer problem”,
“expression”: “{CyberXHorizonProtocol.function} == 25”

```

## <a name="connect-to-an-indexing-service-baseline"></a>Conexión a un servicio de indexación (línea de base)

Puede indexar los valores del protocolo y mostrarlos en los informes de minería de datos.

:::image type="content" source="media/references-horizon-sdk/data-mining.png" alt-text="Vista de la opción de minería de datos.":::

Estos valores se pueden asignar posteriormente a textos específicos; por ejemplo, se pueden asignar números como texto o se puede agregar información en cualquier idioma.

:::image type="content" source="media/references-horizon-sdk/localization.png" alt-text="migration":::

Para obtener información más detallada, consulte [Creación de archivos de asignación (JSON)](#create-mapping-files-json).

También puede usar los valores de los protocolos analizados previamente para extraer información adicional.

Por ejemplo, para el valor que se basa en TCP, puede usar los valores de la capa de IPv4. Desde esta capa puede extraer valores como el origen del paquete y el destino.

Para lograr esto, el archivo de configuración JSON debe actualizarse con la propiedad `whitelists`.

## <a name="allow-list-data-mining-fields"></a>Campos de lista de permitidos (minería de datos)

Están disponibles los siguientes campos de lista de permitidos:

- name: nombre que se usa para la indexación.

- alert_title: título corto y único que explica el evento.

- alert_text: información adicional.

Se pueden agregar varias listas de permitidos, lo que ofrece una total flexibilidad en la indexación.

## <a name="json-sample-with-indexing"></a>Ejemplo de JSON con indexación 

```json
{
  "id":"CyberXHorizonProtocol",
  "endianess": "big",
  "metadata": {
    "is_distributed_control_system": false,
    "has_protocol_address": false,
    "is_scada_protocol": true,
    "is_router_potenial": false
  },
  "sanity_failure_codes": {
    "wrong magic": 0
  },
  "malformed_codes": {
    "not enough bytes": 0
  },
  "exports_dissect_as": {
  },
  "dissect_as": {
    "UDP": {
      "port": ["12345"]
    }
  },
  "fields": [
    {
      "id": "function",
      "type": "numeric"
    },
    {
      "id": "sub_function",
      "type": "numeric"
    },
    {
      "id": "name",
      "type": "string"
    },
    {
      "id": "model",
      "type": "string"
    },
    {
      "id": "version",
      "type": "numeric"
    }
  ],
"whitelists": [
    {
      "name": "Functions",
      "alert_title": "New Activity Detected - CyberX Horizon Protocol Function",
      "alert_text": "There was an attempt by the source to invoke a new function on the destination",
       "fields": [
        {
          "name": "Source",
          "value": "IPv4.src"
        },
        {
          "name": "Destination",
          "value": "IPv4.dst"
        },
        {
          "name": "Function",
          "value": "CyberXHorizonProtocol.function"
        }
      ]
    }

```
## <a name="extract-firmware-data"></a>Extracción de los datos de firmware

Puede extraer información de firmware, definir valores de índice y desencadenar alertas de firmware para el protocolo del complemento. Por ejemplo,

- Extraiga el modelo o la versión del firmware. Esta información se puede usar más adelante para identificar CVE.

- Desencadene una alerta cuando se detecta una nueva versión de firmware.

Para lograr esto, el archivo de configuración JSON debe actualizarse con la propiedad firmware.

## <a name="firmware-fields"></a>Campos de firmware

En esta sección se describen los campos de configuración JSON de la propiedad firmware.

- **name**
  
  Indica cómo se presenta el campo en la consola del sensor.

- **value**

 Indica el campo de implementación que se usará para proporcionar los datos. 

- **firmware_index:**

  Seleccione una de las siguientes opciones:  
  - **modelo**: El modelo del dispositivo. Habilita la detección de CVE.
  - **serial**: Número de serie del dispositivo. El número de serie no siempre está disponible para todos los protocolos. Este valor es único para cada dispositivo.
  - **rack**: Indica el id. del bastidor si el dispositivo forma parte de uno.
  - **slot**: Id. de ranura si el dispositivo forma parte de un bastidor.  
  - **module_address**: Se usa para presentar una jerarquía si el módulo se puede presentar detrás de otro dispositivo. Aplicable en lugar de una combinación de ranuras de bastidor, que es una presentación más sencilla.  
  - **firmware_version**: Indica la versión del dispositivo. Habilita la detección de CVE.
  - **alert_text**: Indica el texto que describe las desviaciones del firmware, por ejemplo, los cambios de versión.  
  - **index_by**: Indica los campos que se usarán para identificar e indexar el dispositivo. En el ejemplo siguiente, el dispositivo se identificó mediante su dirección IP. En algunos protocolos, se puede usar un índice más complejo. Por ejemplo, si se conecta otro dispositivo y sabe cómo extraer su ruta de acceso interna. Por ejemplo, el id. de unidad MODBUS se puede usar como parte del índice, como una combinación diferente de dirección IP e id. de unidad.
  - **firmware_fields**: Indica qué campos son de metadatos de dispositivo. En este ejemplo, se usa lo siguiente: model, revision y name. Cada protocolo puede definir sus propios datos de firmware.

## <a name="json-sample-with-firmware"></a>Ejemplo de JSON con firmware 

```json
{
  "id":"CyberXHorizonProtocol",
  "endianess": "big",
  "metadata": {
    "is_distributed_control_system": false,
    "has_protocol_address": false,
    "is_scada_protocol": true,
    "is_router_potenial": false
  },
  "sanity_failure_codes": {
    "wrong magic": 0
  },
  "malformed_codes": {
    "not enough bytes": 0
  },
  "exports_dissect_as": {
  },
  "dissect_as": {
    "UDP": {
      "port": ["12345"]
    }
  },
  "fields": [
    {
      "id": "function",
      "type": "numeric"
    },
    {
      "id": "sub_function",
      "type": "numeric"
    },
    {
      "id": "name",
      "type": "string"
    },
    {
      "id": "model",
      "type": "string"
    },
    {
      "id": "version",
      "type": "numeric"
    }
  ],
"whitelists": [
    {
      "name": "Functions",
      "alert_title": "New Activity Detected - CyberX Horizon Protocol Function",
      "alert_text": "There was an attempt by the source to invoke a new function on the destination",
       "fields": [
        {
          "name": "Source",
          "value": "IPv4.src"
        },
        {
          "name": "Destination",
          "value": "IPv4.dst"
        },
        {
          "name": "Function",
          "value": "CyberXHorizonProtocol.function"
        }
      ]
    },
"firmware": {
    "alert_text": "Firmware was changed on a network asset.   
                  This may be a planned activity, for example an authorized maintenance procedure",
    "index_by": [
      {
        "name": "Device",
        "value": "IPv4.src",
        "owner": true
      }
    ],
    "firmware_fields": [,
      {
        "name": "Model",
        "value": "CyberXHorizonProtocol.model",
        "firmware_index": "model"
      },
      {
        "name": "Revision",
        "value": "CyberXHorizonProtocol.version",
        “Firmware_index”: “firmware_version”
      },
      {
        "name": "Name",
        "value": "CyberXHorizonProtocol.name"
      }
    ]
  }
}

```
## <a name="extract-device-attributes"></a>Extracción de atributos de dispositivo 

Puede mejorar la información de dispositivo disponible en el dispositivo en el inventario, la minería de datos y otros informes.

- Nombre

- Tipo

- Vendor

- Sistema operativo

Para ello, el archivo de configuración JSON debe actualizarse con la propiedad **Properties**. 

Puede hacerlo después de escribir el complemento básico y de extraer los campos obligatorios.

## <a name="properties-fields"></a>Campos de Properties

En esta sección se describen los campos de configuración JSON de la propiedad properties. 

**config_file** 

Contiene el nombre de archivo que define cómo procesar cada clave en los campos `key`. El archivo de configuración debe estar en formato JSON e incluirse como parte de la carpeta del protocolo de complemento.

Cada clave en el archivo JSON define el conjunto de acciones que debe realizarse cuando se extrae dicha clave de un paquete.

Cada clave puede tener:

- **Datos de paquete**: indican las propiedades que se actualizarán en función de los datos extraídos del paquete (el campo de implementación usado para proporcionar esos datos).

- **Datos estáticos**: indican un conjunto predefinido de acciones `property-value` que deben actualizarse.

Las propiedades que se pueden configurar en este archivo son las siguientes: 

- **Name**: indica el nombre del dispositivo.

- **Type**: indica el tipo de dispositivo.

- **Vendor**: indica el proveedor del dispositivo.

- **Description**: indica el modelo de firmware del dispositivo (tiene menor prioridad que "model").

- **operatingSystem**: indica el sistema operativo del dispositivo.

### <a name="fields"></a>Campos

| Campo | Descripción |
|--|--|
| key | Indica la clave. |
| value | Indica el campo de implementación que se usará para proporcionar los datos. |
| is_static_key | Indica si el campo `key` se deriva como un valor a partir del paquete o si es un valor predefinido. |

### <a name="working-with-static-keys-only"></a>Trabajo solo con claves estáticas

Si está trabajando con claves estáticas, no tiene que configurar el archivo `config.file`. Puede configurar solo el archivo JSON.

## <a name="json-sample-with-properties"></a>Ejemplo de JSON con propiedad properties 

```json
{
  "id":"CyberXHorizonProtocol",
  "endianess": "big",
  "metadata": {
    "is_distributed_control_system": false,
    "has_protocol_address": false,
    "is_scada_protocol": true,
    "is_router_potenial": false
  },
  "sanity_failure_codes": {
    "wrong magic": 0
  },
  "malformed_codes": {
    "not enough bytes": 0
  },
  "exports_dissect_as": {
  },
  "dissect_as": {
    "UDP": {
      "port": ["12345"]
    }
  },
  "fields": [
    {
      "id": "function",
      "type": "numeric"
    },
  
      "id": "sub_function",
      "type": "numeric"
    },
    {
      "id": "name",
      "type": "string"
    },
    {
      "id": "model",
      "type": "string"
    },
    {
      "id": "version",
      "type": "numeric"
    },
    {
      "id": "vendor",
      "type": "string"
    }
  ],
"whitelists": [
    {
      "name": "Functions",
      "alert_title": "New Activity Detected - CyberX Horizon Protocol Function",
      "alert_text": "There was an attempt by the source to invoke a new function on the destination",
       "fields": [
        {
          "name": "Source",
          "value": "IPv4.src"
        },
        {
          "name": "Destination",
          "value": "IPv4.dst"
        },
        {
          "name": "Function",
          "value": "CyberXHorizonProtocol.function"
        }
      ]
    },
"firmware": {
    "alert_text": "Firmware was changed on a network asset. 
                  This may be a planned activity, for example an authorized maintenance procedure",
    "index_by": [
      {
        "name": "Device",
        "value": "IPv4.src",
        "owner": true
      }
    ],
    "firmware_fields": [,
      {
        "name": "Model",
        "value": "CyberXHorizonProtocol.model",
        "firmware_index": "model"
      },
      {
        "name": "Revision",
        "value": "CyberXHorizonProtocol.version",
        “Firmware_index”: “firmware_version”
      },
      {
        "name": "Name",
        "value": "CyberXHorizonProtocol.name"
      }
    ]
  }
"properties": {
    "config_file": "config_file_example",
"fields": [
  {
    "key": "vendor",
    "value": "CyberXHorizonProtocol.vendor",
    "is_static_key": true
  },
{
    "key": "name",
    "value": "CyberXHorizonProtocol.vendor",
    "is_static_key": true
  },

]
  }
}

```

## <a name="config_file_exapmle-json"></a>JSON CONFIG_FILE_EXAMPLE 

```json
{
"someKey": {
  "staticData": {
    "model": "FlashSystem", 
    "vendor": "IBM", 
    "type": "Storage"}
  }
  "packetData": [
    "name”  
  ]
}

```

## <a name="create-mapping-files-json"></a>Creación de archivos de asignación (JSON)

Puede personalizar el texto de salida del complemento para cumplir las necesidades de su entorno empresarial al definir y actualizar los archivos de asignación. Los cambios se pueden implementar fácilmente en el texto sin cambiar ni afectar al código. Cada archivo puede asignar uno o varios campos.

- Asignación de valores de campo a nombres; por ejemplo, 1:Restablecer, 2:Iniciar, 3:Detener.

- Asignación de texto para admitir varios idiomas.

Se pueden definir dos tipos de archivos de asignación.

  - [Archivo de asignación simple](#simple-mapping-file).

  - [Archivo de asignación de dependencias](#dependency-mapping-file).

    :::image type="content" source="media/references-horizon-sdk/localization.png" alt-text="Ethernet":::

    :::image type="content" source="media/references-horizon-sdk/unhandled.png" alt-text="Vista de las alertas no controladas.":::

    :::image type="content" source="media/references-horizon-sdk/policy-violation.png" alt-text="Una lista de infracciones de directiva conocidas.":::

## <a name="file-naming-and-storage-requirements"></a>Requisitos de almacenamiento y nomenclatura de archivos 

Los archivos de asignación deben guardarse en la carpeta metadata.

El nombre del archivo debe coincidir con el id. del archivo de configuración JSON.

:::image type="content" source="media/references-horizon-sdk/json-config.png" alt-text="Ejemplo de un archivo de configuración JSON.":::

## <a name="simple-mapping-file"></a>Archivo de asignación simple 

En el ejemplo siguiente se presenta un archivo JSON básico como valor de clave.

Cuando cree una lista de permitidos y esta contenga uno o varios de los campos asignados, el valor se convertirá de un número, una cadena o cualquier tipo en texto con formato presentado en el archivo de asignación.

```json
{
  “10”: “Read”,
  “20”: “Firmware Data”,
  “3”: “Write”
}

```

## <a name="dependency-mapping-file"></a>Archivo de asignación de dependencias 

Para indicar que el archivo es un archivo de dependencias, agregue la palabra clave `dependency` a la configuración de la asignación.

```json
dependency": { "field": "CyberXHorizonProtocol.function"  }}]
  }],
  "firmware": {
    "alert_text": "Firmware was changed on a network asset. This may be a planned activity, for example an authorized maintenance procedure",
    "index_by": [{ "name": "Device", "value": "IPv4.src", "owner": true }],
    "firmware_fields": [{ "name": "Model", "value":

```

El archivo contiene una asignación entre el campo de dependencia y el campo de función. Por ejemplo, entre la función y la subfunción. La subfunción cambia según la función proporcionada.

En la lista de permitidos configurada previamente, no hay ninguna configuración de dependencia, como se muestra a continuación.

```json
"whitelists": [
{
"name": "Functions",
"alert_title": "New Activity Detected - CyberX Horizon Protocol Function",
"alert_text": "There was an attempt by the source to invoke a new function on the destination",
"fields": [
{
"name": "Source",
"value": "IPv4.src"
},
{
"name": "Destination",
"value": "IPv4.dst"
},
{
"name": "Function",
"value": "CyberXHorizonProtocol.function"
}
]
}

```

La dependencia se puede basar en un valor específico o en un campo. En el ejemplo siguiente, se basa en un campo. Si la basa en un valor, defina el valor de extracción que se leerá del archivo de asignación.

En el ejemplo siguiente, la dependencia es la siguiente para el mismo valor de campo.

Por ejemplo, en la subfunción cinco, el significado cambia según la función.

  - Si es una función de lectura, cinco significa "leer la memoria".

  - Si es una función de escritura, se usa el mismo valor para leer de un archivo.

    ```json
    {
      “10”: {
         “5”:  “Memory”,
         “6”: “File”,
         “7” “Register”
       },
      “3”: {
       “5”: “File”,
       “7”: “Memory”,
       “6”, “Register”
      }
    }

    ```

### <a name="sample-file"></a>Archivo de muestra

```json
{
  "id":"CyberXHorizonProtocol",
  "endianess": "big",
  "metadata": {"is_distributed_control_system": false, "has_protocol_address": false, "is_scada_protocol": true, "is_router_potenial": false},
  "sanity_failure_codes": { "wrong magic": 0 },
  "malformed_codes": { "not enough bytes": 0  },
  "exports_dissect_as": {  },
  "dissect_as": { "UDP": { "port": ["12345"] }},
  "fields": [{ "id": "function", "type": "numeric" }, { "id": "sub_function", "type": "numeric" },
     {"id": "name", "type": "string" }, { "id": "model", "type": "string" }, { "id": "version", "type": "numeric" }],
  "whitelists": [{
      "name": "Functions",
      "alert_title": "New Activity Detected - CyberX Horizon Protocol Function",
      "alert_text": "There was an attempt by the source to invoke a new function on the destination",
      "fields": [{ "name": "Source", "value": "IPv4.src" }, { "name": "Destination", "value": "IPv4.dst" },
        { "name": "Function", "value": "CyberXHorizonProtocol.function" },
        { "name": "Sub function", "value": "CyberXHorizonProtocol.sub_function", "dependency": { "field": "CyberXHorizonProtocol.function"  }}]
  }],
  "firmware": {
    "alert_text": "Firmware was changed on a network asset. This may be a planned activity, for example an authorized maintenance procedure",
    "index_by": [{ "name": "Device", "value": "IPv4.src", "owner": true }],
    "firmware_fields": [{ "name": "Model", "value": "CyberXHorizonProtocol.model", "firmware_index": "model" },
       { "name": "Revision", "value": "CyberXHorizonProtocol.version", "firmware_index": "firmware_version" },
       { "name": "Name", "value": "CyberXHorizonProtocol.name" }]
  },
  "value_mapping": {
      "CyberXHorizonProtocol.function": {
        "file": "function-mapping"
      },
      "CyberXHorizonProtocol.sub_function": {
        "dependency": true,
        "file": "sub_function-mapping"
      }
  }
}

```

## <a name="json-sample-with-mapping"></a>Ejemplo de JSON con asignación

```json
{
  "id":"CyberXHorizonProtocol",
  "endianess": "big",
  "metadata": {
    "is_distributed_control_system": false,
    "has_protocol_address": false,
    "is_scada_protocol": true,
    "is_router_potenial": false
  },
  "sanity_failure_codes": {
    "wrong magic": 0
  },
  "malformed_codes": {
    "not enough bytes": 0
  },
  "exports_dissect_as": {
  },
  "dissect_as": {
    "UDP": {
      "port": ["12345"]
    }
  },
  "fields": [
    {
      "id": "function",
      "type": "numeric"
    },
    {
      "id": "sub_function",
      "type": "numeric"
    },
    {
      "id": "name",
      "type": "string"
    },
    {
      "id": "model",
      "type": "string"
    },
    {
      "id": "version",
      "type": "numeric"
    }
  ],
"whitelists": [
    {
      "name": "Functions",
      "alert_title": "New Activity Detected - CyberX Horizon Protocol Function",
      "alert_text": "There was an attempt by the source to invoke a new function on the destination",
       "fields": [
        {
          "name": "Source",
          "value": "IPv4.src"
        },
        {
          "name": "Destination",
          "value": "IPv4.dst"
        },
        {
          "name": "Function",
          "value": "CyberXHorizonProtocol.function"
        },
        {
          “name”: “Sub function”,
          “value”: “CyberXHorizonProtocol.sub_function”,
          “dependency”: {
              “field”: “CyberXHorizonProtocol.function”
        }
      ]
    },
"firmware": {
    "alert_text": "Firmware was changed on a network asset. This may be a planned activity, for example an authorized maintenance procedure",
    "index_by": [
      {
        "name": "Device",
        "value": "IPv4.src",
        "owner": true
      }
    ],
    "firmware_fields": [,
      {
        "name": "Model",
        "value": "CyberXHorizonProtocol.model",
        "firmware_index": "model"
      },
      {
        "name": "Revision",
        "value": "CyberXHorizonProtocol.version",
        “Firmware_index”: “firmware_version”
      },
      {
        "name": "Name",
        "value": "CyberXHorizonProtocol.name"
      }
    ]
  },
"value_mapping": {
    "CyberXHorizonProtocol.function": {
      "file": "function-mapping"
    },
    "CyberXHorizonProtocol.sub_function": {
      "dependency": true,
      "file": "sub_function-mapping"
    }
}

```
## <a name="package-upload-and-monitor-the-plugin"></a>Empaquetado, carga y supervisión del complemento 

En esta sección se describe cómo

  - Empaquetar el complemento.

  - Cargar el complemento.

  - Supervisar y depurar el complemento para evaluar la calidad del rendimiento.

Para empaquetar el complemento, haga lo siguiente:

1. Agregue el **artefacto** (puede ser, una biblioteca, un archivo config.json o metadatos) a un archivo `tar.gz`.

1. Cambie la extensión de archivo a \<XXX.hdp>, donde \<XXX> sea el nombre del complemento.

Para iniciar sesión en la consola de Horizon:

1.  Inicie sesión en la CLI del sensor como usuario administrador, CyberX o Support.

2.  En el archivo: `/var/cyberx/properties/horizon.properties` cambie la propiedad **ui.enabled** a **true** (`horizon.properties:ui.enabled=true`).

3.  Inicie sesión en la consola del sensor.

4.  Seleccione la opción **Horizon** en el menú principal.

    :::image type="content" source="media/references-horizon-sdk/horizon.png" alt-text="Selección de la opción Horizon en el panel izquierdo.":::

    Se abre la consola de Horizon.

    :::image type="content" source="media/references-horizon-sdk/plugins.png" alt-text="Vista de la consola de Horizon y de todos sus complementos.":::

## <a name="plugins-pane"></a>Panel de complementos

En el panel de complementos se muestra lo siguiente:

  - Complementos de infraestructura: los complementos de infraestructura instalados de manera predeterminada con Defender para IoT.

  - Complementos de la aplicación: complementos de aplicación que se instalaron de manera predeterminada con Defender para IoT y otros complementos desarrollados por Defender para IoT o desarrolladores externos.
    
Habilite y deshabilite los complementos que se han cargado con el botón de alternancia.

:::image type="content" source="media/references-horizon-sdk/toggle.png" alt-text="Botón de alternancia CIP.":::

### <a name="uploading-a-plugin"></a>Carga de un complemento

Después de crear y empaquetar el complemento, puede cargarlo en el sensor de Defender para IoT. Para lograr la cobertura total de la red, debe cargar el complemento en cada uno de los sensores de su organización.

Para cargar:

1.  Inicie sesión en el sensor.


2. Seleccione **Cargar**.

    :::image type="content" source="media/references-horizon-sdk/upload.png" alt-text="Carga de los complementos.":::

3. Vaya al complemento y arrástrelo al cuadro de diálogo de complementos. Compruebe que el prefijo sea `.hdp`. El complemento se carga.

## <a name="plugin-status-overview"></a>Información general del estado del complemento 

La ventana **Overview** (Información general) de la consola de Horizon proporciona información acerca del complemento que ha cargado y permite deshabilitarlo y habilitarlo.

:::image type="content" source="media/references-horizon-sdk/overview.png" alt-text="Información general de la consola de Horizon.":::

| Campo | Descripción |
|--|--|
| Application | Nombre del complemento que ha cargado. |
| :::image type="content" source="media/references-horizon-sdk/switch.png" alt-text="Interruptor de activado y desactivado."::: | **Active** o **desactive** el complemento. Defender para IoT no controlará el tráfico del protocolo definido en el complemento al desactivar el complemento. |
| Hora | Hora en la que se analizaron los datos por última vez. Se actualiza cada 5 segundos. |
| PPS | Número de paquetes por segundo. |
| Ancho de banda | Ancho de banda promedio detectado en los últimos 5 segundos. |
| Malforms (Formato incorrecto) | Se utilizan validaciones de formato incorrecto después de que el protocolo se haya validado de forma positiva. Si se produce un error al procesar los paquetes basados en el protocolo, se devuelve una respuesta de error.   <br><br>Esta columna indica el número de errores de formato incorrecto en los últimos 5 segundos. Para obtener información más detallada, consulte [Validaciones de código con formato incorrecto](#malformed-code-validations). |
| Advertencias | Los paquetes coinciden con la estructura y la especificación, pero hay un comportamiento inesperado según la configuración de advertencias del complemento. |
| Errors | Número de paquetes que devuelven errores en las validaciones de protocolo básicas. Valida que el paquete coincida con las definiciones de protocolo. El número que se muestra aquí indica el número de errores detectados en los últimos 5 segundos. Para obtener información más detallada, consulte [Validaciones de código de comprobación](#sanity-code-validations). |
| :::image type="content" source="media/references-horizon-sdk/monitor.png" alt-text="Icono del monitor."::: | Revise los detalles sobre formato incorrecto y las advertencias detectadas para el complemento. |

## <a name="plugin-details"></a>Detalles del complemento

Puede supervisar el comportamiento del complemento en tiempo real mediante el análisis del número de elementos con *formato incorrecto* y las *advertencias* detectadas para el complemento. Hay una opción disponible para inmovilizar la pantalla y exportarla para una posterior investigación.

:::image type="content" source="media/references-horizon-sdk/snmp.png" alt-text="Pantalla del monitor de SNMP.":::

Para supervisar, haga lo siguiente:

En Información general, seleccione el botón Supervisar del complemento.

Pasos siguientes

Configuración de la [API de Horizon](references-horizon-api.md).
