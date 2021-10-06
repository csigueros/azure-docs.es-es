---
title: Preferencias de enrutamiento no medido en Azure
description: Obtenga información sobre cómo configurar las preferencias de enrutamiento para los datos de salida de los recursos hacia el proveedor de CDN.
services: virtual-network
documentationcenter: na
author: asudbring
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/22/2021
ms.author: allensu
ms.openlocfilehash: 5d459c4a61518aae349184773561448148877afc
ms.sourcegitcommit: 87de14fe9fdee75ea64f30ebb516cf7edad0cf87
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/01/2021
ms.locfileid: "129367855"
---
# <a name="what-is-routing-preference-unmetered"></a>¿Qué son las preferencias de enrutamiento no medido?

La preferencia de enrutamiento no medido está disponible para los proveedores de Content Delivery Network (CDN) con clientes que hospedan su contenido de origen en Azure. El servicio permite a los proveedores de CDN establecer una conexión de emparejamiento directo con los enrutadores perimetrales de la red global de Microsoft en varias ubicaciones.

![Preferencias de enrutamiento no medido](./media/routing-preference-unmetered/unmetered.png)

La salida del tráfico de red desde el origen en Azure destinado al proveedor de CDN se beneficia de la conectividad directa.
* La factura de transferencia de datos para el tráfico de salida de los recursos de Azure que se enrutan mediante estos vínculos directos es gratuita.
* La conexión directa entre el proveedor de CDN y el origen en Azure proporciona un rendimiento óptimo, ya que no hay saltos entre ellos. Esto beneficia a la carga de trabajo de la red CDN, que recupera datos del origen con frecuencia.

## <a name="configuring-routing-preference-unmetered"></a>Configuración de las preferencias de enrutamiento no medido

Para aprovechar las ventajas de las preferencias de enrutamiento no medido, los proveedores de CDN deben formar parte de este programa. Si el proveedor de CDN no forma parte del programa, póngase en contacto con el proveedor de CDN.

A continuación, configure las preferencias de enrutamiento para los recursos y establezca el tipo de preferencias de enrutamiento en **Internet**. Puede configurar las preferencias de enrutamiento al crear una dirección IP pública y, a continuación, asociar la IP pública a recursos como máquinas virtuales, equilibradores de carga accesibles desde Internet y mucho más. [Obtenga información sobre cómo configurar la preferencia de enrutamiento de una dirección IP pública mediante Azure Portal.](../../virtual-network/routing-preference-portal.md)

También puede habilitar las preferencias de enrutamiento para la cuenta de almacenamiento y publicar un segundo punto de conexión, que el proveedor de CDN debe usar para capturar datos del origen de almacenamiento. Por ejemplo, la publicación de un punto de conexión específico de la ruta de Internet para la cuenta de almacenamiento *StorageAccountA* publicará el segundo punto de conexión de los servicios de almacenamiento, como se muestra a continuación:

![Preferencias de enrutamiento para las cuentas de almacenamiento](./media/routing-preference-unmetered/storage-endpoints.png)


## <a name="next-steps"></a>Pasos siguientes

* [Configuración de la preferencia de enrutamiento de una VM mediante Azure PowerShell](../../virtual-network/configure-routing-preference-virtual-machine-powershell.md)
* [Configuración de la preferencia de enrutamiento de una VM mediante la CLI de Azure](../../virtual-network/configure-routing-preference-virtual-machine-cli.md)
* [Configuración de las preferencias de enrutamiento para la cuenta de almacenamiento](../../storage/common/network-routing-preference.md)