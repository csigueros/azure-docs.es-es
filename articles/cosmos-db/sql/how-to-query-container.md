---
title: Consulta de contenedores en Azure Cosmos DB
description: Más información sobre cómo consultar contenedores en Azure Cosmos DB mediante consultas en particiones y entre particiones
author: markjbrown
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: how-to
ms.date: 3/18/2019
ms.author: mjbrown
ms.openlocfilehash: b74a2297f90d5770caa25f5b0886765bf70b0f47
ms.sourcegitcommit: dcf1defb393104f8afc6b707fc748e0ff4c81830
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/27/2021
ms.locfileid: "123114275"
---
# <a name="query-an-azure-cosmos-container"></a>Consulta de un contenedor de Azure Cosmos
[!INCLUDE[appliesto-sql-api](../includes/appliesto-sql-api.md)]

En este artículo se explica cómo consultar un contenedor (colección, grafo, tabla) en Azure Cosmos DB. En concreto, se explica cómo funcionan las consultas en particiones y entre particiones en Azure Cosmos DB.

## <a name="in-partition-query"></a>Consulta en la partición

Al consultar los datos de los contenedores, si la consulta tiene un filtro de clave de partición especificado, Azure Cosmos DB optimiza automáticamente la consulta. Enruta la consulta a las [particiones físicas](../partitioning-overview.md#physical-partitions) correspondientes a los valores de clave de partición especificados en el filtro.

Por ejemplo, considere la consulta siguiente con un filtro de igualdad en `DeviceId`. Si ejecutamos esta consulta en un contenedor con particiones en `DeviceId`, esta consulta se filtrará en una sola partición física.

```sql
SELECT * FROM c WHERE c.DeviceId = 'XMS-0001'
```

Como en el ejemplo anterior, esta consulta también filtrará en una sola partición. Agregar el filtro adicional en `Location` no cambia esto:

```sql
SELECT * FROM c WHERE c.DeviceId = 'XMS-0001' AND c.Location = 'Seattle'
```

Esta es una consulta que tiene un filtro de intervalo en la clave de partición y no se limita a una única partición física. Para ser una consulta en particiones, la consulta debe tener un filtro de igualdad que incluya la clave de partición:

```sql
SELECT * FROM c WHERE c.DeviceId > 'XMS-0001'
```

## <a name="cross-partition-query"></a>Consulta entre particiones

La siguiente consulta no tiene un filtro en la clave de partición (`DeviceId`). Por lo tanto, debe desplegarse en todas las particiones físicas en las que se ejecuta en el índice de cada partición:

```sql
SELECT * FROM c WHERE c.Location = 'Seattle`
```

Cada partición física tiene su propio índice. Por lo tanto, cuando se ejecuta una consulta entre particiones en un contenedor, se ejecuta eficazmente una consulta *por* partición física. Azure Cosmos DB agregará automáticamente los resultados en distintas particiones físicas.

Los índices de distintas particiones físicas son independientes entre sí. No hay ningún índice global en Azure Cosmos DB.

## <a name="parallel-cross-partition-query"></a>Consulta entre particiones en paralelo

Los SDK 1.9.0 y posteriores de Azure Cosmos DB admiten opciones de ejecución de consultas en paralelo. Las consultas entre particiones en paralelo permiten realizar consultas de baja latencia entre particiones.

Puede administrar la ejecución de consultas en paralelo ajustando los parámetros siguientes:

- **MaxConcurrency**: establece el número máximo de conexiones de red simultáneas a las particiones del contenedor. Si establece esta propiedad en `-1`, el SDK administra el grado de paralelismo. Si  `MaxConcurrency` se establece en `0`, hay una única conexión de red a las particiones del contenedor.

- **MaxBufferedItemCount**: equilibra la latencia de las consultas frente al uso de memoria del lado cliente. Si se omite esta opción o se establece en -1, el SDK administra el número de elementos almacenados en búfer durante la ejecución de consultas en paralelo.

Debido a la capacidad de Azure Cosmos DB de paralelizar las consultas entre particiones, la latencia de las consultas normalmente se escalará correctamente, ya que el sistema agrega [particiones físicas](../partitioning-overview.md#physical-partitions). Sin embargo, el cargo RU aumenta significativamente a medida que aumenta el número total de particiones físicas.

Al ejecutar una consulta entre particiones, en esencia se realiza una consulta independiente por cada partición física individual. Aunque las consultas de consultas entre particiones usarán el índice, si está disponible, todavía no son casi tan eficaces como las consultas en la partición.

## <a name="useful-example"></a>Ejemplo útil

Esta es una analogía para comprender mejor las consultas entre particiones:

Supongamos que es un controlador de entrega que tiene que entregar paquetes a diferentes complejos de apartamento. Cada complejo de apartamento tiene una lista en las instalaciones que tienen todos los números de unidad de residente. Podemos comparar cada apartamento complejo con una partición física y cada lista con el índice de la partición física.

Se pueden comparar consultas en particiones y entre particiones mediante este ejemplo:

### <a name="in-partition-query"></a>Consulta en la partición

Si el controlador de entrega sabe el complejo de apartamento (partición física) correcto, puede dirigirse inmediatamente a la compilación correcta. El controlador puede comprobar la lista de complejos del apartamento de los números de unidad del residente (el índice) y ofrecer rápidamente los paquetes correspondientes. En este caso, el controlador no pierde tiempo ni esfuerzo conduciendo a un complejo de apartamento para comprobar y ver si hay algún destinatario del paquete que viva allí.

### <a name="cross-partition-query-fan-out"></a>Consulta entre particiones (distribución ramificada)

Si el controlador de entrega no conoce el complejo de apartamento (partición física) correcto, deberá dirigirse a cada una de las compilaciones de apartamento únicas y comprobar la lista con todos los números de unidad del residente (el índice). Una vez que llegan a cada complejo de apartamento, todavía podrán usar la lista de direcciones de cada residente. Sin embargo, deberán comprobar todas las listas de complejos de apartamentos, tanto si los destinatarios del paquete están activos o no. Así es como funcionan las consultas entre particiones. Aunque pueden utilizar el índice (no es necesario realizar una prueba en cada puerta), deben comprobar por separado el índice de cada partición física.

### <a name="cross-partition-query-scoped-to-only-a-few-physical-partitions"></a>Consulta entre particiones (con ámbito solo en algunas particiones físicas)

Si el controlador de entrega sabe que todos los destinatarios de paquete viven en unos pocos complejos de apartamentos, no necesitarán conducir a cada uno de ellos. Aunque la conducción a unos cuantos complejos de apartamento seguirá requiriendo más trabajo que la visita de un solo edificio, el controlador de entrega todavía ahorra tiempo y esfuerzo. Si una consulta tiene la clave de partición en su filtro con la palabra clave `IN`, solo comprobará los índices de los datos de la partición física pertinente.

## <a name="avoiding-cross-partition-queries"></a>Evitar consultas entre particiones

Para la mayoría de los contenedores, es inevitable que tenga algunas consultas entre particiones. Tener algunas consultas entre particiones está bien. Casi todas las operaciones de consulta se admiten en particiones (tanto claves de partición lógicas como particiones físicas). Azure Cosmos DB también tiene muchas optimizaciones en el motor de consulta y los SDK del cliente para paralelizar la ejecución de consultas en particiones físicas.

Para la mayoría de los escenarios con mucha lectura, se recomienda simplemente seleccionar la propiedad más común en los filtros de consulta. También debe asegurarse de que la clave de partición se adhiera a otras [prácticas recomendadas para la selección de claves de partición](../partitioning-overview.md#choose-partitionkey).

Evitar las consultas entre particiones normalmente solo es importante con contenedores grandes. Se le cobrará un mínimo de aproximadamente 2,5 RUs cada vez que compruebe el índice de la partición física de los resultados, aunque no haya elementos en la partición física que coincidan con el filtro de la consulta. Como tal, si solo tiene una (o pocas) particiones físicas, las consultas entre particiones no consumirán mucho más RUs que las consultas en particiones.

El número de particiones físicas está ligado a la cantidad de RUs aprovisionadas. Cada partición física permite hasta 10 000 RUs aprovisionadas y puede almacenar hasta 50 GB de datos. Azure Cosmos DB administrará automáticamente las particiones físicas. El número de particiones físicas en el contenedor depende del rendimiento aprovisionado y del almacenamiento consumido.

Debe intentar evitar las consultas entre particiones si la carga de trabajo cumple los criterios siguientes:
- Planea tener más de 30 000 RU aprovisionadas
- Planea almacenar más de 100 GB de datos

## <a name="next-steps"></a>Pasos siguientes

Consulte los siguientes artículos para información sobre la creación de particiones en Azure Cosmos DB:

- [Creación de particiones en Azure Cosmos DB](../partitioning-overview.md)
- [Claves de partición sintéticas en Azure Cosmos DB](synthetic-partition-keys.md)
