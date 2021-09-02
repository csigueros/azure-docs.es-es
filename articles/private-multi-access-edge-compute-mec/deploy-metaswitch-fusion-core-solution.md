---
title: Implementación de Fusion Core en un dispositivo de Azure Stack Edge
description: Aprenda a implementar soluciones en la nube de Microsoft Azure y Metaswitch Networks que pueden ayudar a preparar mejor la red para el futuro, reducir los costos y crear nuevos modelos de negocio y flujos de ingresos.
author: djrmetaswitch
ms.service: private-multi-access-edge-compute-mec
ms.topic: how-to
ms.date: 06/16/2021
ms.author: drichards
ms.openlocfilehash: 93e7511cbe8202f15dd055467c253044fa617e74
ms.sourcegitcommit: 8b7d16fefcf3d024a72119b233733cb3e962d6d9
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 07/16/2021
ms.locfileid: "114286922"
---
# <a name="deploy-fusion-core-on-an-azure-stack-edge-device"></a>Implementación de Fusion Core en un dispositivo de Azure Stack Edge

En este artículo se proporciona información general de alto nivel sobre el proceso de implementación de Fusion Core en un dispositivo de Azure Stack Edge.

## <a name="collect-required-azure-resources"></a>Recopilación de los recursos de Azure necesarios

Debe tener lo siguiente:

- Una aplicación Azure Stack Edge Pro totalmente implementada con GPU. Los puertos del dispositivo deben estar conectados como se muestra a continuación.

  - Puerto 5: LAN
  - Puerto 6: WAN
  - Un puerto conectado a la red de administración. Puede elegir cualquier puerto, del 1 al 4. Debe haber habilitado el puerto elegido para el proceso, como se describe en el paso Habilitación de la red de proceso del [Tutorial: Configuración de la red para Azure Stack Edge Pro con GPU](../databox-online/azure-stack-edge-gpu-deploy-configure-network-compute-web-proxy.md).
- Una cuenta de Azure con una suscripción activa y acceso a lo siguiente.

  - El servicio Azure Network Function Manager. Este tiene el espacio de nombres del proveedor de recursos Microsoft.HybridNetwork. Para más información, consulte [¿Qué es Azure Network Function Manager?](../network-function-manager/overview.md)
  - La aplicación administrada Fusion Core - 5G packet core. Para solicitar acceso, visite [https://azuremarketplace.microsoft.com/marketplace/apps/metaswitch.fusioncore_0-1-0?tab=Overview](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/metaswitch.fusioncore_0-1-0?tab=Overview) y use el botón **PONERSE EN CONTACTO CONMIGO**. Nuestros representantes de ventas le proporcionarán información sobre cómo puede evaluar o comprar Fusion Core. Una vez que haya seleccionado una de estas opciones, le proporcionaremos acceso a la aplicación administrada.
  - El rol **Propietario** integrado en el ámbito de la suscripción. Si esto no es posible en su organización, póngase en contacto con su representante de soporte técnico de Metaswitch.
- Un objeto de **dispositivo de Azure Network Function Manager** configurado que actúe como dispositivo de Azure Stack Edge.

## <a name="deploy-fusion-core"></a>Implementación de Fusion Core

En el diagrama siguiente se muestra el proceso de implementación de Fusion Core, incluidos los objetos que creará y los métodos para la administración continua después de la instalación.

:::image type="content" source="./media/deploy-metaswitch-solution/fusion-core-on-azure-stack-edge-deployment-process.png" alt-text="Proceso de implementación de Fusion Core":::  

Después de seleccionar Fusion Core para ASE en Azure Marketplace, implementará una instancia con nombre de la aplicación administrada Fusion Core en el grupo de recursos de su elección de la suscripción de Azure.

La aplicación administrada Fusion Core crea un grupo de recursos administrados que contiene los recursos de Packet Core Network Function y Relay que se instalarán en el dispositivo de Azure Stack Edge (ASE).

- El recurso Packet Core Network Function define la versión de la máquina virtual base de Fusion Core y sus parámetros de configuración. De forma predeterminada, esto está oculto en Azure Portal.
- El recurso Relay contiene una serie de conexiones híbridas que proporcionan la conectividad necesaria para la instalación y el acceso remoto a las interfaces de supervisión.

Fusion Core se implementa automáticamente en el dispositivo ASE como se muestra a continuación.

1. El dispositivo ASE descarga e inicia la máquina virtual base de Fusion Core.
1. La aplicación Metaswitch Service Management instala y aprovisiona Fusion Core en el clúster de Kubernetes dentro de la máquina virtual base de Fusion Core.

Además de coordinar la instalación de Fusion Core, la aplicación Metaswitch Service Management permite a los representantes de soporte técnico de Metaswitch supervisar el estado del sistema y acceder a los diagnósticos.


## <a name="next-steps"></a>Pasos siguientes
- Para obtener instrucciones detalladas sobre la implementación de Fusion Core en un dispositivo de Azure Stack Edge, [descargue la guía de la solución Private 5G Edge Fusion Core](https://go.microsoft.com/fwlink/?linkid=2165096).
- Más información sobre [Private 5G Edge Fusion Core](metaswitch-fusion-core-overview.md)