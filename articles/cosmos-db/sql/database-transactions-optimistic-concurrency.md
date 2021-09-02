---
title: Transacciones entre bases de datos y control de simultaneidad optimista en Azure Cosmos DB
description: En este artículo se describen las transacciones entre bases de datos y el control de simultaneidad optimista en Azure Cosmos DB
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 12/04/2019
ms.reviewer: sngun
ms.openlocfilehash: 3c11f83f46d8fc29911a191e3a420940fabd992d
ms.sourcegitcommit: dcf1defb393104f8afc6b707fc748e0ff4c81830
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/27/2021
ms.locfileid: "123114353"
---
# <a name="transactions-and-optimistic-concurrency-control"></a>Transacciones y control de simultaneidad optimista
[!INCLUDE[appliesto-sql-api](../includes/appliesto-sql-api.md)]

Las transacciones entre bases de datos proporcionan un modelo de programación seguro y predecible para tratar los cambios simultáneos que se producen en los datos. Las bases de datos relacionales tradicionales, como SQL Server, le permiten escribir la lógica de negocios mediante procedimientos almacenados o desencadenadores y enviarla al servidor para su ejecución directamente en el motor de base de datos. Con las bases de datos relacionales tradicionales, se le pide que trate con dos diferentes lenguajes de programación, el lenguaje de programación de la aplicación (no transaccional) como, por ejemplo, JavaScript, Python, C#, Java, etc. y el lenguaje de programación transaccional (como T-SQL) que la base de datos ejecuta de forma nativa.

El motor de base de datos en Azure Cosmos DB es totalmente compatible con transacciones ACID (atomicidad, coherencia, aislamiento, durabilidad) con aislamiento de instantáneas. Todas las operaciones de la base de datos en el ámbito de una [partición lógica](../partitioning-overview.md) de contenedor se ejecutan de forma transaccional en el motor de base de datos que la réplica de la partición hospeda. Estas operaciones incluyen operaciones de escritura (actualización de uno o varios elementos de la partición lógica) y operaciones de lectura. En la siguiente tabla se muestran los diferentes tipos de operaciones y transacciones:

| **operación**  | **Tipo de operación** | **Transacción de uno o varios elementos** |
|---------|---------|---------|
| Inserción (sin necesidad de un desencadenador previo o posterior) | Escritura | Transacción de un elemento único |
| Inserción (con un desencadenador previo o posterior) | Escritura y lectura | Transacción de varios elementos |
| Reemplazo (sin necesidad de un desencadenador previo o posterior) | Escritura | Transacción de un elemento único |
| Reemplazo (con un desencadenador previo o posterior) | Escritura y lectura | Transacción de varios elementos |
| Actualización e inserción (sin necesidad de un desencadenador previo o posterior) | Escritura | Transacción de un elemento único |
| Actualización e inserción (con un desencadenador previo o posterior) | Escritura y lectura | Transacción de varios elementos |
| Eliminación (sin necesidad de un desencadenador previo o posterior) | Escritura | Transacción de un elemento único |
| Eliminación (con un desencadenador previo o posterior) | Escritura y lectura | Transacción de varios elementos |
| Ejecutar procedimiento almacenado | Escritura y lectura | Transacción de varios elementos |
| El sistema inició la ejecución de un procedimiento de combinación | Escritura | Transacción de varios elementos |
| El sistema inició la ejecución de la eliminación de elementos basados en la expiración (TTL) de un elemento. | Escritura | Transacción de varios elementos |
| Lectura | Lectura | Transacción de un solo elemento |
| Fuente de cambios | Lectura | Transacción de varios elementos |
| Lectura paginada | Lectura | Transacción de varios elementos |
| Consulta paginada | Lectura | Transacción de varios elementos |
| Ejecución de UDF como parte de la consulta paginada | Lectura | Transacción de varios elementos |

## <a name="multi-item-transactions"></a>Transacciones de varios elementos

Azure Cosmos DB le permite escribir [procedimientos almacenados, desencadenadores previos o posteriores, funciones definidas por el usuario (UDF)](stored-procedures-triggers-udfs.md) y procedimientos de combinación en JavaScript. Azure Cosmos DB admite de forma nativa la ejecución de JavaScript dentro de su motor de base de datos. Puede registrar los procedimientos almacenados, los desencadenadores previos o posteriores, las funciones definidas por el usuario (UDF) y los procedimientos de combinación en un contenedor y ejecutarlos posteriormente de forma transaccional en el motor de base de datos de Azure Cosmos. La escritura de la lógica de la aplicación en JavaScript permite la expresión de forma natural del flujo de control, el ámbito de las variables, la asignación y la integración de primitivos de control de excepciones en transacciones de bases de datos directamente en el lenguaje JavaScript.

Los procedimientos almacenados basados en JavaScript, los desencadenadores, las UDF y los procedimientos de combinación se encapsulan en una transacción ACID ambiental con aislamiento de instantáneas en todos los elementos de la partición lógica. Durante el transcurso de esta ejecución, si el programa de JavaScript lanza una excepción, entonces se cancela y se revierte toda la transacción. El modelo de programación resultante es sencillo, pero eficaz. Los desarrolladores de JavaScript obtienen un modelo de programación duradero al tiempo que siguen usando sus construcciones de lenguaje y primitivos de biblioteca que ya conocen.

La capacidad de ejecutar JavaScript directamente en el motor de base de datos proporciona rendimiento y la ejecución transaccional de las operaciones de base de datos en los elementos de un contenedor. Además, debido a que el motor de base de datos de Azure Cosmos DB admite de forma nativa JSON y JavaScript, no hay ningún error de coincidencia de impedancia entre los sistemas tipo de la aplicación y la base de datos.

## <a name="optimistic-concurrency-control"></a>Control de simultaneidad optimista

El control de simultaneidad optimista le permite evitar la pérdida de actualizaciones y eliminaciones. Las operaciones simultáneas en conflicto están sujetas al bloqueo pesimista normal del motor de base de datos que hospeda la partición lógica que posee el elemento. Cuando dos operaciones simultáneas intentan actualizar la versión más reciente de un elemento de una partición lógica, uno de ellos tendrá prioridad y en el otro se producirá un error. No obstante, si una o dos operaciones que están intentando actualizar simultáneamente el mismo elemento habían leído anteriormente un valor más antiguo de este, la base de datos no sabrá si el valor leído anteriormente por cualquiera de las operaciones en conflicto, o por ambas, era realmente el valor más reciente de ese elemento. Afortunadamente, esta situación se puede detectar con el **control de simultaneidad optimista (OCC)** antes de permitir que las dos operaciones escriban el límite de la transacción en el motor de base de datos. El control de simultaneidad optimista protege los datos frente a cambios accidentales por sobrescritura realizados por otros. También impide que otros usuarios sobrescriban accidentalmente sus propios cambios.

Las actualizaciones simultáneas de un elemento están sujetas al control de simultaneidad optimista mediante la capa del protocolo de comunicación de Azure Cosmos DB En el caso de las cuentas de Azure Cosmos configuradas para **escrituras de una sola región**, Azure Cosmos DB garantiza que la versión del elemento en el lado del cliente que está actualizando (o eliminando) es la misma que la versión del elemento en el contenedor de Azure Cosmos. Esto garantiza que las escrituras están protegidas frente a la sobrescritura accidental por escrituras de otros y viceversa. En un entorno multiusuario, el control de simultaneidad optimista le protege de eliminar o actualizar accidentalmente la versión equivocada de un elemento. Por tanto, los elementos están protegidos contra los desastrosos problemas de "Actualización perdida" o "Eliminación perdida".

En una cuenta de Azure Cosmos configurada con **escrituras de varias regiones**, los datos se pueden confirmar de forma independiente en las regiones secundarias si su valor `_etag` coincide con el de los datos de la región local. Una vez que los nuevos datos se confirman localmente en una región secundaria, se combinan en el concentrador o en la región primaria. Si la directiva de resolución de conflictos combina los nuevos datos en la región del concentrador, estos datos se replicarán globalmente con el nuevo valor `_etag`. Si la directiva de resolución de conflictos rechaza los nuevos datos, la región secundaria se revertirá a los datos y el valor `_etag` originales.

Todos los elementos almacenados en un contenedor de Azure Cosmos tienen una propiedad `_etag` definida por el sistema. El servidor genera y actualiza automáticamente el valor de `_etag` cada vez que se actualiza el elemento. `_etag` se puede usar con el encabezado de solicitud `if-match` proporcionado por el cliente para permitir al servidor decidir si un elemento se puede actualizar de manera condicional. Si el valor del encabezado `if-match` coincide con el valor de `_etag` en el servidor, el elemento se actualiza. Si el valor del encabezado de solicitud `if-match` ya no es el actual, el servidor rechaza la operación con un mensaje de respuesta "HTTP 412 Precondition failure" (HTTP 412: error de condición previa). El cliente puede, posteriormente, volver a obtener el elemento para adquirir la versión actual de este en el servidor o reemplazar la versión del elemento en el servidor por su propio valor `_etag` para el elemento. Además, `_etag` puede emplearse con el encabezado `if-none-match` para determinar si hay que volver a recuperar un recurso.

El valor `_etag` del elemento cambia cada vez que este se actualiza. En el caso de operaciones de reemplazo de elemento, `if-match` debe expresarse explícitamente como parte de las opciones de la solicitud. Para ver un ejemplo, consulte el código de ejemplo de [GitHub](https://github.com/Azure/azure-cosmos-dotnet-v3/blob/master/Microsoft.Azure.Cosmos.Samples/Usage/ItemManagement/Program.cs#L676-L772). Los valores `_etag` se comprueban de forma implícita para todos los elementos escritos que el procedimiento almacenado toca. Si no se detecta ningún conflicto, el procedimiento almacenado revertirá la transacción y se producirá una excepción. Con este método, se aplicarán de forma atómica todas las operaciones de escritura del procedimiento almacenado o ninguna de ellas. Esto constituye una señal para que la aplicación vuelva a aplicar las actualizaciones y reintente la solicitud original del cliente.

## <a name="next-steps"></a>Pasos siguientes

Puede encontrar más información sobre las transacciones entre bases de datos y el control de simultaneidad optimista en los siguientes artículos:

- [Uso de bases de datos, contenedores y elementos de Azure Cosmos](../account-databases-containers-items.md)
- [Niveles de coherencia](../consistency-levels.md)
- [Tipos de conflicto y directivas de resolución de conflictos](../conflict-resolution-policies.md)
- [Uso de TransactionalBatch](transactional-batch.md)
- [Procedimientos almacenados, desencadenadores y funciones definidas por el usuario](stored-procedures-triggers-udfs.md)
