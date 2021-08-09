---
title: Elección de una API en Azure Cosmos DB
description: Aprenda cómo elegir entre las API SQL/Core, MongoDB, Cassandra, Gremlin y Table en Azure Cosmos DB en función de los requisitos de carga de trabajo.
author: SnehaGunda
ms.author: sngun
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 06/04/2021
ms.openlocfilehash: a4873662578dee69d4f7fe16618101a732129f58
ms.sourcegitcommit: 832e92d3b81435c0aeb3d4edbe8f2c1f0aa8a46d
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 06/07/2021
ms.locfileid: "111561810"
---
# <a name="choose-an-api-in-azure-cosmos-db"></a>Elección de una API en Azure Cosmos DB

Azure Cosmos DB es una base de datos NoSQL totalmente administrada para el desarrollo de aplicaciones modernas. Azure Cosmos DB le libera de la administración de bases de datos con administración, actualizaciones y revisiones automáticas. También controla la administración de la capacidad con opciones de escalado automático y sin servidor rentables que responden a las necesidades de la aplicación para hacer coincidir la capacidad con la demanda.

## <a name="apis-in-azure-cosmos-db"></a>Las API en Azure Cosmos DB

Azure Cosmos DB ofrece varias API de base de datos, incluida la API Core (SQL), API para MongoDB, Cassandra API, API de Gremlin y Table API. Con estas API, puede modelar datos reales mediante documentos, clave-valor, grafos y modelos de datos de familia de columnas. Estas API permiten que las aplicaciones traten Azure Cosmos DB como si fueran otras tecnologías de bases de datos, sin la sobrecarga de los enfoques de administración y escalado. Con estas API, Azure Cosmos DB le ayuda a usar los ecosistemas, las herramientas y las aptitudes que ya tiene para el modelado y las consultas de datos.

Todas las API ofrecen garantías de escalado automático del rendimiento y del almacenamiento, flexibilidad y rendimiento. No hay ninguna API que sea la mejor, y puede elegir cualquiera de las API para compilar la aplicación. Este artículo le ayudará a elegir una API en función de los requisitos de la carga de trabajo y del equipo.

## <a name="considerations-when-choosing-an-api"></a>Consideraciones al elegir una API

La API Core(SQL) es nativa de Azure Cosmos DB.

Las API para MongoDB, Cassandra, Gremlin y Table implementan el protocolo de conexión de los motores de base de datos de código abierto. Estas API son más adecuadas si se cumplen las condiciones siguientes:

* Si tiene aplicaciones existentes de MongoDB, Cassandra o Gremlin.
* Si no quiere reescribir toda la capa de acceso a datos.
* Si quiere usar el ecosistema para desarrolladores de código abierto, los controladores de cliente, la experiencia y los recursos de su base de datos.
* Si quiere usar las características clave de Azure Cosmos DB, como la distribución global, el escalado elástico del rendimiento y del almacenamiento, el rendimiento, la baja latencia, la capacidad de ejecutar cargas de trabajo transaccionales y analíticas, y usar una plataforma totalmente administrada.
* Si está desarrollando aplicaciones modernizadas en un entorno de varias nubes.

Puede compilar nuevas aplicaciones con estas API o migrar los datos existentes. Para ejecutar las aplicaciones migradas, cambie la cadena de conexión de la aplicación y siga con la ejecución como antes. Al migrar aplicaciones existentes, asegúrese de evaluar la compatibilidad de estas API con las distintas características.

En función de la carga de trabajo, debe elegir la API que se ajuste a sus necesidades. En la imagen siguiente se muestra un gráfico de flujo sobre cómo elegir la API adecuada al compilar nuevas aplicaciones o migrar aplicaciones existentes a Azure Cosmos DB:

:::image type="content" source="./media/choose-api/choose-api-decision-tree.png" alt-text="Árbol de decisión para elegir una API en Azure Cosmos DB." lightbox="./media/choose-api/choose-api-decision-tree.png":::

## <a name="coresql-api"></a>API Core (SQL)

Esta API almacena los datos en formato de documento. Ofrece la mejor experiencia integral, ya que se tiene control total sobre la interfaz, el servicio y las bibliotecas cliente del SDK. Toda nueva característica que se implantada en Azure Cosmos DB primero está disponible en las cuentas de la API SQL. Las cuentas de la API SQL en Azure Cosmos DB brindan compatibilidad para consultar elementos mediante la sintaxis del Lenguaje de consulta estructurado (SQL), uno de los lenguajes de consulta más conocidos y populares para consultar objetos JSON. Para más información, consulte [Introducción a las consultas SQL](sql-query-getting-started.md).

Si va a migrar desde otras bases de datos, como Oracle, DynamoDB, HBase, etc., y quiere usar las tecnologías modernizadas para compilar las aplicaciones, la API SQL es la opción recomendada. La API SQL admite análisis y ofrece aislamiento de rendimiento entre cargas de trabajo operativas y analíticas.

## <a name="api-for-mongodb"></a>API para MongoDB

Esta API almacena los datos en una estructura de documentos, mediante el formato BSON. Es compatible con el protocolo de conexión de MongoDB; sin embargo, no usa ningún código nativo relacionado con MongoDB. Esta API es una excelente opción si busca usar el ecosistema más amplio y las aptitudes de MongoDB, sin poner en peligro el uso de características de Azure Cosmos DB, como el escalado, la alta disponibilidad, la replicación geográfica, varias ubicaciones de escritura, la administración automática y transparente de particiones, la replicación transparente entre almacenes operativos y analíticos, etc.

Para usar las aplicaciones de MongoDB existentes con la API para MongoDB, simplemente cambie la cadena de conexión. Puede mover los datos existentes mediante herramientas nativas de MongoDB, como mongodump y mongorestore, o con nuestra herramienta Azure Database Migration. Las herramientas, como el shell de MongoDB, [MongoDB Compass](mongodb-compass.md) y [Robo3T](mongodb-robomongo.md), pueden ejecutar consultas y trabajar con datos como lo hacen con MongoDB nativo.

La API para MongoDB es compatible con las versiones 4.0, 3.6 y 3.2 del servidor de MongoDB. Se recomienda la versión 4.0 del servidor, ya que ofrece el mejor rendimiento y una compatibilidad completa con las características. Para más información, consulte el artículo [API para MongoDB](mongodb-introduction.md).

## <a name="cassandra-api"></a>Cassandra API

Esta API almacena los datos en un esquema orientado a columnas. Apache Cassandra ofrece un enfoque de escalado horizontal y altamente distribuido para almacenar grandes volúmenes de datos, al tiempo que ofrece un enfoque flexible para un esquema orientado a columnas. Cassandra API en Azure Cosmos DB se alinea con esta filosofía relativa a las bases de datos NoSQL distribuidas. Cassandra API es compatible con el protocolo de conexión con Apache Cassandra. Debe considerar la posibilidad de usar Cassandra API si quiere aprovechar la elasticidad y la naturaleza totalmente administrada de Azure Cosmos DB, y seguir usando la mayoría de las características, herramientas y el ecosistema nativos de Apache Cassandra. Esto significa que, con Cassandra API, no es necesario administrar el sistema operativo, VM Java, el recolector de elementos no utilizados, el rendimiento de lectura y escritura, los nodos, los clústeres, etc.

Puede usar controladores de cliente de Apache Cassandra para conectarse a Cassandra API. Cassandra API permite interactuar con los datos mediante Cassandra Query Language (CQL), y herramientas como el shell de CQL, controladores de cliente de Cassandra con los que ya está familiarizado. Actualmente, Cassandra API solo admite escenarios OLTP. Con Cassandra API, también puede usar las características únicas de Azure Cosmos DB, como la fuente de cambios. Para más información, consulte el artículo [Cassandra API](cassandra-introduction.md).

## <a name="gremlin-api"></a>API de Gremlin

Esta API permite a los usuarios realizar consultas de grafos y almacenar datos como bordes y vértices. Use esta API para escenarios que implican datos dinámicos, datos con relaciones complejas, datos demasiado complejos como para modelarse con bases de datos relacionales, y si quiere usar el ecosistema y las aptitudes existentes de Gremlin. La API de Gremlin de Azure Cosmos DB combina la potencia de los algoritmos de base de datos de grafo con una infraestructura administrada altamente escalable. Proporciona una solución única y flexible para los problemas más comunes de datos asociados a la falta de flexibilidad y enfoques relacionales. Actualmente, la API de Gremlin solo admite escenarios OLTP.

La API de Gremlin de Azure Cosmos DB se basa en el marco de trabajo de computación de grafos [Apache TinkerPop](https://tinkerpop.apache.org/). La API de Gremlin usa el mismo lenguaje de consulta de Graph para ingerir y consultar datos. Usa la estrategia de particiones de Azure Cosmos DB para realizar las operaciones de lectura y escritura desde el motor de base de datos de Graph. La API de Gremlin tiene compatibilidad con el protocolo de conexión con Gremlin de código abierto, por lo que puede usar los SDK de Gremlin de código abierto para compilar la aplicación. La API de Gremlin de Azure Cosmos DB también funciona con Apache Spark y [GraphFrames](https://github.com/graphframes/graphframes) para escenarios analíticos complejos de grafos. Para más información, consulte el artículo [API de Gremlin](graph-introduction.md).

## <a name="table-api"></a>Table API

Esta API almacena los datos en formato clave-valor. Si actualmente usa Azure Table Storage, es posible que observe algunas limitaciones en la latencia, el escalado, el rendimiento, la distribución global, la administración de índices y el bajo rendimiento de las consultas. Table API supera estas limitaciones y se recomienda migrar la aplicación si quiere aprovechar las ventajas de Azure Cosmos DB. Table API solo admite escenarios OLTP.

Las aplicaciones escritas para Azure Table Storage pueden migrarse a Table API con pocos cambios en el código, y pueden sacar provecho de las funcionalidades prémium. Para más información, consulte el artículo [Table API](table-introduction.md).

## <a name="next-steps"></a>Pasos siguientes

* [Introducción a SQL API de Azure Cosmos DB](create-sql-api-dotnet.md)
* [Introducción a la API de Azure Cosmos DB para MongoDB](create-mongodb-nodejs.md)
* [Introducción a la API Cassandra de Azure Cosmos DB](create-cassandra-dotnet.md)
* [Introducción a Gremlin API de Azure Cosmos DB](create-graph-dotnet.md)
* [Introducción a Table API de Azure Cosmos DB](create-table-dotnet.md)