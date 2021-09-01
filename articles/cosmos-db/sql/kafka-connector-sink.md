---
title: 'Kafka Connect para Azure Cosmos DB: conector receptor'
description: El conector receptor de Azure Cosmos DB permite exportar datos de temas de Apache Kafka a una base de datos de Azure Cosmos DB. El conector sondea los datos de Kafka para escribir en contenedores de la base de datos en función de la suscripción de temas.
author: kushagrathapar
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 06/28/2021
ms.author: kuthapar
ms.openlocfilehash: fa0fc899453c11858d518ed5f224b6eb94e218f4
ms.sourcegitcommit: dcf1defb393104f8afc6b707fc748e0ff4c81830
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/27/2021
ms.locfileid: "123114470"
---
# <a name="kafka-connect-for-azure-cosmos-db---sink-connector"></a>Kafka Connect para Azure Cosmos DB: conector receptor
[!INCLUDE[appliesto-sql-api](../includes/appliesto-sql-api.md)]

Kafka Connect para Azure Cosmos DB es un conector para leer y escribir datos en Azure Cosmos DB. El conector receptor de Azure Cosmos DB permite exportar datos de temas de Apache Kafka a una base de datos de Azure Cosmos DB. El conector sondea los datos de Kafka para escribir en contenedores de la base de datos en función de la suscripción de temas.

## <a name="prerequisites"></a>Requisitos previos

* Comience con la [configuración de la plataforma de Confluent](https://github.com/microsoft/kafka-connect-cosmosdb/blob/dev/doc/Confluent_Platform_Setup.md), ya que le proporciona un entorno completo con el que trabajar. Si no desea usar la plataforma de Confluent, debe instalar y configurar Zookeeper, Apache Kafka y Kafka Connect usted mismo. También deberá instalar y configurar los conectores de Azure Cosmos DB manualmente.
* [Guía de configuración](https://github.com/microsoft/kafka-connect-cosmosdb/blob/dev/doc/CosmosDB_Setup.md) para crear una cuenta y un contenedor de Azure Cosmos DB.
* Shell de Bash, que se ha probado en GitHub Codespaces, Mac, Ubuntu y Windows con WSL2. Este shell no funciona en Cloud Shell ni WSL1.
* Descarga de [Java 11+](https://www.oracle.com/java/technologies/javase-jdk11-downloads.html)
* Descarga de [Maven](https://maven.apache.org/download.cgi)

## <a name="install-sink-connector"></a>Instalación del conector receptor

Si usa la [configuración recomendada de la plataforma de Confluent](https://github.com/microsoft/kafka-connect-cosmosdb/blob/dev/doc/Confluent_Platform_Setup.md), el conector receptor de Azure Cosmos DB se incluye en la instalación y puede omitir este paso.  

De lo contrario, puede descargar el archivo JAR de la última [versión](https://github.com/microsoft/kafka-connect-cosmosdb/releases) o empaquetar este repositorio para crear un archivo JAR. Para instalar el conector manualmente mediante el archivo JAR, consulte estas [instrucciones](https://docs.confluent.io/current/connect/managing/install.html#install-connector-manually). También puede empaquetar un nuevo archivo JAR desde el código fuente.

```bash
# clone the kafka-connect-cosmosdb repo if you haven't done so already
git clone https://github.com/microsoft/kafka-connect-cosmosdb.git
cd kafka-connect-cosmosdb

# package the source code into a JAR file
mvn clean package

# include the following JAR file in Kafka Connect installation
ls target/*dependencies.jar
```

## <a name="create-a-kafka-topic-and-write-data"></a>Creación de un tema de Kafka y escritura de datos

Si usa la plataforma Confluent, la manera más fácil de crear un tema de Kafka es mediante la experiencia de usuario del Centro de control proporcionada. De lo contrario, puede crear manualmente un tema de Kafka con la sintaxis siguiente:

```bash
./kafka-topics.sh --create --zookeeper <ZOOKEEPER_URL:PORT> --replication-factor <NO_OF_REPLICATIONS> --partitions <NO_OF_PARTITIONS> --topic <TOPIC_NAME>
```

En este escenario, vamos a crear un tema de Kafka llamado "hotels" y a escribir datos JSON insertados sin esquema en el tema. Para crear un tema en el centro de control, consulte la [guía de Confluent](https://docs.confluent.io/platform/current/quickstart/ce-docker-quickstart.html#step-2-create-ak-topics).

A continuación, inicie el productor de la consola de Kafka para escribir algunos registros en el tema "hotels".

```powershell
# Option 1: If using Codespaces, use the built-in CLI utility
kafka-console-producer --broker-list localhost:9092 --topic hotels

# Option 2: Using this repo's Confluent Platform setup, first exec into the broker container
docker exec -it broker /bin/bash
kafka-console-producer --broker-list localhost:9092 --topic hotels

# Option 3: Using your Confluent Platform setup and CLI install
<path-to-confluent>/bin/kafka-console-producer --broker-list <kafka broker hostname> --topic hotels
```

En el productor de la consola, escriba lo siguiente:

```json
{"id": "h1", "HotelName": "Marriott", "Description": "Marriott description"}
{"id": "h2", "HotelName": "HolidayInn", "Description": "HolidayInn description"}
{"id": "h3", "HotelName": "Motel8", "Description": "Motel8 description"}
```

Los tres registros especificados se publican en el tema de Kafka "hotels" en formato JSON.

## <a name="create-the-sink-connector"></a>Creación del conector receptor

Cree el conector receptor de Azure Cosmos DB en Kafka Connect. El siguiente cuerpo JSON define la configuración del conector receptor. Asegúrese de reemplazar los valores de `connect.cosmos.connection.endpoint` y `connect.cosmos.master.key`, las propiedades que debería haber guardado de la guía de configuración de Azure Cosmos DB en los requisitos previos.

Consulte la sección [Propiedades del receptor](#sink-configuration-properties) para obtener más información sobre cada una de estas propiedades de configuración.

```json
{
  "name": "cosmosdb-sink-connector",
  "config": {
    "connector.class": "com.azure.cosmos.kafka.connect.sink.CosmosDBSinkConnector",
    "tasks.max": "1",
    "topics": [
      "hotels"
    ],
    "value.converter": "org.apache.kafka.connect.json.JsonConverter",
    "value.converter.schemas.enable": "false",
    "key.converter": "org.apache.kafka.connect.json.JsonConverter",
    "key.converter.schemas.enable": "false",
    "connect.cosmos.connection.endpoint": "https://<cosmosinstance-name>.documents.azure.com:443/",
    "connect.cosmos.master.key": "<cosmosdbprimarykey>",
    "connect.cosmos.databasename": "kafkaconnect",
    "connect.cosmos.containers.topicmap": "hotels#kafka"
  }
}
```

Una vez que haya rellenado todos los valores, guarde el archivo JSON en algún lugar de forma local. Puede usar este archivo para crear el conector mediante la API de REST.

### <a name="create-connector-using-control-center"></a>Creación de un conector mediante el centro de control

Una opción fácil para crear el conector es desde la página web del centro de control. Siga esta [guía de instalación](https://docs.confluent.io/platform/current/quickstart/ce-docker-quickstart.html#step-3-install-a-ak-connector-and-generate-sample-data) para crear un conector desde el centro de control. En lugar de usar la opción `DatagenConnector`, use el icono `CosmosDBSinkConnector`. Al configurar el conector receptor, rellene los valores que haya rellenado en el archivo JSON.

Como alternativa, en la página de los conectores, puede cargar el archivo JSON creado anteriormente mediante la opción **Upload connector config file** (Cargar archivo de configuración del receptor).

:::image type="content" source="./media/kafka-connector-sink/upload-connector-config.png" alt-text="Carga de la configuración del conector":::

### <a name="create-connector-using-rest-api"></a>Creación de un conector mediante la API de REST

Creación del conector receptor mediante la API de REST de Connect:

```bash
# Curl to Kafka connect service
curl -H "Content-Type: application/json" -X POST -d @<path-to-JSON-config-file> http://localhost:8083/connectors

```

## <a name="confirm-data-written-to-cosmos-db"></a>Confirmación de los datos escritos en Cosmos DB

Inicie sesión en [Azure Portal](https://portal.azure.com/learn.docs.microsoft.com) y vaya a la cuenta de Azure Cosmos DB. Compruebe que los tres registros del tema "hotels" están creados en su cuenta.

## <a name="cleanup"></a>Limpieza

Para eliminar el conector desde el centro de control, vaya al conector receptor que creó y haga clic en el icono de **Eliminar**.

:::image type="content" source="./media/kafka-connector-sink/delete-connector.png" alt-text="Eliminación del conector":::

Como alternativa, use la API de REST de Connect para la eliminación:

```bash
# Curl to Kafka connect service
curl -X DELETE http://localhost:8083/connectors/cosmosdb-sink-connector
```

Para eliminar el servicio Azure Cosmos DB creado y su grupo de recursos mediante la CLI de Azure, consulte estos [pasos](https://github.com/microsoft/kafka-connect-cosmosdb/blob/dev/doc/CosmosDB_Setup.md#cleanup).

## <a name="sink-configuration-properties"></a><a id="sink-configuration-properties"></a>Propiedades de configuración del receptor

La siguiente configuración se usa para configurar el conector receptor de Kafka para Cosmos DB. Estos valores de configuración determinan qué datos de los temas de Kafka se consumen, qué datos del contenedor de Azure Cosmos DB se escriben en ellos y los formatos para serializar los datos. Para obtener un archivo de configuración de ejemplo con los valores predeterminados, consulte [esta configuración]( https://github.com/microsoft/kafka-connect-cosmosdb/blob/dev/src/docker/resources/sink.example.json).

| Nombre | Tipo | Descripción | Obligatorio/opcional |
| :--- | :--- | :--- | :--- |
| Temas | list | Una lista de los temas de Kafka que se verán. | Requerido |
| connector.class | string | Nombre de clase del receptor de Azure Cosmos DB. Se debe establecer en `com.azure.cosmos.kafka.connect.sink.CosmosDBSinkConnector`. | Requerido |
| connect.cosmos.connection.endpoint | uri | Cadena del URI del punto de conexión de Azure Cosmos. | Obligatorio |
| connect.cosmos.master.key | string | La clave principal de Azure Cosmos DB a la que se conecta el receptor. | Obligatorio |
| connect.cosmos.databasename | string | El nombre de la base de datos de Azure Cosmos DB en la que escribe el receptor. | Obligatorio |
| connect.cosmos.containers.topicmap | string | Asignación entre temas de Kafka y contenedores de Azure Cosmos DB, con formato CSV como se muestra: `topic#container,topic2#container2`. | Requerido |
| key.converter | string | Formato de serialización para los datos clave escritos en el tema de Kafka. | Requerido |
| value.converter | string | Formato de serialización para los datos de valor escritos en el tema de Kafka. | Requerido |
| key.converter.schemas.enable | string | Establezca el valor en "true" si los datos de clave tienen un esquema insertado. | Optional |
| value.converter.schemas.enable | string | Establezca el valor en "true" si los datos de clave tienen un esquema insertado. | Optional |
| tasks.max | int | Número máximo de tare as de receptor del conector. Valor predeterminado: `1` | Optional |

Los datos siempre se escribirán en Azure Cosmos DB como JSON sin ningún esquema.

## <a name="supported-data-types"></a>Tipos de datos admitidos

El conector receptor de Azure Cosmos DB convierte el registro de receptor en un documento JSON que admite los siguientes tipos de esquema:

| Tipo de esquema | Tipo de datos JSON |
| :--- | :--- |
| Array | Array |
| Boolean | Boolean |
| Float32 | Número |
| Float64 | Número |
| Int8 | Número |
| Int16 | Número |
| Int32 | Number |
| Int64 | Número|
| Map | Objeto (JSON)|
| String | String<br> Null |
| Estructura | Objeto (JSON) |

El conector receptor también admite los siguientes tipos lógicos de AVRO:

| Tipo de esquema | Tipo de datos JSON |
| :--- | :--- |
| Fecha | Número |
| Hora | Número |
| Timestamp | Número |

> [!NOTE]
> Actualmente, la deserialización de bytes no es compatible con el conector receptor de Azure Cosmos DB.

## <a name="single-message-transformssmt"></a>Transformaciones simples de mensajes (SMT)

Junto con la configuración del conector receptor, puede especificar el uso de transformaciones simples de mensajes (SMT) para modificar los mensajes que fluyen a través de la plataforma de Kafka Connect. Para más información, consulte la [documentación sobre SMT de Confluent](https://docs.confluent.io/platform/current/connect/transforms/overview.html).

### <a name="using-the-insertuuid-smt"></a>Uso de SMT InsertUUID

Puede usar SMT InsertUUID para agregar automáticamente los identificadores de elementos. Con el SMT `InsertUUID` personalizado, puede insertar el campo `id` con un valor UUID aleatorio para cada mensaje, antes de que se escriba en Azure Cosmos DB.

> [!WARNING]
> Use este SMT solo si los mensajes no contienen el campo `id`. De lo contrario, los valores `id` se sobrescribirán y es posible que termine con elementos duplicados en la base de datos. El uso de UUID como identificador de mensaje puede ser rápido y sencillo, pero [no es una clave de partición ideal](https://stackoverflow.com/questions/49031461/would-using-a-substring-of-a-guid-in-cosmosdb-as-partitionkey-be-a-bad-idea) para usarla en Azure Cosmos DB.

### <a name="install-the-smt"></a>Instalación de SMT

Para poder usar la SMT `InsertUUID`, deberá instalar esta transformación en la configuración de la plataforma de Confluent. Si usa el programa de instalación de la plataforma de Confluent de este repositorio, la transformación ya está incluida en la instalación y puede omitir este paso.

Como alternativa, puede empaquetar el [origen InsertUUID](https://github.com/confluentinc/kafka-connect-insert-uuid) para crear un archivo JAR. Para instalar el conector manualmente mediante el archivo JAR, consulte estas [instrucciones](https://docs.confluent.io/current/connect/managing/install.html#install-connector-manually).

```bash
# clone the kafka-connect-insert-uuid repo
https://github.com/confluentinc/kafka-connect-insert-uuid.git
cd kafka-connect-insert-uuid

# package the source code into a JAR file
mvn clean package

# include the following JAR file in Confluent Platform installation
ls target/*.jar
```

### <a name="configure-the-smt"></a>Configuración de SMT

Dentro de la configuración del conector receptor, agregue las siguientes propiedades para establecer `id`.

```json
"transforms": "insertID",
"transforms.insertID.type": "com.github.cjmatta.kafka.connect.smt.InsertUuid$Value",
"transforms.insertID.uuid.field.name": "id"
```

Para obtener más información sobre el uso de esta SMT, vea el [repositorio InsertUUID](https://github.com/confluentinc/kafka-connect-insert-uuid).

### <a name="using-smts-to-configure-time-to-live-ttl"></a>Uso de SMT para configurar el período de vida (TTL)

Con las SMT `InsertField` y `Cast`, puede configurar el TTL en cada elemento creado en Azure Cosmos DB. Habilite el TTL en el contenedor antes de habilitar el TTL en un nivel de elemento. Para más información, vea la documentación sobre el [Período de vida](how-to-time-to-live.md#enable-time-to-live-on-a-container-using-azure-portal).

Dentro de la configuración del conector receptor, agregue las siguientes propiedades para establecer el TTL en segundos. En este ejemplo, el TTL se establece en 100 segundos. Si el mensaje ya contiene el campo `TTL`, estas SMT sobrescribirán el valor `TTL`.

```json
"transforms": "insertTTL,castTTLInt",
"transforms.insertTTL.type": "org.apache.kafka.connect.transforms.InsertField$Value",
"transforms.insertTTL.static.field": "ttl",
"transforms.insertTTL.static.value": "100",
"transforms.castTTLInt.type": "org.apache.kafka.connect.transforms.Cast$Value",
"transforms.castTTLInt.spec": "ttl:int32"
```

Para más información sobre el uso de estas SMT, vea la documentación sobre [InsertField](https://docs.confluent.io/platform/current/connect/transforms/insertfield.html) y [Cast](https://docs.confluent.io/platform/current/connect/transforms/cast.html).

## <a name="troubleshooting-common-issues"></a>Solución de problemas habituales

Estas son las soluciones a algunos problemas comunes que pueden surgir al trabajar con el conector receptor de Kafka.

### <a name="read-non-json-data-with-jsonconverter"></a>Lectura de datos no JSON con JsonConverter

Si tiene datos que no son JSON en el tema de origen en Kafka e intenta leerlos mediante `JsonConverter`, verá la excepción siguiente:

```console
org.apache.kafka.connect.errors.DataException: Converting byte[] to Kafka Connect data failed due to serialization error:
…
org.apache.kafka.common.errors.SerializationException: java.io.CharConversionException: Invalid UTF-32 character 0x1cfa7e2 (above 0x0010ffff) at char #1, byte #7)

```

Este error probablemente se deba a que los datos del tema de origen están serializados en Avro u otro formato, como la cadena CSV.

**Solución**: si los datos del tema están en formato AVRO, cambie el conector receptor de Kafka Connect para que use `AvroConverter` como se muestra a continuación.

```json
"value.converter": "io.confluent.connect.avro.AvroConverter",
"value.converter.schema.registry.url": "http://schema-registry:8081",
```

### <a name="read-non-avro-data-with-avroconverter"></a>Lectura de datos que no son Avro con AvroConverter

Este escenario es aplicable cuando se intenta usar el convertidor de Avro para leer datos de un tema que no está en formato Avro. Incluye datos escritos por un serializador Avro distinto del serializador Avro del registro de esquema de Confluent, que tiene su propio formato de conexión.

```console
org.apache.kafka.connect.errors.DataException: my-topic-name
at io.confluent.connect.avro.AvroConverter.toConnectData(AvroConverter.java:97)
…
org.apache.kafka.common.errors.SerializationException: Error deserializing Avro message for id -1
org.apache.kafka.common.errors.SerializationException: Unknown magic byte!

```

**Solución**: compruebe el formato de serialización del tema de origen. A continuación, cambie el conector receptor de Kafka Connect para que use el convertidor apropiado o cambie el formato ascendente a Avro.

### <a name="read-a-json-message-without-the-expected-schemapayload-structure"></a>Lectura de un mensaje JSON sin la estructura de esquema/carga útil esperada

Kafka Connect admite una estructura especial de mensajes JSON que contienen la carga útil y el esquema como se muestra a continuación.

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
    ]
  },
  "payload": {
    "userid": 123,
    "name": "Sam"
  }
}
```

Si intenta leer datos JSON que no contienen los datos de esta estructura, recibirá el siguiente error:

```none
org.apache.kafka.connect.errors.DataException: JsonConverter with schemas.enable requires "schema" and "payload" fields and may not contain additional fields. If you are trying to deserialize plain JSON data, set schemas.enable=false in your converter configuration.
```

Para que sea claro, la única estructura JSON válida para `schemas.enable=true` tiene campos de esquema y carga útil como elementos de nivel superior, como se muestra anteriormente. Como indica el mensaje de error, si solo tiene datos JSON sin formato, debe cambiar la configuración del conector por lo siguiente:

```json
"value.converter": "org.apache.kafka.connect.json.JsonConverter",
"value.converter.schemas.enable": "false",
```

## <a name="limitations"></a>Limitaciones

* No se admite la creación automática de bases de datos y contenedores en Azure Cosmos DB. La base de datos y los contenedores ya deben existir y deben configurarse correctamente.

## <a name="next-steps"></a>Pasos siguientes

Puede obtener más información sobre la fuente de cambios de Azure Cosmo DB en los siguientes documentos:

* [Introducción a la fuente de cambios](https://azurecosmosdb.github.io/labs/dotnet/labs/08-change_feed_with_azure_functions.html)
* [Lectura desde la fuente de cambios](read-change-feed.md)