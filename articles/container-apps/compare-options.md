---
title: Comparación de Container Apps con otras opciones de contenedor de Azure
description: Obtenga información sobre cuándo usar Azure Container Apps y cómo se compara con otras opciones de contenedor, como Azure Container Instances, Azure App Services, Azure Functions y Azure Kubernetes Service.
services: container-apps
author: jeffhollan
ms.service: container-apps
ms.topic: quickstart
ms.date: 11/03/2021
ms.author: jehollan
ms.custom: ignite-fall-2021
ms.openlocfilehash: 0ac05794b42e91060e604a9d6921547150eb6706
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/11/2021
ms.locfileid: "132310003"
---
# <a name="comparing-container-apps-with-other-azure-container-options"></a>Comparación de Container Apps con otras opciones de contenedor de Azure

Hay muchas opciones para que los equipos compilen e implemente aplicaciones contenedorizadas y nativas de la nube en Azure. Este artículo le ayudará a comprender qué escenarios y casos de uso son más adecuados para Azure Container Apps y cómo se compara con otras opciones de contenedor en Azure, como:  
- [Azure App Service](#azure-app-service)
- [Azure Container Instances](#azure-container-instances)
- [Azure Kubernetes Service](#azure-kubernetes-service)
- [Funciones de Azure](#azure-functions)
- [Azure Spring Cloud](#azure-spring-cloud)

No hay ninguna solución perfecta para cada caso de uso y cada equipo. La explicación siguiente proporciona recomendaciones e instrucciones generales como punto de partida para ayudar a encontrar la mejor opción para su equipo y sus requisitos.

> [!IMPORTANT]
> Azure Container Apps se encuentra actualmente en versión preliminar pública, mientras que estas otras opciones están disponibles con carácter general (GA).


## <a name="container-option-comparisons"></a>Comparaciones de las opciones de contenedor

### <a name="azure-container-apps"></a>Azure Container Apps
Azure Container Apps permite crear microservicios sin servidor basados en contenedores. Entre las características distintivas de Container Apps se incluyen:

* Optimizado para ejecutar contenedores de uso general, especialmente para aplicaciones que abarcan muchos microservicios implementados en contenedores.
* Con tecnología de Kubernetes y tecnologías de código abierto, como [Dapr](https://dapr.io/), [KEDA](https://keda.sh/) y [envoy](https://www.envoyproxy.io/).
* Admite aplicaciones y microservicios de estilo Kubernetes con características como la [detección de servicios](connect-apps.md) y la [separación del tráfico](revisions.md).
* Habilita las arquitecturas de aplicaciones controladas por eventos al admitir la escala basada en el tráfico y la extracción de [orígenes de eventos como colas](scale-app.md), incluida la [escala a cero](scale-app.md).
* Ofrece compatibilidad con procesos de larga duración y la posibilidad de ejecutar [tareas en segundo plano](background-processing.md).

Azure Container Apps no proporciona acceso directo a las API de Kubernetes subyacentes. Si necesita acceso a las API de Kubernetes y al plano de control, debe usar [Azure Kubernetes Service](../aks/intro-kubernetes.md). Sin embargo, si desea compilar aplicaciones de estilo Kubernetes y no requiere acceso directo a todas las API nativas de Kubernetes y a la administración de clústeres, Container Apps proporciona una experiencia totalmente administrada basada en procedimientos recomendados. Por estos motivos, es posible que muchos equipos prefieran empezar a crear microservicios de contenedor con Azure Container Apps.

### <a name="azure-app-service"></a>Azure App Service
Azure App Service proporciona hospedaje totalmente administrado para aplicaciones web, incluidos sitios web y API web. Estas aplicaciones web se pueden implementar mediante código o contenedores. Azure App Service está optimizado para aplicaciones web. Azure App Service se integra con otros servicios de Azure, como Azure Container Apps o Azure Functions. Azure App Service es una opción ideal para compilar aplicaciones web.

### <a name="azure-container-instances"></a>Azure Container Instances
Azure Container Instances (ACI) proporciona un único pod de contenedores aislados de Hyper-V a petición. Puede considerarse como una opción de "bloque de creación" de nivel inferior en comparación con Container Apps. Los conceptos como la escala, el equilibrio de carga y los certificados no se proporcionan con los contenedores de ACI. Por ejemplo, para escalar a cinco instancias de contenedor, cree cinco instancias de contenedor distintas. Azure Container Apps proporciona muchos conceptos específicos de la aplicación aparte de los contenedores, como certificados, revisiones, escala y entornos. Los usuarios suelen interactuar con Azure Container Instances a través de otros servicios. Por ejemplo, Azure Kubernetes Service puede clasificar la orquestación y la escala sobre ACI mediante [nodos virtuales](../aks/virtual-nodes.md). Si necesita un bloque de creación con menos "opiniones" que no se alinee con los escenarios para los que está optimizado Azure Container Apps, Azure Container Instances es una opción ideal.

### <a name="azure-kubernetes-service"></a>Azure Kubernetes Service
Azure Kubernetes Service proporciona una opción de Kubernetes totalmente administrado en Azure. Admite el acceso directo a la API de Kubernetes y ejecuta cualquier carga de trabajo de Kubernetes. El clúster completo reside en su suscripción, con las configuraciones y las operaciones del clúster bajo su control y responsabilidad. Los equipos que buscan una versión totalmente administrada de Kubernetes en Azure, Azure Container Service es una opción ideal.

### <a name="azure-functions"></a>Azure Functions
Azure Functions es una solución de funciones como servicio (FaaS) sin servidor. Está optimizado para ejecutar funciones controladas por eventos mediante el modelo de programación de funciones. Comparte muchas características con Azure Container Apps en torno a la escala y la integración con eventos, pero está optimizado para funciones efímeras implementadas como código o contenedores. El modelo de programación de Azure Functions ofrece ventajas de productividad para los equipos que buscan desencadenar eventos y establecer enlaces con otros orígenes de datos. Al compilar funciones de estilo FaaS, Azure Functions es la opción ideal. El modelo de programación de Azure Functions está disponible como una imagen de contenedor base, lo que permite moverlo a otras plataformas de proceso basadas en contenedores, de tal forma que los equipos pueden reutilizar el código a medida que cambian los requisitos del entorno.

### <a name="azure-spring-cloud"></a>Azure Spring Cloud
Azure Spring Cloud facilita la implementación de aplicaciones de microservicio de Spring Boot en Azure sin necesidad de realizar cambios en el código. El servicio administra la infraestructura de las aplicaciones de Spring Cloud, con el fin de que los desarrolladores puedan centrarse en el código. Azure Spring Cloud proporciona administración del ciclo de vida mediante el uso de una supervisión y un diagnóstico completos, administración de la configuración, detección de servicios, integración de CI/CD e implementaciones blue-green, entre otros. Si su equipo u organización utilizan Spring principalmente, Azure Spring Cloud es una opción ideal.

## <a name="next-steps"></a>Pasos siguientes

> [!div class="nextstepaction"]
> [Implementación de la primera aplicación de contenedor](get-started.md)
