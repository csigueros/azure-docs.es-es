---
title: Automatización en Azure Backup
description: Proporciona un resumen de las funcionalidades de automatización que ofrece Azure Backup
ms.topic: conceptual
ms.date: 10/22/2021
author: v-amallick
ms.service: backup
ms.author: v-amallick
ms.openlocfilehash: ba817714fffb63e1c2b989c43e4a72c1d0f91235
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/02/2021
ms.locfileid: "131093235"
---
# <a name="automation-in-azure-backup"></a>Automatización en Azure Backup

A medida que administra las operaciones en Azure, el estado del archivo de copia de seguridad podría expandirse mediante la inclusión de un nuevo conjunto diverso de cargas de trabajo o escalado vertical en volumen.

A pequeña escala, las siguientes acciones podrían ser manuales o realizarse con facilidad: identificar las máquinas correctas de las que se va a realizar una copia de seguridad, configurar la copia de seguridad, supervisar su estado, extraer datos, y así sucesivamente. Pero a gran escala, estas acciones podrían ser difíciles, complejas y propensas a errores.

Azure Backup permite automatizar la mayoría de las tareas de copia de seguridad mediante métodos de programación. En este documento se proporcionan los distintos clientes de automatización admitidos por Azure Backup. Esto también sirve de guía a través de algunos escenarios de automatización de un extremo a otro usados habitualmente en las implementaciones de copia de seguridad a escala empresarial.

## <a name="automation-methods"></a>Métodos de automatización

Para acceder a la funcionalidad de Azure Backup, puede usar los siguientes métodos de automatización estándar compatibles con Azure:

- PowerShell
- CLI
- API DE REST
- SDK de Python
- Go SDK
- Terraform
- Plantillas de ARM
- Bicep

También puede usar Azure Backup asociado a otros servicios de Azure, como Logic Apps, Runbooks y grupos de acciones, para configurar flujos de trabajo de automatización de un extremo a otro.

Para obtener más información sobre los distintos escenarios que admiten los clientes de automatización y las referencias de documento correspondientes, consulte la sección de [soluciones de automatización compatibles con Azure Backup](#supported-automation-methods-by-operation-type).

## <a name="sample-automation-scenarios"></a>Escenarios de automatización de ejemplo

En esta sección se proporcionan algunos casos de uso de automatización comunes que puede encontrar como administrador de la copia de seguridad. También proporciona instrucciones sobre cómo empezar a trabajar.

### <a name="configure-backups"></a>Configuración de copias de seguridad

Como administrador de la copia de seguridad, debe tratar con la nueva infraestructura que se agrega periódicamente y asegurarse de que estén protegidas según los requisitos acordados. Los clientes de automatización, como PowerShell/CLI, ayudan a capturar todos los detalles de la máquina virtual, comprobar el estado de copia de seguridad de cada uno de ellos y, a continuación, tomar las medidas adecuadas para las máquinas virtuales sin protección.

Sin embargo, debe tener un rendimiento a escala. Además, debe programarlos periódicamente y supervisar cada ejecución. Para facilitar las operaciones de automatización, Azure Backup ahora usa Azure Policy y proporciona [directivas de Azure específicas de copia de seguridad integradas](backup-center-govern-environment.md#azure-policies-for-backup) para regular el estado de la copia de seguridad.

Una vez que asigna una directiva de Azure a un ámbito, se realiza una copia de seguridad automática de todas las máquinas virtuales que cumplen los criterios y las más recientes se examinan y protegen periódicamente mediante Azure Policy. También puede ver un informe de cumplimiento que le avisa de los recursos no compatibles.

[Obtenga más información sobre las directivas de Azure integradas para la copia de seguridad](backup-azure-auto-enable-backup.md).

En el vídeo siguiente se muestra cómo funciona Azure Policy para la copia de seguridad:  <br><br>

> [!VIDEO https://channel9.msdn.com/Shows/IT-Ops-Talk/Configure-backups-at-scale-using-Azure-Policy/player]

### <a name="export-backup-operational-data"></a>Exportación de datos operativos de la copia de seguridad

Es posible que tenga que extraer los datos operativos de la copia de seguridad de todo el patrimonio e importarlos periódicamente a los sistemas o paneles de supervisión. A gran escala, los datos se deben recuperar rápidamente (si se consultan registros enormes). Debe consultar entre recursos, suscripciones e inquilinos. También debe consultar desde un cliente (Azure Portal/PowerShell/CLI/cualquier SDK/API de REST). También debe haber flexibilidad en el formato de salida (tabla frente a matriz).

Azure Resource Graph (ARG) permite realizar estas operaciones y realizar consultas a escala. Azure Backup usa ARG como una manera optimizada de capturar los datos necesarios con consultas mínimas (una consulta para un escenario). Por ejemplo, una sola consulta puede capturar todos los trabajos con errores en todos los almacenes de todas las suscripciones y todos los inquilinos. Además, las consultas son compatibles con el control de acceso basado en rol (RBAC) de Azure.

Vea las [consultas ARG de ejemplo](query-backups-using-azure-resource-graph.md#sample-queries).

### <a name="automate-responsesactions"></a>Automatización de respuestas y acciones

La automatización de las respuestas a errores transitorios de una copia de seguridad le ayuda a asegurarse de que tiene el número correcto de copias de seguridad confiables desde las que restaurar. Esto también ayuda a evitar infracciones involuntarias en el [RPO](azure-backup-glossary.md#rpo-recovery-point-objective).

Puede configurar un flujo de trabajo para reintentar los trabajos de copia de seguridad mediante una combinación de Runbooks de Azure Automation, PowerShell y Azure Resource Graph. Esto ayuda en escenarios en los que se ha producido un error en los trabajos de copia de seguridad debido a un error transitorio o a una interrupción planeada o no planeada.

Para comenzar, asegúrese de lo siguiente:

1. Cree una cuenta de Automation y un nuevo Runbook de PowerShell en la cuenta de Automation. [Más información](/azure/automation/learn/powershell-runbook-managed-identity).

2. Inserte el siguiente script en el cuerpo del runbook. 

   El script ejecuta una consulta ARG para capturar la lista de todas las máquinas virtuales con errores de trabajo recientes (puede agregar un filtro en _startTime_ a la consulta) y, a continuación, desencadena una copia de seguridad a petición para cada máquina virtual. Puede crear consultas similares para capturar la lista de todas las bases de datos de SQL, bases de datos de HANA, Azure Files y otras cargas de trabajo de Azure de las que se va a realizar una copia de seguridad.

    ```azurepowershell
    $connection = Get-AutomationConnection -Name AzureRunAsConnection
    $connectionResult = Connect-AzAccount `
    -ServicePrincipal `
    -Tenant $connection.TenantID `
    -ApplicationId $connection.ApplicationID `
    -CertificateThumbprint $connection.CertificateThumbprint
    "Login successful.."

    $query = "RecoveryServicesResources
    | where type in~ ('microsoft.recoveryservices/vaults/backupjobs')
    | extend vaultName = case(type =~ 'microsoft.dataprotection/backupVaults/backupJobs',properties.vaultName,type =~ 'Microsoft.RecoveryServices/vaults/backupJobs',split(split(id, '/Microsoft.RecoveryServices/vaults/')[1],'/')[0],'--')
    | extend friendlyName = case(type =~ 'microsoft.dataprotection/backupVaults/backupJobs',strcat(properties.dataSourceSetName , '/', properties.dataSourceName),type =~ 'Microsoft.RecoveryServices/vaults/backupJobs', properties.entityFriendlyName, '--')
    | extend dataSourceType = case(type =~ 'Microsoft.RecoveryServices/vaults/backupJobs',properties.backupManagementType,type =~ 'microsoft.dataprotection/backupVaults/backupJobs',properties.dataSourceType,'--')
    | extend protectedItemName = split(split(properties.backupInstanceId, 'protectedItems')[1],'/')[1]
    | extend vaultId = tostring(split(id, '/backupJobs')[0])
    | extend vaultSub = tostring( split(id, '/')[2])
    | extend jobStatus = case (properties.status == 'Completed' or properties.status == 'CompletedWithWarnings','Succeeded',properties.status == 'Failed','Failed',properties.status == 'InProgress', 'Started', properties.status), operation = case(type =~ 'microsoft.dataprotection/backupVaults/backupJobs' and tolower(properties.operationCategory) =~ 'backup' and properties.isUserTriggered == 'true',strcat('adhoc',properties.operationCategory),type =~ 'microsoft.dataprotection/backupVaults/backupJobs', tolower(properties.operationCategory), type =~ 'Microsoft.RecoveryServices/vaults/backupJobs' and tolower(properties.operation) =~ 'backup' and properties.isUserTriggered == 'true',strcat('adhoc',properties.operation),type =~ 'Microsoft.RecoveryServices/vaults/backupJobs',tolower(properties.operation), '--'), startTime = todatetime(properties.startTime),endTime = properties.endTime, duration = properties.duration 
    | where (dataSourceType in~ ('AzureIaasVM'))
    | where jobStatus=='Failed'
    | where operation == 'backup' or operation == 'adhocBackup'
    | project vaultSub, vaultId, protectedItemName, startTime, endTime, jobStatus, operation
    | sort by vaultSub"

    $subscriptions = Get-AzSubscription | foreach {$_.SubscriptionId}
    $result = Search-AzGraph -Subscription $subscriptions -Query $query -First 5
    $result = $result.data
    $prevsub = ""
    foreach($jobresponse in $result)
    {
                if($jobresponse.vaultSub -ne $prevsub)
                {
                            Set-AzContext -SubscriptionId $jobresponse.vaultSub
                            $prevsub = $jobresponse.vaultSub
                }

                $item = Get-AzRecoveryServicesBackupItem -VaultId $jobresponse.vaultId -BackupManagementType AzureVM -WorkloadType AzureVM -Name $jobresponse.protectedItemName

                Backup-AzRecoveryServicesBackupItem -ExpiryDateTimeUTC (get-date).AddDays(10) -Item $item -VaultId $jobresponse.vaultId
    }
    ```

3. Importe los módulos siguientes en el runbook para asegurarse de que el script se ejecuta correctamente: `Az.Accounts`, `Az.RecoveryServices`, `Az.Graph`.

   [Obtenga información sobre cómo importar módulos en un runbook](/azure/automation/shared-resources/modules).

4. [Vincule el runbook a una programación](/azure/automation/shared-resources/schedules) para configurar el script a fin de que se ejecute automáticamente a intervalos regulares.

   Consulte el siguiente vídeo para ver un tutorial de un extremo a otro del escenario: <br><br>

   > [!VIDEO https://channel9.msdn.com/Shows/IT-Ops-Talk/Automatically-retry-failed-backup-jobs-using-Azure-Resource-Graph-and-Azure-Automation-Runbooks/player]

## <a name="supported-automation-methods-by-operation-type"></a>Métodos de automatización admitidos por tipo de operación

| **Carga de trabajo** | **Categoría** | **Operación** | **PowerShell** | **CLI** | **REST API** | **Azure Policy** | **Plantilla de ARM** |
| --- | --- | --- | --- | --- | --- | --- | --- |
| Azure VM | Backup | Creación de una directiva de copia de seguridad y configuración de la misma | Compatible <br><br> [Consulte los ejemplos](/azure/backup/backup-azure-vms-automation#back-up-azure-vms). | Compatible <br><br> [Consulte los ejemplos](/azure/backup/quick-backup-vm-cli#enable-backup-for-an-azure-vm) | Compatible  <br><br>  [Consulte los ejemplos](/azure/backup/backup-azure-arm-userestapi-backupazurevms). | Compatible  <br><br> [Consulte los ejemplos](/azure/backup/backup-azure-auto-enable-backup). | Compatible  <br><br> [Consulte los ejemplos](/azure/backup/backup-rm-template-samples). |
| Azure VM | Backup | Copia de seguridad de discos selectiva | Compatible  <br><br> [Consulte los ejemplos](/azure/backup/selective-disk-backup-restore#using-powershell) | Compatible  <br><br> [Consulte los ejemplos](/azure/backup/selective-disk-backup-restore#using-azure-cli). | Compatible  <br><br> [Consulte los ejemplos](/azure/backup/backup-azure-arm-userestapi-backupazurevms#excluding-disks-in-azure-vm-backup). | N/D | N/D |
| Azure VM | Backup | Ejecución de la copia de seguridad a petición | Compatible   <br><br> [Consulte los ejemplos](/azure/backup/quick-backup-vm-powershell#start-a-backup-job). | Compatible:  <br><br> [Consulte los ejemplos](/azure/backup/quick-backup-vm-cli#start-a-backup-job). | Compatible   <br><br> [Consulte los ejemplos](/azure/backup/backup-azure-arm-userestapi-backupazurevms#trigger-an-on-demand-backup-for-a-protected-azure-vm). | N/D | N/D |
| Azure VM | Restauración | Restauración de discos en la región primaria | Compatible   <br><br> [Consulte los ejemplos](/azure/backup/backup-azure-vms-automation#restore-an-azure-vm). | Compatible  <br><br> [Consulte los ejemplos](/azure/backup/tutorial-restore-disk#restore-a-vm-disk). | Compatible  <br><br> [Consulte los ejemplos](/azure/backup/backup-azure-arm-userestapi-restoreazurevms). | N/D | N/D |
| Azure VM | Restauración | Restauración entre regiones | Compatible   <br><br> [Consulte los ejemplos](/azure/backup/backup-azure-vms-automation#restore-disks-to-a-secondary-region). | Compatible   <br><br> [Consulte los ejemplos](/cli/azure/backup/restore?view=azure-cli-latest#az_backup_restore_restore_disks&preserve-view=true). | Compatible   <br><br> [Consulte los ejemplos](/azure/backup/backup-azure-arm-userestapi-restoreazurevms#cross-region-restore). | N/D | N/D |
| Azure VM | Restauración | Restauración selectiva de discos | Compatible   <br><br> [Consulte los ejemplos](/azure/backup/backup-azure-vms-automation#restore-selective-disks). | Compatible   <br><br> [Consulte los ejemplos](/azure/backup/selective-disk-backup-restore#restore-disks-with-azure-cli). | Compatible   <br><br> [Consulte los ejemplos](/azure/backup/backup-azure-arm-userestapi-restoreazurevms#restore-disks-selectively). | N/D | N/D |
| Azure VM | Restauración | Creación de una máquina virtual a partir de discos restaurados | Compatible   <br><br> [Consulte los ejemplos](/azure/backup/backup-azure-vms-automation#using-managed-identity-to-restore-disks). | Compatible   <br><br> [Consulte los ejemplos](/azure/backup/tutorial-restore-disk#using-managed-identity-to-restore-disks). | Compatible   <br><br> [Consulte los ejemplos](/rest/api/backup/restores/trigge). | N/D | N/D |
| Azure VM | Restauración | Restauración de archivos | Compatible   <br><br> [Consulte los ejemplos](/azure/backup/backup-azure-vms-automation#create-a-vm-from-restored-disks). | Compatible   <br><br> [Consulte los ejemplos](/azure/backup/tutorial-restore-disk#create-a-vm-from-the-restored-disk). | Compatible   <br><br> [Consulte los ejemplos](/azure/backup/backup-azure-arm-userestapi-restoreazurevms#restore-disks). | N/D | N/D |
| Azure VM | Administrar | Supervisión de trabajos | Compatible   <br><br> [Consulte los ejemplos](/azure/backup/backup-azure-vms-automation#restore-files-from-an-azure-vm-backup). | Compatible   <br><br> [Consulte los ejemplos](/azure/backup/tutorial-restore-files). | N/D | N/D |
| Azure VM | Administrar | Modificación de la directiva de copia de seguridad | Compatible   <br><br> [Consulte los ejemplos](/azure/backup/backup-azure-vms-automation#monitoring-a-backup-job). | Compatible   <br><br> [Consulte los ejemplos](/azure/backup/quick-backup-vm-cli#monitor-the-backup-job). | Compatible   <br><br> [Consulte los ejemplos](/azure/backup/backup-azure-arm-userestapi-managejobs#tracking-the-job). | N/D | N/D |
| Azure VM | Administrar | Detener la protección y conservar los datos de copia de seguridad | Compatible   <br><br> [Consulte los ejemplos](/azure/backup/backup-azure-vms-automation#retain-data). | Compatible   <br><br> [Consulte los ejemplos](/cli/azure/backup/protection?view=azure-cli-latest#az_backup_protection_disable&preserve-view=true). | Compatible   <br><br> [Consulte los ejemplos](/azure/backup/backup-azure-arm-userestapi-backupazurevms#stop-protection-but-retain-existing-data). | N/D | N/D |
| Azure VM | Administrar | Detener la protección y eliminar los datos de copia de seguridad | Compatible   <br><br> [Consulte los ejemplos](/azure/backup/backup-azure-vms-automation#delete-backup-data). | Compatible   <br><br> [Consulte los ejemplos](/cli/azure/backup/protection?view=azure-cli-latest#az_backup_protection_disable&preserve-view=true). | Compatible   <br><br> [Consulte los ejemplos](/azure/backup/backup-azure-arm-userestapi-backupazurevms#stop-protection-and-delete-data). | N/D | N/D |
| Azure VM | Administrar | Reanudar protección | Compatible   <br><br> [Consulte los ejemplos](/azure/backup/backup-azure-vms-automation#resume-backup).    | Compatible    <br><br> [Consulte los ejemplos](/cli/azure/backup/protection?view=azure-cli-latest#az_backup_protection_resume&preserve-view=true). | Compatible    <br><br> [Consulte los ejemplos](/azure/backup/backup-azure-arm-userestapi-backupazurevms#undo-the-deletion) | N/D | N/D |
| SQL en Azure VM | Backup | Creación de una directiva de copia de seguridad y configuración de la misma | Compatible    <br><br> [Consulte los ejemplos](/azure/backup/backup-azure-sql-automation#configure-a-backup-policy). | Compatible | Compatible | No se usa actualmente | Compatible    <br><br> [Consulte los ejemplos](/azure/backup/backup-rm-template-samples). |
| SQL en Azure VM | Backup | Habilitación de la protección automática | Compatible    <br><br> [Consulte los ejemplos](/azure/backup/backup-azure-sql-automation#enable-autoprotection) | Compatible | Compatible | N/D | N/D |
| SQL en Azure VM | Backup | Ejecución de la copia de seguridad a petición | Compatible    <br><br> [Consulte los ejemplos](/azure/backup/backup-azure-sql-automation#on-demand-backup). | Compatible | Compatible | N/D | N/D |
| SQL en Azure VM | Restauración | Restauración a un punto de recuperación completo o diferencial distinto | Compatible    <br><br> [Consulte los ejemplos](/azure/backup/backup-azure-sql-automation#original-restore-with-distinct-recovery-point). | Compatible | Compatible | N/D | N/D |
| SQL en Azure VM | Restauración | Restauración a un punto en el tiempo | Compatible    <br><br> [Consulte los ejemplos](/azure/backup/backup-azure-sql-automation#original-restore-with-log-point-in-time). | Compatible | Compatible | N/D | N/D |
| SQL en Azure VM | Restauración | Restauración entre regiones | Compatible    <br><br> [Consulte los ejemplos](/azure/backup/backup-azure-sql-automation#alternate-workload-restore-to-a-vault-in-secondary-region). | Compatible | Compatible | N/D | N/D |
| SQL en Azure VM | Administrar | Supervisión de trabajos | Compatible    <br><br> [Consulte los ejemplos](/azure/backup/backup-azure-sql-automation#track-azure-backup-jobs). | Compatible | Compatible | N/D | N/D |
| SQL en Azure VM | Administrar | Administración de las alertas de Azure Monitor (versión preliminar) | Compatible    <br><br> [Consulte los ejemplos](/azure/azure-monitor/powershell-samples). | Compatible | Compatible | N/D | N/D |
| SQL en Azure VM | Administrar | Administración de las métricas de Azure Monitor (versión preliminar) | Compatible    <br><br> [Consulte los ejemplos](/azure/azure-monitor/powershell-samples). | Compatible | Compatible | N/D | N/D |
| SQL en Azure VM | Administrar | Modificación de la directiva de copia de seguridad | Compatible    <br><br> [Consulte los ejemplos](/azure/backup/backup-azure-sql-automation#change-policy-for-backup-items). | Compatible | Compatible | N/D | N/D |
| SQL en Azure VM | Administrar | Detener la protección y conservar los datos de copia de seguridad | Compatible    <br><br> [Consulte los ejemplos](/azure/backup/backup-azure-sql-automation#retain-data). | Compatible | Compatible | N/D | N/D |
| SQL en Azure VM | Administrar | Detener la protección y eliminar los datos de copia de seguridad | Compatible    <br><br> [Consulte los ejemplos](/azure/backup/backup-azure-sql-automation#delete-backup-data). | Compatible | Compatible | N/D | N/D |
| SQL en Azure VM | Administrar | Anulación del registro de la instancia | Compatible    <br><br> [Consulte los ejemplos](/azure/backup/backup-azure-sql-automation#unregister-sql-vm). | Compatible | Compatible | N/D | N/D |
| SQL en Azure VM | Administrar | Volver a registrar la instancia | Compatible    <br><br> [Consulte los ejemplos](/azure/backup/backup-azure-sql-automation#re-register-sql-vms). | Compatible | Compatible | N/D | N/D |
| SAP HANA en la máquina virtual de Azure | Backup | Creación de una directiva de copia de seguridad y configuración de la misma | Compatible | Compatible    <br><br> [Consulte los ejemplos](/azure/backup/tutorial-sap-hana-backup-cli#register-and-protect-the-sap-hana-instance). | Compatible | Actualmente no se admite | N/D |
| SAP HANA en la máquina virtual de Azure | Backup | Ejecución de la copia de seguridad a petición | Compatible | Compatible    <br><br> [Consulte los ejemplos](/azure/backup/tutorial-sap-hana-backup-cli#trigger-an-on-demand-backup). | Compatible | N/D | Compatible: ejemplos   <br><br> [Consulte los ejemplos](/azure/backup/backup-rm-template-samples). |
| SAP HANA en la máquina virtual de Azure | Restauración | Restauración a un punto de recuperación completo, en incremento o diferencial distinto | Compatible: | Compatible    <br><br> [Consulte los ejemplos](/azure/backup/tutorial-sap-hana-restore-cli#restore-a-database). | Compatible | N/D |    |
| SAP HANA en la máquina virtual de Azure | Restauración | Restauración a un punto en el tiempo | Compatible  | Compatible    <br><br> [Consulte los ejemplos](/azure/backup/tutorial-sap-hana-restore-cli#restore-a-database). | Compatible | N/D | N/D |
| SAP HANA en la máquina virtual de Azure | Restauración | Restauración entre regiones | Compatible: | Compatible | Compatible | N/D | N/D |
| SAP HANA en la máquina virtual de Azure | Administrar | Supervisión de trabajos | Compatible  | Compatible | Compatible | N/D | N/D |
| SAP HANA en la máquina virtual de Azure | Administrar | Modificación de la directiva de copia de seguridad | Compatible: | Compatible    <br><br> [Consulte los ejemplos](/azure/backup/tutorial-sap-hana-manage-cli#change-policy). | Compatible | N/D | N/D |
| SAP HANA en la máquina virtual de Azure | Administrar | Detener la protección y conservar los datos de copia de seguridad | Compatible  | Compatible    <br><br> [Consulte los ejemplos](/azure/backup/tutorial-sap-hana-manage-cli#stop-protection-with-retain-data) | Compatible    <br><br> [Consulte los ejemplos](/azure/backup/backup-azure-arm-userestapi-createorupdatepolicy). | N/D | N/D |
| SAP HANA en la máquina virtual de Azure | Administrar | Detener la protección y eliminar los datos de copia de seguridad | Compatible  | Compatible    <br><br> [Consulte los ejemplos](/azure/backup/tutorial-sap-hana-manage-cli#stop-protection-without-retain-data). | Compatible    <br><br> [Consulte los ejemplos](/rest/api/backup/protected-items/delete). | N/D | N/D |
| SAP HANA en la máquina virtual de Azure | Administrar | Anulación del registro de la instancia | Compatible  | Compatible | Compatible | N/D | N/D |
| SAP HANA en la máquina virtual de Azure | Administrar | Volver a registrar la instancia | Compatible  | Compatible | Compatible | N/D | N/D |
| Azure Files | Backup | Creación de una directiva de copia de seguridad y configuración de la misma | Compatible    <br><br> [Consulte los ejemplos](/azure/backup/backup-azure-afs-automation#configure-a-backup-policy). | Compatible    <br><br> [Consulte los ejemplos](/azure/backup/backup-afs-cli#enable-backup-for-azure-file-shares). | Compatible    <br><br> [Consulte los ejemplos](/azure/backup/backup-azure-file-share-rest-api#configure-backup-for-an-unprotected-azure-file-share-using-rest-api). | Actualmente no se admite | Compatible    <br><br> [Consulte los ejemplos](/azure/backup/backup-rm-template-samples). |
| Azure Files | Backup | Ejecución de la copia de seguridad a petición | Compatible    <br><br> [Consulte los ejemplos](/azure/backup/backup-azure-afs-automation#trigger-an-on-demand-backup). | Compatible    <br><br> [Consulte los ejemplos](/azure/backup/backup-afs-cli#trigger-an-on-demand-backup-for-file-share). | Compatible    <br><br> [Consulte los ejemplos](/azure/backup/backup-azure-file-share-rest-api#trigger-an-on-demand-backup-for-file-share). | N/D | N/D |
| Azure Files | Restauración | Restauración en una ubicación original o alternativa | Compatible    <br><br> [Consulte los ejemplos](/azure/backup/restore-afs-powershell). | Compatible    <br><br> [Consulte los ejemplos](/azure/backup/restore-afs-cli). | Compatible    <br><br> [Consulte los ejemplos](/azure/backup/restore-azure-file-share-rest-api). | N/D | N/D |
| Azure Files | Administrar | Supervisión de trabajos | Compatible    <br><br> [Consulte los ejemplos](/azure/backup/manage-afs-powershell#track-backup-and-restore-jobs). | Compatible    <br><br> [Consulte los ejemplos](/azure/backup/manage-afs-backup-cli#monitor-jobs). | Compatible    <br><br> [Consulte los ejemplos](/azure/backup/manage-azure-file-share-rest-api#monitor-jobs). | N/D | N/D |
| Azure Files | Administrar | Modificación de la directiva de copia de seguridad | Compatible    <br><br> [Consulte los ejemplos](/azure/backup/manage-afs-powershell#modify-the-protection-policy). | Compatible    <br><br> [Consulte los ejemplos](/azure/backup/manage-afs-backup-cli#modify-policy). | Compatible    <br><br> [Consulte los ejemplos](/azure/backup/manage-azure-file-share-rest-api#modify-policy). | N/D | N/D |
| Azure Files | Administrar | Detener la protección y conservar los datos de copia de seguridad | Compatible    <br><br> [Consulte los ejemplos](/azure/backup/manage-afs-powershell#stop-protection-and-retain-recovery-points). | Compatible    <br><br> [Consulte los ejemplos](/azure/backup/manage-afs-backup-cli#stop-protection-and-retain-recovery-points). | Compatible    <br><br> [Consulte los ejemplos](/azure/backup/manage-azure-file-share-rest-api#stop-protection-but-retain-existing-data). | N/D | N/D |
| Azure Files | Administrar | Detener la protección y eliminar los datos de copia de seguridad | Compatible    <br><br> [Consulte los ejemplos](/azure/backup/manage-afs-powershell#stop-protection-without-retaining-recovery-points). | Compatible    <br><br> [Consulte los ejemplos](/azure/backup/manage-afs-backup-cli#stop-protection-without-retaining-recovery-points). | Compatible    <br><br> [Consulte los ejemplos](/azure/backup/manage-azure-file-share-rest-api#stop-protection-and-delete-data). | N/D | N/D |
| Azure Blobs | Backup | Creación de una directiva de copia de seguridad y configuración de la misma | Compatible    <br><br> [Consulte los ejemplos](/azure/backup/backup-blobs-storage-account-ps). | Compatible    <br><br> [Consulte los ejemplos](/azure/backup/backup-blobs-storage-account-cli). | Compatible    <br><br> [Consulte los ejemplos](/azure/backup/backup-azure-dataprotection-use-rest-api-backup-blobs). | Actualmente no se admite | Compatible |
| Azure Blobs | Restauración | Restauración de blobs | Compatible    <br><br> [Consulte los ejemplos](/azure/backup/restore-blobs-storage-account-ps). | Compatible    <br><br> [Consulte los ejemplos](/azure/backup/restore-blobs-storage-account-cli). | Compatible    <br><br> [Consulte los ejemplos](/azure/backup/backup-azure-dataprotection-use-rest-api-restore-blobs). | N/D | N/D |
| Azure Blobs | Administrar | Supervisión de trabajos | Compatible    <br><br> [Consulte los ejemplos](/azure/backup/restore-blobs-storage-account-ps#tracking-job). | Compatible    <br><br> [Consulte los ejemplos](/azure/backup/restore-blobs-storage-account-cli#tracking-job). | Compatible    <br><br> [Consulte los ejemplos](/azure/backup/backup-azure-dataprotection-use-rest-api-restore-blobs#tracking-jobs). | N/D | N/D |
| Azure Blobs | Administrar | Modificación de la directiva de copia de seguridad | Actualmente no se admite | Actualmente no se admite | Actualmente no se admite | N/D | N/D |
| Azure Blobs | Administrar | Detener la protección y conservar los datos de copia de seguridad | Actualmente no se admite | Actualmente no se admite | Actualmente no se admite | N/D | N/D |
| Azure Blobs | Administrar | Detener la protección y eliminar los datos de copia de seguridad | Compatible | Compatible | Compatible | N/D | N/D |
| Azure Blobs | Administrar | Reanudar protección | Actualmente no se admite | Actualmente no se admite | Actualmente no se admite | N/D | N/D |
| Azure Disks | Backup | Creación de una directiva de copia de seguridad y configuración de la misma | Compatible   <br><br> [Consulte los ejemplos](/azure/backup/backup-managed-disks-ps). | Compatible    <br><br> [Consulte los ejemplos](/azure/backup/backup-managed-disks-cli). | Compatible    <br><br> [Consulte los ejemplos](/azure/backup/backup-azure-dataprotection-use-rest-api-backup-disks). | Actualmente no se admite | Compatible |
| Azure Disks | Backup | Ejecución de la copia de seguridad a petición | Compatible    <br><br> [Consulte los ejemplos](/azure/backup/backup-managed-disks-ps#run-an-on-demand-backup). | Compatible    <br><br> [Consulte los ejemplos](/azure/backup/backup-managed-disks-cli#run-an-on-demand-backup). |  N/D | N/D |
| Azure Disks | Restauración | Restauración en el nuevo disco | Compatible    <br><br> [Consulte los ejemplos](/azure/backup/restore-managed-disks-ps). | Compatible    <br><br> [Consulte los ejemplos](/azure/backup/restore-managed-disks-cli). | Compatible    <br><br> [Consulte los ejemplos](/azure/backup/backup-azure-dataprotection-use-rest-api-restore-disks). | N/D | N/D |
| Azure Disks | Administrar | Supervisión de trabajos | Compatible    <br><br> [Consulte los ejemplos](/azure/backup/restore-managed-disks-ps#tracking-job). | Compatible    <br><br> [Consulte los ejemplos](/azure/backup/restore-managed-disks-cli#tracking-job). | Compatible    <br><br> [Consulte los ejemplos](/azure/backup/backup-azure-dataprotection-use-rest-api-restore-disks#track-jobs). | N/D | N/D |
| Azure Disks | Administrar | Modificación de la directiva de copia de seguridad | Actualmente no se admite | Actualmente no se admite | Actualmente no se admite | N/D | N/D |
| Azure Disks | Administrar | Detener la protección y conservar los datos de copia de seguridad | Compatible | Compatible | Compatible | N/D | N/D |
| Azure Disks | Administrar | Detener la protección y eliminar los datos de copia de seguridad | Compatible | Compatible | Compatible | N/D | N/D |
| Azure Disks | Administrar | Reanudar protección | Compatible | Compatible | Compatible | N/D | N/D |
| Servidor de Azure Database for PostgreSQL | Backup | Creación de una directiva de copia de seguridad y configuración de la misma | Compatible | Compatible | Compatible | No se usa actualmente | Compatible |
| Servidor de Azure Database for PostgreSQL | Backup | Ejecución de la copia de seguridad a petición | Compatible | Compatible | Compatible | N/D | N/D |
| Servidor de Azure Database for PostgreSQL | Restauración | Restauración de una base de datos en la cuenta de almacenamiento de destino | Compatible | Compatible | Compatible | N/D | N/D |
| Servidor de Azure Database for PostgreSQL | Administrar | Modificación de la directiva de copia de seguridad | Compatible | Compatible | Compatible | N/D | N/D |
| Servidor de Azure Database for PostgreSQL | Administrar | Detener la protección y eliminar los datos | Compatible | Compatible | Compatible | N/D | N/D |
| Servidor de Azure Database for PostgreSQL | Administrar | Detener la protección y conservar los datos | Compatible | Compatible | Compatible | N/D | N/D |
| Servidor de Azure Database for PostgreSQL | Administrar | Reanudar protección | Compatible | Compatible | Compatible | N/D | N/D |
| Configuraciones de nivel de almacén | Administrar | Crear almacén de Recovery Services | Compatible    <br><br> [Consulte los ejemplos](/azure/backup/backup-azure-vms-automation#create-a-recovery-services-vault). | Compatible    <br><br> [Consulte los ejemplos](/azure/backup/quick-backup-vm-cli#create-a-recovery-services-vault). | Compatible    <br><br> [Consulte los ejemplos](/azure/backup/backup-azure-arm-userestapi-createorupdatevault). | N/D | Compatible    <br><br> [Consulte los ejemplos](/azure/backup/backup-rm-template-samples). |
| Configuraciones de nivel de almacén | Administrar | Creación de un almacén de Backup | Compatible    <br><br> [Consulte los ejemplos](/azure/backup/backup-blobs-storage-account-ps#create-a-backup-vault). | Compatible    <br><br> [Consulte los ejemplos](/azure/backup/backup-blobs-storage-account-cli#create-a-backup-vault). | Compatible    <br><br> [Consulte los ejemplos](/azure/backup/backup-azure-dataprotection-use-rest-api-create-update-backup-vault). | N/D | Compatible |
| Configuraciones de nivel de almacén | Administrar | Mover el almacén de Recovery Services | Compatible    <br><br> [Consulte los ejemplos](/azure/backup/backup-azure-move-recovery-services-vault#use-powershell-to-move-recovery-services-vault). | Compatible    <br><br> [Consulte los ejemplos](/azure/backup/backup-azure-move-recovery-services-vault#use-powershell-to-move-recovery-services-vault). | Compatible | N/D | N/D |
| Configuraciones de nivel de almacén | Administrar | Mover el almacén de copia de seguridad | Compatible | Compatible | Compatible | N/D | N/D |
| Configuraciones de nivel de almacén | Administrar | Eliminación del almacén de Recovery Services | Compatible    <br><br> [Consulte los ejemplos](/azure/backup/backup-azure-delete-vault#delete-the-recovery-services-vault-by-using-powershell). | Compatible    <br><br> [Consulte los ejemplos](/azure/backup/backup-azure-delete-vault#delete-the-recovery-services-vault-by-using-cli). | Compatible    <br><br> [Consulte los ejemplos](/azure/backup/backup-azure-delete-vault#delete-the-recovery-services-vault-by-using-azure-resource-manager). | N/D | N/D |
| Configuraciones de nivel de almacén | Administrar | Eliminar el almacén de copia de seguridad | Compatible | Aquí | Aquí | N/D | N/D |
| Configuraciones de nivel de almacén | Administrar | Configuración de valores de diagnóstico | Compatible | Compatible | Compatible | Compatible    <br><br> [Consulte los ejemplos](/azure/backup/azure-policy-configure-diagnostics). | Compatible |
| Configuraciones de nivel de almacén | Administrar | Administración de las alertas de Azure Monitor (versión preliminar) | Compatible | Compatible | Compatible | N/D | N/D |
| Configuraciones de nivel de almacén | Administrar | Administración de las métricas de Azure Monitor (versión preliminar) | Compatible | Compatible | Compatible | N/D | N/D |
| Configuraciones de nivel de almacén | Seguridad | Habilitar los puntos de conexión privados para el almacén de Recovery Services | Compatible | Compatible | Compatible | Actualmente solo se admite la directiva de auditoría | Compatible |
| Configuraciones de nivel de almacén | Seguridad | Habilite las claves administradas por el cliente para el almacén de Recovery Services. | Compatible | Compatible | Compatible | Actualmente solo se admite la directiva de auditoría | Compatible |
| Configuraciones de nivel de almacén | Seguridad | Habilitar la eliminación automática en el almacén de Recovery Services | Compatible | Compatible | Compatible | Actualmente no se admite | Compatible |
| Configuraciones de nivel de almacén | Resistencia | Habilitar la restauración entre regiones para el almacén de Recovery Services | Compatible | Compatible | Compatible | Actualmente no se admite | Compatible |
