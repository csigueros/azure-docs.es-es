---
title: 'Acceso privado (versión preliminar): Hiperescala (Citus) en Azure Database for PostgreSQL'
description: En este artículo, se describe el acceso privado para Hiperescala (Citus) en Azure Database for PostgreSQL.
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.topic: conceptual
ms.date: 10/15/2021
ms.openlocfilehash: 53e2cc3c62cc04ef05ccfe22b4876616916b0284
ms.sourcegitcommit: 37cc33d25f2daea40b6158a8a56b08641bca0a43
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/15/2021
ms.locfileid: "130074969"
---
# <a name="private-access-preview-in-azure-database-for-postgresql---hyperscale-citus"></a>Acceso privado (versión preliminar) de Hiperescala (Citus) en Azure Database for PostgreSQL

[!INCLUDE [azure-postgresql-hyperscale-access](../../includes/azure-postgresql-hyperscale-access.md)]

En esta página, se describe la opción de acceso privado. Para el acceso público, consulte [aquí](concepts-hyperscale-firewall-rules.md).

> [!NOTE]
>
> El acceso privado está disponible para la versión preliminar solo en [determinadas regiones](concepts-hyperscale-limits.md#regions).
>
> Si la opción de acceso privado no se puede seleccionar para el grupo de servidores aunque el grupo de servidores esté dentro de una región permitida, abra una [solicitud de soporte técnico](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest) de Azure e incluya su identificador de suscripción de Azure para obtener acceso.

## <a name="definitions"></a>Definiciones

**Red virtual**. Una red virtual (VNet) de Azure es el bloque de creación fundamental de las redes privadas en Azure. Las redes virtuales permiten a muchos tipos de recursos de Azure, como servidores de bases de datos y máquinas virtuales de Azure, comunicarse de forma segura entre ellos. Las redes virtuales admiten conexiones locales, permiten que los hosts de varias redes virtuales interactúen entre sí mediante el emparejamiento y proporcionan ventajas adicionales de escala, opciones de seguridad y aislamiento. Cada punto de conexión privado de un grupo de servidores Hiperescala (Citus) requiere una red virtual asociada.

**Subred**. Las subredes segmentan una red virtual en una o varias redes secundarias.
Cada subred obtiene una parte del espacio de direcciones, lo que mejora la eficacia de la asignación de direcciones.  Puede proteger los recursos dentro de las subredes mediante grupos de seguridad de red. Para más información, consulteGrupo de seguridad de red.

Al seleccionar una subred para un punto de conexión privado de Hiperescala (Citus), asegúrese de que haya suficientes direcciones IP privadas disponibles en esa subred para las necesidades actuales y futuras.

**Punto de conexión privado**. Un punto de conexión privado es una interfaz de red que usa una dirección IP privada de una red virtual. Esta interfaz de red se conecta de forma privada y segura a un servicio con la tecnología de Azure Private Link. Los puntos de conexión privados traen los servicios a la red virtual.

Al habilitar el acceso privado para Hiperescala (Citus), se crea un punto de conexión privado para el nodo de coordinación del grupo de servidores. El punto de conexión permite que los hosts de la red virtual seleccionada accedan al coordinador. Opcionalmente, también puede crear puntos de conexión privados para nodos de trabajo.

**Zona DNS privada**. Una zona DNS privada de Azure resuelve los nombres de host dentro de una red virtual vinculada y dentro de cualquier red virtual emparejada. Los registros de dominio de los nodos de Hiperescala (Citus) se crean en una zona DNS privada seleccionada para su grupo de servidores.  Asegúrese de usar nombres de dominio completos (FQDN) para las cadenas de conexión de PostgreSQL de los nodos.

## <a name="private-link"></a>Private Link

Puede usar [puntos de conexión privados](/azure/private-link/private-endpoint-overview) en los grupos de servidores Hiperescala (Citus) para permitir que los hosts de una red virtual (VNet) accedan de forma segura a los datos mediante un [vínculo privado](/azure/private-link/private-link-overview).

El punto de conexión privado del grupo de servidores usa una dirección IP del espacio de direcciones de la red virtual. El tráfico entre los hosts de la red virtual y los nodos de Hiperescala (Citus) pasa por un vínculo privado de la red troncal de Microsoft, lo que elimina la exposición a la red pública de Internet.

Las aplicaciones de la red virtual se pueden conectar a los nodos de Hiperescala (Citus) mediante el punto de conexión privado sin problemas, con las mismas cadenas de conexión y mecanismos de autorización que usarían en cualquier otro caso.

Puede seleccionar el acceso privado durante la creación del grupo de servidores Hiperescala (Citus) y puede cambiar del acceso público al acceso privado en cualquier momento.

### <a name="using-a-private-dns-zone"></a>Uso de una zona DNS privada

Se aprovisiona automáticamente una nueva zona DNS privada para cada punto de conexión privado, a menos que seleccione una de las zonas DNS privadas creadas previamente por Hiperescala (Citus). Para más información, vea la [información general sobre las zonas DNS privadas](/azure/dns/private-dns-overview).

El servicio Hiperescala (Citus) crea registros DNS, como `c.privatelink.mygroup01.postgres.database.azure.com`, en la zona DNS privada seleccionada para cada nodo con un punto de conexión privado. Cuando se conecta a un nodo de Hiperescala (Citus) desde una máquina virtual de Azure mediante un punto de conexión privado, Azure DNS resuelve el FQDN del nodo en una dirección IP privada.

La configuración de la zona DNS privada y el emparejamiento de red virtual son independientes entre sí. Si desea conectarse a un nodo del grupo de servidores desde un cliente aprovisionado en otra red virtual (de la misma región o de otra región), debe vincular la zona DNS privada con la red virtual. Para más información, vea [Vincular la red virtual](/azure/dns/private-dns-getstarted-portal#link-the-virtual-network).

> [!NOTE]
>
> El servicio también siempre crea registros CNAME públicos, como `c.mygroup01.postgres.database.azure.com`, para cada nodo. Sin embargo, los equipos seleccionados de la red pública de Internet solo pueden conectarse al nombre de host público si el administrador de la base de datos habilita el [acceso público](concepts-hyperscale-firewall-rules.md) al grupo de servidores.

Si usa un servidor DNS personalizado, debe usar un reenviador DNS para resolver el FQDN de los nodos de Hiperescala (Citus). La dirección IP del reenviador debe ser 168.63.129.16. El servidor DNS personalizado debe estar dentro de la red virtual o bien debe poder accederse a él a través de la configuración del servidor DNS de la red virtual. Para más información, vea [Resolución de nombres con su propio servidor DNS](/azure/virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances#name-resolution-that-uses-your-own-dns-server).

### <a name="recommendations"></a>Recomendaciones

Al habilitar el acceso privado para el grupo de servidores Hiperescala (Citus), tenga en cuenta lo siguiente:

* **Tamaño de subred**: al seleccionar el tamaño de subred para el grupo de servidores Hiperescala (Citus), tenga en cuenta las necesidades actuales, como las direcciones IP para el coordinador y todos los nodos de ese grupo de servidores, y las necesidades futuras, como el crecimiento de ese grupo de servidores. Asegúrese de tener suficientes direcciones IP privadas para las necesidades actuales y futuras. Tenga en cuenta que Azure reserva cinco direcciones IP en cada subred.
  Consulte más detalles en [esta sección de preguntas más frecuentes](/azure/virtual-network/virtual-networks-faq#configuration).
* **Zona DNS privada**: el servicio Hiperescala (Citus) mantendrá los registros DNS con direcciones IP privadas. Asegúrese de no eliminar la zona DNS privada que se usa para los grupos de servidores Hiperescala (Citus).

## <a name="limits-and-limitations"></a>Límites y limitaciones

Consulte la página de [límites y limitaciones](concepts-hyperscale-limits.md) de Hiperescala (Citus).

## <a name="next-steps"></a>Pasos siguientes

* Obtenga información sobre cómo [habilitar y administrar el acceso privado](howto-hyperscale-private-access.md) (versión preliminar).
* Siga un [tutorial](tutorial-hyperscale-private-access.md) para ver el acceso privado (versión preliminar) en acción.
* Obtenga información sobre los [puntos de conexión privados](/azure/private-link/private-endpoint-overview).
* Obtenga información sobre las [redes virtuales](/azure/virtual-network/concepts-and-best-practices).
* Obtenga información sobre las [zonas DNS privadas](/azure/dns/private-dns-overview).
