---
title: Tiempo de espera de inactividad y restablecimiento de TCP de Load Balancer en Azure
titleSuffix: Azure Load Balancer
description: En este artículo, obtendrá información sobre Azure Load Balancer con paquetes de TCP RST bidireccionales en tiempo de espera de inactividad.
services: load-balancer
documentationcenter: na
author: asudbring
ms.custom: seodec18
ms.service: load-balancer
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/07/2020
ms.author: allensu
ms.openlocfilehash: 9d18f5e674a5d30049b93a4273df1ecc8a37595c
ms.sourcegitcommit: 9f1a35d4b90d159235015200607917913afe2d1b
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/21/2021
ms.locfileid: "122634870"
---
# <a name="load-balancer-tcp-reset-and-idle-timeout"></a>Tiempo de espera de inactividad y restablecimiento de TCP de Load Balancer

Puede usar [Standard Load Balancer](./load-balancer-overview.md) para crear un comportamiento de aplicación más predecible para los escenarios si habilita el restablecimiento de TCP inactivo para una regla determinada. El comportamiento predeterminado de Load Balancer es descartar silenciosamente los flujos cuando se alcanza el tiempo de inactividad de un flujo.  Si habilita esta característica, Load Balancer enviará restablecimientos de TCP bidireccionales (paquete TCP RST) cuando se agote el tiempo de espera de inactividad.  Esto le informará a los puntos de conexión de la aplicación que el tiempo de espera se agotó y ya no se puede usar.  De ser necesario, los puntos de conexión pueden establecer una conexión nueva inmediatamente.

![Restablecimiento de TCP en Load Balancer](media/load-balancer-tcp-reset/load-balancer-tcp-reset.png)
 
## <a name="tcp-reset"></a>Restablecimiento de TCP

Puede cambiar este comportamiento predeterminado y habilitar el envío de restablecimientos de TCP al agotarse el tiempo de espera de inactividad en las reglas NAT de entrada, en las reglas de equilibrio de carga y en las [reglas de salida](./load-balancer-outbound-connections.md#outboundrules).  Cuando se habilita en cada regla, Load Balancer envía restablecimientos TCP bidireccionales (paquetes RST de TCP) tanto a los puntos de conexión del cliente como a los del servidor en el momento en que se agota el tiempo de espera de inactividad para todos los flujos que correspondan.

Los puntos de conexión que reciben los paquetes RST de TCP cierran inmediatamente el socket correspondiente. De este modo, se proporciona una notificación inmediata a los puntos de conexión para indicar que se ha liberado la conexión y cualquier comunicación posterior en la misma conexión TCP generará un error.  Las aplicaciones pueden purgar las conexiones cuando el socket se cierra y restablecer las conexiones según sea necesario sin tener que esperar que la conexión TCP, finalmente, agote el tiempo de espera.

En muchos escenarios, esto puede evitar que haya que enviar paquetes keepalive de TCP (o en la capa de la aplicación) para actualizar el tiempo de inactividad de un flujo. 

Si la duración de la inactividad supera la permitida por la configuración o la aplicación muestra un comportamiento no deseado con los restablecimientos de TCP habilitados, es posible que siga teniendo que usar paquetes keepalive de TCP (o de la capa de la aplicación) para supervisar el estado de las conexiones TCP.  Además, los paquetes keepalive también pueden seguir siendo útiles cuando la conexión atraviesa un proxy en algún lugar de la ruta de acceso, especialmente en el caso de los paquetes keepalive de la capa de la aplicación.  

Examine con cuidado todo el escenario completo para decidir si le beneficia habilitar los restablecimientos de TCP y ajustar el tiempo de espera de inactividad, y si es posible que se requieran pasos adicionales para garantizar el comportamiento deseado de la aplicación.

## <a name="configurable-tcp-idle-timeout"></a>Tiempo de espera de inactividad de TCP configurable

Azure Load Balancer tiene el siguiente intervalo de tiempo de espera de inactividad:
-  De 4 a 100 minutos para las reglas de salida.
-  De 4 a 30 minutos para reglas de Load Balancer y las reglas NAT de entrada.

De forma predeterminada, se establece en 4 minutos. Si un período de inactividad es mayor que el valor de tiempo de espera, no hay ninguna garantía de que todavía exista la sesión TCP o HTTP entre el cliente y el servicio en la nube.

Cuando se cierra la conexión, la aplicación cliente puede recibir el mensaje de error siguiente: "The underlying connection was closed: A connection that was expected to be kept alive was closed by the server" (Se ha terminado la conexión: El servidor cerró una conexión que se esperaba estuviera activa).

Una práctica común es usar TCP Keep-alive. Esta práctica mantiene la conexión activa durante un periodo más largo. Para obtener más información, consulte estos [ejemplos de .NET](/dotnet/api/system.net.servicepoint.settcpkeepalive). Con Keep-alive habilitado, los paquetes se envían durante los periodos de inactividad en la conexión. Los paquetes de Keep-alive garantizan que no se alcance el valor de tiempo de espera de inactividad y la conexión se mantenga durante un largo período.

La configuración funciona solo para conexiones entrantes. Para evitar la pérdida de la conexión, configure el TCP keep-alive con un intervalo menor que el valor de tiempo de espera de inactividad o aumentar el valor de tiempo de espera de inactividad. Para admitir estos escenarios, se agregó compatibilidad con un tiempo de espera de inactividad configurable.

TCP keep-alive funciona en escenarios donde la batería no supone una restricción. No se recomienda para aplicaciones móviles. El uso de TCP Keep-alive desde una aplicación móvil puede agotarla batería del dispositivo más rápidamente.


## <a name="limitations"></a>Limitaciones

- El restablecimiento de TCP solo se envía durante la conexión TCP en el estado ESTABLECIDO.
- El tiempo de espera de inactividad de TCP no afecta a las reglas de equilibrio de carga en el protocolo UDP.
- El restablecimiento de TCP no es compatible con puertos de alta disponibilidad de ILB cuando hay una aplicación virtual de red en la ruta de acceso. Una solución alternativa podría ser usar una regla de salida con el restablecimiento de TCP desde la aplicación.

## <a name="next-steps"></a>Pasos siguientes

- Más información sobre [Standard Load Balancer](./load-balancer-overview.md).
- Más información sobre [reglas de salida](./load-balancer-outbound-connections.md#outboundrules).
- [Configurar TCP RST en tiempo de espera de inactividad](load-balancer-tcp-idle-timeout.md)
