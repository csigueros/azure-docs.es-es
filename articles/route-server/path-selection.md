---
title: Selección de rutas de acceso con Azure Route Server
description: Obtenga información sobre cómo Azure Route Server habilita la selección de rutas de acceso para la aplicación virtual de red.
services: route-server
author: duongau
ms.service: route-server
ms.topic: conceptual
ms.date: 11/09/2021
ms.author: duau
ms.openlocfilehash: 011156427bfaed238679d4fc4b4e6d51e566ec6e
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/11/2021
ms.locfileid: "132350017"
---
# <a name="path-selection-with-azure-route-server"></a>Selección de rutas de acceso con Azure Route Server

Azure Route Server le permite controlar el tráfico para enrutarlo a través de su NVA de SDWAN por Internet hacia su red local. En este artículo, hablaremos sobre cómo Azure Route Server habilita la selección de rutas de acceso, lo que le permite configurar la NVA de SNWAN para que tenga una [preferencia de enrutamiento](../virtual-network/ip-services/routing-preference-overview.md) al comunicarse con la red local.

## <a name="how-does-it-work"></a>¿Cómo funciona?

:::image type="content" source="./media/path-selection/routing-preference.png" alt-text="Diagrama de Azure Route Server y una SDWAN con la dirección IP de enrutamiento de Internet.":::

### <a name="cold-potato-routing"></a>Enrutamiento de "patata fría"

Al implementar una NVA de SDWAN en la misma red virtual que Azure Route Server, se configura con una dirección IP de red de Microsoft. La ruta de acceso del tráfico al entorno local usará la red global de Microsoft y sale de la red de Microsoft más cercana al destino. El enrutamiento desde el entorno local especificará la red de Microsoft más cercana al usuario en la ruta de retorno. Este método de enrutamiento optimiza el rendimiento y, por tanto, proporciona la mejor experiencia posible con un coste. 

### <a name="hot-potato-routing"></a>Enrutamiento de "patata caliente"

Como forma de optimizar el coste, se indica un segundo método de enrutamiento asignando a su NVA de SDWAN una dirección IP de Internet. Cuando el tráfico se enruta al entorno local, sale de la red de Microsoft en la misma región en la que se hospeda el servicio. A continuación, se enruta a través de Internet mediante la red del ISP. El enrutamiento desde el entorno local entrará en la red de Microsoft más cercana a la región de servicio hospedada. Este método de enrutamiento proporcionará el mejor precio global al completar una tarea como la transferencia de una gran cantidad de datos.

## <a name="how-to-enable-routing-preference"></a>¿Cómo habilitar la preferencia de enrutamiento?

Al crear una nueva dirección IP pública, seleccione **Internet** como *Preferencia de enrutamiento*. A continuación, asigne la dirección IP pública a la NVA de SDWAN.

:::image type="content" source="./media/path-selection/internet-ip.png" alt-text="Captura de pantalla de la preferencia de enrutamiento de Internet para una dirección IP pública.":::

Microsoft recomienda implementar una solución de conectividad usando tanto la red de Microsoft como Internet para proporcionar a su entorno una capa adicional de resistencia.

## <a name="next-steps"></a>Pasos siguientes

- Más información sobre [Azure Route Server](route-server-faq.md).
- Consulte [Configuración de una instancia de Azure Route Server](quickstart-configure-route-server-powershell.md).
