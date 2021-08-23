---
title: Modelo de recursos de Azure Cosmos DB
description: En este artículo se describe el modelo de recursos de Azure Cosmos DB que incluye la cuenta, la base de datos, el contenedor y los elementos de Azure Cosmos. También muestra la jerarquía de estos elementos en una cuenta de Azure Cosmos.
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 10/12/2020
ms.reviewer: sngun
ms.openlocfilehash: f714a70a0d9eb971498fc2f9fe648804a59b5136
ms.sourcegitcommit: 80d311abffb2d9a457333bcca898dfae830ea1b4
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 05/26/2021
ms.locfileid: "110475020"
---
# <a name="azure-cosmos-db-resource-model"></a>Modelo de recursos de Azure Cosmos DB
[!INCLUDE[appliesto-all-apis](includes/appliesto-all-apis.md)]

Azure Cosmos DB es una plataforma como servicio (PaaS) totalmente administrada. Para empezar a usar Azure Cosmos DB, debe crear primero una cuenta de Azure Cosmos en su grupo de recursos de Azure en la suscripción necesaria y, a continuación, incluir en esta bases de datos, contenedores y elementos. En este artículo se describe el modelo de recursos de Azure Cosmos DB y las distintas entidades de la jerarquía del modelo de recursos.

La cuenta de Azure Cosmos es la unidad fundamental de distribución global y alta disponibilidad. La cuenta de Azure Cosmos contiene un nombre DNS único y se puede administrar mediante Azure Portal, la CLI de Azure o por medio de diferentes SDK específicos del idioma. Para más información, consulte [cómo administrar su cuenta de Azure Cosmos](how-to-manage-database-account.md). Para distribuir globalmente los datos y el rendimiento entre varias regiones de Azure, puede agregar y quitar regiones de Azure en su cuenta en cualquier momento. Puede configurar la cuenta para que tenga una única región de escritura o varias. Para más información, consulte [como agregar y quitar regiones de Azure en la cuenta](how-to-manage-database-account.md). Puede configurar el [nivel de coherencia predeterminado](consistency-levels.md) de la cuenta.

## <a name="elements-in-an-azure-cosmos-account"></a>Elementos de una cuenta de Azure Cosmos

Un contenedor de Azure Cosmos es la unidad fundamental de escalabilidad. Prácticamente puede tener un rendimiento aprovisionado (RU/s) y un almacenamiento ilimitado en un contenedor. Azure Cosmos DB realiza particiones de forma transparente en el contenedor mediante la clave de partición lógica que especifique para escalar elásticamente el rendimiento y el almacenamiento aprovisionados.

Actualmente, puede crear un máximo de 50 cuentas de Azure Cosmos en una suscripción de Azure (este es un límite flexible que se puede aumentar a través de una solicitud de soporte técnico). Una sola cuenta de Azure Cosmos puede administrar una cantidad casi ilimitada de datos y rendimiento aprovisionado. Para administrar los datos y el rendimiento aprovisionado, puede crear una o varias bases de datos de Azure Cosmos en su cuenta y, dentro de esa base de datos, puede crear uno o varios contenedores. La siguiente imagen muestra la jerarquía de elementos en una cuenta de Azure Cosmos:

:::image type="content" source="./media/account-databases-containers-items/hierarchy.png" alt-text="Jerarquía de una cuenta de Azure Cosmos" border="false":::

Después de crear una cuenta en su suscripción a Azure, puede administrar los datos en la cuenta mediante la creación de bases de datos, contenedores y elementos. 

En la siguiente imagen se muestra la jerarquía de diferentes entidades en una cuenta de Azure Cosmos DB:

:::image type="content" source="./media/account-databases-containers-items/cosmos-entities.png" alt-text="Entidades de cuenta de Azure Cosmos" border="false":::

## <a name="azure-cosmos-databases"></a>Bases de datos de Azure Cosmos

Puede crear una o varias bases de datos de Azure Cosmos en su cuenta. Una base de datos es análoga a un espacio de nombres. Una base de datos es la unidad de administración de un conjunto de contenedores de Azure Cosmos. En la siguiente tabla se muestra la forma de asignar una base de datos a varias entidades específicas de API:

| Entidad de Azure Cosmos | API DE SQL | Cassandra API | Azure Cosmos DB API para MongoDB | API de Gremlin | Table API |
| --- | --- | --- | --- | --- | --- |
|Base de datos de Azure Cosmos | Base de datos | Espacio de claves | Base de datos | Base de datos | N/D |

> [!NOTE]
> Con las cuentas de Table API, cuando crea su primera tabla, también se crea automáticamente una base de datos predeterminada dentro de su cuenta de Azure Cosmos.

### <a name="operations-on-an-azure-cosmos-database"></a>Operaciones en una base de datos de Azure Cosmos

Puede interactuar con una base de datos de Azure Cosmos con API de Azure Cosmos, como se describe en la siguiente tabla:

| Operación | Azure CLI | API DE SQL | Cassandra API | Azure Cosmos DB API para MongoDB | API de Gremlin | Table API |
| --- | --- | --- | --- | --- | --- | --- |
|Enumerar todas las bases de datos| Sí | Sí | Sí (la base de datos está asignada a un espacio de claves). | Sí | N/D | N/D |
|Leer la base de datos| Sí | Sí | Sí (la base de datos está asignada a un espacio de claves). | Sí | N/D | N/D |
|Create new database| Sí | Sí | Sí (la base de datos está asignada a un espacio de claves). | Sí | N/D | N/D |
|Actualizar la base de datos| Sí | Sí | Sí (la base de datos está asignada a un espacio de claves). | Sí | N/D | N/D |

## <a name="azure-cosmos-containers"></a>Contenedores de Azure Cosmos

Un contenedor de Azure Cosmos es la unidad de escalabilidad del rendimiento y del almacenamiento aprovisionados. Un contenedor se divide de forma horizontal y luego se replica en varias regiones. Los elementos que se agregan al contenedor se agrupan automáticamente en particiones lógicas, que se distribuyen entre particiones físicas, en función de la clave de partición. El rendimiento de un contenedor se distribuye uniformemente entre las particiones físicas. Para aprender más sobre la creación de particiones y las claves de particiones, consulte [Partition data](partitioning-overview.md) (Creación de particiones de datos). 

Al crear un contenedor, debe configurar el rendimiento de uno de los siguientes modos:

* Modo de **rendimiento aprovisionado dedicado**: el rendimiento aprovisionado en un contenedor está reservado exclusivamente para ese contenedor y lo respaldan los contratos de nivel de servicio. Para obtener más información, consulte [Aprovisionamiento del rendimiento de un contenedor](how-to-provision-container-throughput.md).

* Modo de **rendimiento aprovisionado compartido**: estos contenedores comparten el rendimiento aprovisionado con los demás contenedores de la misma base de datos (excepto aquellos contenedores que han sido configurados con un rendimiento aprovisionado dedicado). En otras palabras, el rendimiento aprovisionado en la base de datos se comparte entre todos los contenedores de "rendimiento compartidos". Para obtener más información, consulte [Aprovisionamiento del rendimiento en una base de datos](how-to-provision-database-throughput.md).

> [!NOTE]
> Puede configurar el rendimiento compartido y dedicado solamente al crear la base de datos y el contenedor. Para cambiar del modo de rendimiento dedicado al modo de rendimiento compartido (y viceversa) después de crear el contenedor, debe crear un nuevo contenedor y migrar los datos al nuevo contenedor. Puede migrar los datos mediante la característica de fuente de cambios de Azure Cosmos DB.

Un contenedor de Azure Cosmos puede escalarse elásticamente, sin importar si crea contenedores mediante modos de rendimiento aprovisionado compartido o dedicado.

Un contenedor es un contenedor de elementos sin esquemas. Los elementos de un contenedor pueden tener esquemas arbitrarios. Por ejemplo, un elemento que representa a una persona y otro elemento que representa un automóvil se pueden colocar en el *mismo contenedor*. De forma predeterminada, todos los elementos que agregue a un contenedor se indexan automáticamente sin requerir ningún índice explícito ni administración de esquema. Puede personalizar el comportamiento de la indexación configurando la [directiva de indexación](index-overview.md) en un contenedor. 

Asimismo, puede configurar [Período de vida (TTL)](time-to-live.md) en los elementos seleccionados en un contenedor o para que todo el contenedor elimine esos elementos del sistema sin problemas. Azure Cosmos DB elimina automáticamente los elementos cuando estos expiran. También garantiza que una consulta realizada en el contenedor no devuelva los elementos que expiraron en un límite fijado. Para más información, consulte [Configure TTL on your container](how-to-time-to-live.md) (Configuración de TTL en el contenedor).

Puede usar la [fuente de cambios](change-feed.md) para suscribirse al registro de operaciones que se administra para cada partición lógica del contenedor. La fuente de cambios proporciona el registro de todas las actualizaciones realizadas en el contenedor junto con las imágenes de antes y después de los elementos. Para más información, consulte [Build reactive applications by using change feed](serverless-computing-database.md) (Compilación de aplicaciones reactivas mediante la fuente de cambios). También puede configurar la duración de la retención de la fuente de cambios usando la directiva de fuentes de cambios en el contenedor.

Puede registrar [procedimientos almacenados, desencadenadores, funciones que define el usuario (UDF)](stored-procedures-triggers-udfs.md) y [procedimientos de combinación](how-to-manage-conflicts.md) con su contenedor.

Puede especificar una [restricción de clave única](unique-keys.md) en el contenedor de Azure Cosmos. Al crear una directiva de clave única, garantiza la exclusividad de uno o varios valores por clave de partición lógica. Si crea un contenedor con una directiva de clave única, no se pueden crear elementos nuevos o actualizados con valores que dupliquen los valores que haya especificado la restricción de clave única. Para obtener más información, consulte [Unique key constraints](unique-keys.md) (Restricciones de clave única).

Un contenedor está especializado en entidades específicas de API como se muestra en la siguiente tabla:

| Entidad de Azure Cosmos | API DE SQL | Cassandra API | Azure Cosmos DB API para MongoDB | API de Gremlin | Table API |
| --- | --- | --- | --- | --- | --- |
|Contenedor de Azure Cosmos | Contenedor | Tabla | Colección | Grafo | Tabla |

> [!NOTE]
> Cuando cree contenedores, asegúrese de no utilizar el mismo nombre en dos de ellos con distintas mayúsculas y minúsculas. Algunos componentes de la plataforma de Azure no distinguen mayúsculas de minúsculas y esto puede producir confusión o problemas con los datos telemetría y las acciones que se realicen en los contenedores con estos nombres.

### <a name="properties-of-an-azure-cosmos-container"></a>Propiedades de un contenedor de Azure Cosmos

Un contenedor de Azure Cosmos tiene un conjunto de propiedades definidas por el sistema. Según la API que use, puede que algunas propiedades no se expongan directamente. En la siguiente tabla se describe la lista de propiedades definidas por el sistema:

| Propiedad definida por el sistema | Generada por el sistema o configurada por el usuario | Propósito | API DE SQL | Cassandra API | Azure Cosmos DB API para MongoDB | API de Gremlin | Table API |
| --- | --- | --- | --- | --- | --- | --- | --- |
|\_rid | Generada por el sistema | Identificador único del contenedor | Sí | No | No | No | No |
|\_etag | Generada por el sistema | Etiqueta de entidad que se usa para el control de simultaneidad optimista | Sí | No | No | No | No |
|\_ts | Generada por el sistema | Última actualización de la marca de tiempo del contenedor | Sí | No | No | No | No |
|\_self | Generada por el sistema | URI direccionable del contenedor | Sí | No | No | No | No |
|id | Configurada por el usuario | Nombre único que ha definido el usuario del contenedor | Sí | Sí | Sí | Sí | Sí |
|indexingPolicy | Configurada por el usuario | Permite cambiar la ruta de acceso, el tipo y el modo del índice. | Sí | No | No | No | Sí |
|timeToLive | Configurada por el usuario | Permite eliminar automáticamente elementos de un contenedor después de un período de tiempo establecido. Para más información, consulte [Período de vida](time-to-live.md). | Sí | No | No | No | Sí |
|changeFeedPolicy | Configurada por el usuario | Se usa para leer los cambios realizados en los elementos de un contenedor. Para más información, consulte [Fuente de cambios](change-feed.md). | Sí | No | No | No | Sí |
|uniqueKeyPolicy | Configurada por el usuario | Se usa para garantizar la exclusividad de uno o varios valores dentro de una partición lógica. Para más información, consulte [Restricciones de clave únicas](unique-keys.md). | Sí | No | No | No | Sí |

### <a name="operations-on-an-azure-cosmos-container"></a>Operaciones en un contenedor de Azure Cosmos

Un contenedor de Azure Cosmos es compatible con las siguientes operaciones al usar cualquiera de las API de Azure Cosmos:

| Operación | Azure CLI | API DE SQL | Cassandra API | Azure Cosmos DB API para MongoDB | API de Gremlin | Table API |
| --- | --- | --- | --- | --- | --- | --- |
| Enumerar los contenedores en una base de datos | Sí | Sí | Sí | Sí | N/D | N/D |
| Leer un contenedor | Sí | Sí | Sí | Sí | N/D | N/D |
| Crear un contenedor nuevo | Sí | Sí | Sí | Sí | N/D | N/D |
| Actualizar un contenedor | Sí | Sí | Sí | Sí | N/D | N/D |
| Eliminación de un contenedor | Sí | Sí | Sí | Sí | N/D | N/D |

## <a name="azure-cosmos-items"></a>Elementos de Azure Cosmos

En función de la API que use, un elemento de Azure Cosmos puede representar un documento en una colección, una fila en una tabla o un nodo o un borde en un grafo. En la tabla siguiente se muestra la asignación de entidades específicas de API para un elemento de Azure Cosmos:

| Entidad de Cosmos | API DE SQL | Cassandra API | Azure Cosmos DB API para MongoDB | API de Gremlin | Table API |
| --- | --- | --- | --- | --- | --- |
|Elemento de Azure Cosmos | Elemento | Row | Documento | Nodo o borde | Elemento |

### <a name="properties-of-an-item"></a>Propiedades de un elemento

Cada elemento de Azure Cosmos tiene las siguientes propiedades definidas por el sistema. Según la API que use, puede que algunas no se expongan directamente.

| Propiedad definida por el sistema | Generada por el sistema o configurada por el usuario| Propósito | API DE SQL | Cassandra API | Azure Cosmos DB API para MongoDB | API de Gremlin | Table API |
| --- | --- | --- | --- | --- | --- | --- | --- |
|\_rid | Generada por el sistema | Identificador único del elemento | Sí | No | No | No | No |
|\_etag | Generada por el sistema | Etiqueta de entidad que se usa para el control de simultaneidad optimista | Sí | No | No | No | No |
|\_ts | Generada por el sistema | Marca de tiempo de la actualización más reciente del elemento | Sí | No | No | No | No |
|\_self | Generada por el sistema | URI direccionable del elemento | Sí | No | No | No | No |
|id | Es posible usar el | Nombre único que define el usuario en una partición lógica. | Sí | Sí | Sí | Sí | Sí |
|Propiedades arbitrarias que define el usuario | Definidas por el usuario | Propiedades definidas por el usuario que se representan en la representación de la API nativa (incluidos los formatos JSON, BSON y CQL). | Sí | Sí | Sí | Sí | Sí |

> [!NOTE]
> La unicidad de la propiedad `id` solo se aplica dentro de cada partición lógica. Varios documentos pueden tener la misma propiedad `id` con distintos valores de clave de partición.

### <a name="operations-on-items"></a>Operaciones en elementos

Los elementos de Azure Cosmos admiten las siguientes operaciones. Puede usar cualquiera de las API de Azure Cosmos para realizar las operaciones.

| Operación | Azure CLI | API DE SQL | Cassandra API | Azure Cosmos DB API para MongoDB | API de Gremlin | Table API |
| --- | --- | --- | --- | --- | --- | --- |
| Insertar, reemplazar, eliminar, upsert, leer | No | Sí | Sí | Sí | Sí | Sí |

## <a name="next-steps"></a>Pasos siguientes

Sepa cómo administrar su cuenta de Azure Cosmos y aprenda otros conceptos:

* [Administración de la cuenta de Azure Cosmos](how-to-manage-database-account.md)
* [Distribución global](distribute-data-globally.md)
* [Niveles de coherencia](consistency-levels.md)
* [Punto de conexión de servicio de red virtual para la cuenta de Azure Cosmos](how-to-configure-vnet-service-endpoint.md)
* [Firewall para direcciones IP para la cuenta de Azure Cosmos](how-to-configure-firewall.md)
* [Como agregar y quitar regiones de Azure en la cuenta de Azure Cosmos](how-to-manage-database-account.md).
* [Contratos de nivel de servicio para Azure Cosmos DB](https://azure.microsoft.com/support/legal/sla/cosmos-db/v1_2/)
