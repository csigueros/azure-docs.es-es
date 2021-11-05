---
title: 'Microsoft Defender para DNS: ventajas y características'
description: Obtenga información sobre las ventajas y características de Microsoft Defender para DNS
author: memildin
ms.author: memildin
ms.date: 10/20/2021
ms.topic: overview
ms.service: security-center
manager: rkarlin
ms.custom: ignite-fall-2021
ms.openlocfilehash: 00f783b82b9a5b1aa8e6152e513fa91ab3213929
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/02/2021
ms.locfileid: "131055870"
---
# <a name="introduction-to-microsoft-defender-for-dns"></a>Introducción a Microsoft Defender para DNS

[!INCLUDE [Banner for top of topics](./includes/banner.md)]

Microsoft Defender para DNS proporciona una capa adicional de protección a los recursos que usan la funcionalidad de [resolución de nombres proporcionados por Azure](../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md#azure-provided-name-resolution) de Azure DNS. 

En Azure DNS, Defender para DNS supervisa las consultas de estos recursos y detecta actividades sospechosas sin necesidad de agentes adicionales en los recursos.

## <a name="availability"></a>Disponibilidad

|Aspecto|Detalles|
|----|:----|
|Estado de la versión:|Disponibilidad general (GA)|
|Precios:|**Microsoft Defender para DNS** se factura como se muestra en [la página de precios](https://azure.microsoft.com/pricing/details/security-center/)|
|Nubes:|:::image type="icon" source="./media/icons/yes-icon.png"::: Nubes comerciales<br>:::image type="icon" source="./media/icons/yes-icon.png"::: Azure China 21Vianet<br>:::image type="icon" source="./media/icons/yes-icon.png"::: Azure Government|
|||

## <a name="what-are-the-benefits-of-microsoft-defender-for-dns"></a>¿Cuáles son las ventajas de Microsoft Defender para DNS?

Microsoft Defender para DNS detecta actividades sospechosas y anómalas, como:

- **Filtrado de datos** de los recursos de Azure mediante la tunelización de DNS.
- **Malware** que se comunica con los servidores de comandos y control
- **Ataques de DNS**: comunicación con solucionadores de DNS malintencionados 
- **Comunicación con dominios usados para actividades malintencionadas** como la suplantación de identidad (phishing) y la minería de datos de cifrado

En la [página de referencia de alertas](alerts-reference.md#alerts-dns) se encuentra una lista completa de las alertas que proporciona Microsoft Defender para DNS.

## <a name="dependencies"></a>Dependencias

Microsoft Defender para DNS no usa agentes. 

Para proteger la capa de DNS, habilite Microsoft Defender para DNS en todas las suscripciones, tal como se describe en el apartado [Habilitación de las protecciones mejoradas](enable-enhanced-security.md).


## <a name="respond-to-microsoft-defender-for-dns-alerts"></a>Respuesta a alertas de Microsoft Defender para DNS

Cuando reciba una alerta de Microsoft Defender para DNS, se recomienda investigar y responder a la alerta, tal y como se describe a continuación. Microsoft Defender para DNS protege todos los recursos conectados, por lo que, incluso si está familiarizado con la aplicación o el usuario que desencadenó la alerta, es importante comprobar la situación relativa a cada alerta.  


### <a name="step-1-contact"></a>Paso 1. Contacto

1. Póngase en contacto con el propietario del recurso para determinar si se esperaba el comportamiento o era intencionado.
1. Si la actividad se espera, descarte la alerta.
1. Si la actividad es inesperada, trate el recurso como si potencialmente corriera peligro y mitíguelo como se describe en el paso siguiente.

### <a name="step-2-immediate-mitigation"></a>Paso 2. Mitigación inmediata 

1. Aísle el recurso de la red para evitar el movimiento lateral.
1. Ejecute un examen de antimalware completo en el recurso, siguiendo cualquier consejo de corrección resultante.
1. Revise el software instalado y en ejecución en el recurso y quite los paquetes que no conozca o que no desee.
1. Revierta la máquina a un estado bueno conocido, vuelva a instalar el sistema operativo si fuera necesario y restaure el software de un origen que esté comprobado que no tiene malware.
1. Resuelva las recomendaciones para la máquina de Microsoft Defender para nube y corrija los problemas de seguridad resaltados para evitar infracciones futuras.


## <a name="next-steps"></a>Pasos siguientes

En este artículo, ha obtenido información sobre Microsoft Defender para DNS. 

> [!div class="nextstepaction"]
> [Habilitación de las protecciones mejoradas](enable-enhanced-security.md)

Para obtener material relacionado, vea el siguiente artículo: 

- Defender para nube puede generar alertas de seguridad o recibirlas de otros productos de seguridad. Para exportar todas estas alertas a Microsoft Sentinel, a cualquier SIEM de terceros o a cualquier otra herramienta externa, siga las instrucciones de [Exportación de alertas a un SIEM](continuous-export.md).
