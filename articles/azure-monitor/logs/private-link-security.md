---
title: Uso de Private Link para conectar redes a Azure Monitor
description: Configure un ámbito de Azure Monitor Private Link para conectar redes de forma segura a Azure Monitor.
author: noakup
ms.author: noakuper
ms.topic: conceptual
ms.date: 10/05/2020
ms.openlocfilehash: 91230df3223f1227e2126ad48beaba781a3c28cc
ms.sourcegitcommit: f53f0b98031cd936b2cd509e2322b9ee1acba5d6
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/30/2021
ms.locfileid: "123214963"
---
# <a name="use-azure-private-link-to-connect-networks-to-azure-monitor"></a>Uso de Private Link para conectar redes a Azure Monitor

Con [Azure Private Link](../../private-link/private-link-overview.md) puede vincular de forma segura los recursos de plataforma como servicio (PaaS) de Azure a una red virtual mediante puntos de conexión privados. Azure Monitor es una constelación de diferentes servicios interconectados que funcionan conjuntamente para supervisar las cargas de trabajo. Private Link de Azure Monitor conecta un punto de conexión privado a un conjunto de recursos de Azure Monitor, lo que define los límites de la red de supervisión. A esto se denomina ámbito de Private Link de Azure Monitor (AMPLS).


## <a name="advantages"></a>Ventajas

Con Private Link puede:

- Conectarse de forma privada a Azure Monitor sin necesidad de abrir ningún acceso a la red pública
- Asegurarse de que solo se accede a los datos de supervisión a través de redes privadas autorizadas
- Impedir la filtración de datos de las redes privadas mediante la definición de recursos de Azure Monitor específicos que se conectan a través del punto de conexión privado
- Conectar de forma segura la red local privada a Azure Monitor mediante ExpressRoute y Private Link
- Mantener todo el tráfico dentro de la red troncal de Microsoft Azure

Para más información, consulte [Ventajas principales de Private Link](../../private-link/private-link-overview.md#key-benefits).

## <a name="how-it-works"></a>Funcionamiento

### <a name="overview"></a>Información general
Un ámbito de Private Link de Azure Monitor conecta los puntos de conexión privados (y las redes virtuales en las que se encuentran) a uno o varios recursos de Azure Monitor, o a áreas de trabajo de Log Analytics y componentes de Application Insights.

![Diagrama de la topología básica de los recursos](./media/private-link-security/private-link-basic-topology.png)

* El punto de conexión privado de la red virtual le permite alcanzar los puntos de conexión de Azure Monitor a través de direcciones IP privadas desde el grupo de la red, en lugar de usar las direcciones IP públicas de estos puntos de conexión. Esto le permite seguir usando sus recursos de Azure Monitor sin necesidad de abrir la red virtual a un tráfico saliente no necesario. 
* El tráfico desde el punto de conexión privado a los recursos de Azure Monitor pasará por la red troncal de Microsoft Azure y no se enrutará hacia las redes públicas.
* Puede configurar el ámbito de Private Link de Azure Monitor (o redes específicas) para usar el modo de acceso preferido, ya sea permitir el tráfico solo a los recursos de Private Link, o permitir el tráfico tanto a los recursos de Private Link como a los recursos que no son de Private Link (recursos fuera de AMPLS).
* Puede configurar cada uno de los componentes o áreas de trabajo para permitir o denegar la ingesta y las consultas de redes públicas. Esto proporciona una protección en el nivel de recurso para que pueda controlar el tráfico a recursos específicos.

> [!NOTE]
> Una red virtual solo puede conectarse a un único AMPLS, que incluye hasta 50 recursos a los que se puede acceder a través de una instancia de Private Link.

### <a name="azure-monitor-private-link-relies-on-your-dns"></a>Private Link de Azure Monitor se basa en el DNS
Al configurar una conexión de Private Link, las zonas DNS se configuran para asignar puntos de conexión de Azure Monitor a direcciones IP privadas, con el fin de enviar tráfico a través de Private Link. Azure Monitor usa tanto puntos de conexión específicos del recurso como puntos de conexión regionales o globales que controlan el tráfico a varias áreas de trabajo o componentes. En lo que respecta a los puntos de conexión regionales y globales, la configuración de una instancia de Private Link (incluso para un único recurso) afecta a la asignación de DNS que controla el tráfico a **todos** los recursos. En otras palabras, el tráfico a todas las áreas de trabajo o componentes podría verse afectado por la configuración de una única instancia de Private Link.

#### <a name="global-endpoints"></a>Puntos de conexión globales
Lo más importante es que el tráfico a los puntos de conexión globales siguientes se enviará a través de Private Link:
* Todos los puntos de conexión de Application Insights: los puntos de conexión que controlan la ingesta, las métricas en directo, el generador de perfiles, el depurador, etc. en los puntos de conexión de Application Insights son globales.
* Punto de conexión de consulta: el punto de conexión que controla las consultas a los recursos de Application Insights y Log Analytics es global.

A efectos prácticos, eso significa que todo el tráfico de Application Insights se enviará al Private Link y que todas las consultas (a los recursos de Application Insights y Log Analytics) se enviarán a Private Link.

El tráfico al recurso de Application Insights no agregado a AMPLS no pasará la validación de Private Link y se producirá un error.

#### <a name="resource-specific-endpoints"></a>Puntos de conexión específicos del recurso
Todos los puntos de conexión de Log Analytics, excepto el punto de conexión de consulta, son específicos del área de trabajo. Por lo tanto, la creación de una instancia de Private Link a un área de trabajo específica de Log Analytics no afectará la ingesta a otras áreas de trabajo, que seguirán usando los puntos de conexión públicos.


> [!NOTE]
> Cree un solo AMPLS para todas las redes que comparten el mismo DNS. Al crear varios recursos de AMPLS, los puntos de conexión de DNS de Azure Monitor se invalidarán entre sí e interrumpirán los entornos existentes.

### <a name="private-link-access-modes-private-only-vs-open"></a>Modos de acceso de Private Link: solo privado frente a abierto
Como se describe en [Private Link de Azure Monitor se basa en el DNS](#azure-monitor-private-link-relies-on-your-dns), solo se debe crear un único recurso AMPLS para todas las redes que comparten el mismo DNS. Como consecuencia, las organizaciones que usan un único DNS global o regional de hecho tienen una única instancia de Private Link para administrar el tráfico a todos los recursos de Azure Monitor, a través de todas las redes globales o regionales.

En el caso de las instancias de Private Link creadas antes de septiembre de 2021, esto significa: 
* La ingesta de registros solo funciona para los recursos de AMPLS. Se rechaza la ingesta a todos los demás recursos (en todas las redes que comparten el mismo DNS), independientemente de la suscripción o el inquilino.
* Las consultas tienen un comportamiento más abierto, lo que permite que las solicitudes de consulta se comuniquen incluso con recursos que no están en AMPLS. La intención aquí era evitar interrumpir las consultas de clientes a los recursos que no están en AMPLS y permitir que las consultas centradas en recursos devuelvan el conjunto de resultados completo.

Sin embargo, este comportamiento ha demostrado ser demasiado restrictivo para algunos clientes (ya que interrumpe la ingesta de recursos que no están en AMPLS), demasiado permisivo para otros (ya que permite consultar recursos que no están en AMPLS) y, en general, confuso.

Por lo tanto, las instancias de Private Link creadas a partir de septiembre de 2021 tienen una nueva configuración de AMPLS obligatoria que establece explícitamente de qué manera Private Link debe afectar al tráfico de red. Al crear un nuevo recurso de AMPLS, ahora es necesario seleccionar los modos de acceso deseados, para la ingesta y las consultas por separado. 
* Modo solo privado: permite el tráfico solo a recursos de Private Link.
* Modo abierto: usa Private Link para comunicarse con los recursos de AMPLS, pero también permite que el tráfico continúe a otros recursos. Consulte [Control de cómo se aplica Private Link a las redes](./private-link-design.md#control-how-private-links-apply-to-your-networks) para más información.

## <a name="next-steps"></a>Pasos siguientes
- [Diseño de la configuración de Private Link](private-link-design.md)
- Aprenda a [configurar Private Link](private-link-configure.md)

<h3><a id="connect-to-a-private-endpoint"></a></h3>
