---
title: Integración de Apache Kafka y Cassandra API de Azure Cosmos DB mediante Kafka Connect
description: Obtenga información sobre cómo ingerir datos de Kafka en Cassandra API de Azure Cosmos DB mediante el conector de Apache Kafka de DataStax.
author: abhirockzz
ms.service: cosmos-db
ms.subservice: cosmosdb-cassandra
ms.topic: how-to
ms.date: 12/14/2020
ms.author: abhishgu
ms.reviewer: abhishgu
ms.openlocfilehash: 8dcd097752f04924517be08eab964eeb4362267d
ms.sourcegitcommit: f3b930eeacdaebe5a5f25471bc10014a36e52e5e
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 06/16/2021
ms.locfileid: "112240425"
---
# <a name="ingest-data-from-apache-kafka-into-azure-cosmos-db-cassandra-api-using-kafka-connect"></a>Ingesta de datos de Apache Kafka en Cassandra API de Azure Cosmos DB mediante Kafka Connect
[!INCLUDE[appliesto-cassandra-api](includes/appliesto-cassandra-api.md)]

Las aplicaciones de Cassandra existentes pueden funcionar fácilmente con [Cassandra API de Azure Cosmos DB](cassandra-introduction.md) debido a su [compatibilidad con el controlador CQLv4](https://cassandra.apache.org/doc/latest/getting_started/drivers.html?highlight=driver). Puede aprovechar esta capacidad para integrarse con plataformas de streaming, como [Apache Kafka](https://kafka.apache.org/), y poner los datos en Azure Cosmos DB.

Los datos de Apache Kafka (temas) solo son útiles cuando se utilizan en otras aplicaciones o se ingieren en otros sistemas. Es posible compilar una solución con las API de [productor o consumidor de Kafka](https://kafka.apache.org/documentation/#api) [mediante un SDK de cliente e idioma de su elección](https://cwiki.apache.org/confluence/display/KAFKA/Clients). Kafka Connect proporciona una solución alternativa. Es una plataforma para transmitir datos entre Apache Kafka y otros sistemas de forma escalable y confiable. Como Kafka Connect es compatible con los conectores de estantería que incluye Cassandra, no es necesario escribir código personalizado para integrar Kafka con Cassandra API de Azure Cosmos DB. 

En este artículo, vamos a usar el [conector de Apache Kafka de DataStax](https://docs.datastax.com/en/kafka/doc/kafka/kafkaIntro.html) de código abierto, que funciona sobre el marco de Kafka Connect para ingerir registros de un tema de Kafka en filas de una o varias tablas de Cassandra. En el ejemplo se proporciona una configuración reutilizable mediante Docker Compose. Esto es bastante conveniente, ya que le permite arrancar todos los componentes necesarios localmente con un solo comando. Estos componentes incluyen Kafka, Zookeeper, trabajo de Kafka Connect y la aplicación del generador de datos de ejemplo.

A continuación, se muestra un desglose de los componentes y sus definiciones de servicio: puede hacer referencia al archivo `docker-compose` [en el repositorio de GitHub](https://github.com/Azure-Samples/cosmosdb-cassandra-kafka/blob/main/docker-compose.yaml).

- Kafka y Zookeeper usan imágenes de [debezium](https://hub.docker.com/r/debezium/kafka/).
- Para ejecutarse como un contenedor de Docker, el conector de Apache Kafka de DataStax se ha simulado mediante "bake" por encima de una imagen de Docker existente: [debezium/connect-base](https://github.com/debezium/docker-images/tree/master/connect-base/1.2). Esta imagen incluye una instalación de Kafka y sus bibliotecas de Kafka Connect, por lo que es realmente conveniente agregar conectores personalizados. Puede hacer referencia al [Dockerfile](https://github.com/Azure-Samples/cosmosdb-cassandra-kafka/blob/main/connector/Dockerfile).
- El servicio `data-generator` proporciona los datos generados de forma aleatoria (JSON) en el tema `weather-data` de Kafka. Puede hacer referencia al código y a `Dockerfile` en el [repositorio de GitHub](https://github.com/Azure-Samples/cosmosdb-cassandra-kafka/blob/main/data-generator/).

## <a name="prerequisites"></a>Requisitos previos

* [Aprovisionar una cuenta de Cassandra API de Azure Cosmos DB](create-cassandra-dotnet.md#create-a-database-account)

* [Uso de cqlsh o shell hospedado para la validación](cassandra-support.md#hosted-cql-shell-preview)

* Instalar [Docker](https://docs.docker.com/get-docker/) y [Docker Compose](https://docs.docker.com/compose/install)

## <a name="create-keyspace-tables-and-start-the-integration-pipeline"></a>Creación del espacio de claves y las tablas e inicio de la canalización de integración

Mediante Azure Portal, cree el espacio de claves de Cassandra y las tablas necesarias para la aplicación de demostración.

> [!NOTE]
> Use los mismos nombres de espacio de claves y tabla que se indican a continuación.

```sql
CREATE KEYSPACE weather WITH REPLICATION = {'class' : 'NetworkTopologyStrategy', 'datacenter1' : 1};

CREATE TABLE weather.data_by_state (station_id text, temp int, state text, ts timestamp, PRIMARY KEY (state, ts)) WITH CLUSTERING ORDER BY (ts DESC) AND cosmosdb_cell_level_timestamp=true AND cosmosdb_cell_level_timestamp_tombstones=true AND cosmosdb_cell_level_timetolive=true;

CREATE TABLE weather.data_by_station (station_id text, temp int, state text, ts timestamp, PRIMARY KEY (station_id, ts)) WITH CLUSTERING ORDER BY (ts DESC) AND cosmosdb_cell_level_timestamp=true AND cosmosdb_cell_level_timestamp_tombstones=true AND cosmosdb_cell_level_timetolive=true;
```

Clone el repositorio de GitHub:

```bash
git clone https://github.com/Azure-Samples/cosmosdb-cassandra-kafka
cd cosmosdb-cassandra-kafka
```

Inicie todos los servicios:

```shell
docker-compose --project-name kafka-cosmos-cassandra up --build
```

> [!NOTE]
> La descarga y el inicio de los contenedores puede tardar un poco, pero es un proceso único.

Para confirmar si se han iniciado todos los contenedores:

```shell
docker-compose -p kafka-cosmos-cassandra ps
```

La aplicación del generador de datos comenzará a bombear los datos en el tema `weather-data` de Kafka. También puede realizar una comprobación rápida para confirmar. Eche un vistazo al contenedor de Docker que ejecuta el trabajo de Kafka Connect:


```bash
docker exec -it kafka-cosmos-cassandra_cassandra-connector_1 bash
```

Una vez colocado en el shell de contenedor, solo tiene que iniciar el proceso de consumidor de la consola de Kafka habitual y debería ver aparecer los datos meteorológicos (en formato JSON).

```bash
cd ../bin
./kafka-console-consumer.sh --bootstrap-server kafka:9092 --topic weather-data
```

## <a name="cassandra-sink-connector-setup"></a>Configuración del conector del receptor de Cassandra

Copie el contenido de JSON siguiente en un archivo (puede asignarle el nombre `cassandra-sink-config.json`). Deberá actualizarlo según la configuración y en el resto de esta sección se proporcionará una guía sobre este tema.

```json
{
    "name": "kafka-cosmosdb-sink",
    "config": {
        "connector.class": "com.datastax.oss.kafka.sink.CassandraSinkConnector",
        "tasks.max": "1",
        "topics": "weather-data",
        "contactPoints": "<cosmos db account name>.cassandra.cosmos.azure.com",
        "port": 10350,
        "loadBalancing.localDc": "<cosmos db region e.g. Southeast Asia>",
        "auth.username": "<enter username for cosmosdb account>",
        "auth.password": "<enter password for cosmosdb account>",
        "ssl.hostnameValidation": true,
        "ssl.provider": "JDK",
        "ssl.keystore.path": "/etc/alternatives/jre/lib/security/cacerts/",
        "ssl.keystore.password": "changeit",
        "datastax-java-driver.advanced.connection.init-query-timeout": 5000,
        "maxConcurrentRequests": 500,
        "maxNumberOfRecordsInBatch": 32,
        "queryExecutionTimeout": 30,
        "connectionPoolLocalSize": 4,
        "topic.weather-data.weather.data_by_state.mapping": "station_id=value.stationid, temp=value.temp, state=value.state, ts=value.created",
        "topic.weather-data.weather.data_by_station.mapping": "station_id=value.stationid, temp=value.temp, state=value.state, ts=value.created",
        "key.converter": "org.apache.kafka.connect.storage.StringConverter",
        "value.converter": "org.apache.kafka.connect.json.JsonConverter",
        "value.converter.schemas.enable": false,
        "offset.flush.interval.ms": 10000
    }
}
```

A continuación, se muestra un resumen de los atributos:

**Conectividad básica**

- `contactPoints`: escriba el punto de contacto para Cassandra de Cosmos DB.
- `loadBalancing.localDc`: escriba la región de la cuenta de Cosmos DB, por ejemplo, Sudeste Asiático.
- `auth.username`: escriba el nombre de usuario.
- `auth.password`: escriba la contraseña.
- `port`: escriba el valor del puerto (es decir, `10350`, no `9042`. Déjelo tal cual).

**Configuración de SSL**

Azure Cosmos DB exige [conectividad segura a través de SSL](database-security.md) y el conector de Kafka Connect también admite SSL.

- `ssl.keystore.path`: ruta de acceso al almacén de claves de JDK en el contenedor: `/etc/alternatives/jre/lib/security/cacerts/`.
- `ssl.keystore.password`: contraseña del almacén de claves de JDK (predeterminada).
- `ssl.hostnameValidation`: se activa la validación del nombre de host del nodo.
- `ssl.provider`: `JDK` se utiliza como proveedor de SSL.

**Parámetros genéricos**

- `key.converter`: Usamos el convertidor de cadenas `org.apache.kafka.connect.storage.StringConverter`.
- `value.converter`: dado que los datos de los temas de Kafka son JSON, usamos `org.apache.kafka.connect.json.JsonConverter`.
- `value.converter.schemas.enable`: Dado que nuestra carga de JSON no tiene un esquema asociado (para fines de la aplicación de demostración), debemos establecer este atributo en `false` para indicar a Kafka Connect que no busque un esquema. Si no lo hace, se producirán errores.

### <a name="install-the-connector"></a>Instalación del conector

Instale el conector mediante el punto de conexión REST de Kafka Connect:

```shell
curl -X POST -H "Content-Type: application/json" --data @cassandra-sink-config.json http://localhost:8083/connectors
```

Para comprobar el estado:

```
curl http://localhost:8080/connectors/kafka-cosmosdb-sink/status
```

Si todo va bien, el conector debería empezar a funcionar. Debe autenticarse en Azure Cosmos DB y comenzar a ingerir datos del tema de Kafka (`weather-data`) en tablas de Cassandra: `weather.data_by_state` y `weather.data_by_station`.

Ahora puede consultar los datos de las tablas. Diríjase a Azure Portal, abra el shell de CQL hospedado para la cuenta de Azure Cosmos DB.

:::image type="content" source="./media/cassandra-kafka-connect/cqlsh.png" alt-text="Abrir CQLSH":::

## <a name="query-data-from-azure-cosmos-db"></a>Consulta de datos de Azure Cosmos DB

Compruebe las tablas `data_by_state` y `data_by_station`. Estas son algunas consultas de ejemplo para comenzar:

```sql
select * from weather.data_by_state where state = 'state-1';
select * from weather.data_by_state where state IN ('state-1', 'state-2');
select * from weather.data_by_state where state = 'state-3' and ts > toTimeStamp('2020-11-26');

select * from weather.data_by_station where station_id = 'station-1';
select * from weather.data_by_station where station_id IN ('station-1', 'station-2');
select * from weather.data_by_station where station_id IN ('station-2', 'station-3') and ts > toTimeStamp('2020-11-26');
```

## <a name="clean-up-resources"></a>Limpieza de recursos

[!INCLUDE [cosmosdb-delete-resource-group](includes/cosmos-db-delete-resource-group.md)]

## <a name="next-steps"></a>Pasos siguientes

* [Aprovisionamiento del rendimiento en contenedores y bases de datos](set-throughput.md) 
* [Procedimientos recomendados de las claves de partición](partitioning-overview.md#choose-partitionkey)
* [Estimación de RU/s mediante Capacity Planner de Azure Cosmos DB](estimate-ru-with-capacity-planner.md)
