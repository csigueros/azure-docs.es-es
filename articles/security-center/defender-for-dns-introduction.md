---
title: 'Azure Defender para DNS: ventajas y características'
description: Obtenga información sobre las ventajas y características de Azure Defender para DNS.
author: memildin
ms.author: memildin
ms.date: 10/11/2021
ms.topic: overview
ms.service: security-center
manager: rkarlin
ms.openlocfilehash: ad2b761a747401646c48dcbf32385b8e5ec153c1
ms.sourcegitcommit: ee5d9cdaf691f578f2e390101bf5350859d85c67
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2021
ms.locfileid: "129740234"
---
# <a name="introduction-to-azure-defender-for-dns"></a>Introducción a Azure Defender para DNS

Azure Defender para DNS proporciona una capa adicional de protección a los recursos que usan la funcionalidad de [resolución de nombres proporcionados por Azure](../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md#azure-provided-name-resolution) de Azure DNS. 

En Azure DNS, Defender para DNS supervisa las consultas de estos recursos y detecta actividades sospechosas sin necesidad de agentes adicionales en los recursos.

## <a name="availability"></a>Disponibilidad

|Aspecto|Detalles|
|----|:----|
|Estado de la versión:|Disponibilidad general (GA)|
|Precios:|**Azure Defender para DNS** se factura como se indica en [Precios de Security Center](https://azure.microsoft.com/pricing/details/security-center/).|
|Nubes:|:::image type="icon" source="./media/icons/yes-icon.png"::: Nubes comerciales<br>:::image type="icon" source="./media/icons/yes-icon.png"::: Azure China 21Vianet<br>:::image type="icon" source="./media/icons/yes-icon.png"::: Azure Government|
|||

## <a name="what-are-the-benefits-of-azure-defender-for-dns"></a>Ventajas de Azure Defender para DNS

Azure Defender para DNS detecta actividades sospechosas y anómalas, como:

- **Filtrado de datos** de los recursos de Azure mediante la tunelización de DNS.
- **Malware** que se comunica con los servidores de comandos y control
- **Ataques de DNS**: comunicación con solucionadores de DNS malintencionados 
- **Comunicación con dominios usados para actividades malintencionadas** como la suplantación de identidad (phishing) y la minería de datos de cifrado

En la [página de referencia de alertas](alerts-reference.md#alerts-dns) se encuentra una lista completa de las alertas que proporciona Azure Defender para DNS.

## <a name="dependencies"></a>Dependencias

Azure Defender para DNS no usa agentes. 

Para proteger la capa de DNS, habilite Azure Defender para DNS en todas las suscripciones, tal como se describe en el apartado [Habilitación de Azure Defender](enable-azure-defender.md).


## <a name="next-steps"></a>Pasos siguientes

En este artículo, ha obtenido información sobre Azure Defender para DNS. 

> [!div class="nextstepaction"]
> [Habilitación de Azure Defender](enable-azure-defender.md)

Para obtener material relacionado, vea el siguiente artículo: 

- Las alertas de seguridad las puede haber generado Security Center, o bien las puede haber recibido Security Center de distintos productos de seguridad. Para exportar todas estas alertas a Azure Sentinel, a cualquier SIEM de terceros o a cualquier otra herramienta externa, siga las instrucciones de [Exportación de alertas a un SIEM](continuous-export.md).
