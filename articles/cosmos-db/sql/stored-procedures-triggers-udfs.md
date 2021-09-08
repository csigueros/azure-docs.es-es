---
title: Trabajo con procedimientos almacenados, desencadenadores y funciones definidas por el usuario en Azure Cosmos DB
description: En este artículo se introducen conceptos como procedimientos almacenados, desencadenadores y funciones definidas por el usuario en Azure Cosmos DB.
author: timsander1
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 08/26/2021
ms.author: tisande
ms.reviewer: sngun
ms.openlocfilehash: 1c30c7e659106b8206fa4e1e781f9a9666509e6c
ms.sourcegitcommit: dcf1defb393104f8afc6b707fc748e0ff4c81830
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/27/2021
ms.locfileid: "123114427"
---
# <a name="stored-procedures-triggers-and-user-defined-functions"></a>Procedimientos almacenados, desencadenadores y funciones definidas por el usuario
[!INCLUDE[appliesto-sql-api](../includes/appliesto-sql-api.md)]

Azure Cosmos DB proporciona la ejecución transaccional integrada en lenguaje JavaScript. Cuando se usa la API de SQL en Azure Cosmos DB, puede escribir los **procedimientos almacenados**, los **desencadenadores** y las **funciones definidas por el usuario (UDF)** en el lenguaje JavaScript. Puede escribir su lógica en JavaScript, que se ejecuta dentro del motor de base de datos. Puede crear y ejecutar desencadenadores, procedimientos almacenados y UDF mediante [Azure Portal](https://portal.azure.com/), la [API de consulta integrada del lenguaje JavaScript en Azure Cosmos DB](javascript-query-api.md) o los [SDK del cliente de la API de SQL de Cosmos DB](how-to-use-stored-procedures-triggers-udfs.md).

## <a name="benefits-of-using-server-side-programming"></a>Ventajas del uso de la programación en el lado servidor

La escritura de procedimientos almacenados, desencadenadores y funciones definidas por el usuario (UDF) en JavaScript le permite crear aplicaciones completas y tienen las siguientes ventajas:

* **Lógica de procedimientos:** JavaScript como un lenguaje de programación de alto nivel que proporciona una interfaz completa y familiar para expresar la lógica empresarial. Puede realizar una secuencia de operaciones complejas en los datos.

* **Transacciones atómicas:** las operaciones de base de datos de Azure Cosmos DB realizadas dentro de un único procedimiento almacenado o desencadenador son atómicas. Esta funcionalidad atómica permite a una aplicación combinar operaciones relacionadas en un único lote para que todas se realicen correctamente o no lo haga ninguna.

* **Rendimiento:** Los datos de JSON se asignan intrínsecamente al sistema de tipos de lenguaje JavaScript. Esta asignación permite diversas optimizaciones, como la materialización diferida de documentos JSON en el grupo de búferes, y las pone a disposición a petición para el código en ejecución. Hay otras ventajas de rendimiento asociadas con el envío de la lógica empresarial a la base de datos, que incluyen:

   * *Procesamiento por lotes:* los desarrolladores pueden agrupar operaciones como inserciones y enviarlas en masa. Los costes de la latencia de tráfico de red y la sobrecarga de almacenamiento para crear transacciones independientes se reducen de forma significativa.

   * *Precompilación:* Los procedimientos almacenados, los desencadenadores y las UDF se precompilan implícitamente en formato de código byte para evitar los costos de compilación en el momento de cada invocación de script. Debido a la precompilación, la invocación de procedimientos almacenados es rápida y tiene una superficie baja.

   * *Secuenciación:* A veces, las operaciones necesitan un mecanismo de desencadenamiento que puede realizar una o varias actualizaciones en los datos. Además de la atomicidad, también existen ventajas de rendimiento cuando se ejecuta en el servidor.

* **Encapsulación:** los procedimientos almacenados se pueden utilizar para agrupar la lógica en un lugar. La encapsulación agrega una capa de abstracción en la parte superior de los datos sin procesar, lo cual le permite desarrollar sus aplicaciones de forma independiente de los datos. Esta capa de abstracción es útil cuando los datos no tienen esquema y no tiene que administrar la adición de lógica adicional directamente en la aplicación. Esta abstracción le permite mantener seguros sus datos simplificando el acceso desde los scripts.

> [!TIP]
> Los procedimientos almacenados son más adecuados para las operaciones con un gran número de escrituras y que requieren una transacción en un valor de clave de partición. Al decidir si se deben usar procedimientos almacenados, se optimiza mediante la encapsulación de la cantidad máxima de operaciones de escritura posibles. Por lo general, los procedimientos almacenados no son el medio más eficaz para realizar una gran cantidad de operaciones de lectura o consulta, por lo que su uso para procesar por lotes un gran número de lecturas que devolver al cliente no dará como resultado el beneficio deseado. Para obtener el mejor rendimiento, estas operaciones con un gran número de lecturas deben realizarse en el lado cliente, mediante el SDK de Cosmos. 

## <a name="transactions"></a>Transacciones

Una transacción en una base de datos típica se puede definir como una secuencia de operaciones realizadas como una única unidad lógica de trabajo. Cada transacción proporciona **garantías de propiedad ACID**. ACID es un acrónimo conocido que hacer referencia a: **A** tomicidad, **C** oherencia, a **I** slamiento y **D** urabilidad. 

* La atomicidad garantiza que todas las operaciones realizadas dentro de una transacción se lean como una única unidad donde se confirman todas o ninguna. 

* La coherencia asegura que los datos se encuentran siempre en un estado válido en todas las transacciones. 

* El aislamiento garantiza que dos transacciones no pueden interferir entre ellas; muchos sistemas comerciales proporcionan varios niveles de aislamiento que se pueden utilizar según las necesidades de aplicación. 

* La durabilidad asegura que cualquier cambio que esté confirmado en la base de datos estará siempre presente.

En Azure Cosmos DB, el entorno en tiempo de ejecución de JavaScript está hospedado dentro del motor de base de datos. Por lo tanto, las solicitudes realizadas dentro de los procedimientos almacenados y desencadenadores se ejecutan en el mismo ámbito que la sesión de base de datos. Esta característica permite a Azure Cosmos DB garantizar propiedades ACID para todas las operaciones que formen parte de un procedimiento almacenado o desencadenador. Para obtener ejemplos, vea el artículo [Cómo implementar transacciones](how-to-write-stored-procedures-triggers-udfs.md#transactions).

### <a name="scope-of-a-transaction"></a>Ámbito de una transacción

Los procedimientos almacenados se asocian a un contenedor de Azure Cosmos y la ejecución del procedimiento almacenado se incluye en el ámbito de una clave de partición lógica. Los procedimientos almacenados deben incluir un valor de clave de partición lógica durante la ejecución que defina la partición lógica del ámbito de la transacción. Para más información, consulte el artículo [Creación de particiones de Azure Cosmos DB](../partitioning-overview.md).

### <a name="commit-and-rollback"></a>Confirmación y reversión

Las transacciones están integradas de forma nativa en el modelo de programación de JavaScript de Azure Cosmos DB. Dentro de una función de JavaScript, todas las operaciones se ajustan automáticamente en una única transacción. Si la lógica de JavaScript en un procedimiento almacenado se completa sin excepciones, todas las operaciones dentro de la transacción se confirman en la base de datos. Instrucciones como `BEGIN TRANSACTION` y `COMMIT TRANSACTION` (familiarizadas con bases de datos relacionales) están implícitas en Azure Cosmos DB. Si existe alguna excepción desde el script, el entorno en tiempo de ejecución de JavaScript de Azure Cosmos DB revertirá toda la transacción. Por lo tanto, el inicio de una excepción es equivalente a una instrucción `ROLLBACK TRANSACTION` en Azure Cosmos DB.

### <a name="data-consistency"></a>Coherencia de datos

Los procedimientos almacenados y los desencadenadores se ejecutan siempre en la réplica principal del contenedor de Azure Cosmos. Esta característica garantiza que las lecturas desde los procedimientos almacenados ofrezcan una [fuerte coherencia](../consistency-levels.md). Las consultas que utilizan funciones definidas por el usuario se pueden ejecutar en el servidor principal o en cualquier réplica secundaria. Los procedimientos almacenados y los desencadenadores están diseñados para admitir escrituras de transacción; sin embargo, la lógica de solo lectura se implementa mejor como lógica del lado de la aplicación y las consultas con los [SDK de la API de SQL de Azure Cosmos DB](sql-api-dotnet-samples.md) le ayudarán a saturar el rendimiento de la base de datos. 

> [!TIP]
> Es posible que las consultas ejecutadas en un procedimiento almacenado o desencadenador no vean los cambios en los elementos realizados por la misma transacción de script. Esta instrucción se aplica a las consultas SQL, como `getContent().getCollection.queryDocuments()` y a las consultas de lenguaje integradas, como `getContext().getCollection().filter()`.

## <a name="bounded-execution"></a>Ejecución vinculada

Todas las operaciones de Azure Cosmos DB se deben completar dentro de la duración del tiempo de espera especificado. Los procedimientos almacenados tienen un tiempo de espera de 5 segundos. Esta restricción se aplica a las funciones de JavaScript: procedimientos almacenados, desencadenadores y funciones definidas por el usuario. Si una operación no se completa dentro de ese límite de tiempo, la transacción se revierte.

Puede asegurarse de que las funciones de JavaScript finalicen dentro del límite de tiempo o implementar un modelo basado en la continuación en el lote o reanudar la ejecución. Con el fin de simplificar el desarrollo de los procedimientos almacenados y los desencadenadores para controlar los límites de tiempo, todas las funciones del contenedor de Azure Cosmos (por ejemplo, la creación, lectura, actualización y eliminación de elementos) devuelven un valor booleano que representa si se completará la operación. Si este valor es false, es una indicación de que el procedimiento debe concluir la ejecución porque el script consume más tiempo o rendimiento aprovisionado que el valor configurado. Se garantiza la finalización de las operaciones en cola anteriores a la primera operación de almacenamiento no aceptada si el procedimiento almacenado se completa a tiempo y no pone en cola más solicitudes. Por lo tanto, las operaciones deben ponerse en cola una a una mediante la convención de devolución de llamada de JavaScript para administrar el flujo de control del script. Dado que los scripts se ejecutan en un entorno de servidor, se rigen estrictamente. Los scripts que infrinjan repetidamente los límites de ejecución puede que se marquen como inactivos y que no se puedan ejecutar, por lo que deben volver a crearse para respetar los dichos límites.

Las funciones de JavaScript también están sujetas a la [capacidad de rendimiento aprovisionado](../request-units.md). Las funciones de JavaScript podrían acabar utilizando un gran número de unidades de solicitud en poco tiempo y pueden tener limitación de frecuencia si se alcanza el límite de capacidad de rendimiento aprovisionado. Es importante tener en cuenta que los scripts consumen un rendimiento adicional, además del rendimiento dedicado a ejecutar operaciones de bases de datos, aunque estas operaciones son ligeramente menos costosas que la ejecución de las misma desde el cliente.

## <a name="triggers"></a>Desencadenadores

Azure Cosmos DB admite dos tipos de desencadenadores:

### <a name="pre-triggers"></a>Desencadenadores previos

Azure Cosmos DB proporciona desencadenadores que se pueden invocar mediante una operación en un elemento de Azure Cosmos. Por ejemplo, puede especificar un desencadenador previo al crear un elemento. En este caso, el desencadenador previo se ejecutará antes de la creación del elemento. Los desencadenadores previos no pueden tener parámetros de entrada. Si es necesario, el objeto de la solicitud se puede usar para actualizar el cuerpo del documento de la solicitud original. Cuando se registran los desencadenadores, los usuarios pueden especificar las operaciones que se pueden ejecutar con ellos. Si un desencadenador se creó con `TriggerOperation.Create`, significa que no se podrá usar en una operación de reemplazo. Para obtener ejemplos, vea el artículo [Escritura de desencadenadores](how-to-write-stored-procedures-triggers-udfs.md#triggers).

### <a name="post-triggers"></a>Desencadenadores posteriores

De forma similar a los desencadenadores previos, los desencadenadores posteriores también están asociados a una operación en un elemento de Azure Cosmos y no requieren ningún parámetro de entrada. Se ejecutan *después* de que se haya completado la operación y tienen acceso al mensaje de respuesta que se envía al cliente. Para obtener ejemplos, vea el artículo [Escritura de desencadenadores](how-to-write-stored-procedures-triggers-udfs.md#triggers).

> [!NOTE]
> Los desencadenadores registrados no se ejecutan automáticamente cuando se producen sus correspondientes operaciones (crear/eliminar/reemplazar/actualizar). Se les debe llamar de forma explícita al ejecutar estas operaciones. Para obtener más información, vea el artículo [Cómo ejecutar desencadenadores](how-to-use-stored-procedures-triggers-udfs.md#pre-triggers).

## <a name="user-defined-functions"></a><a id="udfs"></a>Funciones definidas por el usuario

Las [funciones definidas por el usuario](sql-query-udfs.md) (UDF) se utilizan para ampliar la sintaxis del lenguaje de consultas de la API de SQL e implementar fácilmente la lógica de negocio personalizada. Solo se pueden llamar dentro de las consultas. Las UDF no tienen acceso al objeto de contexto y se supone que se deben utilizar como un JavaScript únicamente de cálculo. Por lo tanto, se pueden ejecutar en réplicas secundarias. Para obtener ejemplos, vea el artículo [Escritura de funciones definidas por el usuario](how-to-write-stored-procedures-triggers-udfs.md#udfs).

## <a name="javascript-language-integrated-query-api"></a><a id="jsqueryapi"></a>Language-integrated query API de JavaScript

Además de emitir consultas mediante la sintaxis de consulta de la API de SQL, el [SDK del lado servidor](https://azure.github.io/azure-cosmosdb-js-server) permite realizar consultas a través de una interfaz de JavaScript sin necesitar conocimientos de SQL. La Query API de JavaScript le permite compilar consultas mediante programación pasando las funciones de predicado en la secuencia de las llamadas de función. Las consultas se analizan con el entorno en tiempo de ejecución de JavaScript y se ejecutan eficazmente dentro de Azure Cosmos DB. Para más información sobre la compatibilidad con la Query API de JavaScript, consulte el artículo [Working with JavaScript Language Integrated Query API in Azure Cosmos DB](javascript-query-api.md) (Trabajo con Language Integrated Query API de JavaScript). Para obtener ejemplos, consulte el artículo [Escritura de procedimientos almacenados y desencadenadores en Azure Cosmos DB con la API de consulta de JavaScript](how-to-write-javascript-query-api.md).

## <a name="next-steps"></a>Pasos siguientes

Aprenda a escribir y usar procedimientos almacenados, desencadenadores y funciones definidas por el usuario en Azure Cosmos DB con los siguientes artículos:

* [Escritura de procedimientos almacenados, desencadenadores y funciones definidas por el usuario](how-to-write-stored-procedures-triggers-udfs.md)

* [Uso de procedimientos almacenados, desencadenadores y funciones definidas por el usuario](how-to-use-stored-procedures-triggers-udfs.md)

* [Working with JavaScript language integrated query API](javascript-query-api.md) (Trabajo con Language Integrated Query API de JavaScript)

¿Intenta planear la capacidad de una migración a Azure Cosmos DB? Puede usar información sobre el clúster de bases de datos existente para planear la capacidad.
* Si lo único que sabe es el número de núcleos virtuales y servidores del clúster de bases de datos existente, lea sobre el [cálculo de unidades de solicitud mediante núcleos o CPU virtuales](../convert-vcore-to-request-unit.md). 
* Si conoce las velocidades de solicitud típicas de la carga de trabajo de la base de datos actual, lea sobre el [cálculo de unidades de solicitud mediante la herramienta de planeamiento de capacidad de Azure Cosmos DB](estimate-ru-with-capacity-planner.md).