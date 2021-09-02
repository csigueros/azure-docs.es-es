---
title: Uso de Kafka Connect para Azure Cosmos DB para leer y escribir datos
description: Kafka Connect para Azure Cosmos DB es un conector para leer y escribir datos en Azure Cosmos DB. Kafka Connect es una herramienta de streaming de datos escalable y confiable entre Apache Kafka y otros sistemas.
author: kushagrathapar
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 06/28/2021
ms.author: kuthapar
ms.openlocfilehash: 02489a961edf3c9044209da64882d0ab60d20bb4
ms.sourcegitcommit: dcf1defb393104f8afc6b707fc748e0ff4c81830
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/27/2021
ms.locfileid: "123114498"
---
# <a name="kafka-connect-for-azure-cosmos-db"></a>Kafka Connect para Azure Cosmos DB
[!INCLUDE[appliesto-sql-api](../includes/appliesto-sql-api.md)]

[Kafka Connect](http://kafka.apache.org/documentation.html#connect) es una herramienta de streaming de datos escalable y confiable entre Apache Kafka y otros sistemas. Con Kafka Connect puede definir conectores que muevan grandes conjuntos de datos dentro y fuera de Kafka. Kafka Connect para Azure Cosmos DB es un conector para leer y escribir datos en Azure Cosmos DB.

## <a name="source--sink-connectors-semantics"></a>Semántica de los conectores de origen y receptores

* **Conector de origen**: actualmente, este conector se admite al menos una vez con varias tareas y exactamente una para tareas únicas.

* **Conector receptor**: este conector es totalmente compatible con la semántica solamente una vez.

## <a name="supported-data-formats"></a>Formatos de datos compatibles

Los conectores de origen y receptor se pueden configurar para admitir los siguientes formatos de datos:

| Formato | Descripción |
| :----------- | :---------- |
| JSON sin formato  | Estructura de registros JSON sin ningún esquema asociado. |
| JSON con esquema | Estructura de registros JSON con información de esquema explícita para asegurarse de que los datos coinciden con el formato esperado. |
| AVRO | Un marco de serialización de datos y de llamada a procedimiento remoto orientado a filas desarrollado dentro del proyecto de Hadoop de Apache. Usa JSON para definir tipos de datos y protocolos y serializa datos en un formato binario compacto.

La configuración de clave y valor, incluido el formato y la serialización, se puede definir de forma independiente en Kafka. Por lo tanto, es posible trabajar con diferentes formatos de datos para claves y valores, respectivamente. Para admitir diferentes formatos de datos, hay una configuración de convertidor para `key.converter` y `value.converter`.

## <a name="converter-configuration-examples"></a>Ejemplos de configuración del convertidor

### <a name="plain-json"></a><a id="json-plain"></a>JSON sin formato

Si necesita usar JSON sin registro de esquema para conectar datos, use el elemento `JsonConverter` compatible con Kafka. En el ejemplo siguiente se muestran las propiedades de valor y clave `JsonConverter` que se agregan a la configuración:

  ```java
  key.converter=org.apache.kafka.connect.json.JsonConverter
  key.converter.schemas.enable=false
  value.converter=org.apache.kafka.connect.json.JsonConverter
  value.converter.schemas.enable=false
  ```

### <a name="json-with-schema"></a><a id="json-with-schema"></a>JSON con esquema

Establezca las propiedades `key.converter.schemas.enable` y `value.converter.schemas.enable` en true para que la clave o el valor se traten como un objeto JSON compuesto que contenga un esquema interno y los datos. Sin estas propiedades, la clave o el valor se tratan como JSON sin formato. 

  ```java
  key.converter=org.apache.kafka.connect.json.JsonConverter
  key.converter.schemas.enable=true
  value.converter=org.apache.kafka.connect.json.JsonConverter
  value.converter.schemas.enable=true
  ```

El mensaje resultante para Kafka sería parecido al ejemplo siguiente, con el esquema y la carga útil como elementos de nivel superior en JSON:

  ```json
  {
    "schema": {
      "type": "struct",
      "fields": [
        {
          "type": "int32",
          "optional": false,
          "field": "userid"
        },
        {
          "type": "string",
          "optional": false,
          "field": "name"
        }
      ],
      "optional": false,
      "name": "ksql.users"
    },
    "payload": {
      "userid": 123,
      "name": "user's name"
    }
  }
  ```

> [!NOTE]
> El mensaje escrito en Azure Cosmos DB se compone del esquema y la carga útil. Observe el tamaño del mensaje, así como la proporción del mensaje compuesta de la carga útil frente al esquema. El esquema se repite en cada mensaje que escribe en Kafka. En escenarios como este, es posible que quiera usar un formato de serialización, como un esquema JSON o AVRO, donde el esquema se almacena por separado y el mensaje contiene solo la carga útil.

### <a name="avro"></a><a id="avro"></a>AVRO

El conector de Kafka admite el formato de datos AVRO. Para usar el formato AVRO, configure un elemento `AvroConverter` para que Kafka Connect sepa cómo trabajar con datos AVRO. Kafka Connect para Azure Cosmos DB se ha probado con [AvroConverter](https://www.confluent.io/hub/confluentinc/kafka-connect-avro-converter), proporcionado por Confluent bajo licencia de Apache 2.0. También puede usar un convertidor personalizado diferente si lo prefiere.

Kafka trata las claves y los valores de forma independiente. Especifique las propiedades `key.converter` y `value.converter` según sea necesario en la configuración de trabajo. Al usar `AvroConverter`, agregue una propiedad de convertidor adicional que proporciona la dirección URL para el registro de esquema. En el ejemplo siguiente se muestran las propiedades de valor y clave de AvroConverter que se agregan a la configuración:

  ```java
  key.converter=io.confluent.connect.avro.AvroConverter
  key.converter.schema.registry.url=http://schema-registry:8081
  value.converter=io.confluent.connect.avro.AvroConverter
  value.converter.schema.registry.url=http://schema-registry:8081
  ```

## <a name="choose-a-conversion-format"></a>Elección de un formato de conversión

Estas son algunas consideraciones sobre cómo elegir un formato de conversión:

* Al configurar un **conector de origen**:

  * Si quiere que Kafka Connect incluya JSON sin formato en el mensaje que escribe en Kafka, establezca la configuración [JSON sin formato](#json-plain).

  * Si quiere que Kafka Connect incluya el esquema en el mensaje que escribe en Kafka, establezca la configuración [JSON con esquema](#json-with-schema).

  * Si quiere que Kafka Connect incluya el formato AVRO en el mensaje que escribe en Kafka, establezca la configuración [AVRO](#avro).

* Si va a consumir datos JSON de un tema de Kafka en un **conector receptor**, obtenga información sobre cómo se serializó JSON cuando se escribió en el tema de Kafka:

  * Si se escribió con el serializador JSON, establezca Kafka Connect para que use el convertidor de JSON `(org.apache.kafka.connect.json.JsonConverter)`.

    * Si los datos JSON se escribieron como una cadena sin formato, determine si los datos incluyen un esquema anidado o una carga útil. Si es así, establezca la configuración [JSON con esquema](#json-with-schema).
    * Sin embargo, si está consumiendo datos JSON y no tiene el esquema o la construcción de carga útil, debe indicar a Kafka Connect que **no** busque un esquema estableciendo `schemas.enable=false` según la configuración [JSON sin formato](#json-plain).

  * Si se escribió con el serializador AVRO, establezca Kafka Connect para que use el convertidor de AVRO `(io.confluent.connect.avro.AvroConverter)` según la configuración [AVRO](#avro).

## <a name="configuration"></a>Configuración

### <a name="common-configuration-properties"></a>Propiedades de configuración comunes

Los conectores de origen y receptor comparten las siguientes propiedades de configuración comunes:

| Nombre | Tipo | Descripción | Obligatorio/opcional |
| :--- | :--- | :--- | :--- |
| connect.cosmos.connection.endpoint | uri | Cadena del URI del punto de conexión de Cosmos | Obligatorio |
| connect.cosmos.master.key | string | La clave principal de Azure Cosmos DB a la que se conecta el receptor. | Obligatorio |
| connect.cosmos.databasename | string | El nombre de la base de datos de Azure Cosmos DB en la que escribe el receptor. | Obligatorio |
| connect.cosmos.containers.topicmap | string | Asignación entre temas de Kafka y contenedores de Azure Cosmos DB. Se le aplica formato mediante CSV como `topic#container,topic2#container2`. | Obligatorio |

Para conocer la configuración específica del conector receptor, vea la [Documentación sobre el conector receptor](kafka-connector-sink.md).

Para conocer la configuración específica del conector de origen, vea la [Documentación sobre el conector de origen](kafka-connector-source.md).

## <a name="common-configuration-errors"></a>Errores de configuración comunes

Si configura mal los convertidores en Kafka Connect, se pueden producir errores. Estos errores se mostrarán en el receptor del conector de Kafka porque intentará deserializar los mensajes que ya están almacenados en Kafka. Los problemas del convertidor no suelen producirse en el origen porque la serialización se establece en el origen.

Para más información, vea la documentación sobre los [errores de configuración comunes](https://www.confluent.io/blog/kafka-connect-deep-dive-converters-serialization-explained/#common-errors).

## <a name="project-setup"></a>Configuración del proyecto

Consulte el [Tutorial para desarrolladores y la configuración del proyecto](https://github.com/microsoft/kafka-connect-cosmosdb/blob/dev/doc/Developer_Walkthrough.md) para obtener las instrucciones de configuración inciales.

## <a name="performance-testing"></a>Pruebas de rendimiento

Para más información sobre la ejecución de pruebas de rendimiento para los conectores de origen y receptor, vea el [documento sobre las pruebas de rendimiento](https://github.com/microsoft/kafka-connect-cosmosdb/blob/dev/doc/Performance_Testing.md).

Consulte la [configuración del entorno de rendimiento](https://github.com/microsoft/kafka-connect-cosmosdb/blob/dev/src/perf/README.md) para ver los pasos exactos para implementar el entorno de prueba de rendimiento para los conectores.

## <a name="resources"></a>Recursos

* [Kafka Connect](http://kafka.apache.org/documentation.html#connect)
* [Kafka Connect en profundidad: explicación sobre los convertidores y la serialización](https://www.confluent.io/blog/kafka-connect-deep-dive-converters-serialization-explained/)

## <a name="next-steps"></a>Pasos siguientes

* Kafka Connect para Azure Cosmos DB: [conector de origen](kafka-connector-source.md)
* Kafka Connect para Azure Cosmos DB: [conector receptor](kafka-connector-sink.md)