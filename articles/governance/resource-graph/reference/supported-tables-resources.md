---
title: Tipos de recurso de Azure Resource Manager admitidos
description: Proporcione una lista de los tipos de recurso de Azure Resource Manager admitidos en Azure Resource Graph y el Historial de cambios.
ms.date: 08/13/2021
ms.topic: reference
ms.custom: generated
ms.openlocfilehash: 69b8e4bb3400b04da3986f9e40a1c5b5fba3b750
ms.sourcegitcommit: e7d500f8cef40ab3409736acd0893cad02e24fc0
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/13/2021
ms.locfileid: "122067600"
---
# <a name="azure-resource-graph-table-and-resource-type-reference"></a>Referencia de tipo de recurso y tabla de Azure Resource Graph

Azure Resource Graph admite estos **tipos de recurso** de [Azure Resource Manager](../../../azure-resource-manager/management/overview.md). Cada **tipo de recurso** es parte de una **tabla** de Resource Graph.

## <a name="advisorresources"></a>advisorresources

En [Consultas de ejemplo de Resource Graph para advisorresources](../samples/samples-by-table.md#advisorresources) encontrará consultas de ejemplo para esta tabla.

- microsoft.advisor/configurations
- microsoft.advisor/recommendations
  - Consulta de ejemplo: [obtención de un resumen de ahorro de costes de Azure Advisor](../samples/samples-by-category.md#get-cost-savings-summary-from-azure-advisor)
- microsoft.advisor/recommendations/suppressions
- microsoft.advisor/suppressions

## <a name="alertsmanagementresources"></a>alertsmanagementresources

- microsoft.alertsmanagement/alerts

## <a name="extendedlocationresources"></a>extendedlocationresources

En [Consultas de ejemplo de Resource Graph para extendedlocationresources](../samples/samples-by-table.md#extendedlocationresources) encontrará consultas de ejemplo para esta tabla.

- microsoft.extendedlocation/customlocations/enabledresourcetypes
  - Consulta de ejemplo: [obtención de los tipos de recurso habilitados para ubicaciones personalizadas compatibles con Azure Arc](../samples/samples-by-category.md#get-enabled-resource-types-for-azure-arc-enabled-custom-locations)
  - Consulta de ejemplo: [enumeración de las ubicaciones personalizadas compatibles con Azure Arc con VMware o SCVMM habilitados](../samples/samples-by-category.md#list-azure-arc-enabled-custom-locations-with-vmware-or-scvmm-enabled)

## <a name="guestconfigurationresources"></a>guestconfigurationresources

En [Consultas de ejemplo de Resource Graph para guestconfigurationresources](../samples/samples-by-table.md#guestconfigurationresources) encontrará consultas de ejemplo para esta tabla.

- microsoft.guestconfiguration/guestconfigurationassignments
  - Consulta de ejemplo: [recuento de máquinas en el ámbito de las directivas de configuración de invitado](../samples/samples-by-category.md#count-machines-in-scope-of-guest-configuration-policies)
  - Consulta de ejemplo: [recuento de asignaciones de configuración de invitado no conformes](../samples/samples-by-category.md#count-of-non-compliant-guest-configuration-assignments)
  - Consulta de ejemplo: [búsqueda de todas las razones por las que una máquina no es conforme con asignaciones de configuración de invitado](../samples/samples-by-category.md#find-all-reasons-a-machine-is-non-compliant-for-guest-configuration-assignments)
  - Consulta de ejemplo: [consulta de detalles de informes de asignaciones de configuración de invitado](../samples/samples-by-category.md#query-details-of-guest-configuration-assignment-reports)

## <a name="healthresources"></a>healthresources

En [Consultas de ejemplo de Resource Graph para healthresources](../samples/samples-by-table.md#healthresources) encontrará consultas de ejemplo para esta tabla.

- microsoft.resourcehealth/availabilitystatuses
  - Consulta de ejemplo: [recuento de máquinas virtuales por estado de disponibilidad e Id. de suscripción](../samples/samples-by-category.md#count-of-virtual-machines-by-availability-state-and-subscription-id)
  - Consulta de ejemplo: [enumeración de máquinas virtuales y estados de disponibilidad asociados por Id. de recurso](../samples/samples-by-category.md#list-of-virtual-machines-and-associated-availability-states-by-resource-ids)
  - Consulta de ejemplo: [enumeración de máquinas virtuales por estado de disponibilidad y estado de energía con Ids. de recurso y grupos de recursos](../samples/samples-by-category.md#list-of-virtual-machines-by-availability-state-and-power-state-with-resource-ids-and-resource-groups)
  - Consulta de ejemplo: [enumeración de máquinas virtuales que no están disponibles por Id. de recurso](../samples/samples-by-category.md#list-of-virtual-machines-that-are-not-available-by-resource-ids)

## <a name="kubernetesconfigurationresources"></a>kubernetesconfigurationresources

En [Consultas de ejemplo de Resource Graph para kubernetesconfigurationresources](../samples/samples-by-table.md#kubernetesconfigurationresources) encontrará consultas de ejemplo para esta tabla.

- microsoft.kubernetesconfiguration/extensions
  - Consulta de ejemplo: [enumeración de todos los clústeres de Kubernetes compatibles con Azure Arc con la extensión de Azure Monitor](../samples/samples-by-category.md#list-all-azure-arc-enabled-kubernetes-clusters-with-azure-monitor-extension)
  - Consulta de ejemplo: [enumeración de todos los clústeres de Kubernetes compatibles con Azure Arc sin la extensión de Azure Monitor](../samples/samples-by-category.md#list-all-azure-arc-enabled-kubernetes-clusters-without-azure-monitor-extension)
- microsoft.kubernetesconfiguration/sourcecontrolconfigurations

## <a name="maintenanceresources"></a>maintenanceresources

- microsoft.maintenance/applyupdates
- microsoft.maintenance/configurationassignments
- microsoft.maintenance/updates

## <a name="patchassessmentresources"></a>patchassessmentresources

En [Consultas de ejemplo de Resource Graph para patchassessmentresources](../samples/samples-by-table.md#patchassessmentresources) encontrará consultas de ejemplo para esta tabla.

- microsoft.compute/virtualmachines/patchassessmentresults
- microsoft.compute/virtualmachines/patchassessmentresults/softwarepatches
- microsoft.hybridcompute/machines/patchassessmentresults
- microsoft.hybridcompute/machines/patchassessmentresults/softwarepatches

## <a name="patchinstallationresources"></a>patchinstallationresources

- microsoft.compute/virtualmachines/patchinstallationresults
- microsoft.compute/virtualmachines/patchinstallationresults/softwarepatches
- microsoft.hybridcompute/machines/patchinstallationresults
- microsoft.hybridcompute/machines/patchinstallationresults/softwarepatches

## <a name="policyresources"></a>policyresources

En [Consultas de ejemplo de Resource Graph para policyresources](../samples/samples-by-table.md#policyresources) encontrará consultas de ejemplo para esta tabla.

- microsoft.policyinsights/policystates
  - Consulta de ejemplo: [cumplimiento por asignación de directiva](../samples/samples-by-category.md#compliance-by-policy-assignment)
  - Consulta de ejemplo: [cumplimiento por tipo de recurso](../samples/samples-by-category.md#compliance-by-resource-type)
  - Consulta de ejemplo: [enumeración de todos los recursos no conformes](../samples/samples-by-category.md#list-all-non-compliant-resources)
  - Consulta de ejemplo: [resumen del cumplimiento de recursos por estado](../samples/samples-by-category.md#summarize-resource-compliance-by-state)
  - Consulta de ejemplo: [resumen del cumplimiento de recursos por estado y ubicación](../samples/samples-by-category.md#summarize-resource-compliance-by-state-per-location)

## <a name="recoveryservicesresources"></a>recoveryservicesresources

- microsoft.dataprotection/backupvaults/backupinstances
- microsoft.dataprotection/backupvaults/backupjobs
- microsoft.dataprotection/backupvaults/backuppolicies
- microsoft.recoveryservices/vaults/alerts
- Microsoft.RecoveryServices/vaults/backupFabrics/protectionContainers/protectedItems (elementos de copia de seguridad)
- microsoft.recoveryservices/vaults/backupjobs
- microsoft.recoveryservices/vaults/backuppolicies

## <a name="resourcecontainers"></a>resourcecontainers

En [Consultas de ejemplo de Resource Graph para resourcecontainers](../samples/samples-by-table.md#resourcecontainers) encontrará consultas de ejemplo para esta tabla.

- microsoft.management/managementgroups
  - Consulta de ejemplo: [recuento de suscripciones por grupo de administración](../samples/samples-by-category.md#count-of-subscriptions-per-management-group)
  - Consulta de ejemplo: [enumeración de todos los antecesores de grupo de administración para un grupo de administración especificado](../samples/samples-by-category.md#list-all-management-group-ancestors-for-a-specified-management-group)
- microsoft.resources/subscriptions (suscripciones)
  - Consulta de ejemplo: [recuento de suscripciones por grupo de administración](../samples/samples-by-category.md#count-of-subscriptions-per-management-group)
  - Consulta de ejemplo: [almacenes de claves con el nombre de la suscripción](../samples/samples-by-category.md#key-vaults-with-subscription-name)
  - Consulta de ejemplo: [enumeración de todos los antecesores de grupo de administración para una suscripción especificada](../samples/samples-by-category.md#list-all-management-group-ancestors-for-a-specified-subscription)
  - Consulta de ejemplo: [enumeración de todas las suscripciones en un grupo de administración especificado](../samples/samples-by-category.md#list-all-subscriptions-under-a-specified-management-group)
  - Consulta de ejemplo: [eliminación de columnas de resultados](../samples/samples-by-category.md#remove-columns-from-results)
  - Consulta de ejemplo: [puntuación segura por grupo de administración](../samples/samples-by-category.md#secure-score-per-management-group)
- Microsoft.Resources/subscriptions/resourceGroups (grupos de recursos)
  - Consulta de ejemplo: [búsqueda de cuentas de almacenamiento con una etiqueta concreta que no distingue mayúsculas de minúsculas en el grupo de recursos](../samples/samples-by-category.md#find-storage-accounts-with-a-specific-case-insensitive-tag-on-the-resource-group)
  - Consulta de ejemplo: [búsqueda de cuentas de almacenamiento con una etiqueta concreta que distingue mayúsculas de minúsculas en el grupo de recursos](../samples/samples-by-category.md#find-storage-accounts-with-a-specific-case-sensitive-tag-on-the-resource-group)

## <a name="resources"></a>resources

En [Consultas de ejemplo de Resource Graph para resources](../samples/samples-by-table.md#resources) encontrará consultas de ejemplo para esta tabla.

- 84codes.CloudAMQP/servers (CloudAMQP)
- Citrix.Services/XenAppEssentials (Citrix Virtual Apps Essentials)
- Citrix.Services/XenDesktopEssentials (Citrix Virtual Desktops Essentials)
- conexlink.mycloudit/accounts
- crypteron.datasecurity/apps
- GitHub.Enterprise/accounts (GitHub AE)
- gridpro.evops/accounts
- gridpro.evops/accounts/eventrules
- gridpro.evops/accounts/requesttemplates
- gridpro.evops/accounts/views
- hive.streaming/services
- incapsula.waf/accounts
- LiveArena.Broadcast/services (LiveArena Broadcast)
- Mailjet.Email/services (Mailjet Email Service)
- Microsoft. AAD/domainServices (Azure AD Domain Services)
- microsoft.aadiam/azureadmetrics
- microsoft.aadiam/privateLinkForAzureAD (Private Link para Azure AD)
- microsoft.aadiam/tenants
- Microsoft.AgFoodPlatform/farmBeats (Azure FarmBeats)
- microsoft.aisupercomputer/accounts
- microsoft.aisupercomputer/accounts/jobgroups
- microsoft.aisupercomputer/accounts/jobgroups/jobs
- microsoft.alertsmanagement/actionrules
- microsoft.alertsmanagement/resourcehealthalertrules
- microsoft.alertsmanagement/smartdetectoralertrules
- Microsoft.AnalysisServices/servers (Analysis Services)
- Microsoft.AnyBuild/clusters (clústeres de AnyBuild)
- Microsoft.ApiManagement/service (Servicios de API Management)
- microsoft.appassessment/migrateprojects
- Microsoft.AppConfiguration/configurationStores (App Configuration)
- Microsoft.AppPlatform/Spring (Azure Spring Cloud)
- microsoft.archive/collections
- Microsoft.Attestation/attestationProviders (proveedores de atestación)
- Microsoft.Authorization/resourceManagementPrivateLinks (vínculos privados de administración de recursos)
- microsoft.automanage/accounts
- microsoft.automanage/configurationprofilepreferences
- microsoft.automanage/configurationprofiles
- Microsoft.Automation/AutomationAccounts (cuentas de Automation)
- microsoft.automation/automationaccounts/configurations
- Microsoft.Automation/automationAccounts/runbooks (runbook)
- microsoft.autonomousdevelopmentplatform/accounts
- Microsoft.AutonomousSystems/workspaces (Bonsai)
- Microsoft.AVS/privateClouds (nubes privadas de AVS)
- microsoft.azconfig/configurationstores
- Microsoft.AzureActiveDirectory/b2cDirectories (inquilinos B2C)
- Microsoft.AzureActiveDirectory/guestUsages (usos de invitado)
- Microsoft.AzureArcData/dataControllers (controladores de datos de Azure Arc)
- Microsoft.Azure LdapData/postgresInstances (grupos de servidores Hiperescala de PostgreSQL habilitada para Azure Arc)
- Microsoft.AzureArcData/sqlManagedInstances (instancias de SQL Managed Instance - Azure Arc)
- Microsoft.AzureArcData/sqlServerInstances (SQL Server - Azure Arc)
- microsoft.azurecis/autopilotenvironments
- microsoft.azuredata/datacontrollers
- microsoft.azuredata/hybriddatamanagers
- microsoft.azuredata/postgresinstances
- microsoft.azuredata/sqlbigdataclusters
- microsoft.azuredata/sqlinstances
- microsoft.azuredata/sqlmanagedinstances
- microsoft.azuredata/sqlserverinstances
- Microsoft.AzureData/sqlServerRegistrations (registros de SQL Server)
- Microsoft.AzurePercept/accounts (cuentas de Azure Percept)
- microsoft.azuresphere/catalogs
- microsoft.azuresphere/catalogs/products
- microsoft.azuresphere/catalogs/products/devicegroups
- microsoft.azurestack/edgesubscriptions
- microsoft.azurestack/linkedsubscriptions
- Microsoft.Azurestack/registrations (instancias de Azure Stack Hub)
- <<<<Microsoft.AzureStackHCI/clusters (Azure Stack HCI)
- microsoft.azurestackhci/galleryimages
- microsoft.azurestackhci/networkinterfaces
- microsoft.azurestackhci/virtualharddisks
- Microsoft.AzureStackHci/virtualMachines (máquina virtual de Azure Stack HCI: Azure Arc)
- microsoft.azurestackhci/virtualnetworks
- microsoft.backupsolutions/vmwareapplications
- microsoft.baremetal/consoleconnections
- Microsoft.BareMetal/crayServers (servidores de Cray)
- Microsoft.BareMetal/monitoringServers (servidores de supervisión)
- Microsoft.BareMetalInfrastructure/bareMetalInstances (instancias de BareMetal)
- Microsoft.Batch/batchAccounts (cuentas de Batch)
- microsoft.batchai/clusters
- microsoft.batchai/fileservers
- microsoft.batchai/jobs
- microsoft.batchai/workspaces
- Microsoft.Bing/accounts (recursos de Bing)
- microsoft.bingmaps/mapapis
- microsoft.biztalkservices/biztalk
- Microsoft.Blockchain/blockchainMembers (Azure Blockchain Service)
- Microsoft.Blockchain/cordaMembers (Corda)
- Microsoft.Blockchain/watchers (Blockchain Data Manager)
- Microsoft.BotService/botServices (instancias de Bot Service)
- Microsoft.Cache/Redis (Azure Cache for Redis)
- Microsoft.Cache/RedisEnterprise (Redis Enterprise)
- microsoft.cascade/sites
- Microsoft.Cdn/CdnWebApplicationFirewallPolicies (directivas de firewall de aplicaciones web [WAF])
- microsoft.cdn/profiles (instancias de Front Door Standard/Premium [versión preliminar])
- Microsoft.Cdn/Profiles/AfdEndpoints (puntos de conexión)
- microsoft.cdn/profiles/endpoints (puntos de conexión)
- Microsoft.CertificateRegistration/certificateOrders (instancias de App Service Certificate)
- microsoft.chaos/chaosexperiments (experimentos de Chaos)
- microsoft.chaos/experiments
- microsoft.classicCompute/domainNames (servicios en la nube (clásicos))
- Microsoft.ClassicCompute/VirtualMachines (máquinas virtuales (clásicas))
- Microsoft.ClassicNetwork/networkSecurityGroups (grupos de seguridad de red (clásicos))
- Microsoft.ClassicNetwork/reservedIps (direcciones IP reservadas (clásicas))
- Microsoft.ClassicNetwork/virtualNetworks (redes virtuales (clásicas))
- Microsoft.ClassicStorage/StorageAccounts (cuentas de almacenamiento (clásicas))
- microsoft.cloudes/accounts
- microsoft.cloudsearch/indexes
- Microsoft.CloudTest/accounts (cuentas de CloudTest)
- Microsoft.CloudTest/hostedpools (grupos hospedados de 1ES)
- Microsoft.CloudTest/images (imágenes de CloudTest)
- Microsoft.CloudTest/pools (grupos de CloudTest)
- Microsoft.ClusterStor/nodes (ClusterStors)
- microsoft.codespaces/plans
- Microsoft.Cognition/syntheticsAccounts (cuentas de Synthetics)
- Microsoft.CognitiveServices/accounts (Cognitive Services)
- Microsoft.Compute/availabilitySets (conjuntos de disponibilidad)
- Microsoft.Compute/capacityReservationGroups (grupos de reserva de capacidad)
- microsoft.compute/capacityreservationgroups/capacityreservations
- microsoft.compute/capacityreservations
- Microsoft.Compute/cloudServices (servicios en la nube (soporte extendido))
- Microsoft.Compute/diskAccesses (accesos al disco)
- Microsoft.Compute/diskEncryptionSets (conjuntos de cifrado de disco)
- Microsoft.Compute/disks (discos)
- Microsoft.Compute/galleries (galerías de imágenes compartidas)
- Microsoft.Compute/galleries/applications (aplicaciones de la galería)
- Microsoft.Compute/galleries/applications/versions (versiones de aplicaciones de la galería)
- Microsoft.Compute/galleries/images (definiciones de imágenes)
- Microsoft.Compute/galleries/images/versions (versiones de imágenes)
- Microsoft.Compute/hostgroups (grupos host)
- Microsoft.Compute/hostgroups/hosts (hosts)
- Microsoft.Compute/images (imágenes)
- Microsoft.Compute/ProximityPlacementGroups (grupos con ubicación por proximidad)
- Microsoft.Compute/restorePointCollections (colecciones de puntos de restauración)
- microsoft.compute/sharedvmextensions
- microsoft.compute/sharedvmextensions/versions
- microsoft.compute/sharedvmimages
- microsoft.compute/sharedvmimages/versions
- Microsoft.Compute/snapshots (instantáneas)
- Microsoft.Compute/sshPublicKeys (claves SSH)
- microsoft.compute/swiftlets
- Microsoft.Compute/VirtualMachines (máquinas virtuales)
  - Consulta de ejemplo: [recuento de máquinas virtuales por estado de energía](../samples/samples-by-category.md#count-of-virtual-machines-by-power-state)
  - Consulta de ejemplo: [recuento de máquinas virtuales por tipo de sistema operativo](../samples/samples-by-category.md#count-virtual-machines-by-os-type)
  - Consulta de ejemplo: [recuento de máquinas virtuales por tipo de sistema operativo con extensión](../samples/samples-by-category.md#count-virtual-machines-by-os-type-with-extend)
  - Consulta de ejemplo: [enumeración de todas las extensiones instaladas en una máquina virtual](../samples/samples-by-category.md#list-all-extensions-installed-on-a-virtual-machine)
  - Consulta de ejemplo: [enumeración de máquinas virtuales por estado de disponibilidad y estado de energía con Ids. de recurso y grupos de recursos](../samples/samples-by-category.md#list-of-virtual-machines-by-availability-state-and-power-state-with-resource-ids-and-resource-groups)
  - Consulta de ejemplo: [enumeración de máquinas virtuales con su interfaz de red y dirección IP pública](../samples/samples-by-category.md#list-virtual-machines-with-their-network-interface-and-public-ip)
  - Consulta de ejemplo: [visualización de todas las máquinas por nombre en orden descendente](../samples/samples-by-category.md#show-all-virtual-machines-ordered-by-name-in-descending-order)
  - Consulta de ejemplo: [visualización de las cinco primeras máquinas virtuales por nombre y tipo de sistema operativo](../samples/samples-by-category.md#show-first-five-virtual-machines-by-name-and-their-os-type)
  - Consulta de ejemplo: [resumen de la máquina virtual mediante la propiedad extendida de estados de energía](../samples/samples-by-category.md#summarize-virtual-machine-by-the-power-states-extended-property)
  - Consulta de ejemplo: [máquinas virtuales que coinciden con expresión regular](../samples/samples-by-category.md#virtual-machines-matched-by-regex)
- microsoft.compute/virtualmachines/extensions
  - Consulta de ejemplo: [enumeración de todas las extensiones instaladas en una máquina virtual](../samples/samples-by-category.md#list-all-extensions-installed-on-a-virtual-machine)
- microsoft.compute/virtualmachines/runcommands
- Microsoft.Compute/virtualMachineScaleSets (conjuntos de escalado de máquinas virtuales)
  - Consulta de ejemplo: [obtención de la capacidad y el tamaño del conjunto de escalado de máquinas virtuales](../samples/samples-by-category.md#get-virtual-machine-scale-set-capacity-and-size)
- Microsoft.ConfidentialLedger/ledgers (libros de contabilidad confidenciales)
- Microsoft.Confluent/organizations (organizaciones de Confluent)
- Microsoft.ConnectedCache/cacheNodes (recursos de caché conectada)
- Microsoft.ConnectedVehicle/platformAccounts (plataformas de vehículos conectados)
- microsoft.connectedvmwarevsphere/resourcepools
- Microsoft.connectedVMwareVSphere/vCenters (instancias de VMware vCenter)
- Microsoft.ConnectedVMwarevSphere/VirtualMachines (máquinas virtuales de VMware y AVS)
- microsoft.connectedvmwarevsphere/virtualmachines/extensions
- microsoft.connectedvmwarevsphere/virtualmachinetemplates
- microsoft.connectedvmwarevsphere/virtualnetworks
- Microsoft.ContainerInstance/containerGroups (instancias de contenedor)
- Microsoft.ContainerRegistry/registries (registros de contenedor)
- microsoft.containerregistry/registries/agentpools
- microsoft.containerregistry/registries/buildtasks
- Microsoft.ContainerRegistry/registries/replications (replicaciones de registro de contenedor)
- microsoft.containerregistry/registries/taskruns
- microsoft.containerregistry/registries/tasks
- Microsoft.ContainerRegistry/registries/webhooks (webhooks del registro de contenedor)
- microsoft.containerservice/containerservices
- Microsoft.ContainerService/managedClusters (servicios de Kubernetes)
- microsoft.containerservice/openshiftmanagedclusters
- microsoft.contoso/clusters
- microsoft.contoso/employees
- microsoft.contoso/installations
- microsoft.contoso/towers
- microsoft.costmanagement/connectors
- microsoft.customproviders/resourceproviders
- microsoft.d365customerinsights/instances
- Microsoft.Dashboard/grafana (áreas de trabajo de Grafana)
- Microsoft.DataBox/jobs (Azure Data Box)
- Microsoft.DataBoxEdge/dataBoxEdgeDevices (Azure Stack Edge/Data Box Gateway)
- Microsoft.Databricks/workspaces (servicios de Azure Databricks)
- Microsoft.DataCatalog/catalogs (Data Catalog)
- microsoft.datacatalog/datacatalogs
- Microsoft.DataCollaboration/workspaces (CI de proyecto)
- Microsoft.Datadog/monitors (Datadog)
- Microsoft.DataFactory/dataFactories (factorías de datos)
- Microsoft.DataFactory/factories (factorías de datos [V2])
- Microsoft.DataLakeAnalytics/accounts (Data Lake Analytics)
- Microsoft.DataLakeStore/accounts (Data Lake Storage Gen1)
- microsoft.datamigration/controllers
- Microsoft.DataMigration/services (instancias de Azure Database Migration Service)
- Microsoft.DataMigration/services/projects (proyectos de Azure Database Migration)
- microsoft.datamigration/slots
- microsoft.datamigration/sqlmigrationservices
- Microsoft.DataProtection/BackupVaults (almacenes de copia de seguridad)
- Microsoft.DataProtection/resourceGuards (restricciones de recursos, versión preliminar)
- microsoft.dataprotection/resourceoperationgatekeepers
- Microsoft.DataShare/accounts (instancias de Data Share)
- Microsoft.DBforMariaDB/servers (servidores de Azure Database for MariaDB)
- Microsoft.DBforMySQL/flexibleServers (servidores flexibles de Azure Database for MySQL)
- Microsoft.DBforMySQL/servers (servidores de Azure Database for MySQL)
- Microsoft.DBforPostgreSQL/flexibleServers (servidores flexibles de Azure Database for PostgreSQL)
- Microsoft.DBforPostgreSQL/serverGroups (grupos de servidores de Azure Database for PostgreSQL)
- Microsoft.DBforPostgreSQL/serverGroupsv2 (grupos de servidores de Azure Database for PostgreSQL)
- Microsoft.DBforPostgreSQL/servers (servidores de Azure Database for PostgreSQL)
- Microsoft.DBforPostgreSQL/serversv2 (servidores de Azure Database for PostgreSQL v2)
- microsoft.dbforpostgresql/singleservers
- microsoft.delegatednetwork/controller
- microsoft.delegatednetwork/delegatedsubnets
- microsoft.delegatednetwork/orchestratorinstances
- microsoft.delegatednetwork/orchestrators
- microsoft.deploymentmanager/artifactsources
- Microsoft.DeploymentManager/Rollouts (lanzamientos)
- microsoft.deploymentmanager/servicetopologies
- microsoft.deploymentmanager/servicetopologies/services
- microsoft.deploymentmanager/servicetopologies/services/serviceunits
- microsoft.deploymentmanager/steps
- Microsoft.DesktopVirtualization/ApplicationGroups (grupos de aplicaciones)
- Microsoft.DesktopVirtualization/HostPools (grupos de hosts)
- Microsoft.DesktopVirtualization/ScalingPlans (planes de escalado)
- Microsoft.DesktopVirtualization/Workspaces (áreas de trabajo)
- microsoft.devices/elasticpools
- microsoft.devices/elasticpools/iothubtenants
- Microsoft.Devices/IotHubs (IoT Hub)
- Microsoft.Devices/ProvisioningServices (instancias de Device Provisioning Service)
- Microsoft.DeviceUpdate/Accounts (actualización de dispositivo para instancias de IoT Hub)
- microsoft.deviceupdate/accounts/instances
- microsoft.devops/pipelines (DevOps Starter)
- microsoft.devspaces/controllers
- microsoft.devtestlab/labcenters
- Microsoft.DevTestLab/labs (DevTest Labs)
- microsoft.devtestlab/labs/servicerunners
- Microsoft.DevTestLab/labs/virtualMachines (máquinas virtuales)
- microsoft.devtestlab/schedules
- Microsoft.DigitalTwins/digitalTwinsInstances (Azure Digital Twins)
- Microsoft.DocumentDB/cassandraClusters (Azure Managed Instance for Apache Cassandra)
- Microsoft.DocumentDb/databaseAccounts (cuentas de Azure Cosmos DB)
  - Consulta de ejemplo: [enumeración de recursos de Azure Cosmos DB con ubicaciones de escritura concretas](../samples/samples-by-category.md#list-azure-cosmos-db-with-specific-write-locations)
- Microsoft.DomainRegistration/domains (dominios de App Service)
- microsoft.dynamics365fraudprotection/instances
- Microsoft.EdgeOrder/addresses (dirección de Azure Edge Hardware Center)
- microsoft.edgeorder/ordercollections
- Microsoft.EdgeOrder/orderItems (Azure Edge Hardware Center)
- microsoft.edgeorder/orders
- Microsoft.Elastic/monitors (Elasticsearch [Elastic Cloud])
- microsoft.enterpriseknowledgegraph/services
- Microsoft.EventGrid/domains (dominios de Event Grid)
- Microsoft.EventGrid/partnerNamespaces (espacios de nombres de partners de Event Grid)
- Microsoft.EventGrid/partnerRegistrations (registros de partner de Event Grid)
- Microsoft.EventGrid/partnerTopics (temas de partner de Event Grid)
- Microsoft.EventGrid/systemTopics (temas del sistema de Event Grid)
- Microsoft.EventGrid/topics (temas de Event Grid)
- Microsoft.EventHub/clusters (clústeres de Event Hubs)
- Microsoft.EventHub/namespaces (espacios de nombres de Event Hubs)
- Microsoft.Experimentation/experimentWorkspaces (áreas de trabajo de experimento)
- Microsoft.ExtendedLocation/CustomLocations (ubicaciones personalizadas)
  - Consulta de ejemplo: [enumeración de las ubicaciones personalizadas compatibles con Azure Arc con VMware o SCVMM habilitados](../samples/samples-by-category.md#list-azure-arc-enabled-custom-locations-with-vmware-or-scvmm-enabled)
- microsoft.falcon/namespaces
- Microsoft.Fidalgo/devcenters (centros de desarrollo de Fidalgo)
- microsoft.fidalgo/machinedefinitions
- Microsoft.Fidalgo/projects (proyectos de Fidalgo)
- Microsoft.Fidalgo/projects/environments (entornos de Fidalgo)
- Microsoft.FluidRelay/fluidRelayServers (servidores FluidRelay)
- microsoft.footprintmonitoring/profiles
- microsoft.gaming/titles
- Microsoft.Genomics/accounts (cuentas de Genomics)
- microsoft.guestconfiguration/automanagedaccounts
- Microsoft.HanaOnAzure/hanaInstances (SAP HANA en Azure)
- Microsoft.HanaOnAzure/sapMonitors (instancias de Azure Monitor para soluciones de SAP)
- microsoft.hardwaresecuritymodules/dedicatedhsms
- Microsoft.HDInsight/clusterpools (grupos de clústeres de HDInsight)
- Microsoft.HDInsight/clusterpools/clusters (clústeres de HDInsight Gen2)
- Microsoft.HDInsight/clusters (clústeres de HDInsight)
- Microsoft.HealthBot/healthBots (Azure Health Bot)
- Microsoft.HealthcareApis/services (Azure API for FHIR)
- microsoft.healthcareapis/services/privateendpointconnections
- Microsoft.HealthcareApis/workspaces (áreas de trabajo de Healthcare APIs)
- Microsoft.HealthcareApis/workspaces/dicomservices (servicios DICOM)
- Microsoft.HealthcareApis/workspaces/fhirservices (servicios FHIR)
- Microsoft.HealthcareApis/workspaces/iotconnectors (conectores de IoT)
- Microsoft.HpcWorkbench/instances (instancias de HPC Workbench, versión preliminar)
- Microsoft.HybridCompute/machines (servidores - Azure Arc)
  - Consulta de ejemplo: [obtención del recuento y el porcentaje de servidores habilitados para Arc por dominio](../samples/samples-by-category.md#get-count-and-percentage-of-arc-enabled-servers-by-domain)
- microsoft.hybridcompute/machines/extensions
- Microsoft.HybridCompute/privateLinkScopes (ámbitos de Private Link de Azure Arc)
- Microsoft.HybridData/dataManagers (instancias de StorSimple Data Manager)
- Microsoft.HybridNetwork/devices (administrador de funciones de red de Azure: dispositivos [versión preliminar])
- Microsoft.HybridNetwork/networkFunctions (administración de funciones de red de Azure: funciones de red [versión preliminar])
- microsoft.hybridnetwork/virtualnetworkfunctions
- Microsoft.ImportExport/jobs (trabajos de importación o exportación)
- microsoft.industrydatalifecycle/basemodels
- microsoft.industrydatalifecycle/custodiancollaboratives
- microsoft.industrydatalifecycle/dataconsumercollaboratives
- microsoft.industrydatalifecycle/derivedmodels
- microsoft.industrydatalifecycle/membercollaboratives
- microsoft.industrydatalifecycle/modelmappings
- microsoft.industrydatalifecycle/pipelinesets
- microsoft.insights/actiongroups
- microsoft.insights/activitylogalerts
- microsoft.insights/alertrules
- microsoft.insights/autoscalesettings
- microsoft.insights/components (Application Insights)
- microsoft.insights/datacollectionendpoints
- microsoft.insights/datacollectionrules (reglas de recopilación de datos)
- microsoft.insights/guestdiagnosticsettings
- microsoft.insights/metricalerts
- microsoft.insights/notificationgroups
- microsoft.insights/notificationrules
- Microsoft.Insights/privateLinkScopes (ámbitos de Private Link de Azure Monitor)
- microsoft.insights/querypacks
- microsoft.insights/scheduledqueryrules
- microsoft.insights/webtests (pruebas de disponibilidad)
- microsoft.insights/workbooks (libros de Azure)
- microsoft.insights/workbooktemplates (plantillas de libro de Azure)
- Microsoft.IntelligentITDigitalTwin/digitalTwins (Minervas)
- Microsoft.IntelligentITDigitalTwin/digitalTwins/assets (recursos)
- Microsoft.IntelligentITDigitalTwin/digitalTwins/executionPlans (implementaciones)
- Microsoft.IntelligentITDigitalTwin/digitalTwins/testPlans (conjuntos)
- Microsoft.IntelligentITDigitalTwin/digitalTwins/tests (scripts)
- Microsoft.IoTCentral/IoTApps (aplicaciones de IoT Central)
- microsoft.iotspaces/graph
- microsoft.keyvault/hsmpools
- microsoft.keyvault/managedhsms
- Microsoft.KeyVault/vaults (almacenes de claves)
  - Consulta de ejemplo: [recuento de recursos de almacén de claves](../samples/samples-by-category.md#count-key-vault-resources)
  - Consulta de ejemplo: [almacenes de claves con el nombre de la suscripción](../samples/samples-by-category.md#key-vaults-with-subscription-name)
- Microsoft.Kubernetes/connectedClusters (Kubernetes - Azure Arc)
  - Consulta de ejemplo: [enumeración de todos los clústeres de Kubernetes compatibles con Azure Arc sin la extensión de Azure Monitor](../samples/samples-by-category.md#list-all-azure-arc-enabled-kubernetes-clusters-without-azure-monitor-extension)
  - Consulta de ejemplo: [enumeración de todos los recursos de Kubernetes compatibles con Azure Arc](../samples/samples-by-category.md#list-all-azure-arc-enabled-kubernetes-resources)
- Microsoft.Kusto/clusters (clústeres de Azure Data Explorer)
- Microsoft.Kusto/clusters/databases (bases de datos de Azure Data Explorer)
- Microsoft.LabServices/labAccounts (servicios de laboratorio)
- microsoft.labservices/labplans
- microsoft.labservices/labs
- Microsoft.LoadTestService/LoadTests (pruebas de carga nativas de la nube)
- Microsoft.Logic/integrationAccounts (cuentas de integración)
- Microsoft.Logic/integrationServiceEnvironments (entornos del servicio de integración)
- Microsoft.Logic/integrationServiceEnvironments/managedApis (conector administrado)
- Microsoft.Logic/workflows (aplicaciones lógicas)
- Microsoft.Logz/monitors (cuenta principal de Logz)
- Microsoft.Logz/monitors/accounts (cuenta secundaria de Logz)
- Microsoft.MachineLearning/commitmentPlans (planes de servicio web de Estudio de Machine Learning [clásicos])
- Microsoft.MachineLearning/webServices (servicios web de Estudio de Machine Learning [clásicos])
- Microsoft.MachineLearning/workspaces (áreas de trabajo de Estudio de Machine Learning [clásicas])
- microsoft.machinelearningcompute/operationalizationclusters
- microsoft.machinelearningexperimentation/accounts/workspaces
- microsoft.machinelearningservices/aisysteminventories
- microsoft.machinelearningservices/modelinventories
- microsoft.machinelearningservices/modelinventory
- microsoft.machinelearningservices/virtualclusters
- Microsoft.MachineLearningServices/workspaces (aprendizaje automático)
- microsoft.machinelearningservices/workspaces/batchendpoints
- microsoft.machinelearningservices/workspaces/batchendpoints/deployments
- microsoft.machinelearningservices/workspaces/inferenceendpoints
- microsoft.machinelearningservices/workspaces/inferenceendpoints/deployments
- Microsoft.MachineLearningServices/workspaces/onlineEndpoints (puntos de conexión en línea de aprendizaje automático)
- Microsoft.MachineLearningServices/workspaces/onlineEndpoints/deployments (implementaciones en línea de aprendizaje automático)
- Microsoft.Maintenance/maintenanceConfigurations (configuraciones de mantenimiento)
- microsoft.maintenance/maintenancepolicies
- microsoft.managedidentity/groups
- Microsoft.ManagedIdentity/userAssignedIdentities (identidades administradas)
- microsoft.managednetwork/managednetworkgroups
- microsoft.managednetwork/managednetworkpeeringpolicies
- microsoft.managednetwork/managednetworks
- microsoft.managednetwork/managednetworks/managednetworkgroups
- microsoft.managednetwork/managednetworks/managednetworkpeeringpolicies
- Microsoft.Maps/accounts (cuentas de Azure Maps)
- Microsoft.Maps/accounts/privateAtlases (recursos de creador de Azure Maps)
- microsoft.maps/accounts/privateatlases
- Microsoft.MarketplaceApps/classicDevServices (servicios para desarrolladores clásicos)
- microsoft.media/mediaservices (Media Services)
- microsoft.media/mediaservices/liveevents (eventos en directo)
- microsoft.media/mediaservices/streamingEndpoints (puntos de conexión de streaming)
- microsoft.media/mediaservices/transforms
- microsoft.media/videoanalyzers (instancias de Video Analyzer)
- microsoft.microservices4spring/appclusters
- microsoft.migrate/assessmentprojects
- microsoft.migrate/migrateprojects
- microsoft.migrate/movecollections
- Microsoft.Migrate/projects (proyectos de migración)
- Microsoft.MixedReality/holographicsBroadcastAccounts (cuentas de Holographics Broadcast)
- Microsoft.MixedReality/objectAnchorsAccounts (cuentas de Object Anchors)
- Microsoft.MixedReality/objectUnderstandingAccounts (cuentas de Object Understanding)
- Microsoft.MixedReality/remoteRenderingAccounts (cuentas de Remote Rendering)
- Microsoft.MixedReality/spatialAnchorsAccounts (cuentas de Spatial Anchors)
- microsoft.mixedreality/surfacereconstructionaccounts
- Microsoft.MobileNetwork/mobileNetworks (redes móviles)
- microsoft.mobilenetwork/mobilenetworks/datanetworks
- microsoft.mobilenetwork/mobilenetworks/services
- microsoft.mobilenetwork/mobilenetworks/simpolicies
- Microsoft.MobileNetwork/mobileNetworks/sites (sitios de redes móviles)
- microsoft.mobilenetwork/mobilenetworks/slices
- microsoft.mobilenetwork/networks
- microsoft.mobilenetwork/networks/sites
- Microsoft.MobileNetwork/packetCoreControlPlanes (Arc para funciones de red: núcleos de paquetes)
- microsoft.mobilenetwork/packetcorecontrolplanes/packetcoredataplanes
- microsoft.mobilenetwork/packetcorecontrolplanes/packetcoredataplanes/attacheddatanetworks
- microsoft.mobilenetwork/sims
- microsoft.mobilenetwork/sims/simprofiles
- Microsoft.NetApp/netAppAccounts (cuentas de NetApp)
- microsoft.netapp/netappaccounts/backuppolicies
- Microsoft.NetApp/netAppAccounts/capacityPools (grupos de capacidad)
- Microsoft.NetApp/netAppAccounts/capacityPools/Volumes (volúmenes)
- microsoft.netapp/netappaccounts/capacitypools/volumes/mounttargets
- Microsoft.NetApp/netAppAccounts/capacityPools/volumes/snapshots (instantáneas)
- Microsoft.NetApp/netAppAccounts/snapshotPolicies (directivas de instantánea)
- Microsoft.Network/applicationGateways (puertas de enlace de aplicación)
- Microsoft.Network/ApplicationGatewayWebApplicationFirewallPolicies (directivas de firewall de aplicaciones web [WAF])
- Microsoft.Network/applicationSecurityGroups (grupos de seguridad de la aplicación)
- Microsoft.Network/azureFirewalls (firewalls)
- Microsoft.Network/bastionHosts (bastiones)
- Microsoft.Network/connections (conexiones)
- Microsoft.Network/customIpPrefixes (prefijos IP personalizados)
- microsoft.network/ddoscustompolicies
- Microsoft.Network/ddosProtectionPlans (planes de protección contra DDoS)
- microsoft.network/dnsforwardingrulesets
- microsoft.network/dnsresolvers
- Microsoft.Network/dnsZones (zonas DNS)
- microsoft.network/dscpconfigurations
- Microsoft.Network/expressRouteCircuits (circuitos ExpressRoute)
- microsoft.network/expressroutecrossconnections
- microsoft.network/expressroutegateways
- Microsoft.Network/expressRoutePorts (ExpressRoute Direct)
- Microsoft.Network/firewallPolicies (directivas de firewall)
- Microsoft.Network/frontdoors (instancias de Front Door)
- Microsoft.Network/FrontDoorWebApplicationFirewallPolicies (directivas de firewall de aplicaciones web [WAF])
- microsoft.network/ipallocations
- Microsoft.Network/ipGroups (grupos de IP)
- Microsoft.Network/LoadBalancers (equilibradores de carga)
- Microsoft.Network/localnetworkgateways (puertas de enlace de red locales)
- microsoft.network/mastercustomipprefixes
- Microsoft.Network/natGateways (puertas de enlace NAT)
- Microsoft.Network/NetworkExperimentProfiles (perfiles de Internet Analyzer)
- microsoft.network/networkintentpolicies
- Microsoft.Network/networkinterfaces (interfaces de red)
  - Consulta de ejemplo. [obtención de redes virtuales y subredes de interfaces de red](../samples/samples-by-category.md#get-virtual-networks-and-subnets-of-network-interfaces)
  - Consulta de ejemplo: [enumeración de máquinas virtuales con su interfaz de red y dirección IP pública](../samples/samples-by-category.md#list-virtual-machines-with-their-network-interface-and-public-ip)
- Microsoft.Network/networkManagers (administradores de red)
- microsoft.network/networkprofiles
- Microsoft.Network/NetworkSecurityGroups (grupos de seguridad de red)
  - Consulta de ejemplo: [visualización de los grupos de seguridad de red sin asociar](../samples/samples-by-category.md#show-unassociated-network-security-groups)
- microsoft.network/networksecurityperimeters
- microsoft.network/networkvirtualappliances
- microsoft.network/networkwatchers (instancias de Network Watcher)
- microsoft.network/networkwatchers/connectionmonitors
- microsoft.network/networkwatchers/flowlogs (registros de flujo de NSG)
- microsoft.network/networkwatchers/lenses
- microsoft.network/networkwatchers/pingmeshes
- microsoft.network/p2svpngateways
- Microsoft.Network/privateDnsZones (zonas DNS privadas)
- microsoft.network/privatednszones/virtualnetworklinks
- microsoft.network/privateendpointredirectmaps
- Microsoft.Network/privateEndpoints (puntos de conexión privados)
- Microsoft.Network/privateLinkServices (servicios de vínculo privado)
- Microsoft.Network/PublicIpAddresses (direcciones IP públicas)
  - Consulta de ejemplo: [enumeración de máquinas virtuales con su interfaz de red y dirección IP pública](../samples/samples-by-category.md#list-virtual-machines-with-their-network-interface-and-public-ip)
- Microsoft.Network/publicIpPrefixes (prefijos de IP pública)
- Microsoft.Network/routeFilters (filtros de ruta)
- Microsoft.Network/routeTables (tablas de ruta)
- microsoft.network/sampleresources
- microsoft.network/securitypartnerproviders
- Microsoft.Network/serviceEndpointPolicies (directivas de punto de conexión de servicio)
- Microsoft.Network/trafficmanagerprofiles (perfiles de Traffic Manager)
- microsoft.network/virtualhubs
- microsoft.network/virtualhubs/bgpconnections
- microsoft.network/virtualhubs/ipconfigurations
- Microsoft.Network/virtualNetworkGateways (puertas de enlace de red virtuales)
- Microsoft.Network/virtualNetworks (redes virtuales)
- microsoft.network/virtualnetworktaps
- microsoft.network/virtualrouters
- Microsoft.Network/virtualWans (instancias de Virtual WAN)
- microsoft.network/vpngateways
- microsoft.network/vpnserverconfigurations
- microsoft.network/vpnsites
- Microsoft.NotificationHubs/namespaces (espacios de nombres del centro de notificaciones)
- Microsoft.NotificationHubs/namespaces/notificationHubs (Notification Hubs)
- microsoft.nutanix/interfaces
- microsoft.nutanix/nodes
- microsoft.objectstore/osnamespaces
- microsoft.offazure/hypervsites
- microsoft.offazure/importsites
- microsoft.offazure/mastersites
- microsoft.offazure/serversites
- microsoft.offazure/vmwaresites
- microsoft.openlogisticsplatform/applicationworkspaces
- Microsoft.OpenLogisticsPlatform/workspaces (plataforma abierta de la cadena de suministro)
- microsoft.operationalinsights/clusters
- Microsoft.OperationalInsights/querypacks (paquetes de consulta de Log Analytics)
- Microsoft.OperationalInsights/workspaces (áreas de trabajo de Log Analytics)
- Microsoft.OperationsManagement/solutions (soluciones)
- microsoft.operationsmanagement/views
- Microsoft.Orbital/contactProfiles (perfiles de contacto)
- microsoft.orbital/groundstations
- microsoft.orbital/orbitalendpoints
- microsoft.orbital/orbitalgateways
- microsoft.orbital/orbitalgateways/orbitall2connections
- microsoft.orbital/orbitalgateways/orbitall3connections
- Microsoft.Orbital/spacecrafts (naves espaciales)
- Microsoft.Peering/peerings (emparejamiento)
- Microsoft.Peering/peeringServices (instancias de Peering Service)
- Microsoft.Portal/dashboards (paneles compartidos)
- microsoft.portalsdk/rootresources
- microsoft.powerbi/privatelinkservicesforpowerbi
- microsoft.powerbi/tenants
- microsoft.powerbi/workspacecollections
- microsoft.powerbidedicated/autoscalevcores
- Microsoft.PowerBIDedicated/capacities (Power BI Embedded)
- microsoft.powerplatform/accounts
- microsoft.powerplatform/enterprisepolicies
- microsoft.projectbabylon/accounts
- Microsoft.Purview/Accounts (cuentas de Purview)
- Microsoft.Quantum/Workspaces (áreas de trabajo de Quantum)
- Microsoft.RecommendationsService/accounts (cuentas de Intelligent Recommendations)
- Microsoft.RecommendationsService/accounts/modeling (modelado)
- Microsoft.RecommendationsService/accounts/serviceEndpoints (puntos de conexión de servicio)
- Microsoft.RecoveryServices/vaults (almacenes de Recovery Services)
- Microsoft.RedHatOpenShift/openShiftClusters (clústeres de OpenShift)
- Microsoft.Relay/namespaces (retransmisiones)
- microsoft.remoteapp/collections
- microsoft.resiliency/chaosexperiments
- Microsoft.ResourceConnector/Appliances (puentes de recursos)
- Microsoft.resourcegraph/queries (consultas de Resource Graph)
- Microsoft.Resources/deploymentScripts (scripts de implementación)
- Microsoft.Resources/templateSpecs (especificaciones de plantilla)
- microsoft.resources/templatespecs/versions
- Microsoft.SaaS/applications (software como servicio [clásico])
- Microsoft.SaaS/resources (SaaS)
- Microsoft.Scheduler/jobCollections (colecciones de trabajos de Scheduler)
- Microsoft.Scom/managedInstances (recursos de Aquila)
- microsoft.scvmm/clouds
- Microsoft.scvmm/virtualMachines (máquina virtual de SCVMM - Azure Arc)
- microsoft.scvmm/virtualmachinetemplates
- microsoft.scvmm/virtualnetworks
- microsoft.scvmm/vmmservers
- Microsoft.Search/searchServices (servicios Search)
- microsoft.security/automations
- microsoft.security/iotsecuritysolutions
- Microsoft.SecurityDetonation/chambers (cámaras de detonación de seguridad)
- Microsoft.ServiceBus/namespaces (espacios de nombres de Service Bus)
- Microsoft.ServiceFabric/clusters (clústeres de Service Fabric)
- microsoft.servicefabric/containergroupsets
- Microsoft.ServiceFabric/managedclusters (clústeres administrados de Service Fabric)
- microsoft.servicefabricmesh/applications
- microsoft.servicefabricmesh/gateways
- microsoft.servicefabricmesh/networks
- microsoft.servicefabricmesh/secrets
- microsoft.servicefabricmesh/volumes
- Microsoft.ServicesHub/connectors (conectores del centro de servicios)
- Microsoft.SignalRService/SignalR (SignalR)
- Microsoft.SignalRService/WebPubSub (servicio Web PubSub)
- microsoft.singularity/accounts
- microsoft.skytap/nodes
- microsoft.solutions/appliancedefinitions
- microsoft.solutions/appliances
- Microsoft.Solutions/applicationDefinitions (definiciones de aplicación administrada del catálogo de servicios)
- Microsoft.Solutions/applications (aplicaciones administradas)
- microsoft.solutions/jitrequests
- microsoft.spoolservice/spools
- Microsoft.Sql/instancePools (grupos de instancia)
- Microsoft.Sql/managedInstances (instancias de SQL Managed Instance)
- Microsoft.Sql/managedInstances/databases (bases de datos administradas)
- Microsoft.Sql/servers (servidores SQL)
- Microsoft.Sql/servers/databases (bases de datos SQL)
  - Consulta de ejemplo: [enumeración de bases de datos SQL y sus grupos elásticos](../samples/samples-by-category.md#list-sql-databases-and-their-elastic-pools)
- Microsoft.Sql/servers/elasticpools (grupos elásticos SQL)
  - Consulta de ejemplo: [enumeración de bases de datos SQL y sus grupos elásticos](../samples/samples-by-category.md#list-sql-databases-and-their-elastic-pools)
- microsoft.sql/servers/jobaccounts
- Microsoft.Sql/servers/jobAgents (agentes de trabajos elásticos)
- Microsoft.Sql/virtualClusters (clústeres virtuales)
- microsoft.sqlvirtualmachine/sqlvirtualmachinegroups
- Microsoft.SqlVirtualMachine/SqlVirtualMachines (máquinas virtuales SQL)
- microsoft.sqlvm/dwvm
- Microsoft.Storage/StorageAccounts (cuentas de almacenamiento)
  - Consulta de ejemplo: [búsqueda de cuentas de almacenamiento con una etiqueta concreta que no distingue mayúsculas de minúsculas en el grupo de recursos](../samples/samples-by-category.md#find-storage-accounts-with-a-specific-case-insensitive-tag-on-the-resource-group)
  - Consulta de ejemplo: [búsqueda de cuentas de almacenamiento con una etiqueta concreta que distingue mayúsculas de minúsculas en el grupo de recursos](../samples/samples-by-category.md#find-storage-accounts-with-a-specific-case-sensitive-tag-on-the-resource-group)
  - Consulta de ejemplo: [enumeración de todas las cuentas de almacenamiento con un valor de etiqueta específico](../samples/samples-by-category.md#list-all-storage-accounts-with-specific-tag-value)
- microsoft.storagecache/amlfilesystems
- Microsoft.StorageCache/caches (cachés HPC)
- Microsoft.StoragePool/diskPools (grupos de discos)
- Microsoft.StorageSync/storageSyncServices (servicios de sincronización de almacenamiento)
- Microsoft.StorageSyncDev/storageSyncServices (servicios de sincronización de almacenamiento)
- Microsoft.StorageSyncInt/storageSyncServices (servicios de sincronización de almacenamiento)
- Microsoft.StorSimple/Managers (administrador de dispositivos de StorSimple)
- Microsoft.StreamAnalytics/clusters (clústeres de Stream Analytics)
- Microsoft.StreamAnalytics/StreamingJobs (trabajos de Stream Analytics)
- microsoft.swiftlet/virtualmachines
- microsoft.swiftlet/virtualmachinesnapshots
- Microsoft.Synapse/privateLinkHubs (Azure Synapse Analytics [centros de Private Link])
- Microsoft.Synapse/workspaces (Azure Synapse Analytics)
- Microsoft.Synapse/workspaces/bigDataPools (grupos de Apache Spark)
- microsoft.synapse/workspaces/eventstreams
- Microsoft.Synapse/workspaces/kustopools (grupos de Kusto, versión preliminar)
- microsoft.synapse/workspaces/sqldatabases
- Microsoft.Synapse/workspaces/sqlPools (grupos de SQL dedicados)
- microsoft.terraformoss/providerregistrations
- Microsoft.TestBase/testBaseAccounts (cuentas de Test Base)
- microsoft.testbase/testbaseaccounts/packages
- microsoft.testbase/testbases
- Microsoft.TimeSeriesInsights/environments (entornos de Time Series Insights)
- Microsoft.TimeSeriesInsights/environments/eventsources (orígenes del evento de Time Series Insights)
- Microsoft.TimeSeriesInsights/environments/referenceDataSets (conjuntos de datos de referencia de Time Series Insights)
- microsoft.token/stores
- microsoft.tokenvault/vaults
- Microsoft.VideoIndexer/accounts (Video Analyzer for Media)
- Microsoft.VirtualMachineImages/imageTemplates (plantillas de imagen)
- microsoft.visualstudio/account (organizaciones de Azure DevOps)
- microsoft.visualstudio/account/extension
- microsoft.visualstudio/account/project (DevOps Starter)
- microsoft.vmware/arczones
- microsoft.vmware/resourcepools
- microsoft.vmware/vcenters
- microsoft.vmware/virtualmachines
- microsoft.vmware/virtualmachinetemplates
- microsoft.vmware/virtualnetworks
- Microsoft.VMwareCloudSimple/dedicatedCloudNodes (nodos de CloudSimple)
- Microsoft.VMwareCloudSimple/dedicatedCloudServices (servicios de CloudSimple)
- Microsoft.VMwareCloudSimple/virtualMachines (máquinas virtuales de CloudSimple)
- microsoft.vmwareonazure/privateclouds
- microsoft.vmwarevirtustream/privateclouds
- microsoft.vsonline/accounts
- Microsoft.VSOnline/Plans (planes de Visual Studio Online)
- microsoft.web/apimanagementaccounts
- microsoft.web/apimanagementaccounts/apis
- microsoft.web/certificates
- Microsoft.Web/connectionGateways (puertas de enlace de datos locales)
- Microsoft.Web/connections (conexiones de API)
- Microsoft.Web/customApis (conector personalizado de Logic Apps)
- Microsoft.Web/HostingEnvironments (instancias de App Service Environment)
- Microsoft.Web/KubeEnvironments (entornos de Kubernetes de App Service)
- Microsoft.Web/serverFarms (planes de App Service)
- Microsoft.Web/sites (App Services)
- microsoft.web/sites/premieraddons
- Microsoft.Web/sites/slots (App Service [ranuras])
- Microsoft.Web/StaticSites (Static Web Apps)
- microsoft.web/workerapps
- Microsoft.WindowsESU/multipleActivationKeys (claves de activación múltiple de Windows)
- Microsoft.WindowsIoT/DeviceServices (Windows 10 IoT Core Services)
- microsoft.workloadbuilder/migrationagents
- microsoft.workloadbuilder/workloads
- myget.packagemanagement/services
- NGINX.NGINXPLUS/nginxDeployments (implementación de NGINX)
- <<<Paraleap.CloudMonix/services (CloudMonix)
- Pokitdok.Platform/services (plataforma PokitDok)
- Providers.Test/statefulIbizaEngines (enlazadores de servicios)
- RavenHq.Db/databases (RavenHQ)
- Raygun.CrashReporting/apps (Raygun)
- Sendgrid.Email/accounts (cuentas de SendGrid)
- sparkpost.basic/services
- stackify.retrace/services
- test.shoebox/testresources
- test.shoebox/testresources2
- TrendMicro.DeepSecurity/accounts (Deep Security SaaS)
- u2uconsult.theidentityhub/services
- Wandisco.Fusion/fusionGroups (instancias de LiveData Plane)
- Wandisco.Fusion/fusionGroups/azureZones (zonas de Azure)
- Wandisco.Fusion/fusionGroups/azureZones/plugins (complementos)
- Wandisco.Fusion/fusionGroups/hiveReplicationRules (reglas de replicación de Hive)
- Wandisco.Fusion/fusionGroups/managedOnPremZones (zonas en el entorno local)
- wandisco.fusion/fusiongroups/onpremzones
- Wandisco.Fusion/fusionGroups/replicationRules (reglas de replicación)
- Wandisco.Fusion/migrators (migradores de LiveData)
- Wandisco.Fusion/migrators/exclusionTemplates (exclusiones)
- Wandisco.Fusion/migrators/liveDataMigrations (migraciones)
- Wandisco.Fusion/migrators/metadataMigrations (migraciones de metadatos)
- Wandisco.Fusion/migrators/metadataTargets (destinos de metadatos)
- Wandisco.Fusion/migrators/pathMappings (asignaciones de rutas de acceso)
- Wandisco.Fusion/migrators/targets (destinos)

## <a name="securityresources"></a>securityresources

En [Consultas de ejemplo de Resource Graph para securityresources](../samples/samples-by-table.md#securityresources) encontrará consultas de ejemplo para esta tabla.

- microsoft.security/assessments
  - Consulta de ejemplo: [recuento de recursos correctos, incorrectos y no aplicables por recomendación](../samples/samples-by-category.md#count-healthy-unhealthy-and-not-applicable-resources-per-recommendation)
  - Consulta de ejemplo: [enumeración de recomendaciones de Azure Security Center](../samples/samples-by-category.md#list-azure-security-center-recommendations)
  - Consulta de ejemplo: [enumeración de los resultados de valoraciones de vulnerabilidades de Container Registry](../samples/samples-by-category.md#list-container-registry-vulnerability-assessment-results)
  - Consulta de ejemplo: [enumeración de los resultados de valoraciones de vulnerabilidades de Qualys](../samples/samples-by-category.md#list-qualys-vulnerability-assessment-results)
- microsoft.security/assessments/subassessments
  - Consulta de ejemplo: [enumeración de los resultados de valoraciones de vulnerabilidades de Container Registry](../samples/samples-by-category.md#list-container-registry-vulnerability-assessment-results)
  - Consulta de ejemplo: [enumeración de los resultados de valoraciones de vulnerabilidades de Qualys](../samples/samples-by-category.md#list-qualys-vulnerability-assessment-results)
- microsoft.security/insights/classification (información de seguridad sobre confidencialidad de datos, versión preliminar)
- microsoft.security/iotalerts
  - Consulta de ejemplo: [obtención de todas las alertas de IoT en el centro, filtradas por tipo](../samples/samples-by-category.md#get-all-iot-alerts-on-hub-filtered-by-type)
  - Consulta de ejemplo: [obtención de una alerta de IoT específica](../samples/samples-by-category.md#get-specific-iot-alert)
- microsoft.security/locations/alerts (aletas de seguridad)
- microsoft.security/pricings
  - Consulta de ejemplo: [visualización del plan de tarifa de Azure Defender por suscripción](../samples/samples-by-category.md#show-azure-defender-pricing-tier-per-subscription)
- microsoft.security/regulatorycompliancestandards
  - Consulta de ejemplo: [estado de cumplimiento normativo por estándar](../samples/samples-by-category.md#regulatory-compliance-state-per-compliance-standard)
- microsoft.security/regulatorycompliancestandards/regulatorycompliancecontrols
- microsoft.security/regulatorycompliancestandards/regulatorycompliancecontrols/regulatorycomplianceassessments
  - Consulta de ejemplo: [estado de las valoraciones del cumplimiento normativo](../samples/samples-by-category.md#regulatory-compliance-assessments-state)
- microsoft.security/securescores
  - Consulta de ejemplo: [puntuación segura por grupo de administración](../samples/samples-by-category.md#secure-score-per-management-group)
  - Consulta de ejemplo: [puntuación de seguridad por suscripción](../samples/samples-by-category.md#secure-score-per-subscription)
- microsoft.security/securescores/securescorecontrols
  - Consulta de ejemplo: [control de la puntuación de seguridad por suscripción](../samples/samples-by-category.md#controls-secure-score-per-subscription)
- microsoft.security/softwareinventories
- microsoft.security/softwareinventory

## <a name="servicehealthresources"></a>servicehealthresources

En [Consultas de ejemplo de Resource Graph para servicehealthresources](../samples/samples-by-table.md#servicehealthresources) encontrará consultas de ejemplo para esta tabla.

- microsoft.resourcehealth/events
  - Consulta de ejemplo: [impacto en la suscripción de los eventos activos de Service Health](../samples/samples-by-category.md#active-service-health-event-subscription-impact)
  - Consulta de ejemplo: [todos los eventos de aviso de estado activos](../samples/samples-by-category.md#all-active-health-advisory-events)
  - Consulta de ejemplo: [todos los eventos de mantenimiento planeado activos](../samples/samples-by-category.md#all-active-planned-maintenance-events)
  - Consulta de ejemplo: [todos los eventos de Service Health activos](../samples/samples-by-category.md#all-active-service-health-events)
  - Consulta de ejemplo: [todos los eventos de incidencia de servicio activos](../samples/samples-by-category.md#all-active-service-issue-events)

## <a name="workloadmonitorresources"></a>workloadmonitorresources

- microsoft.workloadmonitor/monitors

## <a name="next-steps"></a>Pasos siguientes

- Obtenga más información sobre el [lenguaje de consulta](../concepts/query-language.md).
- Obtenga más información sobre cómo [explorar recursos](../concepts/explore-resources.md).
- Consulte ejemplos de [consultas de inicio](../samples/starter.md).
