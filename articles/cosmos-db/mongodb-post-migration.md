---
title: Pasos de optimización posteriores a la migración con la API de Azure Cosmos DB para MongoDB
description: En este documento se proporcionan las técnicas de optimización posteriores a la migración de MongoDB a la API de Azure Cosmos DB para MongoDB.
author: christopheranderson
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.topic: how-to
ms.date: 05/19/2021
ms.author: chrande
ms.openlocfilehash: a0e072df074da5df238ab6047ee659c91ff3fdde
ms.sourcegitcommit: 80d311abffb2d9a457333bcca898dfae830ea1b4
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 05/26/2021
ms.locfileid: "110471164"
---
# <a name="post-migration-optimization-steps-when-using-azure-cosmos-dbs-api-for-mongodb"></a>Pasos de optimización posteriores a la migración cuando se usa la API de Azure Cosmos DB para MongoDB
[!INCLUDE[appliesto-mongodb-api](includes/appliesto-mongodb-api.md)]

> [!IMPORTANT]  
> Lea toda esta guía antes de llevar a cabo los pasos posteriores a la migración.
>

Esta guía de migración posterior de MongoDB forma parte de la serie sobre la migración de MongoDB. Los pasos críticos de la migración de MongoDB son la [migración previa](mongodb-pre-migration.md), la migración y la migración posterior, como se muestra a continuación.

![Diagrama de pasos de migración.](./media/mongodb-pre-migration/overall-migration-steps.png)

## <a name="overview-of-post-migration"></a>Información general sobre la migración posterior

Después de migrar los datos almacenados en la base de datos de MongoDB a la API de Azure Cosmos DB para MongoDB, puede conectarse a Azure Cosmos DB y administrar los datos. En esta guía se proporcionan los pasos que se deben tener en cuenta después de la migración. Para seguir los pasos de migración, consulte [Tutorial de migración de MongoDB a la API de Azure Cosmos DB para MongoDB](../dms/tutorial-mongodb-cosmos-db.md).

Siga estos pasos para realizar una migración posterior:

- [Optimizar la directiva de indexación](#optimize-the-indexing-policy)
- [Configurar la distribución global para la API de Azure Cosmos DB para MongoDB](#globally-distribute-your-data)
- [Establecer un nivel de coherencia](#set-consistency-level)
- [Conectar (migrar totalmente) la aplicación](#connect-or-cutover-your-application)
- [Realizar ajustes para lograr un rendimiento óptimo](#tune-for-optimal-performance)

> [!NOTE]
> El único paso obligatorio posterior a la migración es cambiar la cadena de conexión en la aplicación para que apunte a la nueva cuenta de Azure Cosmos DB. Todos los demás pasos posteriores a la migración son optimizaciones recomendadas para mejorar el rendimiento de la capa de datos. *Sin embargo*, si realiza la migración total de la aplicación inmediatamente sin los otros pasos, la aplicación notará inmediatamente el impacto de tener una indexación y una coherencia que no son óptimas. En concreto, si realiza la migración total antes de configurar los índices, es posible que la aplicación observe una caída inmediata del precio y el rendimiento. Tenga en cuenta que esto se puede [corregir: ](#optimize-the-indexing-policy)una vez optimizado el índice, se prevé que Azure Cosmos DB a menudo mejore los resultados de la solución existente en precio o rendimiento.
>

## <a name="pre-requisites"></a>Requisitos previos

En esta guía, se supone que mantiene un registro del progreso de la migración mediante algún tipo de artefacto de seguimiento, como una hoja de cálculo. Si aún no lo ha hecho, se recomienda que lea la [guía previa a la migración](mongodb-pre-migration.md) para obtener instrucciones sobre cómo crear una *hoja de cálculo de migración del patrimonio de datos*, detectar los recursos de MongoDB existentes y planear la migración.

## <a name="optimize-the-indexing-policy"></a>Optimización de la directiva de indexación

Para optimizar el precio y el rendimiento, se recomienda seguir paso a paso la hoja de cálculo de migración del patrimonio de datos y diseñar una configuración de índice para cada recurso. 
1. En realidad, se recomienda [planear los índices durante la fase previa a la migración](mongodb-pre-migration.md#post-migration). Agregue una columna a la hoja de cálculo de migración del patrimonio de datos para la configuración del índice. 
   * La API de Azure Cosmos DB para el servidor MongoDB versión 3.6 y posteriores solo indexa automáticamente el campo _id. Este campo no se puede quitar. Aplica automáticamente la unicidad del campo _id por clave de partición. Para indexar campos adicionales, se aplican los comandos de administración de índices de MongoDB. Esta directiva de indexación predeterminada difiere de la API SQL de Azure Cosmos DB, que indexa todos los campos de manera predeterminada.

   * En el caso de la API de Azure Cosmos DB para el servidor de MongoDB versión 3.2, todos los campos de datos se indexan automáticamente, de forma predeterminada, durante la migración de datos a Azure Cosmos DB. En muchos casos, esta directiva de indexación predeterminada es aceptable. En general, eliminar los índices optimiza las solicitudes de escritura y tener la directiva de indexación predeterminada (es decir, la indexación automática) optimiza las solicitudes de lectura.

   * Entre las funcionalidades de indexación proporcionadas por Azure Cosmos DB se incluye la adición de índices compuestos, índices únicos e índices del período de vida (TTL). La interfaz de administración de índices se asigna al comando createIndex(). Obtenga más información en Indexación en Azure Cosmos DB e Indexación en la API de Azure Cosmos DB para MongoDB.
2. Aplique esta configuración de índice durante la migración posterior.
   * [Azure Database Migration Service](../dms/tutorial-mongodb-cosmos-db.md) migra automáticamente las colecciones de MongoDB con índices únicos. Sin embargo, los índices únicos deben crearse antes de la migración. Azure Cosmos DB no admite la creación de índices únicos cuando ya hay datos en las colecciones. Para más información, consulte [Claves únicas en Azure Cosmos DB](unique-keys.md).

## <a name="globally-distribute-your-data"></a>Distribución global de los datos

Azure Cosmos DB está disponible en todas las [regiones de Azure](https://azure.microsoft.com/regions/#services) globales. 
1. Siga las instrucciones del artículo [Distribución global de datos en la API de Azure Cosmos DB para MongoDB](tutorial-global-distribution-mongodb.md) con el fin de distribuir los datos globalmente. Tras seleccionar el nivel de coherencia predeterminado para la cuenta de Azure Cosmos DB, puede asociar una o varias regiones de Azure (según las necesidades de distribución global). Para una alta disponibilidad y una continuidad del negocio, siempre se recomienda que se ejecute en al menos 2 regiones. Puede revisar las sugerencias para [optimizar el costo de las implementaciones en varias regiones de Azure Cosmos DB](optimize-cost-regions.md).

## <a name="set-consistency-level"></a>Establecimiento de un nivel de coherencia

Azure Cosmos DB ofrece 5 [niveles de coherencia](consistency-levels.md) bien definidos. Para obtener más información sobre la asignación entre MongoDB y los niveles de coherencia de Azure Cosmos DB, consulte [Niveles de coherencia y API de Cosmos DB](./consistency-levels.md). El nivel de coherencia predeterminado es el de la sesión. Cambiar el nivel de coherencia es opcional y se puede optimizar para la aplicación. Para cambiar el nivel de coherencia mediante Azure Portal:

1. En Configuración, vaya a la hoja **Coherencia predeterminada**.
2. Seleccione el [nivel de coherencia](consistency-levels.md).

La mayoría de usuarios dejan el nivel de coherencia en el valor predeterminado de coherencia de la sesión. Sin embargo, hay [compromisos entre rendimiento y disponibilidad en los distintos niveles de coherencia](./consistency-levels.md).

## <a name="connect-or-cutover-your-application"></a>Conexión o migración total de la aplicación

El procesamiento de migrar totalmente o conectar la aplicación le permite cambiar la aplicación para que use Azure Cosmos DB una vez finalizada la migración. Siga estos pasos:

1. En una nueva ventana, inicie sesión en [Azure Portal](https://www.portal.azure.com/).
2. En [Azure Portal](https://www.portal.azure.com/), en el panel izquierdo abra el menú **Todos los recursos** y busque la cuenta de Azure Cosmos DB a la que se han migrado los datos.
3. Abra la hoja **Cadena de conexión**. El panel derecho contiene toda la información que necesita para conectarse correctamente a la cuenta.
4. Use la información de conexión en la configuración de la aplicación (o en otros lugares pertinentes) para reflejar la API de Azure Cosmos DB para la conexión de MongoDB en la aplicación.
:::image type="content" source="./media/mongodb-post-migration/connection-string.png" alt-text="Captura de pantalla que muestra la configuración de una cadena de conexión.":::

Para obtener más información, consulte la página [Conectar una aplicación de MongoDB a Azure Cosmos DB](connect-mongodb-account.md).

## <a name="tune-for-optimal-performance"></a>Ajuste para lograr un rendimiento óptimo

Un dato práctico sobre la [indexación](#optimize-the-indexing-policy), la [distribución global](#globally-distribute-your-data) y la [coherencia](#set-consistency-level): todas estas tareas se pueden configurar y volver a configurar fácilmente según sea necesario. Por lo tanto, una vez que termine la migración total de la aplicación, se recomienda supervisar el rendimiento de la aplicación y ajustar esta configuración según sea necesario para satisfacer los requisitos de la aplicación.

## <a name="next-steps"></a>Pasos siguientes

* [Conectar una aplicación de MongoDB a Azure Cosmos DB](connect-mongodb-account.md)
* [Conexión a una cuenta de Azure Cosmos DB con Studio 3T](mongodb-mongochef.md)
* [Cómo distribuir las lecturas globalmente con la API de Azure Cosmos DB para MongoDB](mongodb-readpreference.md)
* [Expiración de los datos con la API de Azure Cosmos DB para MongoDB](mongodb-time-to-live.md)
* [Niveles de coherencia en Azure Cosmos DB](consistency-levels.md)
* [Indexación en Azure Cosmos DB](index-overview.md)
* [Unidades de solicitud en Azure Cosmos DB](request-units.md)