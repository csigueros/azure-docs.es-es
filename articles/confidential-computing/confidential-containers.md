---
title: Contenedores confidenciales en Azure
description: Obtenga información sobre la compatibilidad con contenedores sin modificar en contenedores confidenciales.
services: container-service
author: agowdamsft
ms.topic: article
ms.date: 11/01/2011
ms.author: amgowda
ms.service: container-service
ms.custom: ignite-fall-2021
ms.openlocfilehash: d7c94570a7b83fba70a8e95d17f5c6eb215740bb
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/02/2021
ms.locfileid: "131067528"
---
# <a name="confidential-containers-on-azure"></a>Contenedores confidenciales en Azure

La computación confidencial de Azure ofrece contenedores confidenciales. Hay muchas [opciones que puede elegir para los contenedores confidenciales](choose-confidential-containers-offerings.md). Los entornos protegidos y aislados con atestación mejoran la seguridad general de las implementaciones de contenedores. 

Un entorno de ejecución de confianza (TEE) basado en hardware proporciona garantías seguras. Un TEE proporciona medidas de hardware y software a partir de componentes de base de computación de confianza (TCB). Las ofertas de contenedores confidenciales en Azure permiten la comprobación de estas medidas y validan si las aplicaciones de contenedor se ejecutan en un entorno de ejecución verificable.

Los contenedores confidenciales admiten aplicaciones personalizadas desarrolladas con cualquier lenguaje de programación. También puede ejecutar aplicaciones de contenedor de Docker fuera de la plataforma.

![Diagrama del límite de protección de los contenedores confidenciales en Kubernetes.](./media/confidential-containers/sgx-confidential-container.jpg)

## <a name="enablers-with-intel-sgx-on-azure-kubernetes-serviceaks"></a>Habilitadores con Intel SGX en Azure Kubernetes Service (AKS)

 Para ejecutar un contenedor de Docker existente, las aplicaciones en nodos informáticos confidenciales necesitan una capa de abstracción o Intel Software Guard Extensions (SGX) para aprovechar el conjunto de instrucciones especiales de la CPU. Configure SGX para proteger el código confidencial de la aplicación. SGX crea una ejecución directa en la CPU para quitar el sistema operativo invitado (SO), el sistema operativo host o el hipervisor del límite de confianza. Este paso reduce las áreas y vulnerabilidades generales de la superficie de ataques.

Azure Kubernetes Service (AKS) es totalmente compatible con los contenedores confidenciales. Puede ejecutar contenedores existentes de forma confidencial en AKS.

![Diagrama de conversión de contenedores confidenciales, con nuevos pasos para habilitar Intel SGX y AKS](./media/confidential-containers/confidential-containers-deploy-steps.jpg)

## <a name="partner-enablers"></a>Habilitadores de asociados

Puede habilitar contenedores confidenciales en asociados de Azure y proyectos de software de código abierto (OSS). Los desarrolladores pueden elegir proveedores de software en función de sus características, la integración con los servicios de Azure y la compatibilidad con las herramientas. 

> [!IMPORTANT]
> Los asociados de Azure ofrecen las soluciones siguientes. Es posible que estas soluciones incurran en cuotas de licencia. Compruebe todos los términos del software asociado de manera independiente. 

### <a name="fortanix"></a>Fortanix

[Fortanix](https://www.fortanix.com/) tiene experiencias de interfaz de línea de comandos (CLI) y portal para convertir sus aplicaciones contenedorizadas en contenedores confidenciales compatibles con SGX. No es necesario modificar ni volver a compilar la aplicación. Fortanix proporciona la flexibilidad para ejecutar y administrar un amplio conjunto de aplicaciones. Puede usar aplicaciones existentes, nuevas aplicaciones nativas del enclave y aplicaciones empaquetadas previamente. Comience con la interfaz de usuario de [Enclave Manager](https://em.fortanix.com/) de Fortanix o las [API REST](https://www.fortanix.com/api/em/). Cree contenedores confidenciales mediante la [guía de inicio rápido para AKS](https://support.fortanix.com/hc/en-us/articles/360049658291-Fortanix-Confidential-Container-on-Azure-Kubernetes-Service) de Fortanix.

![Diagrama del proceso de implementación de Fortanix, en el que se muestran los pasos para mover aplicaciones a contenedores confidenciales e implementarlas.](./media/confidential-containers/fortanix-confidential-containers-flow.png)

### <a name="scone-scontain"></a>SCONE (Scontain)

Las directivas de seguridad de [SCONE](https://scontain.com/) (Scontain) generan certificados, claves y secretos. Solo los servicios con atestación para una aplicación ven estas credenciales. Los servicios de aplicación realizan automáticamente la atestación entre sí por medio de TLS. No es necesario modificar las aplicaciones ni TLS. Para más explicación, vea la [demostración de la aplicación Flask](https://sconedocs.github.io/flask_demo/) de SCONE.

SCONE puede convertir la mayoría de los archivos binarios existentes en aplicaciones que se ejecutan dentro de enclaves. No es necesario cambiar ni volver a compilar la aplicación. SCONE también protege los lenguajes interpretados como Python mediante el cifrado de los archivos de datos y los archivos de código de Python. Puede usar directivas de seguridad de SCONE para proteger los archivos cifrados contra el acceso no autorizado, las modificaciones y las reversiones. Para más información, vea la documentación de SCONE sobre [cómo usar SCONE con una aplicación de Python existente](https://sconedocs.github.io/sconify_image/).

![Diagrama del flujo de trabajo de SCONE, en el que se muestra cómo procesa SCONE las imágenes binarias.](./media/confidential-containers/scone-workflow.png)

Puede implementar SCONE en nodos de computación confidencial de Azure con AKS. Este proceso es totalmente compatible e integrado. Para más información, vea la [aplicación de AKS de ejemplo de SCONE](https://sconedocs.github.io/aks/).

### <a name="anjuna"></a>Anjuna

[Anjuna](https://www.anjuna.io/) proporciona software de plataforma SGX para ejecutar contenedores sin modificar en AKS. Para más información, vea la [documentación sobre la funcionalidad y las aplicaciones de ejemplo](https://www.anjuna.io/microsoft-azure-confidential-computing-aks-lp) de Anjuna.

Puede comenzar con un ejemplo de Redis Cache y de una aplicación personalizada de Python [aquí](https://www.anjuna.io/microsoft-azure-confidential-computing-aks-lp).

![Diagrama del proceso de Anjuna, en el que se muestra cómo se ejecutan los contenedores en la computación confidencial de Azure.](media/confidential-containers/anjuna-process-flow.png)

## <a name="oss-enablers"></a>Habilitadores de OSS

> [!NOTE]
> Los proyectos de código abierto ofrecen las soluciones siguientes. La computación confidencial de Azure y Microsoft no están directamente afiliadas a estos proyectos y soluciones.  

### <a name="gramine"></a>Gramine

[Gramine](https://grapheneproject.io/) es un sistema operativo invitado ligero diseñado para ejecutar una única aplicación Linux con requisitos mínimos de host. Gramine puede ejecutar aplicaciones en un entorno aislado. Se admiten herramientas para convertir aplicaciones de contenedor de Docker existentes en Gramine Shielded Containers (GSC).

Para más información, vea la [aplicación de ejemplo y la implementación en AKS](https://graphene.readthedocs.io/en/latest/cloud-deployment.html#azure-kubernetes-service-aks) de Gramine.

### <a name="occlum"></a>Occlum

[Occlum](https://occlum.io/) es un sistema operativo de biblioteca (LibOS) de varios procesos y seguro para memoria para Intel SGX. El SO permite que las aplicaciones heredadas se ejecuten en SGX con pocas modificaciones en el código fuente, o incluso ninguna. Occlum protege de forma transparente la confidencialidad de las cargas de trabajo de usuario, a la vez que permite una "migración mediante lift-and-shift" sencilla a aplicaciones de Docker existentes.

Occlum admite implementaciones de AKS. Para más información, vea las [instrucciones de implementación y las aplicaciones de ejemplo](https://github.com/occlum/occlum/blob/master/docs/azure_aks_deployment_guide.md) de Occlum.

### <a name="marblerun"></a>Marblerun

[Marblerun](https://marblerun.sh/) es un marco de orquestación para contenedores confidenciales. Puede ejecutar y modificar la escala de servicios confidenciales en Kubernetes habilitado para SGX. Marblerun se encarga de tareas repetitivas como comprobar los servicios del clúster, administrar sus secretos y establecer conexiones mTLS de enclave a enclave entre ellos. También garantiza que el clúster de contenedores confidenciales se adhiera a un manifiesto definido en JSON simple. Puede comprobar el manifiesto con clientes externos mediante la atestación remota.

Este marco amplía las propiedades de confidencialidad, integridad y capacidad de comprobación de un único enclave para un clúster de Kubernetes.

Marblerun admite contenedores confidenciales creados con Graphene, Occlum y EGo con [ejemplos para cada SDK](https://docs.edgeless.systems/marblerun/#/examples?id=examples). El marco se ejecuta en Kubernetes junto con las herramientas nativas de nube existentes. Hay una CLI y gráficos de Helm. Marblerun también admite nodos de computación confidencial en AKS. Siga la [guía para implementar Marblerun en AKS](https://docs.edgeless.systems/marblerun/#/deployment/cloud?id=cloud-deployment).

## <a name="confidential-containers-demo"></a>Demostración de contenedores confidenciales

Para obtener una aplicación de ejemplo, vea la [demostración de atención sanitaria con contenedores confidenciales](https://github.com/Azure-Samples/confidential-container-samples/blob/main/confidential-healthcare-scone-confinf-onnx/README.md). 

> [!VIDEO https://www.youtube.com/embed/PiYCQmOh0EI]


## <a name="get-in-touch"></a>Ponerse en contacto

¿Tiene alguna pregunta sobre la implementación? ¿Quiere convertirse en un habilitador para contenedores confidenciales? Envíe un correo electrónico a <acconaks@microsoft.com>.

## <a name="next-steps"></a>Pasos siguientes

- [Implementación de un clúster de AKS con nodos de máquina virtual confidencial de Intel SGX](./confidential-enclave-nodes-aks-get-started.md)
- [Microsoft Azure Attestation](../attestation/overview.md)
- [Máquinas virtuales confidenciales de Intel SGX](virtual-machine-solutions-sgx.md)
- [Azure Kubernetes Service (AKS)](../aks/intro-kubernetes.md)
