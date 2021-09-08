---
title: Solución de problemas al usar el desencadenador de Azure Functions para Azure Cosmos DB
description: Problemas comunes, soluciones alternativas y pasos de diagnóstico al usar el desencadenador de Azure Functions para Cosmos DB
author: ealsur
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.date: 12/29/2020
ms.author: maquaran
ms.topic: troubleshooting
ms.reviewer: sngun
ms.openlocfilehash: b293a0208ad3f520a6cb72010601aa8a019c28ec
ms.sourcegitcommit: dcf1defb393104f8afc6b707fc748e0ff4c81830
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/27/2021
ms.locfileid: "123114381"
---
# <a name="diagnose-and-troubleshoot-issues-when-using-azure-functions-trigger-for-cosmos-db"></a>Diagnóstico y solución de problemas al usar el desencadenador de Azure Functions para Azure Cosmos DB
[!INCLUDE[appliesto-sql-api](../includes/appliesto-sql-api.md)]

En este artículo se abordan los problemas comunes, las soluciones alternativas y los pasos de diagnóstico al usar el [desencadenador de Azure Functions para Cosmos DB](change-feed-functions.md).

## <a name="dependencies"></a>Dependencias

El desencadenador de Azure Functions y los enlaces para Cosmos DB dependen de los paquetes de extensión a través del entorno de ejecución básico de Azure Functions. Mantenga siempre estos paquetes actualizados, ya que es posible que incluyan correcciones y características nuevas que puedan solucionar los posibles problemas:

* Para Azure Functions V2, consulte [Microsoft.Azure.WebJobs.Extensions.CosmosDB](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.CosmosDB).
* Para Azure Functions V1, consulte [Microsoft.Azure.WebJobs.Extensions.CosmosDB](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.DocumentDB).

En este artículo siempre se hará referencia a Azure Functions V2 cada vez que se menciona el entorno de ejecución, a menos que se especifique explícitamente.

## <a name="consume-the-azure-cosmos-db-sdk-independently"></a>Consumo independiente del SDK de Azure Cosmos DB

La funcionalidad clave del paquete de extensión consiste en proporcionar compatibilidad con el desencadenador de Azure Functions y los enlaces para Cosmos DB. También incluye el [SDK de Azure Cosmos DB .NET](sql-api-sdk-dotnet-core.md), lo que resulta útil si desea interactuar con Azure Cosmos DB mediante programación sin usar el desencadenador ni enlaces.

Si desea usar el SDK de Azure Cosmos DB, asegúrese de no agregar al proyecto otra referencia de paquete de NuGet. En su lugar, **permita que la referencia del SDK se resuelva mediante el paquete de extensión de Azure Functions**. Uso del SDK de Azure Cosmos DB por separado de los desencadenadores y enlaces

Además, si va a crear manualmente su propia instancia del [cliente del SDK de Azure Cosmos DB](./sql-api-sdk-dotnet-core.md), debe seguir el patrón de tener solo una instancia del cliente [mediante una solución de patrón Singleton](../../azure-functions/manage-connections.md?tabs=csharp#azure-cosmos-db-clients). Este proceso evitará posibles problemas con el socket en sus operaciones.

## <a name="common-scenarios-and-workarounds"></a>Escenarios comunes y soluciones alternativas

### <a name="azure-function-fails-with-error-message-collection-doesnt-exist"></a>La función de Azure emite un mensaje de error que indica que la colección no existe

La función de Azure emite un mensaje de error similar a "O la colección de origen "nombre de la colección" (en la base de datos "nombre de la base de datos") o la colección de concesión"colección2-name"(en la base de datos "nombre de la base de datos2") no existen. Ambas colecciones deben existir antes de que se inicia el agente de escucha. Para crear automáticamente la colección de concesión, establezca "CreateLeaseCollectionIfNotExists" en "true"

Esto significa que uno de los contenedores de Azure Cosmos o ambos necesarios para que funcione el desencadenador de trabajo no existen o no son accesibles para la función de Azure. **El propio error le indicará qué base de datos y qué contenedores de Azure Cosmos son el desencadenador que se busca**, según la configuración.

1. Compruebe el `ConnectionStringSetting` atributo y que **hace referencia a una configuración que existe en la aplicación de la función de Azure**. El valor de este atributo no debe ser la propia cadena de conexión, sino el nombre de la opción de configuración.
2. Compruebe que `databaseName` y `collectionName` existen en su cuenta de Azure Cosmos. Si usa el reemplazo de valor automático (mediante los patrones `%settingName%`), asegúrese de que el nombre de la configuración existe en la aplicación de la función de Azure.
3. Si no especifica un `LeaseCollectionName/leaseCollectionName`, el valor predeterminado es "leases". Compruebe que este contenedor exista. Si lo desea, puede establecer el atributo `CreateLeaseCollectionIfNotExists` en el desencadenador como `true` para crearlo automáticamente.
4. Compruebe la [configuración del Firewall de la cuenta de Azure Cosmos](../how-to-configure-firewall.md) para ver que no está bloqueando la función de Azure.

### <a name="azure-function-fails-to-start-with-shared-throughput-collection-should-have-a-partition-key"></a>La función de Azure no puede iniciarse e indica que "la colección con rendimiento compartido debe tener una clave de partición"

Las versiones anteriores de la extensión de Azure Cosmos DB no permitían usar un contenedor de concesión que se creara dentro de una [base de datos de rendimiento compartido](../set-throughput.md#set-throughput-on-a-database). Para resolver este problema, actualice la extensión [Microsoft.Azure.WebJobs.Extensions.CosmosDB](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.CosmosDB) para obtener la versión más reciente.

### <a name="azure-function-fails-to-start-with-partitionkey-must-be-supplied-for-this-operation"></a>Función de Azure no se inicia con el mensaje de error "PartitionKey must be supplied for this operation." (Se debe proporcionar PartitionKey para esta operación).

Este error significa que actualmente está usando una colección de concesiones con particiones con una antigua [dependencia de extensión](#dependencies). Actualice a la última versión disponible. Si actualmente está ejecutando Azure Functions v1, tendrá que actualizar a Azure Functions V2.

### <a name="azure-function-fails-to-start-with-the-lease-collection-if-partitioned-must-have-partition-key-equal-to-id"></a>La función de Azure no puede iniciarse e indica que "la colección de concesión, si tiene particiones, debe tener la clave de partición igual que el identificador".

Este error significa que el contenedor de concesiones actual tiene particiones, pero la ruta de acceso de clave de partición no es `/id`. Para resolver este problema, debe volver a crear el contenedor de concesiones con `/id` como clave de partición.

### <a name="you-see-a-value-cannot-be-null-parameter-name-o-in-your-azure-functions-logs-when-you-try-to-run-the-trigger"></a>Aparece un mensaje que indica "El valor no puede ser null. Nombre de parámetro: o" en los registros de Azure Functions cuando se intenta ejecutar el desencadenador

Este problema aparece si se usa Azure Portal y se intenta seleccionar el botón **Ejecutar** en la pantalla al inspeccionar una función de Azure que usa el desencadenador. El desencadenador no requiere que seleccione Ejecutar para iniciarse, se iniciará automáticamente cuando se implemente la función de Azure. Si desea comprobar la secuencia de registro de la función de Azure en Azure Portal, basta con que vaya al contenedor supervisado e inserte algunos elementos nuevos; verá automáticamente el desencadenador ejecutándose.

### <a name="my-changes-take-too-long-to-be-received"></a>Mis cambios tardan demasiado en recibirse

Este escenario puede tener varias causas y todas deben comprobarse:

1. ¿La función de Azure se implementa en la misma región que su cuenta de Azure Cosmos? Para que la latencia de red se a óptima, la función de Azure y su cuenta de Azure Cosmos deben estar colocadas en la misma región de Azure.
2. ¿Se producen los cambios en el contenedor de Azure Cosmos de forma continua o esporádica?
Si ocurre lo segundo, podría haber algún retraso entre los cambios que se almacenan y el momento en que la función de Azure los recoge. Esto se debe a que, internamente, cuando el desencadenador comprueba si hay cambios en el contenedor de Azure Cosmos y no encuentra ninguno pendientes para leerse, se suspende durante un periodo (5 segundos, de forma predeterminada) configurable antes de comprobar si hay nuevos cambios (para evitar un consumo elevado de RU). Puede configurar este tiempo de suspensión en la opción `FeedPollDelay/feedPollDelay` de la [configuración](../../azure-functions/functions-bindings-cosmosdb-v2-trigger.md#configuration) del desencadenador (el valor debe especificarse en milisegundos).
3. El contenedor de Azure Cosmos podría tener una [velocidad limitada](../request-units.md).
4. Puede usar el atributo `PreferredLocations` en el desencadenador para especificar una lista separada por comas de las regiones de Azure y definir un orden personalizado preferido de conexión.

### <a name="some-changes-are-repeated-in-my-trigger"></a>Algunos cambios se repiten en el desencadenador

El concepto de "cambio" es una operación en un documento. Los escenarios más comunes en los que se reciben eventos para el mismo documento son:
* La cuenta utiliza una posible coherencia. Durante el consumo de la fuente de cambios en un nivel de posible coherencia, podría haber eventos duplicados entre las operaciones de lectura de fuente de cambios posteriores (el último evento de una operación de lectura aparece como el primero de la siguiente).
* El documento se está actualizando. La fuente de cambios puede contener varias operaciones para los mismos documentos, si ese documento recibe actualizaciones, puede recoger varios eventos (uno para cada actualización). Una manera fácil de distinguir entre las distintas operaciones del mismo documento es realizar un seguimiento de la propiedad `_lsn` [para cada cambio](../change-feed.md#change-feed-and-_etag-_lsn-or-_ts). Si no coinciden, se trata de cambios diferentes en el mismo documento.
* Si está identificando documentos simplemente por el `id`, recuerde que el identificador único de un documento es el `id` y su clave de partición (puede haber dos documentos con el mismo `id` pero con una clave de partición diferente).

### <a name="some-changes-are-missing-in-my-trigger"></a>Faltan algunos cambios en mi desencadenador

Si observa que algunos de los cambios que se produjeron en el contenedor de Azure Cosmos no están recogidos por la función de Azure o que faltan algunos cambios en el destino al copiarlos, siga los pasos que se indican a continuación.

Cuando la función de Azure recibe los cambios, a menudo los procesa y, opcionalmente, envía el resultado a otro destino. Cuando investigue los cambios que faltan, asegúrese de **medir los cambios que se reciben en el punto de ingesta** (cuando se inicia la función de Azure), no en el destino.

Si faltan algunos cambios en el destino, esto podría significar que se trata de algún error que sucede durante la ejecución de la función de Azure después de recibir los cambios.

En este escenario, la mejor forma de proceder consiste en agregar bloques `try/catch` en el código y dentro de los bucles que podrían estar procesando los cambios, con el fin de detectar errores en un subconjunto determinado de elementos y administrarlos según corresponda (enviarlos a otro almacenamiento adicional para seguir con el análisis o volver a intentarlo).

> [!NOTE]
> El desencadenador de Azure Functions para Cosmos DB, de forma predeterminada, no volverá a intentar un lote de cambios si se ha producido una excepción no controlada durante la ejecución del código. Esto significa que la razón por la que no han llegado los cambios al destino es que se producen errores al procesarlos.

Si el destino es otro contenedor de Cosmos y realiza operaciones Upsert para copiar los elementos, **compruebe que la definición de la clave de partición en el contenedor supervisado y en el de destino es la misma**. Las operaciones Upsert podrían guardar varios elementos de origen como uno solo en el destino debido a esta diferencia de configuración.

Si aprecia que algunos cambios no se han recibido en el desencadenador, el escenario más común es que hay **otra Función de Azure en ejecución**. Es posible que otra función de Azure implementada en Azure o que se ejecute localmente en la máquina del desarrollador tenga **exactamente la misma configuración** (mismos contenedores de concesión y supervisados), y esta función de Azure esté robando un subconjunto de los cambios que se espera que la función de Azure procesase.

Además, el escenario se puede validar, si sabe cuántas instancias de la aplicación de función de Azure están ejecutándose. Si inspecciona el contenedor de concesión y cuenta el número de elementos de concesión que contiene, los distintos valores de la propiedad `Owner` en ellos debería ser igual al número de instancias de la aplicación de función. Si hay más propietarios de las instancias de la aplicación de función de Azure conocidas, significa que estos propietarios adicionales son los que están "robando" los cambios.

Una manera fácil de solucionar esta situación es aplicar un `LeaseCollectionPrefix/leaseCollectionPrefix` a la función con un valor nuevo o diferente, o bien probar con un nuevo contenedor de concesiones.

### <a name="need-to-restart-and-reprocess-all-the-items-in-my-container-from-the-beginning"></a>Necesidad de reiniciar y volver a procesar todos los elementos del contenedor desde el principio 
Para volver a procesar todos los elementos de un contenedor desde el principio:
1. Detenga la función de Azure si se está ejecutando actualmente. 
1. Elimine los documentos de la colección de concesiones (o elimine y vuelva a crear la colección de concesiones para que esté vacía)
1. Establezca el atributo CosmosDBTrigger de [StartFromBeginning](../../azure-functions/functions-bindings-cosmosdb-v2-trigger.md#configuration) de la función en True. 
1. Reinicie la función de Azure. Ahora se leerán y procesarán todos los cambios desde el principio. 

Cuando se establece [StartFromBeginning](../../azure-functions/functions-bindings-cosmosdb-v2-trigger.md#configuration) en True, indica a la función de Azure que comience a leer los cambios desde el principio del historial de la colección en lugar de desde la hora actual. Esto solo funciona cuando no hay concesiones ya creadas (es decir, documentos en la colección de concesiones). Establecer esta propiedad en True cuando hay concesiones ya creadas no tiene ningún efecto; en este escenario, cuando una función se detiene y se reinicia, comenzará a leer desde el último punto de control, tal y como se define en la colección de concesiones. Para volver a procesar desde el principio, siga los pasos anteriores del 1 al 4.  

### <a name="binding-can-only-be-done-with-ireadonlylistdocument-or-jarray"></a>Solo se puede realizar el enlace con IReadOnlyList\<Document> o JArray

Este error se produce si el proyecto de Azure Functions (o cualquier proyecto al que se haga referencia) contiene una referencia de NuGet manual al SDK de Azure Cosmos DB con una versión diferente de la proporcionada por la [Extensión Cosmos DB de Azure Functions](./troubleshoot-changefeed-functions.md#dependencies).

Para solucionar esta situación, quite la referencia manual de NuGet que se agregó y deje que la referencia del SDK de Azure Cosmos DB se resuelva con el paquete de la Extensión de Azure Functions de Cosmos DB.

### <a name="changing-azure-functions-polling-interval-for-the-detecting-changes"></a>Cambio del intervalo de sondeo de Azure Functions para detectar cambios

Como se explicó anteriormente en [Mis cambios tardan demasiado en recibirse](#my-changes-take-too-long-to-be-received), la función de Azure se suspenderá durante un período configurable (5 segundos, de forma predeterminada) antes de comprobar si hay nuevos cambios (para evitar un consumo elevado de RU). Puede configurar este tiempo de suspensión en la opción `FeedPollDelay/feedPollDelay` de la [configuración](../../azure-functions/functions-bindings-cosmosdb-v2-trigger.md#configuration) del desencadenador (el valor debe especificarse en milisegundos).

## <a name="next-steps"></a>Pasos siguientes

* [Habilitación de la supervisión para Azure Functions](../../azure-functions/functions-monitoring.md)
* [Solución de problemas del SDK de .NET para Azure Cosmos DB](./troubleshoot-dot-net-sdk.md)
