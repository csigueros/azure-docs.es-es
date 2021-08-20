---
title: Azure Front Door Estándar/Prémium| Microsoft Docs
description: En este artículo se ofrece información general sobre Azure Front Door Estándar/Prémium.
services: frontdoor
author: duongau
ms.service: frontdoor
ms.topic: overview
ms.workload: infrastructure-services
ms.date: 02/18/2021
ms.author: duau
ms.openlocfilehash: acae0362d664567bb15b964f396f0c300135ed1c
ms.sourcegitcommit: f2eb1bc583962ea0b616577f47b325d548fd0efa
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 07/28/2021
ms.locfileid: "114726761"
---
# <a name="what-is-azure-front-door-standardpremium-preview"></a>¿Qué es Azure Front Door Standard/Premium (versión preliminar)?

> [!IMPORTANT]
> Esta documentación trata sobre Azure Front Door Estándar/Prémium (versión preliminar). ¿Busca información sobre Azure Front Door? Vea la [documentación sobre Azure Front Door](../front-door-overview.md).

Azure Front Door Estándar/Premium es una red CDN de nube moderna, rápida, confiable y segura que utiliza la red perimetral global de Microsoft y se integra con la protección contra amenazas inteligente. Combina las funcionalidades de Azure Front Door, el estándar de Azure Content Delivery Network (CDN) y Azure Web Application Firewall (WAF) en una única plataforma de red CDN de nube segura.

Con Azure Front Door Estándar/Prémium, puede transformar sus aplicaciones empresariales y de consumidor globales en aplicaciones modernas personalizadas, de alto rendimiento y seguras, con contenido que llega a una audiencia global en el perímetro de la red cerca del usuario. También permite que la aplicación se escale horizontalmente sin preparación, al tiempo que se beneficia del equilibrio de carga HTTP global con conmutación por error instantánea.

   :::image type="content" source="../media/overview/front-door-overview.png" alt-text="Arquitectura de Azure Front Door Estándar/Prémium" lightbox="../media/overview/front-door-overview-expanded.png":::

Azure Front Door Estándar/Prémium funciona en la capa 7 (HTTP/HTTPS) y usa el protocolo de difusión por proximidad con división TCP y la red global de Microsoft para mejorar la conectividad global. Azure Front Door utiliza el método de enrutamiento que se haya personalizado mediante el conjunto de reglas para enrutar las solicitudes de cliente al origen más rápido y que esté más disponible. Un origen de aplicación es cualquier servicio accesible desde Internet hospedado dentro o fuera de Azure. Azure Front Door Estándar/Prémium proporciona una serie de métodos de enrutamiento del tráfico y opciones de supervisión de estado del origen para satisfacer las distintas necesidades de las aplicaciones y los escenarios de conmutación automática por error. Al igual que Traffic Manager, Front Door es resistente a errores, incluidos los que afectan a una región completa de Azure.

Azure Front-Door también protege la aplicación en el perímetro gracias a la protección integrada del firewall de aplicaciones web, la protección contra bots y la protección integrada contra denegación de servicio distribuido (DDoS) de capa 3 y 4. También protege los servidores back-end privados con el servicio de vínculo privado. Azure Front-Door ofrece la mejor seguridad a escala global de Microsoft.  

>[!NOTE]
> Azure ofrece un conjunto de soluciones de equilibrio de carga completamente administradas para sus escenarios.
>
> * Si busca un enrutamiento global basado en DNS y **no** tiene requisitos de finalización con el protocolo de seguridad de la capa de transporte (TLS) ("descarga SSL"), de procesamiento de niveles de aplicación o por solicitud HTTP/HTTPS, revise [Traffic Manager](../../traffic-manager/traffic-manager-overview.md).
> * Si quiere equilibrar la carga entre los servidores de una región en el nivel de aplicación, revise [Application Gateway](../../application-gateway/overview.md)
> * Para realizar el equilibrio de carga de capa de red, revise [Load Balancer](../../load-balancer/load-balancer-overview.md).
>
> Sus escenarios pueden beneficiarse de extremo a extremo de la combinación de estas soluciones según sea necesario.
> Si desea ver una comparación de las distintas opciones de equilibrio de carga de Azure, consulte [Información general sobre las opciones de equilibrio de carga en Azure](/azure/architecture/guide/technology-choices/load-balancing-overview).

> [!IMPORTANT]
> Azure Front Door Estándar/Prémium está disponible actualmente en versión preliminar pública.
> Esta versión preliminar se ofrece sin Acuerdo de Nivel de Servicio y no se recomienda para cargas de trabajo de producción. Es posible que algunas características no sean compatibles o que tengan sus funcionalidades limitadas.
> Para más información, consulte [Términos de uso complementarios de las Versiones Preliminares de Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="why-use-azure-front-door-standardpremium-preview"></a>¿Por qué se debe usar Azure Front Door Estándar/Prémium (versión preliminar)?

Azure Front Door Estándar/Prémium proporciona una única plataforma unificada que combina tanto la aceleración dinámica como estática, con integración de seguridad llave en mano incorporada y un modelo de precios sencillo y predecible. Front Door también le permite definir, administrar y supervisar el enrutamiento global de la aplicación.

Principales características incluidas con Azure Front Door Estándar/Prémium (versión preliminar):

- Rendimiento acelerado de las aplicaciones gracias al protocolo de difusión por proximidad (anycast) **[basado en la división TCP](../front-door-routing-architecture.md#splittcp)** .

- Supervisión de **[sondeos de estado](concept-health-probes.md)** y equilibrio de carga inteligentes entre **[orígenes](concept-origin.md)** .

- Definición del propio **[dominio personalizado](how-to-add-custom-domain.md)** con validación de dominios flexible.

- Seguridad de las aplicaciones con el **[firewall de aplicaciones web (WAF)](../../web-application-firewall/afds/afds-overview.md)** integrado.

- Descarga SSL y **[administración de certificados](how-to-configure-https-custom-domain.md)** integrada.

- Protección de los orígenes con **[Private Link](concept-private-link.md)** .  

- Enrutamiento y optimización del tráfico personalizables a través del **[conjunto de reglas](concept-rule-set.md)** .

- **[Informes integrados](how-to-reports.md)** con un panel todo en uno para los patrones de Front Door y de seguridad.

- **[Supervisión y alertas en tiempo real](how-to-monitor-metrics.md)** que se integran con Azure Monitor.

- **[Registro](how-to-logs.md)** de cada solicitud de Front Door y de los sondeos de estado con error.

- Compatibilidad nativa con la conectividad IPv6 de un extremo a otro y el protocolo HTTP/2.

## <a name="pricing"></a>Precios

Azure Front Door tiene dos SKU: Estándar y Prémium. Consulte el artículo donde se [comparan los distintos niveles](tier-comparison.md). Para obtener información sobre los precios, vea [Precios de Front Door](https://azure.microsoft.com/pricing/details/frontdoor/). 

## <a name="whats-new"></a>Novedades

Suscríbase a la fuente RSS y vea las actualizaciones más recientes de las características de Azure Front Door en la página [Actualizaciones de Azure](https://azure.microsoft.com/updates/?category=networking&query=Azure%20Front%20Door).

## <a name="next-steps"></a>Pasos siguientes

* Aprenda a [crear una instancia de Front Door](create-front-door-portal.md).
