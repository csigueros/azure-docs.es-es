---
title: Modelos de diseño de la fuente de cambios de Azure Cosmos DB
description: Introducción a los modelos de diseño comunes de la fuente de cambios
author: timsander1
ms.author: tisande
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 08/26/2021
ms.openlocfilehash: df2836bf201ffcceed4c2b6b1bf9a767bc046f4d
ms.sourcegitcommit: dcf1defb393104f8afc6b707fc748e0ff4c81830
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/27/2021
ms.locfileid: "123113792"
---
# <a name="change-feed-design-patterns-in-azure-cosmos-db"></a>Modelos de diseño de la fuente de cambios de Azure Cosmos DB
[!INCLUDE[appliesto-sql-api](../includes/appliesto-sql-api.md)]

La fuente de cambios de Azure Cosmos DB permite un procesamiento eficaz de grandes conjuntos de datos con un elevado volumen de escritura. La fuente de cambios también ofrece una alternativa a las consultas de conjuntos de datos completos para identificar qué ha cambiado. Este documento se centra en los modelos de diseño comunes de la fuente de cambios, las compensaciones del diseño y las limitaciones de la fuente de cambios.

Azure Cosmos DB es adecuado para IoT, juegos y aplicaciones de registro de operaciones. Un modelo de diseño común en estas aplicaciones es usar los cambios en los datos para desencadenar acciones adicionales. Ejemplos de acciones adicionales:

* Desencadenamiento de una notificación o una llamada a una API cuando se inserta o actualiza un elemento.
* Procesamiento de secuencias en tiempo real para IoT o procesamiento de estadísticas sobre datos operativos en tiempo real.
* Movimiento de datos, como la sincronización con una memoria caché, un motor de búsqueda, un almacenamiento de datos o el almacenamiento en frío.

La fuente de cambios en Azure Cosmos DB le permite crear soluciones eficientes y escalables para cada uno de estos patrones, como se muestra en la imagen siguiente:

:::image type="content" source="../media/change-feed/changefeedoverview.png" alt-text="Uso de la fuente de cambios de Azure Cosmos DB para aumentar la eficacia de los escenarios de informática orientada a eventos y análisis en tiempo real" border="false":::

## <a name="event-computing-and-notifications"></a>Procesamiento de eventos y notificaciones

La fuente de cambios de Azure Cosmos DB puede simplificar los escenarios que necesitan desencadenar una notificación o enviar una llamada a una API en función de un evento determinado. Puede usar la [biblioteca de procesos de la fuente de cambios](change-feed-processor.md) para sondear automáticamente el contenedor en busca de cambios y llamar a una API externa cada vez que haya una escritura o una actualización.

También puede desencadenar una notificación de forma selectiva o enviar una llamada a una API en función de criterios específicos. Por ejemplo, si está leyendo desde la fuente de cambios mediante [Azure Functions](change-feed-functions.md), puede colocar la lógica en la función para enviar una notificación solo si se cumple un criterio específico. Aunque el código de Azure Functions se ejecutaría durante cada escritura y actualización, la notificación solo se enviaría si se cumpliesen determinados criterios.

## <a name="real-time-stream-processing"></a>Procesamiento de secuencias en tiempo real

La fuente de cambios de Azure Cosmos DB se puede usar para el procesamiento de secuencias en tiempo real para IoT o el procesamiento de estadísticas sobre datos operativos en tiempo real.
Por ejemplo, puede recibir y almacenar datos de eventos de dispositivos, sensores, infraestructura y aplicaciones, y luego procesarlos en tiempo real con [Spark](../../hdinsight/spark/apache-spark-overview.md). En la siguiente imagen se muestra cómo puede implementar la arquitectura lambda mediante Azure Cosmos DB a través de la fuente de cambios:

:::image type="content" source="../media/change-feed/lambda.png" alt-text="Canalización Lambda basada en Azure Cosmos DB para ingesta y consulta" border="false":::

En muchos casos, las implementaciones de procesamiento de secuencias reciben primero un gran volumen de datos entrantes en una cola de mensajes temporal, como Azure Event Hub o Apache Kafka. La fuente de cambios es una alternativa excelente debido a la capacidad de Azure Cosmos DB para admitir una alta tasa de ingesta de datos con una latencia de lectura y escritura baja garantizada. Las ventajas de la fuente de cambios de Azure Cosmos DB en una cola de mensajes incluyen:

### <a name="data-persistence"></a>Persistencia de los datos

Los datos escritos en Azure Cosmos DB se mostrarán en la fuente de cambios y se conservarán hasta que se eliminen. Las colas de mensajes suelen tener un período de retención máximo. Por ejemplo, el [Centro de eventos de Azure](https://azure.microsoft.com/services/event-hubs/) ofrece una retención de datos máxima de 90 días.

### <a name="querying-ability"></a>Capacidad de consulta

Además de leer la fuente de cambios de un contenedor de Cosmos, también puede ejecutar consultas SQL en los datos almacenados en Azure Cosmos DB. La fuente de cambios no es una duplicación de los datos que ya están en el contenedor, sino simplemente un mecanismo diferente para leer los datos. Por lo tanto, si lee los datos desde la fuente de cambios, siempre será coherente con las consultas del mismo contenedor de Azure Cosmos DB.

### <a name="high-availability"></a>Alta disponibilidad

Azure Cosmos DB ofrece hasta un 99,999 % de disponibilidad de lectura y escritura. A diferencia de muchas colas de mensajes, los datos de Azure Cosmos DB se pueden distribuir y configurar globalmente de forma sencilla con un [RTO (objetivo de tiempo de recuperación)](../consistency-levels.md#rto) de cero.

Después de procesar los elementos de la fuente de cambios, puede compilar una vista materializada y conservar los valores agregados de nuevo en Azure Cosmos DB. Si usa Azure Cosmos DB para compilar un juego, puede usar la fuente de cambios para, por ejemplo, implementar marcadores en tiempo real basados en las puntuaciones de los juegos completados.

## <a name="data-movement"></a>Movimiento de datos

También puede leer desde la fuente de cambios para el movimiento de datos en tiempo real.

Por ejemplo, la fuente de cambios le ayuda a realizar las siguientes tareas de manera eficaz:

* Actualizar una memoria caché, un índice de búsqueda o un almacenamiento de datos con los datos almacenados en Azure Cosmos DB.

* Realizar migraciones sin tiempos de inactividad a otra cuenta de Cosmos Azure o a otro contenedor de Azure Cosmos con una clave de partición lógica diferente.

* Implemente un archivado y una capa de datos de nivel de aplicación. Por ejemplo, puede almacenar "datos de acceso frecuente" en Azure Cosmos DB y quitar "datos inactivos" en otros sistemas de almacenamiento, como [Azure Blob Storage](../../storage/common/storage-introduction.md).

Si tiene que [desnormalizar los datos entre las particiones y los contenedores](how-to-model-partition-example.md#v2-introducing-denormalization-to-optimize-read-queries
), puede leer desde la fuente de cambios del contenedor como origen de esta replicación de datos. La replicación de datos en tiempo real con la fuente de cambios solo puede garantizar la coherencia final. Puede [supervisar hasta qué punto el procesador de la fuente de cambios se retrasa](how-to-use-change-feed-estimator.md) en el procesamiento de cambios en el contenedor de Cosmos.

## <a name="event-sourcing"></a>Aprovisionamiento de eventos

El [patrón de aprovisionamiento de eventos](/azure/architecture/patterns/event-sourcing) implica el uso de un almacén de solo anexar para registrar la serie completa de acciones en esos datos. La fuente de cambios de Azure Cosmos DB es una excelente opción como almacén de datos central en las arquitecturas de aprovisionamiento de eventos, donde toda la ingesta de datos se modela como escritura (sin actualizaciones ni eliminaciones). En este caso, cada escritura en Azure Cosmos DB es un "evento" y tendrá un registro completo de los eventos pasados en la fuente de cambios. Los usos habituales de los eventos publicados por el almacén de eventos central son para mantener vistas materializadas o para la integración con sistemas externos. Dado que no hay ningún límite de tiempo para la retención en la fuente de cambios, puede reproducir todos los eventos pasados leyendo desde el principio de la fuente de cambios del contenedor de Cosmos.

Puede hacer que [varios consumidores de fuentes de cambios se suscriban a la misma fuente de cambios del contenedor](how-to-create-multiple-cosmos-db-triggers.md#optimizing-containers-for-multiple-triggers). Aparte del rendimiento aprovisionado del [contenedor de concesión](change-feed-processor.md#components-of-the-change-feed-processor), no hay ningún costo por el uso de la fuente de cambios. La fuente de cambios está disponible en cada contenedor, independientemente de si se ha utilizado.

Azure Cosmos DB es un excelente almacén de datos persistentes central de solo anexar en el patrón de aprovisionamiento de eventos debido a sus ventajas en la escalabilidad horizontal y alta disponibilidad. Además, la biblioteca de procesadores de fuente de cambios ofrece una garantía de ["al menos una vez"](change-feed-processor.md#error-handling), lo que garantiza que no se perderá el procesamiento de los eventos.

## <a name="current-limitations"></a>Limitaciones actuales

La fuente de cambios tiene limitaciones importantes que debe comprender. Aunque los elementos de un contenedor de Cosmos permanecerán siempre en la fuente de cambios, esta no es un registro de operaciones completo. Hay áreas importantes que se deben tener en cuenta al diseñar una aplicación que use la fuente de cambios.

### <a name="intermediate-updates"></a>Actualizaciones intermedias

Solo el cambio más reciente en un elemento determinado se incluye en la fuente de cambios. Al procesar los cambios, leerá la última versión disponible del elemento. Si hay varias actualizaciones para el mismo elemento en un breve período de tiempo, es posible que se pierda el procesamiento de las actualizaciones intermedias. Si quiere realizar un seguimiento de las actualizaciones y poder volver a reproducir las actualizaciones anteriores de un elemento, se recomienda el modelado de estas actualizaciones como una serie de escrituras.

### <a name="deletes"></a>Eliminaciones

La fuente de cambios no captura las eliminaciones. Si elimina un elemento del contenedor, también se quita de la fuente de cambios. El método más común para controlar esto consiste en agregar un marcador flexible en los elementos que se van a eliminar. Puede agregar una propiedad denominada "deleted" y establecerla en "true" en el momento de la eliminación. Esta actualización del documento se mostrará en la fuente de cambios. Puede establecer un TTL en este elemento para que se pueda eliminar automáticamente más adelante.

### <a name="guaranteed-order"></a>Orden garantizado

Existe un orden garantizado en la fuente de cambios dentro de un valor de clave de partición, pero no en todos los valores de clave de partición. Debe seleccionar una clave de partición que proporcione una garantía de orden significativa.

Por ejemplo, considere que una aplicación comercial usa el modelo de diseño de aprovisionamiento de eventos. En esta aplicación, las distintas acciones de usuario son cada uno de los "eventos" que se modelan como escrituras en Azure Cosmos DB. Imagine si algunos eventos de ejemplo se producen en la secuencia siguiente:

1. El cliente agrega el elemento A al carro de la compra.
2. El cliente agrega el elemento B al carro de la compra.
3. El cliente elimina el elemento A del carro de la compra.
4. El cliente paga y se envía el contenido del carro de la compra.

Se mantiene una vista materializada del contenido del carro de la compra actual para cada cliente. Esta aplicación debe asegurarse de que estos eventos se procesen en el orden en que se producen. Si, por ejemplo, el pago del carro se hubiera procesado antes de la eliminación del elemento A, es probable que se hubiese enviado el elemento A al cliente, en lugar del elemento B deseado. Con el fin de garantizar que estos cuatro eventos se procesen en orden de aparición, deben estar dentro del mismo valor de clave de partición. Si selecciona **nombre de usuario** (cada cliente tiene un nombre de usuario único) como clave de partición, puede garantizar que estos eventos aparezcan en la fuente de cambios en el mismo orden en el que se escriben en Azure Cosmos DB.

## <a name="examples"></a>Ejemplos

Estos son algunos ejemplos reales de código de fuente de cambios que van más allá del ámbito de los ejemplos proporcionados en Microsoft Docs:

- [Introducción a la fuente de cambios](https://azurecosmosdb.github.io/labs/dotnet/labs/08-change_feed_with_azure_functions.html)
- [Caso de uso de IoT centrado en torno a la fuente de cambios](https://github.com/AzureCosmosDB/scenario-based-labs)
- [Caso de uso de Retail centrado en torno a la fuente de cambios](https://github.com/AzureCosmosDB/scenario-based-labs)

## <a name="next-steps"></a>Pasos siguientes

* [Introducción a la fuente de cambios](../change-feed.md)
* [Options to read change feed](read-change-feed.md) (Opciones para leer la fuente de cambios)
* [Using change feed with Azure Functions](change-feed-functions.md) (Usar la fuente de cambios con Azure Functions)
* ¿Intenta planear la capacidad de una migración a Azure Cosmos DB? Para ello, puede usar información sobre el clúster de base de datos existente.
    * Si lo único que sabe es el número de núcleos virtuales y servidores del clúster de bases de datos existente, lea sobre el [cálculo de unidades de solicitud mediante núcleos o CPU virtuales](../convert-vcore-to-request-unit.md). 
    * Si conoce las tasas de solicitudes típicas de la carga de trabajo de la base de datos actual, obtenga información sobre el [cálculo de unidades de solicitud mediante la herramienta de planeamiento de capacidad de Azure Cosmos DB](estimate-ru-with-capacity-planner.md).