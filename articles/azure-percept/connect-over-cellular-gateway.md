---
title: Conexión de Azure Percept a través de redes 5G o LTE mediante una puerta de enlace
description: En este artículo se explica cómo conectar Azure Percept DK a través de redes 5G o LTE mediante una puerta de enlace de red de telefonía móvil.
author: juhaluoto
ms.author: amiyouss
ms.service: azure-percept
ms.topic: how-to
ms.date: 09/23/2021
ms.custom: template-how-to
ms.openlocfilehash: 00596a23e086f801b152e1a3129ecaf7ef44a0df
ms.sourcegitcommit: 611b35ce0f667913105ab82b23aab05a67e89fb7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/14/2021
ms.locfileid: "130007131"
---
# <a name="connect-azure-percept-over-5g-or-lte-networks-using-a-gateway"></a>Conexión de Azure Percept a través de redes 5G o LTE mediante una puerta de enlace
Una puerta de enlace que se conecta a Internet a través de 5G o LTE y proporciona puertos Ethernet es una manera sencilla de conectar Azure Percept a Internet. En este caso, Azure Percept ni siquiera es consciente de que está conectado a través de 5G o LTE, todo lo que sabe que su puerto Ethernet tiene conectividad y enruta todo el tráfico a través de eso.  


## <a name="5glte-gateway-topology-overview"></a>Información general sobre la topología de puerta de enlace 5G o LTE
A continuación, puede ver cómo se puede emparejar fácilmente una puerta de enlace 5G o LTE con Azure Percept DK.

:::image type="Image" source="media/connect-over-cellular/topology.png" alt-text="En este diagrama se muestra cómo se conecta Azure Percept DK a una puerta de enlace 5G o LTE a través de Ethernet." lightbox="media/connect-over-cellular/topology-expanded.png":::

## <a name="considerations-when-connecting-to-a-5g-or-lte-gateway"></a>Consideraciones al establecer conexión con una puerta de enlace 5G o LTE
Estos son algunos detalles importantes que se deben tener en cuenta al conectar Azure Percept DK a una puerta de enlace 5G o LTE.
- Configure primero la puerta de enlace y, a continuación, valide que recibe una conexión a través de la SIM. Después, será más fácil solucionar cualquier incidencia que aparezca al conectar Azure Percept DK.
- Asegúrese de que ambos extremos del cable Ethernet están conectados correctamente a la puerta de enlace y a Azure Percept DK.
- Siga las [instrucciones predeterminadas](./how-to-connect-over-ethernet.md) para conectar Azure Percept DK a través de Ethernet.
- Si el plan 5G o LTE tiene una cuota, se recomienda optimizar la cantidad de datos que los modelos de Azure Percept DK envían a la nube.
- Asegúrese de que tiene un [firewall configurado correctamente](./concept-security-configuration.md) que bloquea el tráfico entrante de origen externo.

## <a name="considerations-when-doing-ssh-to-the-devkit"></a>Consideraciones al realizar SSH en el devkit
Para establecer una conexión SSH con el kit de desarrollo a través de una puerta de enlace Ethernet 5G o LTE, tiene las siguientes opciones:
- **Uso del punto de acceso Wi-Fi del kit de desarrollo**. Si ha deshabilitado la conexión Wi-Fi, puede volver a habilitarla si reinicia el kit de desarrollo. Desde allí, puede conectarse al punto de acceso Wi-Fi del kit de desarrollo y seguir los procedimientos sobre cómo [establecer una conexión SSH en Azure Percept DK](./how-to-ssh-into-percept-dk.md).
- **Uso de una conexión Ethernet a una red local (LAN)** . Con esta opción, desconectará el kit de desarrollo de la puerta de enlace 5G o LTE y lo conectará al enrutador LAN. Para más información, consulte [Conexión a través de Ethernet](./how-to-connect-over-ethernet.md). 
- **Uso de las características de acceso remoto de la puerta de enlace**. Muchas puertas de enlace 5G o LTE incluyen administradores de acceso remoto que se pueden usar para conectarse a dispositivos de la red a través de SSH. Consulte al fabricante de su puerta de enlace 5G o LTE para saber si tiene esta característica. A continuación se muestra un ejemplo de administrador de acceso remoto para [puertas de enlace 5G o LTE de Cradlepoint](https://customer.cradlepoint.com/s/article/NCM-Remote-Connect-LAN-Manager).
- **Uso del puerto serie del kit de desarrollo**. Azure Percept DK incluye un puerto de conexión de serie que se puede usar para conectarse directamente al dispositivo. Consulte [Conexión de Azure Percept DK a través de conexión de serie](./how-to-connect-to-percept-dk-over-serial.md) para obtener instrucciones detalladas.

## <a name="next-steps"></a>Pasos siguientes
En función del dispositivo móvil al que pueda tener acceso, es posible que quiera considerar la posibilidad de conectarse a través de un modo USB:

[Conexión mediante módem USB](./connect-over-cellular-usb.md).

Vuelva al artículo principal sobre 5G o LTE:

[Conexión mediante 5G o LTE](./connect-over-cellular.md).