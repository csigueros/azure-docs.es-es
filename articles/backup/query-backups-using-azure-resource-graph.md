---
title: Consulta de las copias de seguridad con Azure Resource Graph (ARG)
description: Aprenda más sobre cómo consultar información sobre la copia de seguridad de los recursos de Azure mediante Azure Resource Graph (ARG).
ms.topic: conceptual
ms.date: 05/21/2021
ms.openlocfilehash: c464e95b9b6b45a49655b8f5f4659a262f9097ee
ms.sourcegitcommit: 80d311abffb2d9a457333bcca898dfae830ea1b4
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 05/26/2021
ms.locfileid: "110483444"
---
# <a name="query-your-backups-using-azure-resource-graph-arg"></a>Consulta de las copias de seguridad con Azure Resource Graph (ARG)

Puede consultar información sobre la copia de seguridad de los recursos de Azure sin costo adicional mediante [Azure Resource Graph (ARG)](../governance/resource-graph/overview.md). ARG es un servicio de Azure diseñado para ampliar la administración de los recursos de Azure. Permite explorar los recursos de forma eficaz y la posibilidad de realizar consultas a gran escala en un conjunto de suscripciones. Estas son las principales ventajas de usar ARG para consultar los metadatos de copia de seguridad:

- Capacidades para consultar recursos a gran escala con filtrado complejo, agrupación y ordenación por propiedades de recursos.
- Capacidad de obtener información en tiempo real sobre las copias de seguridad, incluidos los trabajos de copia de seguridad en curso.
- Capacidad de unir datos relacionados con la copia de seguridad con información útil sobre recursos de Azure relacionados, como Azure Virtual Machines y cuentas de almacenamiento.

## <a name="getting-started"></a>Introducción

Para empezar a consultar las copias de seguridad mediante ARG, siga estos pasos:

1. Busque _Resource Graph Explorer_ en Azure Portal. Seleccione lo mismo para que se le redirija al editor de consultas de ARG.
    
    ![Búsqueda del grupo de recursos de Azure](./media/query-backups-using-azure-resource-graph/search-resource-graph-explorer.png)

    En el panel izquierdo se muestran todas las tablas (y sus esquemas asociados) que están disponibles para la consulta.
    
    - La tabla **RecoveryServicesResources** contiene la mayoría de los registros relacionados con la copia de seguridad, como los detalles del trabajo y los detalles de la instancia de copia de seguridad.  etcétera.
    - La tabla **Recursos** contiene información sobre todos los recursos de Azure de nivel superior, como almacenes de Recovery Services, Azure Virtual Machines, cuentas de almacenamiento, etc.

    ![Tablas disponibles y esquemas asociados para consulta](./media/query-backups-using-azure-resource-graph/tables-and-associated-schemas.png)

1. Para explorar los datos de cualquiera de estas tablas, escriba **consultas de Kusto** en el editor de consultas y haga clic en **Ejecutar consulta**.

    Puede descargar la salida de estas consultas como CSV desde **Resource Graph Explorer**. También puede usar estas consultas en la automatización personalizada mediante cualquier cliente de automatización compatible con ARG, como [PowerShell](../governance/resource-graph/first-query-powershell.md), la [CLI](../governance/resource-graph/first-query-azurecli.md) o el [SDK](../governance/resource-graph/first-query-python.md). También puede crear [libros personalizados](../azure-monitor/visualize/workbooks-overview.md) en Azure Portal mediante ARG como origen de datos.

>[!NOTE] 
>- Se pueden consultar trabajos de copia de seguridad y restauración con una antigüedad de hasta 14 en ARG. Si quiere consultar registros históricos, se recomienda usar los **registros de Azure Monitor**.
>- ARG permite consultar los recursos para los que tiene los derechos de RBAC adecuados.

## <a name="sample-queries"></a>Consultas de ejemplo

Las siguientes son algunas consultas ARG de ejemplo en los datos de copia de seguridad que puede usar en paneles y automatizaciones personalizados.

### <a name="list-all-azure-vms-that-have-been-configured-for-backup"></a>Enumeración de todas las máquinas virtuales de Azure que se han configurado para la copia de seguridad

```dotnetcli
RecoveryServicesResources 
| where type in~ ('Microsoft.RecoveryServices/vaults/backupFabrics/protectionContainers/protectedItems')
| extend vaultName = case(type =~ 'microsoft.dataprotection/backupVaults/backupInstances',split(split(id, '/Microsoft.DataProtection/backupVaults/')[1],'/')[0],type =~ 'Microsoft.RecoveryServices/vaults/backupFabrics/protectionContainers/protectedItems',split(split(id, '/Microsoft.RecoveryServices/vaults/')[1],'/')[0],'--')
| extend dataSourceType = case(type=~'Microsoft.RecoveryServices/vaults/backupFabrics/protectionContainers/protectedItems',properties.backupManagementType,type =~ 'microsoft.dataprotection/backupVaults/backupInstances',properties.dataSourceSetInfo.datasourceType,'--')
| extend friendlyName = properties.friendlyName
| extend dsResourceGroup = split(split(properties.dataSourceInfo.resourceID, '/resourceGroups/')[1],'/')[0]
| extend dsSubscription = split(split(properties.dataSourceInfo.resourceID, '/subscriptions/')[1],'/')[0]
| extend lastRestorePoint = properties.lastRecoveryPoint
| extend primaryLocation = properties.dataSourceInfo.resourceLocation
| extend policyName = case(type =~ 'Microsoft.RecoveryServices/vaults/backupFabrics/protectionContainers/protectedItems',properties.policyName, type =~ 'microsoft.dataprotection/backupVaults/backupInstances', properties.policyInfo.name, '--')
| extend protectionState = properties.currentProtectionState
| where protectionState in~ ('ConfiguringProtection','ProtectionConfigured','ConfiguringProtectionFailed','ProtectionStopped','SoftDeleted','ProtectionError')
| where (dsSubscription in~ ('00000000-0000-0000-0000-000000000000')) and (dataSourceType in~ ('AzureIaasVM')) //add the relevant subscription ids you wish to query to this line

```

### <a name="list-all-backup-jobs-on-azure-databases-for-postgresql-servers-in-the-last-one-week"></a>Enumeración de todos los trabajos de copia de seguridad en servidores de Azure Database for PostgreSQL en la última semana

```dotnetcli
RecoveryServicesResources 
| where type in~ ('Microsoft.DataProtection/backupVaults/backupJobs')
| extend vaultName = case(type =~ 'microsoft.dataprotection/backupVaults/backupJobs',properties.vaultName,type =~ 'Microsoft.RecoveryServices/vaults/backupJobs',split(split(id, '/Microsoft.RecoveryServices/vaults/')[1],'/')[0],'--')
| extend friendlyName = case(type =~ 'microsoft.dataprotection/backupVaults/backupJobs',strcat(properties.dataSourceSetName , '/', properties.dataSourceName),type =~ 'Microsoft.RecoveryServices/vaults/backupJobs', properties.entityFriendlyName, '--')
| extend dataSourceType = case(type =~ 'Microsoft.RecoveryServices/vaults/backupJobs',properties.backupManagementType,type =~ 'microsoft.dataprotection/backupVaults/backupJobs',properties.dataSourceType,'--')
| extend backupInstanceName = properties.backupInstanceId
| extend dsResourceGroup = split(split(properties.dataSourceId, '/resourceGroups/')[1],'/')[0]| extend dsSubscription = split(split(properties.dataSourceId, '/subscriptions/')[1],'/')[0]
| extend status = properties.status
| extend dataSourceId = properties.dataSourceId
| extend primaryLocation = properties.dataSourceLocation
| extend jobStatus = case (properties.status == 'Completed' or properties.status == 'CompletedWithWarnings','Succeeded',properties.status == 'Failed','Failed',properties.status == 'InProgress', 'Started', properties.status), operation = case(type =~ 'microsoft.dataprotection/backupVaults/backupJobs' and tolower(properties.operationCategory) =~ 'backup' and properties.isUserTriggered == 'true',strcat('adhoc',properties.operationCategory),type =~ 'microsoft.dataprotection/backupVaults/backupJobs', tolower(properties.operationCategory), type =~ 'Microsoft.RecoveryServices/vaults/backupJobs' and tolower(properties.operation) =~ 'backup' and properties.isUserTriggered == 'true',strcat('adhoc',properties.operation),type =~ 'Microsoft.RecoveryServices/vaults/backupJobs',tolower(properties.operation), '--'),startTime = todatetime(properties.startTime),endTime = properties.endTime, duration = properties.duration
| project id, name, friendlyName, resourceGroup, vaultName, dataSourceType, operation, jobStatus, startTime, duration, backupInstanceName, dsResourceGroup, dsSubscription, status, primaryLocation, dataSourceId
| where  (dsSubscription in~ ('00000000-0000-0000-0000-000000000000')) and (dataSourceType in~ ('Microsoft.DBforPostgreSQL/servers/databases')) and (status in~ ('Started','InProgress','Succeeded','Completed','Failed','CompletedWithWarnings')) and (operation in~ ('adhocBackup','backup')) //add the relevant subscription ids you wish to query to this line
| where (startTime >= ago(7d))

```

### <a name="list-all-azure-vms-that-have-not-been-configured-for-backup"></a>Enumeración de todas las máquinas virtuales de Azure que no se han configurado para la copia de seguridad

```dotnetcli
Resources
| where type in~ ('microsoft.compute/virtualmachines','microsoft.classiccompute/virtualmachines') 
| extend resourceId=tolower(id) 
| join kind = leftouter ( RecoveryServicesResources
| where type == "microsoft.recoveryservices/vaults/backupfabrics/protectioncontainers/protecteditems"
| where properties.backupManagementType == "AzureIaasVM"
| project resourceId = tolower(tostring(properties.sourceResourceId)), backupItemid = id, isBackedUp = isnotempty(id) ) on resourceId 
| extend isProtected = isnotempty(backupItemid)
| where (isProtected == (0))
| project id,name,resourceGroup,location,tags

```

### <a name="list-all-backup-policies-used-for-azure-databases-for-postgresql-servers"></a>Enumeración de todas las directivas de copia de seguridad usadas para servidores de Azure Database for PostgreSQL

```dotnetcli
RecoveryServicesResources 
| where type in~ ('Microsoft.DataProtection/BackupVaults/backupPolicies')
| extend vaultName = case(type =~ 'microsoft.dataprotection/backupVaults/backupPolicies', split(split(id, '/Microsoft.DataProtection/backupVaults/')[1],'/')[0],type =~ 'microsoft.recoveryservices/vaults/backupPolicies', split(split(id, '/Microsoft.RecoveryServices/vaults/')[1],'/')[0],'--')
| extend datasourceType = case(type =~ 'Microsoft.RecoveryServices/vaults/backupPolicies', properties.backupManagementType,type =~ 'microsoft.dataprotection/backupVaults/backupPolicies',properties.datasourceTypes[0],'--')
| project id,name,vaultName,resourceGroup,properties,datasourceType
| where (datasourceType in~ ('Microsoft.DBforPostgreSQL/servers/databases'))

```

## <a name="next-steps"></a>Pasos siguientes

[Más información sobre Azure Resource Graph](../governance/resource-graph/overview.md).
