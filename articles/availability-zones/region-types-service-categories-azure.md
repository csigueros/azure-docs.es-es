---
title: Servicios de Azure
description: Obtenga información sobre los tipos de región y las categorías de servicio de Azure.
author: prsandhu
ms.service: azure
ms.topic: conceptual
ms.date: 10/01/2021
ms.author: prsandhu
ms.reviewer: cynthn
ms.custom: references_regions
ms.openlocfilehash: dc0adacbda491f7658056e7d313b9bdc923b43af
ms.sourcegitcommit: 1d56a3ff255f1f72c6315a0588422842dbcbe502
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/06/2021
ms.locfileid: "129620944"
---
# <a name="azure-services"></a>Servicios de Azure

La disponibilidad de los servicios entre regiones de Azure depende del tipo de región. Hay dos tipos de regiones en Azure: *recomendadas* y *alternativas*.

- **Recomendada**: estas regiones proporcionan la gama más amplia de funcionalidades de servicio y actualmente admiten zonas de disponibilidad. En Azure Portal se designan como **Recomendadas**.
- **Alternativa**: estas regiones extienden la superficie de Azure dentro del límite de la residencia de datos donde también existe una región recomendada. Las regiones alternativas ayudan a optimizar la latencia y proporcionan una segunda región para las necesidades de recuperación ante desastres, pero no admiten zonas de disponibilidad. Azure realiza evaluaciones periódicas de regiones alternativas para determinar si deben convertirse en regiones recomendadas. En Azure Portal se designan como **Otras**.

## <a name="service-categories-across-region-types"></a>Categorías de servicio entre tipos de regiones

Los servicios de Azure se agrupan en tres categorías: *fundamentales*, *estándar* y *estratégicos*. La directiva general de Azure sobre la implementación de servicios en una región determinada se basa principalmente en el tipo de región, las categorías de servicio y la demanda del cliente.

- **Fundamentales**: están disponibles en todas las regiones recomendadas y alternativas cuando la región está disponible con carácter general, o bien en un plazo de 90 días después de que un nuevo servicio fundamental esté disponible con carácter general.
- **Estándar**: disponibles en todas las regiones recomendadas en un plazo de 90 días a partir de la disponibilidad general de una región. Controlados por la demanda en regiones alternativas y muchos ya están implementados en un gran subconjunto de regiones alternativas.
- **Estratégicos** (anteriormente Especializados): ofertas de servicio dirigidas, a menudo centradas en el sector o respaldadas por hardware personalizado. Disponibilidad controlada por la demanda entre regiones, y muchos ya están implementados en un gran subconjunto de regiones recomendadas.

Para ver los servicios que se implementan en una región y la futura hoja de ruta de la versión preliminar o la disponibilidad general de los servicios de una región, consulte [Productos disponibles por región](https://azure.microsoft.com/global-infrastructure/services/).

Si una oferta de servicio no está disponible en una región, póngase en contacto con un representante de ventas de Microsoft para obtener más información y explorar las opciones.

| Tipo de región | No regional | Fundamental | Estándar | Estratégico | Zonas de disponibilidad | Residencia de datos |
| --- | --- | --- | --- | --- | --- | --- |
| Recomendado | **S** | **S** | **S** | Basado en la demanda | **S** | **S** |
| Alternativa | **S** | **S** | Basado en la demanda | Basado en la demanda | N/D | **S** |

## <a name="available-services-by-category"></a>Servicios disponibles por categoría

Azure asigna categorías de servicio como fundamentales, estándar y estratégicas en disponibilidad general. Normalmente, los servicios comienzan como un servicio estratégico y se actualizan a estándar y fundamental a medida que crecen la demanda y el uso.

Los servicios de Azure se presentan en las tablas siguientes por categoría. Tenga en cuenta que algunos servicios no son regionales. Esto significa que están disponibles globalmente, independientemente de la región. Para obtener información y una lista completa de servicios no regionales, consulte [Productos disponibles por región](https://azure.microsoft.com/global-infrastructure/services/).

> [!div class="mx-tableFixed"]
> | ![Icono que indica que este servicio es fundamental.](media/icon-foundational.svg) Fundamental                           | ![Icono que indica que este servicio es estándar.](media/icon-mainstream.svg) Estándar                                        | 
> |----------------------------------------|---------------------------------------------------|
> | Cuentas de Azure Storage                 | Azure API Management                              | 
> | Azure Application Gateway              | Azure App Configuration                           | 
> | Azure Backup                           | Azure App Service                                 | 
> | Azure Cosmos DB                        | Azure Automation                                  | 
> | Azure Data Lake Storage Gen2           | Azure Active Directory Domain Services            | 
> | Azure ExpressRoute                     | Azure Bastion                                     | 
> | IP pública de Azure                        | Azure Cache for Redis                             | 
> | Azure SQL Database                     | Azure Cognitive Services                          | 
> | Azure SQL Managed Instance             | Azure Cognitive Services: Computer Vision         | 
> | Disk Storage                           | Azure Cognitive Services: Content Moderator       | 
> | Azure Event Hubs                       | Azure Cognitive Services: Caras                    | 
> | Azure Key Vault                        | Azure Cognitive Services: Text Analytics          | 
> | Azure Load Balancer                    | Explorador de datos de Azure                               | 
> | Azure Service Bus                      | Azure Database for MySQL                          | 
> | Azure Service Fabric                   | Azure Database for PostgreSQL                     | 
> | Azure Storage: niveles de Blob Storage de acceso frecuente y acceso esporádico   | Azure DDoS Protection                       | 
> | Almacenamiento: Managed Disks                 | Azure Firewall                                    | 
> | Conjuntos de escalado de máquinas virtuales de Azure       | Azure Firewall Manager                            | 
> | Azure Virtual Machines                 | Azure Functions                                   | 
> | Máquinas virtuales: Azure Dedicated Host | Azure IoT Hub                                     | 
> | Máquinas virtuales: serie Av2           | Azure Kubernetes Service (AKS)                    | 
> | Máquinas virtuales: serie Bs            | Azure Monitor: Application Insights               | 
> | Máquinas virtuales: serie DSv2          | Azure Monitor: Log Analytics                      | 
> | Máquinas virtuales: serie DSv3          | Azure Private Link                                | 
> | Máquinas virtuales: serie Dv2           | Azure Site Recovery                               | 
> | Máquinas virtuales: serie Dv3           | Azure Synapse Analytics                           |     
> | Máquinas virtuales: serie ESv3          | Azure Batch                                       | 
> | Máquinas virtuales: serie Ev3           | Servicio en la nube de Azure: serie M                     | 
> | Azure Virtual Network                  | Azure Container Instances                         | 
> | Azure VPN Gateway                      | Azure Container Registry                          | 
> |                                        | Azure Data Factory                                | 
> |                                        | Azure Event Grid                                  | 
> |                                        | HDInsight de Azure                                   |  
> |                                        | Azure Logic Apps                                  | 
> |                                        | Azure Media Services                              | 
> |                                        | Azure Network Watcher                             | 
> |                                        | Premium Blob Storage                              | 
> |                                        | Premium Files Storage                             | 
> |                                        | Máquinas virtuales: serie Ddsv4                    | 
> |                                        | Máquinas virtuales: serie Ddv4                     | 
> |                                        | Máquinas virtuales: serie Dsv4                     | 
> |                                        | Máquinas virtuales: serie Dv4                      | 
> |                                        | Máquinas virtuales: serie Edsv4                    | 
> |                                        | Máquinas virtuales: serie Edv4                     | 
> |                                        | Máquinas virtuales: serie Esv4                     | 
> |                                        | Máquinas virtuales: serie Ev4                      | 
> |                                        | Máquinas virtuales: serie Fsv2                     | 
> |                                        | Máquinas virtuales: serie M                        | 
> |                                        | Azure Virtual WAN                                 | 

### <a name="strategic-services"></a>Servicios estratégicos
Como se mencionó anteriormente, Azure clasifica los servicios en tres categorías: fundamental, estándar y estratégico. Las categorías de servicio se asignan en la disponibilidad general. A menudo, los servicios inician su ciclo de vida como un servicio estratégico y, a medida que aumenta la demanda y el uso, se pueden promocionar a estándar o fundamental. En esta tabla se muestran los servicios estratégicos. 

> [!div class="mx-tableFixed"]
> | ![Icono que indica que este servicio es estratégico.](media/icon-strategic.svg) Estratégico                                          |
> |------------------------------------------------------|
> | API de Azure para FHIR                                   |
> | Azure Analysis Services                              |
> | Azure Blockchain Service                             |
> | Azure Cognitive Services: Anomaly Detector           |
> | Azure Cognitive Services: Custom Vision              |
> | Azure Cognitive Services: Form Recognizer            |
> | Azure Cognitive Services: Lector inmersivo           |
> | Azure Cognitive Services: Language Understanding     |
> | Azure Cognitive Services: Personalizer               |
> | Azure Cognitive Services: QnA Maker                  |
> | Azure Cognitive Services: Speech Services            |
> | Azure Data Share                                     |
> | Azure Databricks                                     |
> | Azure Database for MariaDB                           |
> | Azure Database Migration Service                     |
> | Azure Dedicated HSM                                  |
> | Azure Digital Twins                                  |
> | Azure Health Bot                                     |
> | Azure HPC Cache                                      |
> | Azure Lab Services                                   |
> | Azure NetApp Files                                   |
> | Red Hat OpenShift en Azure                              |
> | Servicio Azure SignalR                                |
> | Azure Spring Cloud                                   |
> | Azure Stream Analytics                               |
> | Azure Time Series Insights                           |
> | Azure VMware Solution                                |
> | Azure VMware Solution by CloudSimple                 |
> | Azure Spatial Anchors                                |
> | Almacenamiento: Archive Storage                             |
> | Almacenamiento en disco Ultra de Azure                             |
> | Video Indexer                                        |
> | Máquinas virtuales: serie DASv4                       |
> | Máquinas virtuales: serie DAv4                        |
> | Máquinas virtuales: serie DCsv2                       |
> | Máquinas virtuales: serie EASv4                       |
> | Máquinas virtuales: serie EAv4                        |
> | Máquinas virtuales: serie HBv1                        |
> | Máquinas virtuales: serie HBv2                        |
> | Máquinas virtuales: serie HCv1                        |
> | Máquinas virtuales: serie H                           |
> | Máquinas virtuales: serie LSv2                        |
> | Máquinas virtuales: serie Mv2                         |
> | Máquinas virtuales: serie NCv3                        |
> | Máquinas virtuales: serie NDv2                        |
> | Máquinas virtuales: serie NVv3                        |
> | Máquinas virtuales: serie NVv4                        | 
> | Virtual Machines: SAP HANA en Azure (instancias grandes)  |

\*Máquinas virtuales compatibles con zonas de disponibilidad: serie AV2, serie B, serie DSv2, serie DSv3, serie Dv2, serie Dv3, serie ESv3, serie Ev3, serie F, serie FS, serie FSv2 y serie M.\*

Las generaciones anteriores de servicios o máquinas virtuales no aparecen en la lista. Para más información, consulte [Generaciones anteriores de tamaños de máquina virtual](../virtual-machines/sizes-previous-gen.md).

Para más información sobre los servicios en versión preliminar que aún no están en disponibilidad general y para ver una lista de estos servicios, consulte [Productos disponibles por región](https://azure.microsoft.com/global-infrastructure/services/). Para obtener una lista completa de los servicios que admiten zonas de disponibilidad, consulte [Servicios de Azure que admiten zonas de disponibilidad](az-region.md).

## <a name="next-steps"></a>Pasos siguientes

- [Servicios de Azure compatibles con las zonas de disponibilidad](az-region.md)
- [Regiones y Availability Zones en Azure](az-overview.md)
