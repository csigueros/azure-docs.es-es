---
title: Residencia y resistencia de datos para Azure Stack Edge Pro con GPU/Pro R/Mini R
description: Describe la posición de residencia de datos para Azure Stack Edge.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: conceptual
ms.date: 07/30/2021
ms.author: alkohli
ms.openlocfilehash: 6edb7aafdee046f2dd198d54e566c69b791cc1eb
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/13/2021
ms.locfileid: "121780196"
---
# <a name="data-residency-and-resiliency-for-azure-stack-edge-preview"></a>Residencia y resistencia de datos para Azure Stack Edge (versión preliminar)

[!INCLUDE [applies-to-GPU-and-pro-r-and-mini-r-skus](../../includes/azure-stack-edge-applies-to-gpu-pro-r-mini-r-sku.md)]

En este artículo se describe la información que necesita para ayudar a comprender el comportamiento de residencia y resistencia de datos de Azure Stack Edge y cómo habilitar la residencia de datos en el servicio.  

## <a name="about-data-residency-for-azure-stack-edge"></a>Acerca de la residencia de datos para Azure Stack Edge 

El servicio Azure Stack Edge usa [pares regionales de Azure](../best-practices-availability-paired-regions.md#azure-regional-pairs) al almacenar y procesar datos de clientes en todas las ubicaciones geográficas donde el servicio está disponible. Para la región Sudeste Asiático (Singapur), el servicio está emparejado actualmente con Hong Kong. El emparejamiento de regiones de Azure implica que los datos almacenados en Singapur se replican en Hong Kong. Singapur tiene leyes vigentes que exigen que los datos de los clientes no traspasen las fronteras del país. 

Para asegurarse de que los datos de los clientes residan en una sola región, se ha habilitado una nueva opción en el servicio Azure Stack Edge. Cuando esta opción está seleccionada, permite que el servicio almacene y procese los datos del cliente solo en la región de Singapur. Los datos de los clientes no se replican en Hong Kong. Hay metadatos específicos del servicio (que no son datos confidenciales) que aún se replican en la región emparejada.  

Con esta opción habilitada, el servicio es resistente a las interrupciones de toda la zona, pero no a las interrupciones en toda la región. Si le importan las interrupciones en toda la región, tiene que seleccionar la replicación basada en el par regional.

La opción de residencia de datos de una sola región está disponible únicamente para Sudeste Asiático (Singapur). Para todas las demás regiones, Azure Stack Edge almacena y procesa los datos de los clientes en la ubicación geográfica especificada por el cliente.

La posición de residencia de datos de Azure Stack Edge se puede resumir en los siguientes aspectos del servicio:

- Servicio existente de pedidos y administración de Azure Stack Edge.
- Nuevo Azure Edge Hardware Center (versión preliminar) que se usará para los nuevos pedidos en el futuro.
<!--- Telemetry for the device and the service.
- Proactive Support log collection where any logs that the service generates are stored in a single region and are not replicated to the paired region.-->

El servicio Azure Stack Edge también se integra en los siguientes servicios dependientes, y su comportamiento también se resume: 

- Kubernetes habilitado para Azure Arc
- Azure IoT Hub y Azure IoT Edge
<!--- Azure Key Vault -->


## <a name="azure-stack-edge-classic-ordering-and-management-resource"></a>Recurso clásico de pedidos y administración de Azure Stack Edge 

Si usa la experiencia clásica para hacer un pedido de Azure Stack Edge, el servicio usa actualmente el par regional de Azure para implementar la resistencia de datos frente a interrupciones en toda la región. Para los recursos existentes de Azure Stack Edge en Singapur, los datos se replican en Hong Kong.

Si va a crear un nuevo recurso de Azure Stack Edge, tiene la opción de habilitar la residencia de datos solo en Singapur. Cuando esta opción está seleccionada, los datos no se replican en Hong Kong. Si se produce una interrupción del servicio en toda la región, tiene dos opciones:

- Esperar a que se restaure la región de Singapur.

- Crear un recurso en otra región, restablecer el dispositivo y administrar el dispositivo a través del nuevo recurso. Para obtener instrucciones detalladas, consulte [Restablecimiento y reactivación de un dispositivo de Azure Stack Edge](azure-stack-edge-reset-reactivate-device.md).

## <a name="azure-edge-hardware-center-ordering-and-management-resource"></a>Recursos de pedidos y administración de Azure Edge Hardware Center 

El nuevo servicio Azure Edge Hardware Center (versión preliminar) ya está disponible y le permite crear y administrar recursos de Azure Stack Edge. Al hacer un pedido en la región Sudeste Asiático, puede seleccionar la opción para que los datos residan solo en Singapur y no se repliquen. 

En caso de interrupciones en toda la región, no podrá acceder a los recursos del pedido. No podrá devolver, cancelar ni eliminar los recursos. Si solicita actualizaciones en el estado del pedido o necesita iniciar una devolución urgente del dispositivo durante la interrupción del servicio, Soporte técnico de Microsoft se encargará de esas solicitudes.

Para obtener instrucciones detalladas, consulte [Creación de un pedido a través de Azure Edge Hardware Center](azure-stack-edge-gpu-deploy-prep.md#create-a-new-resource).


<!--## Azure Stack Edge telemetry

As Azure Stack Edge is a first-party Microsoft device, the telemetry from the device is automatically collected (without the user consent) and sent to Microsoft. This telemetry is stored in a common central location. This gathered telemetry provides valuable insights into enterprise deployments of Azure Stack Edge. This telemetry is also used for security, health, quality, and performance analysis.

- Microsoft collects telemetry for the infrastructure VMs (for example, Kubernetes master VM and Kubernetes worker VM) deployed on your Azure Stack Edge device and hosts. Telemetry is also gathered for other services that run on Azure Stack Edge device (for example, local Azure Resource Manager, Kubernetes dashboard). 
- The telemetry data is encrypted-in-transit as well at rest.
- Raw telemetry data sent to Microsoft is retained for 90 days. Aggregated data is retained for longer.
- For all the containerized workloads (deployed via IoT Edge and Kubernetes) and VM workloads, the application data is considered as the customer data. This data can only be accessed by the customer unless it pertains to the underlying infrastructure. 

For more information, see [Use the Kubernetes dashboard to monitor the Kubernetes cluster health on your Azure Stack Edge Pro device](azure-stack-edge-gpu-monitor-kubernetes-dashboard.md).-->

## <a name="azure-stack-edge-dependent-services"></a>Servicios dependientes de Azure Stack Edge

Kubernetes habilitado para Azure Arc, Azure IoT Hub y Azure IoT Edge, y Azure Key Vault son servicios que se integran en Azure Stack Edge.

### <a name="azure-arc-enabled-kubernetes"></a>Kubernetes habilitado para Azure Arc 

Kubernetes habilitado para Azure Arc está disponible como complemento para Azure Stack Edge. Para Singapur (Sudeste Asiático), los datos de Azure Arc residen solo en Singapur y no se replican en Hong Kong. <!--If there is a region-wide outage, the service is not resilient.-->

<!--For all other regions, Azure Arc supports Azure Regional Pair and is resilient to any region-wide outages.--> 
<!--For more information, see [Data residency and resiliency for Azure Arc enabled Kubernetes clusters]().-->


### <a name="azure-iot"></a>Azure IoT

Azure IoT está disponible como complemento para Azure Stack Edge. Para Singapur (Sudeste Asiático), Azure IoT usa la región emparejada y los datos se replican en Hong Kong. Esto significa que Azure IoT es resistente a las interrupciones en toda la región. 

<!--For more information, see [Data residency and resiliency for Azure IoT]().-->


<!--### Azure Key Vault

Azure Key Vault currently uses Azure Regional Pair for region outage resiliency. For new Azure Key Vault resources, an option is now available that can be enabled at the subscription level. When enabled, if your service is deployed in Singapore (Southeast Asia), you can control the data replication to Hong Kong. 

If you choose to store and process the data only in Singapore region, then the service will not be resilient to region-wide outages. -->
<!--For more information, see [Data residency and resiliency for Azure Key Vault]().-->

## <a name="next-steps"></a>Pasos siguientes

- Obtenga más información sobre los [requisitos de residencia de datos de Azure](https://azure.microsoft.com/global-infrastructure/data-residency/).