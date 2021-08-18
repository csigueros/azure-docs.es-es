---
title: Migración de los datos de PostgreSQL a una cuenta de Cassandra API de Azure Cosmos DB mediante Apache Kafka
description: Aprenda a usar Kafka Connect para sincronizar datos de PostgreSQL en Cassandra API de Azure Cosmos DB en tiempo real.
author: abhirockzz
ms.service: cosmos-db
ms.subservice: cosmosdb-cassandra
ms.topic: how-to
ms.date: 01/05/2021
ms.author: abhishgu
ms.reviewer: abhishgu
ms.openlocfilehash: f2f6148d268a4a4b41a8b59c0751143e04cae95f
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/13/2021
ms.locfileid: "121780410"
---
# <a name="migrate-data-from-postgresql-to-azure-cosmos-db-cassandra-api-account-using-apache-kafka"></a>Migración de los datos de PostgreSQL a una cuenta de Cassandra API de Azure Cosmos DB mediante Apache Kafka
[!INCLUDE[appliesto-cassandra-api](../includes/appliesto-cassandra-api.md)]

Cassandra API en Azure Cosmos DB se ha convertido en una excelente opción para cargas de trabajo empresariales que se ejecutan en Apache Cassandra por varios motivos como, por ejemplo:

* **Importante ahorro de costos**: puede ahorrar costos con Azure Cosmos DB, como en el costo de las máquinas virtuales, del ancho de banda y de las licencias de Oracle aplicables. Además, no tiene que administrar los centros de datos, los servidores, el almacenamiento en SSD, las redes y los costos de electricidad.

* **Mejor escalabilidad y disponibilidad**: Evita los puntos únicos de error, lo que supone una mejor escalabilidad y disponibilidad para las aplicaciones.

* **No hay sobrecarga de administración y supervisión**: como servicio en la nube totalmente administrado, Azure Cosmos DB evita la sobrecarga de administrar y supervisar innumerables configuraciones.

[Kafka Connect](https://kafka.apache.org/documentation/#connect) es una plataforma para transmitir datos entre [Apache Kafka](https://kafka.apache.org/) y otros sistemas de forma escalable y confiable. Admite varios conectores comerciales, lo que significa que no necesita código personalizado para integrar sistemas externos con Apache Kafka.

En este artículo se muestra cómo usar una combinación de conectores de Kafka para configurar una canalización de datos para sincronizar continuamente registros desde una base de datos relacional como [PostgreSQL](https://www.postgresql.org/) en [Cassandra API de Azure Cosmos DB](cassandra-introduction.md).

## <a name="overview"></a>Información general

Aquí encontrará información general de alto nivel sobre el flujo completo que se presenta en este artículo.

Los datos de la tabla de PostgreSQL se insertarán en Apache Kafka mediante el [conector de Debezium PostgreSQL](https://debezium.io/documentation/reference/1.2/connectors/postgresql.html), que es un conector de **origen** de Kafka Connect. Las inserciones, actualizaciones o eliminaciones en los registros de la tabla de PostgreSQL se capturarán como eventos `change data` y se enviarán a los temas de Kafka. El [conector de Apache Kafka de DataStax](https://docs.datastax.com/en/kafka/doc/kafka/kafkaIntro.html) (conector del **receptor** de Kafka Connect) forma la segunda parte de la canalización. Sincronizará los eventos de datos modificados del tema Kafka con las tablas de Cassandra API de Azure Cosmos DB.

> [!NOTE]
> El uso de características específicas del conector de Apache Kafka de DataStax permite insertar datos en varias tablas. En este ejemplo, el conector nos ayudará a conservar los registros de datos modificados en dos tablas de Cassandra que pueden admitir diferentes requisitos de consulta.

## <a name="prerequisites"></a>Requisitos previos

* [Aprovisionar una cuenta de Cassandra API de Azure Cosmos DB](manage-data-dotnet.md#create-a-database-account)
* [Uso de cqlsh o shell hospedado para la validación](cassandra-support.md#hosted-cql-shell-preview)
* JDK 8 o posterior
* [Docker](https://www.docker.com/) (opcional)

## <a name="base-setup"></a>Configuración básica

### <a name="set-up-postgresql-database-if-you-havent-already"></a>Configure la base de datos de PostgreSQL si aún no lo ha hecho. 

Podría tratarse de una base de datos local existente o podría [descargar e instalar una](https://www.postgresql.org/download/) en el equipo local. También es posible usar un [contenedor de Docker](https://hub.docker.com/_/postgres).
[!INCLUDE [pull-image-include](../../../includes/pull-image-include.md)]

Para iniciar un contenedor:

```bash
docker run --rm -p 5432:5432 -e POSTGRES_PASSWORD=<enter password> postgres
```

Conéctese a la instancia de PostgreSQL con el cliente [`psql`](https://www.postgresql.org/docs/current/app-psql.html):

```bash
psql -h localhost -p 5432 -U postgres -W -d postgres
```

Cree una tabla para almacenar información de pedidos de ejemplo:

```sql
CREATE SCHEMA retail;

CREATE TABLE retail.orders_info (
    orderid SERIAL NOT NULL PRIMARY KEY,
    custid INTEGER NOT NULL,
    amount INTEGER NOT NULL,
    city VARCHAR(255) NOT NULL,
    purchase_time VARCHAR(40) NOT NULL
);
```

### <a name="using-the-azure-portal-create-the-cassandra-keyspace-and-the-tables-required-for-the-demo-application"></a>Mediante Azure Portal, cree el espacio de claves de Cassandra y las tablas necesarias para la aplicación de demostración.

> [!NOTE]
> Use los mismos nombres de espacio de claves y tabla que se indican a continuación.

```sql
CREATE KEYSPACE retail WITH REPLICATION = {'class' : 'NetworkTopologyStrategy', 'datacenter1' : 1};

CREATE TABLE retail.orders_by_customer (order_id int, customer_id int, purchase_amount int, city text, purchase_time timestamp, PRIMARY KEY (customer_id, purchase_time)) WITH CLUSTERING ORDER BY (purchase_time DESC) AND cosmosdb_cell_level_timestamp=true AND cosmosdb_cell_level_timestamp_tombstones=true AND cosmosdb_cell_level_timetolive=true;

CREATE TABLE retail.orders_by_city (order_id int, customer_id int, purchase_amount int, city text, purchase_time timestamp, PRIMARY KEY (city,order_id)) WITH cosmosdb_cell_level_timestamp=true AND cosmosdb_cell_level_timestamp_tombstones=true AND cosmosdb_cell_level_timetolive=true;
```

### <a name="setup-apache-kafka"></a>Instalación de Apache Kafka 

En este artículo se usa un clúster local, pero puede elegir cualquier otra opción. [Descargue Kafka](https://kafka.apache.org/downloads), descomprímalo e inicie el clúster de Zookeeper y Kafka.

```bash
cd <KAFKA_HOME>/bin

#start zookeeper
bin/zookeeper-server-start.sh config/zookeeper.properties

#start kafka (in another terminal)
bin/kafka-server-start.sh config/server.properties
```

### <a name="setup-connectors"></a>Instalación de los conectores

Instale el conector de Apache Kafka de DataStax y de PostgreSQL de Debezium. Descargue el archivo del complemento del conector de PostgreSQL de Debezium. Por ejemplo, para descargar la versión 1.3.0 del conector (la más reciente en el momento de redactar este artículo), use [este vínculo](https://repo1.maven.org/maven2/io/debezium/debezium-connector-postgres/1.3.0.Final/debezium-connector-postgres-1.3.0.Final-plugin.tar.gz). Descargue el conector de Apache Kafka de DataStax desde [este vínculo](https://downloads.datastax.com/#akc).

Descomprima los archivos del conector y copie los archivos JAR en [plugin.path de Kafka Connect](https://kafka.apache.org/documentation/#connectconfigs).


```bash
cp <path_to_debezium_connector>/*.jar <KAFKA_HOME>/libs
cp <path_to_cassandra_connector>/*.jar <KAFKA_HOME>/libs
```

> Para obtener más información, consulte la documentación de [Debezium](https://debezium.io/documentation/reference/1.2/connectors/postgresql.html#postgresql-deploying-a-connector) y [DataStax](https://docs.datastax.com/en/kafka/doc/).

## <a name="configure-kafka-connect-and-start-data-pipeline"></a>Configuración de la canalización de datos de inicio y de Kafka Connect

### <a name="start-kafka-connect-cluster"></a>Inicio del clúster de Kafka Connect

```bash
cd <KAFKA_HOME>/bin
./connect-distributed.sh ../config/connect-distributed.properties
```

### <a name="start-postgresql-connector-instance"></a>Inicio de la instancia del conector de PostgreSQL

Guarde la configuración del conector (JSON) en un ejemplo de archivo `pg-source-config.json`.

```json
{
    "name": "pg-orders-source",
    "config": {
        "connector.class": "io.debezium.connector.postgresql.PostgresConnector",
        "database.hostname": "localhost",
        "database.port": "5432",
        "database.user": "postgres",
        "database.password": "password",
        "database.dbname": "postgres",
        "database.server.name": "myserver",
        "plugin.name": "wal2json",
        "table.include.list": "retail.orders_info",
        "value.converter": "org.apache.kafka.connect.json.JsonConverter"
    }
}
```

Para iniciar la instancia del conector de PostgreSQL:

```bash
curl -X POST -H "Content-Type: application/json" --data @pg-source-config.json http://localhost:8083/connectors
```

> [!NOTE]
> Para eliminarla, puede usar: `curl -X DELETE http://localhost:8083/connectors/pg-orders-source`


### <a name="insert-data"></a>Insertar datos

La tabla `orders_info` contiene detalles de pedidos como el identificador de pedido, el identificador de cliente, la ciudad, etc. Rellene la tabla con datos aleatorios mediante los siguientes datos SQL.

```sql
insert into retail.orders_info (
    custid, amount, city, purchase_time
)
select
    random() * 10000 + 1,
    random() * 200,
    ('{New Delhi,Seattle,New York,Austin,Chicago,Cleveland}'::text[])[ceil(random()*3)],
    NOW() + (random() * (interval '1 min'))
from generate_series(1, 10) s(i);
```

Debe insertar 10 registros en la tabla. Asegúrese de actualizar el número de registros en `generate_series(1, 10)` a continuación, según el ejemplo de requisitos; para insertar `100` registros, use `generate_series(1, 100)`.

Para confirmar:

```bash
select * from retail.orders_info;
```

Compruebe los eventos de la captura de datos modificados en el tema de Kafka.

> [!NOTE]
> Tenga en cuenta que el nombre del tema es `myserver.retail.orders_info`, según la [convención del conector](https://debezium.io/documentation/reference/1.3/connectors/postgresql.html#postgresql-topic-names).

```bash
cd <KAFKA_HOME>/bin

./kafka-console-consumer.sh --topic myserver.retail.orders_info --bootstrap-server localhost:9092 --from-beginning
```

Debería ver los eventos de datos modificados en formato JSON.

### <a name="start-datastax-apache-kafka-connector-instance"></a>Inicio de la instancia del conector de Apache Kafka de DataStax

Guarde la configuración del conector (JSON) en un ejemplo de archivo, `cassandra-sink-config.json`, y actualice las propiedades según su entorno.

```json
{
    "name": "kafka-cosmosdb-sink",
    "config": {
        "connector.class": "com.datastax.oss.kafka.sink.CassandraSinkConnector",
        "tasks.max": "1",
        "topics": "myserver.retail.orders_info",
        "contactPoints": "<Azure Cosmos DB account name>.cassandra.cosmos.azure.com",
        "loadBalancing.localDc": "<Azure Cosmos DB region e.g. Southeast Asia>",
        "datastax-java-driver.advanced.connection.init-query-timeout": 5000,
        "ssl.hostnameValidation": true,
        "ssl.provider": "JDK",
        "ssl.keystore.path": "<path to JDK keystore path e.g. <JAVA_HOME>/jre/lib/security/cacerts>",
        "ssl.keystore.password": "<keystore password: it is 'changeit' by default>",
        "port": 10350,
        "maxConcurrentRequests": 500,
        "maxNumberOfRecordsInBatch": 32,
        "queryExecutionTimeout": 30,
        "connectionPoolLocalSize": 4,
        "auth.username": "<Azure Cosmos DB user name (same as account name)>",
        "auth.password": "<Azure Cosmos DB password>",
        "topic.myserver.retail.orders_info.retail.orders_by_customer.mapping": "order_id=value.orderid, customer_id=value.custid, purchase_amount=value.amount, city=value.city, purchase_time=value.purchase_time",
        "topic.myserver.retail.orders_info.retail.orders_by_city.mapping": "order_id=value.orderid, customer_id=value.custid, purchase_amount=value.amount, city=value.city, purchase_time=value.purchase_time",
        "key.converter": "org.apache.kafka.connect.storage.StringConverter",
        "transforms": "unwrap",
        "transforms.unwrap.type": "io.debezium.transforms.ExtractNewRecordState",
        "offset.flush.interval.ms": 10000
    }
}
```

Para iniciar la instancia del conector:

```bash
curl -X POST -H "Content-Type: application/json" --data @cassandra-sink-config.json http://localhost:8083/connectors
```

El conector debe entrar en acción, y la canalización completa de PostgreSQL a Azure Cosmos DB estará operativa.

### <a name="query-azure-cosmos-db"></a>Consulta de Azure Cosmos DB

Compruebe las tablas de Cassandra en Azure Cosmos DB. A continuación, se indican algunas de las consultas que puede probar:

```sql
select count(*) from retail.orders_by_customer;
select count(*) from retail.orders_by_city;

select * from retail.orders_by_customer;
select * from retail.orders_by_city;

select * from retail.orders_by_city where city='Seattle';
select * from retail.orders_by_customer where customer_id = 10;
```

Puede continuar insertando más datos en PostgreSQL y confirmar que los registros están sincronizados con Azure Cosmos DB.

## <a name="next-steps"></a>Pasos siguientes

* [Integración de Apache Kafka y Cassandra API de Azure Cosmos DB mediante Kafka Connect](kafka-connect.md)
* [Integración de Apache Kafka Connect en Azure Event Hubs (versión preliminar) con Debezium para operaciones de captura de datos modificados](../../event-hubs/event-hubs-kafka-connect-debezium.md)
* [Migración de los datos de Oracle a Cassandra API de Azure Cosmos DB mediante Blitzz](oracle-migrate-cosmos-db-blitzz.md)
* [Aprovisionamiento del rendimiento en contenedores y bases de datos](../set-throughput.md) 
* [Procedimientos recomendados de las claves de partición](../partitioning-overview.md#choose-partitionkey)
* [Estimación de RU/s mediante Capacity Planner de Azure Cosmos DB](../estimate-ru-with-capacity-planner.md)

