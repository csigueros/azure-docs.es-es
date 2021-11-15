---
title: 'Ejemplos de plantillas de Azure Resource Manager: Azure Front Door'
description: Conozca los ejemplos de plantillas de Resource Manager para Azure Front Door, como las plantillas para crear una instancia básica de Front Door y configurar la limitación de velocidad de Front Door.
services: frontdoor
documentationcenter: ''
author: duongau
ms.service: frontdoor
ms.topic: sample
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 11/01/2021
ms.author: duau
ms.openlocfilehash: db0bf4783de324af2ab4e5a5ec6bfbaa18ad33cf
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/03/2021
ms.locfileid: "131449780"
---
# <a name="azure-resource-manager-deployment-model-templates-for-front-door"></a>Plantillas del modelo de implementación de Azure Resource Manager para Front Door

En la tabla siguiente se incluyen vínculos a las plantillas del modelo de implementación de Azure Resource Manager para Azure Front Door.

## <a name="azure-front-door"></a>Azure Front Door

| Plantilla | Descripción |
| ---| ---|
| [Create a basic Front Door](https://github.com/Azure/azure-quickstart-templates/tree/master/quickstarts/microsoft.network/front-door-create-basic)| Crea una configuración básica de Front Door con un único back-end. |
| [Create a Front Door with multiple backends and backend pools and URL based routing](https://github.com/Azure/azure-quickstart-templates/tree/master/quickstarts/microsoft.network/front-door-create-multiple-backends)| Crea una instancia de Front Door con equilibrio de carga configurado para varios back-ends en el grupo de back-ends y también a través de grupos de back-ends en función de la ruta de acceso de la URL. |
| [Incorporación de un dominio personalizado y un certificado TLS administrado con Front Door](https://github.com/Azure/azure-quickstart-templates/tree/master/quickstarts/microsoft.network/front-door-custom-domain)| Agregue un dominio personalizado a su instancia de Front Door y use un certificado TLS administrado por Front Door. |
| [Incorporación de un dominio personalizado y un certificado TLS administrado por el cliente con Front Door](https://github.com/Azure/azure-quickstart-templates/tree/master/quickstarts/microsoft.network/front-door-custom-domain-customer-certificate)| Agregue un dominio personalizado a su instancia de Front Door y use su propio certificado TLS mediante Key Vault. |
| [Create Front Door with geo filtering](https://github.com/Azure/azure-quickstart-templates/tree/master/quickstarts/microsoft.network/front-door-geo-filtering)| Crea una instancia de Front Door que permite o bloquea tráfico desde determinados países y regiones. |
| [Control Health Probes for your backends on Front Door](https://github.com/Azure/azure-quickstart-templates/tree/master/quickstarts/microsoft.network/front-door-health-probes)| Actualiza su instancia de Front Door para cambiar la configuración de sondeo de estado mediante la actualización de la ruta de acceso de sondeo y también los intervalos en los que se enviarán los sondeos. |
| [Create Front Door with Active/Standby backend configuration](https://github.com/Azure/azure-quickstart-templates/tree/master/quickstarts/microsoft.network/front-door-priority-lb)| Crea una instancia de Front Door que demuestra el enrutamiento basado en la prioridad para la topología de aplicación activo/en espera; es decir, mediante el envío de forma predeterminada de todo el tráfico al back-end principal (prioridad más alta) hasta que deja de estar disponible. |
| [Create Front Door with caching enabled for certain routes](https://github.com/Azure/azure-quickstart-templates/tree/master/quickstarts/microsoft.network/front-door-create-caching)| Crea una instancia de Front Door con el almacenamiento en caché habilitado para la configuración de enrutamiento definida, lo que almacena en caché los recursos estáticos para la carga de trabajo. |
| [Configure Session Affinity for your Front Door host names](https://github.com/Azure/azure-quickstart-templates/tree/master/quickstarts/microsoft.network/front-door-session-affinity) | Actualiza una instancia de Front Door para habilitar la afinidad de sesión para el host de front-end; por lo tanto, envía el tráfico posterior desde la misma sesión de usuario para el mismo back-end. |
| [Configure Front Door for client IP allowlisting or blocklisting](https://github.com/Azure/azure-quickstart-templates/tree/master/quickstarts/microsoft.network/front-door-waf-clientip)| Configura una instancia de Front Door para restringir el tráfico de determinadas direcciones IP cliente mediante el control de acceso personalizado con direcciones IP de cliente. |
| [Configure Front Door to take action with specific http parameters](https://github.com/Azure/azure-quickstart-templates/tree/master/quickstarts/microsoft.network/front-door-waf-http-params)| Configura una instancia de Front Door para permitir o bloquear cierto tráfico según los parámetros de http en la solicitud entrante mediante el uso de reglas personalizadas para el control de acceso con parámetros http. |
| [Configure Front Door rate limiting](https://github.com/Azure/azure-quickstart-templates/tree/master/quickstarts/microsoft.network/front-door-rate-limiting)| Configura una instancia de Front Door para limitar la velocidad del tráfico entrante para un host de front-end determinado. |
| | |

## <a name="azure-front-door-standardpremium-preview"></a>Azure Front Door Standard/Premium (versión preliminar)

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

## <a name="next-steps"></a>Pasos siguientes

- Aprenda a [crear una instancia de Front Door](quickstart-create-front-door.md).
- Más información acerca de cómo [funciona Front Door](front-door-routing-architecture.md).
