---
title: Tipos de resolución de conflictos y directivas de resolución en Azure Cosmos DB
description: En este artículo se describen las categorías de conflicto y las directivas de resolución de conflictos de Azure Cosmos DB.
author: markjbrown
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 04/20/2020
ms.author: mjbrown
ms.reviewer: sngun
ms.openlocfilehash: aa21a1a6d6dfdd89f6532159a8da98fc5df08465
ms.sourcegitcommit: 91fdedcb190c0753180be8dc7db4b1d6da9854a1
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 06/17/2021
ms.locfileid: "112281375"
---
# <a name="conflict-types-and-resolution-policies-when-using-multiple-write-regions"></a>Tipos de conflicto y directivas de resolución al usar varias regiones de escritura
[!INCLUDE[appliesto-all-apis](includes/appliesto-all-apis.md)]

Los conflictos y las directivas de resolución de conflictos son aplicables si la cuenta de Azure Cosmos DB está configurada con varias regiones de escritura.

En el caso de las cuentas de Azure Cosmos configuradas con varias regiones de escritura, se pueden producir conflictos de actualización cuando los objetos de escritura actualizan simultáneamente el mismo elemento en varias regiones. Los conflictos de actualización pueden ser de los tres tipos siguientes:

* **Conflictos de inserción**: Estos conflictos pueden producirse cuando una aplicación inserta simultáneamente dos o más elementos con un mismo índice único en dos o más regiones. Por ejemplo, podría producirse este conflicto con una propiedad de identificador.

* **Conflictos de reemplazo**: Estos conflictos pueden producirse cuando una aplicación actualiza simultáneamente el mismo elemento en dos o más regiones.

* **Conflictos de eliminación**: Estos conflictos pueden producirse cuando una aplicación elimina simultáneamente un elemento de una región y lo actualiza en cualquier otra región.

## <a name="conflict-resolution-policies"></a>Directivas de resolución de conflictos

Azure Cosmos DB ofrece un mecanismo flexible controlado por directivas para resolver conflictos de escritura. En un contenedor de Azure Cosmos, puede seleccionar una de las dos directivas de resolución de conflictos siguientes:

* **Últimos casos de escritura correcta (LWW)** : esta directiva de resolución utiliza de forma predeterminada una propiedad de marca de tiempo definida por el sistema. (que se basa en el protocolo de reloj de sincronización de hora). Si usa la API de SQL, puede especificar cualquier otra propiedad numérica personalizada (por ejemplo, su propia noción de una marca de tiempo) que se usará para la resolución de conflictos. A las propiedades numéricas personalizadas también se les denominan *ruta de acceso para la resolución de conflictos*. 

  Si dos o más elementos entran en conflicto en operaciones de inserción o de reemplazo, el elemento que contiene el valor más alto para la ruta de acceso para la resolución de conflictos se convierte en el ganador. Si varios elementos tienen el mismo valor numérico para la ruta de acceso para la resolución de conflictos, el sistema determina la versión ganadora. Todas las regiones convergirán en un solo ganador y finalizarán con la versión idéntica del elemento confirmado. En caso de que haya conflictos de eliminación, la versión eliminada siempre se impone a otros conflictos, ya sean de inserción o de reemplazo, Este resultado tiene lugar independientemente del valor de la ruta de acceso para la resolución de conflictos.

  > [!NOTE]
  > Últimos casos de escritura correcta es la directiva de resolución de conflictos predeterminada y usa la marca de tiempo `_ts` para estas API: SQL, MongoDB, Cassandra, Gremlin y Table. La propiedad numérica personalizada solo está disponible para la API SQL.

  Para obtener más información, consulte los [ejemplos de uso de directivas de resolución de conflictos LWW](how-to-manage-conflicts.md).

* **Personalizado**: esta directiva de resolución se ha diseñado para la conciliación de conflictos con la semántica definida por la aplicación. Al establecer esta directiva en el contenedor de Azure Cosmos, debe registrar también un *procedimiento almacenado de combinación*. Este se invoca automáticamente cuando se detectan conflictos en una transacción de base de datos en el servidor. El sistema garantiza exactamente una vez la ejecución de un procedimiento de combinación como parte del protocolo de confirmación.  

  Si configura el contenedor con la opción de resolución personalizada y no registra un procedimiento de combinación en el contenedor o procedimiento de combinación lanza una excepción en tiempo de ejecución, los conflictos se escriben en la *fuente de conflictos*. La aplicación tendrá luego que resolver manualmente los conflictos en la fuente de conflictos. Para obtener más información, consulte los [ejemplos de uso de la directiva de resolución personalizada y la fuente de conflictos](how-to-manage-conflicts.md).

  > [!NOTE]
  > La directiva de resolución de conflictos personalizada solo está disponible para las cuentas de API de SQL y solo se puede establecer en el momento de la creación. No es posible establecer una directiva de resolución personalizada en un contenedor existente.

## <a name="next-steps"></a>Pasos siguientes

Obtenga información sobre cómo configurar directivas de resolución de conflictos:

* [Configuración de varias regiones de escritura para las aplicaciones](how-to-multi-master.md)
* [Administración de directivas de resolución de conflictos](how-to-manage-conflicts.md)
* [Lectura desde la fuente de conflictos](how-to-manage-conflicts.md#read-from-conflict-feed)
