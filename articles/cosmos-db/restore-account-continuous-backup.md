---
title: Restaure una cuenta de Azure Cosmos DB que usa el modo de copia de seguridad continua.
description: Obtenga información sobre cómo identificar el tiempo de restauración y cómo restaurar una cuenta de Azure Cosmos DB activa o eliminada. Se muestra cómo usar la fuente de eventos para identificar el tiempo de restauración y restaurar la cuenta mediante Azure Portal, PowerShell, la CLI o una plantilla de Resource Manager.
author: kanshiG
ms.service: cosmos-db
ms.topic: how-to
ms.date: 07/29/2021
ms.author: govindk
ms.reviewer: sngun
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 1f8622b37055cf8585e9c43f2e822756ac06d1de
ms.sourcegitcommit: 87de14fe9fdee75ea64f30ebb516cf7edad0cf87
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/01/2021
ms.locfileid: "129352199"
---
# <a name="restore-an-azure-cosmos-db-account-that-uses-continuous-backup-mode"></a>Restauración de una cuenta de Azure Cosmos DB que usa el modo de copia de seguridad continua
[!INCLUDE[appliesto-sql-mongodb-api](includes/appliesto-sql-mongodb-api.md)]

La característica de restauración a un momento dado de Azure Cosmos DB ayuda a recuperarse de un cambio accidental en un contenedor, a restaurar una cuenta, una base de datos o un contenedor eliminados o a realizar una restauración en cualquier región (cuando existan copias de seguridad). El modo de copia de seguridad continua permite realizar la restauración a cualquier momento dado de los últimos 30 días.

En este artículo se describe cómo identificar el tiempo de restauración y cómo restaurar una cuenta de Azure Cosmos DB activa o eliminada. Se muestra cómo restaurar la cuenta mediante [Azure Portal](#restore-account-portal), [PowerShell](#restore-account-powershell), la [CLI](#restore-account-cli) o [Azure Resource Manager](#restore-arm-template).

## <a name="restore-an-account-using-azure-portal"></a><a id="restore-account-portal">Restauración de una cuenta mediante Azure Portal</a>

### <a name="restore-a-live-account-from-accidental-modification"></a><a id="restore-live-account"></a>Restauración de una cuenta activa después de una modificación accidental

Puede usar Azure Portal para restaurar toda una cuenta activa o una selección de bases de datos y contenedores asociados a esta. Siga estos pasos para restaurar los datos:

1. Inicie sesión en [Azure Portal](https://portal.azure.com/).
1. Vaya a la cuenta de Azure Cosmos DB y abra la hoja **Restauración a un momento dado**.

   > [!NOTE]
   > La hoja de restauración de Azure Portal solo se rellena si cuenta con el permiso `Microsoft.DocumentDB/locations/restorableDatabaseAccounts/*/read`. Para más información sobre cómo establecer este permiso, consulte el artículo sobre los [permisos de copia de seguridad y restauración](continuous-backup-restore-permissions.md).

1. Complete estos detalles para realizar la restauración:

   * **Punto de restauración (UTC)** : marca de tiempo dentro de los últimos 30 días. La cuenta debe existir en esa marca de tiempo. Puede especificar el punto de restauración en hora UTC. El valor puede acercarse lo más posible al segundo que quiere restaurar. Seleccione el vínculo **Haga clic aquí** para obtener ayuda sobre cómo [identificar el punto de restauración](#event-feed).

   * **Ubicación**: región de destino en que se restaura la cuenta. La cuenta debe existir en esta región en la marca de tiempo especificada (por ejemplo, Oeste de EE. UU. o Este de EE. UU.). Una cuenta solo se puede restaurar en las regiones en las que existe la cuenta de origen.

   * **Recurso de restauración**: puede elegir restaurar **toda la cuenta** o una **base de datos o contenedor seleccionados**. Las bases de datos y los contenedores deben existir en la marca de tiempo especificada. Los recursos de restauración se rellenan en función de la ubicación y el punto de restauración seleccionados, lo que permite al usuario seleccionar bases de datos o contenedores específicos que se deben restaurar.

   * **Grupo de recursos**: grupo de recursos en el que se creará y restaurará la cuenta de destino. El grupo de recursos ya debe existir.

   * **Cuenta de destino de restauración**: nombre de la cuenta de destino. El nombre de la cuenta de destino debe seguir las mismas directrices que cuando se crea una cuenta. El proceso de restauración creará esta cuenta en la misma región en la que existe la cuenta de destino.
 
   :::image type="content" source="./media/restore-account-continuous-backup/restore-live-account-portal.png" alt-text="Restauración de una cuenta activa después de una modificación accidental con Azure Portal." border="true" lightbox="./media/restore-account-continuous-backup/restore-live-account-portal.png":::

1. Después de seleccionar los parámetros anteriores, haga clic en el botón **Enviar** para iniciar una restauración. El coste de restauración es un cargo único que se basa en el tamaño de los datos y en el coste del almacenamiento de copia de seguridad en la región seleccionada. Para más información, consulte la sección de [precios](continuous-backup-restore-introduction.md#continuous-backup-pricing).

La eliminación de la cuenta de origen mientras una restauración está en curso podría dar lugar a un error en la restauración.

### <a name="use-event-feed-to-identify-the-restore-time"></a><a id="event-feed"></a>Uso de una fuente de eventos para identificar la hora de restauración

Al rellenar la hora del punto de restauración en Azure Portal, si necesita ayuda para identificar el punto de restauración, seleccione el vínculo **Haga clic aquí**, que le llevará a la hoja de la fuente de eventos. La fuente de eventos proporciona una lista de fidelidad total de eventos de creación, reemplazo y eliminación en bases de datos y contenedores de la cuenta de origen. 

Por ejemplo, si quiere restaurar al punto antes de la eliminación o actualización de un contenedor determinado, compruebe esta fuente de eventos. Los eventos se muestran en orden cronológico descendente respecto del momento en que se produjeron, con los eventos recientes en la parte superior. Puede examinar los resultados y seleccionar la hora antes o después del evento para restringir aún más el tiempo.

:::image type="content" source="./media/restore-account-continuous-backup/event-feed-portal.png" alt-text="Uso de una fuente de eventos para identificar la hora del punto de restauración." border="true" lightbox="./media/restore-account-continuous-backup/event-feed-portal.png":::

> [!NOTE]
> La fuente de eventos no muestra los cambios en los recursos del elemento. Siempre puede especificar manualmente cualquier marca de tiempo de los últimos 30 días (siempre que exista una cuenta en ese momento) para la restauración.

### <a name="restore-a-deleted-account"></a><a id="restore-deleted-account"></a>Restauración de una cuenta eliminada

Puede usar Azure Portal para restaurar completamente una cuenta eliminada en un plazo de 30 días a contar de su eliminación. Siga estos pasos para restaurar una cuenta eliminada:

1. Inicie sesión en [Azure Portal](https://portal.azure.com/).
1. Busque recursos de *Azure Cosmos DB* en la barra de búsqueda global. Se muestran todas las cuentas existentes.
1. Luego, haga clic en el botón **Restaurar**. En la hoja "Restaurar" se muestra una lista de las cuentas eliminadas que se pueden restaurar dentro del período de retención, que es de 30 días a partir del momento de la eliminación.
1. Elija la cuenta que quiere restaurar.

   :::image type="content" source="./media/restore-account-continuous-backup/restore-deleted-account-portal.png" alt-text="Restauración de una cuenta eliminada desde Azure Portal." border="true" lightbox="./media/restore-account-continuous-backup/restore-deleted-account-portal.png":::

   > [!NOTE]
   > La hoja de restauración de Azure Portal solo se rellena si cuenta con el permiso `Microsoft.DocumentDB/locations/restorableDatabaseAccounts/*/read`. Para más información sobre cómo establecer este permiso, consulte el artículo sobre los [permisos de copia de seguridad y restauración](continuous-backup-restore-permissions.md).

1. Seleccione una cuenta para restaurar y escriba los detalles siguientes para restaurar una cuenta eliminada:

   * **Punto de restauración (UTC)** : marca de tiempo dentro de los últimos 30 días. La cuenta debe haber existido en esa marca de tiempo. Especifique el punto de restauración en hora UTC. El valor puede acercarse lo más posible al segundo que quiere restaurar.

   * **Ubicación**: región de destino en que se debe restaurar la cuenta. La cuenta de origen debe existir en esta región en la marca de tiempo especificada. Por ejemplo, Oeste de EE. UU., Este de EE. UU.  

   * **Grupo de recursos**: grupo de recursos en el que se creará y restaurará la cuenta de destino. El grupo de recursos ya debe existir.

   * **Cuenta de destino de restauración**: el nombre de la cuenta de destino debe seguir las mismas directrices que cuando se crea una cuenta. El proceso de restauración creará esta cuenta en la misma región en la que existe la cuenta de destino.

### <a name="track-the-status-of-restore-operation"></a><a id="track-restore-status"></a>Seguimiento del estado de una operación de restauración

Después de iniciar una operación de restauración, seleccione el icono de campana de **Notificación** que se encuentra en la esquina superior derecha del portal. Proporciona un vínculo que muestra el estado de la cuenta que se está restaurando. Mientras la restauración esté en curso, el estado de la cuenta será *En creación* y, cuando la operación de restauración se complete, cambiará a *En línea*.

:::image type="content" source="./media/restore-account-continuous-backup/track-restore-operation-status.png" alt-text="El estado de la cuenta restaurada cambia de En creación a En línea cuando se completa la operación." border="true" lightbox="./media/restore-account-continuous-backup/track-restore-operation-status.png":::

## <a name="restore-an-account-using-azure-powershell"></a><a id="restore-account-powershell">Restauración de una cuenta mediante Azure PowerShell</a>

Antes de restaurar la cuenta, instale la [versión más reciente de Azure PowerShell](/powershell/azure/install-az-ps?view=azps-6.2.1&preserve-view=true) o cualquier versión posterior a la 6.2.0. Luego, conéctese a su cuenta de Azure y seleccione la suscripción necesaria con los siguientes comandos:

1. Utilice el siguiente comando para iniciar sesión en Azure:

   ```azurepowershell
   Connect-AzAccount
   ```

1. Seleccione una suscripción concreta con el comando siguiente:

   ```azurepowershell
   Select-AzSubscription -Subscription <SubscriptionName>

### <a id="trigger-restore-ps"></a>Trigger a restore operation

The following cmdlet is an example to trigger a restore operation with the restore command by using the target account, source account, location, resource group, and timestamp:

```azurepowershell

Restore-AzCosmosDBAccount `
  -TargetResourceGroupName "MyRG" `
  -TargetDatabaseAccountName "RestoredAccountName" `
  -SourceDatabaseAccountName "SourceDatabaseAccountName" `
  -RestoreTimestampInUtc "UTCTime" `
  -Location "AzureRegionName"

```

**Ejemplo 1:** restauración de toda la cuenta:

```azurepowershell

Restore-AzCosmosDBAccount `
  -TargetResourceGroupName "MyRG" `
  -TargetDatabaseAccountName "Pitracct" `
  -SourceDatabaseAccountName "source-sql" `
  -RestoreTimestampInUtc "2021-01-05T22:06:00" `
  -Location "West US"

```

**Ejemplo 2:** restauración de colecciones y bases de datos concretas. En este ejemplo se restauran las colecciones *MyCol1* y *MyCol2* de *MyDB1* y toda la base de datos *MyDB2*, que incluye todos los contenedores que hay en ella.

```azurepowershell
$datatabaseToRestore1 = New-AzCosmosDBDatabaseToRestore -DatabaseName "MyDB1" -CollectionName "MyCol1", "MyCol2"
$datatabaseToRestore2 = New-AzCosmosDBDatabaseToRestore -DatabaseName "MyDB2"

Restore-AzCosmosDBAccount `
  -TargetResourceGroupName "MyRG" `
  -TargetDatabaseAccountName "Pitracct" `
  -SourceDatabaseAccountName "SourceSql" `
  -RestoreTimestampInUtc "2021-01-05T22:06:00" `
  -DatabasesToRestore $datatabaseToRestore1, $datatabaseToRestore2 `
  -Location "West US"

```

### <a name="enumerate-restorable-resources-for-sql-api"></a><a id="enumerate-sql-api"></a>Enumeración de los recursos restaurables de la API de SQL

Los cmdlets de enumeración ayudan a detectar los recursos que están disponibles para restaurar en varias marcas de tiempo. Además, también proporcionan una fuente de eventos clave en los recursos restaurables de cuenta, base de datos y contenedor.

#### <a name="list-all-the-accounts-that-can-be-restored-in-the-current-subscription"></a>Enumeración de todas las cuentas que se pueden restaurar en la suscripción actual

Ejecute el comando `Get-AzCosmosDBRestorableDatabaseAccount` de PowerShell para enumerar todas las cuentas que se pueden restaurar en la suscripción actual.

La respuesta incluye todas las cuentas de base de datos (tanto activas como eliminadas) que se pueden restaurar y las regiones desde las que se puede hacer.

```json
{
    "accountName": "SampleAccount",
    "apiType": "Sql",
    "creationTime": "2020-08-08T01:04:52.070190+00:00",
    "deletionTime": null,
    "id": "/subscriptions/00000000-0000-0000-0000-000000000000/providers/Microsoft.DocumentDB/locations/West US/restorableDatabaseAccounts/23e99a35-cd36-4df4-9614-f767a03b9995",
    "identity": null,
    "location": "West US",
    "name": "23e99a35-cd36-4df4-9614-f767a03b9995",
    "restorableLocations": [
      {
        "creationTime": "2020-08-08T01:04:52.945185+00:00",
        "deletionTime": null,
        "location": "West US",
        "regionalDatabaseAccountInstanceId": "30701557-ecf8-43ce-8810-2c8be01dccf9"
      },
      {
        "creationTime": "2020-08-08T01:15:43.507795+00:00",
        "deletionTime": null,
        "location": "East US",
        "regionalDatabaseAccountInstanceId": "8283b088-b67d-4975-bfbe-0705e3e7a599"
      }
    ],
    "tags": null,
    "type": "Microsoft.DocumentDB/locations/restorableDatabaseAccounts"
  }
```

Al igual que `CreationTime` o `DeletionTime` para la cuenta, también hay `CreationTime` o `DeletionTime` para la región. Estas horas permiten elegir la región correcta y un intervalo de tiempo válido para restaurar en esa región.

#### <a name="list-all-the-versions-of-sql-databases-in-a-live-database-account"></a>Enumeración de todas las versiones de bases de datos SQL de una cuenta de base de datos activa

La enumeración de todas las versiones de bases de datos permite elegir la base de datos correcta en un escenario en el que se desconoce la hora real de existencia de la base de datos.

Ejecute el siguiente comando de PowerShell para enumerar todas las versiones de bases de datos. Este comando solo funciona con cuentas activas. Los parámetros `DatabaseAccountInstanceId` y `Location` se obtienen de las propiedades `name` y `location` en la respuesta del cmdlet `Get-AzCosmosDBRestorableDatabaseAccount`. El atributo `DatabaseAccountInstanceId` hace referencia a la propiedad `instanceId` de la cuenta de base de datos de origen que se está restaurando:

```azurepowershell

Get-AzCosmosdbSqlRestorableDatabase `
  -Location "East US" `
  -DatabaseAccountInstanceId <DatabaseAccountInstanceId>

```

#### <a name="list-all-the-versions-of-sql-containers-of-a-database-in-a-live-database-account"></a>Enumeración de todas las versiones de contenedores SQL de una base de datos de una cuenta de base de datos activa

Use el comando siguiente para enumerar todas las versiones de contenedores SQL. Este comando solo funciona con cuentas activas. El parámetro `DatabaseRId` es el elemento `ResourceId` de la base de datos que se quiere restaurar. Es el valor del atributo `ownerResourceid` que se encuentra en la respuesta del cmdlet `Get-AzCosmosdbSqlRestorableDatabase`. La respuesta también incluye una lista de las operaciones realizadas en todos los contenedores de esta base de datos.

```azurepowershell

Get-AzCosmosdbSqlRestorableContainer `
  -DatabaseAccountInstanceId "d056a4f8-044a-436f-80c8-cd3edbc94c68" `
  -DatabaseRId "AoQ13r==" `
  -Location "West US"

```

#### <a name="find-databases-or-containers-that-can-be-restored-at-any-given-timestamp"></a>Búsqueda de bases de datos o contenedores que se pueden restaurar en cualquier marca de tiempo

Use el comando siguiente para obtener la lista de bases de datos o contenedores que se pueden restaurar en una marca de tiempo determinada. Este comando solo funciona con cuentas activas.

```azurepowershell

Get-AzCosmosdbSqlRestorableResource `
  -DatabaseAccountInstanceId "d056a4f8-044a-436f-80c8-cd3edbc94c68" `
  -Location "West US" `
  -RestoreLocation "East US" `
  -RestoreTimestamp "2020-07-20T16:09:53+0000"

```

### <a name="enumerate-restorable-resources-in-api-for-mongodb"></a><a id="enumerate-mongodb-api"></a>Enumeración de los recursos restaurables en la API para MongoDB

Los comandos de enumeración que se describen a continuación ayudan a detectar los recursos que están disponibles para restaurar en varias marcas de tiempo. Además, también proporcionan una fuente de eventos clave en los recursos restaurables de cuenta, base de datos y contenedor. Estos comandos solo funcionan en cuentas activas y son similares a los comandos de la API de SQL, aunque con `MongoDB` en el nombre de comando en lugar de `sql`.

#### <a name="list-all-the-versions-of-mongodb-databases-in-a-live-database-account"></a>Enumeración de todas las versiones de bases de datos MongoDB de una cuenta de base de datos activa

```azurepowershell

Get-AzCosmosdbMongoDBRestorableDatabase `
  -DatabaseAccountInstanceId "d056a4f8-044a-436f-80c8-cd3edbc94c68" `
  -Location "West US"

```

#### <a name="list-all-the-versions-of-mongodb-collections-of-a-database-in-a-live-database-account"></a>Enumeración de todas las versiones de colecciones MongoDB de una base de datos de una cuenta de base de datos activa

```azurepowershell

Get-AzCosmosdbMongoDBRestorableCollection `
  -DatabaseAccountInstanceId "d056a4f8-044a-436f-80c8-cd3edbc94c68" `
  -DatabaseRId "AoQ13r==" `
  -Location "West US"
```

#### <a name="list-all-the-resources-of-a-mongodb-database-account-that-are-available-to-restore-at-a-given-timestamp-and-region"></a>Enumeración de todos los recursos de una cuenta de base de datos MongoDB que están disponibles para restaurar en una determinada marca de tiempo y región

```azurepowershell

Get-AzCosmosdbMongoDBRestorableResource `
  -DatabaseAccountInstanceId "d056a4f8-044a-436f-80c8-cd3edbc94c68" `
  -Location "West US" `
  -RestoreLocation "West US" `
  -RestoreTimestamp "2020-07-20T16:09:53+0000"
```

## <a name="restore-an-account-using-azure-cli"></a><a id="restore-account-cli"></a>Restauración de una cuenta mediante la CLI de Azure

Antes de restaurar la cuenta, instale la CLI de Azure con los siguientes pasos:

1. Instale la versión más reciente de la CLI de Azure.

   * Instale la versión más reciente de la [CLI de Azure](/cli/azure/install-azure-cli) o cualquier versión posterior a la 2.26.0.
   * Si ya tiene instalada la CLI, ejecute el comando `az upgrade` para actualizarla a la versión más reciente. Este comando funcionará solo con una versión de la CLI superior a 2.11. Si tiene una versión anterior, use el vínculo anterior para instalar la versión más reciente.

1. Inicie sesión y seleccione su suscripción.

   * Use el comando `az login` para iniciar sesión en su cuenta de Azure.
   * Use el comando `az account set -s <subscriptionguid>` para seleccionar la suscripción requerida.

### <a name="trigger-a-restore-operation-with-cli"></a><a id="trigger-restore-cli"></a>Desencadenamiento de una operación de restauración con la CLI

La manera más sencilla de desencadenar una restauración es mediante la emisión del comando de restauración con el nombre de la cuenta de destino, la cuenta de origen, la ubicación, el grupo de recursos, la marca de tiempo (en hora UTC) y, opcionalmente, los nombres de la base de datos y del contenedor. A continuación, se muestran algunos ejemplos para desencadenar la operación de restauración:

1. Cree una cuenta de Azure Cosmos DB mediante la restauración de una cuenta existente.

   ```azurecli-interactive

   az cosmosdb restore \
    --target-database-account-name MyRestoredCosmosDBDatabaseAccount \
    --account-name MySourceAccount \
    --restore-timestamp 2020-07-13T16:03:41+0000 \
    --resource-group MyResourceGroup \
    --location "West US"

   ```

2. Cree una cuenta de Azure Cosmos DB mediante la restauración solo de las bases de datos y los contenedores seleccionados de una cuenta de base de datos existente.

   ```azurecli-interactive

   az cosmosdb restore \
    --resource-group MyResourceGroup \
    --target-database-account-name MyRestoredCosmosDBDatabaseAccount \
    --account-name MySourceAccount \
    --restore-timestamp 2020-07-13T16:03:41+0000 \
    --location "West US" \
    --databases-to-restore name=MyDB1 collections=Collection1 Collection2 \
    --databases-to-restore name=MyDB2 collections=Collection3 Collection4

   ```

### <a name="enumerate-restorable-resources-for-sql-api"></a><a id="enumerate-sql-api"></a>Enumeración de los recursos que se pueden restaurar de la API de SQL

Los comandos de enumeración que se describen a continuación ayudan a detectar los recursos que están disponibles para restaurar en varias marcas de tiempo. Además, también proporcionan una fuente de eventos clave en los recursos que se pueden restaurar de cuenta, base de datos y contenedor.

#### <a name="list-all-the-accounts-that-can-be-restored-in-the-current-subscription"></a>Enumeración de todas las cuentas que se pueden restaurar en la suscripción actual

Ejecute el comando de CLI siguiente para enumerar todas las cuentas que se pueden restaurar en la suscripción actual.

```azurecli-interactive
az cosmosdb restorable-database-account list --account-name "Pitracct"
```

La respuesta incluye todas las cuentas de base de datos (tanto activas como eliminadas) que se pueden restaurar y las regiones desde las que se puede hacer esta restauración:

```json
{
    "accountName": "Pitracct",
    "apiType": "Sql",
    "creationTime": "2021-01-08T23:34:11.095870+00:00",
    "deletionTime": null,
    "id": "/subscriptions/00000000-0000-0000-0000-000000000000/providers/Microsoft.DocumentDB/locations/West US/restorableDatabaseAccounts/7133a59a-d1c0-4645-a699-6e296d6ac865",
    "identity": null,
    "location": "West US",
    "name": "7133a59a-d1c0-4645-a699-6e296d6ac865",
    "restorableLocations": [
      {
        "creationTime": "2021-01-08T23:34:11.095870+00:00",
        "deletionTime": null,
        "locationName": "West US",
        "regionalDatabaseAccountInstanceId": "f02df26b-c0ec-4829-8bef-3482d36e6230"
      }
    ],
    "tags": null,
    "type": "Microsoft.DocumentDB/locations/restorableDatabaseAccounts"
  }
```

Igual que los elementos `CreationTime` o `DeletionTime` de la cuenta, existe también un elemento `CreationTime` o `DeletionTime` para la región. Estas horas permiten elegir la región correcta y un intervalo de tiempo válido para restaurar en esa región.

#### <a name="list-all-the-versions-of-databases-in-a-live-database-account"></a>Enumeración de todas las versiones de bases de datos de una cuenta de base de datos activa

La enumeración de todas las versiones de bases de datos permite elegir la base de datos correcta en un escenario en el que se desconoce la hora real de existencia de la base de datos.

Ejecute el comando de CLI siguiente para enumerar todas las versiones de bases de datos. Este comando solo funciona con cuentas activas. Los parámetros `instance-id` y `location` se obtienen de las propiedades `name` y `location` en la respuesta del comando `az cosmosdb restorable-database-account list`. El atributo instanceId también es una propiedad de la cuenta de base de datos de origen que se está restaurando:

```azurecli-interactive
az cosmosdb sql restorable-database list \
  --instance-id "7133a59a-d1c0-4645-a699-6e296d6ac865" \
  --location "West US"
```

La salida de este comando ahora muestra cuándo se creó y eliminó una base de datos.

```json
[
  {
    "id": "/subscriptions/00000000-0000-0000-0000-000000000000/providers/Microsoft.DocumentDB/locations/West US/restorableDatabaseAccounts/7133a59a-d1c0-4645-a699-6e296d6ac865/restorableSqlDatabases/40e93dbd-2abe-4356-a31a-35567b777220",
    ..
    "name": "40e93dbd-2abe-4356-a31a-35567b777220",
    "resource": {
      "database": {
        "id": "db1"
      },
      "eventTimestamp": "2021-01-08T23:27:25Z",
      "operationType": "Create",
      "ownerId": "db1",
      "ownerResourceId": "YuZAAA=="
    },
    ..
  },
  {
    "id": "/subscriptions/00000000-0000-0000-0000-000000000000/providers/Microsoft.DocumentDB/locations/West US/restorableDatabaseAccounts/7133a59a-d1c0-4645-a699-6e296d6ac865/restorableSqlDatabases/243c38cb-5c41-4931-8cfb-5948881a40ea",
    ..
    "name": "243c38cb-5c41-4931-8cfb-5948881a40ea",
    "resource": {
      "database": {
        "id": "spdb1"
      },
      "eventTimestamp": "2021-01-08T23:25:25Z",
      "operationType": "Create",
      "ownerId": "spdb1",
      "ownerResourceId": "OIQ1AA=="
    },
   ..
  }
]
```

#### <a name="list-all-the-versions-of-sql-containers-of-a-database-in-a-live-database-account"></a>Enumeración de todas las versiones de contenedores SQL de una base de datos de una cuenta de base de datos activa

Use el comando siguiente para enumerar todas las versiones de contenedores SQL. Este comando solo funciona con cuentas activas. El parámetro `database-rid` es el elemento `ResourceId` de la base de datos que se quiere restaurar. Es el valor del atributo `ownerResourceid` que se encuentra en la respuesta del comando `az cosmosdb sql restorable-database list`.

```azurecli-interactive
az cosmosdb sql restorable-container list \
    --instance-id "7133a59a-d1c0-4645-a699-6e296d6ac865" \
    --database-rid "OIQ1AA==" \
    --location "West US"
```

La respuesta de este comando ahora incluye la lista de las operaciones realizadas en todos los contenedores de esta base de datos:

```json
[
  {
    ...
    
      "eventTimestamp": "2021-01-08T23:25:29Z",
      "operationType": "Replace",
      "ownerId": "procol3",
      "ownerResourceId": "OIQ1APZ7U18="
...
  },
  {
    ...
      "eventTimestamp": "2021-01-08T23:25:26Z",
      "operationType": "Create",
      "ownerId": "procol3",
      "ownerResourceId": "OIQ1APZ7U18="
  },
]
```

#### <a name="find-databases-or-containers-that-can-be-restored-at-any-given-timestamp"></a>Búsqueda de bases de datos o contenedores que se pueden restaurar en cualquier marca de tiempo

Use el comando siguiente para obtener la lista de bases de datos o contenedores que se pueden restaurar en una marca de tiempo determinada. Este comando solo funciona con cuentas activas.

```azurecli-interactive

az cosmosdb sql restorable-resource list \
  --instance-id "7133a59a-d1c0-4645-a699-6e296d6ac865" \
  --location "West US" \
  --restore-location "West US" \  
  --restore-timestamp "2021-01-10T01:00:00+0000"

```

```json
[
  {
    "collectionNames": [
      "procol1",
      "procol2"
    ],
    "databaseName": "db1"
  },
  {
    "collectionNames": [
      "procol3",
       "spcol1"
    ],
    "databaseName": "spdb1"
  }
]
```

### <a name="enumerate-restorable-resources-for-mongodb-api-account"></a><a id="enumerate-mongodb-api"></a>Enumeración de los recursos que se pueden restaurar para una cuenta de la API de MongoDB

Los comandos de enumeración que se describen a continuación ayudan a detectar los recursos que están disponibles para restaurar en varias marcas de tiempo. Además, también proporcionan una fuente de eventos clave en los recursos que se pueden restaurar de cuenta, base de datos y contenedor. Estos comandos solo funcionan con cuentas activas.

#### <a name="list-all-the-versions-of-mongodb-databases-in-a-live-database-account"></a>Enumeración de todas las versiones de bases de datos MongoDB de una cuenta de base de datos activa

```azurecli-interactive
az cosmosdb mongodb restorable-database list \
    --instance-id "7133a59a-d1c0-4645-a699-6e296d6ac865" \
    --location "West US"
```

#### <a name="list-all-the-versions-of-mongodb-collections-of-a-database-in-a-live-database-account"></a>Enumeración de todas las versiones de colecciones MongoDB de una base de datos de una cuenta de base de datos activa

```azurecli-interactive
az cosmosdb mongodb restorable-collection list \
    --instance-id "7133a59a-d1c0-4645-a699-6e296d6ac865" \
    --database-rid "AoQ13r==" \
    --location "West US"
```

#### <a name="list-all-the-resources-of-a-mongodb-database-account-that-are-available-to-restore-at-a-given-timestamp-and-region"></a>Enumeración de todos los recursos de una cuenta de base de datos MongoDB que están disponibles para restaurar en una determinada marca de tiempo y región

```azurecli-interactive
az cosmosdb mongodb restorable-resource list \
    --instance-id "7133a59a-d1c0-4645-a699-6e296d6ac865" \
    --location "West US" \
    --restore-location "West US" \
    --restore-timestamp "2020-07-20T16:09:53+0000"
```

## <a name="restore-using-the-resource-manager-template"></a><a id="restore-arm-template"> </a>Restauración mediante la plantilla de Resource Manager

También puede restaurar una cuenta mediante la plantilla de Resource Manager. Al definir la plantilla, incluya los parámetros siguientes:

* Establezca el parámetro `createMode` en *Restaurar*
* Defina `restoreParameters`, tenga en cuenta que el valor de `restoreSource` se extrae de la salida del comando `az cosmosdb restorable-database-account list` de la cuenta de origen. El atributo del identificador de instancia para el nombre de cuenta se usa para realizar la restauración.
* Establezca el parámetro `restoreMode` en *PointInTime* y configure el valor `restoreTimestampInUtc`.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "resources": [
    {
      "name": "vinhpitrarmrestore-kal3",
      "type": "Microsoft.DocumentDB/databaseAccounts",
      "apiVersion": "2016-03-31",
      "location": "West US",
      "properties": {
        "locations": [
          {
            "locationName": "West US"
          }
        ],
        "databaseAccountOfferType": "Standard",
        "createMode": "Restore",
        "restoreParameters": {
            "restoreSource": "/subscriptions/2296c272-5d55-40d9-bc05-4d56dc2d7588/providers/Microsoft.DocumentDB/locations/West US/restorableDatabaseAccounts/6a18ecb8-88c2-4005-8dce-07b44b9741df",
            "restoreMode": "PointInTime",
            "restoreTimestampInUtc": "6/24/2020 4:01:48 AM"
        }
      }
    }
  ]
}
```

A continuación, implemente la plantilla mediante Azure PowerShell o la CLI. En el ejemplo siguiente se muestra cómo implementar la plantilla con un comando de la CLI:  

```azurecli-interactive
az group deployment create -g <ResourceGroup> --template-file <RestoreTemplateFilePath> 
```

## <a name="next-steps"></a>Pasos siguientes

* Aprovisione la copia de seguridad continua mediante [Azure Portal](provision-account-continuous-backup.md#provision-portal), [PowerShell](provision-account-continuous-backup.md#provision-powershell), la [CLI](provision-account-continuous-backup.md#provision-cli) o [Azure Resource Manager](provision-account-continuous-backup.md#provision-arm-template).
* [Migración a una cuenta desde una copia de seguridad periódica a una copia de seguridad continua](migrate-continuous-backup.md).
* [Modelo de recursos del modo de copia de seguridad continua](continuous-backup-restore-resource-model.md).
* [Administre los permisos](continuous-backup-restore-permissions.md) necesarios para restaurar datos con el modo de copia de seguridad continua.
