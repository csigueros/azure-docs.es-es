---
title: Eliminación del modo completo
description: Muestra cómo los tipos de recursos controlan la eliminación en modo completo en las plantillas de Azure Resource Manager.
ms.topic: conceptual
ms.date: 10/20/2021
ms.openlocfilehash: ea3ea4de33a1680961f078e8e3cc8815da45956e
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/22/2021
ms.locfileid: "130229906"
---
# <a name="deletion-of-azure-resources-for-complete-mode-deployments"></a>Eliminación de recursos de Azure en implementaciones en modo completo

En este artículo, se describe cómo se eliminan los tipos de recursos cuando no están en una plantilla implementada en modo completo.

Los tipos de recursos con la marca **Sí** se eliminan cuando el tipo no está en la plantilla que se implementó en modo completo.

Los tipos de recursos con la marca **No** no se eliminan automáticamente cuando no están en la plantilla; sin embargo, sí lo hacen cuando se elimina el recurso principal. Para ver una descripción completa de este comportamiento, consulte [Modos de implementación de Azure Resource Manager](deployment-modes.md).

Si implementa en [más de un grupo de recursos en una plantilla](./deploy-to-resource-group.md), se pueden eliminar los recursos del grupo de recursos especificado en la operación de implementación. Los recursos de los grupos de recursos secundarios no se eliminarán.

Los recursos se enumeran por espacio de nombres del proveedor de recursos. Para hacer coincidir un espacio de nombres de proveedor de recursos con su nombre de servicio de Azure, consulte [Proveedores de recursos para servicios de Azure](../management/azure-services-resource-providers.md).

> [!NOTE]
> Use siempre la [operación what-if](./deploy-what-if.md) antes de implementar una plantilla en el modo completo. What-if muestra qué recursos se crearán, eliminarán o modificarán. Use what-if para evitar la eliminación accidental de recursos.

Vaya a un espacio de nombres del proveedor de recursos:
> [!div class="op_single_selector"]
> - [Microsoft.AAD](#microsoftaad)
> - [Microsoft.Addons](#microsoftaddons)
> - [Microsoft.ADHybridHealthService](#microsoftadhybridhealthservice)
> - [Microsoft.Advisor](#microsoftadvisor)
> - [Microsoft.AgFoodPlatform](#microsoftagfoodplatform)
> - [Microsoft.AlertsManagement](#microsoftalertsmanagement)
> - [Microsoft.AnalysisServices](#microsoftanalysisservices)
> - [Microsoft.AnyBuild](#microsoftanybuild)
> - [Microsoft.ApiManagement](#microsoftapimanagement)
> - [Microsoft.AppAssessment](#microsoftappassessment)
> - [Microsoft.AppConfiguration](#microsoftappconfiguration)
> - [Microsoft.AppPlatform](#microsoftappplatform)
> - [Microsoft.Attestation](#microsoftattestation)
> - [Microsoft.Authorization](#microsoftauthorization)
> - [Microsoft.Automanage](#microsoftautomanage)
> - [Microsoft.Automation](#microsoftautomation)
> - [Microsoft.AVS](#microsoftavs)
> - [Microsoft.Azure.Geneva](#microsoftazuregeneva)
> - [Microsoft.AzureActiveDirectory](#microsoftazureactivedirectory)
> - [Microsoft.AzureArcData](#microsoftazurearcdata)
> - [Microsoft.AzureCIS](#microsoftazurecis)
> - [Microsoft.AzureData](#microsoftazuredata)
> - [Microsoft.AzurePercept](#microsoftazurepercept)
> - [Microsoft.AzureSphere](#microsoftazuresphere)
> - [Microsoft.AzureStack](#microsoftazurestack)
> - [Microsoft.AzureStackHCI](#microsoftazurestackhci)
> - [Microsoft.BackupSolutions](#microsoftbackupsolutions)
> - [Microsoft.BareMetalInfrastructure](#microsoftbaremetalinfrastructure)
> - [Microsoft.Batch](#microsoftbatch)
> - [Microsoft.Billing](#microsoftbilling)
> - [Microsoft.BillingBenefits](#microsoftbillingbenefits)
> - [Microsoft.Blockchain](#microsoftblockchain)
> - [Microsoft.BlockchainTokens](#microsoftblockchaintokens)
> - [Microsoft.Blueprint](#microsoftblueprint)
> - [Microsoft.BotService](#microsoftbotservice)
> - [Microsoft.Cache](#microsoftcache)
> - [Microsoft.Capacity](#microsoftcapacity)
> - [Microsoft.Cascade](#microsoftcascade)
> - [Microsoft.Cdn](#microsoftcdn)
> - [Microsoft.CertificateRegistration](#microsoftcertificateregistration)
> - [Microsoft.ChangeAnalysis](#microsoftchangeanalysis)
> - [Microsoft.ClassicCompute](#microsoftclassiccompute)
> - [Microsoft.ClassicInfrastructureMigrate](#microsoftclassicinfrastructuremigrate)
> - [Microsoft.ClassicNetwork](#microsoftclassicnetwork)
> - [Microsoft.ClassicStorage](#microsoftclassicstorage)
> - [Microsoft.ClusterStor](#microsoftclusterstor)
> - [Microsoft.CodeSigning](#microsoftcodesigning)
> - [Microsoft.Codespaces](#microsoftcodespaces)
> - [Microsoft.CognitiveServices](#microsoftcognitiveservices)
> - [Microsoft.Compute](#microsoftcompute)
> - [Microsoft.Commerce](#microsoftcommerce)
> - [Microsoft.ConfidentialLedger](#microsoftconfidentialledger)
> - [Microsoft. ConnectedCache](#microsoftconnectedcache)
> - [Microsoft.ConnectedVehicle](#microsoftconnectedvehicle)
> - [Microsoft.ConnectedVMwarevSphere](#microsoftconnectedvmwarevsphere)
> - [Microsoft.Consumption](#microsoftconsumption)
> - [Microsoft.ContainerInstance](#microsoftcontainerinstance)
> - [Microsoft.ContainerRegistry](#microsoftcontainerregistry)
> - [Microsoft.ContainerService](#microsoftcontainerservice)
> - [Microsoft.CostManagement](#microsoftcostmanagement)
> - [Microsoft.CustomerLockbox](#microsoftcustomerlockbox)
> - [Microsoft.CustomProviders](#microsoftcustomproviders)
> - [Microsoft.D365CustomerInsights](#microsoftd365customerinsights)
> - [Microsoft.Dashboard](#microsoftdashboard)
> - [Microsoft.DataBox](#microsoftdatabox)
> - [Microsoft.DataBoxEdge](#microsoftdataboxedge)
> - [Microsoft.Databricks](#microsoftdatabricks)
> - [Microsoft.DataCatalog](#microsoftdatacatalog)
> - [Microsoft.DataFactory](#microsoftdatafactory)
> - [Microsoft.DataLakeAnalytics](#microsoftdatalakeanalytics)
> - [Microsoft.DataLakeStore](#microsoftdatalakestore)
> - [Microsoft.DataMigration](#microsoftdatamigration)
> - [Microsoft.DataProtection](#microsoftdataprotection)
> - [Microsoft.DataShare](#microsoftdatashare)
> - [Microsoft.DBforMariaDB](#microsoftdbformariadb)
> - [Microsoft.DBforMySQL](#microsoftdbformysql)
> - [Microsoft.DBforPostgreSQL](#microsoftdbforpostgresql)
> - [Microsoft.DelegatedNetwork](#microsoftdelegatednetwork)
> - [Microsoft.DeploymentManager](#microsoftdeploymentmanager)
> - [Microsoft.DesktopVirtualization](#microsoftdesktopvirtualization)
> - [Microsoft.Devices](#microsoftdevices)
> - [Microsoft.DeviceUpdate](#microsoftdeviceupdate)
> - [Microsoft.DevOps](#microsoftdevops)
> - [Microsoft.DevSpaces](#microsoftdevspaces)
> - [Microsoft.DevTestLab](#microsoftdevtestlab)
> - [Microsoft.Diagnostics](#microsoftdiagnostics)
> - [Microsoft.DigitalTwins](#microsoftdigitaltwins)
> - [Microsoft.DocumentDB](#microsoftdocumentdb)
> - [Microsoft.DomainRegistration](#microsoftdomainregistration)
> - [Microsoft.DynamicsLcs](#microsoftdynamicslcs)
> - [Microsoft.EdgeOrder](#microsoftedgeorder)
> - [Microsoft.EnterpriseKnowledgeGraph](#microsoftenterpriseknowledgegraph)
> - [Microsoft.EventGrid](#microsofteventgrid)
> - [Microsoft.EventHub](#microsofteventhub)
> - [Microsoft.Experimentation](#microsoftexperimentation)
> - [Microsoft.Falcon](#microsoftfalcon)
> - [Microsoft.Features](#microsoftfeatures)
> - [Microsoft.Fidalgo](#microsoftfidalgo)
> - [Microsoft.FluidRelay](#microsoftfluidrelay)
> - [Microsoft.Gallery](#microsoftgallery)
> - [Microsoft.Genomics](#microsoftgenomics)
> - [Microsoft.Graph](#microsoftgraph)
> - [Microsoft.GuestConfiguration](#microsoftguestconfiguration)
> - [Microsoft.HanaOnAzure](#microsofthanaonazure)
> - [Microsoft.HardwareSecurityModules](#microsofthardwaresecuritymodules)
> - [Microsoft.HDInsight](#microsofthdinsight)
> - [Microsoft.HealthBot](#microsofthealthbot)
> - [Microsoft.HealthcareApis](#microsofthealthcareapis)
> - [Microsoft.HpcWorkbench](#microsofthpcworkbench)
> - [Microsoft.HybridCompute](#microsofthybridcompute)
> - [Microsoft.HybridConnectivity](#microsofthybridconnectivity)
> - [Microsoft.HybridContainerService](#microsofthybridcontainerservice)
> - [Microsoft.HybridData](#microsofthybriddata)
> - [Microsoft.HybridNetwork](#microsofthybridnetwork)
> - [Microsoft.Hydra](#microsofthydra)
> - [Microsoft.ImportExport](#microsoftimportexport)
> - [Microsoft.Intune](#microsoftintune)
> - [Microsoft.IoTCentral](#microsoftiotcentral)
> - [Microsoft.IoTSecurity](#microsoftiotsecurity)
> - [Microsoft.IoTSpaces](#microsoftiotspaces)
> - [Microsoft.KeyVault](#microsoftkeyvault)
> - [Microsoft.Kubernetes](#microsoftkubernetes)
> - [Microsoft.KubernetesConfiguration](#microsoftkubernetesconfiguration)
> - [Microsoft.Kusto](#microsoftkusto)
> - [Microsoft.LabServices](#microsoftlabservices)
> - [Microsoft.LocationServices](#microsoftlocationservices)
> - [Microsoft.Logic](#microsoftlogic)
> - [Microsoft.MachineLearning](#microsoftmachinelearning)
> - [Microsoft.MachineLearningServices](#microsoftmachinelearningservices)
> - [Microsoft.Maintenance](#microsoftmaintenance)
> - [Microsoft.ManagedIdentity](#microsoftmanagedidentity)
> - [Microsoft.ManagedServices](#microsoftmanagedservices)
> - [Microsoft.Management](#microsoftmanagement)
> - [Microsoft.Maps](#microsoftmaps)
> - [Microsoft.Marketplace](#microsoftmarketplace)
> - [Microsoft.MarketplaceApps](#microsoftmarketplaceapps)
> - [Microsoft.MarketplaceNotifications](#microsoftmarketplacenotifications)
> - [Microsoft.MarketplaceOrdering](#microsoftmarketplaceordering)
> - [Microsoft.Media](#microsoftmedia)
> - [Microsoft.Migrate](#microsoftmigrate)
> - [Microsoft.MixedReality](#microsoftmixedreality)
> - [Microsoft.MobileNetwork](#microsoftmobilenetwork)
> - [Microsoft.Monitor](#microsoftmonitor)
> - [Microsoft.NetApp](#microsoftnetapp)
> - [Microsoft.NetworkFunction](#microsoftnetworkfunction)
> - [Microsoft.Network](#microsoftnetwork)
> - [Microsoft.Notebooks](#microsoftnotebooks)
> - [Microsoft.NotificationHubs](#microsoftnotificationhubs)
> - [Microsoft.ObjectStore](#microsoftobjectstore)
> - [Microsoft.OffAzure](#microsoftoffazure)
> - [Microsoft.OperationalInsights](#microsoftoperationalinsights)
> - [Microsoft.OperationsManagement](#microsoftoperationsmanagement)
> - [Microsoft.Peering](#microsoftpeering)
> - [Microsoft.PlayFab](#microsoftplayfab)
> - [Microsoft.PolicyInsights](#microsoftpolicyinsights)
> - [Microsoft.Portal](#microsoftportal)
> - [Microsoft.PowerBI](#microsoftpowerbi)
> - [Microsoft.PowerBIDedicated](#microsoftpowerbidedicated)
> - [Microsoft.PowerPlatform](#microsoftpowerplatform)
> - [Microsoft.ProjectBabylon](#microsoftprojectbabylon)
> - [Microsoft.ProviderHub](#microsoftproviderhub)
> - [Microsoft.Purview](#microsoftpurview)
> - [Microsoft.Quantum](#microsoftquantum)
> - [Microsoft.Quota](#microsoftquota)
> - [Microsoft.RecommendationsService](#microsoftrecommendationsservice)
> - [Microsoft.RecoveryServices](#microsoftrecoveryservices)
> - [Microsoft.RedHatOpenShift](#microsoftredhatopenshift)
> - [Microsoft.Relay](#microsoftrelay)
> - [Microsoft.ResourceConnector](#microsoftresourceconnector)
> - [Microsoft.ResourceGraph](#microsoftresourcegraph)
> - [Microsoft.ResourceHealth](#microsoftresourcehealth)
> - [Microsoft.Resources](#microsoftresources)
> - [Microsoft.SaaS](#microsoftsaas)
> - [Microsoft.Scom](#microsoftscom)
> - [Microsoft.ScVmm](#microsoftscvmm)
> - [Microsoft.Search](#microsoftsearch)
> - [Microsoft.Security](#microsoftsecurity)
> - [Microsoft.SecurityGraph](#microsoftsecuritygraph)
> - [Microsoft.SecurityInsights](#microsoftsecurityinsights)
> - [Microsoft.SerialConsole](#microsoftserialconsole)
> - [Microsoft.ServiceBus](#microsoftservicebus)
> - [Microsoft.ServiceFabric](#microsoftservicefabric)
> - [Microsoft.ServiceFabricMesh](#microsoftservicefabricmesh)
> - [Microsoft.ServiceLinker](#microsoftservicelinker)
> - [Microsoft.Services](#microsoftservices)
> - [Microsoft.SignalRService](#microsoftsignalrservice)
> - [Microsoft.Singularity](#microsoftsingularity)
> - [Microsoft.SoftwarePlan](#microsoftsoftwareplan)
> - [Microsoft.Solutions](#microsoftsolutions)
> - [Microsoft.SQL](#microsoftsql)
> - [Microsoft.SqlVirtualMachine](#microsoftsqlvirtualmachine)
> - [Microsoft.Storage](#microsoftstorage)
> - [Microsoft.StorageCache](#microsoftstoragecache)
> - [Microsoft.StorageReplication](#microsoftstoragereplication)
> - [Microsoft.StorageSync](#microsoftstoragesync)
> - [Microsoft.StorSimple](#microsoftstorsimple)
> - [Microsoft.StreamAnalytics](#microsoftstreamanalytics)
> - [Microsoft.Subscription](#microsoftsubscription)
> - [Microsoft.Synapse](#microsoftsynapse)
> - [Microsoft.TestBase](#microsofttestbase)
> - [Microsoft.TimeSeriesInsights](#microsofttimeseriesinsights)
> - [Microsoft.VideoIndexer](#microsoftvideoindexer)
> - [Microsoft.VirtualMachineImages](#microsoftvirtualmachineimages)
> - [Microsoft.VMware](#microsoftvmware)
> - [Microsoft.VMwareCloudSimple](#microsoftvmwarecloudsimple)
> - [Microsoft.VSOnline](#microsoftvsonline)
> - [Microsoft.Web](#microsoftweb)
> - [Microsoft.WindowsDefenderATP](#microsoftwindowsdefenderatp)
> - [Microsoft.WindowsESU](#microsoftwindowsesu)
> - [Microsoft.WindowsIoT](#microsoftwindowsiot)
> - [Microsoft.WorkloadBuilder](#microsoftworkloadbuilder)
> - [Microsoft.WorkloadMonitor](#microsoftworkloadmonitor)

## <a name="microsoftaad"></a>Microsoft.AAD

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminación del modo completo |
> | ------------- | ----------- |
> | DomainServices | Sí |
> | DomainServices/oucontainer | No |

## <a name="microsoftaddons"></a>Microsoft.Addons

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminación del modo completo |
> | ------------- | ----------- |
> | supportProviders | No |

## <a name="microsoftadhybridhealthservice"></a>Microsoft.ADHybridHealthService

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminación del modo completo |
> | ------------- | ----------- |
> | aadsupportcases | No |
> | addsservices | No |
> | agents | No |
> | anonymousapiusers | No |
> | configuración | No |
> | logs | No |
> | reports | No |
> | servicehealthmetrics | No |
> | services | No |

## <a name="microsoftadvisor"></a>Microsoft.Advisor

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminación del modo completo |
> | ------------- | ----------- |
> | advisorScore | No |
> | configuraciones | No |
> | generateRecommendations | No |
> | metadata | No |
> | de películas | No |
> | suppressions | No |

## <a name="microsoftagfoodplatform"></a>Microsoft.AgFoodPlatform

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminación del modo completo |
> | ------------- | ----------- |
> | farmBeats | Sí |
> | farmBeats/eventGridFilters | No |
> | farmBeats/extensions | No |
> | farmBeatsExtensionDefinitions | No |

## <a name="microsoftalertsmanagement"></a>Microsoft.AlertsManagement

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminación del modo completo |
> | ------------- | ----------- |
> | actionRules | Sí |
> | alerts | No |
> | alertsList | No |
> | alertsMetaData | No |
> | alertsSummary | No |
> | alertsSummaryList | No |
> | migrateFromSmartDetection | No |
> | resourceHealthAlertRules | Sí |
> | smartDetectorAlertRules | Sí |
> | smartGroups | No |

## <a name="microsoftanalysisservices"></a>Microsoft.AnalysisServices

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminación del modo completo |
> | ------------- | ----------- |
> | servers | Sí |

## <a name="microsoftanybuild"></a>Microsoft.AnyBuild

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminación del modo completo |
> | ------------- | ----------- |
> | clusters | No |

## <a name="microsoftapimanagement"></a>Microsoft.ApiManagement

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminación del modo completo |
> | ------------- | ----------- |
> | deletedServices | No |
> | getDomainOwnershipIdentifier | No |
> | reportFeedback | No |
> | service | Sí |
> | service/eventGridFilters | No |
> | validateServiceName | No |

## <a name="microsoftappassessment"></a>Microsoft.AppAssessment

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminación del modo completo |
> | ------------- | ----------- |
> | migrateProjects | No |
> | migrateProjects/assessments | No |
> | migrateProjects/assessments/assessedApplications | No |
> | migrateProjects/assessments/assessedApplications/machines | No |
> | migrateProjects/assessments/assessedMachines | No |
> | migrateProjects/assessments/assessedMachines/applications | No |
> | migrateProjects/assessments/machinesToAssess | No |
> | migrateProjects/sites | No |
> | migrateProjects/sites/applianceConfigurations | No |
> | migrateProjects/sites/machines | No |

## <a name="microsoftappconfiguration"></a>Microsoft.AppConfiguration

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminación del modo completo |
> | ------------- | ----------- |
> | configurationStores | Sí |
> | configurationStores/eventGridFilters | No |
> | configurationStores/keyValues | No |

## <a name="microsoftappplatform"></a>Microsoft.AppPlatform

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminación del modo completo |
> | ------------- | ----------- |
> | Spring | Sí |
> | Spring/apps | No |
> | Spring/apps/deployments | No |

## <a name="microsoftattestation"></a>Microsoft.Attestation

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminación del modo completo |
> | ------------- | ----------- |
> | attestationProviders | Sí |
> | defaultProviders | No |

## <a name="microsoftauthorization"></a>Microsoft.Authorization

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminación del modo completo |
> | ------------- | ----------- |
> | accessReviewScheduleDefinitions | No |
> | accessReviewScheduleSettings | No |
> | batchResourceCheckAccess | No |
> | classicAdministrators | No |
> | dataAliases | No |
> | dataPolicyManifests | No |
> | denyAssignments | No |
> | diagnosticSettings | No |
> | diagnosticSettingsCategories | No |
> | elevateAccess | No |
> | eligibleChildResources | No |
> | findOrphanRoleAssignments | No |
> | locks | No |
> | permisos | No |
> | policyAssignments | No |
> | policyDefinitions | No |
> | policyExemptions | No |
> | policySetDefinitions | No |
> | privateLinkAssociations | No |
> | providerOperations | No |
> | resourceManagementPrivateLinks | Sí |
> | roleAssignmentApprovals | No |
> | roleAssignments | No |
> | roleAssignmentScheduleInstances | No |
> | roleAssignmentScheduleRequests | No |
> | roleAssignmentSchedules | No |
> | roleAssignmentsUsageMetrics | No |
> | roleDefinitions | No |
> | roleEligibilityScheduleInstances | No |
> | roleEligibilityScheduleRequests | No |
> | roleEligibilitySchedules | No |
> | roleManagementPolicies | No |
> | roleManagementPolicyAssignments | No |

## <a name="microsoftautomanage"></a>Microsoft.Automanage

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminación del modo completo |
> | ------------- | ----------- |
> | accounts | Sí |
> | bestPractices | No |
> | bestPractices/versions | No |
> | configurationProfileAssignmentIntents | No |
> | configurationProfileAssignments | No |
> | configurationProfilePreferences | Sí |
> | configurationProfiles | Yes |
> | configurationProfiles/versions | Sí |

## <a name="microsoftautomation"></a>Microsoft.Automation

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminación del modo completo |
> | ------------- | ----------- |
> | automationAccounts | Sí |
> | automationAccounts/configurations | Sí |
> | automationAccounts/hybridRunbookWorkerGroups | No |
> | automationAccounts/hybridRunbookWorkerGroups/hybridRunbookWorkers | No |
> | automationAccounts/jobs | No |
> | automationAccounts / privateEndpointConnectionProxies | No |
> | automationAccounts / privateEndpointConnections | No |
> | automationAccounts / privateLinkResources | No |
> | automationAccounts/runbooks | Sí |
> | automationAccounts/softwareUpdateConfigurations | No |
> | automationAccounts/webhooks | No |

## <a name="microsoftavs"></a>Microsoft.AVS

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminación del modo completo |
> | ------------- | ----------- |
> | privateClouds | Sí |
> | privateClouds / addons | No |
> | privateClouds / authorizations | No |
> | privateClouds/cloudLinks | No |
> | privateClouds / clusters | No |
> | privateClouds/clusters/datastores | No |
> | privateClouds/clusters/placementPolicies | No |
> | privateClouds/clusters/virtualMachines | No |
> | privateClouds/globalReachConnections | No |
> | privateClouds / hcxEnterpriseSites | No |
> | privateClouds/scriptExecutions | No |
> | privateClouds/scriptPackages | No |
> | privateClouds/scriptPackages/scriptCmdlets | No |
> | privateClouds/workloadNetworks | No |
> | privateClouds/workloadNetworks/dhcpConfigurations | No |
> | privateClouds/workloadNetworks/dnsServices | No |
> | privateClouds/workloadNetworks/dnsZones | No |
> | privateClouds/workloadNetworks/gateways | No |
> | privateClouds/workloadNetworks/portMirroringProfiles | No |
> | privateClouds/workloadNetworks/publicIPs | No |
> | privateClouds/workloadNetworks/segments | No |
> | privateClouds/workloadNetworks/virtualMachines | No |
> | privateClouds/workloadNetworks/vmGroups | No |

## <a name="microsoftazuregeneva"></a>Microsoft.Azure.Geneva

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminación del modo completo |
> | ------------- | ----------- |
> | environments | No |
> | environments/accounts | No |
> | environments/accounts/namespaces | No |
> | environments/accounts/namespaces/configurations | No |

## <a name="microsoftazureactivedirectory"></a>Microsoft.AzureActiveDirectory

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminación del modo completo |
> | ------------- | ----------- |
> | b2cDirectories | Sí |
> | b2ctenants | No |
> | guestUsages | Sí |

## <a name="microsoftazurearcdata"></a>Microsoft.AzureArcData

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminación del modo completo |
> | ------------- | ----------- |
> | DataControllers | No |
> | DataWarehouseInstances | No |
> | PostgresInstances | No |
> | SqlManagedInstances | No |
> | SqlServerInstances | No |

## <a name="microsoftazurecis"></a>Microsoft.AzureCIS

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminación del modo completo |
> | ------------- | ----------- |
> | autopilotEnvironments | No |
> | dstsServiceAccounts | No |
> | dstsServiceClientIdentities | No |

## <a name="microsoftazuredata"></a>Microsoft.AzureData

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminación del modo completo |
> | ------------- | ----------- |
> | sqlServerRegistrations | Sí |
> | sqlServerRegistrations/sqlServers | No |

## <a name="microsoftazurepercept"></a>Microsoft.AzurePercept

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminación del modo completo |
> | ------------- | ----------- |
> | accounts | No |
> | accounts/devices | No |

## <a name="microsoftazuresphere"></a>Microsoft.AzureSphere

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminación del modo completo |
> | ------------- | ----------- |
> | catalogs | No |
> | catalogs/certificates | No |
> | catalogs/deployments | No |
> | catalogs/devices | No |
> | catalogs/images | No |
> | catalogs/products | No |
> | catalogs/products/devicegroups | No |

## <a name="microsoftazurestack"></a>Microsoft.AzureStack

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminación del modo completo |
> | ------------- | ----------- |
> | cloudManifestFiles | No |
> | linkedSubscriptions | Sí |
> | registrations | Sí |
> | registrations/customerSubscriptions | No |
> | registrations/products | No |

## <a name="microsoftazurestackhci"></a>Microsoft.AzureStackHCI

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminación del modo completo |
> | ------------- | ----------- |
> | clusters | No |
> | clusters/arcsettings | No |
> | clusters/arcsettings/extensions | No |
> | galleryImages | No |
> | networkInterfaces | No |
> | virtualHardDisks | No |
> | virtualmachines | No |
> | virtualmachines/extensions | No |
> | virtualmachines/hybrididentitymetadata | No |
> | virtualNetworks | No |

## <a name="microsoftbackupsolutions"></a>Microsoft.BackupSolutions

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminación del modo completo |
> | ------------- | ----------- |
> | VMwareApplications | Sí |

## <a name="microsoftbaremetalinfrastructure"></a>Microsoft.BareMetalInfrastructure

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminación del modo completo |
> | ------------- | ----------- |
> | bareMetalInstances | Sí |

## <a name="microsoftbatch"></a>Microsoft.Batch

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminación del modo completo |
> | ------------- | ----------- |
> | batchAccounts | Sí |
> | batchAccounts/certificates | No |
> | batchAccounts/pools | No |

## <a name="microsoftbilling"></a>Microsoft.Billing

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminación del modo completo |
> | ------------- | ----------- |
> | billingAccounts | No |
> | billingAccounts/agreements | No |
> | billingAccounts/appliedReservationOrders | No |
> | billingAccounts/billingPermissions | No |
> | billingAccounts/billingProfiles | No |
> | billingAccounts/billingProfiles/billingPermissions | No |
> | billingAccounts/billingProfiles/billingRoleAssignments | No |
> | billingAccounts/billingProfiles/billingRoleDefinitions | No |
> | billingAccounts/billingProfiles/billingSubscriptions | No |
> | billingAccounts/billingProfiles/createBillingRoleAssignment | No |
> | billingAccounts/billingProfiles/customers | No |
> | billingAccounts / billingProfiles / instructions | No |
> | billingAccounts/billingProfiles/invoices | No |
> | billingAccounts/billingProfiles/invoices/pricesheet | No |
> | billingAccounts/billingProfiles/invoices/transactions | No |
> | billingAccounts/billingProfiles/invoiceSections | No |
> | billingAccounts/billingProfiles/invoiceSections/billingPermissions | No |
> | billingAccounts/billingProfiles/invoiceSections/billingRoleAssignments | No |
> | billingAccounts/billingProfiles/invoiceSections/billingRoleDefinitions | No |
> | billingAccounts/billingProfiles/invoiceSections/billingSubscriptions | No |
> | billingAccounts/billingProfiles/invoiceSections/createBillingRoleAssignment | No |
> | billingAccounts/billingProfiles/invoiceSections/initiateTransfer | No |
> | billingAccounts/billingProfiles/invoiceSections/products | No |
> | billingAccounts/billingProfiles/invoiceSections/products/transfer | No |
> | billingAccounts/billingProfiles/invoiceSections/products/updateAutoRenew | No |
> | billingAccounts/billingProfiles/invoiceSections/transactions | No |
> | billingAccounts/billingProfiles/invoiceSections/transfers | No |
> | billingAccounts/billingProfiles/invoiceSections/validateDeleteInvoiceSectionEligibility | No |
> | billingAccounts/BillingProfiles/patchOperations | No |
> | billingAccounts/billingProfiles/paymentMethodLinks | No |
> | billingAccounts/billingProfiles/paymentMethods | No |
> | billingAccounts/billingProfiles/policies | No |
> | billingAccounts/billingProfiles/pricesheet | No |
> | billingAccounts/billingProfiles/pricesheetDownloadOperations | No |
> | billingAccounts/billingProfiles/products | No |
> | billingAccounts / billingProfiles / reservations | No |
> | billingAccounts/billingProfiles/transactions | No |
> | billingAccounts/billingProfiles/validateDeleteBillingProfileEligibility | No |
> | billingAccounts/billingProfiles/validateDetachPaymentMethodEligibility | No |
> | billingAccounts/billingRoleAssignments | No |
> | billingAccounts/billingRoleDefinitions | No |
> | billingAccounts/billingSubscriptions | No |
> | billingAccounts/billingSubscriptions/elevateRole | No |
> | billingAccounts/billingSubscriptions/invoices | No |
> | billingAccounts/createBillingRoleAssignment | No |
> | billingAccounts/createInvoiceSectionOperations | No |
> | billingAccounts/customers | No |
> | billingAccounts/customers/billingPermissions | No |
> | billingAccounts/customers/billingSubscriptions | No |
> | billingAccounts/customers/initiateTransfer | No |
> | billingAccounts/customers/policies | No |
> | billingAccounts/customers/products | No |
> | billingAccounts/customers/transactions | No |
> | billingAccounts/customers/transfers | No |
> | billingAccounts/customers/transferSupportedAccounts | No |
> | billingAccounts/departments | No |
> | billingAccounts / departments / billingPermissions | No |
> | billingAccounts / departments / billingRoleAssignments | No |
> | billingAccounts / departments / billingRoleDefinitions | No |
> | billingAccounts/departments/billingSubscriptions | No |
> | billingAccounts/departments/enrollmentAccounts | No |
> | billingAccounts/enrollmentAccounts | No |
> | billingAccounts / enrollmentAccounts / billingPermissions | No |
> | billingAccounts / enrollmentAccounts / billingRoleAssignments | No |
> | billingAccounts / enrollmentAccounts / billingRoleDefinitions | No |
> | billingAccounts/enrollmentAccounts/billingSubscriptions | No |
> | billingAccounts/invoices | No |
> | billingAccounts / invoices / transactions | No |
> | billingAccounts/invoices/transactionSummary | No |
> | billingAccounts/invoiceSections | No |
> | billingAccounts/invoiceSections/billingSubscriptionMoveOperations | No |
> | billingAccounts/invoiceSections/billingSubscriptions | No |
> | billingAccounts/invoiceSections/billingSubscriptions/transfer | No |
> | billingAccounts/invoiceSections/elevate | No |
> | billingAccounts/invoiceSections/initiateTransfer | No |
> | billingAccounts/invoiceSections/patchOperations | No |
> | billingAccounts/invoiceSections/productMoveOperations | No |
> | billingAccounts/invoiceSections/products | No |
> | billingAccounts/invoiceSections/products/transfer | No |
> | billingAccounts/invoiceSections/products/updateAutoRenew | No |
> | billingAccounts/invoiceSections/transactions | No |
> | billingAccounts/invoiceSections/transfers | No |
> | billingAccounts/lineOfCredit | No |
> | billingAccounts/patchOperations | No |
> | billingAccounts/payableOverage | No |
> | billingAccounts/paymentMethods | No |
> | billingAccounts/payNow | No |
> | billingAccounts/policies | No |
> | billingAccounts/products | No |
> | billingAccounts/promotionalCredits | No |
> | billingAccounts / reservations | No |
> | billingAccounts/savingsPlanOrders | No |
> | billingAccounts/savingsPlanOrders/savingsPlans | No |
> | billingAccounts/savingsPlans | No |
> | billingAccounts/transactions | No |
> | billingPeriods | No |
> | billingPermissions | No |
> | billingProperty | No |
> | billingRoleAssignments | No |
> | billingRoleDefinitions | No |
> | createBillingRoleAssignment | No |
> | departments | No |
> | enrollmentAccounts | No |
> | invoices | No |
> | paymentMethods | No |
> | promotionalCredits | No |
> | promociones | No |
> | transfers | No |
> | transfers/acceptTransfer | No |
> | transfers/declineTransfer | No |
> | transfers/operationStatus | No |
> | transfers/validateTransfer | No |
> | validateAddress | No |

## <a name="microsoftbillingbenefits"></a>Microsoft.BillingBenefits

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminación del modo completo |
> | ------------- | ----------- |
> | savingsPlanOrderAliases | No |
> | validate | No |

## <a name="microsoftblockchain"></a>Microsoft.Blockchain

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminación del modo completo |
> | ------------- | ----------- |
> | blockchainMembers | Sí |
> | cordaMembers | Sí |
> | watchers | Sí |

## <a name="microsoftblockchaintokens"></a>Microsoft.BlockchainTokens

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminación del modo completo |
> | ------------- | ----------- |
> | TokenServices | Sí |
> | TokenServices/BlockchainNetworks | No |
> | TokenServices/Groups | No |
> | TokenServices/Groups/Accounts | No |
> | TokenServices/TokenTemplates | No |

## <a name="microsoftblueprint"></a>Microsoft.Blueprint

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminación del modo completo |
> | ------------- | ----------- |
> | blueprintAssignments | No |
> | blueprintAssignments/assignmentOperations | No |
> | blueprintAssignments/operations | No |
> | blueprints | No |
> | blueprints/artifacts | No |
> | blueprints/versions | No |
> | blueprints/versions/artifacts | No |

## <a name="microsoftbotservice"></a>Microsoft.BotService

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminación del modo completo |
> | ------------- | ----------- |
> | botServices | Sí |
> | botServices/channels | No |
> | botServices/connections | No |
> | botServices/privateEndpointConnectionProxies | No |
> | botServices/privateEndpointConnections | No |
> | botServices/privateLinkResources | No |
> | hostSettings | No |
> | languages | No |
> | plantillas | No |

## <a name="microsoftcache"></a>Microsoft.Cache

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminación del modo completo |
> | ------------- | ----------- |
> | Redis | Sí |
> | Redis / EventGridFilters | No |
> | Redis / privateEndpointConnectionProxies | No |
> | Redis / privateEndpointConnectionProxies / validate | No |
> | Redis / privateEndpointConnections | No |
> | Redis / privateLinkResources | No |
> | redisEnterprise | Sí |
> | redisEnterprise/databases | No |
> | RedisEnterprise / privateEndpointConnectionProxies | No |
> | RedisEnterprise / privateEndpointConnectionProxies / validate | No |
> | RedisEnterprise / privateEndpointConnections | No |
> | RedisEnterprise / privateLinkResources | No |

## <a name="microsoftcapacity"></a>Microsoft.Capacity

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminación del modo completo |
> | ------------- | ----------- |
> | appliedReservations | No |
> | autoQuotaIncrease | No |
> | calculateExchange | No |
> | calculatePrice | No |
> | calculatePurchasePrice | No |
> | catalogs | No |
> | commercialReservationOrders | No |
> | cambio | No |
> | ownReservations | No |
> | placePurchaseOrder | No |
> | reservationOrders | No |
> | reservationOrders/calculateRefund | No |
> | reservationOrders/merge | No |
> | reservationOrders/reservations | No |
> | reservationOrders/reservations/revisions | No |
> | reservationOrders/return | No |
> | reservationOrders/split | No |
> | reservationOrders/swap | No |
> | reservations | No |
> | resourceProviders | No |
> | resources | No |
> | validateReservationOrder | No |

## <a name="microsoftcascade"></a>Microsoft.Cascade

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminación del modo completo |
> | ------------- | ----------- |
> | sites | No |

## <a name="microsoftcdn"></a>Microsoft.Cdn

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminación del modo completo |
> | ------------- | ----------- |
> | CdnWebApplicationFirewallManagedRuleSets | No |
> | CdnWebApplicationFirewallPolicies | Sí |
> | edgenodes | No |
> | profiles | Sí |
> | profiles/afdendpoints | Sí |
> | profiles/afdendpoints/routes | No |
> | profiles/customdomains | No |
> | profiles/endpoints | Sí |
> | profiles/endpoints/customdomains | No |
> | profiles/endpoints/origingroups | No |
> | profiles/endpoints/origins | No |
> | profiles/origingroups | No |
> | profiles/origingroups/origins | No |
> | profiles/rulesets | No |
> | profiles/rulesets/rules | No |
> | profiles/secrets | No |
> | profiles/securitypolicies | No |
> | validateProbe | No |

## <a name="microsoftcertificateregistration"></a>Microsoft.CertificateRegistration

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminación del modo completo |
> | ------------- | ----------- |
> | certificateOrders | Sí |
> | certificateOrders/certificates | No |
> | validateCertificateRegistrationInformation | No |

## <a name="microsoftchangeanalysis"></a>Microsoft.ChangeAnalysis

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminación del modo completo |
> | ------------- | ----------- |
> | cambios | No |
> | changeSnapshots | No |
> | computeChanges | No |
> | perfile | No |
> | resourceChanges | No |

## <a name="microsoftclassiccompute"></a>Microsoft.ClassicCompute

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminación del modo completo |
> | ------------- | ----------- |
> | capabilities | No |
> | domainNames | Sí |
> | domainNames/capabilities | No |
> | domainNames/internalLoadBalancers | No |
> | domainNames/serviceCertificates | No |
> | domainNames/slots | No |
> | domainNames/slots/roles | No |
> | domainNames/slots/roles/metricDefinitions | No |
> | domainNames/slots/roles/metrics | No |
> | moveSubscriptionResources | No |
> | operatingSystemFamilies | No |
> | operatingSystems | No |
> | quotas | No |
> | resourceTypes | No |
> | validateSubscriptionMoveAvailability | No |
> | virtualMachines | Sí |
> | virtualMachines/diagnosticSettings | No |
> | virtualMachines/metricDefinitions | No |
> | virtualMachines/metrics | No |

## <a name="microsoftclassicinfrastructuremigrate"></a>Microsoft.ClassicInfrastructureMigrate

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminación del modo completo |
> | ------------- | ----------- |
> | classicInfrastructureResources | No |

## <a name="microsoftclassicnetwork"></a>Microsoft.ClassicNetwork

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminación del modo completo |
> | ------------- | ----------- |
> | capabilities | No |
> | expressRouteCrossConnections | No |
> | expressRouteCrossConnections/peerings | No |
> | gatewaySupportedDevices | No |
> | networkSecurityGroups | Sí |
> | quotas | No |
> | reservedIps | Sí |
> | virtualNetworks | Sí |
> | virtualNetworks/remoteVirtualNetworkPeeringProxies | No |
> | virtualNetworks/virtualNetworkPeerings | No |

## <a name="microsoftclassicstorage"></a>Microsoft.ClassicStorage

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminación del modo completo |
> | ------------- | ----------- |
> | capabilities | No |
> | disks | No |
> | images | No |
> | osImages | No |
> | osPlatformImages | No |
> | publicImages | No |
> | quotas | No |
> | storageAccounts | Sí |
> | storageAccounts/blobServices | No |
> | storageAccounts/fileServices | No |
> | storageAccounts/metricDefinitions | No |
> | storageAccounts/metrics | No |
> | storageAccounts/queueServices | No |
> | storageAccounts/services | No |
> | storageAccounts/services/diagnosticSettings | No |
> | storageAccounts/services/metricDefinitions | No |
> | storageAccounts/services/metrics | No |
> | storageAccounts/tableServices | No |
> | storageAccounts/vmImages | No |
> | vmImages | No |

## <a name="microsoftclusterstor"></a>Microsoft.ClusterStor

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminación del modo completo |
> | ------------- | ----------- |
> | nodes | Sí |

## <a name="microsoftcodesigning"></a>Microsoft.CodeSigning

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminación del modo completo |
> | ------------- | ----------- |
> | codeSigningAccounts | No |
> | codeSigningAccounts/certificateProfiles | No |

## <a name="microsoftcodespaces"></a>Microsoft.Codespaces

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminación del modo completo |
> | ------------- | ----------- |
> | plans | Sí |
> | registeredSubscriptions | No |

## <a name="microsoftcognitiveservices"></a>Microsoft.CognitiveServices

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminación del modo completo |
> | ------------- | ----------- |
> | accounts | Sí |
> | accounts/privateEndpointConnectionProxies | No |
> | accounts/privateEndpointConnections | No |
> | accounts/privateLinkResources | No |
> | deletedAccounts | No |

## <a name="microsoftcompute"></a>Microsoft.Compute

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminación del modo completo |
> | ------------- | ----------- |
> | availabilitySets | Sí |
> | cloudServices | Sí |
> | cloudServices / networkInterfaces | No |
> | cloudServices / publicIPAddresses | No |
> | cloudServices / roleInstances | No |
> | cloudServices / roleInstances / networkInterfaces | No |
> | cloudServices / roles | No |
> | diskAccesses | Sí |
> | diskEncryptionSets | Sí |
> | disks | Sí |
> | galleries | Sí |
> | galleries/applications | No |
> | galleries/applications/versions | No |
> | galleries/images | Sí |
> | galleries/images/versions | Sí |
> | hostGroups | Sí |
> | hostGroups/hosts | Sí |
> | images | Sí |
> | proximityPlacementGroups | Sí |
> | restorePointCollections | Sí |
> | restorePointCollections/restorePoints | No |
> | sharedVMExtensions | Sí |
> | sharedVMExtensions/versions | No |
> | sharedVMImages | Sí |
> | sharedVMImages/versions | No |
> | snapshots | Sí |
> | sshPublicKeys | Sí |
> | virtualMachines | Sí |
> | virtualMachines/extensions | Sí |
> | virtualMachines/metricDefinitions | No |
> | virtualMachines / runCommands | Sí |
> | virtualMachineScaleSets | Sí |
> | virtualMachineScaleSets/extensions | No |
> | virtualMachineScaleSets/networkInterfaces | No |
> | virtualMachineScaleSets/publicIPAddresses | No |
> | virtualMachineScaleSets/virtualMachines | No |
> | virtualMachineScaleSets/virtualMachines/networkInterfaces | No |

## <a name="microsoftcommerce"></a>Microsoft.Commerce

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminación del modo completo |
> | ------------- | ----------- |
> | RateCard | No |
> | UsageAggregates | No |

## <a name="microsoftconfidentialledger"></a>Microsoft.ConfidentialLedger

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminación del modo completo |
> | ------------- | ----------- |
> | Ledgers | No |

## <a name="microsoftconnectedcache"></a>Microsoft.ConnectedCache

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminación del modo completo |
> | ------------- | ----------- |
> | CacheNodes | No |

## <a name="microsoftconnectedvehicle"></a>Microsoft.ConnectedVehicle

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminación del modo completo |
> | ------------- | ----------- |
> | platformAccounts | No |
> | registeredSubscriptions | No |

## <a name="microsoftconnectedvmwarevsphere"></a>Microsoft.ConnectedVMwarevSphere

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminación del modo completo |
> | ------------- | ----------- |
> | Clústeres | No |
> | Almacenes de datos | No |
> | Hosts | No |
> | ResourcePools | No |
> | VCenters | No |
> | VCenters/InventoryItems | No |
> | VirtualMachines | No |
> | VirtualMachines/Extensions | Sí |
> | VirtualMachines/GuestAgents | No |
> | VirtualMachines/HybridIdentityMetadata | No |
> | VirtualMachineTemplates | No |
> | VirtualNetworks | No |

## <a name="microsoftconsumption"></a>Microsoft.Consumption

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminación del modo completo |
> | ------------- | ----------- |
> | AggregatedCost | No |
> | Saldos | No |
> | Presupuestos | No |
> | Charges | No |
> | CostTags | No |
> | credits | No |
> | events | No |
> | Previsiones | No |
> | lots | No |
> | Catálogos de soluciones | No |
> | Pricesheets | No |
> | products | No |
> | ReservationDetails | No |
> | ReservationRecommendationDetails | No |
> | ReservationRecommendations | No |
> | ReservationSummaries | No |
> | ReservationTransactions | No |
> | Etiquetas | No |
> | tenants | No |
> | Términos | No |
> | UsageDetails | No |

## <a name="microsoftcontainerinstance"></a>Microsoft.ContainerInstance

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminación del modo completo |
> | ------------- | ----------- |
> | containerGroups | Sí |
> | serviceAssociationLinks | No |

## <a name="microsoftcontainerregistry"></a>Microsoft.ContainerRegistry

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminación del modo completo |
> | ------------- | ----------- |
> | registries | Sí |
> | registries/agentPools | Sí |
> | registries/builds | No |
> | registries/builds/cancel | No |
> | registries/builds/getLogLink | No |
> | registries/buildTasks | Sí |
> | registries/buildTasks/steps | No |
> | registries/connectedRegistries | No |
> | registries/connectedRegistries/deactivate | No |
> | registries/eventGridFilters | No |
> | registries / exportPipelines | No |
> | registries/generateCredentials | No |
> | registries/getBuildSourceUploadUrl | No |
> | registries/GetCredentials | No |
> | registries/importImage | No |
> | registries / importPipelines | No |
> | registries / pipelineRuns | No |
> | registries/privateEndpointConnectionProxies | No |
> | registries/privateEndpointConnectionProxies/validate | No |
> | registries / privateEndpointConnections | No |
> | registries/privateLinkResources | No |
> | registries/queueBuild | No |
> | registries/regenerateCredential | No |
> | registries/regenerateCredentials | No |
> | registries/replications | Sí |
> | registries/runs | No |
> | registries/runs/cancel | No |
> | registries/scheduleRun | No |
> | registries/scopeMaps | No |
> | registries/taskRuns | No |
> | registries/tasks | Sí |
> | registries/tokens | No |
> | registries/updatePolicies | No |
> | registries/webhooks | Sí |
> | registries/webhooks/getCallbackConfig | No |
> | registries/webhooks/ping | No |

## <a name="microsoftcontainerservice"></a>Microsoft.ContainerService

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminación del modo completo |
> | ------------- | ----------- |
> | containerServices | Sí |
> | managedClusters | Sí |
> | ManagedClusters/eventGridFilters | No |
> | openShiftManagedClusters | Sí |
> | snapshots | Sí |

## <a name="microsoftcostmanagement"></a>Microsoft.CostManagement

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminación del modo completo |
> | ------------- | ----------- |
> | Alertas | No |
> | BenefitUtilizationSummaries | No |
> | BillingAccounts | No |
> | Presupuestos | No |
> | calculatePrice | No |
> | CloudConnectors | No |
> | Conectores | Sí |
> | costAllocationRules | No |
> | Departments | No |
> | Dimensions | No |
> | EnrollmentAccounts | No |
> | Exports | No |
> | ExternalBillingAccounts | No |
> | ExternalBillingAccounts/Alerts | No |
> | ExternalBillingAccounts/Dimensions | No |
> | ExternalBillingAccounts/Forecast | No |
> | ExternalBillingAccounts/Query | No |
> | ExternalSubscriptions | No |
> | ExternalSubscriptions/Alerts | No |
> | ExternalSubscriptions/Dimensions | No |
> | ExternalSubscriptions/Forecast | No |
> | ExternalSubscriptions/Query | No |
> | fetchPrices | No |
> | Forecast | No |
> | GenerateDetailedCostReport | No |
> | GenerateReservationDetailsReport | No |
> | Información detallada | No |
> | Consultar | No |
> | registro | No |
> | Reportconfigs | No |
> | Informes | No |
> | ScheduledActions | No |
> | Configuración | No |
> | showbackRules | No |
> | Vistas | No |

## <a name="microsoftcustomerlockbox"></a>Microsoft.CustomerLockbox

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminación del modo completo |
> | ------------- | ----------- |
> | DisableLockbox | No |
> | EnableLockbox | No |
> | Solicitudes | No |
> | TenantOptedIn | No |

## <a name="microsoftcustomproviders"></a>Microsoft.CustomProviders

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminación del modo completo |
> | ------------- | ----------- |
> | associations | No |
> | resourceProviders | Sí |

## <a name="microsoftd365customerinsights"></a>Microsoft.D365CustomerInsights

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminación del modo completo |
> | ------------- | ----------- |
> | instances | Sí |

## <a name="microsoftdashboard"></a>Microsoft.Dashboard

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminación del modo completo |
> | ------------- | ----------- |
> | grafana | No |

## <a name="microsoftdatabox"></a>Microsoft.DataBox

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminación del modo completo |
> | ------------- | ----------- |
> | jobs | Sí |

## <a name="microsoftdataboxedge"></a>Microsoft.DataBoxEdge

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminación del modo completo |
> | ------------- | ----------- |
> | DataBoxEdgeDevices | Sí |

## <a name="microsoftdatabricks"></a>Microsoft.Databricks

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminación del modo completo |
> | ------------- | ----------- |
> | workspaces | Sí |
> | workspaces / dbWorkspaces | No |
> | workspaces/virtualNetworkPeerings | No |

## <a name="microsoftdatacatalog"></a>Microsoft.DataCatalog

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminación del modo completo |
> | ------------- | ----------- |
> | catalogs | Sí |

## <a name="microsoftdatafactory"></a>Microsoft.DataFactory

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminación del modo completo |
> | ------------- | ----------- |
> | dataFactories | Sí |
> | dataFactories/diagnosticSettings | No |
> | dataFactories/metricDefinitions | No |
> | dataFactorySchema | No |
> | factories | Sí |
> | factories/integrationRuntimes | No |

## <a name="microsoftdatalakeanalytics"></a>Microsoft.DataLakeAnalytics

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminación del modo completo |
> | ------------- | ----------- |
> | accounts | Sí |
> | accounts/dataLakeStoreAccounts | No |
> | accounts/storageAccounts | No |
> | accounts/storageAccounts/containers | No |
> | accounts/transferAnalyticsUnits | No |

## <a name="microsoftdatalakestore"></a>Microsoft.DataLakeStore

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminación del modo completo |
> | ------------- | ----------- |
> | accounts | Sí |
> | accounts/eventGridFilters | No |
> | accounts/firewallRules | No |

## <a name="microsoftdatamigration"></a>Microsoft.DataMigration

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminación del modo completo |
> | ------------- | ----------- |
> | DatabaseMigrations | No |
> | services | Sí |
> | services/projects | Sí |
> | SqlMigrationServices | Sí |

## <a name="microsoftdataprotection"></a>Microsoft.DataProtection

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminación del modo completo |
> | ------------- | ----------- |
> | BackupVaults | Sí |
> | ResourceGuards | Sí |

## <a name="microsoftdatashare"></a>Microsoft.DataShare

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminación del modo completo |
> | ------------- | ----------- |
> | accounts | Sí |
> | accounts/shares | No |
> | accounts/shares/datasets | No |
> | accounts/shares/invitations | No |
> | accounts/shares/providersharesubscriptions | No |
> | accounts/shares/synchronizationSettings | No |
> | accounts/sharesubscriptions | No |
> | accounts/sharesubscriptions/consumerSourceDataSets | No |
> | accounts/sharesubscriptions/datasetmappings | No |
> | accounts/sharesubscriptions/triggers | No |

## <a name="microsoftdbformariadb"></a>Microsoft.DBforMariaDB

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminación del modo completo |
> | ------------- | ----------- |
> | servers | Sí |
> | servers/advisors | No |
> | servers/keys | No |
> | servers/privateEndpointConnectionProxies | No |
> | servers/privateEndpointConnections | No |
> | servers/privateLinkResources | No |
> | servers/queryTexts | No |
> | servers/recoverableServers | No |
> | servers/resetQueryPerformanceInsightData | No |
> | servers / start | No |
> | servers / stop | No |
> | servers/topQueryStatistics | No |
> | servers/virtualNetworkRules | No |
> | servers/waitStatistics | No |

## <a name="microsoftdbformysql"></a>Microsoft.DBforMySQL

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminación del modo completo |
> | ------------- | ----------- |
> | flexibleServers | Sí |
> | getPrivateDnsZoneSuffix | No |
> | servers | Sí |
> | servers/advisors | No |
> | servers/keys | No |
> | servers/privateEndpointConnectionProxies | No |
> | servers/privateEndpointConnections | No |
> | servers/privateLinkResources | No |
> | servers/queryTexts | No |
> | servers/recoverableServers | No |
> | servers/resetQueryPerformanceInsightData | No |
> | servers / start | No |
> | servers / stop | No |
> | servers/topQueryStatistics | No |
> | servers / upgrade | No |
> | servers/virtualNetworkRules | No |
> | servers/waitStatistics | No |

## <a name="microsoftdbforpostgresql"></a>Microsoft.DBforPostgreSQL

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminación del modo completo |
> | ------------- | ----------- |
> | flexibleServers | Sí |
> | getPrivateDnsZoneSuffix | No |
> | serverGroups | Sí |
> | serverGroupsv2 | Sí |
> | servers | Sí |
> | servers/advisors | No |
> | servers/keys | No |
> | servers/privateEndpointConnectionProxies | No |
> | servers/privateEndpointConnections | No |
> | servers/privateLinkResources | No |
> | servers/queryTexts | No |
> | servers/recoverableServers | No |
> | servers/resetQueryPerformanceInsightData | No |
> | servers/topQueryStatistics | No |
> | servers/virtualNetworkRules | No |
> | servers/waitStatistics | No |
> | serversv2 | Sí |

## <a name="microsoftdelegatednetwork"></a>Microsoft.DelegatedNetwork

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminación del modo completo |
> | ------------- | ----------- |
> | controlador | Yes |
> | delegatedSubnets | Yes |
> | orchestrators | Sí |

## <a name="microsoftdeploymentmanager"></a>Microsoft.DeploymentManager

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminación del modo completo |
> | ------------- | ----------- |
> | artifactSources | Sí |
> | rollouts | Sí |
> | serviceTopologies | Sí |
> | serviceTopologies/services | Sí |
> | serviceTopologies/services/serviceUnits | Sí |
> | steps | Sí |

## <a name="microsoftdesktopvirtualization"></a>Microsoft.DesktopVirtualization

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminación del modo completo |
> | ------------- | ----------- |
> | applicationgroups | Sí |
> | applicationgroups/applications | No |
> | applicationgroups/desktops | No |
> | applicationgroups/startmenuitems | No |
> | hostpools | Sí |
> | hostpools / msixpackages | No |
> | hostpools/sessionhosts | No |
> | hostpools/sessionhosts/usersessions | No |
> | hostpools/usersessions | No |
> | scalingplans | Sí |
> | workspaces | Sí |

## <a name="microsoftdevices"></a>Microsoft.Devices

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminación del modo completo |
> | ------------- | ----------- |
> | ElasticPools | Sí |
> | ElasticPools/IotHubTenants | Sí |
> | ElasticPools / IotHubTenants / securitySettings | No |
> | IotHubs | Sí |
> | IotHubs/eventGridFilters | No |
> | IotHubs/failover | No |
> | IotHubs / securitySettings | No |
> | ProvisioningServices | Sí |
> | usages | No |

## <a name="microsoftdeviceupdate"></a>Microsoft.DeviceUpdate

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminación del modo completo |
> | ------------- | ----------- |
> | accounts | No |
> | accounts/instances | No |
> | registeredSubscriptions | No |

## <a name="microsoftdevops"></a>Microsoft.DevOps

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminación del modo completo |
> | ------------- | ----------- |
> | pipelines | Sí |

## <a name="microsoftdevspaces"></a>Microsoft.DevSpaces

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminación del modo completo |
> | ------------- | ----------- |
> | controllers | Sí |

## <a name="microsoftdevtestlab"></a>Microsoft.DevTestLab

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminación del modo completo |
> | ------------- | ----------- |
> | labcenters | Sí |
> | labs | Sí |
> | labs/environments | Sí |
> | labs/serviceRunners | Sí |
> | labs/virtualMachines | Sí |
> | schedules | Sí |

## <a name="microsoftdiagnostics"></a>Microsoft.Diagnostics

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminación del modo completo |
> | ------------- | ----------- |
> | AzureKB | No |
> | InsightDiagnostics | No |
> | solutions | No |

## <a name="microsoftdigitaltwins"></a>Microsoft.DigitalTwins

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminación del modo completo |
> | ------------- | ----------- |
> | digitalTwinsInstances | Sí |
> | digitalTwinsInstances / endpoints | No |
> | digitalTwinsInstances/timeSeriesDatabaseConnections | No |

## <a name="microsoftdocumentdb"></a>Microsoft.DocumentDB

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminación del modo completo |
> | ------------- | ----------- |
> | cassandraClusters | Sí |
> | databaseAccountNames | No |
> | databaseAccounts | Sí |
> | restorableDatabaseAccounts | No |

## <a name="microsoftdomainregistration"></a>Microsoft.DomainRegistration

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminación del modo completo |
> | ------------- | ----------- |
> | domains | Sí |
> | domains/domainOwnershipIdentifiers | No |
> | generateSsoRequest | No |
> | topLevelDomains | No |
> | validateDomainRegistrationInformation | No |

## <a name="microsoftdynamicslcs"></a>Microsoft.DynamicsLcs

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminación del modo completo |
> | ------------- | ----------- |
> | lcsprojects | No |
> | lcsprojects/clouddeployments | No |
> | lcsprojects/connectors | No |

## <a name="microsoftedgeorder"></a>Microsoft.EdgeOrder

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminación del modo completo |
> | ------------- | ----------- |
> | direcciones | Sí |
> | orderItems | Sí |
> | pedidos | No |
> | productFamiliesMetadata | No |

## <a name="microsoftenterpriseknowledgegraph"></a>Microsoft.EnterpriseKnowledgeGraph

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminación del modo completo |
> | ------------- | ----------- |
> | services | Sí |

## <a name="microsofteventgrid"></a>Microsoft.EventGrid

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminación del modo completo |
> | ------------- | ----------- |
> | domains | Sí |
> | domains/topics | No |
> | eventSubscriptions | No |
> | extensionTopics | No |
> | partnerNamespaces | Sí |
> | partnerNamespaces / eventChannels | No |
> | partnerRegistrations | Sí |
> | partnerTopics | Sí |
> | partnerTopics / eventSubscriptions | No |
> | systemTopics | Sí |
> | systemTopics / eventSubscriptions | No |
> | topics | Sí |
> | topicTypes | No |

## <a name="microsofteventhub"></a>Microsoft.EventHub

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminación del modo completo |
> | ------------- | ----------- |
> | clusters | Sí |
> | espacios de nombres | Sí |
> | namespaces/authorizationrules | No |
> | namespaces/disasterrecoveryconfigs | No |
> | namespaces/eventhubs | No |
> | namespaces/eventhubs/authorizationrules | No |
> | namespaces/eventhubs/consumergroups | No |
> | namespaces/networkrulesets | No |
> | namespaces / privateEndpointConnections | No |

## <a name="microsoftexperimentation"></a>Microsoft.Experimentation

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminación del modo completo |
> | ------------- | ----------- |
> | experimentWorkspaces | Sí |

## <a name="microsoftfalcon"></a>Microsoft.Falcon

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminación del modo completo |
> | ------------- | ----------- |
> | espacios de nombres | Sí |

## <a name="microsoftfeatures"></a>Microsoft.Features

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminación del modo completo |
> | ------------- | ----------- |
> | featureConfigurations | No |
> | featureProviderNamespaces | No |
> | featureProviders | No |
> | features | No |
> | providers | No |
> | subscriptionFeatureRegistrations | No |

## <a name="microsoftfidalgo"></a>Microsoft.Fidalgo

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminación del modo completo |
> | ------------- | ----------- |
> | devcenters | No |
> | devcenters/catalogs | No |
> | devcenters/catalogs/items | No |
> | devcenters/environmentTypes | No |
> | devcenters/mappings | No |
> | machinedefinitions | No |
> | networksettings | No |
> | projects | No |
> | projects/catalogItems | No |
> | projects/environments | No |
> | projects/environments/deployments | No |
> | projects/environmentTypes | No |
> | projects/pools | No |

## <a name="microsoftfluidrelay"></a>Microsoft.FluidRelay

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminación del modo completo |
> | ------------- | ----------- |
> | fluidRelayServers | No |
> | fluidRelayServers/fluidRelayContainers | No |

## <a name="microsoftgallery"></a>Microsoft.Gallery

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminación del modo completo |
> | ------------- | ----------- |
> | enroll | No |
> | galleryitems | No |
> | generateartifactaccessuri | No |
> | myareas | No |
> | myareas/areas | No |
> | myareas/areas/areas | No |
> | myareas/areas/areas/galleryitems | No |
> | myareas/areas/galleryitems | No |
> | myareas/galleryitems | No |
> | registro | No |
> | resources | No |
> | retrieveresourcesbyid | No |

## <a name="microsoftgenomics"></a>Microsoft.Genomics

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminación del modo completo |
> | ------------- | ----------- |
> | accounts | Sí |

## <a name="microsoftgraph"></a>Microsoft.Graph

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminación del modo completo |
> | ------------- | ----------- |
> | AzureAdApplication | No |

## <a name="microsoftguestconfiguration"></a>Microsoft.GuestConfiguration

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminación del modo completo |
> | ------------- | ----------- |
> | autoManagedAccounts | Sí |
> | autoManagedVmConfigurationProfiles | Sí |
> | configurationProfileAssignments | No |
> | guestConfigurationAssignments | No |
> | software | No |
> | softwareUpdateProfile | No |
> | softwareUpdates | No |

## <a name="microsofthanaonazure"></a>Microsoft.HanaOnAzure

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminación del modo completo |
> | ------------- | ----------- |
> | hanaInstances | Sí |
> | sapMonitors | Sí |

## <a name="microsofthardwaresecuritymodules"></a>Microsoft.HardwareSecurityModules

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminación del modo completo |
> | ------------- | ----------- |
> | dedicatedHSMs | Sí |

## <a name="microsofthdinsight"></a>Microsoft.HDInsight

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminación del modo completo |
> | ------------- | ----------- |
> | clusterPools | Sí |
> | clusterPools/clusters | Sí |
> | clusterPools/clusters/instanceViews | No |
> | clusterPools/clusters/serviceConfigs | No |
> | clusterPools/clusters/sessionClusters | Yes |
> | clusterPools/clusters/sessionClusters/instanceViews | No |
> | clusterPools/clusters/sessionClusters/serviceConfigs | No |
> | clusters | Sí |
> | clusters/applications | No |

## <a name="microsofthealthbot"></a>Microsoft.HealthBot

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminación del modo completo |
> | ------------- | ----------- |
> | healthBots | No |

## <a name="microsofthealthcareapis"></a>Microsoft.HealthcareApis

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminación del modo completo |
> | ------------- | ----------- |
> | services | Sí |
> | services / iomtconnectors | No |
> | services / iomtconnectors / connections | No |
> | services / iomtconnectors / mappings | No |
> | services / privateEndpointConnectionProxies | No |
> | services / privateEndpointConnections | No |
> | services / privateLinkResources | No |
> | workspaces | Sí |
> | workspaces/dicomservices | Sí |
> | workspaces/eventGridFilters | No |
> | workspaces/fhirservices | Yes |
> | workspaces/iotconnectors | Yes |
> | workspaces/iotconnectors/destinations | No |
> | workspaces/iotconnectors/fhirdestinations | No |
> | workspaces / privateEndpointConnectionProxies | No |
> | workspaces / privateEndpointConnections | No |
> | workspaces / privateLinkResources | No |

## <a name="microsofthpcworkbench"></a>Microsoft.HpcWorkbench

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminación del modo completo |
> | ------------- | ----------- |
> | instances | No |

## <a name="microsofthybridcompute"></a>Microsoft.HybridCompute

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminación del modo completo |
> | ------------- | ----------- |
> | machines | Sí |
> | machines / assessPatches | No |
> | machines/extensions | Sí |
> | machines / installPatches | No |
> | machines/privateLinkScopes | No |
> | privateLinkScopes | Sí |
> | privateLinkScopes/privateEndpointConnectionProxies | No |
> | privateLinkScopes/privateEndpointConnections | No |

## <a name="microsofthybridconnectivity"></a>Microsoft.HybridConnectivity

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminación del modo completo |
> | ------------- | ----------- |
> | extremos | No |

## <a name="microsofthybridcontainerservice"></a>Microsoft.HybridContainerService

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminación del modo completo |
> | ------------- | ----------- |
> | provisionedClusters | No |

## <a name="microsofthybriddata"></a>Microsoft.HybridData

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminación del modo completo |
> | ------------- | ----------- |
> | dataManagers | Sí |

## <a name="microsofthybridnetwork"></a>Microsoft.HybridNetwork

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminación del modo completo |
> | ------------- | ----------- |
> | devices | No |
> | networkFunctions | No |
> | networkFunctionVendors | No |
> | registeredSubscriptions | No |
> | vendors | No |
> | vendors / vendorSkus | No |
> | vendors/vendorskus/previewSubscriptions | No |

## <a name="microsofthydra"></a>Microsoft.Hydra

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminación del modo completo |
> | ------------- | ----------- |
> | components | Sí |
> | networkScopes | Sí |

## <a name="microsoftimportexport"></a>Microsoft.ImportExport

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminación del modo completo |
> | ------------- | ----------- |
> | jobs | Sí |

## <a name="microsoftintune"></a>Microsoft.Intune

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminación del modo completo |
> | ------------- | ----------- |
> | diagnosticSettings | No |
> | diagnosticSettingsCategories | No |

## <a name="microsoftiotcentral"></a>Microsoft.IoTCentral

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminación del modo completo |
> | ------------- | ----------- |
> | appTemplates | No |
> | IoTApps | Sí |

## <a name="microsoftiotsecurity"></a>Microsoft.IoTSecurity

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminación del modo completo |
> | ------------- | ----------- |
> | alertTypes | No |
> | defenderSettings | No |
> | onPremiseSensors | No |
> | recommendationTypes | No |
> | sensors | No |
> | sites | No |

## <a name="microsoftiotspaces"></a>Microsoft.IoTSpaces

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminación del modo completo |
> | ------------- | ----------- |
> | Grafo | Sí |

## <a name="microsoftkeyvault"></a>Microsoft.KeyVault

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminación del modo completo |
> | ------------- | ----------- |
> | deletedManagedHSMs | No |
> | deletedVaults | No |
> | hsmPools | Sí |
> | managedHSMs | Sí |
> | vaults | Sí |
> | vaults/accessPolicies | No |
> | vaults/eventGridFilters | No |
> | vaults / keys | No |
> | vaults / keys / versions | No |
> | vaults/secrets | No |

## <a name="microsoftkubernetes"></a>Microsoft.Kubernetes

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminación del modo completo |
> | ------------- | ----------- |
> | connectedClusters | No |
> | registeredSubscriptions | No |

## <a name="microsoftkubernetesconfiguration"></a>Microsoft.KubernetesConfiguration

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminación del modo completo |
> | ------------- | ----------- |
> | extensions | No |
> | fluxConfigurations | No |
> | sourceControlConfigurations | No |

## <a name="microsoftkusto"></a>Microsoft.Kusto

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminación del modo completo |
> | ------------- | ----------- |
> | clusters | Sí |
> | clusters/attacheddatabaseconfigurations | No |
> | clusters/databases | No |
> | clusters/databases/dataconnections | No |
> | clusters/databases/eventhubconnections | No |
> | clusters / databases / principalassignments | No |
> | clusters/databases/scripts | No |
> | clusters/dataconnections | No |
> | clusters / principalassignments | No |
> | clusters/sharedidentities | No |

## <a name="microsoftlabservices"></a>Microsoft.LabServices

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminación del modo completo |
> | ------------- | ----------- |
> | labaccounts | Sí |
> | labplans | Sí |
> | labs | Sí |
> | users | No |

## <a name="microsoftlocationservices"></a>Microsoft.LocationServices

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminación del modo completo |
> | ------------- | ----------- |
> | accounts | Sí |

## <a name="microsoftlogic"></a>Microsoft.Logic

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminación del modo completo |
> | ------------- | ----------- |
> | hostingEnvironments | Sí |
> | integrationAccounts | Sí |
> | integrationServiceEnvironments | Sí |
> | integrationServiceEnvironments/managedApis | Sí |
> | isolatedEnvironments | Sí |
> | workflows | Sí |

## <a name="microsoftmachinelearning"></a>Microsoft.MachineLearning

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminación del modo completo |
> | ------------- | ----------- |
> | commitmentPlans | Sí |
> | webServices | Sí |
> | Áreas de trabajo | Sí |

## <a name="microsoftmachinelearningservices"></a>Microsoft.MachineLearningServices

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminación del modo completo |
> | ------------- | ----------- |
> | aisysteminventories | Sí |
> | virtualclusters | Sí |
> | workspaces | Sí |
> | workspaces / batchEndpoints | Sí |
> | workspaces / batchEndpoints / deployments | Sí |
> | workspaces/batchEndpoints/deployments/jobs | No |
> | workspaces/batchEndpoints/jobs | No |
> | workspaces / codes | No |
> | workspaces / codes / versions | No |
> | workspaces/components | No |
> | workspaces/components/versions | No |
> | workspaces/computes | No |
> | workspaces/data | No |
> | workspaces/datasets | No |
> | workspaces / datastores | No |
> | workspaces/environments | No |
> | workspaces/eventGridFilters | No |
> | workspaces / jobs | No |
> | workspaces / labelingJobs | No |
> | workspaces/linkedServices | No |
> | workspaces / models | No |
> | workspaces / models / versions | No |
> | workspaces / onlineEndpoints | Sí |
> | workspaces / onlineEndpoints / deployments | Sí |

## <a name="microsoftmaintenance"></a>Microsoft.Maintenance

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminación del modo completo |
> | ------------- | ----------- |
> | applyUpdates | No |
> | configurationAssignments | No |
> | maintenanceConfigurations | Sí |
> | publicMaintenanceConfigurations | No |
> | updates | No |

## <a name="microsoftmanagedidentity"></a>Microsoft.ManagedIdentity

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminación del modo completo |
> | ------------- | ----------- |
> | Identities | No |
> | userAssignedIdentities | Sí |

## <a name="microsoftmanagedservices"></a>Microsoft.ManagedServices

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminación del modo completo |
> | ------------- | ----------- |
> | marketplaceRegistrationDefinitions | No |
> | registrationAssignments | No |
> | registrationDefinitions | No |

## <a name="microsoftmanagement"></a>Microsoft.Management

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminación del modo completo |
> | ------------- | ----------- |
> | getEntities | No |
> | managementGroups | No |
> | managementGroups / settings | No |
> | resources | No |
> | startTenantBackfill | No |
> | tenantBackfillStatus | No |

## <a name="microsoftmaps"></a>Microsoft.Maps

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminación del modo completo |
> | ------------- | ----------- |
> | accounts | Sí |
> | accounts/creators | Sí |
> | accounts/eventGridFilters | No |
> | accounts/privateAtlases | Sí |

## <a name="microsoftmarketplace"></a>Microsoft.Marketplace

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminación del modo completo |
> | ------------- | ----------- |
> | macc | No |
> | offers | No |
> | offerTypes | No |
> | offerTypes/publishers | No |
> | offerTypes/publishers/offers | No |
> | offerTypes/publishers/offers/plans | No |
> | offerTypes/publishers/offers/plans/agreements | No |
> | offerTypes/publishers/offers/plans/configs | No |
> | offerTypes/publishers/offers/plans/configs/importImage | No |
> | privategalleryitems | No |
> | privateStoreClient | No |
> | privateStores | No |
> | privateStores/AdminRequestApprovals | No |
> | privateStores/billingAccounts | No |
> | privateStores/bulkCollectionsAction | No |
> | privateStores/collections | No |
> | privateStores/collections/offers | No |
> | privateStores/collections/transferOffers | No |
> | privateStores/collectionsToSubscriptionsMapping | No |
> | privateStores/offers | No |
> | privateStores/offers/acknowledgeNotification | No |
> | privateStores/queryApprovedPlans | No |
> | privateStores/queryNotificationsState | No |
> | privateStores/queryOffers | No |
> | privateStores/RequestApprovals | No |
> | privateStores/requestApprovals/query | No |
> | privateStores/requestApprovals/withdrawPlan | No |
> | products | No |
> | publishers | No |
> | publishers/offers | No |
> | publishers/offers/amendments | No |
> | registro | No |

## <a name="microsoftmarketplaceapps"></a>Microsoft.MarketplaceApps

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminación del modo completo |
> | ------------- | ----------- |
> | classicDevServices | Sí |
> | updateCommunicationPreference | No |

## <a name="microsoftmarketplacenotifications"></a>Microsoft.MarketplaceNotifications

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminación del modo completo |
> | ------------- | ----------- |
> | reviewsnotifications | No |

## <a name="microsoftmarketplaceordering"></a>Microsoft.MarketplaceOrdering

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminación del modo completo |
> | ------------- | ----------- |
> | agreements | No |
> | offertypes | No |

## <a name="microsoftmedia"></a>Microsoft.Media

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminación del modo completo |
> | ------------- | ----------- |
> | mediaservices | Sí |
> | mediaservices/accountFilters | No |
> | mediaservices/assets | No |
> | mediaservices/assets/assetFilters | No |
> | mediaservices/contentKeyPolicies | No |
> | mediaservices/eventGridFilters | No |
> | mediaservices/graphInstances | No |
> | mediaservices/graphTopologies | No |
> | mediaservices/liveEventOperations | No |
> | mediaservices/liveEvents | Sí |
> | mediaservices/liveEvents/liveOutputs | No |
> | mediaservices/liveOutputOperations | No |
> | mediaservices/mediaGraphs | No |
> | mediaservices/privateEndpointConnectionOperations | No |
> | mediaservices/privateEndpointConnectionProxies | No |
> | mediaservices/privateEndpointConnections | No |
> | mediaservices/streamingEndpointOperations | No |
> | mediaservices/streamingEndpoints | Sí |
> | mediaservices/streamingLocators | No |
> | mediaservices/streamingPolicies | No |
> | mediaservices/transforms | No |
> | mediaservices/transforms/jobs | No |
> | videoAnalyzers | Sí |
> | videoAnalyzers/accessPolicies | No |
> | videoAnalyzers/edgeModules | No |
> | videoAnalyzers/livePipelines | No |
> | videoAnalyzers/pipelineJobs | No |
> | videoAnalyzers/pipelineTopologies | No |
> | videoAnalyzers/videos | No |

## <a name="microsoftmigrate"></a>Microsoft.Migrate

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminación del modo completo |
> | ------------- | ----------- |
> | assessmentProjects | Sí |
> | migrateprojects | Sí |
> | moveCollections | Sí |
> | projects | Sí |

## <a name="microsoftmixedreality"></a>Microsoft.MixedReality

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminación del modo completo |
> | ------------- | ----------- |
> | holographicsBroadcastAccounts | Sí |
> | objectAnchorsAccounts | Sí |
> | objectUnderstandingAccounts | Sí |
> | remoteRenderingAccounts | Sí |
> | spatialAnchorsAccounts | Sí |

## <a name="microsoftmobilenetwork"></a>Microsoft.MobileNetwork

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminación del modo completo |
> | ------------- | ----------- |
> | mobileNetworks | No |
> | mobileNetworks/dataNetworks | No |
> | mobileNetworks/services | No |
> | mobileNetworks/simPolicies | No |
> | mobileNetworks/sites | No |
> | mobileNetworks/slices | No |
> | networks | No |
> | networks/sites | No |
> | packetCoreControlPlanes | No |
> | packetCoreControlPlanes/packetCoreDataPlanes | No |
> | packetCoreControlPlanes/packetCoreDataPlanes/attachedDataNetworks | No |
> | packetCores | No |
> | sims | No |
> | sims/simProfiles | No |

## <a name="microsoftmonitor"></a>Microsoft.Monitor

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminación del modo completo |
> | ------------- | ----------- |
> | accounts | Sí |

## <a name="microsoftnetapp"></a>Microsoft.NetApp

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminación del modo completo |
> | ------------- | ----------- |
> | netAppAccounts | Sí |
> | netAppAccounts/accountBackups | No |
> | netAppAccounts/capacityPools | Sí |
> | netAppAccounts/capacityPools/volumes | Sí |
> | netAppAccounts/capacityPools/volumes/snapshots | No |
> | netAppAccounts/capacityPools/volumes/subvolumes | No |
> | netAppAccounts/snapshotPolicies | Yes |
> | netAppAccounts/volumeGroups | No |

## <a name="microsoftnetworkfunction"></a>Microsoft.NetworkFunction

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminación del modo completo |
> | ------------- | ----------- |
> | azureTrafficCollectors | Sí |
## <a name="microsoftnetwork"></a>Microsoft.Network

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminación del modo completo |
> | ------------- | ----------- |
> | applicationGateways | Sí |
> | applicationGatewayWebApplicationFirewallPolicies | Sí |
> | applicationSecurityGroups | Sí |
> | azureFirewallFqdnTags | No |
> | azureFirewalls | Sí |
> | bastionHosts | Sí |
> | bgpServiceCommunities | No |
> | connections | Sí |
> | ddosCustomPolicies | Sí |
> | ddosProtectionPlans | Sí |
> | dnsOperationStatuses | No |
> | dnszones | Sí |
> | dnszones/A | No |
> | dnszones/AAAA | No |
> | dnszones/all | No |
> | dnszones/CAA | No |
> | dnszones/CNAME | No |
> | dnszones/MX | No |
> | dnszones/NS | No |
> | dnszones/PTR | No |
> | dnszones/recordsets | No |
> | dnszones/SOA | No |
> | dnszones/SRV | No |
> | dnszones/TXT | No |
> | expressRouteCircuits | Sí |
> | expressRouteCrossConnections | Sí |
> | expressRouteGateways | Sí |
> | expressRoutePorts | Sí |
> | expressRouteServiceProviders | No |
> | firewallPolicies | Sí |
> | frontdoors | Sí |
> | frontdoorWebApplicationFirewallManagedRuleSets | No |
> | frontdoorWebApplicationFirewallPolicies | Sí |
> | getDnsResourceReference | No |
> | internalNotify | No |
> | ipGroups | Sí |
> | loadBalancers | Sí |
> | localNetworkGateways | Sí |
> | natGateways | Sí |
> | networkIntentPolicies | Sí |
> | networkInterfaces | Sí |
> | networkProfiles | Sí |
> | networkSecurityGroups | Sí |
> | networkWatchers | Sí |
> | networkWatchers/connectionMonitors | Sí |
> | networkWatchers/flowLogs | Sí |
> | networkWatchers/lenses | Sí |
> | networkWatchers/pingMeshes | Sí |
> | p2sVpnGateways | Sí |
> | privateDnsOperationStatuses | No |
> | privateDnsZones | Sí |
> | privateDnsZones/A | No |
> | privateDnsZones/AAAA | No |
> | privateDnsZones/all | No |
> | privateDnsZones/CNAME | No |
> | privateDnsZones/MX | No |
> | privateDnsZones/PTR | No |
> | privateDnsZones/SOA | No |
> | privateDnsZones/SRV | No |
> | privateDnsZones/TXT | No |
> | privateDnsZones/virtualNetworkLinks | Sí |
> | privateEndpoints | Sí |
> | privateLinkServices | Sí |
> | publicIPAddresses | Sí |
> | publicIPPrefixes | Sí |
> | routeFilters | Sí |
> | routeTables | Sí |
> | serviceEndpointPolicies | Sí |
> | trafficManagerGeographicHierarchies | No |
> | trafficmanagerprofiles | Sí |
> | trafficmanagerprofiles/heatMaps | No |
> | trafficManagerUserMetricsKeys | No |
> | virtualHubs | Sí |
> | virtualNetworkGateways | Sí |
> | virtualNetworks | Sí |
> | virtualNetworks / subnets | No |
> | virtualNetworkTaps | Sí |
> | virtualWans | Sí |
> | vpnGateways | Sí |
> | vpnSites | Sí |
> | webApplicationFirewallPolicies | Sí |

## <a name="microsoftnotebooks"></a>Microsoft.Notebooks

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminación del modo completo |
> | ------------- | ----------- |
> | NotebookProxies | No |

## <a name="microsoftnotificationhubs"></a>Microsoft.NotificationHubs

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminación del modo completo |
> | ------------- | ----------- |
> | espacios de nombres | Sí |
> | namespaces/notificationHubs | Sí |

## <a name="microsoftobjectstore"></a>Microsoft.ObjectStore

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminación del modo completo |
> | ------------- | ----------- |
> | osNamespaces | No |

## <a name="microsoftoffazure"></a>Microsoft.OffAzure

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminación del modo completo |
> | ------------- | ----------- |
> | HyperVSites | Sí |
> | ImportSites | Sí |
> | MasterSites | Sí |
> | ServerSites | Sí |
> | VMwareSites | Sí |

## <a name="microsoftoperationalinsights"></a>Microsoft.OperationalInsights

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminación del modo completo |
> | ------------- | ----------- |
> | clusters | Sí |
> | deletedWorkspaces | No |
> | linkTargets | No |
> | querypacks | Sí |
> | storageInsightConfigs | No |
> | workspaces | Sí |
> | workspaces / dataExports | No |
> | workspaces/dataSources | No |
> | workspaces/linkedServices | No |
> | workspaces/linkedStorageAccounts | No |
> | workspaces/metadata | No |
> | workspaces/query | No |
> | workspaces/scopedPrivateLinkProxies | No |
> | workspaces/storageInsightConfigs | No |
> | workspaces/tables | No |

## <a name="microsoftoperationsmanagement"></a>Microsoft.OperationsManagement

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminación del modo completo |
> | ------------- | ----------- |
> | managementassociations | No |
> | managementconfigurations | Sí |
> | solutions | Sí |
> | views | Sí |

## <a name="microsoftpeering"></a>Microsoft.Peering

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminación del modo completo |
> | ------------- | ----------- |
> | cdnPeeringPrefixes | No |
> | legacyPeerings | No |
> | lookingGlass | No |
> | peerAsns | No |
> | peerings | Sí |
> | peeringServiceCountries | No |
> | peeringServiceProviders | No |
> | peeringServices | Sí |

## <a name="microsoftplayfab"></a>Microsoft.PlayFab

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminación del modo completo |
> | ------------- | ----------- |
> | PlayerAccountPools | No |
> | Títulos | No |

## <a name="microsoftpolicyinsights"></a>Microsoft.PolicyInsights

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminación del modo completo |
> | ------------- | ----------- |
> | attestations | No |
> | eventGridFilters | No |
> | policyEvents | No |
> | policyMetadata | No |
> | policyStates | No |
> | policyTrackedResources | No |
> | remediations | No |

## <a name="microsoftportal"></a>Microsoft.Portal

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminación del modo completo |
> | ------------- | ----------- |
> | consoles | No |
> | dashboards | Sí |
> | tenantconfigurations | No |
> | userSettings | No |

## <a name="microsoftpowerbi"></a>Microsoft.PowerBI

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminación del modo completo |
> | ------------- | ----------- |
> | privateLinkServicesForPowerBI | Sí |
> | tenants | Sí |
> | tenants / workspaces | No |
> | workspaceCollections | Sí |

## <a name="microsoftpowerbidedicated"></a>Microsoft.PowerBIDedicated

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminación del modo completo |
> | ------------- | ----------- |
> | autoScaleVCores | Sí |
> | capacities | Sí |
> | servers | Sí |

## <a name="microsoftpowerplatform"></a>Microsoft.PowerPlatform

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminación del modo completo |
> | ------------- | ----------- |
> | accounts | Sí |
> | enterprisePolicies | Sí |

## <a name="microsoftprojectbabylon"></a>Microsoft.ProjectBabylon

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminación del modo completo |
> | ------------- | ----------- |
> | accounts | Sí |
> | deletedAccounts | No |

## <a name="microsoftproviderhub"></a>Microsoft.ProviderHub

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminación del modo completo |
> | ------------- | ----------- |
> | providerRegistrations | No |
> | providerRegistrations/customRollouts | No |
> | providerRegistrations / defaultRollouts | No |
> | providerRegistrations/resourceActions | No |
> | providerRegistrations/resourceTypeRegistrations | No |

## <a name="microsoftpurview"></a>Microsoft.Purview

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminación del modo completo |
> | ------------- | ----------- |
> | accounts | Sí |
> | deletedAccounts | No |
> | getDefaultAccount | No |
> | removeDefaultAccount | No |
> | setDefaultAccount | No |

## <a name="microsoftquantum"></a>Microsoft.Quantum

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminación del modo completo |
> | ------------- | ----------- |
> | Áreas de trabajo | No |

## <a name="microsoftquota"></a>Microsoft.Quota

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminación del modo completo |
> | ------------- | ----------- |
> | quotaRequests | No |
> | quotas | No |
> | usages | No |

## <a name="microsoftrecommendationsservice"></a>Microsoft.RecommendationsService

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminación del modo completo |
> | ------------- | ----------- |
> | accounts | No |
> | accounts/modeling | No |
> | accounts/serviceEndpoints | No |

## <a name="microsoftrecoveryservices"></a>Microsoft.RecoveryServices

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminación del modo completo |
> | ------------- | ----------- |
> | backupProtectedItems | No |
> | vaults | Sí |

## <a name="microsoftredhatopenshift"></a>Microsoft.RedHatOpenShift

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminación del modo completo |
> | ------------- | ----------- |
> | OpenShiftClusters | Sí |

## <a name="microsoftrelay"></a>Microsoft.Relay

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminación del modo completo |
> | ------------- | ----------- |
> | espacios de nombres | Sí |
> | namespaces/authorizationrules | No |
> | namespaces/hybridconnections | No |
> | namespaces/hybridconnections/authorizationrules | No |
> | namespaces / privateEndpointConnections | No |
> | namespaces/wcfrelays | No |
> | namespaces/wcfrelays/authorizationrules | No |

## <a name="microsoftresourceconnector"></a>Microsoft.ResourceConnector

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminación del modo completo |
> | ------------- | ----------- |
> | appliances | Sí |

## <a name="microsoftresourcegraph"></a>Microsoft.ResourceGraph

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminación del modo completo |
> | ------------- | ----------- |
> | Consultas | Sí |
> | resourceChangeDetails | No |
> | resourceChanges | No |
> | resources | No |
> | resourcesHistory | No |
> | subscriptionsStatus | No |

## <a name="microsoftresourcehealth"></a>Microsoft.ResourceHealth

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminación del modo completo |
> | ------------- | ----------- |
> | availabilityStatuses | No |
> | childAvailabilityStatuses | No |
> | childResources | No |
> | emergingissues | No |
> | events | No |
> | impactedResources | No |
> | metadata | No |

## <a name="microsoftresources"></a>Microsoft.Resources

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminación del modo completo |
> | ------------- | ----------- |
> | deployments | No |
> | deployments/operations | No |
> | deploymentScripts | Sí |
> | deploymentScripts/logs | No |
> | deploymentStacks | No |
> | deploymentStacks/snapshots | No |
> | vínculos | No |
> | providers | No |
> | resourceGroups | No |
> | subscriptions | No |
> | templateSpecs | Sí |
> | templateSpecs / versions | Sí |
> | tenants | No |

## <a name="microsoftsaas"></a>Microsoft.SaaS

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminación del modo completo |
> | ------------- | ----------- |
> | applications | Sí |
> | resources | Sí |
> | saasresources | No |

## <a name="microsoftscom"></a>Microsoft.Scom

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminación del modo completo |
> | ------------- | ----------- |
> | managedInstances | No |

## <a name="microsoftscvmm"></a>Microsoft.ScVmm

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminación del modo completo |
> | ------------- | ----------- |
> | clouds | No |
> | VirtualMachines | No |
> | VirtualMachineTemplates | No |
> | VirtualNetworks | No |
> | vmmservers | No |

## <a name="microsoftsearch"></a>Microsoft.Search

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminación del modo completo |
> | ------------- | ----------- |
> | resourceHealthMetadata | No |
> | searchServices | Sí |

## <a name="microsoftsecurity"></a>Microsoft.Security

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminación del modo completo |
> | ------------- | ----------- |
> | adaptiveNetworkHardenings | No |
> | advancedThreatProtectionSettings | No |
> | alerts | No |
> | alertsSuppressionRules | No |
> | allowedConnections | No |
> | antiMalwareSettings | No |
> | applicationWhitelistings | No |
> | assessmentMetadata | No |
> | assessments | No |
> | asignaciones | Yes |
> | autoDismissAlertsRules | No |
> | automations | Sí |
> | AutoProvisioningSettings | No |
> | Compliances | No |
> | connectedContainerRegistries | No |
> | conectores | No |
> | customAssessmentAutomations | Yes |
> | customEntityStoreAssignments | Yes |
> | dataCollectionAgents | No |
> | deviceSecurityGroups | No |
> | discoveredSecuritySolutions | No |
> | externalSecuritySolutions | No |
> | InformationProtectionPolicies | No |
> | ingestionSettings | No |
> | insights | No |
> | iotSecuritySolutions | Sí |
> | iotSecuritySolutions/analyticsModels | No |
> | iotSecuritySolutions/analyticsModels/aggregatedAlerts | No |
> | iotSecuritySolutions/analyticsModels/aggregatedRecommendations | No |
> | iotSecuritySolutions / iotAlerts | No |
> | iotSecuritySolutions / iotAlertTypes | No |
> | iotSecuritySolutions/iotRecommendations | No |
> | iotSecuritySolutions/iotRecommendationTypes | No |
> | jitNetworkAccessPolicies | No |
> | jitPolicies | No |
> | directivas | No |
> | pricings | No |
> | regulatoryComplianceStandards | No |
> | regulatoryComplianceStandards/regulatoryComplianceControls | No |
> | regulatoryComplianceStandards/regulatoryComplianceControls/regulatoryComplianceAssessments | No |
> | secureScoreControlDefinitions | No |
> | secureScoreControls | No |
> | secureScores | No |
> | secureScores/secureScoreControls | No |
> | securityConnectors | Yes |
> | securityContacts | No |
> | securitySolutions | No |
> | securitySolutionsReferenceData | No |
> | securityStatuses | No |
> | securityStatusesSummaries | No |
> | serverVulnerabilityAssessments | No |
> | configuración | No |
> | sqlVulnerabilityAssessments | No |
> | standards | Yes |
> | subAssessments | No |
> | tareas | No |
> | topologies | No |
> | workspaceSettings | No |

## <a name="microsoftsecuritygraph"></a>Microsoft.SecurityGraph

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminación del modo completo |
> | ------------- | ----------- |
> | diagnosticSettings | No |
> | diagnosticSettingsCategories | No |

## <a name="microsoftsecurityinsights"></a>Microsoft.SecurityInsights

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminación del modo completo |
> | ------------- | ----------- |
> | aggregations | No |
> | alertRules | No |
> | alertRuleTemplates | No |
> | automationRules | No |
> | bookmarks | No |
> | cases | No |
> | dataConnectors | No |
> | dataConnectorsCheckRequirements | No |
> | enrichment | No |
> | entities | No |
> | entityQueries | No |
> | entityQueryTemplates | No |
> | incidents | No |
> | metadata | No |
> | officeConsents | No |
> | onboardingStates | No |
> | configuración | No |
> | sourceControls | No |
> | threatIntelligence | No |
> | watchlists | No |

## <a name="microsoftserialconsole"></a>Microsoft.SerialConsole

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminación del modo completo |
> | ------------- | ----------- |
> | consoleServices | No |
> | serialPorts | No |

## <a name="microsoftservicebus"></a>Microsoft.ServiceBus

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminación del modo completo |
> | ------------- | ----------- |
> | espacios de nombres | Sí |
> | namespaces/authorizationrules | No |
> | namespaces/disasterrecoveryconfigs | No |
> | namespaces/eventgridfilters | No |
> | namespaces/networkrulesets | No |
> | namespaces / privateEndpointConnections | No |
> | namespaces/queues | No |
> | namespaces/queues/authorizationrules | No |
> | namespaces/topics | No |
> | namespaces/topics/authorizationrules | No |
> | namespaces/topics/subscriptions | No |
> | namespaces/topics/subscriptions/rules | No |
> | premiumMessagingRegions | No |

## <a name="microsoftservicefabric"></a>Microsoft.ServiceFabric

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminación del modo completo |
> | ------------- | ----------- |
> | applications | Sí |
> | clusters | Sí |
> | clusters/applications | No |
> | containerGroups | Sí |
> | containerGroupSets | Sí |
> | edgeclusters | Sí |
> | edgeclusters/applications | No |
> | managedclusters | Sí |
> | managedclusters/applications | No |
> | managedclusters/applications/services | No |
> | managedclusters/applicationTypes | No |
> | managedclusters/applicationTypes/versions | No |
> | managedclusters/nodetypes | No |
> | networks | Sí |
> | secretstores | Sí |
> | secretstores/certificates | No |
> | secretstores/secrets | No |
> | volumes | Sí |

## <a name="microsoftservicefabricmesh"></a>Microsoft.ServiceFabricMesh

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminación del modo completo |
> | ------------- | ----------- |
> | applications | Sí |
> | containerGroups | Sí |
> | gateways | Sí |
> | networks | Sí |
> | secrets | Sí |
> | volumes | Sí |

## <a name="microsoftservicelinker"></a>Microsoft.ServiceLinker

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminación del modo completo |
> | ------------- | ----------- |
> | linkers | No |

## <a name="microsoftservices"></a>Microsoft.Services

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminación del modo completo |
> | ------------- | ----------- |
> | providerRegistrations | No |
> | providerRegistrations/resourceTypeRegistrations | No |
> | rollouts | Sí |

## <a name="microsoftsignalrservice"></a>Microsoft.SignalRService

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminación del modo completo |
> | ------------- | ----------- |
> | SignalR | Sí |
> | SignalR/eventGridFilters | No |
> | WebPubSub | Sí |

## <a name="microsoftsingularity"></a>Microsoft.Singularity

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminación del modo completo |
> | ------------- | ----------- |
> | accounts | Sí |
> | accounts / accountQuotaPolicies | No |
> | accounts / groupPolicies | No |
> | accounts / jobs | No |
> | accounts/models | No |
> | accounts / storageContainers | No |
> | images | No |
> | quotas | No |

## <a name="microsoftsoftwareplan"></a>Microsoft.SoftwarePlan

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminación del modo completo |
> | ------------- | ----------- |
> | hybridUseBenefits | No |

## <a name="microsoftsolutions"></a>Microsoft.Solutions

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminación del modo completo |
> | ------------- | ----------- |
> | applicationDefinitions | Sí |
> | applications | Sí |
> | jitRequests | Sí |

## <a name="microsoftsql"></a>Microsoft.SQL

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminación del modo completo |
> | ------------- | ----------- |
> | managedInstances | Sí |
> | managedInstances/databases | Sí |
> | managedInstances/databases/backupShortTermRetentionPolicies | No |
> | managedInstances/databases/schemas/tables/columns/sensitivityLabels | No |
> | managedInstances/databases/vulnerabilityAssessments | No |
> | managedInstances/databases/vulnerabilityAssessments/rules/baselines | No |
> | managedInstances/encryptionProtector | No |
> | managedInstances/keys | No |
> | managedInstances/restorableDroppedDatabases/backupShortTermRetentionPolicies | No |
> | managedInstances/vulnerabilityAssessments | No |
> | servers | Sí |
> | servers/administrators | No |
> | servers/communicationLinks | No |
> | servers/databases | Sí |
> | servers/encryptionProtector | No |
> | servers/firewallRules | No |
> | servers/keys | No |
> | servers/restorableDroppedDatabases | No |
> | servers/serviceobjectives | No |
> | servers/tdeCertificates | No |
> | virtualClusters | No |

## <a name="microsoftsqlvirtualmachine"></a>Microsoft.SqlVirtualMachine

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminación del modo completo |
> | ------------- | ----------- |
> | SqlVirtualMachineGroups | Sí |
> | SqlVirtualMachineGroups/AvailabilityGroupListeners | No |
> | SqlVirtualMachines | Sí |

## <a name="microsoftstorage"></a>Microsoft.Storage

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminación del modo completo |
> | ------------- | ----------- |
> | deletedAccounts | No |
> | storageAccounts | Sí |
> | storageAccounts/blobServices | No |
> | storageAccounts/encryptionScopes | No |
> | storageAccounts/fileServices | No |
> | storageAccounts/queueServices | No |
> | storageAccounts/services | No |
> | storageAccounts/services/metricDefinitions | No |
> | storageAccounts/tableServices | No |
> | usages | No |

## <a name="microsoftstoragecache"></a>Microsoft.StorageCache

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminación del modo completo |
> | ------------- | ----------- |
> | amlFilesystems | Sí |
> | caches | Sí |
> | caches/storageTargets | No |
> | usageModels | No |

## <a name="microsoftstoragereplication"></a>Microsoft.StorageReplication

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminación del modo completo |
> | ------------- | ----------- |
> | replicationGroups | No |

## <a name="microsoftstoragesync"></a>Microsoft.StorageSync

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminación del modo completo |
> | ------------- | ----------- |
> | storageSyncServices | Sí |
> | storageSyncServices/registeredServers | No |
> | storageSyncServices/syncGroups | No |
> | storageSyncServices/syncGroups/cloudEndpoints | No |
> | storageSyncServices/syncGroups/serverEndpoints | No |
> | storageSyncServices/workflows | No |

## <a name="microsoftstorsimple"></a>Microsoft.StorSimple

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminación del modo completo |
> | ------------- | ----------- |
> | managers | Sí |

## <a name="microsoftstreamanalytics"></a>Microsoft.StreamAnalytics

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminación del modo completo |
> | ------------- | ----------- |
> | clusters | Sí |
> | clusters / privateEndpoints | No |
> | streamingjobs | Sí |

## <a name="microsoftsubscription"></a>Microsoft.Subscription

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminación del modo completo |
> | ------------- | ----------- |
> | acceptChangeTenant | No |
> | acceptOwnership | No |
> | acceptOwnershipStatus | No |
> | aliases | No |
> | cancel | No |
> | changeTenantRequest | No |
> | changeTenantStatus | No |
> | CreateSubscription | No |
> | enable | No |
> | directivas | No |
> | rename | No |
> | SubscriptionDefinitions | No |
> | SubscriptionOperations | No |
> | subscriptions | No |

## <a name="microsoftsynapse"></a>Microsoft.Synapse

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminación del modo completo |
> | ------------- | ----------- |
> | kustoOperations | No |
> | privateLinkHubs | Sí |
> | workspaces | Sí |
> | workspaces / bigDataPools | Sí |
> | workspaces/kustoPools | Yes |
> | workspaces/kustoPools/attacheddatabaseconfigurations | No |
> | workspaces/kustoPools/databases | No |
> | workspaces/kustoPools/databases/dataconnections | No |
> | workspaces / operationStatuses | No |
> | workspaces/sqlDatabases | Sí |
> | workspaces / sqlPools | Sí |

## <a name="microsofttestbase"></a>Microsoft.TestBase

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminación del modo completo |
> | ------------- | ----------- |
> | testBaseAccounts | No |
> | testBaseAccounts/customerEvents | No |
> | testBaseAccounts/emailEvents | No |
> | testBaseAccounts/flightingRings | No |
> | testBaseAccounts/packages | No |
> | testBaseAccounts/packages/favoriteProcesses | No |
> | testBaseAccounts/packages/osUpdates | No |
> | testBaseAccounts/testSummaries | No |
> | testBaseAccounts/testTypes | No |
> | testBaseAccounts/usages | No |

## <a name="microsofttimeseriesinsights"></a>Microsoft.TimeSeriesInsights

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminación del modo completo |
> | ------------- | ----------- |
> | environments | Sí |
> | environments/accessPolicies | No |
> | environments/eventsources | Sí |
> | environments/privateEndpointConnectionProxies | No |
> | environments/privateEndpointConnections | No |
> | environments/privateLinkResources | No |
> | environments/referenceDataSets | Sí |

## <a name="microsoftvideoindexer"></a>Microsoft.VideoIndexer

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminación del modo completo |
> | ------------- | ----------- |
> | accounts | No |

## <a name="microsoftvirtualmachineimages"></a>Microsoft.VirtualMachineImages

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminación del modo completo |
> | ------------- | ----------- |
> | imageTemplates | Sí |
> | imageTemplates / runOutputs | No |

## <a name="microsoftvmware"></a>Microsoft.VMware

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminación del modo completo |
> | ------------- | ----------- |
> | arczones | No |
> | resourcepools | No |
> | vcenters | No |
> | VCenters/InventoryItems | No |
> | virtualmachines | No |
> | virtualmachinetemplates | No |
> | virtualnetworks | No |

## <a name="microsoftvmwarecloudsimple"></a>Microsoft.VMwareCloudSimple

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminación del modo completo |
> | ------------- | ----------- |
> | dedicatedCloudNodes | Sí |
> | dedicatedCloudServices | Sí |
> | virtualMachines | Sí |

## <a name="microsoftvsonline"></a>Microsoft.VSOnline

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminación del modo completo |
> | ------------- | ----------- |
> | accounts | Sí |
> | plans | Sí |
> | registeredSubscriptions | No |
## <a name="microsoftweb"></a>Microsoft.Web

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminación del modo completo |
> | ------------- | ----------- |
> | apiManagementAccounts | No |
> | apiManagementAccounts/apiAcls | No |
> | apiManagementAccounts/apis | No |
> | apiManagementAccounts/apis/apiAcls | No |
> | apiManagementAccounts/apis/connectionAcls | No |
> | apiManagementAccounts/apis/connections | No |
> | apiManagementAccounts/apis/connections/connectionAcls | No |
> | apiManagementAccounts/apis/localizedDefinitions | No |
> | apiManagementAccounts/connectionAcls | No |
> | apiManagementAccounts/connections | No |
> | billingMeters | No |
> | certificates | Sí |
> | connectionGateways | Sí |
> | connections | Sí |
> | customApis | Sí |
> | deletedSites | No |
> | functionAppStacks | No |
> | generateGithubAccessTokenForAppserviceCLI | No |
> | hostingEnvironments | Sí |
> | hostingEnvironments / eventGridFilters | No |
> | hostingEnvironments/multiRolePools | No |
> | hostingEnvironments/workerPools | No |
> | kubeEnvironments | Sí |
> | publishingUsers | No |
> | de películas | No |
> | resourceHealthMetadata | No |
> | runtimes | No |
> | serverFarms | Sí |
> | serverFarms/eventGridFilters | No |
> | serverFarms / firstPartyApps | No |
> | serverFarms / firstPartyApps / keyVaultSettings | No |
> | sites | Sí |
> | sites/config  | No |
> | sites/eventGridFilters | No |
> | sites/hostNameBindings | No |
> | sites/networkConfig | No |
> | sites/premieraddons | Sí |
> | sites/slots | Sí |
> | sites/slots/eventGridFilters | No |
> | sites/slots/hostNameBindings | No |
> | sites/slots/networkConfig | No |
> | sourceControls | No |
> | staticSites | Sí |
> | validate | No |
> | verifyHostingEnvironmentVnet | No |
> | webAppStacks | No |

## <a name="microsoftwindowsdefenderatp"></a>Microsoft.WindowsDefenderATP

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminación del modo completo |
> | ------------- | ----------- |
> | diagnosticSettings | No |
> | diagnosticSettingsCategories | No |

## <a name="microsoftwindowsesu"></a>Microsoft.WindowsESU

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminación del modo completo |
> | ------------- | ----------- |
> | multipleActivationKeys | Sí |

## <a name="microsoftwindowsiot"></a>Microsoft.WindowsIoT

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminación del modo completo |
> | ------------- | ----------- |
> | DeviceServices | Sí |

## <a name="microsoftworkloadbuilder"></a>Microsoft.WorkloadBuilder

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminación del modo completo |
> | ------------- | ----------- |
> | migrationAgents | No |
> | workloads | No |
> | workloads / instances | No |
> | workloads / versions | No |
> | workloads / versions / artifacts | No |

## <a name="microsoftworkloadmonitor"></a>Microsoft.WorkloadMonitor

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminación del modo completo |
> | ------------- | ----------- |
> | monitors | No |

## <a name="next-steps"></a>Pasos siguientes

Para obtener los mismos datos que un archivo de valores separados por comas, descargue [complete-mode-deletion.csv](https://github.com/tfitzmac/resource-capabilities/blob/master/complete-mode-deletion.csv).