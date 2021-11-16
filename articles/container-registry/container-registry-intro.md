---
title: Registros de contenedor administrados
description: Introducción al servicio Azure Container Registry, que proporciona registros de Docker basados en la nube, administrados y privados.
author: stevelas
ms.topic: overview
ms.date: 02/10/2020
ms.author: stevelas
ms.custom: seodec18, mvc
ms.openlocfilehash: 6a05e0664bc7576f662d39e3ccd44d3258a84a9d
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/11/2021
ms.locfileid: "132337782"
---
# <a name="introduction-to-private-docker-container-registries-in-azure"></a>Introducción a los registros de contenedores privados de Docker en Azure

Azure Container Registry es un servicio privado administrado del Registro de Docker que usa Docker Registry 2.0, que es de código abierto. Cree y mantenga los registros de Azure Container para almacenar y administrar las imágenes privadas del contenedor Docker y los artefactos relacionados.

Use registros de contenedor de Azure con el desarrollo de contenedor y las canalizaciones de implementación existentes, o bien use Azure Container Registry Tasks para compilar imágenes de contenedor en Azure. Compile a petición o automatice completamente las compilaciones con desencadenadores como las confirmaciones del código fuente y las actualizaciones de la imagen de base.

Para más información acerca de los conceptos de Docker y del registro, [Introducción a Docker](https://docs.docker.com/engine/docker-overview/) y [Acerca de registros, repositorios e imágenes](container-registry-concepts.md).

## <a name="use-cases"></a>Casos de uso

Extraiga imágenes desde un registro de contenedor de Azure a varios destinos de implementación:

* **Sistemas escalables de orquestación** que administran aplicaciones en contenedores a través de clústeres de hosts, incluidos [Kubernetes](https://kubernetes.io/docs/), [DC/OS](https://docs.mesosphere.com/), y [Docker Swarm](https://docs.docker.com/get-started/swarm-deploy/).
* Los **Servicios de Azure** que admiten la compilación y ejecución de aplicaciones a escala, entre los que se incluyen [Azure Kubernetes Service (AKS)](../aks/index.yml), [App Service](../app-service/index.yml), [Batch](../batch/index.yml), [Service Fabric](../service-fabric/index.yml) y otros más.

Los desarrolladores también pueden insertar en un registro de contenedor como parte de un flujo de trabajo de desarrollo de contenedor. Por ejemplo, puede dirigir un registro de contenedor desde una herramienta de entrega e integración continuas como [Azure Pipelines](/azure/devops/pipelines/ecosystems/containers/acr-template) o [Jenkins](https://jenkins.io/).

Configure ACR Tasks para que recompile automáticamente imágenes de aplicaciones cuando se actualicen sus imágenes base o automatice las compilaciones de imágenes cuando el equipo guarde el código en un repositorio de Git. Cree tareas de varios pasos para automatizar la compilación, prueba y aplicación de revisiones de varias imágenes de contenedor en paralelo en la nube.

Azure proporciona varias herramientas, entre las que se incluyen la Interfaz de la línea de comandos de Azure, Azure Portal y soporte de API para administrar los registros de contenedor de Azure. Opcionalmente, instale la [extensión de Docker para Visual Studio Code](https://code.visualstudio.com/docs/azure/docker) y la extensión de la [cuenta de Azure](https://marketplace.visualstudio.com/items?itemName=ms-vscode.azure-account) para trabajar con los registros de contenedor de Azure. Extraiga e inserte imágenes en un registro de contenedor de Azure o ejecute ACR Tasks y, todo ello, en Visual Studio Code.

## <a name="key-features"></a>Principales características

* **Niveles de servicio del registro**: cree uno o varios registros de contenedor en la suscripción de Azure. Los registros están disponibles en tres niveles: [Basic, Standard y Premium](container-registry-skus.md), cada una de las cuales admite la integración de webhook, la autenticación del registro con Azure Active Directory y la funcionalidad de eliminación. Aproveche las ventajas del almacenamiento local, cercano a la red, de las imágenes de contenedor mediante la creación de un registro en la misma ubicación de Azure que las implementaciones. Use la característica de [replicación geográfica](container-registry-geo-replication.md) de los registros Premium en escenarios avanzados de replicación y de distribución de imágenes de contenedor. 

* **Seguridad y acceso**: inicie sesión en un registro mediante la CLI de Azure o el comando `docker login` estándar. Azure Container Registry transfiere imágenes de contenedor a través de HTTPS y admite TLS para proteger las conexiones de cliente. 

  > [!IMPORTANT]
  > A partir del 13 de enero de 2020, Azure Container Registry requerirá que todas las conexiones seguras de servidores y aplicaciones utilicen TLS 1.2. Habilite TLS 1.2 mediante cualquier cliente de Docker reciente (versión 18.03.0 o posterior). Se retirará la compatibilidad con TLS 1.0 y 1.1. 

  Puede [controlar el acceso](container-registry-authentication.md) a un registro de contenedor mediante una identidad de Azure, una [entidad de servicio](../active-directory/develop/app-objects-and-service-principals.md) respaldada por Azure Active Directory o una cuenta de administrador proporcionada. Use el control de acceso basado en rol de Azure (Azure RBAC) para asignar a los usuarios o sistemas permisos específicos para un registro.

  Entre las características de seguridad del nivel de servicio Premium se incluyen la [confianza de contenido](container-registry-content-trust.md) para la firma de etiquetas de imagen y los [firewalls y las redes virtuales (versión preliminar)](container-registry-vnet.md) para restringir el acceso al registro. De manera opcional, Microsoft Defender para la nube se integra en Azure Container Registry para [examinar imágenes](../security-center/defender-for-container-registries-introduction.md?bc=%2fazure%2fcontainer-registry%2fbreadcrumb%2ftoc.json&toc=%2fazure%2fcontainer-registry%2ftoc.json) cada vez que se inserta una imagen en un registro.

* **Imágenes y artefactos compatibles**: todas las imágenes se almacenan en un repositorio y cada de ellas es una instantánea de solo lectura de un contenedor compatible con Docker. Los registros de contenedor de Azure pueden incluir imágenes de Windows y de Linux. Controle los nombres de imagen de todas las implementaciones de contenedor. Use [comandos de Docker](https://docs.docker.com/engine/reference/commandline/) estándar para insertar imágenes en un repositorio o extraer una imagen de un repositorio. Además de las imágenes de contenedor de Docker, Azure Container Registry almacena los [formatos de contenido relacionados](container-registry-image-formats.md), como los [gráficos de Helm](container-registry-helm-repos.md) y las imágenes creadas para la [especificación de formato de imagen de Open Container Initiative (OCI)](https://github.com/opencontainers/image-spec/blob/master/spec.md).

* **Compilaciones de imágenes automatizadas**: use [Azure Container Registry Tasks](container-registry-tasks-overview.md) (ACR Tasks) para simplificar la creación, prueba, inserción e implementación de imágenes en Azure. Por ejemplo, utilice ACR Tasks para extender el bucle interno de desarrollo a la nube mediante la descarga de operaciones de `docker build` en Azure. Configure las tareas de compilación para automatizar el sistema operativo del contenedor y la canalización de aplicaciones de revisión de marcos, y compile imágenes de forma automática cuando el equipo guarde el código en el control de origen.

  Las [tareas con varios pasos](container-registry-tasks-overview.md#multi-step-tasks) proporcionan definición y ejecución de tareas basadas en pasos para compilar y probar imágenes de contenedor en la nube, y aplicarles revisiones. Los pasos de las tareas definen las operaciones de creación e inserción de imágenes de contenedor individuales. También pueden definir la ejecución de uno o más contenedores, donde cada paso utiliza el contenedor como su entorno de ejecución.

## <a name="next-steps"></a>Pasos siguientes

* [Creación de un registro de contenedor mediante Azure Portal](container-registry-get-started-portal.md)
* [Creación de un registro de contenedor con la CLI de Azure](container-registry-get-started-azure-cli.md)
* [Automatización de compilaciones de contenedor y mantenimiento con ACR Tasks](container-registry-tasks-overview.md)
