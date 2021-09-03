---
title: 'Conceptos: API Management'
description: Obtenga información sobre la manera en que API Management protege las API que se ejecutan en máquinas virtuales (VM) de Azure VMware Solution
ms.topic: conceptual
ms.date: 04/28/2021
ms.openlocfilehash: 14b2d81d0080ebf389c817d1c718d207aa922b0d
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/13/2021
ms.locfileid: "121752108"
---
# <a name="publish-and-protect-apis-running-on-azure-vmware-solution-vms"></a>Publicación y protección de API que se ejecutan en máquinas virtuales basadas en Azure VMware Solution

Con [API Management](https://azure.microsoft.com/services/api-management/) de Microsoft Azure, puede publicar de forma segura a consumidores internos o externos.  Las SKU Desarrollador (desarrollo) y Premium (producción) son las únicas que permiten la integración de Azure Virtual Network para publicar API que se ejecuten en cargas de trabajo de Azure VMware Solution. Además, estas dos SKU habilitan la conectividad entre el servicio API Management y el back-end.

La configuración de API Management es la misma para los servicios de back-end que se ejecutan en las máquinas virtuales (VM) de Azure VMware Solution y en el entorno local. API Management también configura la IP virtual del equilibrador de carga como punto de conexión de back-end para las dos implementaciones cuando el servidor de back-end se coloca detrás de una instancia de NSX Load Balancer en Azure VMware Solution.

## <a name="external-deployment"></a>Implementación externa

Una implementación externa publica las API utilizadas por usuarios externos en las que se usa un punto de conexión público. Los desarrolladores y los ingenieros de DevOps pueden administrar API a través de Azure Portal o PowerShell, así como desde el portal para desarrolladores de API Management.

El diagrama de implementación externa muestra todo el proceso y los actores implicados (mostrados en la parte superior). Los actores son:

- **Administradores:** representa el equipo de administradores o DevOps, que administra Azure VMware Solution en Azure Portal y con mecanismos de automatización como PowerShell o Azure DevOps.

- **Usuarios:** representa los consumidores de las API expuestas, y pueden ser los usuarios y los servicios que consumen las API.

El flujo de tráfico pasa por la instancia de API Management, que extrae los servicios de back-end conectados a la red virtual del centro. La puerta de enlace de ExpressRoute enruta el tráfico al canal Global Reach de ExpressRoute. A continuación, el tráfico llega a una instancia de NSX Load Balancer, que distribuye el tráfico entrante a las distintas instancias de servicios de back-end.

API Management tiene una API pública de Azure, por lo que se recomienda activar el servicio Azure DDOS Protection. 

:::image type="content" source="media/api-management/api-management-external-deployment.png" alt-text="Diagrama en el que se muestra una implementación de API Management externa para Azure VMware Solution" border="false":::


## <a name="internal-deployment"></a>Implementación interna

Una implementación interna publica las API que consumen los usuarios o sistemas internos. Los equipos de DevOps y los desarrolladores de API usan las mismas herramientas de administración y el mismo portal para desarrolladores que en la implementación externa.

Use [Azure Application Gateway](../api-management/api-management-howto-integrate-internal-vnet-appgateway.md) para implementaciones internas con el fin de crear un punto de conexión público y seguro para la API.  Las capacidades de la puerta de enlace se usan para crear una implementación híbrida que habilita distintos escenarios.  

* Utilizar el mismo recurso de API Management para su uso por parte de usuarios internos y externos.

* Tener un único recurso de API Management con un subconjunto de API definido y disponible para los consumidores externos.

* Proporcionar una solución fácil para activar y desactivar el acceso a API Management desde la red Internet pública.

En el diagrama de implementación siguiente se muestran los consumidores, que pueden ser internos o externos, y cada tipo tiene acceso a las mismas API o a API diferentes.

En una implementación interna, las API se exponen a la misma instancia de API Management. Delante de API Management, Application Gateway se implementa con la capacidad Azure Web Application Firewall (WAF) activada. También se implementa un conjunto de agentes de escucha de HTTP y las reglas para filtrar el tráfico, exponiendo solo un subconjunto de los servicios back-end que se ejecutan en Azure VMware Solution.


* El tráfico interno se enruta a través de la puerta de enlace de ExpressRoute hacia Azure Firewall y, a continuación, hacia API Management, directamente o a través de las reglas de tráfico.   

* El tráfico externo entra en Azure a través de Application Gateway, que usa el nivel de protección externa para API Management.


:::image type="content" source="media/api-management/api-management-internal-deployment.png" alt-text="Diagrama en el que se muestra una implementación de API Management interna para Azure VMware Solution" lightbox="media/api-management/api-management-internal-deployment.png" border="false":::
