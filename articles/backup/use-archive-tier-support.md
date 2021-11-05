---
title: Utilización del nivel de archivo
description: Obtenga más información sobre la utilización del nivel de archivo para Azure Backup.
ms.topic: conceptual
ms.date: 10/23/2021
ms.custom: devx-track-azurepowershell-azurecli
zone_pivot_groups: backup-client-powershelltier-clitier-portaltier
author: v-amallick
ms.service: backup
ms.author: v-amallick
ms.openlocfilehash: 9b35cd9269ef75f7c97d576c85004a4fe0be6a34
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/02/2021
ms.locfileid: "131090869"
---
# <a name="use-archive-tier-support"></a>Utilización de la compatibilidad con el nivel de archivo


::: zone pivot="client-powershelltier"

En este artículo se proporciona el procedimiento para realizar copias de seguridad de puntos de retención a largo plazo en el nivel de archivo, así como instantáneas y el nivel estándar mediante PowerShell.

## <a name="supported-workloads"></a>Cargas de trabajo compatibles

| Cargas de trabajo | Operations |
| --- | --- |
| Máquinas virtuales de Azure (versión preliminar)   <br><br>  SQL Server en máquinas virtuales de Azure   | <ul><li>Comprobación de los puntos de recuperación archivables    </li><li>Visualización de los puntos de recuperación recomendados (solo para máquinas virtuales)  </li><li>Mover los puntos de recuperación archivables   </li><li>Mover los puntos de recuperación recomendados (solo para máquinas virtuales de Azure)   </li><li>Visualización de los puntos de recuperación archivados   </li><li>Restauración desde puntos de recuperación archivados   </li></ul> |

## <a name="get-started"></a>Introducción

1. Descargue la versión [más reciente](https://github.com/PowerShell/PowerShell/releases) de PowerShell en GitHub.

1. En PowerShell, ejecute el siguiente cmdlet:
  
    ```azurepowershell
    install-module -name Az.RecoveryServices -Repository PSGallery -RequiredVersion 4.4.0 -AllowPrerelease -force
    ```

1. Conéctese a Azure mediante el cmdlet [Connect-AzAccount](/powershell/module/az.accounts/connect-azaccount).

1. Inicie sesión en su suscripción:

   ```azurepowershell
   Set-AzContext -Subscription "SubscriptionName"
   ```

1. Obtenga el almacén:

    ```azurepowershell
    $vault = Get-AzRecoveryServicesVault -ResourceGroupName "rgName" -Name "vaultName"
    ```

1. Obtenga la lista de elementos de copia de seguridad:

   - **Para máquinas virtuales de Azure**

       ```azurepowershell
       $BackupItemList = Get-AzRecoveryServicesBackupItem -vaultId $vault.ID -BackupManagementType "AzureVM" -WorkloadType "AzureVM"
       ```

   - **Para SQL Server en máquinas virtuales de Azure**

       ```azurepowershell
       $BackupItemList = Get-AzRecoveryServicesBackupItem -vaultId $vault.ID -BackupManagementType "AzureWorkload" -WorkloadType "MSSQL"
       ```

1. Obtenga el elemento de copia de seguridad.

   - **Para máquinas virtuales de Azure**

     ```azurepowershell
     $bckItm = $BackupItemList | Where-Object {$_.Name -match '<vmName>'}
     ```

   - **Para SQL Server en máquinas virtuales de Azure**

     ```azurepowershell
     $bckItm = $BackupItemList | Where-Object {$_.FriendlyName -eq '<dbName>' -and $_.ContainerName -match '<vmName>'}
     ```

1. (Opcional) Agregue el intervalo de fechas para el que desea ver los puntos de recuperación. Por ejemplo, si desea ver los puntos de recuperación de los últimos 120 días, use el siguiente cmdlet:

   ```azurepowershell
    $startDate = (Get-Date).AddDays(-120)
    $endDate = (Get-Date).AddDays(0) 
   ```
   >[!NOTE]
   >Para ver los puntos de recuperación de un intervalo de tiempo diferente, modifique las fechas de inicio y finalización en consecuencia. <br><br> Por defecto, se realiza para los últimos 30 días.

## <a name="check-the-archivable-status-of-all-recovery-points"></a>Comprobación del estado archivable de todos los puntos de recuperación

Ahora puede comprobar el estado archivable de todos los puntos de recuperación de un elemento de copia de seguridad mediante el siguiente cmdlet:

```azurepowershell
$rp = Get-AzRecoveryServicesBackupRecoveryPoint -VaultId $vault.ID -Item $bckItm -StartDate $startdate.ToUniversalTime() -EndDate $enddate.ToUniversalTime() 

$rp | select RecoveryPointId, @{ Label="IsArchivable";Expression={$_.RecoveryPointMoveReadinessInfo["ArchivedRP"].IsReadyForMove}}, @{ Label="ArchivableInfo";Expression={$_.RecoveryPointMoveReadinessInfo["ArchivedRP"].AdditionalInfo}}
```

## <a name="check-archivable-recovery-points"></a>Comprobación de los puntos de recuperación que se pueden archivar

```azurepowershell
$rp = Get-AzRecoveryServicesBackupRecoveryPoint -VaultId $vault.ID -Item $bckItm -StartDate $startdate.ToUniversalTime() -EndDate $enddate.ToUniversalTime() -IsReadyForMove $true -TargetTier VaultArchive
```

Con esto se enumerarán todos los puntos de recuperación asociados a un elemento de copia de seguridad determinado que estén listos para moverse al archivo (desde la fecha de inicio hasta la fecha de finalización). También puede modificar las fechas de inicio y de finalización.

## <a name="check-why-a-recovery-point-cant-be-moved-to-archive"></a>Comprobación de por qué un punto de recuperación no se puede mover al archivo

```azurepowershell
$rp = Get-AzRecoveryServicesBackupRecoveryPoint -VaultId $vault.ID -Item $bckItm -StartDate $startdate.ToUniversalTime() -EndDate $enddate.ToUniversalTime() -IsReadyForMove $false -TargetTier VaultArchive
$rp[0].RecoveryPointMoveReadinessInfo["ArchivedRP"]
```

Donde `$rp[0]` es el punto de recuperación para el que quiere comprobar por qué no se puede archivar.

**Salida de ejemplo**

```output
IsReadyForMove  AdditionalInfo
--------------  --------------
False           Recovery-Point Type is not eligible for archive move as it is already moved to archive tier
```

## <a name="check-recommended-set-of-archivable-points-only-for-azure-vms"></a>Comprobación del conjunto recomendado de puntos que se pueden archivar (solo para VM de Azure)

Los puntos de recuperación asociados con una máquina virtual tienen una naturaleza incremental. Cuando un punto de recuperación determinado se mueve a archivo, se convierte en una copia de seguridad completa y, a continuación, se mueve a archivo. Por lo tanto, el ahorro de costes asociado al traslado a archivo depende de la renovación del origen de datos.

Por lo tanto, Azure Backup proporciona un conjunto recomendado de puntos de recuperación que podrían ahorrar costes si se mueven juntos.

>[!NOTE]
>- El ahorro en los costes depende de varias razones y puede variar en cada caso.
>- El ahorro de costes solo se garantiza cuando se mueven todos los puntos de recuperación incluidos en la recomendación establecida al nivel de almacén-archivo.

```azurepowershell
$RecommendedRecoveryPointList = Get-AzRecoveryServicesBackupRecommendedArchivableRPGroup -Item $bckItm -VaultId $vault.ID
```

## <a name="move-to-archive"></a>Traslado al nivel de archivo

```azurepowershell
Move-AzRecoveryServicesBackupRecoveryPoint -VaultId $vault.ID -RecoveryPoint $rp[0] -SourceTier VaultStandard -DestinationTier VaultArchive
```

Donde, `$rp[0]` es el primer punto de recuperación de la lista. Si desea mover otros puntos de recuperación, use `$rp[1]`, `$rp[2]`, etc.

Este cmdlet mueve al archivo un punto de recuperación que se puede archivar. Devuelve un trabajo que se puede usar para hacer el seguimiento de la operación de movimiento desde el portal y con PowerShell.

## <a name="view-archived-recovery-points"></a>Visualización de puntos de recuperación archivados

Este cmdlet devuelve todos los puntos de recuperación archivados.

```azurepowershell
$rp = Get-AzRecoveryServicesBackupRecoveryPoint -VaultId $vault.ID -Item $bckItm -Tier VaultArchive -StartDate $startdate.ToUniversalTime() -EndDate $enddate.ToUniversalTime()
```

## <a name="restore-with-powershell"></a>Restauración con PowerShell

En el caso de los puntos de recuperación en archivo, Azure Backup proporciona una metodología de restauración integrada.
La restauración integrada es un proceso de dos pasos.

1. Implica rehidraticar los puntos de recuperación almacenados en archivo.
2. Se almacena temporalmente en el nivel estándar del almacén durante un período (también conocido como duración de rehidratación) que va de 10 a 30 días. El valor predeterminado es de 15 días. Hay dos prioridades diferentes de rehidratación: prioridad estándar y alta. Más información sobre la [prioridad de rehidratación](../storage/blobs/archive-rehydrate-overview.md#rehydration-priority).

>[!NOTE]
>
>- La duración de rehidratación no se puede cambiar una vez seleccionada, y los puntos de recuperación rehidratados permanecen en el nivel estándar durante este período de rehidratación.
>- El paso adicional de rehidratación conlleva un costo.

Para obtener más información sobre los distintos métodos de restauración de máquinas virtuales de Azure, consulte [Restauración de una VM de Azure con PowerShell](backup-azure-vms-automation.md#restore-an-azure-vm).

```azurepowershell
Restore-AzRecoveryServicesBackupItem -VaultLocation $vault.Location -RehydratePriority "Standard" -RehydrateDuration 15 -RecoveryPoint $rp -StorageAccountName "SampleSA" -StorageAccountResourceGroupName "SArgName" -TargetResourceGroupName $vault.ResourceGroupName -VaultId $vault.ID
```

Para restaurar SQL Server, siga [estos pasos](backup-azure-sql-automation.md#restore-sql-dbs). El `Restore-AzRecoveryServicesBackupItem` cmdlet requiere dos parámetros adicionales, `RehydrationDuration` y `RehydrationPriority`.

## <a name="view-jobs"></a>Ver trabajos

Para ver los trabajos de movimiento y restauración, use el siguiente cmdlet de PowerShell:

```azurepowershell
Get-AzRecoveryServicesBackupJob -VaultId $vault.ID
```

## <a name="move-recovery-points-to-archive-tier-at-scale"></a>Traslado de puntos de recuperación al nivel de acceso de archivo a escala

Ahora puede usar scripts de ejemplo para realizar operaciones a escala. [Obtenga más información](https://github.com/hiaga/Az.RecoveryServices/blob/master/README.md) sobre cómo ejecutar scripts de ejemplo. Puede descargar los scripts de [aquí](https://github.com/hiaga/Az.RecoveryServices).

Puede realizar las siguientes operaciones mediante los scripts de ejemplo proporcionados por Azure Backup:

- Mueva todos los puntos de recuperación aptos para una base de datos determinada o todas las bases de datos de un servidor SQL en la VM de Azure al nivel de archivo.
- Mueva todos los puntos de recuperación recomendados para una máquina virtual de Azure determinada al nivel de archivo.
 
También puede escribir un script según sus requisitos o modificar los scripts de ejemplo anteriores para capturar los elementos de copia de seguridad necesarios.

::: zone-end



::: zone pivot="client-clitier"

En este artículo se proporciona el procedimiento para realizar copias de seguridad de puntos de retención a largo plazo en el nivel de archivo, así como instantáneas y el nivel estándar mediante la Interfaz de la línea de comandos (CLI).

## <a name="supported-workloads"></a>Cargas de trabajo compatibles

| Cargas de trabajo | Operations |
| --- | --- |
| Máquinas virtuales de Azure (versión preliminar)   <br><br>  SQL Server en máquinas virtuales de Azure   <br><br>   SAP HANA en máquinas virtuales de Azure | <ul><li> Comprobación de los puntos de recuperación archivables       </li><li>Visualización de los puntos de recuperación recomendados (solo para máquinas virtuales)    </li><li>Mover los puntos de recuperación archivables    </li><li>Mover los puntos de recuperación recomendados (solo para máquinas virtuales de Azure)    </li><li>Visualización de los puntos de recuperación archivados    </li><li>Restauración desde puntos de recuperación archivados </li></ul> |


## <a name="get-started"></a>Introducción

1. Descargar o actualizar la versión de la CLI de AZ a la versión 2.26.0 o posterior 

   1. Siga las [instrucciones](/cli/azure/install-azure-cli) para instalar la CLI por primera vez.
   1. Ejecute az --upgrade para actualizar una versión ya instalada.

2. Inicie sesión utilizando el siguiente comando:

   ```azurecli
   az login
   ```

3. Establecimiento del contexto de la suscripción:

   ```azurecli
   az account set –s <subscriptionId>
   ```
## <a name="view-archivable-recovery-points"></a>Visualización de los puntos de recuperación archivables

Puede mover los puntos de recuperación archivables al nivel almacén-archivo mediante los siguientes comandos. [Obtenga más información](archive-tier-support.md#supported-workloads) sobre los criterios de elegibilidad.

- **Para máquinas virtuales de Azure**

  ```azurecli
  az backup recoverypoint list -g {rg} -v {vault} -c {container} -i {item} --backup-management-type {AzureIaasVM} --workload-type {VM}  --target-tier {VaultArchive} --is-ready-for-move {True}
  ```

- Para SQL Server en máquinas virtuales de Azure

  ```azurecli
  az backup recoverypoint list -g {rg} -v {vault} -c {container} -i {item} --backup-management-type {AzureWorkload} --workload-type {MSSQL}  --target-tier {VaultArchive} --is-ready-for-move {True}
  ```

- Para SAP HANA en máquinas virtuales de Azure

  ```azurecli
  az backup recoverypoint list -g {rg} -v {vault} -c {container} -i {item} --backup-management-type {AzureWorkload} --workload-type {SAPHANA}  --target-tier {VaultArchive} --is-ready-for-move {True}
  ```

## <a name="check-why-a-recovery-point-isnt-archivable"></a>Compruebe por qué no se puede archivar un punto de recuperación

Ejecute el siguiente comando:

```azurecli
az backup recoverypoint list -g {rg} -v {vault} -c {container} -i {item} --backup-management-type {AzureWorkload / AzureIaasVM} --workload-type {MSSQL / SAPHANA / VM}  --query [].{Name:name,move_ready:properties.recoveryPointMoveReadinessInfo.ArchivedRP.isReadyForMove,additional_details: properties.recoveryPointMoveReadinessInfo.ArchivedRP.additionalInfo
```

Recibirá una lista de todos los puntos de recuperación, tanto si se pueden archivar como si no se pueden archivar, así como el motivo por el cual no se pueden archivar

## <a name="check-recommended-set-of-archivable-points-only-for-azure-vms"></a>Comprobación del conjunto recomendado de puntos que se pueden archivar (solo para VM de Azure)

Ejecute el siguiente comando:

```azurecli
az backup recoverypoint list -g {rg} -v {vault} -c {container} -i {item} --backup-management-type { AzureIaasVM} --workload-type {VM} --recommended-for-archive
```

[Más información sobre](archive-tier-support.md#archive-recommendations-only-for-azure-virtual-machines) el conjunto de recomendaciones.

>[!Note]
>- El ahorro en los costes depende de varias razones y puede variar en cada caso.
>- Solo puede garantizar el ahorro de costes cuando todos los puntos de recuperación incluidos en el conjunto de recomendaciones se mueven al nivel de almacén-archivo.

## <a name="move-to-archive"></a>Traslado al nivel de archivo

Puede mover los puntos de recuperación archivables al nivel almacén-archivo mediante los siguientes comandos. El parámetro nombre en el comando debe incluir el nombre de un punto de recuperación archivable.

- **Para una máquina virtual de Azure**

  ```azurecli
  az backup recoverypoint move -g {rg} -v {vault} -c {container} -i {item} --backup-management-type { AzureIaasVM} --workload-type {VM} --source-tier {VaultStandard} --destination-tier {VaultArchive} --name {rp}
  ```
- **Para SQL Server en una máquina virtual de Azure**

  ```azurecli
  az backup recoverypoint move -g {rg} -v {vault} -c {container} -i {item} --backup-management-type {AzureWorkload} --workload-type {MSSQL} --source-tier {VaultStandard} --destination-tier {VaultArchive} --name {rp}
  ```
- **Para SAP HANA en una máquinas virtual de Azure**

  ```azurecli
  az backup recoverypoint move -g {rg} -v {vault} -c {container} -i {item} --backup-management-type {AzureWorkload} --workload-type {SAPHANA} --source-tier {VaultStandard} --destination-tier {VaultArchive} --name {rp}
  ```

## <a name="view-archived-recovery-points"></a>Visualización de puntos de recuperación archivados

Use los comandos siguientes:

- **Para máquinas virtuales de Azure**

    ```azurecli
    az backup recoverypoint list -g {rg} -v {vault} -c {container} -i {item} --backup-management-type {AzureWorkload } --workload-type {VM} -- tier {VaultArchive}
    ```
- **Para SQL Server en máquinas virtuales de Azure**

    ```azurecli
    az backup recoverypoint list -g {rg} -v {vault} -c {container} -i {item} --backup-management-type {AzureWorkload} --workload-type {MSSQL} -- tier {VaultArchive}
    ```
- **Para SAP HANA en máquinas virtuales de Azure**

    ```azurecli
    az backup recoverypoint list -g {rg} -v {vault} -c {container} -i {item} --backup-management-type {AzureWorkload} --workload-type {SAPHANA} -- tier {VaultArchive}
    ```

## <a name="restore"></a>Restauración 

Ejecute los comandos siguientes:

- **Para máquinas virtuales de Azure**

    ```azurecli
    az backup restore restore-disks -g {rg} -v {vault} -c {container} -i {item} --rp-name {rp} --storage-account {storage_account} --rehydration-priority {Standard / High} --rehydration-duration {rehyd_dur}
    ```

- **Para SQL Server en máquinas virtuales de Azure o SAP HANA en máquinas virtuales de Azure**

    ```azurecli
    az backup recoveryconfig show --resource-group saphanaResourceGroup \
        --vault-name saphanaVault \
        --container-name VMAppContainer;Compute;saphanaResourceGroup;saphanaVM \
        --item-name saphanadatabase;hxe;hxe \
        --restore-mode AlternateWorkloadRestore \
        --rp-name 7660777527047692711 \
        --target-item-name restored_database \
        --target-server-name hxehost \
        --target-server-type HANAInstance \
        --workload-type SAPHANA \
        --output json


    az backup restore restore-azurewl -g {rg} -v {vault} --recovery-config {recov_config} --rehydration-priority {Standard / High} --rehydration-duration {rehyd_dur}
    ```

::: zone-end



::: zone pivot="client-portaltier"

En este artículo se proporciona el procedimiento para realizar copias de seguridad de puntos de retención a largo plazo en el nivel de archivo, así como instantáneas y el nivel estándar mediante Azure portal.

## <a name="supported-workloads"></a>Cargas de trabajo compatibles

| Cargas de trabajo | Operations |
| --- | --- |
| Máquina virtual de Azure | <ul><li>Visualización de puntos de recuperación archivados    </li><li>Restauración de puntos de recuperación archivados   </li><li>Visualización de trabajos de movimiento y restauración de archivos </li></ul> |
| SQL Server en una máquina virtual de Azure/ <br> SAP HANA en máquinas virtuales de Azure | <ul><li>Visualización de puntos de recuperación archivados    </li><li>Traslado de toda la recuperación archivable al archivo   </li><li>Restauración desde puntos de recuperación archivados   </li><li>Visualización de trabajos de movimiento y restauración de archivos</li></ul> |

## <a name="view-archived-recovery-points"></a>Visualización de puntos de recuperación archivados

Ahora puede ver todos los puntos de recuperación que se han movido al nivel de archivo.

:::image type="content" source="./media/use-archive-tier-support/view-recovery-points-list-inline.png" alt-text="Captura de pantalla en la que se muestra la lista de recomendaciones." lightbox="./media/use-archive-tier-support/view-recovery-points-list-expanded.png":::


## <a name="move-archivable-recovery-points-for-a-particular-sqlsap-hana-database"></a>Mover puntos de recuperación archivables para una base de datos SQL/SAP HANA determinada

Ahora puede mover todos los puntos de recuperación de una base de datos SQL o SAP HANA de una sola vez.

Siga estos pasos:

1. Seleccione el elemento de copia de seguridad (base de datos en SQL Server o SAP HANA en la máquina virtual de Azure) cuyos puntos de recuperación desea mover al nivel de almacén-archivo.

2. Seleccione **hacer clic aquí** para ver los puntos de recuperación que tienen más de 7 días.

   :::image type="content" source="./media/use-archive-tier-support/view-old-recovery-points-inline.png" alt-text="Captura de pantalla que muestra el proceso para ver los puntos de recuperación que tienen más de 7 días." lightbox="./media/use-archive-tier-support/view-old-recovery-points-expanded.png":::

3. Para ver todos los puntos archivables aptos que se trasladarán al archivo, seleccione _Puntos de retención a largo plazo que se pueden mover al archivo. Para mover todos los "puntos de recuperación aptos" al nivel de archivo, haga clic aquí._

   :::image type="content" source="./media/use-archive-tier-support/view-all-eligible-archivable-points-for-move-inline.png" alt-text="Captura de pantalla que muestra el proceso para ver todos los puntos archivables aptos que se deben mover al archivo." lightbox="./media/use-archive-tier-support/view-all-eligible-archivable-points-for-move-expanded.png":::

   Se mostrarán todos los puntos de recuperación archivables.


   [Obtenga más información](archive-tier-support.md#supported-workloads) sobre los criterios de elegibilidad.

3. Haga clic en **Mover puntos de recuperación a archivo** para mover todos los puntos de recuperación al nivel almacén-archivo.

   :::image type="content" source="./media/use-archive-tier-support/move-all-recovery-points-to-vault-inline.png" alt-text="Captura de pantalla que muestra la opción de iniciar el proceso de traslado de todos los puntos de recuperación al nivel de almacén-archivo." lightbox="./media/use-archive-tier-support/move-all-recovery-points-to-vault-expanded.png":::

   >[!Note]
   >Esta opción mueve todos los puntos de recuperación archivables al almacén-archivo.

Puede supervisar el progreso en los trabajos de copia de seguridad.

## <a name="restore"></a>Restauración

Para restaurar los puntos de recuperación que se mueven al archivo, debe agregar los parámetros necesarios para la duración y la prioridad de la rehidratación.

:::image type="content" source="./media/use-archive-tier-support/restore-in-portal.png" alt-text="Captura de pantalla que muestra el proceso para restaurar puntos de recuperación en el portal.":::

## <a name="view-jobs"></a>Ver trabajos

:::image type="content" source="./media/use-archive-tier-support/view-jobs-portal.png" alt-text="Captura de pantalla que muestra el proceso para ver los trabajos en el portal.":::

## <a name="view-archive-usage-in-vault-dashboard"></a>Visualización del uso del archivo en el panel del almacén

También puede ver el uso del archivo en el panel del almacén.

:::image type="content" source="./media/use-archive-tier-support/view-archive-usage-in-vault-dashboard.png" alt-text="Captura de pantalla que muestra el uso del archivo en el panel del almacén.":::


::: zone-end

## <a name="next-steps"></a>Pasos siguientes

- [Solución de errores de nivel de archivo](troubleshoot-archive-tier.md)
