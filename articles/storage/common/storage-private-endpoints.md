---
title: Usar puntos de conexión privados
titleSuffix: Azure Storage
description: Información general de los puntos de conexión privados para el acceso seguro a las cuentas de almacenamiento de redes virtuales.
services: storage
author: normesta
ms.service: storage
ms.topic: conceptual
ms.date: 03/16/2021
ms.author: normesta
ms.reviewer: santoshc
ms.subservice: common
ms.openlocfilehash: a52460db452d519c51fb7a1b191766b21da67f88
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/24/2021
ms.locfileid: "128592287"
---
# <a name="use-private-endpoints-for-azure-storage"></a>Uso de puntos de conexión privados para Azure Storage

Puede usar [puntos de conexión privados](../../private-link/private-endpoint-overview.md) en sus cuentas de Azure Storage para que los clientes de una red virtual (VNet) puedan acceder de forma segura a los datos a través de una instancias de [Private Link](../../private-link/private-link-overview.md). El punto de conexión privado usa una dirección IP del espacio de direcciones de la red virtual para el servicio de la cuenta de almacenamiento. El tráfico de red entre los clientes de la red virtual y la cuenta de almacenamiento atraviesa la red virtual y un vínculo privado de la red troncal de Microsoft, lo que elimina la exposición a la red pública de Internet.

El uso de puntos de conexión privados en una cuenta de almacenamiento permite:

- Proteger la cuenta de almacenamiento mediante la configuración del firewall de almacenamiento para que bloquee todas las conexiones del punto de conexión público del servicio de almacenamiento.
- Aumentar la seguridad de la red virtual (VNet), ya que permite bloquear la filtración de datos de la red virtual.
- Conectarse de forma segura a las cuentas de almacenamiento desde las redes locales que se conectan a la red virtual mediante [VPN](../../vpn-gateway/vpn-gateway-about-vpngateways.md) o [ExpressRoute](../../expressroute/expressroute-locations.md) con emparejamiento privado.

## <a name="conceptual-overview"></a>Información general conceptual

![Información general de los puntos de conexión privados para Azure Storage](media/storage-private-endpoints/storage-private-endpoints-overview.jpg)

Un punto de conexión privado es una interfaz de red especial para un servicio de Azure de una [red virtual](../../virtual-network/virtual-networks-overview.md). Cuando se crea un punto de conexión privado para una cuenta de almacenamiento, este proporciona conectividad segura entre los clientes de la red virtual y el almacenamiento. Al punto de conexión privado se le asigna una dirección IP del intervalo de direcciones IP de la red virtual. La conexión entre el punto de conexión privado y el servicio de almacenamiento usa un vínculo privado seguro.

Las aplicaciones de la red virtual se pueden conectar al servicio de almacenamiento a través del punto de conexión privado sin problemas, **ya que se usan las mismas cadenas de conexión y mecanismos de autorización que se usarían en cualquier otro caso**. Los puntos de conexión privados se pueden usar con todos los protocolos que admita la cuenta de almacenamiento, incluidos REST y SMB.

Los puntos de conexión privados se pueden crear en subredes que usan [puntos de conexión de servicio](../../virtual-network/virtual-network-service-endpoints-overview.md). Por lo tanto, los clientes de una subred pueden conectarse a una cuenta de almacenamiento mediante un punto de conexión privado, al mismo tiempo que usan puntos de conexión de servicio para acceder a otras.

Cuando se crea un punto de conexión privado para un servicio de almacenamiento en la red virtual, se envía una solicitud de consentimiento para su aprobación al propietario de la cuenta de almacenamiento. Si el usuario que solicita la creación del punto de conexión privado también es propietario de la cuenta de almacenamiento, esta solicitud de consentimiento se aprueba automáticamente.

Los propietarios de cuentas de almacenamiento pueden administrar las solicitudes de consentimiento y los puntos de conexión privados desde la pestaña "*Puntos de conexión privados*" de la cuenta de almacenamiento en [Azure Portal](https://portal.azure.com).

> [!TIP]
> Si desea restringir el acceso a una cuenta de almacenamiento de modo que solo se pueda realizar mediante el punto de conexión privado, configure el firewall de almacenamiento para denegar o controlar todo acceso que se realice mediante el punto de conexión público.

Puede proteger la cuenta de almacenamiento para que acepte solo las conexiones que provengan de la red virtual. Para ello, debe [configurar el firewall de almacenamiento](storage-network-security.md#change-the-default-network-access-rule) para que, de forma predeterminada, deniegue el acceso a través de su punto de conexión. Para permitir el tráfico de una red virtual que tenga un punto de conexión privado no se necesita ninguna regla de firewall, ya que el firewall de almacenamiento solo controla el acceso a través del punto de conexión privado. En su lugar, los puntos de conexión privados usan el flujo de consentimiento para conceder el acceso de las subredes al servicio de almacenamiento.

> [!NOTE]
> Cuando se copian blobs entre cuentas de almacenamiento, el cliente debe tener acceso a redes en ambas cuentas. Por consiguiente, si elige usar un vínculo privado para una sola cuenta (ya sea el origen o el destino), asegúrese de que el cliente tiene acceso a redes en la otra cuenta. Para obtener información sobre otras formas de configurar el acceso a redes, consulte [Configuración de redes virtuales y firewalls de Azure Storage](storage-network-security.md?toc=/azure/storage/blobs/toc.json).

<a id="private-endpoints-for-azure-storage"></a>

## <a name="creating-a-private-endpoint"></a>Creación de un punto de conexión privado

Para crear un punto de conexión privado mediante Azure Portal, consulte el artículo sobre la [conexión privada a una cuenta de almacenamiento desde la experiencia de cuenta de almacenamiento en Azure Portal](../../private-link/tutorial-private-endpoint-storage-portal.md).

Para crear un punto de conexión privado mediante PowerShell o la CLI de Azure, consulte uno de estos artículos. Ambos presentan una aplicación web de Azure como servicio de destino, pero los pasos para crear un vínculo privado son los mismos que para una cuenta de Azure Storage.

- [Creación de un punto de conexión privado mediante la CLI de Azure](../../private-link/create-private-endpoint-cli.md)

- [Creación de un punto de conexión privado mediante Azure PowerShell](../../private-link/create-private-endpoint-powershell.md)

Al crear un punto de conexión privado, debe especificar la cuenta de almacenamiento y el servicio de almacenamiento al que se conecta.

Necesita un punto de conexión privado independiente para cada recurso de almacenamiento al que necesite acceder, concretamente, [Blobs](../blobs/storage-blobs-overview.md), [Data Lake Storage Gen2](../blobs/data-lake-storage-introduction.md), [Files](../files/storage-files-introduction.md), [Queues](../queues/storage-queues-introduction.md), [Tables](../tables/table-storage-overview.md) o [Static Websites](../blobs/storage-blob-static-website.md). En el punto de conexión privado, estos servicios de almacenamiento se definen como el **recurso secundario de destino** de la cuenta de almacenamiento asociada.

Si crea un punto de conexión privado para el recurso de almacenamiento de Data Lake Storage Gen2, también debe crear uno para el recurso de Blob Storage. Esto se debe a que las operaciones que tienen como destino el punto de conexión de Data Lake Storage Gen2 se pueden redirigir al punto de conexión del blob. Al crear un punto de conexión privado para ambos recursos, se asegura de que las operaciones se pueden completar correctamente.

> [!TIP]
> Cree un punto de conexión privado independiente para la instancia secundaria del servicio de almacenamiento para mejorar el rendimiento de lectura en las cuentas de almacenamiento con redundancia geográfica con acceso de lectura.
> Asegúrese de crear una cuenta de almacenamiento de uso general v2 (Estándar o Premium).

Para obtener acceso de lectura a la región secundaria con una cuenta de almacenamiento configurada para el almacenamiento con redundancia geográfica se necesitan puntos de conexión privados para las instancias principal y secundaria del servicio. No es preciso crear un punto de conexión privado para la instancia secundaria para la **conmutación por error**. El punto de conexión privado se conectará automáticamente a la nueva instancia principal después de la conmutación por error. Para más información sobre las opciones de redundancia de almacenamiento, consulte [Redundancia de Azure Storage](storage-redundancy.md).

<a id="connecting-to-private-endpoints"></a>

## <a name="connecting-to-a-private-endpoint"></a>Conexión a un punto de conexión privado

Los clientes de una red virtual que usan el punto de conexión privado deben usar la misma cadena de conexión para la cuenta de almacenamiento que aquellos clientes que se conectan mediante el punto de conexión público. Confiamos en la resolución del DNS para enrutar automáticamente las conexiones desde la red virtual a la cuenta de almacenamiento a través de un vínculo privado.

> [!IMPORTANT]
> Use la misma cadena de conexión para conectarse a la cuenta de almacenamiento mediante puntos de conexión privados, tal como la usaría en otras ocasiones. No se conecte a la cuenta de almacenamiento mediante su dirección URL de subdominio `privatelink`.

De forma predeterminada, se crea una [zona DNS privada](../../dns/private-dns-overview.md) conectada a la red virtual con las actualizaciones necesarias para los puntos de conexión privados. Sin embargo, si usa su propio servidor DNS, puede que tenga que realizar cambios adicionales en la configuración de DNS. En la sección [Cambios de DNS](#dns-changes-for-private-endpoints) que aparece a continuación se describen las actualizaciones necesarias para los puntos de conexión privados.

## <a name="dns-changes-for-private-endpoints"></a>Cambios de DNS en puntos de conexión privados

Al crear un punto de conexión privado, el registro del recurso CNAME de DNS de la cuenta de almacenamiento se actualiza a un alias de un subdominio con el prefijo `privatelink`. De forma predeterminada, también se crea una [zona DNS privada](../../dns/private-dns-overview.md), que se corresponde con el subdominio `privatelink`, con los registros de recursos D de DNS para los puntos de conexión privados.

Cuando se resuelve la dirección URL del punto de conexión de almacenamiento desde fuera de la red virtual con el punto de conexión privado, se resuelve en el punto de conexión público del servicio de almacenamiento. Cuando se resuelve desde la red virtual que hospeda el punto de conexión privado, la dirección URL del punto de conexión de almacenamiento se resuelve en la dirección IP del punto de conexión privado.

En el ejemplo anterior, los registros de recursos DNS de la cuenta de almacenamiento "StorageAccountA", cuando se resuelven desde fuera de la red virtual que hospeda el punto de conexión privado, serán:

| Nombre                                                  | Tipo  | Value                                                 |
| :---------------------------------------------------- | :---: | :---------------------------------------------------- |
| `StorageAccountA.blob.core.windows.net`             | CNAME | `StorageAccountA.privatelink.blob.core.windows.net` |
| `StorageAccountA.privatelink.blob.core.windows.net` | CNAME | \<storage service public endpoint\>                   |
| \<storage service public endpoint\>                   | Un     | \<storage service public IP address\>                 |

Como ya se ha mencionado, puede denegar o controlar el acceso de los clientes de fuera de la red virtual a través del punto de conexión público mediante el firewall de Storage.

Los registros de recursos DNS de StorageAccountA, cuando los resuelve un cliente en la red virtual que hospeda el punto de conexión privado, serán:

| Nombre  | Tipo | Value |
| :--- | :---: | :--- |
| `StorageAccountA.blob.core.windows.net` | CNAME | `StorageAccountA.privatelink.blob.core.windows.net` |
| `StorageAccountA.privatelink.blob.core.windows.net` | A | `10.1.1.5` |

Este enfoque permite el acceso a la cuenta de almacenamiento **mediante la misma cadena de conexión** para los clientes de la red virtual que hospeda los puntos de conexión privados y los clientes que están fuera de esta.

Si va a usar un servidor DNS personalizado en la red, los clientes deben ser capaces de resolver el FQDN del punto de conexión de la cuenta de almacenamiento en la dirección IP del punto de conexión privado. Debe configurar el servidor DNS para delegar el subdominio del vínculo privado en la zona DNS privada de la red virtual, o bien configurar los registros D para `StorageAccountA.privatelink.blob.core.windows.net` con la dirección IP del punto de conexión privado.

> [!TIP]
> Cuando use un servidor DNS personalizado o local, debe configurarlo para resolver el nombre de la cuenta de almacenamiento del subdominio `privatelink` en la dirección IP del punto de conexión privado. Para ello, puede delegar el subdominio `privatelink` en la zona DNS privada de la red virtual, o bien configurar la zona DNS en el servidor DNS y agregar los registros A de DNS.

Los nombres de zona DNS recomendados para los puntos de conexión privados para los servicios de almacenamiento y los recursos secundarios de destino del punto de conexión asociados son:

| Servicio de Storage        | Recurso secundario de destino | Nombre de zona                            |
| :--------------------- | :------------------ | :----------------------------------- |
| Blob service           | blob                | `privatelink.blob.core.windows.net`  |
| Data Lake Storage Gen2 | DFS                 | `privatelink.dfs.core.windows.net`   |
| File service           | archivo                | `privatelink.file.core.windows.net`  |
| Queue service          | cola               | `privatelink.queue.core.windows.net` |
| Table service          | mesa               | `privatelink.table.core.windows.net` |
| Static Websites        | web                 | `privatelink.web.core.windows.net`   |

Para más información sobre cómo configurar su propio servidor DNS para que admita puntos de conexión privados, consulte los siguientes artículos:

- [Resolución de nombres de recursos en redes virtuales de Azure](../../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-that-uses-your-own-dns-server)
- [Configuración de DNS para puntos de conexión privados](../../private-link/private-endpoint-overview.md#dns-configuration)

## <a name="pricing"></a>Precios

Para más información sobre los precios, consulte [Precios de Azure Private Link](https://azure.microsoft.com/pricing/details/private-link).

## <a name="known-issues"></a>Problemas conocidos

Tenga en cuenta los siguientes problemas conocidos relacionados con los puntos de conexión privados para Azure Storage.

### <a name="storage-access-constraints-for-clients-in-vnets-with-private-endpoints"></a>Restricciones de acceso al almacenamiento para los clientes de redes virtuales con puntos de conexión privados

Los clientes de redes virtuales con puntos de conexión privados existentes se enfrentan a ciertas restricciones al acceder a otras cuentas de almacenamiento que tienen puntos de conexión privados. Por ejemplo, imagine que la red virtual N1 tiene un punto de conexión privado para una cuenta de almacenamiento A1 con el fin de almacenar blobs. Si la cuenta de almacenamiento A2 tiene un punto de conexión privado en una red virtual N2 para el almacenamiento de blobs, los clientes de la red virtual N1 también deben acceder a al almacenamiento de blobs de la cuenta A2 mediante un punto de conexión privado. Si la cuenta de almacenamiento A2 no tiene puntos de conexión privados para el almacenamiento de blobs, los clientes de la red virtual N1 pueden acceder al almacenamiento de blobs sin ningún punto de conexión privado.

Esta restricción es el resultado de los cambios de DNS realizados cuando la cuenta A2 crea un punto de conexión privado.

### <a name="network-security-group-rules-for-subnets-with-private-endpoints"></a>Reglas de grupo de seguridad de red para subredes con puntos de conexión privados

Actualmente, no se pueden configurar reglas de [grupo de seguridad de red](../../virtual-network/network-security-groups-overview.md) (NSG) ni rutas definidas por el usuario para puntos de conexión privados. Las reglas de NSG que se aplican a la subred que hospeda el punto de conexión privado no se aplican al punto de conexión privado. Solo se aplican a otros puntos de conexión (por ejemplo: controladores de interfaz de red). Una solución alternativa limitada para este problema es implementar las reglas de acceso para los puntos de conexión privados en las subredes de origen, aunque este enfoque puede requerir mayor sobrecarga de administración.

### <a name="copying-blobs-between-storage-accounts"></a>Copia de blobs entre cuentas de almacenamiento

Puede copiar blobs entre cuentas de almacenamiento mediante puntos de conexión privados solo si usa la API REST de Azure o las herramientas que usan la API REST. Entre estas herramientas se incluyen AzCopy, explorador de Storage, Azure PowerShell, CLI de Azure y los SDK de Azure Blob Storage.

Solo se admiten los puntos de conexión privados que tienen como destino el recurso de almacenamiento de blobs. Todavía no se admiten los puntos de conexión privados que tienen como destino Data Lake Storage Gen2 o el recurso File. Además, todavía no se admite la copia entre cuentas de almacenamiento mediante el protocolo Network File System (NFS).

## <a name="next-steps"></a>Pasos siguientes

- [Configuración de redes virtuales y firewalls de Azure Storage](storage-network-security.md)
- [Recomendaciones de seguridad para Blob Storage](../blobs/security-recommendations.md)
