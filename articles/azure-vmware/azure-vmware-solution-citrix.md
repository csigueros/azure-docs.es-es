---
title: Implementación de Citrix en Azure VMware Solution
description: Aprenda a implementar VMware Citrix en Azure VMware Solution.
ms.topic: how-to
ms.date: 11/02/2021
ms.custom: ignite-fall-2021
ms.openlocfilehash: 7d3acd7ac8772a3d6e501b43f7277bcb57d8cd08
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/02/2021
ms.locfileid: "131017393"
---
# <a name="deploy-citrix-on-azure-vmware-solution"></a>Implementación de Citrix en Azure VMware Solution

El servicio Citrix Virtual Apps and Desktops admite Azure VMware Solution. Azure VMware Solution proporciona una infraestructura en la nube que contiene clústeres de vSphere que ha creado la infraestructura de Azure. Puede aprovechar el servicio Citrix Virtual Apps and Desktops para usar Azure VMware Solution y así aprovisionar la carga de trabajo del [Agente de entrega virtual (VDA)](https://www.citrix.com/downloads/xendesktop/components/xendesktop-and-xenapp-76-VDA.html) de la misma manera que usaría vSphere en entornos locales. 

[Obtenga más información sobre Citrix Virtual Apps and Desktops](https://www.citrix.com/products/citrix-virtual-apps-and-desktops/)

[Guía de implementación](https://docs.citrix.com/en-us/citrix-virtual-apps-desktops-service/install-configure/resource-location/azure-resource-manager.html#azure-vmware-solution-avs-integration)

[Resumen de la solución](https://www.citrix.com/content/dam/citrix/en_us/documents/solution-brief/citrix-virtual-apps-and-desktop-service-on-azure-vmware-solution.pdf)

**Preguntas más frecuentes (revise las preguntas y respuestas)**
 
- Q. ¿Puedo migrar mis aplicaciones y escritorios de Citrix existentes a Azure VMware Solution, o bien usar un entorno híbrido que tenga cargas de trabajo de Citrix locales y en la nube basadas en Azure VMware Solution? 

    A. Sí. Puede usar las mismas imágenes de máquina, paquetes de aplicación y procesos que usa actualmente. Puede vincular sin problemas entornos locales y basados en Azure VMware Solution para realizar una migración.

- Q. ¿Citrix se puede implementar como un entorno independiente dentro Azure VMware Solution? 

    A. Sí. Puede migrar, operar un entorno híbrido o implementar un entorno independiente directamente en Azure VMware Solution. 

- Q. ¿Azure VMware Solution admite PVS y MCS? 

    A. Sí 

- Q. ¿Se admiten cargas de trabajo basadas en GPU en la instancia de Citrix de Azure VMware Solution? 

    A. De momento, no. Sin embargo, las cargas de trabajo de Citrix para Microsoft Azure admiten GPU si ese caso de uso es importante para usted. 

- Q. ¿Se admite Azure VMware Solution con implementaciones de Citrix locales o LTSR?  

    A. No.  Azure VMware Solution solo se admite con las ofertas del servicio Citrix Virtual Apps and Desktops.  

- Q. ¿A quién puedo llamar para obtener soporte técnico? 

    A. Los clientes deben ponerse en contacto mediante la web de Citrix www.citrix.com/support para obtener ayuda. 

- Q. ¿Puedo usar mi ventaja de Azure Virtual Desktop de Microsoft con Citrix en Azure VMware Solution?
 
    A. No. Las ventajas de Azure Virtual Desktop solo se aplican a cargas de trabajo nativas de Microsoft Azure. El servicio Citrix Virtual Apps y Desktops es una oferta nativa de Azure que puede aplicar la ventaja de Azure Virtual Desktop junto con la implementación de Azure VMware Solution. 

- Q. ¿Cómo compro el servicio Citrix Virtual Apps and Desktops para usar Azure VMware Solution? 
    
    A. Puede comprar ofertas de Citrix a través de su asociado de Citrix o directamente desde Azure Marketplace. 
