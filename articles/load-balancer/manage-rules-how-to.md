---
title: 'Administración de reglas para Azure Load Balancer: Azure Portal'
description: En este artículo obtendrá información sobre cómo administrar reglas para Azure Load Balancer mediante Azure Portal.
author: asudbring
ms.author: allensu
ms.service: load-balancer
ms.topic: how-to
ms.date: 08/23/2021
ms.custom: template-how-to
ms.openlocfilehash: e94978fa61c144ced0b405717eefc8ffecc47873
ms.sourcegitcommit: 16e25fb3a5fa8fc054e16f30dc925a7276f2a4cb
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/25/2021
ms.locfileid: "122868751"
---
# <a name="manage-rules-for-azure-load-balancer-using-the-azure-portal"></a>Administración de reglas para Azure Load Balancer mediante Azure Portal

Azure Load Balancer admite reglas para configurar el tráfico que llega al grupo de back-end.  En este artículo obtendrá información sobre cómo administrar las reglas de una instancia de Azure Load Balancer.

Hay cuatro tipos de reglas:

* **Reglas de equilibrio de carga:** regla de equilibrador de carga que se usa para definir cómo se distribuye el tráfico entrante a **todas** las instancias del grupo de back-end. Las reglas de equilibrio de carga asignan una configuración de IP de front-end y un puerto determinados a varios puertos y direcciones IP de back-end. Un ejemplo sería una regla creada en el puerto 80 para equilibrar la carga del tráfico web.

* **Puertos de alta disponibilidad:** regla de equilibrador de carga configurada con **Protocolo: Todos** y **Puerto: 0**. Estas reglas habilitan una única regla para equilibrar la carga de todo el tráfico TCP y UDP que llega a todos los puertos de un equilibrador de carga estándar interno. Las reglas de equilibrio de carga de puertos de alta disponibilidad resultan de utilidad en diversos escenarios, como los de alta disponibilidad y escalabilidad para dispositivos virtuales de red (NVA) en redes virtuales. La característica puede ayudar cuando hay que equilibrar la carga de un gran número de puertos.

* **Regla NAT de entrada:** regla que reenvía el tráfico entrante enviado a la combinación de dirección IP y puerto de front-end. El tráfico se envía a una máquina virtual o instancia **específica** en el grupo de back-end. El reenvío de puertos se realiza mediante la misma distribución basada en hash que el equilibrio de carga.

* **Regla de salida:** regla que configura una traducción de direcciones de red (NAT) de salida para **todas** las máquinas virtuales o instancias identificadas por el grupo de back-end. Esta regla permite que las instancias del back-end se comuniquen (saliente) con Internet u otros puntos de conexión.

## <a name="prerequisites"></a>Requisitos previos

- Una cuenta de Azure con una suscripción activa. [Cree una cuenta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

- Un equilibrador de carga público estándar en su suscripción. Para obtener más información sobre cómo crear una instancia de Azure Load Balancer, consulte [Inicio rápido: Uso de Azure Portal para crear un equilibrador de carga público para equilibrar la carga de máquinas virtuales](quickstart-load-balancer-standard-public-portal.md). El nombre del equilibrador de carga para los ejemplos de este artículo es **myLoadBalancer**.

- Un equilibrador de carga interno estándar en su suscripción. Para obtener más información sobre cómo crear una instancia de Azure Load Balancer, consulte [Inicio rápido: Creación de un equilibrador de carga interno para equilibrar la carga de las máquinas virtuales mediante Azure Portal](quickstart-load-balancer-standard-internal-portal.md). El nombre del equilibrador de carga para los ejemplos de este artículo es **myLoadBalancer**.

## <a name="load-balancing-rules"></a>Reglas de equilibrio de carga

En esta sección obtendrá información sobre cómo agregar y quitar reglas de equilibrio de carga. En los ejemplos se usa un equilibrador de carga público.

### <a name="add-a-load-balancing-rule"></a>Agregar una regla de equilibrio de carga

En este ejemplo va a crear una regla para equilibrar la carga del puerto 80.

1. Inicie sesión en [Azure Portal](https://portal.azure.com).

2. En el cuadro de búsqueda que aparece en la parte superior del portal, escriba **Load Balancer**. Seleccione **Equilibradores de carga** en los resultados de la búsqueda.

3. Seleccione **myLoadBalancer** o su equilibrador de carga.

4. En la página del equilibrador de carga, seleccione **Reglas de equilibrio de carga** en **Configuración**.

5. Seleccione **+ Agregar** en **Reglas de equilibrio de carga** para agregar una regla.

    :::image type="content" source="./media/manage-rules-how-to/load-balancing-rules.png" alt-text="Captura de pantalla de la página de reglas de equilibrio de carga en un equilibrador estándar" border="true":::

6. Escriba o seleccione la siguiente información en **Agregar regla de equilibrio de carga**:

    | Configuración | Value |
    | ------- | ----- |
    | Nombre | **myHTTPRule** |
    | Versión de la dirección IP | Seleccione **IPv4** o **IPv6**. |
    | Dirección IP del front-end | Seleccione la dirección IP de front-end del equilibrador de carga. <br> En este ejemplo, es **myFrontendIP**. |
    | Protocolo | Deje el valor predeterminado de **TCP**. |
    | Port | Escriba **80**. |
    | Puerto back-end | Escriba **80**. |
    | Grupo back-end | Seleccione el grupo de back-end del equilibrador de carga. </br> En este ejemplo, es **myBackendPool**. |
    | Sondeo de mantenimiento | Seleccione **Crear nuevo**. </br> En **Nombre**, escriba **myHealthProbe**. </br> Seleccione **HTTP** en **Protocolo**. </br> Deje el resto de los campos con los valores predeterminados o modifíquelos según sus requisitos. </br> Seleccione **Aceptar**. |
    | Persistencia de la sesión | Seleccione **Ninguno** o la persistencia necesaria. </br> Para obtener más información sobre los modos de distribución, consulte [Modos de distribución de Azure Load Balancer](load-balancer-distribution-mode.md). | 
    | Tiempo de espera de inactividad (minutos) | Deje el valor predeterminado **4** o mueva el control deslizante al tiempo de espera de inactividad necesario. |
    | Restablecimiento de TCP | Seleccione **Habilitado**. </br> Para obtener más información sobre el restablecimiento de TCP, consulte [Tiempo de espera de inactividad y restablecimiento de TCP de Load Balancer](load-balancer-tcp-reset.md). |
    | Dirección IP flotante | Deje el valor predeterminado **Deshabilitado** o habilítelo si la implementación requiere una dirección IP flotante. </br> Para obtener información sobre la dirección IP flotante, consulte [Configuración de la IP flotante de Azure Load Balancer](load-balancer-floating-ip.md). |
    | Traducción de direcciones de red de origen (SNAT) de salida | Deje el valor predeterminado, **(Recommended) Use outbound rules to provide backend pool members access to the internet** ([Recomendado] Usar reglas de salida para que los miembros del grupo de servidores de back-end puedan acceder a Internet). </br> Para obtener más información sobre las reglas salientes y SNAT, consulte [Reglas de salida en Azure Load Balancer](outbound-rules.md) y [Uso de la traducción de direcciones de red de origen (SNAT) para conexiones salientes](load-balancer-outbound-connections.md).|

7. Seleccione **Agregar**.

    :::image type="content" source="./media/manage-rules-how-to/add-load-balancing-rule.png" alt-text="Captura de pantalla de la página &quot;Agregar regla de equilibrador de carga&quot;" border="true":::

### <a name="remove-a-load-balancing-rule"></a>Eliminación de una regla de equilibrio de carga

En este ejemplo va a eliminar una regla de equilibrio de carga.

1. Inicie sesión en [Azure Portal](https://portal.azure.com).

2. En el cuadro de búsqueda que aparece en la parte superior del portal, escriba **Load Balancer**. Seleccione **Equilibradores de carga** en los resultados de la búsqueda.

3. Seleccione **myLoadBalancer** o su equilibrador de carga.

4. En la página del equilibrador de carga, seleccione **Reglas de equilibrio de carga** en **Configuración**.

5. Seleccione los tres puntos situados junto a la regla que quiera eliminar.

6. Seleccione **Eliminar**.

    :::image type="content" source="./media/manage-rules-how-to/remove-load-balancing-rule.png" alt-text="Captura de pantalla de la eliminación de una regla de equilibrio de carga" border="true":::

## <a name="high-availability-ports"></a>Puertos de alta disponibilidad

En esta sección obtendrá información sobre cómo agregar y eliminar reglas de puertos de alta disponibilidad. En este ejemplo va a usar un equilibrador de carga interno. 

Las reglas de puertos de alta disponibilidad son compatibles con un equilibrador de carga interno estándar.

### <a name="add-high-availability-ports-rule"></a>Incorporación de una regla de puertos de alta disponibilidad

En este ejemplo va a crear una regla de puertos de alta disponibilidad.

1. Inicie sesión en [Azure Portal](https://portal.azure.com).

2. En el cuadro de búsqueda que aparece en la parte superior del portal, escriba **Load Balancer**. Seleccione **Equilibradores de carga** en los resultados de la búsqueda.

3. Seleccione **myLoadBalancer** o su equilibrador de carga.

4. En la página del equilibrador de carga, seleccione **Reglas de equilibrio de carga** en **Configuración**.

5. Seleccione **+ Agregar** en **Reglas de equilibrio de carga** para agregar una regla.

    :::image type="content" source="./media/manage-rules-how-to/load-balancing-rules.png" alt-text="Captura de pantalla de la página de reglas de equilibrio de carga en un equilibrador estándar" border="true":::

6. Escriba o seleccione la siguiente información en **Agregar regla de equilibrio de carga**:

    | Configuración | Value |
    | ------- | ----- |
    | Nombre | Escriba **myHARule**. |
    | Versión de la dirección IP | Seleccione **IPv4** o **IPv6**. |
    | Dirección IP del front-end | Seleccione la dirección IP de front-end del equilibrador de carga. <br> En este ejemplo, es **myFrontendIP**. </br> Active la casilla situada junto a **Puertos de alta disponibilidad**. |
    | Grupo back-end | Seleccione el grupo de back-end del equilibrador de carga. </br> En este ejemplo, es **myBackendPool**. |
    | Sondeo de mantenimiento | Seleccione **Crear nuevo**. </br> En **Nombre**, escriba **myHealthProbe**. </br> Seleccione **TCP** en **Protocolo**. </br> Indique un puerto TCP en **Puerto**. En este ejemplo, se trata del puerto **80**. Indique un puerto que cumpla sus requisitos. </br> Deje el resto de los campos con los valores predeterminados o modifíquelos según sus requisitos. </br> Seleccione **Aceptar**. |
    | Persistencia de la sesión | Seleccione **Ninguno** o la persistencia necesaria. </br> Para obtener más información sobre los modos de distribución, consulte [Modos de distribución de Azure Load Balancer](load-balancer-distribution-mode.md). | 
    | Tiempo de espera de inactividad (minutos) | Deje el valor predeterminado **4** o mueva el control deslizante al tiempo de espera de inactividad necesario. |
    | Restablecimiento de TCP | Seleccione **Habilitado**. </br> Para obtener más información sobre el restablecimiento de TCP, consulte [Tiempo de espera de inactividad y restablecimiento de TCP de Load Balancer](load-balancer-tcp-reset.md). |
    | Dirección IP flotante | Deje el valor predeterminado **Deshabilitado** o habilítelo si la implementación requiere una dirección IP flotante. </br> Para obtener información sobre la dirección IP flotante, consulte [Configuración de la IP flotante de Azure Load Balancer](load-balancer-floating-ip.md). |

    Para obtener más información sobre la configuración de reglas de puertos de alta disponibilidad, consulte **[Introducción a los puertos de alta disponibilidad](load-balancer-ha-ports-overview.md)** .

7. Seleccione **Agregar**.

    :::image type="content" source="./media/manage-rules-how-to/add-ha-ports-load-balancing-rule.png" alt-text="Captura de pantalla de la página para agregar una regla de puertos de alta disponibilidad" border="true":::

### <a name="remove-a-high-availability-ports-rule"></a>Eliminación de una regla de puertos de alta disponibilidad

En este ejemplo va a eliminar una regla de equilibrio de carga.

1. Inicie sesión en [Azure Portal](https://portal.azure.com).

2. En el cuadro de búsqueda que aparece en la parte superior del portal, escriba **Load Balancer**. Seleccione **Equilibradores de carga** en los resultados de la búsqueda.

3. Seleccione **myLoadBalancer** o su equilibrador de carga.

4. En la página del equilibrador de carga, seleccione **Reglas de equilibrio de carga** en **Configuración**.

5. Seleccione los tres puntos situados junto a la regla que quiera eliminar.

6. Seleccione **Eliminar**.

    :::image type="content" source="./media/manage-rules-how-to/remove-ha-ports-load-balancing-rule.png" alt-text="Captura de pantalla de la eliminación de una regla de puertos de alta disponibilidad" border="true":::

## <a name="inbound-nat-rule"></a>Regla NAT de entrada

Las reglas NAT de entrada se usan para enrutar conexiones a una máquina virtual específica del grupo de back-end. Para obtener más información y un tutorial detallado sobre cómo configurar y probar reglas NAT de entrada, consulte [Tutorial: Configuración del reenvío de puertos en Azure Load Balancer mediante Azure Portal](tutorial-load-balancer-port-forwarding-portal.md).

## <a name="outbound-rule"></a>Regla de salida

En esta sección obtendrá información sobre cómo agregar y eliminar reglas de salida. En este ejemplo va a usar un equilibrador de carga público. 

Las reglas de salida son compatibles con equilibradores de carga públicos estándar.

### <a name="add-outbound-rule"></a>Incorporación de una regla de salida

En este ejemplo va a crear una regla de salida.

1. Inicie sesión en [Azure Portal](https://portal.azure.com).

2. En el cuadro de búsqueda que aparece en la parte superior del portal, escriba **Load Balancer**. Seleccione **Equilibradores de carga** en los resultados de la búsqueda.

3. Seleccione **myLoadBalancer** o su equilibrador de carga.

4. En la página del equilibrador de carga, seleccione **Reglas de salida** en **Configuración**.

5. Seleccione **+ Agregar** en **Reglas de salida** para agregar una regla.

    :::image type="content" source="./media/manage-rules-how-to/outbound-rules.png" alt-text="Captura de pantalla de la página de reglas de salida en un equilibrador de carga estándar" border="true":::

6. Escriba o seleccione la siguiente información en **Agregar regla de salida**:

    | Configuración | Value |
    | ------- | ----- |
    | Nombre | Escriba **myOutboundRule**. |
    | Dirección IP del front-end | Seleccione la dirección IP de front-end del equilibrador de carga. <br> En este ejemplo, es **myFrontendIP**. | 
    | Protocolo | Deje el valor predeterminado **Todos**. |
    | Tiempo de espera de inactividad (minutos) | Deje el valor predeterminado **4** o mueva el control deslizante de acuerdo con sus requisitos. |
    | Restablecimiento de TCP | Deje el valor predeterminado **Habilitado**. |
    | Grupo back-end | Seleccione el grupo de back-end del equilibrador de carga. </br> En este ejemplo, es **myBackendPool**. |
    | **Asignación de puertos** |   |
    | Asignación de puertos | Seleccione **Elegir manualmente el número de puertos de salida**. |
    | **Puertos de salida** |  |
    | Elegir por | Seleccione **Puertos por instancia**. |
    | Puertos por instancia | Escriba **10000**. |

7. Seleccione **Agregar**.

    :::image type="content" source="./media/manage-rules-how-to/add-outbound-rule.png" alt-text="Captura de pantalla de la página &quot;Agregar regla de salida&quot;" border="true":::

### <a name="remove-an-outbound-rule"></a>Eliminación de una regla de salida

En este ejemplo va a eliminar una regla de salida.

1. Inicie sesión en [Azure Portal](https://portal.azure.com).

2. En el cuadro de búsqueda que aparece en la parte superior del portal, escriba **Load Balancer**. Seleccione **Equilibradores de carga** en los resultados de la búsqueda.

3. Seleccione **myLoadBalancer** o su equilibrador de carga.

4. En la página del equilibrador de carga, seleccione **Reglas de salida** en **Configuración**.

5. Seleccione los tres puntos situados junto a la regla que quiera eliminar.

6. Seleccione **Eliminar**.

    :::image type="content" source="./media/manage-rules-how-to/remove-outbound-rule.png" alt-text="Captura de pantalla de la eliminación de una regla de salida" border="true":::

## <a name="next-steps"></a>Pasos siguientes

En este artículo ha obtenido información sobre cómo administrar reglas de equilibrio de carga para una instancia de Azure Load Balancer.

Para obtener más información sobre Azure Load Balancer, consulte los siguientes artículos:
- [¿Qué es Azure Load Balancer?](load-balancer-overview.md)
- [Preguntas frecuentes sobre Azure Load Balancer](load-balancer-faqs.yml)
