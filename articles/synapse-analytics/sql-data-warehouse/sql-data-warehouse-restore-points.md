---
title: Puntos de restauración definidos por el usuario
description: Cómo crear un punto de restauración para el grupo de SQL dedicado (anteriormente SQL DW).
services: synapse-analytics
author: rothja
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql-dw
ms.date: 07/03/2019
ms.author: jroth
ms.reviewer: igorstan
ms.custom: seo-lt-2019, devx-track-azurepowershell
ms.openlocfilehash: 39f8c336fdddf58d6cfa9cd8162e974d3dbc7c2d
ms.sourcegitcommit: 7d63ce88bfe8188b1ae70c3d006a29068d066287
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 07/22/2021
ms.locfileid: "114450953"
---
# <a name="user-defined-restore-points-for-a-dedicated-sql-pool-formerly-sql-dw"></a>Puntos de restauración definidos por el usuario para el grupo de SQL dedicado (anteriormente SQL DW).

En este artículo, aprenderá a crear un punto de restauración definido por el usuario para un grupo de SQL dedicado (anteriormente SQL DW) en Azure Synapse Analytics, mediante PowerShell y Azure Portal.

## <a name="create-user-defined-restore-points-through-powershell"></a>Creación de puntos de restauración definidos por el usuario mediante PowerShell

Para crear un punto de restauración definido por el usuario, utilice el cmdlet de PowerShell [New-AzSqlDatabaseRestorePoint](/powershell/module/az.sql/new-azsqldatabaserestorepoint?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json).

1. Antes de empezar, asegúrese de [instalar Azure PowerShell](/powershell/azure/?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json).
2. Abra PowerShell.
3. Conéctese a su cuenta de Azure y enumere todas las suscripciones asociadas a su cuenta.
4. Seleccione la suscripción que contiene la base de datos que se va a restaurar.
5. Cree un punto de restauración para realizar una copia inmediata de la base de datos de almacenamiento de datos.

```powershell

$SubscriptionName="<YourSubscriptionName>"
$ResourceGroupName="<YourResourceGroupName>"
$ServerName="<YourServerNameWithoutURLSuffixSeeNote>"  # Without database.windows.net
$DatabaseName="<YourDatabaseName>"
$Label = "<YourRestorePointLabel>"

Connect-AzAccount
Get-AzSubscription
Set-AzContext -SubscriptionName $SubscriptionName

# Create a restore point of the original database
New-AzSqlDatabaseRestorePoint -ResourceGroupName $ResourceGroupName -ServerName $ServerName -DatabaseName $DatabaseName -RestorePointLabel $Label
```

6. Consulte la lista de todos los puntos de restauración existentes.

```powershell
# List all restore points
Get-AzSqlDatabaseRestorePoint -ResourceGroupName $ResourceGroupName -ServerName $ServerName -DatabaseName $DatabaseName
```

## <a name="create-user-defined-restore-points-through-the-azure-portal"></a>Creación de puntos de restauración definidos por el usuario mediante Azure Portal

Los puntos de restauración definidos por el usuario también se pueden crear mediante Azure Portal.

1. Inicie sesión en su cuenta de [Azure Portal](https://portal.azure.com/).

2. Vaya al grupo de SQL dedicado (anteriormente SQL DW) para el que quiere crear un punto de restauración.

3. Seleccione **Información general** en el panel izquierdo y seleccione **Nuevo punto de restauración**. Si el botón Nuevo punto de restauración no está habilitado, asegúrese de que el grupo de SQL dedicado (anteriormente SQL DW) no esté en pausa.

    ![Nuevo punto de restauración](./media/sql-data-warehouse-restore-points/creating-restore-point-01.png)

4. Especifique el nombre del punto de restauración definido por el usuario y haga clic en **Aplicar**. Los puntos de restauración definidos por el usuario tienen un período de retención predeterminado de siete días.

    ![Nombre de punto de restauración](./media/sql-data-warehouse-restore-points/creating-restore-point-11.png)

## <a name="next-steps"></a>Pasos siguientes

- [Restauración de un grupo de SQL dedicado existente (anteriormente SQL DW)](sql-data-warehouse-restore-active-paused-dw.md)
- [Restauración de un grupo de SQL dedicado eliminado (anteriormente SQL DW)](sql-data-warehouse-restore-deleted-dw.md)
- [Restauración desde una copia de seguridad geográfica para un grupo de SQL dedicado (anteriormente SQL DW)](sql-data-warehouse-restore-from-geo-backup.md)
