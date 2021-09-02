---
title: Contenedores confidenciales en Azure Kubernetes Service (AKS)
description: Conozca la compatibilidad con contenedores sin modificar en contenedores confidenciales.
services: container-service
author: agowdamsft
ms.topic: article
ms.date: 2/11/2020
ms.author: amgowda
ms.service: container-service
ms.subservice: confidential-computing
ms.openlocfilehash: 7b225fa68b9a280ef4a1c7145ab691b82168202a
ms.sourcegitcommit: 47fac4a88c6e23fb2aee8ebb093f15d8b19819ad
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/26/2021
ms.locfileid: "122967417"
---
# <a name="confidential-containers"></a>Contenedores confidenciales

## <a name="overview"></a>Información general

Permita a los desarrolladores incorporar una **aplicación de Docker (nueva o existente)** y ejecutarla de forma segura en Azure Kubernetes Service (AKS) gracias a la compatibilidad con nodos de informática confidencial.

Los contenedores confidenciales ayudan a:

- integridad de datos 
- proteger la confidencialidad de los datos
- proteger la integridad del código
- proteger el código del contenedor mediante cifrado
- proteger las garantías basadas en hardware
- permitir la ejecución de aplicaciones existentes
- crear la raíz de confianza del hardware
- quitar el administrador de host, el administrador de Kubernetes y el hipervisor del límite de confianza

Un entorno de ejecución de confianza (TEE) basado en hardware es un componente importante que se usa para proporcionar garantías sólidas mediante medidas de hardware y software de componentes de la base informática de confianza (TCB). Las comprobaciones de estas medidas ayudan en la validación del cálculo esperado y confirman las posibles manipulaciones de las aplicaciones de contenedor.

Los contenedores confidenciales admiten la ejecución de aplicaciones personalizadas desarrolladas con **Python, Java, Node.js, etc. o paquetes de aplicaciones de contenedor, como NGINX, Redis Cache, MemCache**, etc., sin modificar en AKS con nodos de computación confidencial.

Los contenedores confidenciales son la ruta más rápida a la confidencialidad de los contenedores y solo requerirán volver a empaquetar las aplicaciones de contenedor de Docker existentes y no requerirán cambios en el código de la aplicación. Los contenedores confidenciales son aplicaciones de contenedor de Docker que se empaquetan para ejecutarse en un TEE. Algunos habilitadores de contenedores confidenciales también ofrecen cifrado de contenedores que pueden ayudar a proteger el código de contenedor durante el almacenamiento y el transporte, así como mientras están montados en el host. El cifrado de contenedores permite profundizar y proteger el código o modelo empaquetado en el contenedor con su clave de descifrado asociada a TEE.

A continuación, se muestra el proceso para los contenedores confidenciales, desde el desarrollo hasta la implementación. ![Instrucciones para procesar el contenedor confidencial.](./media/confidential-containers/how-to-confidential-container.png)

## <a name="confidential-container-enablers"></a>Habilitadores de contenedores confidenciales
Para ejecutar un contenedor de Docker existente sin modificar, se requiere un software de SGX para que las llamadas a la aplicación puedan usar un conjunto de instrucciones de CPU especial disponible para reducir el área expuesta de conexión y no tomar ninguna dependencia del sistema operativo invitado. Una vez ajustado con el software en tiempo de ejecución de SGX, los contenedores se inician automáticamente en el enclaves protegidos y, por tanto, quitan el SO invitado, el sistema operativo del host o el hipervisor del límite de confianza. Esta ejecución aislada en un nodo (máquina virtual) con cifrado de datos en memoria respaldado por el hardware reduce las áreas de ataque de superficie general, así como las vulnerabilidades con los niveles de sistema operativo o hipervisor.

Los contenedores confidenciales son completamente compatibles con AKS y se habilitan a través de los proyectos de asociados de Azure y de software de código abierto (OSS). Los desarrolladores pueden elegir proveedores de software en función de las características, la integración con los servicios de Azure y la compatibilidad con las herramientas.

## <a name="partner-enablers"></a>Habilitadores de asociados
> [!NOTE]
> Las soluciones siguientes se ofrecen a través de los asociados de Azure y pueden conllevar tarifas de licencia. Compruebe por otro lado los términos del software asociado. 

### <a name="anjuna"></a>Anjuna

[Anjuna](https://www.anjuna.io/) proporciona software de plataforma SGX que permite ejecutar contenedores sin modificar en AKS. Obtenga más información sobre la funcionalidad y consulte las aplicaciones de ejemplo [aquí](https://www.anjuna.io/microsoft-azure-confidential-computing-aks-lp).

Puede comenzar con un ejemplo de Redis Cache y de una aplicación personalizada de Python [aquí](https://www.anjuna.io/microsoft-azure-confidential-computing-aks-lp).

![Proceso de Anjuna](./media/confidential-containers/anjuna-process-flow.png)

### <a name="fortanix"></a>Fortanix

[Fortanix](https://www.fortanix.com/) ofrece a los desarrolladores la opción de un portal y una experiencia basada en la CLI para incorporar sus aplicaciones en contenedores y convertirlas en contenedores confidenciales con capacidad SGX sin necesidad de modificar o volver a compilar la aplicación. Fortanix ofrece la flexibilidad para ejecutar y administrar el conjunto más amplio de aplicaciones, como las aplicaciones existentes, las nuevas aplicaciones nativas de enclave y las aplicaciones ya empaquetadas. Los usuarios pueden empezar con la interfaz de usuario del [administrador de computación confidencial](https://em.fortanix.com/) o las [API REST](https://www.fortanix.com/api/em/) para crear contenedores confidenciales mediante la guía [Inicio rápido](https://fortanix.com/blog/2020/10/fortanix-confidential-containers-on-microsoft-azure-kubernetes-service-aks/) para Azure Kubernetes Service.

![Proceso de implementación de Fortanix](./media/confidential-containers/fortanix-confidential-containers-flow.png)

### <a name="scone-scontain"></a>Scone (Scontain)

[SCONE](https://scontain.com/index.html?lang=en) admite directivas de seguridad que pueden generar certificados, claves y secretos, y garantiza que solo son visibles para los servicios atestiguados de una aplicación. De esta manera, los servicios de una aplicación se atestiguan automáticamente entre sí a través de TLS, sin necesidad de modificar las aplicaciones ni TLS. Esto se explica aquí con la ayuda de una sencilla aplicación de Flask: https://sconedocs.github.io/flask_demo/  

SCONE puede convertir los archivos binarios actuales en aplicaciones que se ejecutan dentro de enclaves sin necesidad de cambiar la aplicación o de volver a compilarla. SCONE también protege los lenguajes interpretados como Python mediante el **cifrado** de los archivos de datos y de los archivos de código de Python. Con la ayuda de una directiva de seguridad de SCONE, uno puede proteger los archivos cifrados contra los accesos, las modificaciones y las reversiones no autorizados. Puede encontrar información sobre cómo aplicar SCONE a una aplicación de Python existente [aquí](https://sconedocs.github.io/sconify_image/).

![Flujo de Scontain](./media/confidential-containers/scone-workflow.png)

Las implementaciones de Scone en nodos de computación confidencial con AKS se admiten e integran por completo con otros servicios de Azure. Puede comenzar con una aplicación de ejemplo aquí https://sconedocs.github.io/aks/.


## <a name="oss-enablers"></a>Habilitadores de OSS 
> [!NOTE]
> Las soluciones siguientes se ofrecen a través de proyectos de código abierto y no están directamente afiliadas a la computación confidencial de Azure (ACC) o Microsoft.  

### <a name="graphene"></a>Graphene

[Graphene](https://grapheneproject.io/) es un sistema operativo invitado ligero diseñado para ejecutar una única aplicación Linux con requisitos mínimos de host. Graphene puede ejecutar aplicaciones en un entorno aislado con ventajas comparables a las de ejecutar un sistema operativo completo y tiene una buena compatibilidad con herramientas para convertir la aplicación de contenedor de Docker existente en Graphene Shielded Containers (GSC).

Puede comenzar con una aplicación de ejemplo y la implementación en AKS [aquí](https://graphene.readthedocs.io/en/latest/cloud-deployment.html#azure-kubernetes-service-aks).

### <a name="occlum"></a>Occlum
[Occlum](https://occlum.io/) es un sistema operativo de biblioteca (LibOS) de varios procesos y seguro para memoria para Intel SGX. Permite que las aplicaciones heredadas se ejecuten en SGX con pocas modificaciones en el código fuente, o incluso ninguna. Occlum protege de forma transparente la confidencialidad de las cargas de trabajo de los usuarios, a la vez que permite una migración mediante lift-and-shift sencilla a aplicaciones de Docker existentes.

Occlum admite implementaciones de AKS. Siga las instrucciones de implementación con varias aplicaciones de ejemplo [aquí](https://github.com/occlum/occlum/blob/master/docs/azure_aks_deployment_guide.md).

### <a name="marblerun"></a>Marblerun

[Marblerun](https://marblerun.sh/) es un marco de orquestación para contenedores confidenciales. Facilita la ejecución y el escalado de servicios confidenciales en Kubernetes habilitado para SGX. Marblerun se encarga de tareas repetitivas como comprobar los servicios del clúster, administrar sus secretos y establecer conexiones mTLS de enclave a enclave entre ellos. También garantiza que el clúster de contenedores confidenciales se adhiera a un manifiesto definido en JSON simple. Los clientes externos pueden comprobar el manifiesto mediante la atestación remota. 

![Flujo de Marblerun](./media/confidential-containers/marblerun-workflow.png)

En pocas palabras, Marblerun amplía las propiedades de confidencialidad, integridad y comprobabilidad de un único enclave para un clúster de Kubernetes. 

Marblerun admite contenedores confidenciales creados con Graphene, Occlum y EGo. [Aquí](https://docs.edgeless.systems/marblerun/#/examples?id=examples) se proporcionan ejemplos para cada SDK. Marblerun se ha creado para ejecutarse en Kubernetes y junto con las herramientas nativas de nube existentes. Incluye una CLI y gráficos de Helm fáciles de usar. Tiene compatibilidad de primera clase con los nodos de computación confidencial en AKS. Puede encontrar información sobre cómo implementar Marblerun en AKS [aquí](https://docs.edgeless.systems/marblerun/#/deployment/cloud?id=cloud-deployment).

## <a name="confidential-containers-demo"></a>Demostración de contenedores confidenciales
Vea la demostración de contenedores confidenciales en el ámbito sanitario. El ejemplo está disponible [aquí](/azure/architecture/example-scenario/confidential/healthcare-inference). 

> [!VIDEO https://www.youtube.com/embed/PiYCQmOh0EI]


## <a name="get-in-touch"></a>Contacto

¿Tiene alguna pregunta sobre su implementación o desea convertirse en habilitador? Envíe un correo electrónico al equipo del producto **acconaks@microsoft.com** .

## <a name="reference-links"></a>Vínculos de referencia

[Microsoft Azure Attestation](../attestation/overview.md)

[Máquinas virtuales DCsv2](virtual-machine-solutions.md)

[Azure Kubernetes Service (AKS)](../aks/intro-kubernetes.md)
