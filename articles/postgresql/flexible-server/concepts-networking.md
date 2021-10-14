---
title: 'Información general sobre redes: Servidor flexible de Azure Database for PostgreSQL'
description: Obtenga información sobre las opciones de conectividad y redes en la opción de implementación Servidor flexible para Azure Database for PostgreSQL.
author: niklarin
ms.author: nlarin
ms.service: postgresql
ms.topic: conceptual
ms.date: 07/08/2021
ms.openlocfilehash: 3a0ce42cf32e218f3debaf6f3e84bb8f27a81c82
ms.sourcegitcommit: 613789059b275cfae44f2a983906cca06a8706ad
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/29/2021
ms.locfileid: "129279039"
---
# <a name="networking-overview-for-azure-database-for-postgresql---flexible-server-preview"></a>Información general sobre redes para Azure Database for PostgreSQL con la opción Servidor flexible (versión preliminar)

En este artículo se describen los conceptos de conectividad y redes para Servidor flexible de Azure Database for PostgreSQL. La opción de implementación Servidor flexible se encuentra disponible en versión preliminar.

Al crear una instancia de Azure Database for PostgreSQL con la opción Servidor flexible (un *servidor flexible*), debe elegir una de las siguientes opciones de red: **Acceso privado (integración con red virtual)** o **Acceso público (direcciones IP permitidas)** . 

> [!NOTE]
> No se puede cambiar la opción de red una vez creado el servidor. 

Las características siguientes se aplican tanto si decide usar la opción de acceso privado como la de acceso público:

* Las conexiones desde direcciones IP permitidas se deben autenticar en el servidor PostgreSQL con credenciales válidas.
* El [cifrado de la conexión](#tls-and-ssl) se aplica para el tráfico de red.
* El servidor tiene un nombre de dominio completo (FQDN). En el caso de la propiedad `hostname` en las cadenas de conexión, se recomienda usar el FQDN en lugar de una dirección IP.
* Las dos opciones controlan el acceso en el nivel de servidor, no en el nivel de base de datos o de tabla. Tendría que usar las propiedades de los roles de PostgreSQL para controlar el acceso a bases de datos, tablas y otros objetos.

> [!NOTE]
> Dado que Azure Database for PostgreSQL es un servicio de base de datos administrado, no se proporciona a los usuarios acceso al host ni al sistema operativo para ver o modificar archivos de configuración como `pg_hba.conf`. El contenido de los archivos se actualiza de forma automática en función de la configuración de red.

## <a name="private-access-vnet-integration"></a>Acceso privado (integración con red virtual)

Puede implementar un servidor flexible en la [Red virtual de Azure (VNet)](../../virtual-network/virtual-networks-overview.md). Las redes virtuales de Azure proporcionan una comunicación de red privada y segura. Los recursos de una red virtual pueden comunicarse a través de direcciones IP privadas que se asignaron en esta red.

Elija la opción de red si quiere las funcionalidades siguientes:

* Conexión desde recursos de Azure en la misma red virtual a un servidor flexible mediante direcciones IP privadas
* Uso de VPN o Azure ExpressRoute para conectarse desde recursos que no son de Azure al servidor flexible
* Garantizar que el servidor flexible no tenga ningún punto de conexión público al que se pueda acceder a través de Internet

:::image type="content" source="./media/how-to-manage-virtual-network-portal/flexible-pg-vnet-diagram.png" alt-text="Diagrama en el que se muestra cómo funciona el emparejamiento entre redes virtuales, una de las cuales incluye un servidor flexible.":::

En el diagrama anterior:
- Los servidores flexibles se insertan en una subred 10.0.1.0/24 de la red virtual VNet-1.
- Las aplicaciones que se implementan en subredes diferentes dentro de la misma red virtual pueden acceder directamente a los servidores flexibles.
- Las aplicaciones que se implementan en otra red virtual (VNet-2) no tienen acceso directo a los servidores flexibles. Debe realizar el [emparejamiento de la red virtual con una zona DNS privada](#private-dns-zone-and-virtual-network-peering) para que las aplicaciones puedan acceder al servidor flexible.
   
### <a name="virtual-network-concepts"></a>Conceptos de redes virtuales

Una red virtual de Azure contiene un espacio de direcciones IP privadas que se configura para su uso. La red virtual debe estar en la misma región de Azure que el servidor flexible. Para más información sobre las redes virtuales, vea la [Información general sobre Azure Virtual Network](../../virtual-network/virtual-networks-overview.md).

Estos son algunos conceptos que debe conocer al usar redes virtuales con servidores flexibles de PostgreSQL:

* **Subred delegada**. Una red virtual contiene subredes. Las subredes permiten segmentar la red virtual en espacios de direcciones más pequeños. Los recursos de Azure se implementan en subredes específicas dentro de una red virtual. 

  El servidor flexible debe estar en una subred *delegada*. Es decir, solo las instancias de Azure Database for PostgreSQL con la opción Servidor flexible pueden usar esa subred. No puede haber otros tipos de recursos de Azure en la subred delegada. Para delegar una subred, asigne su propiedad de delegación como `Microsoft.DBforPostgreSQL/flexibleServers`.

  > [!IMPORTANT]
  > Los nombres `AzureFirewallSubnet`, `AzureFirewallManagementSubnet`, `AzureBastionSubnet` y `GatewaySubnet` están reservados en Azure. No use ninguno de ellos para el nombre de la subred.

* **Grupo de seguridad de red (NSG)** . Las reglas de seguridad de los grupos de seguridad de red permiten filtrar el tipo de tráfico de red que puede fluir dentro y fuera de las interfaces de red y las subredes de redes virtuales. Para más información, vea la [información general sobre los grupos de seguridad de red](../../virtual-network/network-security-groups-overview.md).

  Los grupos de seguridad de aplicaciones (ASG) facilitan el control de la seguridad de nivel 4 mediante el uso de grupos de seguridad de red para redes planas. Puede hacer las siguientes acciones de forma rápida:
  
  - Unir máquinas virtuales a un ASG o quitar máquinas virtuales de un ASG
  - Aplicar dinámicamente reglas a esas máquinas virtuales o quitar las reglas de esas máquinas virtuales 
  
  Para más información, vea la [información general sobre los grupos de seguridad de aplicaciones](../../virtual-network/application-security-groups.md). 
  
  En este momento, no admitimos los NSG donde un ASG forma parte de la regla mediante Azure Database for PostgreSQL con la opción Servidor flexible. Actualmente se recomienda usar el [filtrado de origen o de destino basado en IP](../../virtual-network/network-security-groups-overview.md#security-rules) en un NSG. 

  > [!IMPORTANT]
  > Las características de alta disponibilidad de la opción de servidor flexible de Azure Database for PostgreSQL requieren la capacidad de enviar y recibir tráfico a los puertos de destino 5432 y 6432 dentro de la subred de la red virtual de Azure donde se ha implementado el servidor flexible de Azure Database for PostgreSQL, así como a Azure Storage para el archivado de registros. Si crea [grupos de seguridad de red (NSG)](../../virtual-network/network-security-groups-overview.md) para denegar el flujo de tráfico hacia o desde el servidor flexible de Azure Database for PostgreSQL dentro de la subred donde se implementó, asegúrese de permitir el tráfico a los puertos de destino 5432 y 6432 dentro de la subred y también a Azure Storage mediante la [etiqueta de servicio](../../virtual-network/service-tags-overview.md) de Azure Storage como destino.

* **Integración de la zona DNS privada**. La integración de la zona DNS privada de Azure permite resolver el DNS privado dentro de la red virtual actual o en cualquier red virtual emparejada en la región en la que esté vinculada la zona DNS privada. 

### <a name="using-a-private-dns-zone"></a>Uso de una zona DNS privada

Si usa Azure Portal o la CLI de Azure para crear servidores flexibles con una red virtual, se aprovisiona automáticamente una nueva zona DNS privada para cada servidor de la suscripción mediante el nombre de servidor que proporcionó. 

Si usa una API de Azure, una plantilla de Azure Resource Manager (plantilla de ARM) o Terraform, cree zonas DNS privadas que terminen con `postgres.database.azure.com`. Use esas zonas al configurar servidores flexibles con acceso privado. Para más información, vea la [información general sobre las zonas DNS privadas](../../dns/private-dns-overview.md).


 Al usar el acceso de red privada con la red virtual de Azure, proporcionar la información de la zona DNS privada es obligatorio en varias interfaces, como la API, ARM y Terraform.  Por lo tanto, para la nueva creación de un servidor flexible de Azure Database for PostgreSQL mediante el acceso de red privada con la API, ARM o Terraform, cree zonas DNS privadas y úselas al configurar los servidores flexibles con acceso privado. Consulte más información sobre las [Especificaciones de la API REST para Microsoft Azure](https://github.com/Azure/azure-rest-api-specs/blob/master/specification/postgresql/resource-manager/Microsoft.DBforPostgreSQL/preview/2021-06-01-preview/postgresql.json). Si usa [Azure Portal](./how-to-manage-virtual-network-portal.md) o la [CLI de Azure](./how-to-manage-virtual-network-cli.md) para crear servidores flexibles, puede proporcionar un nombre de zona DNS privada que haya creado previamente en la misma suscripción o en otra, o bien se crea automáticamente una zona DNS privada predeterminada en la suscripción.



### <a name="integration-with-a-custom-dns-server"></a>Integración con un servidor DNS personalizado

Si usa el servidor DNS personalizado, debe usar un reenviador DNS para resolver el FQDN de Azure Database for PostgreSQL con la opción Servidor flexible. La dirección IP del reenviador debe ser [168.63.129.16](../../virtual-network/what-is-ip-address-168-63-129-16.md). 

El servidor DNS personalizado debe estar dentro de la red virtual o bien debe poder accederse a él a través de la configuración del servidor DNS de la red virtual. Para más información, vea [Resolución de nombres con su propio servidor DNS](../../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-that-uses-your-own-dns-server).

### <a name="private-dns-zone-and-virtual-network-peering"></a>Zona DNS privada y emparejamiento de red virtual

La configuración de la zona DNS privada y el emparejamiento de red virtual son independientes entre sí. Si desea conectarse al servidor flexible desde un cliente aprovisionado en otra red virtual de la misma región o de otra región, debe vincular la zona DNS privada con la red virtual. Para más información, vea [Vincular la red virtual](../../dns/private-dns-getstarted-portal.md#link-the-virtual-network).

> [!NOTE]
> Solo se pueden vincular los nombres de zonas DNS privadas que terminan con `postgres.database.azure.com`.

### <a name="unsupported-virtual-network-scenarios"></a>Escenarios de red virtual no admitidos

Estas son algunas limitaciones para trabajar con las redes virtuales:

* Un servidor flexible implementado en una red virtual no puede tener un punto de conexión público ni un DNS o una IP públicos.
* Una vez que se haya implementado un servidor flexible en una red virtual y una subred, no se puede trasladar a otra red virtual o subred. No se puede trasladar la red virtual a otro grupo de recursos o suscripción.
* No se puede aumentar el tamaño de la subred (espacios de direcciones) después de que existan recursos en la subred.
* Un servidor flexible no admite Azure Private Link. En su lugar, usa la inserción de la red virtual para que el servidor flexible esté disponible dentro de una red virtual. 


## <a name="public-access-allowed-ip-addresses"></a>Acceso público (direcciones IP permitidas)

Al elegir el método de acceso público, se accede al servidor flexible mediante un punto de conexión público a través de Internet. El punto de conexión público es una dirección DNS que se puede resolver públicamente. La frase *direcciones IP permitidas* hace referencia a un intervalo de direcciones IP a las que decida conceder permiso para acceder al servidor. Estos permisos se denominan *reglas de firewall*. 

Elija la opción de red si quiere las funcionalidades siguientes:

* Conexión desde recursos de Azure que no admiten redes virtuales
* Conexión desde recursos externos de Azure que no están conectados mediante VPN o ExpressRoute
* Garantizar que el servidor flexible tenga un punto de conexión público al que se pueda acceder a través de Internet

Entre las características del método de acceso público se incluyen las siguientes:

* Solo las direcciones IP que permita tienen permiso para acceder al servidor flexible de PostgreSQL. De forma predeterminada, no se admite ninguna dirección IP. Puede agregar direcciones IP durante la creación del servidor o después.
* El servidor PostgreSQL tiene un nombre DNS que se puede resolver de forma pública.
* El servidor flexible no está en una de las redes virtuales de Azure.
* El tráfico de red hacia y desde el servidor no se realiza a través de una red privada. El tráfico usa las rutas de Internet generales.

### <a name="firewall-rules"></a>Reglas de firewall

Si un intento de conexión procede de una dirección IP que no se ha permitido a través de una regla de firewall, el cliente de origen recibirá un error.

### <a name="allowing-all-azure-ip-addresses"></a>Permitir todas las direcciones IP de Azure

Si no hay ninguna dirección IP saliente fija disponible para su servicio de Azure, puede habilitar las conexiones desde todas las direcciones IP de los centros de datos de Azure.

> [!IMPORTANT]
> La opción **Permitir acceso público desde los recursos y servicios de Azure dentro de Azure** configura el firewall para permitir todas las conexiones desde Azure, incluidas las que provienen de suscripciones de otros clientes. Si selecciona esta opción, asegúrese de que los permisos de usuario y el inicio de sesión solo dejan acceder a los usuarios autorizados.

### <a name="troubleshooting-public-access-issues"></a>Solución de problemas de acceso público
Tenga en cuenta los siguientes puntos cuando el acceso al servicio Azure Database for PostgreSQL no se comporte según lo previsto:

* **Los cambios en la lista de permitidos aún no se han aplicado**. Puede que se produzca un retraso de hasta cinco minutos hasta que se apliquen los cambios de configuración del firewall del servidor de Azure Database for PostgreSQL.

* **Error de autenticación**. Si un usuario no tiene permisos en el servidor de Azure Database for PostgreSQL o la contraseña usada es incorrecta, se denegará la conexión al servidor de Azure Database for PostgreSQL. La creación de una configuración de firewall solo ofrece a los clientes una oportunidad para intentar conectarse al servidor. Cada cliente todavía tiene que proporcionar las credenciales de seguridad necesarias.

* **La dirección IP de cliente dinámica impide el acceso**. Si tiene una conexión a Internet con direccionamiento IP dinámico y tiene problemas con el firewall, pruebe alguna de las soluciones siguientes:

   * Pida a su proveedor de acceso a Internet (ISP) el intervalo de direcciones IP asignado a los equipos cliente que acceden al servidor de Azure Database for PostgreSQL. A continuación, agréguelo como una regla de firewall.
   * Obtenga el direccionamiento IP estático en su lugar para los equipos cliente y luego agregue la dirección IP estática como regla de firewall.

* **La regla de firewall no está disponible en formato IPv6**. las reglas de firewall deben estar en formato IPv4. Si especifica reglas de firewall en formato IPv6, recibirá un error de validación.

## <a name="host-name"></a>Nombre de host

Con independencia de la opción de red que elija, se recomienda usar siempre un FQDN como nombre de host al conectarse al servidor flexible. No se garantiza que la dirección IP del servidor permanezca estática. El uso del FQDN le ayudará a evitar realizar cambios en la cadena de conexión. 

Un ejemplo que usa un FQDN como nombre de host es `hostname = servername.postgres.database.azure.com`. Siempre que sea posible, evite usar `hostname = 10.0.0.4` (una dirección privada) o `hostname = 40.2.45.67` (una dirección pública).


## <a name="tls-and-ssl"></a>TLS y SSL

Azure Database for PostgreSQL con la opción Servidor flexible aplica la conexión de las aplicaciones cliente al servicio PostgreSQL mediante Seguridad de la capa de transporte (TLS). TLS es un protocolo estándar del sector que garantiza conexiones de red cifradas entre el servidor de bases de datos y las aplicaciones cliente. TLS es un protocolo actualizado de Capa de sockets seguros (SSL). 

Azure Database for PostgreSQL admite TLS 1.2 y versiones posteriores. En [RFC 8996](https://datatracker.ietf.org/doc/rfc8996/), el Grupo de tareas de ingeniería de Internet (IETF) indica explícitamente que no se deben usar TLS 1.0 ni TLS 1.1. Ambos protocolos quedaron en desuso a finales de 2019.

Se denegarán de manera predeterminada todas las conexiones entrantes que usen versiones anteriores del protocolo TLS, como TLS 1.0 y TLS 1.1. 

> [!NOTE]
> Los certificados SSL y TLS certifican que la conexión está protegida con protocolos de cifrado de última generación. Al cifrar la conexión en directo, se evita el acceso no autorizado a los datos mientras están en tránsito. Este es el motivo por el que se recomienda usar las versiones más recientes de TLS para cifrar las conexiones al servidor flexible de Azure Database for PostgreSQL. Aunque no se recomienda, si es necesario, tiene la opción de deshabilitar TLS\SSL para las conexiones al servidor flexible de Azure Database for PostgreSQL mediante la actualización del parámetro de servidor **require_secure_transport** a OFF. También puede establecer la versión de TLS; para ello, establezca los parámetros de servidor **ssl_min_protocol_version** y **ssl_max_protocol_version**.

## <a name="next-steps"></a>Pasos siguientes

* Obtenga información sobre cómo crear un servidor flexible con la opción **Acceso privado (integración con red virtual)** en [Azure Portal](how-to-manage-virtual-network-portal.md) o en la [CLI de Azure](how-to-manage-virtual-network-cli.md).
* Obtenga información sobre cómo crear un servidor flexible con la opción **Acceso público (direcciones IP permitidas)** en [Azure Portal](how-to-manage-firewall-portal.md) o en la [CLI de Azure](how-to-manage-firewall-cli.md).