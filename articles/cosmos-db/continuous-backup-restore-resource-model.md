---
title: Modelo de recursos para la característica de restauración a un momento dado de Azure Cosmos DB.
description: En este artículo se explica el modelo de recursos para la característica de restauración a un momento dado de Azure Cosmos DB. Se explican los parámetros que admiten los recursos y la copia de seguridad continua que se pueden restaurar en la API de Azure Cosmos DB para las cuentas de SQL y MongoDB.
author: kanshiG
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 07/29/2021
ms.author: govindk
ms.reviewer: sngun
ms.openlocfilehash: e4fffd12b72b41c45b2718e96c34a03e28eeca29
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/13/2021
ms.locfileid: "121733179"
---
# <a name="resource-model-for-the-azure-cosmos-db-point-in-time-restore-feature"></a>Modelo de recursos para la característica de restauración a un momento dado de Azure Cosmos DB
[!INCLUDE[appliesto-sql-mongodb-api](includes/appliesto-sql-mongodb-api.md)]

En este artículo se explica el modelo de recursos para la característica de restauración a un momento dado de Azure Cosmos DB. Se explican los parámetros que admiten los recursos y la copia de seguridad continua que se pueden restaurar en la API de Azure Cosmos DB para las cuentas de SQL y MongoDB.

## <a name="database-accounts-resource-model"></a>Modelo de recursos de la cuenta de base de datos

El modelo de recursos de la cuenta de base de datos se actualiza con algunas propiedades adicionales a fin de admitir los escenarios de restauración nuevos. Estas propiedades son **BackupPolicy, CreateMode y RestoreParameters**.

### <a name="backuppolicy"></a>BackupPolicy

Una propiedad nueva en la directiva de copia de seguridad en el nivel de la cuenta denominada `Type` bajo el parámetro `backuppolicy` permite las funcionalidades de copia de seguridad continua y restauración a un momento dado. Este modo se denomina **copia de seguridad continua**. Se puede establecer este modo al crear la cuenta o al [migrar una cuenta de modo periódico a continuo](migrate-continuous-backup.md). Una vez habilitado el modo continuo, todos los contenedores y las bases de datos que se creen en esta cuenta tendrán habilitadas las funcionalidades de copia de seguridad continua y restauración a un momento dado de manera predeterminada.

> [!NOTE]
> Actualmente, la característica de restauración a un momento dado está disponible para la API Azure Cosmos DB para cuentas de MongoDB y SQL. Después de crear una cuenta con el modo continuo, no se puede cambiar a un modo periódico.

### <a name="createmode"></a>CreateMode

Esta propiedad indica cómo se creó la cuenta. Los valores posibles son *Default* y *Restore*. Para realizar una restauración, establezca este valor en *Restore* y proporcione los valores adecuados en la propiedad `RestoreParameters`.

### <a name="restoreparameters"></a>RestoreParameters

El recurso `RestoreParameters` contiene los detalles de la operación de restauración, los que incluyen el id. de la cuenta, la hora a la que realizar la restauración y los recursos que se deben restaurar.

|Nombre de la propiedad |Descripción  |
|---------|---------|
|restoreMode  | El modo de restauración debe ser *PointInTime*. |
|restoreSource   |  El id. de instancia de la cuenta de origen desde la que se iniciará la restauración.       |
|restoreTimestampInUtc  | Momento dado en hora UTC al que se debe restaurar la cuenta. |
|databasesToRestore   | Lista de objetos `DatabaseRestoreResource` para especifica qué bases de datos y contenedores se deben restaurar. Cada recurso representa una base de datos única y todas las colecciones de esa base de datos. Para obtener más información, consulte la sección [Recursos SQL que se pueden restaurar](#restorable-sql-resources). Si este valor está vacío, se restaura toda la cuenta.   |

### <a name="sample-resource"></a>Recurso de ejemplo

El JSON siguiente es un recurso de cuenta de base de datos de ejemplo con copia de seguridad continua habilitada:

```json
{
  "location": "westus",
  "properties": {
    "databaseAccountOfferType": "Standard",
    "locations": [
      {
        "failoverPriority": 0,
        "locationName": "southcentralus",
        "isZoneRedundant": false
      }
    ],
    "createMode": "Restore",
    "restoreParameters": {
      "restoreMode": "PointInTime",
      "restoreSource": "/subscriptions/subid/providers/Microsoft.DocumentDB/locations/westus/restorableDatabaseAccounts/1a97b4bb-f6a0-430e-ade1-638d781830cc",
      "restoreTimestampInUtc": "2020-06-11T22:05:09Z",
      "databasesToRestore": [
        {
          "databaseName": "db1",
          "collectionNames": [
            "collection1",
            "collection2"
          ]
        },
        {
          "databaseName": "db2",
          "collectionNames": [
            "collection3",
            "collection4"
          ]
        }
      ]
    },
    "backupPolicy": {
      "type": "Continuous"
    }
}
```

## <a name="restorable-resources"></a>Recursos que se pueden restaurar

Hay disponible un conjunto de API y recursos nuevos para ayudarlo a descubrir información crítica sobre los recursos: cuáles se pueden restaurar, las ubicaciones desde las que se pueden restaurar y las marcas de tiempo de cuando se realizaron operaciones clave en estos recursos.

> [!NOTE]
> Todas las API que se usan para enumerar estos recursos requieren los permisos siguientes:
> * `Microsoft.DocumentDB/locations/restorableDatabaseAccounts/*/read`
> * `Microsoft.DocumentDB/locations/restorableDatabaseAccounts/read`

### <a name="restorable-database-account"></a>Cuenta de base de datos que se puede restaurar

Este recurso contiene una instancia de cuenta de base de datos que se puede restaurar. La cuenta de base de datos puede ser una cuenta eliminada o activa. Contiene información que permite encontrar la cuenta de base de datos de origen que desea restaurar.

|Nombre de la propiedad |Descripción  |
|---------|---------|
| ID | Identificador único del recurso. |
| accountName | Nombre de la cuenta de base de datos global. |
| creationTime | Hora UTC a la que se creó o migró la cuenta.  |
| deletionTime | Hora UTC a la que se eliminó la cuenta.  Este valor está vacío si la cuenta está activa. |
| apiType | Tipo de API de la cuenta de Azure Cosmos DB. |
| restorableLocations | Lista de las ubicaciones en las que existía la cuenta. |
| restorableLocations: locationName | Nombre de la región de la cuenta regional. |
| restorableLocations: regionalDatabaseAccountInstanceId | GUID de la cuenta regional. |
| restorableLocations: creationTime | Hora UTC a la que se creó o migró la cuenta regional.|
| restorableLocations: deletionTime | Hora UTC a la que se eliminó la cuenta regional. Este valor está vacío si la cuenta regional está activa.|

Si quiere ver una lista de todas las cuentas que se pueden restaurar, consulte los artículos [Cuentas de base de datos que se pueden restaurar: lista](/rest/api/cosmos-db-resource-provider/2021-04-01-preview/restorable-database-accounts/list) o [Cuentas de base de datos que se pueden restaurar: lista por ubicación](/rest/api/cosmos-db-resource-provider/2021-04-01-preview/restorable-database-accounts/list-by-location).

### <a name="restorable-sql-database"></a>Base de datos SQL que se puede restaurar

Cada recurso contiene información de un evento de mutación, como su creación y eliminación, que se produjo en la base de datos SQL. Esta información puede ayudar en escenarios en los que la base de datos se eliminó de manera accidental y si es necesario averiguar cuándo se produjo ese evento.

|Nombre de la propiedad |Descripción  |
|---------|---------|
| eventTimestamp | Hora UTC a la que se creó o eliminó la base de datos. |
| ownerId | Nombre de la base de datos SQL. |
| ownerResourceId | Identificador de recurso de la base de datos SQL.|
| operationType | Tipo de operación de este evento de base de datos. Estos son los valores posibles:<br/><ul><li>Create: evento de creación de base de datos.</li><li>Delete: evento de eliminación de base de datos.</li><li>Replace: evento de modificación de base de datos.</li><li>SystemOperation: evento de modificación de base de datos desencadenado por el sistema. No es el usuario quien inicia este evento.</li></ul> |
| database |Propiedades de la base de datos SQL en el momento del evento.|

Si quiere ver una lista de todas las mutaciones de base de datos, consulte el artículo [Bases de datos SQL que se pueden restaurar: lista](/rest/api/cosmos-db-resource-provider/2021-04-01-preview/restorable-sql-databases/list).

### <a name="restorable-sql-container"></a>Contenedor SQL que se puede restaurar

Cada recurso contiene información de un evento de mutación, como su creación y eliminación, que se produjo en el contenedor SQL. Esta información puede ayudar en escenarios en los que se modificó o eliminó el contenedor y si es necesario averiguar cuándo se produjo ese evento.

|Nombre de la propiedad |Descripción  |
|---------|---------|
| eventTimestamp    | Hora UTC a la que se produjo este evento de contenedor.|
| ownerId| Nombre del contenedor SQL.|
| ownerResourceId   | Identificador de recurso del contenedor SQL.|
| operationType | Tipo de operación de este evento de contenedor. Estos son los valores posibles: <br/><ul><li>Create: evento de creación de contenedor.</li><li>Delete: evento de eliminación de contenedor.</li><li>Replace: evento de modificación de contenedor.</li><li>SystemOperation: evento de modificación de contenedor desencadenado por el sistema. No es el usuario quien inicia este evento.</li></ul> |
| contenedor | Propiedades del contenedor SQL en el momento del evento.|

Si quiere ver una lista de todas las mutaciones de contenedor en la misma base de datos, consulte el artículo [Contenedores SQL que se pueden restaurar: lista](/rest/api/cosmos-db-resource-provider/2021-04-01-preview/restorable-sql-containers/list).

### <a name="restorable-sql-resources"></a>Recursos SQL que se pueden restaurar

Cada recurso representa una base de datos única y todos los contenedores de esa base de datos.

|Nombre de la propiedad |Descripción  |
|---------|---------|
| databaseName  | Nombre de la base de datos SQL.
| collectionNames   | Lista de los contenedores SQL de esta base de datos.|

Si quiere ver una lista de las combinaciones de base de datos y contenedor SQL que existen en la cuenta en una marca de tiempo y ubicación determinadas, consulte el artículo [Recursos SQL que se pueden restaurar: lista](/rest/api/cosmos-db-resource-provider/2021-04-01-preview/restorable-sql-resources/list).

### <a name="restorable-mongodb-database"></a>Base de datos MongoDB que se puede restaurar

Cada recurso contiene información de un evento de mutación, como su creación y eliminación, que se produjo en la base de datos MongoDB. Esta información puede ayudar en el escenario en el que la base de datos se eliminó de manera accidental y el usuario necesita averiguar cuándo se produjo ese evento.

|Nombre de la propiedad |Descripción  |
|---------|---------|
|eventTimestamp| Hora UTC a la que se produjo este evento de base de datos.|
| ownerId| Nombre de la base de datos MongoDB. |
| ownerResourceId   | Identificador de recurso de la base de datos MongoDB. |
| operationType |   Tipo de operación de este evento de base de datos. Estos son los valores posibles:<br/><ul><li> Create: evento de creación de base de datos.</li><li> Delete: evento de eliminación de base de datos.</li><li> Replace: evento de modificación de base de datos.</li><li> SystemOperation: evento de modificación de base de datos desencadenado por el sistema. No es el usuario quien inicia este evento. </li></ul> |

Si quiere ver una lista de todas las mutaciones de base de datos, consulte el artículo [Bases de datos MongoDB que se pueden restaurar: lista](/rest/api/cosmos-db-resource-provider/2021-04-01-preview/restorable-mongodb-databases/list).

### <a name="restorable-mongodb-collection"></a>Colección de MongoDB que se puede restaurar

Cada recurso contiene información de un evento de mutación, como su creación y eliminación, que se produjo en la colección de MongoDB. Esta información puede ayudar en escenarios en los que se modificó o eliminó la colección y si el usuario necesita averiguar cuándo se produjo ese evento.

|Nombre de la propiedad |Descripción  |
|---------|---------|
| eventTimestamp |Hora UTC a la que se produjo este evento de colección. |
| ownerId| Nombre de la colección de MongoDB. |
| ownerResourceId   | Identificador de recurso de la colección de MongoDB. |
| operationType |Tipo de operación de este evento de colección. Estos son los valores posibles:<br/><ul><li>Create: evento de creación de colección.</li><li>Delete: evento de eliminación de colección.</li><li>Replace: evento de modificación de colección.</li><li>SystemOperation: evento de modificación de colección desencadenado por el sistema. No es el usuario quien inicia este evento.</li></ul> |

Si quiere ver una lista de todas las mutaciones de contenedor en la misma base de datos, consulte el artículo [Colecciones de MongoDB que se pueden restaurar: lista](/rest/api/cosmos-db-resource-provider/2021-04-01-preview/restorable-mongodb-collections/list).

### <a name="restorable-mongodb-resources"></a>Recursos de MongoDB que se pueden restaurar

Cada recurso representa una base de datos única y todas las colecciones de esa base de datos.

|Nombre de la propiedad |Descripción  |
|---------|---------|
| databaseName  |Nombre de la base de datos MongoDB. |
| collectionNames | Lista de las colecciones de MongoDB en esta base de datos. |

Si quiere ver una lista de las combinaciones de base de datos y colección de MongoDB que existen en la cuenta en una marca de tiempo y ubicación determinadas, consulte el artículo [Recursos MongoDB que se pueden restaurar: lista](/rest/api/cosmos-db-resource-provider/2021-04-01-preview/restorable-mongodb-resources/list).

## <a name="next-steps"></a>Pasos siguientes

* Aprovisione la copia de seguridad continua mediante [Azure Portal](provision-account-continuous-backup.md#provision-portal), [PowerShell](provision-account-continuous-backup.md#provision-powershell), la [CLI](provision-account-continuous-backup.md#provision-cli) o [Azure Resource Manager](provision-account-continuous-backup.md#provision-arm-template).
* Restaure una cuenta mediante [Azure Portal](restore-account-continuous-backup.md#restore-account-portal), [PowerShell](restore-account-continuous-backup.md#restore-account-powershell), la [CLI](restore-account-continuous-backup.md#restore-account-cli) o [Azure Resource Manager](restore-account-continuous-backup.md#restore-arm-template).
* [Realice la migración a una cuenta desde una copia de seguridad periódica a una copia de seguridad continua](migrate-continuous-backup.md).
* [Administre los permisos](continuous-backup-restore-permissions.md) necesarios para restaurar datos con el modo de copia de seguridad continua.
