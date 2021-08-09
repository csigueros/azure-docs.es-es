---
author: cherylmc
ms.service: virtual-wan
ms.topic: include
ms.date: 06/02/2021
ms.author: cherylmc
ms.openlocfilehash: cf11d4614cd4b6c3c387ef83b63da506a1702afc
ms.sourcegitcommit: c385af80989f6555ef3dadc17117a78764f83963
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 06/04/2021
ms.locfileid: "111411920"
---
### <a name="i-am-a-network-appliance-partner-and-want-to-get-our-nva-in-the-hub-can-i-join-this-partner-program"></a>Soy asociado de dispositivos de red y quiero que nuestra NVA esté en el centro de conectividad. ¿Puedo participar en este programa para asociados?

Desafortunadamente, no tenemos capacidad para incorporar nuevas ofertas para asociados en este momento. Vuelva a consultarnos en una fecha posterior.

### <a name="can-i-deploy-any-nva-from-azure-marketplace-into-the-virtual-wan-hub"></a>¿Puedo implementar cualquier NVA desde Azure Marketplace en el centro de conectividad de Virtual WAN?

Actualmente, solo [Barracuda CloudGen WAN](https://aka.ms/BarracudaMarketPlaceOffer), [Cisco Cloud vWAN Application](https://azuremarketplace.microsoft.com/marketplace/apps/cisco.cisco_cloud_vwan_app?tab=Overview) y [VMware Sd-WAN](https://aka.ms/vmwareMarketplaceLink) son los únicos productos disponibles para implementarse en el centro de conectividad de Virtual WAN.

### <a name="what-is-the-cost-of-the-nva"></a>¿Cuál es el costo de la NVA?

Debe adquirir una licencia para la NVA del proveedor de la NVA. Para la licencia de la NVA Barracuda CloudGen WAN, consulte la [página de Barracuda CloudGen WAN](https://www.barracuda.com/products/cloudgenwan). Actualmente, Cisco solo ofrece el modelo de licencia de BYOL (traiga su propia licencia), que debe ser adquirido directamente en Cisco. Además, Microsoft también puede cobrarle por las unidades de la infraestructura de la NVA que consuma, así como cualquier otro recurso que use. Para más información, consulte [Conceptos de precios](../articles/virtual-wan/pricing-concepts.md).

### <a name="can-i-deploy-an-nva-to-a-basic-hub"></a>¿Se puede implementar una NVA en un centro de conectividad Básico?

No. Si desea implementar una NVA, debe usar un centro de conectividad Estándar.

### <a name="can-i-deploy-an-nva-into-a-secure-hub"></a>¿Se puede implementar una NVA en un centro de conectividad Seguro?

Sí. Se puede implementar una NVA de asociado en un centro de conectividad con Azure Firewall.

### <a name="can-i-connect-any-cpe-device-in-my-branch-office-to-barracuda-cloudgen-wan-nva-in-the-hub"></a>¿Se pueden conectar todos los dispositivos CPE de una sucursal a la NVA Barracuda CloudGen WAN del centro de conectividad?

No. Barracuda CloudGen WAN solo es compatible con dispositivos CPE de Barracuda. Para más información acerca de los requisitos de CloudGen WAN, consulte la [página de CloudGen WAN de Barracuda](https://www.barracuda.com/products/cloudgenwan). Para Cisco, hay varios dispositivos SD-WAN CPE que son compatibles. Vea la documentación de [Cisco Cloud OnRamp for Multi-Cloud](https://www.cisco.com/c/en/us/td/docs/routers/sdwan/configuration/cloudonramp/ios-xe-17/cloud-onramp-book-xe/cloud-onramp-multi-cloud.html#Cisco_Concept.dita_c61e0e7a-fff8-4080-afee-47b81e8df701) para ver la compatibilidad con CPE.

### <a name="what-routing-scenarios-are-supported-with-nva-in-the-hub"></a>¿Qué escenarios de enrutamiento son compatibles con NVA en el centro de conectividad?

Todos los escenarios de enrutamiento compatibles con Virtual WAN son compatibles con las NVA del centro de conectividad.
