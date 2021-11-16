---
title: Nodos de computación confidencial en Azure Kubernetes Service (AKS)
description: Nodos de computación confidencial en Azure Kubernetes Service
services: virtual-machines
author: agowdamsft
ms.service: container-service
ms.subservice: confidential-computing
ms.topic: overview
ms.date: 11/04/2021
ms.author: amgowda
ms.custom: ignite-fall-2021
ms.openlocfilehash: f2a0a6452b978b6924574f1499916a0781413a24
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/11/2021
ms.locfileid: "132290236"
---
# <a name="confidential-computing-nodes-on-azure-kubernetes-service"></a>Nodos de computación confidencial en Azure Kubernetes Service

La [computación confidencial de Azure](overview.md) permite proteger los datos confidenciales mientras estén en uso. La infraestructura de computación confidencial subyacente protege estos datos de otras aplicaciones, administradores y proveedores, con un entorno de contenedor de ejecución de confianza con el respaldo de hardware. Agregar nodos de computación confidencial le permite dirigirse a la aplicación contenedora para que se ejecute en un entorno aislado protegido mediante hardware y que se pueda corroborar.

## <a name="overview"></a>Información general

Azure Kubernetes Service (AKS) permite agregar [nodos de computación confidencial de DCsv2](confidential-computing-enclaves.md) con tecnología Intel SGX. Estos nodos permiten ejecutar cargas de trabajo confidenciales en un entorno de ejecución de confianza (TEE) basado en hardware. Un entorno TEE permite que el código de nivel de usuario de los contenedores asigne regiones privadas de memoria para ejecutar el código directamente con la CPU. Estas regiones privadas de memoria que se ejecutan directamente con la CPU se denominan enclaves. Los enclaves ayudan a proteger la confidencialidad de los datos y la integridad de los datos y el código de otros procesos que se ejecutan en los mismos nodos. El modelo de ejecución de Intel SGX también quita las capas intermedias del sistema operativo invitado, el sistema operativo del host y el hipervisor. De este modo, se reduce el área expuesta a ataques. El modelo de *ejecución aislada por contenedor basada en hardware* en un nodo permite que las aplicaciones se ejecuten directamente con la CPU, manteniendo el bloque de memoria especial cifrado. Los nodos de computación confidencial con contenedores confidenciales son una excelente incorporación al planeamiento de seguridad de confianza cero y a la estrategia de contenedor para una defensa en profundidad.

:::image type="content" source="./media/confidential-nodes-aks-overview/sgx-aks-node.png" alt-text="Gráfico del nodo de proceso confidencial de AKS, que muestra contenedores confidenciales con código y datos protegidos dentro.":::

## <a name="aks-confidential-nodes-features"></a>Características de los nodos confidenciales de AKS

- Aislamiento de contenedor en el nivel de proceso y basado en hardware mediante un entorno de ejecución de confianza (TEE) de Intel SGX 
- Clústeres de grupos de nodos heterogéneos (mezcla de grupos de nodos confidenciales y no confidenciales)
- Programación de pods basada en memoria caché de página cifrada (EPC) (se requiere complemento)
- Controlador Intel SGX DCAP preinstalado
- Escalabilidad automática de clúster y escalabilidad horizontal automática de pods en función del consumo de CPU
- Compatibilidad con contenedores Linux mediante los nodos de trabajo de máquina virtual de Ubuntu 18.04 Gen 2

## <a name="confidential-computing-add-on-for-aks"></a>Complemento de computación confidencial para AKS
La función del complemento habilita funcionalidad adicional en AKS al ejecutar grupos de nodos de computación confidencial en el clúster. Este complemento habilita las características que se indican a continuación.

#### <a name="azure-device-plugin-for-intel-sgx"></a>Complemento de dispositivo de Azure para Intel SGX <a id="sgx-plugin"></a>

El complemento de dispositivo implementa la interfaz del complemento de dispositivo de Kubernetes para la memoria caché de página cifrada (EPC) y expone los controladores de dispositivo de los nodos. De hecho, este complemento convierte la memoria EPC en otro tipo de recurso en Kubernetes. Los usuarios pueden especificar límites en este recurso tal como lo harían en cualquier otro. Además de la función de programación, el complemento de dispositivo ayuda a asignar los permisos del controlador de dispositivo de SGX a los contenedores de cargas de trabajo confidenciales. Con este desarrollador de complemento se puede evitar el montaje de volúmenes del controlador Intel SGX en los archivos de implementación. Para ver un ejemplo de la implementación basada en memoria de EPC (`kubernetes.azure.com/sgx_epc_mem_in_MiB`) consulte [aquí](https://github.com/Azure-Samples/confidential-computing/blob/main/containersamples/helloworld/helm/templates/helloworld.yaml)


## <a name="programming-models"></a>Modelos de programación

### <a name="confidential-containers"></a>Contenedores confidenciales

Los [contenedores confidenciales](confidential-containers.md) ayudan a ejecutar aplicaciones de contenedor sin modificar existentes de los entornos de ejecución de **lenguajes de programación más comunes** (por ejemplo, Python, Node, Java, etc.) de forma confidencial. Este modelo de empaquetado no requiere modificaciones ni recompilación del código fuente. Este es el método más rápido para aplicar la confidencialidad que se puede lograr al empaquetar contenedores estándar de Docker con proyectos de código abierto o soluciones de asociados de Azure. En este modelo de empaquetado y ejecución, todos los elementos de la aplicación contenedora se cargan en el límite de confianza (enclave). Este modelo funciona bien con aplicaciones contenedoras comerciales del mercado o con aplicaciones personalizadas que se ejecutan actualmente en nodos de uso general.

### <a name="enclave-aware-containers"></a>Contenedores compatibles con enclave
Los nodos de computación confidencial de AKS también admiten contenedores programados para ejecutarse en un enclave con el fin de utilizar un **conjunto de instrucciones especial** disponible en la CPU. Este modelo de programación permite un control más estricto del flujo de ejecución y requiere el uso de SDK y marcos de trabajo especiales. Este modelo de programación proporciona el máximo control del flujo de la aplicación, con la Base de computación de confianza (TCB) mínima. El desarrollo de contenedores compatible con enclaves afecta a elementos de confianza y que no son de confianza para la aplicación contenedora. De este modo, permite administrar la memoria normal y la memoria caché de página cifrada (EPC) donde se ejecuta el enclave. [Más información](enclave-aware-containers.md) sobre contenedores compatibles con el enclave.

## <a name="next-steps"></a>Pasos siguientes

[Implementación de un clúster de AKS con nodos de computación confidencial](./confidential-enclave-nodes-aks-get-started.md)

[Inicio rápido con ejemplos de contenedores confidenciales](https://github.com/Azure-Samples/confidential-container-samples)

[Máquinas virtuales confidenciales con Intel SGX: lista de SKU de DCsv2](../virtual-machines/dcv2-series.md)

[Máquinas virtuales confidenciales con Intel SGX: lista de SKU de DCsv3](../virtual-machines/dcv3-series.md)

[Sesión de seminario web sobre la defensa en profundidad con contenedores confidenciales](https://www.youtube.com/watch?reload=9&v=FYZxtHI_Or0&feature=youtu.be)

<!-- LINKS - external -->
[Azure Attestation]: ../attestation/index.yml


<!-- LINKS - internal -->
[DC Virtual Machine]: /confidential-computing/virtual-machine-solutions-sgx.md
