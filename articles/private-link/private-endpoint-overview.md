---
title: ¿Qué es un punto de conexión privado de Azure?
description: Información sobre el punto de conexión privado de Azure
services: private-link
author: asudbring
ms.service: private-link
ms.topic: conceptual
ms.date: 07/15/2021
ms.author: allensu
ms.openlocfilehash: f816ae15ddba9f56f1b504b2e4ccc52efdc09249
ms.sourcegitcommit: dcf1defb393104f8afc6b707fc748e0ff4c81830
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/27/2021
ms.locfileid: "123099981"
---
# <a name="what-is-azure-private-endpoint"></a>¿Qué es un punto de conexión privado de Azure?

Un punto de conexión privado de Azure es una interfaz de red que le conecta de forma privada y segura a un servicio con la tecnología de Azure Private Link. El punto de conexión privado usa una dirección IP privada de la red virtual, colocando el servicio de manera eficaz en su red virtual. 

El servicio podría ser un servicio de Azure como:

* Azure Storage
* Azure Cosmos DB
* Azure SQL Database
* Su propio servicio mediante el [servicio Private Link](private-link-service-overview.md).
  
## <a name="private-endpoint-properties"></a>Propiedades del punto de conexión privado 
 Un punto de conexión privado especifica las siguientes propiedades: 


|Propiedad  |Descripción |
|---------|---------|
|Nombre    |    Nombre único dentro del grupo de recursos.      |
|Subnet    |  Subred que se va a implementar y donde se asigna la dirección IP privada. Para conocer los requisitos de subred, consulte la sección Limitaciones de este artículo.         |
|Recurso de vínculo privado    |   El recurso de vínculo privado al que conectarse mediante el identificador o el alias del recurso, de la lista de tipos disponibles. Se generará un identificador de red único para todo el tráfico enviado a este recurso.       |
|Subrecurso de destino   |      Subrecurso que se va a conectar. Cada tipo de recurso de vínculo privado tiene diferentes opciones para seleccionar según las preferencias.    |
|Método de aprobación de conexión    |  Automático o manual. Según los permisos del control de acceso basado en roles de Azure, el punto de conexión privado se puede aprobar automáticamente. Si intenta conectarse a un recurso de vínculo privado sin control de acceso basado en rol de Azure, use el método manual para permitir que el propietario del recurso apruebe la conexión.        |
|Mensaje de solicitud     |  Puede especificar un mensaje para que las conexiones solicitadas se aprueben manualmente. Este mensaje se puede usar para identificar una solicitud específica.        |
|Estado de conexión   |   Propiedad de solo lectura que especifica si el punto de conexión privado está activo. Solo los puntos de conexión privados en un estado aprobado se pueden usar para enviar tráfico. Más estados disponibles: <br>-**Aprobado**: La conexión se aprobó de forma automática o manual y está lista para usarse.</br><br>-**Pendiente**: La conexión se creó manualmente y está pendiente de aprobación por parte del propietario del recurso de vínculo privado.</br><br>-**Rechazado**: El propietario del recurso de vínculo privado rechazó la conexión.</br><br>-**Desconectado**: El propietario del recurso de vínculo privado quitó la conexión. El punto de conexión privado se vuelve informativo y debe eliminarse para la limpieza. </br>|

Estos son algunos detalles importantes acerca de los puntos de conexión privados: 
- El punto de conexión privado permite la conectividad entre los consumidores de la misma red virtual, las redes virtuales emparejadas de forma regional, las redes virtuales emparejadas de forma global y las instalaciones locales que usan [VPN](https://azure.microsoft.com/services/vpn-gateway/) o [Express Route](https://azure.microsoft.com/services/expressroute/) y servicios con la tecnología Private Link.
 
- Las conexiones de red solo las pueden iniciar los clientes que se conectan al punto de conexión privado. Los proveedores de servicios no tienen configuración de enrutamiento para crear conexiones en los consumidores del servicio. Las conexiones solo se pueden establecer en una dirección.

- Al crear un punto de conexión privado, también se crea una interfaz de red de solo lectura para el ciclo de vida del recurso. A la interfaz se le asigna una dirección IP privada dinámica de la subred que se asigna al recurso de vínculo privado. El valor de la dirección IP privada no cambia durante todo el ciclo de vida del punto de conexión privado.
 
- El punto de conexión privado debe implementarse en la misma región y suscripción que la red virtual. 
 
- El recurso de vínculo privado se puede implementar en una región distinta a la de la red virtual y el punto de conexión privado.
 
- Se pueden crear varios puntos de conexión privados mediante el mismo recurso de vínculo privado. En el caso de una sola red que use una configuración de servidor DNS común, el procedimiento recomendado es usar un único punto de conexión privado para un recurso de vínculo privado determinado para evitar entradas duplicadas o conflictos en la resolución de DNS. 
 
- Se pueden crear varios puntos de conexión privados en la misma subred o en subredes diferentes dentro de la misma red virtual. Existen límites en cuanto al número de puntos de conexión privados que se pueden crear en una suscripción. Para más información, consulte el artículo acerca de los  [límites de Azure](../azure-resource-manager/management/azure-subscription-service-limits.md#networking-limits).

- La suscripción del recurso de vínculo privado también se debe registrar con Microsoft. Proveedor de recursos de red. Para más información, consulte  [Proveedores de recursos de Azure](../azure-resource-manager/management/resource-providers-and-types.md).

 
## <a name="private-link-resource"></a>Recurso de vínculo privado 
Un recurso de vínculo privado es el destino de un punto de conexión privado determinado. En la tabla siguiente se enumeran los recursos de punto de conexión privado disponibles: 
 
| Nombre del recurso de vínculo privado | Tipo de recurso | Subrecursos |
| ---------------------------| ------------- | ------------- |
| **Azure App Configuration** | Microsoft.Appconfiguration/configurationStores | configurationStores |
| **Azure Automation** | Microsoft.Automation/automationAccounts | Webhook, DSCAndHybridWorker |
| **Azure Cosmos DB** | Microsoft.AzureCosmosDB/databaseAccounts | Sql, MongoDB, Cassandra, Gremlin, Table |
| **Azure Batch** | Microsoft.Batch/batchAccounts | cuenta de Batch |
| **Azure Cache for Redis** | Microsoft.Cache/Redis | redisCache |
| **Azure Cache for Redis Enterprise** | Microsoft.Cache/redisEnterprise | redisEnterprise |
| **Cognitive Services** | Microsoft.CognitiveServices/accounts | account |
| **Azure Managed Disks** | Microsoft.Compute/diskAccesses | disco administrado |
| **Azure Container Registry** | Microsoft.ContainerRegistry/registries | Registro |
| **Azure Kubernetes Service: API de Kubernetes** | Microsoft.ContainerService/managedClusters | management |
| **Azure Data Factory** | Microsoft.DataFactory/factories | data factory |
| **Azure Database for MariaDB** | Microsoft.DBforMariaDB/servers | mariadbServer |
| **Azure Database for MySQL** | Microsoft.DBforMySQL/servers | mysqlServer |
| **Azure Database for PostgreSQL- Servidor único** | Microsoft.DBforPostgreSQL/servers | postgresqlServer |
| **Azure IoT Hub** | Microsoft.Devices/IotHubs | iotHub |
| **Microsoft Digital Twins** | Microsoft.DigitalTwins/digitalTwinsInstances | digitaltwinsinstance |
| **Azure Event Grid** | Microsoft.EventGrid/domains | dominio |
| **Azure Event Grid** | Microsoft.EventGrid/topics  | Tema de Event Grid |
| **Centro de eventos de Azure** | Microsoft.EventHub/namespaces | espacio de nombres |
| **Azure API for FHIR** | Microsoft.HealthcareApis/services | service |
| **Azure Keyvault HSM** | Microsoft.Keyvault/managedHSMs | HSM |
| **Azure Key Vault** | Microsoft.KeyVault/vaults | almacén |
| **Azure Machine Learning** | Microsoft.MachineLearningServices/workspaces | amlworkspace |
| **Azure Migrate** | Microsoft.Migrate/assessmentProjects | project |
| **Application Gateway** | Microsoft.Network/applicationgateways | puerta de enlace de aplicación |
| **Servicio Private Link** (su propio servicio) |  Microsoft.Network/privateLinkServices | empty |
| **Power BI** | Microsoft.PowerBI/privateLinkServicesForPowerBI | Power BI |
| **Azure Purview** | Microsoft.Purview/accounts | account |
| **Azure Backup** | Microsoft.RecoveryServices/vaults | almacén |
| **Azure Relay** | Microsoft.Relay/namespaces | espacio de nombres |
| **Búsqueda de Microsoft** | Microsoft.Search/searchServices | servicio de búsqueda |
| **Azure Service Bus** | Microsoft.ServiceBus/namespaces | espacio de nombres |
| **SignalR** | Microsoft.SignalRService/SignalR | signalr |
| **SignalR** | Microsoft.SignalRService/webPubSub | webpubsub |
| **Azure SQL Database** | Microsoft.Sql/servers | Sql Server (sqlServer) |
| **Almacenamiento de Azure** | Microsoft.Storage/storageAccounts | Blob (blob, blob_secondary)<BR> Tabla (table, table_secondary)<BR> Cola (queue, queue_secondary)<BR> Archivo (file, file_secondary)<BR> Web (web, web_secondary) |
| **Azure File Sync** | Microsoft.StorageSync/storageSyncServices | Servicio File Sync |
| **Azure Synapse** | Microsoft.Synapse/privateLinkHubs | synapse |
| **Azure Synapse Analytics** | Microsoft.Synapse/workspaces | Sql, SqlOnDemand, Dev | 
| **Azure App Service** | Microsoft.Web/hostingEnvironments | alojar entorno |
| **Azure App Service** | Microsoft.Web/sites | sites |
| **Azure App Service** | Microsoft.Web/staticSites | staticSite |

 
## <a name="network-security-of-private-endpoints"></a>Seguridad de red de los puntos de conexión privados 
Cuando se usan puntos de conexión privados para los servicios de Azure, el tráfico se protege en un recurso de vínculo privado específico. La plataforma realiza un control de acceso para validar las conexiones de red que solo alcanzan el recurso de vínculo privado especificado. Para acceder a más recursos dentro del mismo servicio de Azure, se requieren puntos de conexión privados adicionales. 
 
Puede impedir por completo que las cargas de trabajo accedan a puntos de conexión públicos para conectarse a un servicio de Azure admitido. Este control proporciona una capa de seguridad de red adicional a los recursos al proporcionar una protección de filtración integrada que impide el acceso a otros recursos hospedados en el mismo servicio de Azure. 
 
## <a name="access-to-a-private-link-resource-using-approval-workflow"></a>Acceso a un recurso de vínculo privado mediante el flujo de trabajo de aprobación 
Puede conectarse a un recurso de vínculo privado mediante los siguientes métodos de aprobación de la conexión:
- Aprobación **automática** cuando posee el recurso de vínculo privado específico o tiene permisos sobre él. El permiso requerido se basa en el tipo de recurso de enlace privado con el siguiente formato: Microsoft.\<Provider>/<resource_type>/privateEndpointConnectionsApproval/action
- Solicitud **manual** si no tiene el permiso necesario y desea solicitar acceso. Se iniciará un flujo de trabajo de aprobación. El punto de conexión privado y las conexiones de punto de conexión privado posteriores se crearán en un estado "Pendiente". El propietario del recurso de vínculo privado es responsable de aprobar la conexión. Una vez aprobada, el punto de conexión privado se habilita para enviar el tráfico normalmente, como se muestra en el siguiente diagrama de flujo de trabajo de aprobación.  

![aprobación de flujo de trabajo](media/private-endpoint-overview/private-link-paas-workflow.png)
 
El propietario del recurso de vínculo privado puede realizar las siguientes acciones a través de una conexión de punto de conexión privado: 

- Revisar todos los detalles de las conexiones de los puntos de conexión privados. 
- Aprobar una conexión de punto de conexión privado. El punto de conexión privado correspondiente se habilitará para enviar tráfico al recurso de vínculo privado. 
- Rechazar una conexión de punto de conexión privado. El punto de conexión privado correspondiente se actualizará para reflejar el estado.
- Eliminar una conexión de punto de conexión privado en cualquier estado. El punto de conexión privado correspondiente se actualizará con un estado desconectado para reflejar la acción. El propietario del punto de conexión privado solo puede eliminar el recurso en este momento. 
 
> [!NOTE]
> Solo un punto de conexión privado en un estado aprobado puede enviar tráfico a un recurso de vínculo privado determinado. 

### <a name="connecting-using-alias"></a>Conexión mediante alias
El alias es un moniker único que se genera cuando el propietario del servicio crea el servicio de vínculo privado detrás de un equilibrador de carga estándar. El propietario del servicio puede compartir este alias con sus consumidores sin conexión. Los consumidores pueden solicitar una conexión al servicio de vínculo privado mediante el URI de recurso o el alias. Si desea conectarse mediante el alias, debe crear un punto de conexión privado mediante el método de aprobación de conexión manual. Para usar el método de aprobación de conexión manual, establezca el parámetro solicitud manual en true durante el flujo de creación del punto de conexión privado. Consulte [New-AzPrivateEndpoint](/powershell/module/az.network/new-azprivateendpoint) y [az network private-endpoint create](/cli/azure/network/private-endpoint#az_network_private_endpoint_create) para obtener más información. 

## <a name="dns-configuration"></a>Configuración de DNS 
Al conectarse a un recurso de vínculo privado mediante un nombre de dominio completo (FQDN) como parte de la cadena de conexión, es importante establecer correctamente la configuración de DNS para que se resuelva en la dirección IP privada asignada. Es posible que los servicios de Azure existentes ya tengan una configuración de DNS que usar al conectarse a través de un punto de conexión público. Esta configuración se debe invalidar para realizar la conexión mediante el punto de conexión privado. 
 
La interfaz de red asociada con el punto de conexión privado contiene el conjunto completo de información necesaria para configurar el DNS, incluidos el FQDN y las direcciones IP privadas dadas para un recurso de vínculo privado. 

Para obtener información detallada y completa acerca de las recomendaciones para configurar DNS para puntos de conexión privados, consulte [Configuración de DNS para puntos de conexión privados](private-endpoint-dns.md).
 
## <a name="limitations"></a>Limitaciones
 
En la tabla siguiente se incluye una lista de las limitaciones conocidas al usar puntos de conexión privados: 

| Limitación | Descripción |Mitigación |
| --------- | --------- | --------- |
| El tráfico destinado a un punto de conexión privado mediante una ruta definida por el usuario puede ser asimétrico. | El tráfico devuelto desde un punto de conexión privado omite una NVA e intenta volver a la máquina virtual de origen. | Para todo el tráfico destinado a un punto de conexión privado a través de una UDR, se recomienda el tráfico SNAT en la NVA para garantizar el enrutamiento simétrico.  |

> [!IMPORTANT]
> La compatibilidad de NSG y UDR con puntos de conexión privados se encuentra en versión preliminar pública.
> Esta versión preliminar se ofrece sin Acuerdo de Nivel de Servicio y no se recomienda para cargas de trabajo de producción. Es posible que algunas características no sean compatibles o que tengan sus funcionalidades limitadas. Para más información, consulte [Términos de uso complementarios de las Versiones Preliminares de Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).



## <a name="next-steps"></a>Pasos siguientes
- [Creación de un punto de conexión privado para Azure Web Apps mediante el portal](create-private-endpoint-portal.md)
- [Creación de un punto de conexión privado para Azure Web Apps mediante PowerShell](create-private-endpoint-powershell.md)
- [Creación de un punto de conexión privado para Azure Web Apps mediante la CLI](create-private-endpoint-cli.md)
- [Creación de un punto de conexión privado para la cuenta de almacenamiento mediante el portal](./tutorial-private-endpoint-storage-portal.md)
- [Creación de un punto de conexión privado para la cuenta de Azure Cosmos mediante el portal](../cosmos-db/how-to-configure-private-endpoints.md)
- [Creación del propio servicio Private Link con Azure PowerShell](create-private-link-service-powershell.md)
- [Creación de un servicio Private Link propio para Azure Database for PostgreSQL: servidor único con el portal](../postgresql/howto-configure-privatelink-portal.md)
- [Creación de su propio servicio Private Link para Azure Database for PostgreSQL: servidor único con la CLI](../postgresql/howto-configure-privatelink-cli.md)
- [Creación de un servicio Private Link propio para Azure Database for MySQL con el portal](../mysql/howto-configure-privatelink-portal.md)
- [Creación de su propio servicio Private Link para Azure Database for MySQL con la CLI](../mysql/howto-configure-privatelink-cli.md)
- [Creación de un servicio Private Link propio para Azure Database for MariaDB con el portal](../mariadb/howto-configure-privatelink-portal.md)
- [Creación de su propio servicio Private Link para Azure Database for MariaDB con la CLI](../mariadb/howto-configure-privatelink-cli.md)
- [Creación de una instancia propia de Private Link para Azure Key Vault con el portal y la CLI](../key-vault/general/private-link-service.md)
