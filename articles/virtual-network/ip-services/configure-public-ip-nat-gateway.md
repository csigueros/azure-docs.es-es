---
title: Administración de una IP pública con una puerta de enlace NAT
titleSuffix: Azure Virtual Network
description: Obtenga información sobre las formas de usar una IP pública con una puerta de enlace de Azure Virtual Network NAT y cómo cambiar la configuración.
author: asudbring
ms.author: allensu
ms.service: virtual-network
ms.subservice: ip-services
ms.topic: how-to
ms.date: 06/28/2021
ms.custom: template-how-to
ms.openlocfilehash: 2fd41f3459469474c100657747c4873ee99dc057
ms.sourcegitcommit: 87de14fe9fdee75ea64f30ebb516cf7edad0cf87
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/01/2021
ms.locfileid: "129367859"
---
# <a name="manage-a-public-ip-address-with-a-nat-gateway"></a>Administración de una IP pública con una puerta de enlace NAT

Los recursos de Azure NAT Gateway permiten conexiones de salida a Internet desde subredes de una red virtual. Los recursos implementados en la subred de red virtual de la puerta de enlace NAT deben ser la SKU estándar. No se admite la implementación de una puerta de enlace NAT en una subred de red virtual con recursos de SKU básicos. 

La puerta de enlace NAT permite las conexiones de traducción de direcciones de red de origen (SNAT) desde recursos con la puerta de enlace NAT. La puerta de enlace NAT admite IP públicas y prefijos de direcciones IP públicas de SKU. Se admite cualquier combinación, aunque el número de direcciones IP dadas no puede superar las 16. Al agregar direcciones IP o un prefijo IP, se escalan las conexiones SNAT desde los recursos mediante la puerta de enlace NAT. 

En este artículo, aprenderá a crear una puerta de enlace NAT con una IP pública existente en su suscripción. 

Aprenderá a cambiar la IP pública actual asociada a una puerta de enlace NAT. 

Por último, cambiará la configuración de IP de una IP pública a un prefijo IP.

## <a name="prerequisites"></a>Requisitos previos

- Una cuenta de Azure con una suscripción activa. [cree una de forma gratuita](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).
- Dos IP públicas de SKU estándar en la suscripción. Las direcciones IP no pueden estar asociadas con ningún recurso. Para más información sobre cómo crear una IP pública de SKU estándar, consulte [Creación de una dirección IP pública: Azure Portal](../../virtual-network/create-public-ip-portal.md).
    - Para fines de los ejemplos de este artículo, asigne los nombres **myStandardPublicIP-1** y **myStandardPublicIP-2** a las IP públicas nuevas.
- Un prefijo de dirección IP pública en la suscripción. Para más información sobre cómo crear un prefijo de dirección IP pública, consulte la sección sobre [cómo crear un prefijo de dirección IP pública con Azure Portal](../../virtual-network/create-public-ip-prefix-portal.md).
    - Para fines del ejemplo de este artículo, asigne el nombre **myPublicIPPrefixNAT** al prefijo de dirección IP pública nuevo.

## <a name="create-nat-gateway-existing-public-ip"></a>Creación de la IP pública existente de una puerta de enlace NAT

En esta sección, creará un recurso de puerta de enlace NAT. Seleccionará la dirección IP que creó en los requisitos previos como la IP pública para la puerta de enlace NAT.

1. Inicie sesión en [Azure Portal](https://portal.azure.com).

2. En el cuadro de búsqueda que aparece en la parte superior del portal, escriba **Puerta de enlace NAT**.

3. En los resultados de la búsqueda, seleccione **Puertas de enlace NAT**.

4. Seleccione **+ Create** (+ Crear).

5. En **Crear puerta de enlace de traducción de direcciones de red (NAT)** , escriba o seleccione la información siguiente.

    | Configuración | Value |
    | ------- | ----- |
    | **Detalles del proyecto** |   |
    | Subscription | Seleccione su suscripción. |
    | Resource group | Seleccione **Crear nuevo**. </br> Especifique **myResourceGroupNAT**. </br> Seleccione **Aceptar**. |
    | **Detalles de instancia** |   |
    | Nombre | Escriba **myNATgateway**. |
    | Region | Seleccione **(EE. UU.) Oeste de EE. UU. 2**. |
    | Zona de disponibilidad | Deje el valor predeterminado de **No**. |
    | Tiempo de espera inactividad | Deje el valor predeterminado **4**. |

6. Seleccione la pestaña **IP de salida**, o bien elija **Siguiente: IP de salida**.

7. Seleccione **myStandardPublicIP-1** para **IP pública** en la pestaña **IP de salida**.

6. Seleccione la pestaña **Revisar y crear** o seleccione el botón **Revisar y crear**. 

7. Seleccione **Crear**.

> [!NOTE]
> Esta es una implementación sencilla de una puerta de enlace NAT. Para información sobre la configuración avanzada, consulte [Tutorial: Creación de una puerta de enlace NAT mediante Azure Portal](../nat-gateway/tutorial-create-nat-gateway-portal.md).
>
> Para más información sobre Azure Virtual Network NAT, consulte [¿Qué es Azure Virtual Network NAT?](../nat-gateway/nat-overview.md)

## <a name="change-or-remove-public-ip-address"></a>Cambio o eliminación de IP pública

En esta sección, iniciaremos sesión en Azure Portal y cambiaremos la dirección IP de la puerta de enlace NAT. 

Para cambiar la dirección IP, asociará una IP pública nueva que creó anteriormente con la puerta de enlace NAT. Una puerta de enlace NAT debe tener al menos una dirección IP asignada.

1. Inicie sesión en [Azure Portal](https://portal.azure.com).

2. En el cuadro de búsqueda que aparece en la parte superior del portal, escriba **Puerta de enlace NAT**.

3. En los resultados de la búsqueda, seleccione **Puertas de enlace NAT**.

4. En **Puertas de enlace NAT**, seleccione **myNATgateway** o la puerta de enlace NAT que desea cambiar.

5. En la configuración de **myNATgateway**, seleccione **IP de salida**.

6. Seleccione **Cambiar** junto a **IP públicas** en **IP de salida**.

7. Puede optar por reemplazar la dirección IP actual o agregar la dirección existente. En **Administrar prefijos y direcciones IP públicas** junto a **IP públicas**, seleccione **myStandardPublicIP-2**.

8. Seleccione **Aceptar**.

9. Compruebe que **myStandardPublicIP-2** se agregó a las IP públicas. Para eliminar la dirección IP ya asignada, seleccione la papelera de reciclaje si es necesario.

10. Seleccione **Guardar**.

## <a name="add-public-ip-prefix"></a>Adición del prefijo de dirección IP pública

Los prefijos de direcciones IP públicas amplían la extensibilidad de SNAT para las conexiones salientes desde la puerta de enlace NAT. Un prefijo de dirección IP pública evita el agotamiento de puertos SNAT. Cada dirección IP proporciona 64 000 puertos efímeros que se pueden usar.

> [!NOTE] 
> Al asignar un prefijo de dirección IP pública a una puerta de enlace NAT, se usará todo el intervalo. 

En esta sección, cambiará la configuración de la dirección IP de salida para utilizar un prefijo de dirección IP pública que creó anteriormente.

> [!NOTE]
> Puede optar por quitar la dirección IP única asociada con la puerta de enlace NAT y volver a utilizarla, o dejarla asociada a la puerta de enlace NAT para aumentar los puertos SNAT de salida. La puerta de enlace NAT admite una combinación de IP públicas y prefijos de direcciones IP públicas en la configuración de IP de salida. Si creó un prefijo de dirección IP pública con 16 direcciones, quite la IP pública única y guarde la configuración antes de agregar el prefijo. No puede haber más de 16 direcciones IP asignadas.

1. Inicie sesión en [Azure Portal](https://portal.azure.com).

2. En el cuadro de búsqueda que aparece en la parte superior del portal, escriba **Puerta de enlace NAT**.

3. En los resultados de la búsqueda, seleccione **Puertas de enlace NAT**.

4. En **Puertas de enlace NAT**, seleccione **myNATgateway** o la puerta de enlace NAT que desea cambiar.

5. En la configuración de **myNATgateway**, seleccione **IP de salida**.

6. Seleccione **Cambiar** junto a **Prefijos de direcciones IP públicas** en **IP de salida**.

7. Seleccione **myPublicIPPrefixNAT** o el prefijo que desee.

8. Seleccione **Aceptar**.

9. Compruebe que **myPublicIPPrefixNAT** se agregó a los prefijos de direcciones IP públicas.

10. Seleccione **Guardar**.

## <a name="more-information"></a>Más información

* Al implementar máquinas virtuales en una red virtual con una puerta de enlace NAT, todo el tráfico de entrada dirigido a la puerta de enlace NAT sale a través de esa puerta de enlace. Al utilizar una instancia de NAT Gateway con un equilibrador de carga público estándar, todo el tráfico de entrada dirigido a las IP públicas de la puerta de enlace NAT saldrá a través de NAT Gateway. 

    > [!NOTE] 
    > La puerta de enlace de NAT sustituye la configuración de salida de una regla de equilibrio de carga o las reglas de salida. Los miembros del grupo de back-end del equilibrador de carga también usarían la puerta de enlace NAT para las conexiones de salida. Para más información, consulte [Diseño de redes virtuales con recursos de puertas de enlace de NAT](../nat-gateway/nat-gateway-resource.md).

* Las puertas de enlace NAT y las IP públicas pueden tener asignado un valor de tiempo de espera de TCP durante el tiempo que la conexión permanece abierta antes de escuchar conexiones persistentes.  Si se asigna una IP pública a una instancia de NAT Gateway, tiene prioridad el valor de tiempo de espera de la IP.  Para más información, consulte [Diseño de redes virtuales con recursos de puertas de enlace de NAT](../nat-gateway/nat-gateway-resource.md#timers).

## <a name="caveats"></a>Advertencias

* En este momento, la dirección IPv6 pública y los prefijos IPv6 públicos no se admiten en las puertas de enlace NAT. No se pueden implementar puertas de enlace NAT en una subred de red virtual con un prefijo IPv6. Para más información, consulte [Solución de problemas de conectividad de Azure Virtual Network NAT](../nat-gateway/troubleshoot-nat.md).
## <a name="next-steps"></a>Pasos siguientes

En este artículo, aprendió a crear una puerta de enlace NAT de carga y a usar una IP pública existente. Reemplazó la dirección IP en la configuración IP de salida de una puerta de enlace. Por último, cambió una configuración IP de salida para usar un prefijo de dirección IP pública.

- Para más información sobre Azure Virtual Network NAT, consulte [¿Qué es Azure Virtual Network NAT?](../nat-gateway/nat-overview.md)
- Para más información sobre las IP públicas en Azure, consulte [Direcciones IP públicas](../../virtual-network/public-ip-addresses.md).