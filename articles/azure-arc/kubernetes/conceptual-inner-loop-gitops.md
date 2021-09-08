---
title: Experiencia del desarrollador de bucles internos para los equipos que adoptan GitOps
services: azure-arc
ms.service: azure-arc
ms.date: 06/18/2021
ms.topic: conceptual
author: sudivate
ms.author: sudivate
description: 'En este artículo se proporciona información general conceptual sobre la experiencia del desarrollador de bucles internos para los equipos que adoptan GitOps. '
keywords: GitOps, Kubernetes, K8s, Azure, Helm, Arc, AKS, Azure Kubernetes Service, contenedores, CI, CD, Azure DevOps, bucle interno, experiencia del desarrollador
ms.openlocfilehash: 3c0e8041a31c506c926315381741afd75bb8a806
ms.sourcegitcommit: 2cff2a795ff39f7f0f427b5412869c65ca3d8515
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 07/10/2021
ms.locfileid: "113598778"
---
# <a name="inner-loop-developer-experience-for-teams-adopting-gitops"></a>Experiencia del desarrollador de bucles internos para los equipos que adoptan GitOps

En este artículo se describe la manera en que un bucle interno establecido puede mejorar la productividad del desarrollador y contribuir a una transición sin problemas del bucle de desarrollo interno al bucle externo para los equipos que adoptan GitOps.

## <a name="inner-dev-loop-frameworks"></a>Marcos de bucle de desarrollo interno

La creación y la implementación de contenedores pueden ralentizar la experiencia interna de desarrollo y afectar a la productividad del equipo. Los equipos de desarrollo nativos de la nube se beneficiarán de un marco de bucle de desarrollo interno sólido. Los marcos de bucle de desarrollo interno ayudan en el proceso iterativo de escritura de código, compilación y depuración.

Entre las funcionalidades de los marcos de bucle de desarrollo interno se incluyen las siguientes:

 
- Automatización de pasos repetitivos, como la compilación de código y contenedores y la implementación en el clúster de destino. 
- Simplificación del trabajo con clústeres remotos y locales y admisión de la depuración de túnel local para la instalación híbrida.
- Capacidad de configurar un flujo personalizado para la productividad basada en el equipo.
- Control de dependencias de microservicios. 
- Recarga activa, reenvío de puertos, registro y acceso al terminal. 



En función de la madurez y la complejidad del servicio, los equipos de desarrollo determinan qué configuración del clúster usarán para acelerar el bucle de desarrollo interno: 

* Completamente local
* Completamente remota
* Híbrido 


Afortunadamente, hay muchos marcos que admiten las funcionalidades enumeradas. Microsoft ofrece Bridge to Kubernetes para la depuración de túneles locales y hay ofertas similares en el mercado, como DevSpace, Scaffold y Tilt, entre otras.

> [!NOTE]
> No confunda la oferta del mercado [DevSpace](https://github.com/loft-sh/devspace) con el anterior marco de Microsoft denominado DevSpace, que actualmente se llama [Bridge to Kubernetes](https://code.visualstudio.com/docs/containers/bridge-to-kubernetes).


## <a name="inner-loop-to-outer-loop-transition"></a>Transición del bucle interno al bucle externo 

Una vez que haya evaluado y elegido un marco de desarrollo de bucle interno, podrá crear fácilmente una transición de bucle interno a bucle externo.

Como se describe en el ejemplo del artículo [Flujo de trabajo de CI/CD con GitOps](conceptual-gitops-ci-cd.md), un desarrollador de aplicaciones trabaja en el código de una aplicación dentro de un repositorio de aplicaciones. Dicho repositorio de aplicaciones también contiene plantillas de Helm o Kustomize de implementación de alto nivel. Las canalizaciones de CI/CD:

* Generan los manifiestos de bajo nivel a partir de las plantillas de alto nivel y agregan valores específicos del entorno.
* Crean una solicitud de incorporación de cambios que combina los manifiestos de bajo nivel con el repositorio de GitOps que contiene el estado deseado para el entorno específico. 

Se pueden generar manifiestos similares de bajo nivel localmente para el bucle de desarrollo interno, mediante el uso de los valores de configuración locales del desarrollador. Los desarrolladores de aplicaciones pueden iterar los cambios de código y usar los manifiestos de bajo nivel para implementar y depurar aplicaciones. La generación de manifiestos de bajo nivel se puede integrar en un flujo de trabajo de bucle interno con la configuración local del desarrollador. La mayor parte del marco de bucle interno permite configurar flujos personalizados, ya sea mediante la extensión a través de complementos personalizados o mediante la inserción de la invocación de scripts basada en enlaces. 

## <a name="example-inner-loop-workflow-built-with-devspace-framework"></a>Ejemplo de flujo de trabajo de bucle interno creado con el marco DevSpace


### <a name="diagram-a-inner-loop-flow"></a>Diagrama A: flujo de bucle interno
:::image type="content" source="media/dev-inner-loop.png" alt-text="Diagrama del flujo de bucle interno con DevSpace.":::

### <a name="diagram-b-inner-loop-to-outer-loop-transition"></a>Diagrama B: transición del bucle interno al bucle externo
:::image type="content" source="media/inner-loop-to-outer-loop.png" alt-text="Diagrama de la transición del bucle interno al bucle externo." :::


## <a name="example-workflow"></a>Flujo de trabajo de ejemplo
Como desarrolladora de aplicaciones, Alice:
- Crea un archivo devspace.yaml para configurar el bucle interno.
- Escribe y prueba el código de la aplicación mediante el bucle interno para comprobar su eficacia.
- Realiza la implementación en el almacenamiento provisional o en producción con el bucle externo.


Supongamos que Alice quiere actualizar, ejecutar y depurar la aplicación en un clúster local o remoto.

1. Alice actualiza la configuración local para el entorno de desarrollo representado en un archivo .env.
1. Alice ejecuta `devspace use context` y selecciona el contexto del clúster de Kubernetes.
1.  Alice selecciona un espacio de nombres con el que trabajar mediante la ejecución de `devspace use namespace <namespace_name>`.
1.  Alice puede iterar los cambios en el código de la aplicación, e implementa y depura la aplicación en el clúster de destino mediante la ejecución de `devspace dev`.
1. La ejecución de `devspace dev` genera manifiestos de bajo nivel basados en la configuración local de Alice e implementa la aplicación. Estos manifiestos de bajo nivel se configuran con enlaces de DevSpace en devspace.yaml.
1. Alice no necesita recompilar el contenedor cada vez que realiza cambios en el código, ya que DevSpace habilitará la recarga activa, de modo que usará la sincronización de archivos para copiar los cambios más recientes en el contenedor.
1. La ejecución de `devspace dev` también implementará todas las dependencias configuradas en devspace.yaml, como las dependencias de back-end en el front-end. 
1. Alice prueba los cambios, para lo que accede a la aplicación mediante el reenvío configurado a través de devspace.yaml.
1. Una vez que Alice haya realizado los cambios, podrá purgar la implementación mediante la ejecución de `devspace purge` y crear una solicitud de incorporación de cambios para combinar sus cambios en la rama de desarrollo del repositorio de aplicaciones.

> [!NOTE]
> Busque el código de ejemplo del flujo de trabajo anterior en este [repositorio de GitHub](https://github.com/Azure/arc-cicd-demo-src).


## <a name="next-steps"></a>Pasos siguientes
Obtenga más información sobre la creación de conexiones entre el clúster y un repositorio de GIT como un [recurso de configuración con Kubernetes habilitado para Azure Arc](./conceptual-configurations.md).