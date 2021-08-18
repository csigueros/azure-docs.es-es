---
title: Indexación en una cuenta de Cassandra API de Azure Cosmos DB
description: Obtenga información sobre cómo funciona la indexación secundaria en una cuenta de Cassandra API de Azure Cosmos DB.
author: TheovanKraay
ms.service: cosmos-db
ms.subservice: cosmosdb-cassandra
ms.topic: conceptual
ms.date: 04/04/2020
ms.author: thvankra
ms.reviewer: sngun
ms.openlocfilehash: 62de11b153b0a8da83f550cec9e64b03b0dbd160
ms.sourcegitcommit: d9a2b122a6fb7c406e19e2af30a47643122c04da
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 07/24/2021
ms.locfileid: "114668120"
---
# <a name="secondary-indexing-in-azure-cosmos-db-cassandra-api"></a>Indexación secundaria en Cassandra API de Azure Cosmos DB
[!INCLUDE[appliesto-cassandra-api](includes/appliesto-cassandra-api.md)]

Cassandra API de Azure Cosmos DB aprovecha la infraestructura de indexación subyacente para exponer la intensidad de indexación inherente a la plataforma. Sin embargo, a diferencia de SQL API, Cassandra API de Azure Cosmos DB no indexa todos los atributos de forma predeterminada. En su lugar, admite la indexación secundaria para crear un índice sobre determinados atributos, que se comporta de la misma manera que Apache Cassandra.  

En general, no se recomienda ejecutar consultas de filtro en las columnas que no tienen particiones. Debe usar la sintaxis ALLOW FILTERING explícitamente, lo que da como resultado una operación que podría no tener un buen rendimiento. En Azure Cosmos DB puede ejecutar estas consultas sobre atributos de baja cardinalidad, ya que se dividen entre las particiones para recuperar los resultados.

No se recomienda crear un índice en una columna que se actualiza con frecuencia. Es prudente crear un índice al definir la tabla. Esto garantiza que los datos y los índices se encuentran en un estado coherente. En el caso de que cree un nuevo índice sobre los datos existentes, actualmente no puede realizar un seguimiento de los cambios del progreso del índice de la tabla. Si necesita realizar un seguimiento del progreso de esta operación, tendrá que solicitar el cambio de progreso mediante una [incidencia de soporte técnico](../azure-portal/supportability/how-to-create-azure-support-request.md).


> [!NOTE]
> No se admiten índices secundarios en los objetos siguientes:
> - tipos de datos como tipos de colección inmovilizados, decimales y tipos Variant.
> - Columnas estáticas
> - Claves de agrupación en clústeres

## <a name="indexing-example"></a>Ejemplo de indexación

En primer lugar, cree un espacio de claves y una tabla de ejemplo mediante la ejecución de los siguientes comandos en el símbolo del sistema de CQL:

```shell
CREATE KEYSPACE sampleks WITH REPLICATION = {'class' : 'SimpleStrategy'};
CREATE TABLE sampleks.t1(user_id int PRIMARY KEY, lastname text) WITH cosmosdb_provisioned_throughput=400;
``` 

A continuación, inserte los datos de usuario de ejemplo con los siguientes comandos:

```shell
insert into sampleks.t1(user_id,lastname) values (1, 'nishu');
insert into sampleks.t1(user_id,lastname) values (2, 'vinod');
insert into sampleks.t1(user_id,lastname) values (3, 'bat');
insert into sampleks.t1(user_id,lastname) values (5, 'vivek');
insert into sampleks.t1(user_id,lastname) values (6, 'siddhesh');
insert into sampleks.t1(user_id,lastname) values (7, 'akash');
insert into sampleks.t1(user_id,lastname) values (8, 'Theo');
insert into sampleks.t1(user_id,lastname) values (9, 'jagan');
```

Si intenta ejecutar la instrucción siguiente, se producirá un error que le pedirá que use la opción `ALLOW FILTERING`: 

```shell
select user_id, lastname from sampleks.t1 where lastname='nishu';
``` 

Aunque Cassandra API admite la opción ALLOW FILTERING, como se mencionó en la sección anterior, no se recomienda. En su lugar, debe crear un índice, tal y como se muestra en el ejemplo siguiente:

```shell
CREATE INDEX ON sampleks.t1 (lastname);
```
Después de crear un índice en el campo "lastname", ahora puede ejecutar la consulta anterior correctamente. Con Cassandra API de Azure Cosmos DB, no es necesario proporcionar un nombre de índice. Se utiliza un índice predeterminado con el formato `tablename_columnname_idx`. Por ejemplo, ` t1_lastname_idx` es el nombre del índice de la tabla anterior.

## <a name="dropping-the-index"></a>Eliminación del índice 
Debe saber cuál es el nombre del índice para eliminarlo. Ejecute el comando `desc schema` para obtener la descripción de la tabla. La salida de este comando incluye el nombre del índice con el formato `CREATE INDEX tablename_columnname_idx ON keyspacename.tablename(columnname)`. Después, puede usar el nombre del índice para eliminar el índice, tal y como se muestra en el ejemplo siguiente:

```shell
drop index sampleks.t1_lastname_idx;
```

## <a name="next-steps"></a>Pasos siguientes
* Más información sobre cómo funciona la [indexación automática](index-overview.md) en Azure Cosmos DB.
* [Características de Apache Cassandra admitidas por Cassandra API de Azure Cosmos DB](cassandra-support.md)
