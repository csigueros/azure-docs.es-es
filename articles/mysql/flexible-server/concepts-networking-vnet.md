---
title: 'Información general sobre acceso a redes privadas: Servidor flexible de Azure Database for MySQL'
description: Obtenga información sobre la opción de redes de acceso privado en la opción de implementación Servidor flexible para Azure Database for MySQL
author: Madhusoodanan
ms.author: dimadhus
ms.service: mysql
ms.topic: conceptual
ms.date: 8/6/2021
ms.openlocfilehash: fa85818b364a869114fe6e3b2de8d2cebfa8ca60
ms.sourcegitcommit: 05dd6452632e00645ec0716a5943c7ac6c9bec7c
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/17/2021
ms.locfileid: "122253657"
---
# <a name="private-network-access-for-azure-database-for-mysql---flexible-server-preview"></a>Acceso a redes privadas para Azure Database for MySQL: Servidor flexible (versión preliminar)

[!INCLUDE[applies-to-mysql-flexible-server](../includes/applies-to-mysql-flexible-server.md)]

En este artículo se describe la opción de conectividad privada para servidor flexible de Azure MySQL. Aprenderá en detalle los conceptos de red virtual para Servidor flexible de Azure Database for MySQL a fin de crear un servidor de forma segura en Azure.

> [!IMPORTANT]
> Servidor flexible de Azure Database for MySQL está en versión preliminar.

## <a name="private-access-vnet-integration"></a>Acceso privado (integración con red virtual)

[Azure Virtual Network (VNet)](../../virtual-network/virtual-networks-overview.md) es el bloque de creación fundamental de una red privada en Azure. Integración de red virtual (VNet) con Azure Database for MySQL: Servidor flexible aporta las ventajas de seguridad y aislamiento de red de Azure.  

La integración de red virtual (VNet) para Azure Database for MySQL: Servidor flexible le permite bloquear el acceso al servicio únicamente a la infraestructura de red virtual. La red virtual (VNet) puede incluir todos los recursos de aplicación y base de datos en una sola red virtual o puede extenderse entre redes virtuales diferentes en la misma región o en diferentes regiones. La conectividad fluida entre varias redes virtuales se puede establecer mediante el [emparejamiento](../../virtual-network/virtual-network-peering-overview.md) que usa la infraestructura troncal privada de infraestructura troncal de baja latencia y ancho de banda alto de Microsoft. A efectos de conectividad las redes virtuales aparecen como una sola.

Azure Database for MySQL: Servidor flexible admite la conectividad de cliente desde:

* Redes virtuales que se encuentren en la misma región de Azure. (redes virtuales emparejadas localmente)
* Redes virtuales en distintas regiones de Azure. (redes virtuales emparejadas globalmente)

Las subredes le permiten segmentar la red virtual en una o varias subredes y asignar una parte del espacio de direcciones de la red virtual que puede implementar posteriormente recursos de Azure. Azure Database for MySQL: Servidor flexible requiere una [subred delegada](../../virtual-network/subnet-delegation-overview.md). Una subred delegada es un identificador explícito de que una subred puede hospedar una única Azure Database for MySQL: Servidores flexibles. Al delegar la subred, el servicio obtiene permisos explícitos para crear recursos específicos del servicio en la subred para administrar sin problemas Azure Database for MySQL: Servidor flexible.

Azure Database for MySQL: Servidor flexible se integra con [zonas DNS privadas](../../dns/private-dns-privatednszone.md) de Azure para proporcionar un servicio DNS confiable y seguro para administrar y resolver los nombres de dominio en una red virtual sin necesidad de agregar una solución DNS personalizada. Las zonas DNS privadas se pueden vincular a una o varias redes virtuales mediante la creación de [vínculos de red virtual](../../dns/private-dns-virtual-network-links.md). 


:::image type="content" source="./media/concepts-networking/vnet-diagram.png" alt-text="Red virtual de MySQL de servidor flexible":::

En el diagrama anterior:

1. Los servidores flexibles se insertan en una subred delegada: 10.0.1.0/24 de la VNET **VNet-1**.
2. Las aplicaciones que se implementan en subredes diferentes dentro de la misma red virtual pueden acceder directamente a los servidores flexibles.
3. Las aplicaciones que se implementan en otra red virtual **VNet-2** no tienen acceso directo a servidores flexibles. Debe realizar el [emparejamiento de red virtual de zona DNS privada](#private-dns-zone-and-vnet-peering) para poder acceder al servidor flexible.

## <a name="virtual-network-concepts"></a>Conceptos de redes virtuales

Estos son algunos conceptos que debe conocer al usar redes virtuales con servidores flexibles de MySQL.

* **Red virtual** -

   Una red virtual (VNet) de Azure contiene un espacio de direcciones IP privadas que se configura para su uso. Para obtener más información sobre las redes virtuales de Azure, visite la [Información general sobre Azure Virtual Network](../../virtual-network/virtual-networks-overview.md).

    La red virtual debe estar en la misma región de Azure que el servidor flexible.

* **Subred delegada** -

   Una red virtual contiene subredes (redes secundarias). Las subredes permiten segmentar la red virtual en espacios de direcciones más pequeños. Los recursos de Azure se implementan en subredes específicas dentro de una red virtual.

   El servidor flexible de MySQL debe estar en una subred **delegada** para uso exclusivo del servidor flexible de MySQL. Esta delegación significa que solo los servidores flexibles de Azure Database for MySQL pueden utilizar esa subred. No puede haber otros tipos de recursos de Azure en la subred delegada. Puede delegar una subred si asigna su propiedad de delegación como Microsoft.DBforMySQL/flexibleServers.

* **Grupos de seguridad de red (NSG)**

   Las reglas de seguridad de grupos de seguridad de red permiten filtrar el tipo de tráfico de red que puede fluir dentro y fuera de las interfaces de red y las subredes de redes virtuales. Revise la [Introducción a los grupos de seguridad de red](../../virtual-network/network-security-groups-overview.md) para obtener más información.

* **Integración de zonas DNS privadas** -

   La integración de zonas DNS privadas de Azure permite resolver el DNS privado dentro de la red virtual actual o en cualquier red virtual emparejada en la región en la que esté vinculada la zona DNS privada.

* **Interconexión de red virtual**

   El emparejamiento de red virtual permite conectar sin problemas dos o más redes virtuales en Azure. A efectos de conectividad las redes virtuales emparejadas aparecen como una sola. El tráfico entre las máquinas virtuales de la red virtual emparejada usa la infraestructura de la red troncal de Microsoft. El tráfico entre la aplicación cliente y el servidor flexible en redes virtuales emparejadas se enruta a través de la red privada de Microsoft únicamente y está aislado a esa red únicamente.

## <a name="using-private-dns-zone"></a>Uso de zonas DNS privadas

* Si usa Azure Portal o la CLI de Azure para crear servidores flexibles con red virtual, se aprovisiona automáticamente una nueva zona DNS privada por servidor de la suscripción con el nombre de servidor proporcionado. Alternativamente, si desea configurar su propia zona DNS privada para usarla con el servidor flexible, consulte la documentación de [ información general de DNS privada](../../dns/private-dns-overview.md).
* Si usa la API de Azure, una plantilla de Azure Resource Manager (plantilla de ARM) o Terraform, cree zonas DNS privadas que terminen con `mysql.database.azure.com` y úselas al configurar servidores flexibles con acceso privado. Para más información, consulte la [información general sobre las zonas DNS privadas](../../dns/private-dns-overview.md).

   > [!IMPORTANT]
   > Los nombres de las zonas DNS privadas deben terminar por `mysql.database.azure.com`.

Obtenga información sobre cómo crear un servidor flexible con acceso privado (integración con red virtual) en [Azure Portal](how-to-manage-virtual-network-portal.md) o [la CLI de Azure](how-to-manage-virtual-network-cli.md).

## <a name="integration-with-custom-dns-server"></a>Integración con el servidor DNS personalizado

Si usa el servidor DNS personalizado, debe usar un reenviador DNS para resolver el FQDN de Azure Database for MySQL: servidor flexible. La dirección IP del reenviador debe ser [168.63.129.16](../../virtual-network/what-is-ip-address-168-63-129-16.md). El servidor DNS personalizado debe estar dentro de la red virtual o bien debe poder accederse a él a través de la configuración del servidor DNS de la red virtual. Para obtener más información, consulte [Resolución de nombres con su propio servidor DNS](../../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-that-uses-your-own-dns-server).

## <a name="private-dns-zone-and-vnet-peering"></a>Zona DNS privada y emparejamiento de la red virtual

La configuración de la zona DNS privada y el emparejamiento de la red virtual son independientes entre sí. Consulte la sección [Uso de las zonas DNS privadas](concepts-networking-vnet.md#using-private-dns-zone) anterior para obtener más detalles sobre la creación y el uso de zonas DNS privadas.

Si desea conectarse al servidor flexible desde un cliente aprovisionado en otra red virtual de la misma región o de otra región, debe vincular la zona DNS privada con la red virtual. Vea la documentación de [cómo vincular la red virtual](../../dns/private-dns-getstarted-portal.md#link-the-virtual-network).

> [!NOTE]
> Solo se pueden vincular los nombres de zonas DNS privadas que terminan con `mysql.database.azure.com`.

## <a name="connecting-from-on-premises-to-flexible-server-in-virtual-network-using-expressroute-or-vpn"></a>Conexión de un servidor local a un servidor flexible en Virtual Network mediante ExpressRoute o VPN

En el caso de cargas de trabajo que requieran acceso a un servidor flexible en la red virtual desde la red local, necesitará [ExpressRoute](/azure/architecture/reference-architectures/hybrid-networking/expressroute/) o [VPN](/azure/architecture/reference-architectures/hybrid-networking/vpn/) y la red virtual [conectados al entorno local](/azure/architecture/reference-architectures/hybrid-networking/). Con esta configuración en su lugar, necesitará un reenviador DNS para resolver el nombre de servidor flexible si desea conectarse desde la aplicación cliente (como MySQL Workbench) que se ejecuta en una red virtual local. Este reenviador DNS es responsable de resolver todas las consultas de DNS a través de un reenviador de nivel de servidor en el servicio DNS proporcionado por Azure [168.63.129.16](../../virtual-network/what-is-ip-address-168-63-129-16.md).

Para realizar la configuración correctamente, necesitaría los siguientes recursos:

* Red local
* Servidor flexible de MySQL aprovisionado con acceso privado (integración con red virtual)
* Red virtual [conectada al entorno local](/azure/architecture/reference-architectures/hybrid-networking/)
* Uso del reenviador de DNS [168.63.129.16](../../virtual-network/what-is-ip-address-168-63-129-16.md) implementado en Azure

Después, puede usar el nombre de servidor flexible (FQDN) para conectarse desde la aplicación cliente en una red virtual emparejada o en una red local a un servidor flexible.

## <a name="unsupported-virtual-network-scenarios"></a>Escenarios de red virtual no admitidos

* Punto de conexión público (o dirección IP pública o DNS): un servidor flexible implementado en una red virtual no puede tener un punto de conexión público
* Una vez que se haya implementado el servidor flexible en una red virtual y una subred, no se puede trasladar a otra red virtual o subred. No se puede trasladar la red virtual a otro grupo de recursos o suscripción.
* No se puede aumentar el tamaño de la subred (espacios de direcciones) cuando existen recursos en la subred
* Un servidor flexible no admite Azure Private Link. En su lugar, usa la inserción de red virtual para que el servidor flexible esté disponible dentro de una red virtual.

> [!NOTE]
> Si usa el servidor DNS personalizado, debe usar un reenviador DNS para resolver el FQDN de Azure Database for MySQL: servidor flexible. Para obtener más información, consulte [Resolución de nombres con su propio servidor DNS](../../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-that-uses-your-own-dns-server).

## <a name="next-steps"></a>Pasos siguientes

* Información sobre cómo habilitar el acceso privado (integración con red virtual) con [Azure Portal](how-to-manage-virtual-network-portal.md) o la [CLI de Azure](how-to-manage-virtual-network-cli.md)
* Información sobre cómo [usar TLS](how-to-connect-tls-ssl.md)
