---
title: Servicios de Azure compatibles con zonas de disponibilidad
description: Obtenga información sobre qué servicios son compatibles con zonas de disponibilidad y comprenda la resistencia en todos los servicios de Azure.
author: prsandhu
ms.service: azure
ms.topic: conceptual
ms.date: 10/01/2021
ms.author: prsandhu
ms.reviewer: cynthn
ms.custom: references_regions
ms.openlocfilehash: e7c8ce91f15a6b92e3efa9bae8c5a78bc9fd4baa
ms.sourcegitcommit: 147910fb817d93e0e53a36bb8d476207a2dd9e5e
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/18/2021
ms.locfileid: "130129061"
---
# <a name="azure-services-that-support-availability-zones"></a>Servicios de Azure compatibles con zonas de disponibilidad

Las [regiones y las zonas de disponibilidad de Azure](az-overview.md) son ubicaciones físicamente independientes dentro de cada región de Azure que son tolerantes a los errores del centro de datos debido a la infraestructura redundante y el aislamiento lógico de los servicios de Azure. 

Las zonas de disponibilidad tienen que ver con la redundancia de red. Los servicios de Azure que admiten zonas de disponibilidad están diseñados para proporcionar el nivel adecuado de resistencia y flexibilidad junto con una latencia ultrabaja. Con los servicios de Azure que admiten zonas de disponibilidad, tanto si diseña su propia resistencia u opta por la replicación y distribución automáticas, la ventaja es la misma. Obtiene una resistencia superior en los servicios de alta disponibilidad, independientemente del tipo de servicio. 

Azure se esfuerza por habilitar una alta resistencia en todos los servicios y ofertas. La ejecución de servicios de Azure que admiten zonas de disponibilidad proporciona una resistencia totalmente transparente y coherente en casi todos los escenarios, sin interrupción.

## <a name="azure-regions-with-availability-zones"></a>Regiones de Azure con zonas de disponibilidad

Azure proporciona la superficie global más amplia de cualquier proveedor de nube y está abriendo rápidamente nuevas regiones y zonas de disponibilidad. Las siguientes regiones admiten actualmente zonas de disponibilidad.

| América | Europa | África | Asia Pacífico |
|--------------------|----------------------|---------------------|----------------|
| Sur de Brasil | Centro de Francia | Norte de Sudáfrica | Este de Australia |
| Centro de Canadá | Centro-oeste de Alemania | | Centro de la India\* |
| Centro de EE. UU. | Norte de Europa | | Japón Oriental |
| Este de EE. UU. | Este de Noruega | | Centro de Corea del Sur |
| Este de EE. UU. 2 | Sur de Reino Unido 2 | | Sudeste de Asia |
| Centro-sur de EE. UU. | Oeste de Europa | | Este de Asia* |
| US Gov - Virginia | Suecia* | | |
| Oeste de EE. UU. 2 | | | |
| Oeste de EE. UU. 3 | | | |

\* Para obtener más información sobre la compatibilidad con las zonas de disponibilidad y los servicios disponibles en esta región, póngase en contacto con el representante de ventas o de clientes de Microsoft. Para obtener información sobre las próximas regiones que van a admitir zonas de disponibilidad, consulte [Zonas geográficas de Azure](https://azure.microsoft.com/global-infrastructure/geographies/).

Para obtener una lista de los servicios de Azure que admiten zonas de disponibilidad por región de Azure, consulte la [documentación de zonas de disponibilidad](az-overview.md).

## <a name="highly-available-services"></a>Servicios de alta disponibilidad

Tres tipos de servicios de Azure admiten zonas de disponibilidad: *zonales*, *con redundancia de zona* y *siempre disponibles*. Puede combinar los tres enfoques de arquitectura al diseñar la estrategia de resistencia.

- **Servicios zonales**: un recurso se puede implementar en una zona de disponibilidad específica y seleccionada automáticamente para lograr unos requisitos de rendimiento o latencia más rigurosos. La resistencia se ha diseñado automáticamente mediante la replicación de aplicaciones y datos en una o más zonas dentro de la región. Los recursos se pueden anclar a una zona específica. Puede anclar recursos (por ejemplo, máquinas virtuales, discos administrados o direcciones IP estándar) a una zona específica, lo que permite una mayor resistencia al tener una o más instancias de recursos distribuidas entre zonas.
- **Servicios con redundancia de zona**: los recursos se replican o distribuyen entre zonas automáticamente. Por ejemplo, los servicios con redundancia de zona replican los datos en tres zonas para que un error en una zona no afecte a la alta disponibilidad de los datos. 
- **Servicios siempre disponibles**: siempre están disponibles en todas las zonas geográficas de Azure y son resistentes a interrupciones en toda la zona y a interrupciones en toda la región. Para obtener una lista completa de los servicios siempre disponibles, también denominados servicios no regionales, en Azure, consulte [Productos disponibles por región](https://azure.microsoft.com/global-infrastructure/services/).

Como se mencionó anteriormente en este artículo, no todos los servicios de Azure están disponibles universalmente en todas las regiones. Las ofertas se agrupan en tres categorías que reflejan su disponibilidad _regional_: servicios *fundamentales*, *estándar* y *estratégicos*. La directiva general de Azure sobre la implementación de servicios en una región determinada se basa principalmente en el tipo de región, la categoría de servicio y la demanda del cliente. Para obtener más información, consulte [Servicios de Azure](region-types-service-categories-azure.md).

- **Servicios fundamentales**: están disponibles en todas las regiones recomendadas y alternativas cuando una región está disponible con carácter general, o en un plazo de 90 días después de que un nuevo servicio fundamental esté disponible con carácter general.
- **Servicios estándar**: disponibles en todas las regiones recomendadas en un plazo de 90 días a partir de la disponibilidad general de una región. Los servicios estándar están controlados por la demanda en regiones alternativas y muchos ya están implementados en un gran subconjunto de regiones alternativas.
- **Servicios estratégicos**: ofertas de servicio dirigidas, a menudo centradas en el sector o respaldadas por hardware personalizado. Los servicios estratégicos están controlados por la demanda para la disponibilidad entre regiones y muchos ya están implementados en un gran subconjunto de regiones recomendadas.

Los servicios de Azure que admiten zonas de disponibilidad, incluidas las ofertas zonales y con redundancia de zona, se expanden continuamente.

Para obtener más información sobre máquina virtual de generaciones anteriores, consulte [Generaciones anteriores de tamaños de máquina virtual](/azure/virtual-machines/sizes-previous-gen).

En las tablas siguientes se proporciona un resumen de la oferta actual de servicios de Azure zonales, con redundancia de zona y siempre disponibles. Se enumeran las ofertas de Azure según la disponibilidad regional de cada una de ellas.

##### <a name="legend"></a>Leyenda
![Leyenda que contiene los iconos y el significado con respecto a la categoría de servicio y la disponibilidad regional de cada servicio de la tabla.](media/legend.png) 

En el catálogo de productos, los servicios siempre disponibles se enumeran como servicios "no regionales".

### <a name="an-icon-that-signifies-this-service-is-foundational-foundational-services"></a>![Icono que indica que este servicio es fundamental.](media/icon-foundational.svg) Servicios fundamentales 

| **Productos**   | **Resistencia**   |
| --- | --- |
| [Azure Application Gateway (V2)](../application-gateway/application-gateway-autoscaling-zone-redundant.md)  | ![Icono que indica que este servicio tiene redundancia de zona.](media/icon-zone-redundant.svg) ![Icono que indica que este servicio es zonal.](media/icon-zonal.svg)  |
| [Azure Backup](/azure/backup/backup-create-rs-vault)  | ![Icono que indica que este servicio es zonal.](media/icon-zonal.svg)  |
| [Azure Cosmos DB](../cosmos-db/high-availability.md#availability-zone-support)  | ![Icono que indica que este servicio tiene redundancia de zona.](media/icon-zone-redundant.svg)   |
| [Azure Data Lake Storage Gen 2](../storage/blobs/data-lake-storage-introduction.md)  | ![Icono que indica que este servicio tiene redundancia de zona.](media/icon-zone-redundant.svg)  |
| [Azure DNS: Azure DNS Private Zones](../dns/private-dns-getstarted-portal.md)  | ![Icono que indica que este servicio tiene redundancia de zona.](media/icon-zone-redundant.svg)  |
| [Información técnica de ExpressRoute](../expressroute/designing-for-high-availability-with-expressroute.md)  | ![Icono que indica que este servicio tiene redundancia de zona.](media/icon-zone-redundant.svg)  |
| [IP pública de Azure](../virtual-network/public-ip-addresses.md)  | ![Icono que indica que este servicio tiene redundancia de zona.](media/icon-zone-redundant.svg) ![Icono que indica que este servicio es zonal.](media/icon-zonal.svg)  |
| Azure SQL Database ([nivel de uso general](../azure-sql/database/high-availability-sla.md))  | ![Icono que indica que este servicio tiene redundancia de zona.](media/icon-zone-redundant.svg)  |
| Azure SQL Database ([niveles Premium y Crítico para la empresa&amp;](../azure-sql/database/high-availability-sla.md))  | ![Icono que indica que este servicio tiene redundancia de zona.](media/icon-zone-redundant.svg)  |
| [Azure Disk Storage](../storage/common/storage-redundancy.md)  | ![Icono que indica que este servicio tiene redundancia de zona.](media/icon-zone-redundant.svg)  |
| [Azure Event Hubs](../event-hubs/event-hubs-geo-dr.md#availability-zones)  | ![Icono que indica que este servicio tiene redundancia de zona.](media/icon-zone-redundant.svg)  |
| [Azure Key Vault](../key-vault/general/disaster-recovery-guidance.md)  | ![Icono que indica que este servicio tiene redundancia de zona.](media/icon-zone-redundant.svg)  |
| [Equilibrador de carga de Azure](../load-balancer/load-balancer-standard-availability-zones.md)  | ![Icono que indica que este servicio tiene redundancia de zona.](media/icon-zone-redundant.svg) ![Icono que indica que este servicio es zonal.](media/icon-zonal.svg)  |
| [Azure Service Bus](../service-bus-messaging/service-bus-geo-dr.md#availability-zones)  | ![Icono que indica que este servicio tiene redundancia de zona.](media/icon-zone-redundant.svg)  |
| [Azure Service Fabric](../service-fabric/service-fabric-cross-availability-zones.md)  | ![Icono que indica que este servicio tiene redundancia de zona.](media/icon-zone-redundant.svg) ![Icono que indica que este servicio es zonal.](media/icon-zonal.svg)  |
| [Cuenta de Azure Storage](../storage/common/storage-redundancy.md)  | ![Icono que indica que este servicio tiene redundancia de zona.](media/icon-zone-redundant.svg)  |
| Storage:  [niveles de Azure Blob Storage de acceso frecuente y de acceso esporádico](../storage/common/storage-redundancy.md)  | ![Icono que indica que este servicio tiene redundancia de zona.](media/icon-zone-redundant.svg)  |
| Storage:  [Managed Disks](../virtual-machines/managed-disks-overview.md)  | ![Icono que indica que este servicio tiene redundancia de zona.](media/icon-zone-redundant.svg) ![Icono que indica que este servicio es zonal.](media/icon-zonal.svg)  |
| [Azure Virtual Machine Scale Sets](../virtual-machine-scale-sets/scripts/cli-sample-zone-redundant-scale-set.md)  | ![Icono que indica que este servicio tiene redundancia de zona.](media/icon-zone-redundant.svg) ![Icono que indica que este servicio es zonal.](media/icon-zonal.svg)  |
| [Azure Virtual Machines](../virtual-machines/windows/create-powershell-availability-zone.md)  | ![Icono que indica que este servicio es zonal.](media/icon-zonal.svg)  |
| Virtual Machines:  [Serie Av2](../virtual-machines/windows/create-powershell-availability-zone.md)  | ![Icono que indica que este servicio es zonal.](media/icon-zonal.svg)  |
| Virtual Machines:  [Serie Bs](../virtual-machines/windows/create-powershell-availability-zone.md)  | ![Icono que indica que este servicio es zonal.](media/icon-zonal.svg)  |
| Virtual Machines:  [Serie DSv2](../virtual-machines/windows/create-powershell-availability-zone.md)  | ![Icono que indica que este servicio es zonal.](media/icon-zonal.svg)  |
| Virtual Machines:  [Serie DSv3](../virtual-machines/windows/create-powershell-availability-zone.md)  | ![Icono que indica que este servicio es zonal.](media/icon-zonal.svg)  |
| Virtual Machines:  [Serie Dv2](../virtual-machines/windows/create-powershell-availability-zone.md)  | ![Icono que indica que este servicio es zonal.](media/icon-zonal.svg)  |
| Virtual Machines:  [Serie Dv3](../virtual-machines/windows/create-powershell-availability-zone.md)  | ![Icono que indica que este servicio es zonal.](media/icon-zonal.svg)  |
| Virtual Machines: Serie  [ESv3](../virtual-machines/windows/create-powershell-availability-zone.md)  | ![Icono que indica que este servicio es zonal.](media/icon-zonal.svg)  |
| Virtual Machines:  [Serie Ev3](../virtual-machines/windows/create-powershell-availability-zone.md)  | ![Icono que indica que este servicio es zonal.](media/icon-zonal.svg)  |
| Virtual Machines:  [Serie F](../virtual-machines/windows/create-powershell-availability-zone.md)  | ![Icono que indica que este servicio es zonal.](media/icon-zonal.svg)  |
| Virtual Machines:  [Serie FS](../virtual-machines/windows/create-powershell-availability-zone.md)  | ![Icono que indica que este servicio es zonal.](media/icon-zonal.svg)  |
| Virtual Machines:  [Shared Image Gallery](../virtual-machines/shared-image-galleries.md#make-your-images-highly-available)  | ![Icono que indica que este servicio tiene redundancia de zona.](media/icon-zone-redundant.svg)  |
| [Azure Virtual Network](../vpn-gateway/create-zone-redundant-vnet-gateway.md)  | ![Icono que indica que este servicio tiene redundancia de zona.](media/icon-zone-redundant.svg)  |
| [Acerca de VPN Gateway](../vpn-gateway/about-zone-redundant-vnet-gateways.md)  | ![Icono que indica que este servicio tiene redundancia de zona.](media/icon-zone-redundant.svg)  |
### <a name="an-icon-that-signifies-this-service-is-mainstream-mainstream-services"></a>![Icono que indica que este servicio es estándar.](media/icon-mainstream.svg) Servicio estándar

| **Productos**   | **Resistencia**   |
| --- | --- |
| [App Service](../app-service/how-to-zone-redundancy.md)  | ![Icono que indica que este servicio tiene redundancia de zona.](media/icon-zone-redundant.svg) ![Icono que indica que este servicio es zonal.](media/icon-zonal.svg)  |
| [Entornos de Azure App Service](../app-service/environment/zone-redundancy.md)  | ![Icono que indica que este servicio tiene redundancia de zona.](media/icon-zone-redundant.svg) ![Icono que indica que este servicio es zonal.](media/icon-zonal.svg)  |
| [Azure Active Directory Domain Services](../active-directory-domain-services/overview.md)  | ![Icono que indica que este servicio tiene redundancia de zona.](media/icon-zone-redundant.svg)  |
| [Azure API Management](../api-management/zone-redundancy.md)  | ![Icono que indica que este servicio es zonal.](media/icon-zonal.svg)  |
| [Azure App Configuration](../azure-app-configuration/faq.yml#how-does-app-configuration-ensure-high-data-availability)  | ![Icono que indica que este servicio tiene redundancia de zona.](media/icon-zone-redundant.svg)  |
| [Azure Bastion](../bastion/bastion-overview.md)  | ![Icono que indica que este servicio tiene redundancia de zona.](media/icon-zone-redundant.svg)  |
| [Azure Batch](../batch/create-pool-availability-zones.md)  | ![Icono que indica que este servicio tiene redundancia de zona.](media/icon-zone-redundant.svg)  |
| [Azure Cache for Redis](../azure-cache-for-redis/cache-high-availability.md)  | ![Icono que indica que este servicio tiene redundancia de zona.](media/icon-zone-redundant.svg) ![Icono que indica que este servicio es zonal.](media/icon-zonal.svg)  |
| [Azure Cognitive Search](../search/search-performance-optimization.md#availability-zones)  | ![Icono que indica que este servicio tiene redundancia de zona.](media/icon-zone-redundant.svg)  |
| Azure Cognitive Services:  [Text Analytics](../cognitive-services/text-analytics/index.yml)  | ![Icono que indica que este servicio tiene redundancia de zona.](media/icon-zone-redundant.svg)  |
| [Azure Data Explorer](/azure/data-explorer/create-cluster-database-portal)  | ![Icono que indica que este servicio tiene redundancia de zona.](media/icon-zone-redundant.svg)  |
| [Azure Data Factory](../data-factory/index.yml)  | ![Icono que indica que este servicio tiene redundancia de zona.](media/icon-zone-redundant.svg)  |
| Azure Database for MySQL:  [servidor flexible](../mysql/flexible-server/concepts-high-availability.md)  | ![Icono que indica que este servicio tiene redundancia de zona.](media/icon-zone-redundant.svg)  |
| Azure Database for PostgreSQL:  [servidor flexible](../postgresql/flexible-server/overview.md)  | ![Icono que indica que este servicio tiene redundancia de zona.](media/icon-zone-redundant.svg)  |
| [Azure DDoS Protection](../ddos-protection/ddos-faq.yml)  | ![Icono que indica que este servicio tiene redundancia de zona.](media/icon-zone-redundant.svg) |
| [Azure Disk Encryption](../virtual-machines/disks-redundancy.md)  | ![Icono que indica que este servicio tiene redundancia de zona.](media/icon-zone-redundant.svg) |
| [Azure Firewall](../firewall/deploy-availability-zone-powershell.md)  | ![Icono que indica que este servicio tiene redundancia de zona.](media/icon-zone-redundant.svg)  |
| [Azure Firewall Manager](../firewall-manager/quick-firewall-policy.md)  | ![Icono que indica que este servicio tiene redundancia de zona.](media/icon-zone-redundant.svg)  |
| [Funciones de Azure](https://azure.github.io/AppService/2021/08/25/App-service-support-for-availability-zones.html)  | ![Icono que indica que este servicio tiene redundancia de zona.](media/icon-zone-redundant.svg)  |
| [Azure Kubernetes Service (AKS)](../aks/availability-zones.md)  | ![Icono que indica que este servicio tiene redundancia de zona.](media/icon-zone-redundant.svg) ![Icono que indica que este servicio es zonal.](media/icon-zonal.svg)  |
| [Azure Monitor](../azure-monitor/logs/availability-zones.md)  | ![Icono que indica que este servicio tiene redundancia de zona.](media/icon-zone-redundant.svg)  |
| [Azure Monitor: Application Insights](../azure-monitor/logs/availability-zones.md)  | ![Icono que indica que este servicio tiene redundancia de zona.](media/icon-zone-redundant.svg)  |
| [Azure Monitor: Log Analytics](../azure-monitor/logs/availability-zones.md)  | ![Icono que indica que este servicio tiene redundancia de zona.](media/icon-zone-redundant.svg)  |
| [Azure Private Link](../private-link/private-link-overview.md)  | ![Icono que indica que este servicio tiene redundancia de zona.](media/icon-zone-redundant.svg)  |
| [Azure Site Recovery](../site-recovery/azure-to-azure-how-to-enable-zone-to-zone-disaster-recovery.md)  | ![Icono que indica que este servicio es zonal.](media/icon-zonal.svg)  |
| Azure SQL:  [máquina virtual](../azure-sql/database/high-availability-sla.md)  | ![Icono que indica que este servicio tiene redundancia de zona.](media/icon-zone-redundant.svg) |
| [Firewall de aplicaciones web de Azure](../firewall/deploy-availability-zone-powershell.md)  | ![Icono que indica que este servicio tiene redundancia de zona.](media/icon-zone-redundant.svg)  |
| [Azure Container Registry](../container-registry/zone-redundancy.md)  | ![Icono que indica que este servicio tiene redundancia de zona.](media/icon-zone-redundant.svg)  |
| [Azure Event Grid](../event-grid/overview.md)  | ![Icono que indica que este servicio tiene redundancia de zona.](media/icon-zone-redundant.svg)  |
| [Azure HDInsight](../hdinsight/hdinsight-use-availability-zones.md)  | ![Icono que indica que este servicio es zonal.](media/icon-zonal.svg)  |
| [Azure Network Watcher](../network-watcher/frequently-asked-questions.yml)  | ![Icono que indica que este servicio tiene redundancia de zona.](media/icon-zone-redundant.svg)  |
| Network Watcher:  [Análisis de tráfico](../network-watcher/frequently-asked-questions.yml)  | ![Icono que indica que este servicio tiene redundancia de zona.](media/icon-zone-redundant.svg)  |
| [¿Qué es Power BI Embedded de Azure?](/power-bi/admin/service-admin-failover#what-does-high-availability)  | ![Icono que indica que este servicio tiene redundancia de zona.](media/icon-zone-redundant.svg)  |
| [Premium Blob Storage](../storage/blobs/storage-blob-performance-tiers.md)  | ![Icono que indica que este servicio tiene redundancia de zona.](media/icon-zone-redundant.svg)  |
| Almacenamiento:  [Azure Premium Files](../storage/files/storage-files-planning.md)  | ![Icono que indica que este servicio tiene redundancia de zona.](media/icon-zone-redundant.svg)  |
| Virtual Machines:  [Azure Dedicated Host](../virtual-machines/windows/create-powershell-availability-zone.md)  | ![Icono que indica que este servicio es zonal.](media/icon-zonal.svg)  |
| Virtual Machines:  [Serie Ddsv4](../virtual-machines/windows/create-powershell-availability-zone.md)  | ![Icono que indica que este servicio es zonal.](media/icon-zonal.svg)  |
| Virtual Machines:  [Serie Ddv4](../virtual-machines/windows/create-powershell-availability-zone.md)  | ![Icono que indica que este servicio es zonal.](media/icon-zonal.svg)  |
| Virtual Machines:  [Serie Dsv4](../virtual-machines/windows/create-powershell-availability-zone.md)  | ![Icono que indica que este servicio es zonal.](media/icon-zonal.svg)  |
| Virtual Machines:  [Serie Dv4](../virtual-machines/windows/create-powershell-availability-zone.md)  | ![Icono que indica que este servicio es zonal.](media/icon-zonal.svg)  |
| Virtual Machines:  [Serie Edsv4](../virtual-machines/windows/create-powershell-availability-zone.md)  | ![Icono que indica que este servicio es zonal.](media/icon-zonal.svg)  |
| Virtual Machines:  [Serie Edv4](../virtual-machines/windows/create-powershell-availability-zone.md)  | ![Icono que indica que este servicio es zonal.](media/icon-zonal.svg)  |
| Virtual Machines:  [Serie Esv4](../virtual-machines/windows/create-powershell-availability-zone.md)  | ![Icono que indica que este servicio es zonal.](media/icon-zonal.svg)  |
| Virtual Machines:  [Serie Ev4](../virtual-machines/windows/create-powershell-availability-zone.md)  | ![Icono que indica que este servicio es zonal.](media/icon-zonal.svg)  |
| Virtual Machines:  [Serie Fsv2](../virtual-machines/windows/create-powershell-availability-zone.md)  | ![Icono que indica que este servicio es zonal.](media/icon-zonal.svg)  |
| Virtual Machines:  [Serie M](../virtual-machines/windows/create-powershell-availability-zone.md)  | ![Icono que indica que este servicio es zonal.](media/icon-zonal.svg)  |
| [Azure Virtual WAN](../virtual-wan/virtual-wan-faq.md#how-are-availability-zones-and-resiliency-handled-in-virtual-wan)  | ![Icono que indica que este servicio tiene redundancia de zona.](media/icon-zone-redundant.svg)  |
| Virtual WAN:  [Azure ExpressRoute](../virtual-wan/virtual-wan-faq.md#how-are-availability-zones-and-resiliency-handled-in-virtual-wan)  | ![Icono que indica que este servicio tiene redundancia de zona.](media/icon-zone-redundant.svg)  |
| Virtual WAN:  [VPN Gateway de punto a sitio](../vpn-gateway/about-zone-redundant-vnet-gateways.md)  | ![Icono que indica que este servicio tiene redundancia de zona.](media/icon-zone-redundant.svg)  |
| Virtual WAN:  [VPN Gateway de sitio a sitio](../vpn-gateway/about-zone-redundant-vnet-gateways.md)  | ![Icono que indica que este servicio tiene redundancia de zona.](media/icon-zone-redundant.svg)  |

### <a name="an-icon-that-signifies-this-service-is-strategic-strategic-services"></a>![Icono que indica que este servicio es estratégico.](media/icon-strategic.svg) Servicios estratégicos

| **Productos**   | **Resistencia**   |
| --- | --- |
| Red Hat OpenShift en Azure  | ![Icono que indica que este servicio tiene redundancia de zona.](media/icon-zone-redundant.svg) ![Icono que indica que este servicio es zonal.](media/icon-zonal.svg)  |
| Azure Cognitive Services: Anomaly Detector  | ![Icono que indica que este servicio tiene redundancia de zona.](media/icon-zone-redundant.svg)  |
| Azure Cognitive Services: Form Recognizer  | ![Icono que indica que este servicio tiene redundancia de zona.](media/icon-zone-redundant.svg)  |
| Azure Storage: Disco Ultra  | ![Icono que indica que este servicio es zonal.](media/icon-zonal.svg)  |

### <a name="an-icon-that-signifies-this-service-is-non-regional-non-regional-services-always-available-services"></a>![Icono que indica que este servicio no es regional.](media/icon-always-available.svg) Servicios no regionales (servicios siempre disponibles)

| **Productos**   | **Resistencia**   |
| --- | --- |
| Azure DNS  | ![Icono que indica que este servicio siempre está disponible.](media/icon-always-available.svg) |
| Azure Active Directory  | ![Icono que indica que este servicio siempre está disponible.](media/icon-always-available.svg) |
| Azure Advanced Threat Protection  | ![Icono que indica que este servicio siempre está disponible.](media/icon-always-available.svg) |
| Azure Advisor  | ![Icono que indica que este servicio siempre está disponible.](media/icon-always-available.svg) |
| Azure Blueprint  | ![Icono que indica que este servicio siempre está disponible.](media/icon-always-available.svg) |
| Azure Bot Services  | ![Icono que indica que este servicio siempre está disponible.](media/icon-always-available.svg) |
| Azure Front Door  | ![Icono que indica que este servicio siempre está disponible.](media/icon-always-available.svg) |
| Azure Defender para IoT  | ![Icono que indica que este servicio siempre está disponible.](media/icon-always-available.svg) |
| Azure Front Door  | ![Icono que indica que este servicio siempre está disponible.](media/icon-always-available.svg) |
| Azure Information Protection  | ![Icono que indica que este servicio siempre está disponible.](media/icon-always-available.svg) |
| Azure Lighthouse  | ![Icono que indica que este servicio siempre está disponible.](media/icon-always-available.svg) |
| Azure Managed Applications  | ![Icono que indica que este servicio siempre está disponible.](media/icon-always-available.svg) |
| Azure Maps  | ![Icono que indica que este servicio siempre está disponible.](media/icon-always-available.svg) |
| Azure Performance Diagnostics  | ![Icono que indica que este servicio siempre está disponible.](media/icon-always-available.svg) |
| Azure Policy  | ![Icono que indica que este servicio siempre está disponible.](media/icon-always-available.svg) |
| Azure Resource Graph  | ![Icono que indica que este servicio siempre está disponible.](media/icon-always-available.svg) |
| Azure Sentinel  | ![Icono que indica que este servicio siempre está disponible.](media/icon-always-available.svg) |
| Azure Stack  | ![Icono que indica que este servicio siempre está disponible.](media/icon-always-available.svg) |
| Azure Stack Edge  | ![Icono que indica que este servicio siempre está disponible.](media/icon-always-available.svg) |
| Azure Cloud Shell  | ![Icono que indica que este servicio siempre está disponible.](media/icon-always-available.svg) |
| Azure Content Delivery Network  | ![Icono que indica que este servicio siempre está disponible.](media/icon-always-available.svg) |
| Administración de costos y facturación de Azure | ![Icono que indica que este servicio siempre está disponible.](media/icon-always-available.svg) |
| Caja de seguridad del cliente de Microsoft Azure  | ![Icono que indica que este servicio siempre está disponible.](media/icon-always-available.svg) |
| Microsoft Intune  | ![Icono que indica que este servicio siempre está disponible.](media/icon-always-available.svg) |
| Azure Peering Service  | ![Icono que indica que este servicio siempre está disponible.](media/icon-always-available.svg) |
| Azure portal  | ![Icono que indica que este servicio siempre está disponible.](media/icon-always-available.svg) |
| Microsoft Cloud App Security  | ![Icono que indica que este servicio siempre está disponible.](media/icon-always-available.svg) |
| Microsoft Graph  | ![Icono que indica que este servicio siempre está disponible.](media/icon-always-available.svg) |
| Azure Security Center  | ![Icono que indica que este servicio siempre está disponible.](media/icon-always-available.svg) |
| Administrador de tráfico de Azure  | ![Icono que indica que este servicio siempre está disponible.](media/icon-always-available.svg) |

Para obtener una lista de los servicios de Azure que admiten zonas de disponibilidad por región de Azure, consulte la [documentación de zonas de disponibilidad](az-overview.md).

## <a name="pricing-for-virtual-machines-in-availability-zones"></a>Precios de máquinas virtuales en zonas de disponibilidad

Puede acceder a Azure Availability Zones mediante su suscripción de Azure. Para más información, consulte [Precios de ancho de banda](https://azure.microsoft.com/pricing/details/bandwidth/).

## <a name="next-steps"></a>Pasos siguientes

- [Compilación de soluciones para alta disponibilidad mediante zonas de disponibilidad](/azure/architecture/high-availability/building-solutions-for-high-availability)
- [Alta disponibilidad con servicios de Azure](/azure/architecture/framework/resiliency/overview)
- [Patrones de diseño de alta disponibilidad](/azure/architecture/framework/resiliency/app-design)
