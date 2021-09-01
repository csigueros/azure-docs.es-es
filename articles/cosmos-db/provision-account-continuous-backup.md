---
title: Aprovisionamiento de una cuenta con copia de seguridad continua y restauración a un momento dado en Azure Cosmos DB
description: Aprenda a aprovisionar una cuenta con copias de seguridad continuas y restauración a un momento dado mediante Azure Portal, PowerShell, la CLI y las plantillas de Resource Manager.
author: kanshiG
ms.service: cosmos-db
ms.topic: how-to
ms.date: 07/29/2021
ms.author: govindk
ms.reviewer: sngun
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 78996e58111d380778e8d02673f518720250e6fb
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/13/2021
ms.locfileid: "121781215"
---
# <a name="provision-an-azure-cosmos-db-account-with-continuous-backup-and-point-in-time-restore"></a>Aprovisionamiento de una cuenta de Azure Cosmos DB con copia de seguridad continua y restauración a un momento dado 
[!INCLUDE[appliesto-sql-mongodb-api](includes/appliesto-sql-mongodb-api.md)]

La característica de restauración a un momento dado de Azure Cosmos DB ayuda a recuperarse de un cambio accidental en un contenedor, a restaurar una cuenta, una base de datos o un contenedor eliminados o a realizar una restauración en cualquier región (donde existan copias de seguridad). El modo de copia de seguridad continua permite realizar la restauración a cualquier momento dado de los últimos 30 días.

En este artículo se explica cómo aprovisionar una cuenta con copias de seguridad continuas y restauración a un momento dado mediante [Azure Portal](#provision-portal), [PowerShell](#provision-powershell), la [CLI](#provision-cli) y las [plantillas de Resource Manager](#provision-arm-template).

> [!NOTE]
> La cuenta del modo de copia de seguridad continua solo se puede aprovisionar si se cumplen las siguientes condiciones:
>
> * Si la cuenta es del tipo SQL API o API para MongoDB.
> * Si la cuenta tiene una sola región de escritura.
> * Si la cuenta no está habilitada con claves administradas por el cliente (CMK).

## <a name="provision-using-azure-portal"></a><a id="provision-portal"></a>Aprovisionamiento mediante Azure Portal

Al crear una cuenta de Azure Cosmos DB, en la pestaña **Directiva de copia de seguridad**, elija el modo **continuo** para habilitar la funcionalidad de restauración a un momento dato para la cuenta nueva. Con la restauración a un momento dado, los datos se restauran en una cuenta nueva; actualmente, no se puede restaurar en una cuenta existente.

:::image type="content" source="./media/provision-account-continuous-backup/configure-continuous-backup-portal.png" alt-text="Aprovisionamiento de una cuenta de Azure Cosmos DB con la configuración de copia de seguridad continua." border="true" lightbox="./media/provision-account-continuous-backup/configure-continuous-backup-portal.png":::

## <a name="provision-using-azure-powershell"></a><a id="provision-powershell"></a>Aprovisionamiento mediante Azure PowerShell

Antes de aprovisionar la cuenta, instale la [versión más reciente de Azure PowerShell](/powershell/azure/install-az-ps?view=azps-6.2.1&preserve-view=true) o cualquier versión posterior a la 6.2.0. Luego, conéctese a su cuenta de Azure y seleccione la suscripción necesaria con los siguientes comandos:

1. Utilice el siguiente comando para iniciar sesión en Azure:

   ```azurepowershell
   Connect-AzAccount
   ```

1. Seleccione una suscripción concreta con el comando siguiente:

   ```azurepowershell
   Select-AzSubscription -Subscription <SubscriptionName>
   ```

#### <a name="sql-api-account"></a><a id="provision-powershell-sql-api"></a>Cuenta de SQL API

Para aprovisionar una cuenta con copia de seguridad continua, agregue un argumento `-BackupPolicyType Continuous` junto con el comando de aprovisionamiento normal.

El siguiente cmdlet es un ejemplo de una cuenta de escritura de una sola región *Pitracct* con una directiva de copia de seguridad continua creada en la región *Oeste de EE. UU.* en el grupo de recursos *MyRG*:

```azurepowershell

New-AzCosmosDBAccount `
  -ResourceGroupName "MyRG" `
  -Location "West US" `
  -BackupPolicyType Continuous `
  -Name "pitracct" `
  -ApiKind "Sql"
      
```

#### <a name="api-for-mongodb"></a><a id="provision-powershell-mongodb-api"></a>API para MongoDB

El siguiente cmdlet es un ejemplo de una cuenta de copia de seguridad continua *Pitracct* creada en la región *Oeste de EE. UU.* en el grupo de recursos *MyRG*:

```azurepowershell

New-AzCosmosDBAccount `
  -ResourceGroupName "MyRG" `
  -Location "West US" `
  -BackupPolicyType Continuous `
  -Name "Pitracct" `
  -ApiKind "MongoDB" `
  -ServerVersion "3.6"

```

## <a name="provision-using-azure-cli"></a><a id="provision-cli"></a>Aprovisionamiento mediante la CLI de Azure

Antes de aprovisionar la cuenta, instale la CLI de Azure con los pasos siguientes:

1. Instale la versión más reciente de la CLI de Azure.

   * Instale la versión más reciente de la [CLI de Azure](/cli/azure/install-azure-cli) o una versión superior a la 2.26.0.
   * Si ya tiene instalada la CLI, ejecute el comando `az upgrade` para actualizarla a la versión más reciente. Este comando funcionará solo con una versión de la CLI superior a 2.11. Si tiene una versión anterior, use el vínculo anterior para instalar la versión más reciente.

1. Inicie sesión y seleccione su suscripción.

   * Use el comando `az login` para iniciar sesión en su cuenta de Azure.
   * Use el comando `az account set -s <subscriptionguid>` para seleccionar la suscripción requerida.

### <a name="sql-api-account"></a><a id="provision-cli-sql-api"></a>Cuenta de SQL API

Para aprovisionar una cuenta de API de SQL con copia de seguridad continua, se debe agregar un argumento `--backup-policy-type Continuous` adicional junto con el comando de aprovisionamiento normal. El comando siguiente es un ejemplo de una cuenta de escritura de una sola región denominada *Pitracct* con una directiva de copia de seguridad continua creada en la región *Oeste de EE. UU.* en el grupo de recursos *MyRG*:

```azurecli-interactive

az cosmosdb create \
  --name Pitracct \
  --resource-group MyRG \
  --backup-policy-type Continuous \
  --default-consistency-level Session \
  --locations regionName="West US"

```

### <a name="api-for-mongodb"></a><a id="provision-cli-mongo-api"></a>API para MongoDB

El siguiente comando muestra un ejemplo de una cuenta de escritura de una sola región denominada *Pitracct* con una directiva de copia de seguridad continua creada en la región *Oeste de EE. UU.* en el grupo de recursos *MyRG*:

```azurecli-interactive

az cosmosdb create \
  --name Pitracct \
  --kind MongoDB \
  --resource-group MyRG \
  --server-version "3.6" \
  --backup-policy-type Continuous \
  --default-consistency-level Session \
  --locations regionName="West US"

```

## <a name="provision-using-resource-manager-template"></a><a id="provision-arm-template"></a>Aprovisionamiento mediante una plantilla de Resource Manager

Puede usar plantillas de Azure Resource Manager para implementar una cuenta de base de datos de Azure Cosmos DB con modo continuo. Al definir la plantilla para aprovisionar una cuenta, incluya el parámetro `backupPolicy` como se muestra en el ejemplo siguiente:

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "resources": [
    {
      "name": "ademo-pitr1",
      "type": "Microsoft.DocumentDB/databaseAccounts",
      "apiVersion": "2016-03-31",
      "location": "West US",
      "properties": {
        "locations": [
          {
            "locationName": "West US"
          }
        ],
        "backupPolicy": {
          "type": "Continuous"
        },
        "databaseAccountOfferType": "Standard"
      }
    }
  ]
}
```

A continuación, implemente la plantilla mediante Azure PowerShell o la CLI. En el ejemplo siguiente se muestra cómo implementar la plantilla con un comando de la CLI:

```azurecli-interactive
az group deployment create -g <ResourceGroup> --template-file <ProvisionTemplateFilePath>
```

## <a name="next-steps"></a>Pasos siguientes

* [Restauración de una cuenta de Azure Cosmos DB en directo o eliminada](restore-account-continuous-backup.md)
* [Procedimiento de migración a una cuenta desde una copia de seguridad periódica a una copia de seguridad continua](migrate-continuous-backup.md).
* [Modelo de recursos del modo de copia de seguridad continua](continuous-backup-restore-resource-model.md).
* [Administre los permisos](continuous-backup-restore-permissions.md) necesarios para restaurar datos con el modo de copia de seguridad continua.