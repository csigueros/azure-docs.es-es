---
title: Copia de seguridad continua con la característica de restauración a un momento dado de Azure Cosmos DB
description: La característica de restauración a un momento dado de Azure Cosmos DB permite recuperar datos de operaciones de escritura o eliminación accidentales, o bien restaurar datos en cualquier región. Obtenga información sobre los precios y las limitaciones actuales.
author: kanshiG
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 07/29/2021
ms.author: govindk
ms.reviewer: sngun
ms.custom: references_regions
ms.openlocfilehash: a8862f0b71a6b3f8bba21bdd4ab40290a00d0959
ms.sourcegitcommit: 5f659d2a9abb92f178103146b38257c864bc8c31
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/17/2021
ms.locfileid: "122323022"
---
# <a name="continuous-backup-with-point-in-time-restore-in-azure-cosmos-db"></a>Copia de seguridad continua con la característica de restauración a un momento dado de Azure Cosmos DB
[!INCLUDE[appliesto-sql-mongodb-api](includes/appliesto-sql-mongodb-api.md)]

La característica de restauración a un momento dado de Azure Cosmos DB ayuda en varios escenarios, como los siguientes:

* Para recuperarse de una operación de escritura o eliminación accidental dentro de un contenedor.
* Para restaurar una cuenta, una base de datos o un contenedor eliminados.
* Para realizar una restauración en cualquier región (donde existan copias de seguridad) en el momento dado de la restauración.

Azure Cosmos DB realiza la copia de seguridad de datos en segundo plano sin consumir ningún rendimiento aprovisionado (RU) adicional ni afectar al rendimiento y la disponibilidad de la base de datos. Las copias de seguridad continuas se crean en todas las regiones en las que existe la cuenta. En la imagen siguiente se muestra cómo se crea una copia de seguridad de un contenedor con una región de escritura en Oeste de EE. UU., regiones de lectura en Este de EE. UU. y Este de EE. UU. 2 en una cuenta de Azure Blob Storage en las regiones respectivas. De manera predeterminada, cada región almacena la copia de seguridad en cuentas de almacenamiento con redundancia local. Si la región tiene habilitadas las [zonas de disponibilidad](high-availability.md#availability-zone-support), la copia de seguridad se almacena en cuentas de almacenamiento con redundancia de zona.

:::image type="content" source="./media/continuous-backup-restore-introduction/continuous-backup-restore-blob-storage.png" alt-text="Copia de seguridad de datos de Azure Cosmos DB en Azure Blob Storage." lightbox="./media/continuous-backup-restore-introduction/continuous-backup-restore-blob-storage.png" border="false":::

La ventana de tiempo disponible para la restauración (también conocido como período de retención) es el más bajo de los dos valores siguientes: *Últimos 30 días desde ahora* o *Hasta la creación del recurso*. El momento dado para la restauración puede ser cualquier marca de tiempo dentro del período de retención.

Actualmente, puede restaurar la cuenta de Azure Cosmos DB para el contenido de MongoDB o la API SQL en un momento dado en otra cuenta con [Azure Portal](restore-account-continuous-backup.md#restore-account-portal), la [interfaz de la línea de comandos (CLI) de Azure](restore-account-continuous-backup.md#restore-account-cli), [Azure PowerShell](restore-account-continuous-backup.md#restore-account-powershell) o [Azure Resource Manager](restore-account-continuous-backup.md#restore-arm-template).

## <a name="backup-storage-redundancy"></a>Redundancia del almacenamiento de copia de seguridad

De forma predeterminada, Azure Cosmos DB almacena datos de copia de seguridad en modo continuo en blobs de almacenamiento con redundancia local. En el caso de las regiones que tienen configurada la redundancia de zona, la copia de seguridad se almacena en blobs de almacenamiento con redundancia de zona. En el modo de copia de seguridad continua, no se puede actualizar la redundancia de almacenamiento de copia de seguridad.

## <a name="what-is-restored"></a>¿Qué se restaura?

En un estado estable, se realiza una copia de seguridad de todas las mutaciones realizadas en la cuenta de origen (lo que incluye las bases de datos, los contenedores y los elementos) de forma asincrónica en 100 segundos. Si el medio de copia de seguridad (es decir, el almacenamiento de Azure) está fuera de servicio o no está disponible, las mutaciones se conservan localmente hasta que el medio vuelve a estar disponible y, a continuación, se vacían para evitar cualquier pérdida de fidelidad de las operaciones que se pueden restaurar.

Puede optar por restaurar cualquier combinación de contenedores de rendimiento aprovisionados, una base de datos de rendimiento compartida o toda la cuenta. La acción de restauración restaura todos los datos y sus propiedades de índice en una cuenta nueva. El proceso de restauración garantiza que todos los datos restaurados en una cuenta, una base de datos o un contenedor tienen la garantía de que el tiempo de restauración especificado es coherente. La duración de la restauración dependerá de la cantidad de datos que sea necesario restaurar.

> [!NOTE]
> Con el modo de copia de seguridad continua, las copias de seguridad se crean en todas las regiones en las que está disponible su cuenta de Azure Cosmos DB. De manera predeterminada, las copias de seguridad creadas para cada cuenta de región son localmente redundantes y tienen redundancia de zona si la cuenta tiene habilitada la característica de [zona de disponibilidad](high-availability.md#availability-zone-support) para esa región. La acción de restauración siempre restaura los datos en una cuenta nueva.

## <a name="what-is-not-restored"></a>¿Qué no se restaura?

Las configuraciones siguientes no se restauran después de la recuperación a un momento dado:

* Configuración de firewall, red virtual y punto de conexión privado.
* Configuración de coherencia. De manera predeterminada, la cuenta se restaura con coherencia de sesión.  
* Regiones.
* Procedimientos almacenados, desencadenadores y UDF.

Puede agregar estas configuraciones a la cuenta restaurada una vez completada la restauración.

## <a name="restore-scenarios"></a>Escenarios de restauración

A continuación, se muestran algunos de los escenarios clave que se abordan con la característica de restauración a un momento dado. Los escenarios [a] a [c] muestran cómo desencadenar una restauración si la marca de tiempo de restauración se conoce de antemano.
Sin embargo, puede haber escenarios en los que no conozca la hora exacta de un daño o una eliminación accidental. Los escenarios [d] y [e] muestran cómo _detectar_ la marca de tiempo de restauración con las API de fuente de eventos nuevas en la base de datos o los contenedores que se pueden restaurar.

:::image type="content" source="./media/continuous-backup-restore-introduction/restorable-account-scenario.png" alt-text="Eventos del ciclo de vida con marcas de tiempo para una cuenta que se puede restaurar." lightbox="./media/continuous-backup-restore-introduction/restorable-account-scenario.png" border="false":::

1. **Restaurar una cuenta eliminada**: puede ver todas las cuentas eliminadas que se pueden restaurar en el panel **Restaurar**. Por ejemplo, si la *cuenta A* se elimina en la marca de tiempo T3. En este caso, basta la marca de tiempo justo antes de T3, la ubicación, el nombre de la cuenta de destino y el grupo de recursos para restaurar desde [Azure Portal](restore-account-continuous-backup.md#restore-deleted-account), [PowerShell](restore-account-continuous-backup.md#trigger-restore-ps) o la [CLI](restore-account-continuous-backup.md#trigger-restore-cli).  

:::image type="content" source="./media/continuous-backup-restore-introduction/restorable-container-database-scenario.png" alt-text="Eventos del ciclo de vida con marcas de tiempo para una base de datos y contenedor que se pueden restaurar." lightbox="./media/continuous-backup-restore-introduction/restorable-container-database-scenario.png" border="false":::

2. **Restaurar los datos de una cuenta en una región determinada**: por ejemplo, si la *cuenta A* existe en dos regiones, *Este de EE. UU.* y *Oeste de EE. UU.* en la marca de tiempo T3. Si necesita crear una copia de la cuenta A en *Oeste de EE. UU.* , puede hacer una restauración a un momento dado desde [Azure Portal](restore-account-continuous-backup.md#restore-deleted-account), [PowerShell](restore-account-continuous-backup.md#trigger-restore-ps) o la [CLI](restore-account-continuous-backup.md#trigger-restore-cli) con Oeste de EE. UU. como la ubicación de destino.

3. **Recuperar de una operación de escritura o eliminación accidental dentro de un contenedor con una marca de tiempo de restauración conocida**: por ejemplo, si **sabe** que el contenido del *contenedor 1* dentro de la *base de datos 1* se modificó accidentalmente en la marca de tiempo T3. Puede realizar una restauración a un momento dado desde [Azure Portal](restore-account-continuous-backup.md#restore-live-account), [PowerShell](restore-account-continuous-backup.md#trigger-restore-ps) o la [CLI](restore-account-continuous-backup.md#trigger-restore-cli) en otra cuenta en la marca de tiempo T3 para recuperar el estado deseado del contenedor.

4. **Restaurar una cuenta a un momento dado antes de la eliminación accidental de la base de datos**: en el [Azure Portal](restore-account-continuous-backup.md#restore-live-account), puede usar el panel fuente de eventos para determinar cuándo se eliminó una base de datos y buscar la hora de restauración. De un modo similar, con la [CLI de Azure](restore-account-continuous-backup.md#trigger-restore-cli) y [PowerShell](restore-account-continuous-backup.md#trigger-restore-ps), puede descubrir el evento de eliminación de la base de datos si enumera la fuente de eventos de la base de datos y, luego, desencadena el comando de restauración con los parámetros necesarios.

5. **Restaurar una cuenta a un momento dado antes de la eliminación o la modificación accidental de las propiedades del contenedor**: en [Azure Portal](restore-account-continuous-backup.md#restore-live-account), puede usar el panel de la fuente de eventos para determinar cuándo se creó, modificó o eliminó un contenedor a fin de encontrar la hora de restauración. De un modo similar, con la [CLI de Azure](restore-account-continuous-backup.md#trigger-restore-cli) y [PowerShell](restore-account-continuous-backup.md#trigger-restore-ps), puede descubrir todos los eventos del contenedor si enumera la fuente de eventos del contenedor y, luego, desencadena el comando de restauración con los parámetros necesarios.

## <a name="permissions"></a>Permisos

Azure Cosmos DB permite aislar y restringir los permisos de restauración de una cuenta de copia de seguridad continua a un rol o una entidad de seguridad concretos. El propietario de la cuenta puede desencadenar una restauración y asignar un rol a otras entidades de seguridad para realizar la operación de restauración. Para más información, consulte el artículo [Permisos](continuous-backup-restore-permissions.md).

## <a name="pricing"></a><a id="continuous-backup-pricing"></a>Precios

Las cuentas de Azure Cosmos DB que tienen habilitada la copia de seguridad continua incurrirán en un cargo mensual adicional por concepto de *almacenamiento de la copia de seguridad* y *restauración de los datos*. El costo de restauración se suma cada vez que se inicia la operación de restauración. Si configura una cuenta con copia de seguridad continua pero no restaura los datos, en la factura solo se incluye el costo de almacenamiento de copia de seguridad.

El ejemplo siguiente se basa en el precio de una cuenta de Azure Cosmos implementada en una región no gubernamental de EE. UU. Los precios y el cálculo pueden variar en función de la región que use; consulte la [página de precios de Azure Cosmos DB](https://azure.microsoft.com/pricing/details/cosmos-db/) para más información.

* Todas las cuentas habilitadas con la directiva de copia de seguridad continua incurren en un cargo mensual adicional para el almacenamiento de copia de seguridad que se calcula como se indica a continuación:

  USD 0,20/GB * Tamaño de los datos en GB en la cuenta * Cantidad de regiones

* Cada invocación de API de restauración incurre en un cargo único. El cargo es una función de la cantidad de restauración de datos y se calcula como se indica a continuación:

  USD 0,15/GB * Tamaño de los datos en GB.

Por ejemplo, si tiene 1 TB de datos en dos regiones:

* El costo del almacenamiento de copia de seguridad se calcula como (1000 * 0,20 * 2) = USD 400 al mes

* El costo de restauración se calcula como (1000 * 0,15) = USD 150 por restauración

## <a name="current-limitations"></a>Limitaciones actuales

Actualmente, la funcionalidad de restauración a un momento dado tiene las siguientes limitaciones:

* Solo se admiten las API de Azure Cosmos DB para SQL y MongoDB para copias de seguridad continuas. Todavía no se admiten Cassandra API, Table API y Gremlin API.

* Todavía no se admiten las regiones de la nube soberana de Azure y de Azure Government.

* No se admiten cuentas con claves administradas por el cliente para usar la copia de seguridad continua.

* No se admiten cuentas de escritura de varias regiones.

* En las cuentas habilitadas para Azure Synapse Link, los datos del almacén analítico no se incluyen en las copias de seguridad y restauraciones. Cuando Synapse Link esté habilitado, Azure Cosmos DB seguirá haciendo copias de seguridad automáticamente de los datos del almacén de transacciones en el intervalo programado de copias de seguridad. En este momento, no se admite la copia de seguridad ni la restauración automáticas de los datos del almacén analítico.

* La cuenta restaurada se crea en la misma región en la que existe la cuenta de origen. No se puede restaurar una cuenta en una región en la que la cuenta de origen no existe.

* La ventana de restauración es solo de 30 días y no se puede cambiar.

* De manera automática, las copias de seguridad no son resistentes a los desastres geográficos. Tendrá que agregar explícitamente otra región a fin de tener resistencia para la cuenta y la copia de seguridad.

* Mientras una restauración está en curso, no modifique ni elimine las directivas de administración de identidades y acceso (IAM) que conceden los permisos para la cuenta ni cambie la configuración del firewall y de red virtual.

* No se admiten las cuentas de API de Azure Cosmos DB para SQL o de MongoDB que crean un índice único una vez creado el contenedor para la copia de seguridad continua. Solo se admiten los contenedores que crean un índice único como parte de la creación inicial del contenedor. En el caso de las cuentas de MongoDB, puede crear un índice único mediante [comandos de extensión](mongodb/custom-commands.md).

* La funcionalidad de restauración a un momento dado siempre se restaura en una nueva cuenta de Azure Cosmos. Actualmente no se admite la restauración de una cuenta existente. Si está interesado en proporcionar comentarios sobre la restauración en contexto, póngase en contacto con el equipo de Azure Cosmos DB mediante su representante de cuenta.

* Después de la restauración, es posible que, para determinadas colecciones, se pueda recompilar el índice coherente. Puede comprobar el estado de la operación de recompilación mediante la propiedad [IndexTransformationProgress](how-to-manage-indexing-policy.md).

* El proceso de restauración restaura todas las propiedades de un contenedor, incluida la configuración de TTL. Como resultado, es posible que los datos restaurados se eliminen inmediatamente si se configuraron de este modo. Para evitar esta situación, la marca de tiempo de restauración debe ser antes de que se agreguen las propiedades de TTL en el contenedor.

* Los índices únicos de la API para MongoDB no se pueden agregar ni actualizar al crear una cuenta en modo de copia de seguridad continua o migrar una cuenta de modo periódico a modo continuo.

## <a name="next-steps"></a>Pasos siguientes

* Aprovisione la copia de seguridad continua mediante [Azure Portal](provision-account-continuous-backup.md#provision-portal), [PowerShell](provision-account-continuous-backup.md#provision-powershell), la [CLI](provision-account-continuous-backup.md#provision-cli) o [Azure Resource Manager](provision-account-continuous-backup.md#provision-arm-template).
* Restaure la cuenta de copia de seguridad continua mediante [Azure Portal](restore-account-continuous-backup.md#restore-account-portal), [PowerShell](restore-account-continuous-backup.md#restore-account-powershell), la [CLI](restore-account-continuous-backup.md#restore-account-cli) o [Azure Resource Manager](restore-account-continuous-backup.md#restore-arm-template).
* [Realice la migración a una cuenta desde una copia de seguridad periódica a una copia de seguridad continua](migrate-continuous-backup.md).
* [Administre los permisos](continuous-backup-restore-permissions.md) necesarios para restaurar datos con el modo de copia de seguridad continua.
* [Modelo de recursos del modo de copia de seguridad continua](continuous-backup-restore-resource-model.md)
