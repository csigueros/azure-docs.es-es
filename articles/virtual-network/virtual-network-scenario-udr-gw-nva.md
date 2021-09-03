---
title: Conexión híbrida con aplicación de 2 niveles | Microsoft Docs
description: Obtenga información sobre cómo implementar aplicaciones virtuales y UDR para crear un entorno de aplicaciones de niveles múltiples en Azure
services: virtual-network
documentationcenter: na
author: KumudD
manager: carmonm
ms.assetid: 1f509bec-bdd1-470d-8aa4-3cf2bb7f6134
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/05/2016
ms.author: kumud
ms.openlocfilehash: e1d84a09ddd758f333ccb588ac341dce607509d8
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/13/2021
ms.locfileid: "121749265"
---
# <a name="virtual-appliance-scenario"></a>Escenario de aplicación virtual
Un escenario común entre los clientes de Azure de mayor tamaño es la necesidad de ofrecer una aplicación en 2 niveles expuesta a Internet a la vez que permiten el acceso al nivel posterior desde un centro de datos local. Este documento le guiará en un escenario con Rutas definidas por el usuario (UDR), una instancia de VPN Gateway y aplicaciones virtuales de red para implementar un entorno de 2 niveles que cumple los siguientes requisitos:

* Solo se debe poder tener acceso a la aplicación web desde una red pública de Internet.
* El servidor web que hospeda la aplicación debe poder tener acceso a un servidor de aplicaciones back-end.
* Todo el tráfico desde Internet a la aplicación web debe pasar por una aplicación virtual de firewall. Esta aplicación virtual se usará solo para el tráfico de Internet.
* Todo el tráfico que vaya al servidor de aplicaciones debe pasar por una aplicación virtual de firewall. Esta aplicación virtual se usará para tener acceso al servidor back-end y el acceso entrante provendrá de la red local a través de VPN Gateway.
* Los administradores deberán poder administrar las aplicaciones virtuales de firewall desde los equipos locales con una tercera aplicación virtual de firewall que se usará exclusivamente para la administración.

Este es un escenario de red perimetral estándar con una red perimetral y una red protegida. Dicho escenario se puede construir en Azure con NSG, aplicaciones virtuales de firewall o una combinación de ambos elementos. La tabla a continuación muestra algunas de las ventajas y desventajas entre los NSG y las aplicaciones virtuales de firewall.

|  | Ventajas | Desventajas |
| --- | --- | --- |
| **NSG** |No tienen costo. <br/>Están integrados en Azure RBAC. <br/>Las reglas se pueden crear en plantillas de Azure Resource Manager. |La complejidad podría variar en entornos de mayor tamaño. |
| **Firewall** |Control total sobre el plano de datos. <br/>Administración central a través de la consola de firewall. |El costo de la aplicación de firewall. <br/>No está integrado con Azure RBAC. |

La solución siguiente usa aplicaciones virtuales de firewall para implementar un escenario de red protegida o red perimetral.

## <a name="considerations"></a>Consideraciones
Puede implementar el entorno que se explicó anteriormente en Azure con distintas características que actualmente están disponibles, como se indica a continuación.

* **Red virtual**. Una red virtual de Azure actúa de manera similar a una red local y se puede segmentar en una o más subredes para ofrecer el aislamiento del tráfico y la separación de proceso.
* **Aplicación virtual**. Varios socios proporcionan aplicaciones virtuales en Azure Marketplace que se pueden usar para los tres firewalls anteriormente descritos. 
* **Rutas definidas por el usuario (UDR)** . Las tablas de ruta pueden contener UDR que las redes de Azure usan para controlar el flujo de paquetes dentro de una red virtual. Estas tablas de ruta se pueden aplicar a las subredes. Una de las características más nuevas de Azure es la capacidad de aplicar una tabla de ruta a la subred GatewaySubnet, lo que ofrece la posibilidad de reenviar todo el tráfico entrante a la red virtual de Azure desde una conexión híbrida a una aplicación virtual.
* **Reenvío IP**. De manera predeterminada, el motor de redes de Azure reenvía paquetes a las tarjetas de interfaz de red (NIC) virtuales solo si la dirección IP de destino del paquete coincide con la dirección IP de la NIC. Por lo tanto, si una UDR define que un paquete se debe enviar a una aplicación virtual determinada, el motor de redes de Azure descartaría ese paquete. Para asegurarse de que el paquete se entregue a una máquina virtual (en este caso, una aplicación virtual) que no es el destino real del paquete, debe habilitar Reenvío IP para la aplicación virtual.
* **Grupos de seguridad de red (NSG)** . El siguiente ejemplo no usa los NSG, pero puede usarlos en las subredes o las tarjetas NIC de esta solución para filtrar adicionalmente el tráfico que entra y sale de esas subredes y NIC.

![Conectividad de IPv6](./media/virtual-network-scenario-udr-gw-nva/figure01.png)

En este ejemplo hay una suscripción que contiene lo siguiente:

* Dos grupos de recursos que no aparecen en el diagrama. 
  * **ONPREMRG**. Contiene todos los recursos que se necesitan para simular una red local.
  * **AZURERG**. Contiene todos los recursos que se necesitan para el entorno de red virtual de Azure. 
* Una red virtual llamada **onpremvnet** que se usa para simular un centro de datos local segmentado como se muestra a continuación.
  * **onpremsn1**. Subred que contiene una máquina virtual donde se ejecuta Ubuntu para simular un servidor local.
  * **onpremsn2**. Subred que contiene una máquina virtual donde se ejecuta Ubuntu para simular el equipo local que usa un administrador.
* Hay una aplicación virtual de firewall llamada **OPFW** en **onpremvnet** que se usa para mantener un túnel a **azurevnet**.
* Una red virtual llamada **azurevnet** segmentada como se muestra a continuación.
  * **azsn1**. Subred de firewall externo que se usa exclusivamente para el firewall externo. Todo el tráfico de Internet entrará a través de esta subred. Esta subred contiene sólo una NIC vinculada para el firewall externo.
  * **azsn2**. Subred de front-end que hospeda una máquina virtual que se ejecuta como servidor web al que se tendrá acceso desde Internet.
  * **azsn3**. Subred de back-end que hospeda una máquina virtual que ejecuta un servidor de aplicaciones back-end al que tendrá acceso el servidor web de front-end.
  * **azsn4**. Subred de administración que se usa exclusivamente para brindar acceso de administración a todas las aplicaciones virtuales de firewall. Esta subred solo contiene una tarjeta NIC para cada aplicación virtual de firewall que se usa en la solución.
  * **GatewaySubnet**. Subred de conexión híbrida de Azure que se requiere para que ExpressRoute y VPN Gateway proporcionen conectividad entre redes virtuales de Azure y otras redes. 
* Hay 3 aplicaciones virtuales de firewall en la red **azurevnet** . 
  * **AZF1**. Firewall externo expuesto a la red pública de Internet mediante un recurso de dirección IP pública en Azure. Debe asegurarse de tener una plantilla proveniente de Marketplace, o bien directamente desde el proveedor de aplicaciones, que aprovisione una aplicación virtual de 3 NIC.
  * **AZF2**. Firewall interno que se usa para controlar el tráfico entre **azsn2** y **azsn3**. También es una aplicación virtual de 3 NIC.
  * **AZF3**. Firewall de administración al que los administradores tienen acceso desde el centro de datos local y que está conectado a una subred de administración que se usa para administrar todas las aplicaciones de firewall. Puede encontrar plantillas de aplicaciones virtuales de 2 NIC en Marketplace, o bien solicitar una directamente al proveedor de aplicaciones.

## <a name="user-defined-routing-udr"></a>Enrutamiento definido por el usuario (UDR)
Cada subred de Azure se puede vincular a una tabla de UDR que se usa para definir cómo se enruta el tráfico que se inicia en esa subred. Si no hay definida ninguna UDR, Azure usa las rutas predeterminadas para permitir que el tráfico fluya de una subred a otra. Consulte el artículo [¿Qué son las rutas definidas por el usuario y el reenvío IP?](virtual-networks-udr-overview.md)para comprender mejor las UDR.

Para asegurarse de que la comunicación que se realiza a través de la aplicación de firewall correcta, según el último requisito mencionado, debe crear la siguiente tabla de ruta que contiene UDR en **azurevnet**.

### <a name="azgwudr"></a>azgwudr
En este escenario, el único tráfico que fluye desde el sitio local a Azure se usará para administrar los firewalls a través de la conexión con **AZF3** y ese tráfico debe pasar por el firewall interno, **AZF2**. Por lo tanto, solo se necesita una ruta en **GatewaySubnet** , tal como se indica a continuación.

| Destination | Próximo salto | Explicación |
| --- | --- | --- |
| 10.0.4.0/24 |10.0.3.11 |Permite que el tráfico local llegue al firewall de administración **AZF3** |

### <a name="azsn2udr"></a>azsn2udr
| Destination | Próximo salto | Explicación |
| --- | --- | --- |
| 10.0.3.0/24 |10.0.2.11 |Permite el tráfico a la red back-end que hospeda el servidor de aplicaciones a través de **AZF2** |
| 0.0.0.0/0 |10.0.2.10 |Permite que todo el tráfico restante se enrute a través de **AZF1** |

### <a name="azsn3udr"></a>azsn3udr
| Destination | Próximo salto | Explicación |
| --- | --- | --- |
| 10.0.2.0/24 |10.0.3.10 |Permite que el tráfico a **azsn2** fluya desde el servidor de aplicaciones al servidor web a través de **AZF2** |

También deberá crear tablas de ruta para que las subredes de **onpremvnet** simulen el centro de datos local.

### <a name="onpremsn1udr"></a>onpremsn1udr
| Destination | Próximo salto | Explicación |
| --- | --- | --- |
| 192.168.2.0/24 |192.168.1.4 |Permite el tráfico a **onpremsn2** a través de **OPFW** |

### <a name="onpremsn2udr"></a>onpremsn2udr
| Destination | Próximo salto | Explicación |
| --- | --- | --- |
| 10.0.3.0/24 |192.168.2.4 |Permite el tráfico de la subred back-end en Azure a través de **OPFW** |
| 192.168.1.0/24 |192.168.2.4 |Permite el tráfico a **onpremsn1** a través de **OPFW** |

## <a name="ip-forwarding"></a>reenvío de IP
UDR y Reenvío de IP son características que se pueden usar en conjunto para permitir que las aplicaciones virtuales se usen para controlar el flujo de tráfico en una red virtual de Azure.  Un dispositivo virtual no es más que una máquina virtual que ejecuta una aplicación utilizada para controlar el tráfico de red de alguna manera, como un firewall o un dispositivo NAT.

La máquina virtual de este dispositivo virtual debe ser capaz de recibir el tráfico entrante que no se dirige a sí mismo. Para permitir que una máquina virtual reciba el tráfico dirigido a otros destinos, debe habilitar el reenvío IP de la máquina virtual. Esta es una opción de configuración de Azure, no de la configuración del sistema operativo invitado. De todos modos, la aplicación virtual debe ejecutar algún tipo de aplicación para controlar el tráfico entrante y enrutarlo como corresponda.

Visite [¿Qué son las rutas definidas por el usuario y el reenvío IP?](virtual-networks-udr-overview.md)para obtener más información sobre el reenvío IP.

Como ejemplo, imagine que tiene una red virtual de Azure con la siguiente configuración:

* La subred **onpremsn1** contiene una máquina virtual llamada **onpremvm1**.
* La subred **onpremsn2** contiene una máquina virtual llamada **onpremvm2**.
* Una aplicación virtual llamada **OPFW** está conectada a **onpremsn1** y **onpremsn2**.
* Una ruta definida por el usuario vinculada a **onpremsn1** especifica que todo el tráfico a **onpremsn2** se debe enviar a **OPFW**.

En este punto, si **onpremvm1** intenta establecer una conexión con **onpremvm2**, se usará la UDR y el tráfico se enviará a **OPFW** como el próximo salto. Tenga en cuenta que el destino real del paquete no cambia, el destino sigue siendo **onpremvm2** . 

Si Reenvío IP no está habilitado para **OPFW**, la lógica de la red virtual de Azure descartará los paquetes, debido a que solo permite que los paquetes se envíen a una máquina virtual si la dirección IP de la máquina virtual es el destino del paquete.

Con Reenvío IP, la lógica de red virtual de Azure reenviará los paquetes a OPFW sin cambiar la dirección de destino original. **OPFW** debe controlar los paquetes y determinar qué hacer con ellos.

Para que el escenario anterior funcione, debe habilitar Reenvío IP en las tarjetas NIC para **OPFW**, **AZF1**, **AZF2** y **AZF3**, que se usan para el enrutamiento (todas las tarjetas NIC, excepto las vinculadas a la subred de administración). 

## <a name="firewall-rules"></a>Reglas de firewall
Como se describió anteriormente, Reenvío IP solo asegura que los paquetes se envíen a las aplicaciones virtuales. De todos modos, la aplicación debe decidir qué hacer con esos paquetes. En el escenario anterior, deberá crear las siguientes reglas en las aplicaciones:

### <a name="opfw"></a>OPFW
OPFW representa un dispositivo local que contiene las siguientes reglas:

* **Ruta**: todo el tráfico a 10.0.0.0/16 (**azurevnet**) se debe enviar a través del túnel **ONPREMAZURE**.
* **Directiva**: permita todo el tráfico bidireccional entre **port2** y **ONPREMAZURE**.

### <a name="azf1"></a>AZF1
AZF1 representa una aplicación virtual de Azure que incluye las siguientes reglas:

* **Directiva**: permita todo el tráfico bidireccional entre **port1** y **port2**.

### <a name="azf2"></a>AZF2
AZF2 representa una aplicación de Azure que contiene las siguientes reglas:

* **Directiva**: permita todo el tráfico bidireccional entre **port1** y **port2**.

### <a name="azf3"></a>AZF3
AZF3 representa una aplicación virtual de Azure que incluye las reglas siguientes:

* **Ruta**: todo el tráfico a 192.168.0.0/16 (**onpremvnet**) se debe enviar a la dirección IP de la puerta de enlace de Azure (es decir, 10.0.0.1) a través de **port1**.

## <a name="network-security-groups-nsgs"></a>Grupos de seguridad de red (NSG)
En este escenario, no se usan los NSG. Sin embargo, podría aplicar los NSG a cada subred para restringir el tráfico entrante y saliente. Por ejemplo, podría aplicar las siguientes reglas de NSG a la subred de firewall externo.

**Entrante**

* Permita todo el tráfico TCP desde Internet al puerto 80 en cualquier máquina virtual de la subred.
* Deniegue el resto del tráfico que provenga de Internet.

**Saliente**

* Deniegue todo el tráfico hacia Internet.

## <a name="high-level-steps"></a>Pasos de alto nivel
Para implementar este escenario, siga estos pasos de alto nivel.

1. Inicie sesión a su suscripción de Azure.
2. Si desea implementar una red virtual que simule la red local, aprovisione los recursos que son parte de **ONPREMRG**.
3. Aprovisione los recursos que son parte de **AZURERG**.
4. Aprovisione el túnel de **onpremvnet** a **azurevnet**.
5. Una vez que se aprovisionen todos los recursos, inicie sesión en **onpremvm2** y haga ping a 10.0.3.101 para probar la conectividad entre **onpremsn2** y **azsn3**.
