---
title: Ejemplos de plantillas de Resource Manager para Azure Front Door
description: Información sobre algunas plantillas de ejemplo de Azure Resource Manager para Azure Front Door.
services: frontdoor
author: johndowns
ms.author: jodowns
ms.service: frontdoor
ms.topic: sample
ms.date: 05/11/2021
ms.openlocfilehash: e55628988d80f13b86481477d6f8e59ac4ceb30b
ms.sourcegitcommit: beff1803eeb28b60482560eee8967122653bc19c
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 07/07/2021
ms.locfileid: "113438591"
---
# <a name="azure-resource-manager-templates-for-azure-front-door"></a>Plantillas de Azure Resource Manager para Azure Front Door

> [!Note]
> Esta documentación trata sobre Azure Front Door Estándar/Prémium (versión preliminar). ¿Busca información sobre Azure Front Door? Consulte [aquí](../front-door-overview.md).

En la tabla siguiente, se incluyen vínculos a plantillas de Azure Resource Manager para Azure Front Door con algunas arquitecturas de referencia, como otros servicios de Azure.

| Muestra | Descripción |
|-|-|
| [Front Door (creación rápida)](https://github.com/Azure/azure-quickstart-templates/tree/master/quickstarts/microsoft.network/front-door-standard-premium/) | Crea un perfil Front Door básico, con un punto de conexión, un grupo de origen, un origen y una ruta.  |
| [Conjunto de reglas](https://github.com/Azure/azure-quickstart-templates/tree/master/quickstarts/microsoft.network/front-door-standard-premium-rule-set/) | Crea un perfil de Front Door y un conjunto de reglas.  |
| [Directiva de WAF con conjunto de reglas administradas](https://github.com/Azure/azure-quickstart-templates/tree/master/quickstarts/microsoft.network/front-door-premium-waf-managed/) | Crea un perfil de Front Door y un firewall de aplicaciones web con un conjunto de reglas administradas.  |
| [Directiva de WAF con regla personalizada](https://github.com/Azure/azure-quickstart-templates/tree/master/quickstarts/microsoft.network/front-door-standard-premium-waf-custom/) | Crea un perfil de Front Door y un firewall de aplicaciones web con regla personalizada.  |
| [Directiva de WAF con límite de frecuencia](https://github.com/Azure/azure-quickstart-templates/tree/master/quickstarts/microsoft.network/front-door-standard-premium-rate-limit/) | Crea un perfil de Front Door y un firewall de aplicaciones web con una regla personalizada para limitar la frecuencia.  |
| [Directiva de WAF con filtrado geográfico](https://github.com/Azure/azure-quickstart-templates/tree/master/quickstarts/microsoft.network/front-door-standard-premium-geo-filtering/) | Crea un perfil de Front Door y un firewall de aplicaciones web con una regla personalizada para filtrado geográfico.  |
|**Orígenes de App Service**| **Descripción** |
| [App Service](https://github.com/Azure/azure-quickstart-templates/tree/master/quickstarts/microsoft.network/front-door-standard-premium-app-service-public) | Crea una aplicación de App Service con un punto de conexión público y un perfil de Front Door.  |
| [App Service con Private Link](https://github.com/Azure/azure-quickstart-templates/tree/master/quickstarts/microsoft.network/front-door-premium-app-service-private-link) | Crea una aplicación de App Service con un punto de conexión privado y un perfil de Front Door.  |
|**Orígenes de Azure Functions**| **Descripción** |
| [Funciones de Azure](https://github.com/Azure/azure-quickstart-templates/tree/master/quickstarts/microsoft.network/front-door-standard-premium-function-public/) | Crea una aplicación de Azure Functions con un punto de conexión público y un perfil de Front Door.  |
| [Azure Functions con Private Link](https://github.com/Azure/azure-quickstart-templates/tree/master/quickstarts/microsoft.network/front-door-premium-function-private-link) | Crea una aplicación de Azure Functions con un punto de conexión privado y un perfil de Front Door.  |
|**Orígenes de API Management**| **Descripción** |
| [API Management (externa)](https://github.com/Azure/azure-quickstart-templates/tree/master/quickstarts/microsoft.network/front-door-standard-premium-api-management-external) | Crea una instancia de API Management con la integración de una red virtual externa y un perfil de Front Door.  |
|**Orígenes de Storage**| **Descripción** |
| [Sitio web estático de almacenamiento](https://github.com/Azure/azure-quickstart-templates/tree/master/quickstarts/microsoft.network/front-door-standard-premium-storage-static-website) | Crea una cuenta de Azure Storage y un sitio web estático con un punto de conexión público y un perfil de Front Door.  |
| [Blobs de almacenamiento con Private Link](https://github.com/Azure/azure-quickstart-templates/tree/master/quickstarts/microsoft.network/front-door-premium-storage-blobs-private-link) | Crea una cuenta de Azure Storage y un contenedor de blobs con un punto de conexión privado y un perfil de Front Door.  |
|**Orígenes de Application Gateway**| **Descripción** |
| [Application Gateway](https://github.com/Azure/azure-quickstart-templates/tree/master/quickstarts/microsoft.network/front-door-standard-premium-application-gateway-public) | Crea una instancia de Application Gateway y un perfil de Front Door. |
|**Orígenes de máquina virtual**| **Descripción** |
| [Máquina virtual con servicio de Private Link](https://github.com/Azure/azure-quickstart-templates/tree/master/quickstarts/microsoft.network/front-door-premium-vm-private-link) | Crea una máquina virtual y un servicio de Private Link, así como un perfil de Front Door. |
| | |
