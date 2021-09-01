---
title: 'Kafka Connect para Azure Cosmos DB: conector de origen'
description: El conector de origen de Azure Cosmos DB proporciona la capacidad de leer datos de la fuente de cambios de Azure Cosmos DB y publicar estos datos en un tema de Kafka. Kafka Connect para Azure Cosmos DB es un conector para leer y escribir datos en Azure Cosmos DB.
author: kushagrathapar
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 06/28/2021
ms.author: kuthapar
ms.openlocfilehash: 99879c5fa14cc91e8a39b99ff1a428f31bfde744
ms.sourcegitcommit: dcf1defb393104f8afc6b707fc748e0ff4c81830
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/27/2021
ms.locfileid: "123114259"
---
# <a name="kafka-connect-for-azure-cosmos-db---source-connector"></a>Kafka Connect para Azure Cosmos DB: conector de origen
[!INCLUDE[appliesto-sql-api](../includes/appliesto-sql-api.md)]

Kafka Connect para Azure Cosmos DB es un conector para leer y escribir datos en Azure Cosmos DB. El conector de origen de Azure Cosmos DB proporciona la capacidad de leer datos de la fuente de cambios de Azure Cosmos DB y publicar estos datos en un tema de Kafka.

## <a name="prerequisites"></a>Requisitos previos

* Comience con la [configuración de la plataforma de Confluent](https://github.com/microsoft/kafka-connect-cosmosdb/blob/dev/doc/Confluent_Platform_Setup.md), ya que le proporciona un entorno completo con el que trabajar. Si no desea usar la plataforma de Confluent, debe instalar y configurar Zookeeper, Apache Kafka y Kafka Connect usted mismo. También deberá instalar y configurar los conectores de Azure Cosmos DB manualmente.
* [Guía de configuración](https://github.com/microsoft/kafka-connect-cosmosdb/blob/dev/doc/CosmosDB_Setup.md) para crear una cuenta y un contenedor de Azure Cosmos DB.
* Shell de Bash, que se ha probado en GitHub Codespaces, Mac, Ubuntu y Windows con WSL2. Este shell no funciona en Cloud Shell ni WSL1.
* Descarga de [Java 11+](https://www.oracle.com/java/technologies/javase-jdk11-downloads.html)
* Descarga de [Maven](https://maven.apache.org/download.cgi)

## <a name="install-the-source-connector"></a>Instalación del conector de origen

Si usa la [configuración recomendada de la plataforma de Confluent](https://github.com/microsoft/kafka-connect-cosmosdb/blob/dev/doc/Confluent_Platform_Setup.md), el conector de origen de Azure Cosmos DB se incluye en la instalación y puede omitir este paso.

De lo contrario, puede usar el archivo JAR de la [versión](https://github.com/microsoft/kafka-connect-cosmosdb/releases) más reciente e instalar el conector manualmente. Para más información, consulte estas [instrucciones](https://docs.confluent.io/current/connect/managing/install.html#install-connector-manually). También puede empaquetar un nuevo archivo JAR desde el código fuente:

```bash
# clone the kafka-connect-cosmosdb repo if you haven't done so already
git clone https://github.com/microsoft/kafka-connect-cosmosdb.git
cd kafka-connect-cosmosdb

# package the source code into a JAR file
mvn clean package

# include the following JAR file in Confluent Platform installation
ls target/*dependencies.jar
```

## <a name="create-a-kafka-topic"></a>Creación de un tema de Kafka

Cree un tema de Kafka mediante el centro de control de Confluent. En este escenario, vamos a crear un tema de Kafka llamado "apparels" y a escribir datos JSON insertados sin esquema en el tema. Para crear un tema en el centro de control, consulte la [documentación sobre cómo crear un tema de Kafka](https://docs.confluent.io/platform/current/quickstart/ce-docker-quickstart.html#step-2-create-ak-topics).

## <a name="create-the-source-connector"></a>Creación del conector de origen

### <a name="create-the-source-connector-in-kafka-connect"></a>Creación del conector de origen en Kafka Connect

Para crear el conector de origen de Azure Cosmos DB en Kafka Connect, use la siguiente configuración JSON. Asegúrese de reemplazar los valores de marcador de posición para las propiedades `connect.cosmos.connection.endpoint` y `connect.cosmos.master.key` que debería haber guardado de la guía de configuración de Azure Cosmos DB en los requisitos previos.

```json
{
  "name": "cosmosdb-source-connector",
  "config": {
    "connector.class": "com.azure.cosmos.kafka.connect.source.CosmosDBSourceConnector",
    "tasks.max": "1",
    "key.converter": "org.apache.kafka.connect.json.JsonConverter",
    "value.converter": "org.apache.kafka.connect.json.JsonConverter",
    "connect.cosmos.task.poll.interval": "100",
    "connect.cosmos.connection.endpoint": "https://<cosmosinstance-name>.documents.azure.com:443/",
    "connect.cosmos.master.key": "<cosmosdbprimarykey>",
    "connect.cosmos.databasename": "kafkaconnect",
    "connect.cosmos.containers.topicmap": "apparels#kafka",
    "connect.cosmos.offset.useLatest": false,
    "value.converter.schemas.enable": "false",
    "key.converter.schemas.enable": "false"
  }
}
```

Para más información sobre cada una de las propiedades de configuración anteriores, consulte la sección [Propiedades de configuración de origen](#source-configuration-properties). Una vez que haya rellenado todos los valores, guarde el archivo JSON en algún lugar de forma local. Puede usar este archivo para crear el conector mediante la API de REST.

#### <a name="create-connector-using-control-center"></a>Creación de un conector mediante el centro de control

Una opción sencilla para crear el conector es desde el portal del centro de control de Confluent. Siga la [guía de configuración de Confluent](https://docs.confluent.io/platform/current/quickstart/ce-docker-quickstart.html#step-3-install-a-ak-connector-and-generate-sample-data) para crear un conector desde el centro de control. Al realizar la configuración, en lugar de usar la opción `DatagenConnector`, use el icono `CosmosDBSourceConnector`. Al configurar el conector de origen, rellene los valores que haya rellenado en el archivo JSON.

Como alternativa, en la página de conectores, puede cargar el archivo JSON creado a partir de la sección anterior mediante la opción **Upload connector config file** (Cargar archivo de configuración del conector).

:::image type="content" source="./media/kafka-connector-source/upload-connector-config.png" alt-text="Carga de la configuración del conector.":::

#### <a name="create-connector-using-rest-api"></a>Creación de un conector mediante la API REST

Creación del conector de origen mediante la API REST de Connect

```bash
# Curl to Kafka connect service
curl -H "Content-Type: application/json" -X POST -d @<path-to-JSON-config-file> http://localhost:8083/connectors
```

## <a name="insert-document-into-azure-cosmos-db"></a>Inserción de un documento en Azure Cosmos DB

1. Inicie sesión en [Azure Portal](https://portal.azure.com/learn.docs.microsoft.com) y vaya a la cuenta de Azure Cosmos DB.
1. Abra la pestaña **Explorador de datos** y seleccione **Bases de datos**.
1. Abra la base de datos "kafkaconnect" y el contenedor "kafka" que creó anteriormente.
1. Para crear un nuevo documento JSON, en el panel de SQL API, expanda el contenedor "kafka", seleccione **Elementos** y,a continuación, seleccione **Nuevo elemento** en la barra de herramientas.
1. Ahora agregue un documento al contenedor con la estructura siguiente. Pegue el bloque JSON de ejemplo siguiente en la pestaña Elementos y sobrescriba el contenido actual:

   ``` json
 
   {
     "id": "2",
     "productId": "33218897",
     "category": "Women's Outerwear",
     "manufacturer": "Contoso",
     "description": "Black wool pea-coat",
     "price": "49.99",
     "shipping": {
       "weight": 2,
       "dimensions": {
         "width": 8,
         "height": 11,
         "depth": 3
       }
     }
   }
 
   ```

1. Seleccione **Guardar**.
1. Para confirmar que el documento se ha guardado, consulte la opción Elementos en el menú de la izquierda.

### <a name="confirm-data-written-to-kafka-topic"></a>Confirmación de que los datos se han escrito en el tema de Kafka

1. Abra la interfaz de usuario de temas de Kafka en `<http://localhost:9000>`.
1. Seleccione el tema de Kafka llamado "apparels" que ha creado.
1. Compruebe que el documento que insertó anteriormente en Azure Cosmos DB se muestre en el tema de Kafka.

### <a name="cleanup"></a>Limpieza

Para eliminar el conector desde el centro de control de Confluent, vaya al conector de origen que creó y seleccione el icono **Delete** (Eliminar).

:::image type="content" source="./media/kafka-connector-source/delete-source-connector.png" alt-text="Eliminación del conector desde el centro de Confluent":::

Como alternativa, utilice la API REST del conector:

```bash
# Curl to Kafka connect service
curl -X DELETE http://localhost:8083/connectors/cosmosdb-source-connector
```

Para eliminar el servicio Azure Cosmos DB creado y su grupo de recursos mediante la CLI de Azure, consulte estos [pasos](https://github.com/microsoft/kafka-connect-cosmosdb/blob/dev/doc/CosmosDB_Setup.md#cleanup).

## <a name="source-configuration-properties"></a>Propiedades de configuración de origen

Los siguientes valores se usan para configurar el conector de origen de Kafka. Estos valores de configuración determinan qué contenedor de Azure Cosmos DB se consume, los datos de los que se escriben los temas de Kafka y los formatos para serializar los datos. Para obtener un ejemplo con los valores predeterminados, consulte este [archivo de configuración](https://github.com/microsoft/kafka-connect-cosmosdb/blob/dev/src/docker/resources/source.example.json).

| Nombre | Tipo | Descripción | Obligatorio/opcional |
| :--- | :--- | :--- | :--- |
| connector.class | String | Nombre de clase del origen de Azure Cosmos DB. Se debe establecer en `com.azure.cosmos.kafka.connect.source.CosmosDBSourceConnector`. | Obligatorio |
| connect.cosmos.databasename | String | Nombre de la base de datos desde la que se va a leer. | Obligatorio |
| connect.cosmos.master.key | String | Clave principal de Azure Cosmos DB. | Requerido |
| connect.cosmos.connection.endpoint | Identificador URI | Punto de conexión de la cuenta. | Obligatorio |
| connect.cosmos.containers.topicmap | String | Tema separado por comas para la asignación de contenedores. Por ejemplo, topic1#coll1, topic2#coll2 | Requerido |
| connect.cosmos.messagekey.enabled | Boolean | Este valor representa si se debe establecer la clave de mensaje de Kafka. El valor predeterminado es `true`. | Requerido |
| connect.cosmos.messagekey.field | String | Use el valor del campo del documento como clave de mensaje. El valor predeterminado es `id`. | Requerido |
| connect.cosmos.offset.useLatest | Boolean |  Establezca el valor en `true` para usar el desplazamiento de origen más reciente. Establezca el valor en `false` para usar el desplazamiento registrado más antiguo. El valor predeterminado es `false`. | Requerido |
| connect.cosmos.task.poll.interval | Int | Intervalo para sondear el contenedor de la fuente de cambios en busca de cambios. | Requerido |
| key.converter | String | Formato de serialización para los datos clave escritos en el tema de Kafka. | Requerido |
| value.converter | String | Formato de serialización para los datos de valor escritos en el tema de Kafka. | Requerido |
| key.converter.schemas.enable | String | Establezca el valor en `true` si los datos de clave tienen un esquema insertado. | Optional |
| value.converter.schemas.enable | String | Establezca el valor en `true` si los datos de clave tienen un esquema insertado. | Optional |
| tasks.max | Int | Número máximo de tareas de origen de conectores. El valor predeterminado es `1`. | Optional |

## <a name="supported-data-types"></a>Tipos de datos admitidos

El conector de origen de Azure Cosmos DB convierte el documento JSON al esquema y admite los siguientes tipos de datos JSON:

| Tipo de datos JSON | Tipo de esquema |
| :--- | :--- |
| Array | Array |
| Boolean | Boolean | 
| Número | Float32<br>Float64<br>Int8<br>Int16<br>Int32<br>Int64|
| Null | String |
| Objeto (JSON)| Estructura|
| String | String |

## <a name="next-steps"></a>Pasos siguientes

* Kafka Connect para Azure Cosmos DB: [conector receptor](kafka-connector-sink.md)