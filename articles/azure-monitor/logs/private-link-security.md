---
title: Uso de Private Link para conectar redes a Azure Monitor
description: Configure un ámbito de Azure Monitor Private Link para conectar redes de forma segura a Azure Monitor.
author: noakup
ms.author: noakuper
ms.topic: conceptual
ms.date: 10/05/2020
ms.openlocfilehash: bdd47962b56324f9832070b13644b5489ee38989
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/13/2021
ms.locfileid: "121727235"
---
# <a name="use-azure-private-link-to-connect-networks-to-azure-monitor"></a>Uso de Private Link para conectar redes a Azure Monitor

Con [Azure Private Link](../../private-link/private-link-overview.md) puede vincular de forma segura los servicios de plataforma como servicio de Azure a una red virtual mediante puntos de conexión privados. En muchos servicios, solo tiene que configurar un punto de conexión para cada recurso. Sin embargo, Azure Monitor es una constelación de diferentes servicios interconectados que funcionan conjuntamente para supervisar las cargas de trabajo. 

## <a name="advantages"></a>Ventajas

Con Private Link puede:

- Conectarse de forma privada a Azure Monitor sin necesidad de abrir ningún acceso a la red pública
- Asegurarse de que solo se accede a los datos de supervisión a través de redes privadas autorizadas
- Impedir la filtración de datos de las redes privadas mediante la definición de recursos de Azure Monitor específicos que se conectan a través del punto de conexión privado
- Conectar de forma segura la red local privada a Azure Monitor mediante ExpressRoute y Private Link
- Mantener todo el tráfico dentro de la red troncal de Microsoft Azure

Para más información, consulte [Ventajas principales de Private Link](../../private-link/private-link-overview.md#key-benefits).

## <a name="how-it-works"></a>Funcionamiento

Ámbito de Private Link de Azure Monitor (AMPLS) conecta los puntos de conexión privados (y las redes virtuales en las que se encuentran) a uno o varios recursos de Azure Monitor, o a áreas de trabajo de Log Analytics y componentes de Application Insights.

![Diagrama de la topología básica de los recursos](./media/private-link-security/private-link-basic-topology.png)

* El punto de conexión privado de la red virtual le permite alcanzar los puntos de conexión de Azure Monitor a través de direcciones IP privadas desde el grupo de la red, en lugar de usar las direcciones IP públicas de estos puntos de conexión. Esto le permite seguir usando sus recursos de Azure Monitor sin necesidad de abrir la red virtual a un tráfico saliente no necesario. 
* El tráfico desde el punto de conexión privado a los recursos de Azure Monitor pasará por la red troncal de Microsoft Azure y no se enrutará hacia las redes públicas. 
* Puede configurar cada uno de los componentes o áreas de trabajo para permitir o denegar la ingesta y las consultas de redes públicas. Esto proporciona una protección en el nivel de recurso para que pueda controlar el tráfico a recursos específicos.

> [!NOTE]
> Un único recurso de Azure Monitor puede pertenecer a varios AMPLS, pero no puede conectar una sola red virtual a más de un AMPLS. 

### <a name="azure-monitor-private-links-and-your-dns-its-all-or-nothing"></a>Vínculos de Azure Monitor Private Link y su DNS: todo o nada
Algunos servicios de Azure Monitor usan puntos de conexión globales, lo que significa que atienden solicitudes dirigidas a cualquier área de trabajo o componente. Al configurar una conexión de Private Link, el DNS se actualiza para asignar puntos de conexión de Azure Monitor a direcciones IP privadas, con el fin de enviar tráfico a través del Private Link. En lo que respecta a los puntos de conexión globales, la configuración de un vínculo de Private Link (incluso en un recurso individual) afecta al tráfico a todos los recursos. En otras palabras, es imposible crear una conexión de Private Link solo para un componente o área de trabajo específicos.

#### <a name="global-endpoints"></a>Puntos de conexión globales
Lo más importante es que el tráfico a los puntos de conexión globales siguientes se enviará a través de Private Link:
* Todos los puntos de conexión de Application Insights: los puntos de conexión que controlan la ingesta, las métricas en directo, el generador de perfiles, el depurador, etc. en los puntos de conexión de Application Insights son globales.
* Punto de conexión de consulta: el punto de conexión que controla las consultas a los recursos de Application Insights y Log Analytics es global.

A efectos prácticos, eso significa que todo el tráfico de Application Insights se enviará al Private Link y que todas las consultas (a los recursos de Application Insights y Log Analytics) se enviarán a Private Link.

El tráfico al recurso de Application Insights no agregado a AMPLS no pasará la validación de Private Link y se producirá un error.

![Diagrama del comportamiento All o Nothing (Todo o nada)](./media/private-link-security/all-or-nothing.png)

#### <a name="resource-specific-endpoints"></a>Puntos de conexión específicos del recurso
Todos los puntos de conexión de Log Analytics, excepto el punto de conexión de consulta, son específicos del área de trabajo. Por lo tanto, la creación de un vínculo de Private Link a un área de trabajo específica de Log Analytics no afectará al tráfico de ingesta (u otro) a otras áreas de trabajo, que seguirán usando los puntos de conexión públicos de Log Analytics. Sin embargo, todas las consultas se enviarán a través de Private Link.

### <a name="azure-monitor-private-link-applies-to-all-networks-that-share-the-same-dns"></a>Azure Monitor Private Link se aplica a todas las redes que comparten el mismo DNS
Algunas redes se componen de varias redes virtuales u otras redes conectadas. Si estas redes comparten el mismo DNS, la configuración de un vínculo de Private Link en cualquiera de ellas actualizaría el DNS y afectaría al tráfico entre todas las redes. Es especialmente importante tener esto en cuenta debido al comportamiento "Todo o nada" descrito anteriormente.

![Diagrama de invalidaciones de DNS en varias redes virtuales](./media/private-link-security/dns-overrides-multiple-vnets.png)

En el diagrama anterior, la red virtual 10.0.1.x se conecta primero a AMPLS1 y asigna los puntos de conexión globales de Azure Monitor a las direcciones IP de su intervalo. Posteriormente, la red virtual 10.0.2.x se conecta a AMPLS2 e invalida la asignación de DNS de los *mismos puntos de conexión globales* con direcciones IP de su intervalo. Puesto que estas redes virtuales no están emparejadas, la primera red virtual ahora no puede alcanzar estos puntos de conexión.


## <a name="next-steps"></a>Pasos siguientes
- [Diseño de la configuración de Private Link](private-link-design.md)
- Aprenda a [configurar Private Link](private-link-configure.md)

<h3><a id="connect-to-a-private-endpoint"></a></h3>
