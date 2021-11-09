---
title: Conexión de Azure Percept DK mediante redes 5G y LTE con una puerta de enlace
description: En este artículo se explica cómo conectar Azure Percept DK mediante redes 5G y LTE con una puerta de enlace de telefonía móvil.
author: juhaluoto
ms.author: amiyouss
ms.service: azure-percept
ms.topic: how-to
ms.date: 09/23/2021
ms.custom: template-how-to
ms.openlocfilehash: 5b66885afdec8e32b938c735625bd48c9c62535a
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/03/2021
ms.locfileid: "131439995"
---
# <a name="connect-azure-percept-dk-over-5g-and-lte-networks-by-using-a-gateway"></a>Conexión de Azure Percept DK mediante redes 5G y LTE con una puerta de enlace

Una manera sencilla de conectar Azure Percept a Internet es usar una puerta de enlace que conecte a Internet mediante 5G o LTE y proporcione puertos Ethernet. En este caso, Azure Percept ni siquiera es consciente de que se conecta mediante 5G o LTE. Solo "sabe" que su puerto Ethernet tiene conectividad y está enrutando todo el tráfico a través de ese puerto.  


## <a name="overview-of-5g-and-lte-gateway-topology"></a>Información general sobre la topología de puerta de enlace 5G y LTE

En el diagrama siguiente se muestra cómo se puede emparejar fácilmente una puerta de enlace 5G o LTE con Azure Percept DK (kit de desarrollo).

:::image type="Image" source="media/connect-over-cellular/topology-v2.png" alt-text="Diagrama que muestra cómo se conecta Azure Percept DK a una puerta de enlace 5G o LTE a través de Ethernet." lightbox="media/connect-over-cellular/topology-expanded-v2.png":::

## <a name="if-youre-connecting-to-a-5g-or-lte-gateway"></a>Si se va a conectar a una puerta de enlace 5G o LTE

Si va a conectar Azure Percept DK a una puerta de enlace 5G o LTE, tenga en cuenta los siguientes puntos importantes:
- Configure primero la puerta de enlace y luego compruebe que recibe una conexión a través de la SIM. Este orden facilita la solución de cualquier problema que se encuentre al conectar Azure Percept DK.
- Asegúrese de que ambos extremos del cable Ethernet están bien conectados a la puerta de enlace y a Azure Percept DK.
- Siga las [instrucciones predeterminadas](./how-to-connect-over-ethernet.md) para conectar Azure Percept DK a través de Ethernet.
- Si el plan 5G o LTE tiene una cuota, se recomienda optimizar la cantidad de datos que los modelos de Azure Percept DK envían a la nube.
- Asegúrese de que tiene un [firewall configurado correctamente](./concept-security-configuration.md) que bloquea el tráfico entrante de origen externo.

## <a name="if-youre-connecting-to-the-dev-kit-via-ssh-protocol"></a>Si se va a conectar al kit de desarrollo mediante el protocolo SSH

Si usa el protocolo de red Secure Shell (SSH) para conectar con el kit de desarrollo mediante una puerta de enlace Ethernet 5G o LTE, use alguna de las siguientes opciones:
- **Use el punto de acceso Wi-Fi del kit de desarrollo**: si ha deshabilitado la conexión Wi-Fi, puede volver a habilitarla mediante el reinicio del kit de desarrollo. Desde allí, puede conectarse al punto de acceso Wi-Fi del kit de desarrollo y seguir las instrucciones de [Conexión a Azure Percept DK a través de SSH](./how-to-ssh-into-percept-dk.md).
- **Use una conexión Ethernet a una red de área local (LAN)** : con esta opción, desconecta el kit de desarrollo de la puerta de enlace 5G o LTE y lo conecta a un enrutador LAN. Para obtener más información, vea [Conexión a Azure Percept DK a través de Ethernet](./how-to-connect-over-ethernet.md). 
- **Use las características de acceso remoto de la puerta de enlace**: muchas puertas de enlace 5G y LTE incluyen administradores de acceso remoto que se pueden usar para conectarse a dispositivos de la red a través de SSH. Consulte con el fabricante de la puerta de enlace 5G o LTE para ver si tiene esta característica. Para obtener un ejemplo de un administrador de acceso remoto, vea [Puertas de enlace 5G y LTE de Cradlepoint](https://customer.cradlepoint.com/s/article/NCM-Remote-Connect-LAN-Manager).
- **Use el puerto de serie del kit de desarrollo**: Azure Percept DK incluye un puerto de conexión de serie que se puede usar para conectarse directamente al dispositivo. Para obtener más información, vea [Conexión a Azure Percept DK a través de cable serie](./how-to-connect-to-percept-dk-over-serial.md).

## <a name="next-steps"></a>Pasos siguientes
En función del dispositivo de telefonía móvil al que tenga acceso, puede conectarse de una de estas dos maneras:

* [Conexión mediante un módem USB](./connect-over-cellular-usb.md)
* [Conexión mediante 5G o LTE](./connect-over-cellular.md)
