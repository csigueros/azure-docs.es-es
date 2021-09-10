---
title: Administración de una IP pública con un equilibrador de carga
titleSuffix: Azure Virtual Network
description: Obtenga información sobre las distintas formas de usar una IP pública con Azure Load Balancer y cómo cambiar la configuración.
author: asudbring
ms.author: allensu
ms.service: virtual-network
ms.subservice: ip-services
ms.topic: how-to
ms.date: 06/28/2021
ms.custom: template-how-to
ms.openlocfilehash: 04b1b4b9b9dd859a2f4b4515c2896163b8f9a698
ms.sourcegitcommit: 03f0db2e8d91219cf88852c1e500ae86552d8249
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/27/2021
ms.locfileid: "123038484"
---
# <a name="manage-a-public-ip-address-with-a-load-balancer"></a>Administración de una IP pública con un equilibrador de carga

Un equilibrador de carga público es una solución de nivel 4 para distribuir el tráfico TCP y UDP a un grupo de back-end. El equilibro puede usar SKU básicas y estándar. Estas SKU se corresponden con las SKU básicas y estándar de una IP pública.

Una IP pública asociada a un equilibrador de carga actúa como configuración de IP del front-end orientada a Internet. El front-end se usa para acceder a los recursos del grupo de back-end. La IP del front-end se puede usar para que los miembros del grupo de back-end salgan a Internet. 

Una instancia de Azure Load Balancer de SKU básica está limitada en las opciones de disponibilidad y los conjuntos de características. La combinación de dirección IP y una instancia de Azure Load Balancer de SKU estándar es la implementación recomendada para cargas de trabajo de producción. Las direcciones IP de SKU estándar admiten zonas de disponibilidad en las regiones admitidas. 

En este artículo, aprenderá a crear un equilibrador de carga con una dirección IP pública existente en la suscripción. 

Aprenderá a cambiar la IP pública actual asociada a un equilibrador de carga. 

Aprenderá a cambiar la configuración del front-end de un grupo de back-end de salida a un prefijo de IP pública.  

Por último, en el artículo se revisan aspectos únicos del uso de IP públicas y prefijos de IP públicas con un equilibrador de carga. 

> [!NOTE]
> En los ejemplos de este artículo se usan el equilibrador de carga de SKU estándar y la IP pública. En el caso de los equilibradores de carga de SKU básica, los procedimientos son los mismos, excepto la selección de SKU tras la creación del equilibrador de carga y el recurso de IP pública. Los equilibradores de carga básicos no admiten reglas de salida ni prefijos de IP pública. 

## <a name="prerequisites"></a>Requisitos previos

- Una cuenta de Azure con una suscripción activa. [cree una de forma gratuita](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).
- Dos IP públicas de SKU estándar en la suscripción. Las direcciones IP no pueden estar asociadas con ningún recurso. Para más información sobre cómo crear una IP pública de SKU estándar, consulte [Creación de una dirección IP pública: Azure Portal](create-public-ip-portal.md).
    - Para fines de los ejemplos de este artículo, asigne los nombres **myStandardPublicIP-1** y **myStandardPublicIP-2** a las IP públicas nuevas.
- Un prefijo de dirección IP pública en la suscripción. Para más información sobre cómo crear un prefijo de dirección IP pública, consulte la sección sobre [cómo crear un prefijo de dirección IP pública con Azure Portal](create-public-ip-prefix-portal.md).
    - Para fines del ejemplo de este artículo, asigne el nombre **myPublicIPPrefixOutbound** al prefijo de dirección IP pública nuevo.

## <a name="create-load-balancer-existing-public-ip"></a>Creación de una IP pública existente de un equilibrador de carga

En esta sección, creará un equilibrador estándar de SKU estándar. Seleccionará la dirección IP que creó en los requisitos previos como IP del front-end del equilibrador de carga.

1. Inicie sesión en [Azure Portal](https://portal.azure.com).

2. En el cuadro de búsqueda que aparece en la parte superior del portal, escriba **Load Balancer**.

3. En los resultados de la búsqueda, seleccione **Equilibradores de carga**.

4. Seleccione **+ Create** (+ Crear).

5. En **Crear equilibrador de carga**, especifique o seleccione la información siguiente.

    | Configuración | Value |
    | ------- | ----- |
    | **Detalles del proyecto** |   |
    | Subscription | Seleccione su suscripción. |
    | Resource group | Seleccione **Crear nuevo**. </br> Escriba **myResourceGroupIP**. </br> Seleccione **Aceptar**. |
    | **Detalles de instancia** |   |
    | Nombre | Escriba **myLoadBalancer**. |
    | Region | Seleccione **(EE. UU.) Oeste de EE. UU. 2**. |
    | Tipo | Deje el valor predeterminado, **Públicas**. |
    | SKU | Deje el valor predeterminado **Estándar**. |
    | Nivel | Deje el valor predeterminado, **Regional**. |
    | **Dirección IP pública** |   |
    | Dirección IP pública | Seleccione **Usar existente**. |
    | Elección de una IP pública | Seleccione **myStandardPublicIP-1**. |
    | Adición de una dirección IPv6 pública | Deje el valor predeterminado de **No**. |

6. Seleccione la pestaña **Revisar y crear** o seleccione el botón **Revisar y crear**. 

7. Seleccione **Crear**.

> [!NOTE]
> Esta es una implementación sencilla de un equilibrador de carga. Para obtener información sobre la configuración avanzada, consulte [Inicio rápido: Uso de Azure Portal para crear un equilibrador de carga público para equilibrar la carga de máquinas virtuales](../load-balancer/quickstart-load-balancer-standard-public-portal.md).
>
> Para más información sobre Azure Load Balancer, consulte [¿Qué es Azure Load Balancer?](../load-balancer/load-balancer-overview.md)

## <a name="change-or-remove-public-ip-address"></a>Cambio o eliminación de IP pública

En esta sección, iniciaremos sesión en Azure Portal y cambiaremos la dirección IP de el equilibrador de carga. 

Una instancia de Azure Load Balancer debe tener una dirección IP asociada a un front-end. Las IP públicas independientes se pueden como front-end para el tráfico de entrada y de salida. 

Para cambiar la IP, asociará una nueva IP pública creada anteriormente con el front-end del equilibrador de carga.

1. Inicie sesión en [Azure Portal](https://portal.azure.com).

2. En el cuadro de búsqueda que aparece en la parte superior del portal, escriba **Load Balancer**.

3. En los resultados de la búsqueda, seleccione **Equilibradores de carga**.

4. En **Equilibradores de carga**, seleccione **myLoadBalancer** o el equilibrador de carga que desea cambiar.

5. En la configuración de **myLoadBalancer**, seleccione **Configuración de IP de front-end**.

6. En **Configuración de IP de front-end**, seleccione **LoadBalancerFrontend** o el front-end del equilibrador de carga.

7. En la configuración del front-end del equilibrador de carga, seleccione **myStandardPublicIP-2** en **Dirección IP pública**.

8. Seleccione **Guardar**.

9. Compruebe que el front-end del equilibrador de carga muestra la nueva dirección IP denominada **myStandardPublicIP-2**.

    > [!NOTE]
    > Estos procedimientos son válidos para los equilibradores de carga entre regiones. Para más información sobre el equilibrador de carga entre regiones, consulte **[Equilibrador de carga entre regiones](../load-balancer/cross-region-overview.md)** .


## <a name="add-public-ip-prefix"></a>Adición del prefijo de dirección IP pública

Un equilibrador de carga estándar admite reglas de salida para la traducción de direcciones de red de origen (SNAT). SNAT permite la salida a Internet de los miembros del grupo de back-end. Los prefijos de la IP pública amplían la extensibilidad de SNAT, ya que permiten varias direcciones IP para las conexiones salientes. 

Varias direcciones IP evitan el agotamiento del puerto SNAT. Cada IP de front-end proporciona 64 000 puertos efímeros que el equilibrador de carga puede usar. Para más información, consulte [Reglas de salida](../load-balancer/outbound-rules.md).

En esta sección, cambiará la configuración del front-end que se utiliza para que las conexiones salientes usen un prefijo de IP pública.

> [!IMPORTANT]
> Para completar esta sección, debe tener implementado un equilibrador de carga con una configuración del front-end de salida y reglas de salida.

1. Inicie sesión en [Azure Portal](https://portal.azure.com).

2. En el cuadro de búsqueda que aparece en la parte superior del portal, escriba **Load Balancer**.

3. En los resultados de la búsqueda, seleccione **Equilibradores de carga**.

4. En **Equilibradores de carga**, seleccione **myLoadBalancer** o el equilibrador de carga que desea cambiar.

5. En la configuración de **myLoadBalancer**, seleccione **Configuración de IP de front-end**.

6. En **Configuración de IP de front-end**, seleccione **LoadBalancerFrontendOutbound** o el front-end de salida del equilibrador de carga.

7. En **Tipo de IP**, seleccione **Prefijo de dirección IP pública**.

8. En **Prefijo de dirección IP pública**, seleccione el prefijo de IP pública que creó anteriormente, **myPublicIPPrefixOutbound**.

9. Seleccione **Guardar**.

10. En **Configuración de IP de front-end**, confirme que el prefijo de IP se agregó a la configuración de front-end de salida.

## <a name="more-information"></a>Más información

* Los equilibradores de carga entre regiones son un tipo especial de equilibrador de carga público estándar que puede abarcar varias regiones. El front-end de un equilibrador de carga entre regiones solo se puede usar con la opción de nivel global de las IP públicas de SKU estándar. El tráfico enviado a la dirección IP del front-end de un equilibrador de carga entre regiones se distribuye entre los equilibradores de carga públicos regionales. Las direcciones IP del front-end regional se encuentran en el grupo de back-end del equilibrador de carga entre regiones. Para más información, consulte [Equilibrador de carga entre regiones](../load-balancer/cross-region-overview.md).

* De forma predeterminada, los equilibradores de carga público no permitirán usar varias reglas de equilibrio de carga con el mismo puerto de back-end. Si se requiere una configuración de varias reglas en el mismo puerto de back-end, habilite la opción de IP flotante para una regla de equilibrio de carga. Esta configuración sobrescribe la dirección IP de destino del tráfico enviado al grupo de back-end. Sin la IP flotante habilitada, el destino será la IP privada del grupo de back-end. Con la IP flotante habilitada, la IP de destino será la IP pública del front-end del equilibrador de carga. La instancia de back-end debe tener esta IP pública configurada en su configuración de red para recibir correctamente este tráfico. Una interfaz de bucle invertido con la dirección IP del front-end configurada en la instancia. Para más información, consulte [Configuración de la IP flotante de Azure Load Balancer](../load-balancer/load-balancer-floating-ip.md)

* Con una configuración del equilibrador de carga, a los miembros del grupo de back-end a menudo también se les pueden asignar IP públicas de nivel de instancia. Si se usa esta arquitectura, el envío de tráfico directamente a estas IP omite el equilibrador de carga. 

* Tanto los equilibradores de carga públicos estándar como las IP públicas pueden tener asignado un valor de tiempo de espera de TCP durante el tiempo que la conexión permanece abierta antes de escuchar conexiones persistentes. Si se asigna una IP pública como front-end del equilibrador de carga, el valor de tiempo de espera de la IP tiene prioridad. Tenga en cuenta que este valor solo se aplica a las conexiones entrantes al equilibrador de carga. Para más información, consulte [Tiempo de espera de inactividad y restablecimiento de TCP de Load Balancer](../load-balancer/load-balancer-tcp-reset.md).

## <a name="caveats"></a>Advertencias

* Los equilibradores de carga públicos estándar pueden usar direcciones IPv6 estáticas de SKU estándar como IP públicas del front-end o prefijos de IP pública.  Cada implementación debe tener dos pilas con los front-ends IPv4 e IPv6. La traducción NAT64 no está disponible. Para más información, consulte [Implementación de una aplicación de pila doble IPv6 en Azure: PowerShell](./virtual-network-ipv4-ipv6-dual-stack-standard-load-balancer-powershell.md) (tenga en cuenta que los equilibradores de carga públicos básicos pueden usar direcciones IPv6 dinámicas de SKU básicas como direcciones IP públicas de front-end).

* Cuando se asignan varios front-ends a un equilibrador de carga público, no hay un método para asignar flujos de instancias de back-end concretas a la salida en una IP específica.  Para más información, consulte [Varios front-ends para Azure Load Balancer](../load-balancer/load-balancer-multivip-overview.md).
## <a name="next-steps"></a>Pasos siguientes

En este artículo, ha aprendido a crear un equilibrador de carga y a usar una IP pública existente. 

Ha reemplazado la dirección IP en una configuración de front-end del equilibrador de carga. 

Por último, ha cambiado la configuración del front-end de salida para usar un prefijo de IP pública.

- Para más información sobre Azure Load Balancer, consulte [¿Qué e Azure Load Balancer?](../load-balancer/load-balancer-overview.md)
- Para más información sobre las IP públicas en Azure, consulte [Direcciones IP públicas](public-ip-addresses.md).
