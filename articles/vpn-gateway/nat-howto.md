---
title: Configuración de NAT en Azure VPN Gateway
titleSuffix: Azure VPN Gateway
description: Aprenda cómo configurar NAT en Azure VPN Gateway.
services: vpn-gateway
author: yushwang
ms.service: vpn-gateway
ms.topic: how-to
ms.date: 06/24/2021
ms.author: yushwang
ms.openlocfilehash: 8b9d3d298986aa4aae6ead4e9733c9b0994b1f91
ms.sourcegitcommit: 6bd31ec35ac44d79debfe98a3ef32fb3522e3934
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 07/02/2021
ms.locfileid: "113214072"
---
# <a name="how-to-configure-nat-on-azure-vpn-gateways-preview"></a>Cómo configurar NAT en instancias de Azure VPN Gateway (versión preliminar)

Este artículo le ayuda a configurar la traducción de direcciones de red (NAT) en Azure VPN Gateway mediante Azure Portal.

## <a name="about-nat"></a><a name="about"></a>Acerca de NAT

NAT define los mecanismos para traducir una dirección IP a otra en un paquete IP. Se usa normalmente para conectar redes con intervalos de direcciones IP superpuestos. Las directivas o reglas NAT en los dispositivos de puerta de enlace que conectan las redes especifican las asignaciones de direcciones para la traducción de direcciones en las redes.

Para obtener más información sobre la compatibilidad con NAT en Azure VPN Gateway, consulte la documentación [Acerca de NAT en instancias de Azure VPN Gateway](nat-overview.md).

> [!IMPORTANT] 
> NAT de Azure para VPN Gateway está en versión preliminar. 
> * Asegúrese de leer la sección **[Limitaciones de la versión preliminar](#limits)** de este artículo.
> * NAT de Azure VPN Gateway solo admite reglas NAT estáticas 1:1. No se admiten reglas NAT dinámicas.
> * NAT se admite en las SKU siguientes: VpnGw2~5, VpnGw2AZ~5AZ.

## <a name="getting-started"></a>Introducción

Cada parte de este artículo le ayuda a constituir un bloque de creación básico para configurar NAT en la conectividad de red. Si completa las tres partes, podrá crear la topología tal como se muestra en el diagrama 1.

### <a name="diagram-1"></a><a name="diagram"></a>Diagrama 1

:::image type="content" source="./media/nat-overview/vpn-nat.png" alt-text="Captura de pantalla del diagrama 1." lightbox="./media/nat-overview/vpn-nat.png" border="false":::

### <a name="prerequisites"></a>Requisitos previos

* Compruebe que tiene una suscripción a Azure. Si todavía no la tiene, puede activar sus [ventajas como suscriptor de MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) o registrarse para obtener una [cuenta gratuita](https://azure.microsoft.com/pricing/free-trial/).
* Consulte las [limitaciones de la versión preliminar](#limits).

## <a name="part-1-create-vnet-and-gateways"></a><a name ="vnet"></a>Parte 1: Creación de redes virtuales y puertas de enlace

En esta sección, creará una red virtual, una instancia de VPN Gateway y los recursos de puerta de enlace de red local para que se correspondan con los recursos que se muestran en el [Diagrama 1](#diagram).

Para crear estos recursos, siga los pasos descritos en el artículo del [tutorial de conexión de sitio a sitio](tutorial-site-to-site-portal.md). Complete las secciones del artículo siguientes, pero no cree ninguna conexión.

* [Red virtual](tutorial-site-to-site-portal.md#CreatVNet)
* [VPN Gateway](tutorial-site-to-site-portal.md#VNetGateway)
* [Puerta de enlace de red local](tutorial-site-to-site-portal.md#LocalNetworkGateway)
* [Configuración del dispositivo VPN](tutorial-site-to-site-portal.md#VPNDevice)


>[!IMPORTANT]
>  Cuando siga los pasos descritos en los artículos siguientes, no cree los recursos de **conexión** en los artículos. Se producirá un error en la operación porque los espacios de direcciones IP son los mismos entre la red virtual, la rama 1 y la rama 2. Siga los pasos de la sección siguiente para crear las reglas NAT y, a continuación, cree las conexiones con dichas reglas.
>


En las capturas de pantalla siguientes se muestran ejemplos de los recursos que se van a crear.

* **Red virtual**

   :::image type="content" source="./media/nat-howto/vnet.png" alt-text="Captura de pantalla que muestra el espacio de direcciones de la red virtual." lightbox="./media/nat-howto/vnet.png":::
* **VPN Gateway**

   :::image type="content" source="./media/nat-howto/vpn-gateway.png" alt-text="Captura de pantalla que muestra la puerta de enlace." lightbox="./media/nat-howto/vpn-gateway.png":::
* **Rama 1, puerta de enlace de red local**

   :::image type="content" source="./media/nat-howto/branch-1.png" alt-text="Captura de pantalla que muestra la puerta de enlace de red local de la rama 1." lightbox="./media/nat-howto/branch-1.png" :::

* **Rama 2, puerta de enlace de red local**

   :::image type="content" source="./media/nat-howto/branch-2.png" alt-text="Captura de pantalla que muestra la puerta de enlace de red local de la rama 2." lightbox="./media/nat-howto/branch-2.png":::

   > [!IMPORTANT] 
   > Durante la versión preliminar, si el espacio de direcciones de la puerta de enlace de red local es el mismo o menor que el espacio de direcciones de la red virtual, use **BGP** y deje el campo espacio de direcciones de la puerta de enlace de red local **en blanco**. El enrutamiento estático (que no es BGP) no se admite en este escenario durante la versión preliminar.
   >

## <a name="part-2-create-nat-rules"></a><a name ="nat-rules"></a>Parte 2: Creación de reglas NAT

Antes de crear las conexiones, debe crear y guardar reglas NAT en la puerta de enlace de la red privada virtual. En la tabla siguiente se muestran las reglas NAT requeridas. Consulte el [Diagrama 1](#diagram) para ver la topología.

**Tabla de reglas NAT**

| Nombre     | Tipo   | Modo        | Interno    | Externo     | Conexión          |
| ---      | ---    | ---         | ---         | ---          | ---                 |
| VNet     | Estático | EgressSNAT  | 10.0.1.0/24 | 100.0.1.0/24 | Ambas conexiones    | 
| Branch_1 | Estático | IngressSNAT | 10.0.1.0/24 | 100.0.2.0/24 | Conexión de la rama 1 |
| Branch_2 | Estático | IngressSNAT | 10.0.1.0/24 | 100.0.3.0/24 | Conexión de la rama 2 |

Realice los pasos siguientes para crear todas las reglas NAT en la puerta de enlace de VPN.

1. En Azure Portal, vaya a la página de recursos **Puerta de enlace de red virtual** y seleccione **Reglas NAT (versión preliminar)** .
1. Rellene los valores con la **tabla de reglas NAT** anterior.

   :::image type="content" source="./media/nat-howto/nat-rules.png" alt-text="Captura de pantalla que muestra las reglas NAT." lightbox="./media/nat-howto/nat-rules.png":::
1. Haga clic en **Guardar** para guardar las reglas NAT en el recurso de puerta de enlace de VPN. La operación puede tardar hasta 10 minutos en completarse.

## <a name="part-3-create-connections-and-link-nat-rules"></a><a name ="connections"></a>Parte 3: Creación de conexiones y vinculación de reglas NAT

En esta sección, creará las conexiones y, a continuación, asociará las reglas NAT con las conexiones para implementar la topología de ejemplo en el [Diagrama 1](#diagram).

### <a name="1-create-connections"></a>1. Creación de conexiones

Siga los pasos descritos en el artículo sobre la [creación de una conexión de sitio a sitio](tutorial-site-to-site-portal.md) para crear ambas conexiones, tal y como se muestra a continuación:

   :::image type="content" source="./media/nat-howto/connections.png" alt-text="Captura de pantalla que muestra la página Conexiones." lightbox="./media/nat-howto/connections.png":::

### <a name="2-associate-nat-rules-with-the-connections"></a>2. Asociación de reglas NAT con las conexiones

En este paso, asociará las reglas NAT a cada uno de los recursos de conexión.

1. En Azure Portal, vaya a los recursos de conexión y seleccione **Configuración**.

1. En Ingress NAT Rules (Reglas NAT de entrada), seleccione las reglas NAT que se han creado anteriormente.

   :::image type="content" source="./media/nat-howto/config-nat.png" alt-text="Captura de pantalla que muestra las reglas NAT configuradas." lightbox="./media/nat-howto/config-nat.png":::

1. Haga clic en **Guardar** para aplicar las configuraciones al recurso de conexión.

1. Repita los pasos para aplicar las reglas NAT a otros recursos de conexión.

1. Si se usa BGP, seleccione **Enable BGP Route Translation** (Habilitar traducción de rutas BGP) en la página de reglas NAT y haga clic en **Guardar**. Tenga en cuenta que la tabla muestra ahora las conexiones vinculadas a cada regla NAT.

   :::image type="content" source="./media/nat-howto/nat-rules-linked.png" alt-text="Captura de pantalla que muestra la opción para habilitar BGP." lightbox="./media/nat-howto/nat-rules-linked.png":::

Después de completar estos pasos, tendrá una configuración que coincide con la topología que se muestra en el [Diagrama 1](#diagram).

## <a name="preview-limitations"></a><a name ="limits"></a>Limitaciones de la versión preliminar

> [!IMPORTANT] 
> Existen algunas restricciones durante la versión preliminar de la característica NAT. Algunas de estas se abordarán antes de la disponibilidad general.
>

* NAT de Azure VPN Gateway solo admite reglas NAT estáticas 1:1. No se admiten reglas NAT dinámicas.
* NAT se admite en las SKU siguientes: VpnGw2~5, VpnGw2AZ~5AZ.
* NAT solo se admite para conexiones entre locales de IPsec o IKE. No se admiten conexiones de red virtual a red virtual ni conexiones de punto a sitio.
* Las reglas NAT no se pueden asociar a los recursos de conexión durante el proceso de creación de la conexión. Cree primero el recurso de conexión y, a continuación, asocie las reglas NAT en la página Configuración de la conexión.
* Para la versión preliminar, use **BGP** y deje el espacio de direcciones de la puerta de enlace de red local **en blanco** si dicho espacio es el mismo o es una parte del espacio de direcciones de la red virtual. El enrutamiento estático (que no es BGP) **no** se admite con el conflicto de direcciones entre las puertas de enlace de red local y la red virtual.
* Los espacios de direcciones para distintas puertas de enlace de red local (redes o ramas locales) pueden ser los mismos con las reglas *IngressSNAT* para asignarse a prefijos no superpuestos, tal y como se muestra en el [Diagrama 1](#diagram).
* Las reglas NAT no se admiten en las conexiones que tienen habilitado el *uso de selectores de tráfico basados en directivas*.

## <a name="next-steps"></a>Pasos siguientes

Una vez completada la conexión, puede agregar máquinas virtuales a las redes virtuales. Consulte [Creación de una máquina virtual que ejecuta Windows en el Portal de Azure](../virtual-machines/windows/quick-create-portal.md) para ver los pasos.
