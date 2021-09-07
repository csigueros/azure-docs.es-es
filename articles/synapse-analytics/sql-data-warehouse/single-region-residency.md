---
title: Residencia en una sola región
description: Guía paso a paso para configurar la residencia en una sola región para un grupo de SQL dedicado (anteriormente SQL DW) en Azure Synapse Analytics
services: synapse-analytics
author: joannapea
manager: igorstan
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql-dw
ms.date: 05/15/2021
ms.author: joanpo
ms.reviewer: igorstan
ms.custom: seo-lt-2019
ms.openlocfilehash: a9fd0860fba93f2aa45616707c791aef498fb06e
ms.sourcegitcommit: e1d5abd7b8ded7ff649a7e9a2c1a7b70fdc72440
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 05/27/2021
ms.locfileid: "110580720"
---
# <a name="configure-single-region-residency-for-a-dedicated-sql-pool-formerly-sql-dw-in-azure-synapse-analytics"></a>Configuración de la residencia en una sola región para un grupo de SQL dedicado (anteriormente SQL DW) en Azure Synapse Analytics

En este artículo, aprenderá a aprovisionar la base de datos en almacenamiento con redundancia local para asegurarse de que los datos permanecen dentro de los límites geográficos. Este artículo solo es aplicable a grupos de SQL dedicados que residen en una región donde el par regional de Azure se encuentra fuera del país. Si sigue los pasos descritos en este artículo, la mayoría de los datos y todas las copias de seguridad no se replicarán en una [región emparejada](../../best-practices-availability-paired-regions.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json). Tenga en cuenta que algunos metadatos, como el nombre del servidor lógico, el nombre de la base de datos lógica o el nombre del grupo de recursos, se siguen replicando en una región emparejada de forma predeterminada y esto no se puede cambiar. 

Para lograr la residencia en una sola región, aprovisione el grupo de SQL dedicado (anteriormente SQL DW) en el almacenamiento con redundancia local seleccionando la opción "No" para la redundancia geográfica al aprovisionar el grupo de SQL. Si ya ha aprovisionado el grupo de SQL y reside en almacenamiento con redundancia geográfica, puede restaurar el grupo de SQL y seleccionar "No" en el almacenamiento con redundancia geográfica durante el proceso de restauración. A continuación, se detallan las instrucciones para ambos escenarios. 

## <a name="provision-a-dedicated-sql-pool-formerly-sql-dw-on-locally-redundant-storage-through-azure-portal"></a>Aprovisionamiento de un grupo de SQL dedicado (anteriormente SQL DW) en almacenamiento con redundancia local a través de Azure Portal

Siga estos pasos para aprovisionar un grupo de SQL dedicado (anteriormente SQL DW) en almacenamiento con redundancia local:

1. Inicie sesión en su cuenta de [Azure Portal](https://portal.azure.com/).
1. Busque **grupos de SQL dedicados (anteriormente SQL DW)** .

   ![Nuevo DW 2](./media/sql-data-warehouse-restore-from-geo-backup/georestore-new.png)

1. Haga clic para agregar y rellenar la información solicitada en la pestaña **Aspectos básicos***.

1. Si el servidor se encuentra en una región (como el Sudeste Asiático) en la que el par regional se encuentra en una ubicación geográfica diferente, tendrá la opción de no participar en la redundancia geográfica. 

   ![Residencia de datos](./media/sql-data-warehouse-single-region-residency/data-residency-1.png)

1. Seleccione **Siguiente: Redes** para configurar un punto de conexión público o privado. 

1. Seleccione **Siguiente: Configuración adicional** para restaurar desde una copia de seguridad, muestrear o crear una base de datos en blanco.  

1. Seleccione **Siguiente: Etiquetas** para configurar las etiquetas para categorizar el grupo de SQL. 

1. Seleccione **Siguiente: Review + create** (Revisar y crear). Asegúrese de que se ha elegido la redundancia de almacenamiento correcta para garantizar la residencia de datos. 

    ![Creación de un grupo una sola región](./media/sql-data-warehouse-single-region-residency/data-residency-2.png)

1. Seleccione **Crear**.  

## <a name="provision-a-dedicated-sql-pool-formerly-sql-dw-on-locally-redundant-storage-through-powershell"></a>Aprovisionamiento de un grupo de SQL dedicado (anteriormente SQL DW) en almacenamiento con redundancia local a través de PowerShell
Para crear un nuevo grupo de SQL dedicado a través de PowerShell, use el cmdlet [New-AzSqlDatabase](/powershell/module/az.sql/new-azsqldatabaserestorepoint?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) de PowerShell.

1. Antes de empezar, asegúrese de [instalar Azure PowerShell](/powershell/azure/?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json).
2. Abra PowerShell.
3. Conéctese a su cuenta de Azure y enumere todas las suscripciones asociadas a su cuenta.
4. Seleccione la suscripción que contiene la base de datos que se va a restaurar.
5. Cree una base de datos en el almacenamiento con redundancia local.
1. Asegúrese de que la base de datos se ha creado correctamente en el almacenamiento con redundancia local.  

```powershell

$SubscriptionName="<YourSubscriptionName>"
$ResourceGroupName="<YourResourceGroupName>"
$ServerName="<YourServerNameWithoutURLSuffixSeeNote>"  # Without database.windows.net
$DatabaseName="<YourDatabaseName>"
$Label = "<YourRestorePointLabel>"

Connect-AzAccount
Get-AzSubscription
Select-AzSubscription -SubscriptionName $SubscriptionName
New-AzSqlDatabase -ResourceGroupName $ResourceGroupName -Edition 'DataWarehouse' -ServerName $ServerName -DatabaseName $DatabaseName -BackupStorageRedundancy 'Local'
Get-AzSqlDatabase -ResourceGroupName $ResourceGroupName -Edition 'DataWarehouse' -ServerName $ServerName -DatabaseName $DatabaseName 
```

## <a name="restore-a-dedicated-sql-pool-formerly-sql-dw-on-locally-redundant-storage-through-azure-portal"></a>Restauración de un grupo de SQL dedicado (anteriormente SQL DW) en almacenamiento con redundancia local a través de Azure Portal
1. Inicie sesión en [Azure Portal](https://portal.azure.com/).

1. Vaya al grupo dedicado desde el que quiere realizar la restauración.

1. En la parte superior de la hoja de información general, seleccione **Restaurar**.

1. Seleccione el punto de restauración desde el que quiere restaurar. 

1. Para **Redundancia geográfica** seleccione "No". 

   ![Restauración a través del portal](./media/sql-data-warehouse-single-region-residency/data-residency-3.png)

## <a name="restore-a-dedicated-sql-pool-formerly-sql-dw-on-locally-redundant-storage-through-powershell"></a>Restauración de un grupo de SQL dedicado (anteriormente SQL DW) en almacenamiento con redundancia local a través de PowerShell
Para crear un nuevo grupo de SQL dedicado a través de PowerShell, use el cmdlet [New-AzSqlDatabase](/powershell/module/az.sql/new-azsqldatabaserestorepoint?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) de PowerShell.

1. Antes de empezar, asegúrese de [instalar Azure PowerShell](/powershell/azure/?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json).
2. Abra PowerShell.
3. Conéctese a su cuenta de Azure y enumere todas las suscripciones asociadas a su cuenta.
4. Seleccione la suscripción que contiene la base de datos que se va a restaurar.
5. Enumere los puntos de restauración del grupo de SQL dedicado (anteriormente SQL DW).
1. Elija el punto de restauración deseado mediante RestorePointCreationDate.
1. Restaure el grupo de SQL dedicado (anteriormente SQL DW) al punto de restauración deseado mediante el cmdlet Restore-AzSqlDatabase de PowerShell especificando BackupStorageRedundnacy como "Local". 

```powershell

$SubscriptionName="<YourSubscriptionName>"
$ResourceGroupName="<YourResourceGroupName>"
$ServerName="<YourServerNameWithoutURLSuffixSeeNote>"  # Without database.windows.net
$DatabaseName="<YourDatabaseName>"
$NewDatabaseName="<YourDatabaseName>"

Connect-AzAccount
Get-AzSubscription
Select-AzSubscription -SubscriptionName $SubscriptionName

# Or list all restore points
Get-AzSqlDatabaseRestorePoint -ResourceGroupName $ResourceGroupName -ServerName $ServerName -DatabaseName $DatabaseName

# Get the specific database to restore
$Database = Get-AzSqlDatabase -ResourceGroupName $ResourceGroupName -ServerName $ServerName -DatabaseName $DatabaseName

# Pick desired restore point using RestorePointCreationDate "xx/xx/xxxx xx:xx:xx xx"
$PointInTime="<RestorePointCreationDate>"

# Restore database from a restore point
$RestoredDatabase = Restore-AzSqlDatabase –FromPointInTimeBackup –PointInTime $PointInTime -ResourceGroupName $Database.ResourceGroupName -ServerName $Database.ServerName -TargetDatabaseName $NewDatabaseName –ResourceId $Database.ResourceID -BackupStorageRedundancy 'Local'

# Verify the status of restored database
$RestoredDatabase.status
```


## <a name="next-steps"></a>Pasos siguientes

- [Restauración de un grupo de SQL dedicado existente (anteriormente SQL DW)](sql-data-warehouse-restore-active-paused-dw.md)
- [Restauración de un grupo de SQL dedicado eliminado (anteriormente SQL DW)](sql-data-warehouse-restore-deleted-dw.md)
