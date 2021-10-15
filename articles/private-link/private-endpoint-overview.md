---
title: ¿Qué es un punto de conexión privado de Azure?
description: Información sobre el punto de conexión privado de Azure
services: private-link
author: asudbring
ms.service: private-link
ms.topic: conceptual
ms.date: 09/09/2021
ms.author: allensu
ms.openlocfilehash: 692bcd4900ae960928a66eaa63b02c6ddf12f2bf
ms.sourcegitcommit: 860f6821bff59caefc71b50810949ceed1431510
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/09/2021
ms.locfileid: "129705282"
---
# <a name="what-is-azure-private-endpoint"></a>¿Qué es un punto de conexión privado de Azure?

Un punto de conexión privado es una interfaz de red que usa una dirección IP privada de la red virtual. Esta interfaz de red le conecta de forma privada y segura a un servicio con la tecnología de Azure Private Link. Al habilitar un punto de conexión privado, incorpora el servicio a la red virtual.

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

Algunos detalles importantes acerca de los puntos de conexión privados: 

- El punto de conexión privado permite la conectividad entre los consumidores desde el mismo servicio:
    
    - Virtual Network
    - Redes virtuales emparejadas regionalmente
    - Redes virtuales emparejadas globalmente
    - Acceso local mediante [VPN](https://azure.microsoft.com/services/vpn-gateway/) o [Express Route](https://azure.microsoft.com/services/expressroute/)
    - Servicios con tecnología de Private Link
 
- Las conexiones de red solo las pueden iniciar los clientes que se conectan al punto de conexión privado. Los proveedores de servicios no tienen configuración de enrutamiento para crear conexiones en los consumidores del servicio. Las conexiones solo se pueden establecer en una dirección.

- Al crear un punto de conexión privado, también se crea una interfaz de red de solo lectura para el ciclo de vida del recurso. A la interfaz se le asigna una dirección IP privada dinámica de la subred que se asigna al recurso de vínculo privado. El valor de la dirección IP privada no cambia durante todo el ciclo de vida del punto de conexión privado.
 
- El punto de conexión privado debe implementarse en la misma región y suscripción que la red virtual. 
 
- El recurso de vínculo privado se puede implementar en una región distinta a la de la red virtual y el punto de conexión privado.
 
- Se pueden crear varios puntos de conexión privados mediante el mismo recurso de vínculo privado. En el caso de una sola red que use una configuración de servidor DNS común, el procedimiento recomendado es usar un único punto de conexión privado para un recurso de vínculo privado determinado. Use esta práctica para evitar entradas duplicadas o conflictos en la resolución DNS. 
 
- Se pueden crear varios puntos de conexión privados en la misma subred o en subredes diferentes dentro de la misma red virtual. Existen límites en cuanto al número de puntos de conexión privados que se pueden crear en una suscripción. Para más información, consulte el artículo acerca de los  [límites de Azure](../azure-resource-manager/management/azure-subscription-service-limits.md#networking-limits).

- La suscripción del recurso de vínculo privado también se debe registrar con Microsoft. Proveedor de recursos de red. Para más información, consulte  [Proveedores de recursos de Azure](../azure-resource-manager/management/resource-providers-and-types.md).
 
## <a name="private-link-resource"></a>Recurso de vínculo privado 
Un recurso de vínculo privado es el destino de un punto de conexión privado determinado. 

En la tabla siguiente se enumeran los recursos disponibles que admiten un punto de conexión privado: 
 
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
| **Azure HDInsight** | Microsoft.HDInsight/clusters | cluster |
| **Azure API for FHIR** | Microsoft.HealthcareApis/services | service |
| **Azure Keyvault HSM** | Microsoft.Keyvault/managedHSMs | HSM |
| **Azure Key Vault** | Microsoft.KeyVault/vaults | almacén |
| **Azure Machine Learning** | Microsoft.MachineLearningServices/workspaces | amlworkspace |
| **Azure Migrate** | Microsoft.Migrate/assessmentProjects | proyecto |
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

Cuando se usan puntos de conexión privados, el tráfico se protege en un recurso de vínculo privado. La plataforma realiza un control de acceso para validar las conexiones de red que solo alcanzan el recurso de vínculo privado especificado. Para acceder a más recursos dentro del mismo servicio de Azure, se requieren puntos de conexión privados adicionales. 
 
Puede impedir por completo que las cargas de trabajo accedan a puntos de conexión públicos para conectarse a un servicio de Azure admitido. Este control proporciona una capa de seguridad de red adicional a los recursos. La seguridad proporciona protección que impide el acceso a otros recursos hospedados en el mismo servicio de Azure. 
 
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

### <a name="connect-with-alias"></a>Conexión con alias

El alias es un moniker único que se genera cuando el propietario del servicio crea el servicio de vínculo privado detrás de un equilibrador de carga estándar. El propietario del servicio puede compartir este alias con sus consumidores sin conexión. 

Los consumidores pueden solicitar una conexión al servicio de vínculo privado mediante el URI de recurso o el alias. Si desea conectarse mediante el alias, debe crear un punto de conexión privado mediante el método de aprobación de conexión manual. Para usar el método de aprobación de conexión manual, establezca el parámetro solicitud manual en true durante el flujo de creación del punto de conexión privado. Consulte [New-AzPrivateEndpoint](/powershell/module/az.network/new-azprivateendpoint) y [az network private-endpoint create](/cli/azure/network/private-endpoint#az_network_private_endpoint_create) para obtener más información.

## <a name="dns-configuration"></a>Configuración de DNS

La configuración de DNS usada para las conexiones a un recurso de vínculo privado es importante. Asegúrese de que la configuración de DNS sea correcta cuando use el nombre de dominio completo (FQDN) para la conexión. La configuración debe resolver la dirección IP privada del punto de conexión privado. Es posible que los servicios de Azure existentes ya tengan una configuración de DNS que usar al conectarse a través de un punto de conexión público. Esta configuración se debe invalidar para realizar la conexión mediante el punto de conexión privado. 
 
La interfaz de red asociada al punto de conexión privado contiene la información necesaria para configurar DNS. La información incluye el FQDN y las direcciones IP privadas del recurso de vínculo privado. 

Para obtener información detallada y completa acerca de las recomendaciones para configurar DNS para puntos de conexión privados, consulte [Configuración de DNS para puntos de conexión privados](private-endpoint-dns.md).
 
## <a name="limitations"></a>Limitaciones
 
En la tabla siguiente se incluye una lista de las limitaciones conocidas al usar puntos de conexión privados: 

| Limitación | Descripción |Mitigación |
| --------- | --------- | --------- |
| El tráfico destinado a un punto de conexión privado mediante una ruta definida por el usuario puede ser asimétrico. | El tráfico devuelto desde un punto de conexión privado omite una aplicación virtual de red (NVA) e intenta volver a la máquina virtual de origen. | La traducción de direcciones de red (SNAT) de origen se usa para garantizar el enrutamiento simétrico. Para todo el tráfico destinado a un punto de conexión privado a través de una UDR, se recomienda el tráfico SNAT en la NVA. |

> [!IMPORTANT]
> La compatibilidad de NSG y UDR con puntos de conexión privados se encuentra en versión preliminar pública en regiones exclusivas. Para obtener más información, vea [Versión preliminar pública de la compatibilidad UDR de Private Link](https://azure.microsoft.com/updates/public-preview-of-private-link-udr-support/) y [Versión preliminar pública de la compatibilidad del grupo de seguridad de red de Private Link](https://azure.microsoft.com/updates/public-preview-of-private-link-network-security-group-support/).
> Esta versión preliminar se ofrece sin Acuerdo de Nivel de Servicio y no se recomienda para cargas de trabajo de producción. Es posible que algunas características no sean compatibles o que tengan sus funcionalidades limitadas. Para más información, consulte [Términos de uso complementarios de las Versiones Preliminares de Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="public-preview-limitations"></a>Limitaciones de la vista preliminar pública

### <a name="nsg"></a>Grupo de seguridad de red

| Limitación | Descripción | Mitigación |
| ---------- | ----------- | ---------- |
| La obtención de rutas y reglas de seguridad eficaces no estará disponible en una interfaz de red de punto de conexión privado. | No puede navegar a la interfaz de red para ver información pertinente sobre las rutas y las reglas de seguridad vigentes. | Q4CY21 |
| No se admiten los registros de flujo de NSG. | Los registros de flujo de NSG no funcionarán para el tráfico entrante destinado a un punto de conexión privado. | No hay información en este momento. |
| Caídas intermitentes con cuentas de almacenamiento de ZRS. | Los clientes que usan la cuenta de almacenamiento de ZRS pueden ver caídas intermitentes periódicas, incluso con la aplicación del grupo de seguridad de red permitido en la subred del punto de conexión privado de almacenamiento. | Septiembre |
| Caídas intermitentes con Azure Key Vault. | Los clientes que usan Azure Key Vault pueden ver caídas intermitentes periódicas, incluso con la aplicación del grupo de seguridad de red permitido en la subred del punto de conexión privado de Azure Key Vault. | Septiembre |
| Limite en el número de prefijos de dirección por NSG. | No se admite tener más de 500 prefijos de dirección en NSG en una sola regla. | Septiembre |
| Marca AllowVirtualNetworkAccess | Los clientes que establecen el emparejamiento de VNet en su red virtual (VNet A) con la marca **AllowVirtualNetworkAccess** establecida en false en el vínculo de emparejamiento a otra red virtual (VNet B) no pueden usar la etiqueta **VirtualNetwork** para denegar el tráfico de la VNet B que accede a los recursos de punto de conexión privado. Deberá colocar explícitamente un bloque para que el prefijo de dirección de la VNET B deniegue el tráfico al punto de conexión privado. | Septiembre |
| No se admiten reglas de NSG de puerto dual. | Si se usan varios intervalos de puertos con reglas de NSG, solo se respeta el primer intervalo de puertos para las reglas de permiso y las reglas de denegación. Las reglas con varios intervalos de puertos tienen como valor predeterminado la denegación de todo en lugar de puertos específicos. </br> **Para obtener más información, vea el ejemplo B a continuación.** | Septiembre |

| Prioridad | Puerto de origen | Puerto de destino | Acción | Acción efectiva |
| -------- | ----------- | ---------------- | ------ | ---------------- |
| 10 | 10-12 | 10-12 | Permitir o denegar | El rango de puertos individuales en los puertos de origen/destino funcionará como se espera. |
| 10 | 10-12, 13-14 | 14-15, 16-17 | Allow | Solo se permitirán los puertos de origen 10-12 y los puertos de destino 14-15. |
| 10 | 10-12, 13-14 | 120-130, 140-150 | Denegar | El tráfico de todos los puertos de origen se denegará a todos los puertos de destino, ya que hay varios intervalos de puertos de origen y destino. |
| 10 | 10-12, 13-14 | 120-130 | Denegar | El tráfico de todos los puertos de origen se denegará solo a los puertos de destino 120-130. Hay varios intervalos de puertos de origen y un único intervalo de puertos de destino. |

**Tabla: regla de puerto dual de ejemplo.**

### <a name="udr"></a>UDR

| Limitación | Descripción | Mitigación |
| ---------- | ----------- | ---------- |
| Siempre se recomienda la traducción de direcciones de red (SNAT) de origen. | Debido a la naturaleza variable del plano de datos del punto de conexión privado, se recomienda usar el tráfico SNAT destinado a un punto de conexión privado para asegurarse de que se respeta el tráfico devuelto. | No hay información en este momento. |
 
## <a name="next-steps"></a>Pasos siguientes

- Para más información sobre el punto de conexión privado y el vínculo privado, consulte [¿Qué es Azure Private Link?](private-link-overview.md)
- Para empezar a crear un punto de conexión privado para una aplicación web, consulte [Inicio rápido: Creación de un punto de conexión privado mediante Azure Portal](create-private-endpoint-portal.md).
