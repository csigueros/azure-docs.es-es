---
title: Configuración de la resolución de nombres DNS para puntos de conexión privados
description: En este artículo se describe la información general sobre cómo puede usar un punto de conexión privado para la cuenta de Purview.
author: viseshag
ms.author: viseshag
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: how-to
ms.date: 08/18/2021
ms.openlocfilehash: b6d0b4cb58562540cd3b2024631bb9cec2d320c9
ms.sourcegitcommit: 0ede6bcb140fe805daa75d4b5bdd2c0ee040ef4d
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/20/2021
ms.locfileid: "122607052"
---
# <a name="configure-and-verify-dns-name-resolution-for-azure-purview-private-endpoints"></a>Configuración y comprobación de la resolución de nombres DNS para puntos de conexión privados de Azure Purview

## <a name="conceptual-overview"></a>Información general conceptual
La resolución de nombres precisa es un requisito fundamental al configurar puntos de conexión privados para las cuentas de Azure Purview. 

Es posible que necesite habilitar la resolución de nombres interna en la configuración de DNS para resolver las direcciones IP del punto de conexión privado en el nombre de dominio completo (FQDN) desde los orígenes de datos y la máquina de administración a la cuenta de Azure Purview y al entorno de ejecución de integración auto-hospedado, en función de los escenarios que implemente.

En el ejemplo siguiente se muestra la resolución de nombres DNS de Azure Purview desde fuera de la red virtual o cuando no hay un punto de conexión privado de Azure configurado.

   :::image type="content" source="media/catalog-private-link/purview-name-resolution-external.png" alt-text="Captura de pantalla que muestra la resolución de nombres de Azure Purview desde fuera de CorpNet.":::

En el ejemplo siguiente se muestra la resolución de nombres DNS de Azure Purview desde dentro de la red virtual.

   :::image type="content" source="media/catalog-private-link/purview-name-resolution-private-link.png" alt-text="Captura de pantalla que muestra la resolución de nombres de Purview desde dentro de CorpNet.":::

## <a name="deployment-options"></a>Opciones de implementación 

Use cualquiera de las siguientes opciones para enviar la resolución de nombres interna al usar puntos de conexión privados para la cuenta de Azure Purview:

-  [Implemente nuevas zonas DNS privadas de Azure](#option-1---deploy-new-azure-private-dns-zones) en la parte del entorno de Azure de la implementación de puntos de conexión privados. (Opción predeterminada)
- [Use las zonas DNS privadas de Azure existentes](#option-2---use-existing-azure-private-dns-zones). Utilice esta opción si usa un punto de conexión privado en un modelo tipo hub-and-spoke de una suscripción diferente o incluso dentro de la misma suscripción. 
- [Use sus propios servidores DNS](#option-3---use-your-own-dns-servers) si no usa reenviadores DNS y, en su lugar, administra registros D directamente en los servidores DNS locales.

## <a name="option-1---deploy-new-azure-private-dns-zones"></a>Opción 1: Implementación de nuevas zonas DNS privadas de Azure  

Para habilitar la resolución de nombres interna, puede implementar las zonas DNS privadas de Azure necesarias dentro de la suscripción de Azure donde se implementa la cuenta de Azure Purview. 

Al crear puntos de conexión privados de portal y cuenta, los registros de recursos CNAME de DNS para Azure Purview se actualizan automáticamente a un alias de un subdominio con el prefijo `privatelink`. De manera predeterminada, se crea también una [zona DNS privada](../dns/private-dns-overview.md) que corresponde al subdominio `privatelink` para Azure Purview, como privatelink.purview.azure.com, que contiene los registros A de DNS del recurso para los puntos de conexión privados. Si habilita los puntos de conexión privados de ingesta, se requieren zonas DNS adicionales para los recursos administrados. 

En la tabla siguiente se muestra un ejemplo de las zonas DNS privadas de Azure y los registros D de DNS que se implementan como parte de la configuración del punto de conexión privado para una cuenta de Azure Purview si habilita la _integración de DNS privado_ durante la implementación: 

Punto de conexión privado  |Punto de conexión privado asociado a  |Zona DNS (nueva)  |Registro D (ejemplo) |
|---------|---------|---------|---------|
|Cuenta     |Azure Purview         |`privatelink.purview.azure.com`         |PurviewA         |
|Portal     |Cuenta de Azure Purview          |`privatelink.purview.azure.com`        |Web         |
|Ingesta     |Cuenta de almacenamiento administrada de Purview: blob          |`privatelink.blob.core.windows.net`          |scaneastusabcd1234         |
|Ingesta   |Cuenta de almacenamiento administrada de Purview: cola         |`privatelink.queue.core.windows.net`         |scaneastusabcd1234         |
|Ingesta     |Cuenta de almacenamiento administrada de Purview: centro de eventos         |`privatelink.servicebus.windows.net`         |atlas-12345678-1234-1234-abcd-123456789abc         |

Cuando se resuelve la dirección URL del punto de conexión de Azure Purview desde fuera de la red virtual con el punto de conexión privado, se resuelve en el punto de conexión público de Azure Purview. Cuando se resuelve desde la red virtual que hospeda el punto de conexión privado, la dirección URL del punto de conexión de Azure Purview se resuelve en la dirección IP del punto de conexión privado.

Por ejemplo, si un nombre de cuenta de Azure Purview es "PurviewA", cuando se resuelve desde fuera de la red virtual que hospeda el punto de conexión privado, será:

| Nombre | Tipo | Value |
| ---------- | -------- | --------------- |
| `PurviewA.purview.azure.com` | CNAME | `PurviewA.privatelink.purview.azure.com` |
| `PurviewA.privatelink.purview.azure.com` | CNAME | \<Purview public endpoint\> |
| \<Purview public endpoint\> | A | \<Purview public IP address\> |
| `Web.purview.azure.com` | CNAME | \<Purview public endpoint\> |

Los registros de recursos DNS para PurviewA, cuando se resuelvan en la red virtual que hospeda el punto de conexión privado, serán:

| Nombre | Tipo | Value |
| ---------- | -------- | --------------- |
| `PurviewA.purview.azure.com` | CNAME | `PurviewA.privatelink.purview.azure.com` |
| `PurviewA.privatelink.purview.azure.com` | A | \<private endpoint IP address\> |
| `Web.purview.azure.com` | CNAME | \<private endpoint IP address\> |

## <a name="option-2---use-existing-azure-private-dns-zones"></a>Opción 2: uso de las zonas DNS privadas de Azure
Durante la implementación de los puntos de conexión privados de Azure Purview, puede elegir _integración de DNS privado_ mediante zonas DNS privadas de Azure existentes. Este es el caso común de las organizaciones en las que se usa un punto de conexión privado para otros servicios de Azure. En este caso, durante la implementación de puntos de conexión privados, asegúrese de seleccionar las zonas DNS existentes en lugar de crear otras nuevas. 

Esto también se aplica si su organización usa una suscripción central o de centro para todas las instancias de zonas DNS privadas de Azure.

En la lista siguiente se muestran las zonas DNS de Azure y los registros D necesarios para los puntos de conexión privados de Purview:

> [!NOTE]
> Actualice todos los nombres con `PurviewA`,`scaneastusabcd1234` y `atlas-12345678-1234-1234-abcd-123456789abc` con los nombres de recursos de Azure correspondientes en el entorno. Por ejemplo, en lugar de `scaneastusabcd1234`, use el nombre de la cuenta de almacenamiento administrada de Azure Purview.

Punto de conexión privado  |Punto de conexión privado asociado a  |Zona DNS (existente)  |Registro D (ejemplo) |
|---------|---------|---------|---------|
|Cuenta     |Azure Purview         |`privatelink.purview.azure.com`         |PurviewA         |
|Portal     |Cuenta de Azure Purview          |`privatelink.purview.azure.com`        |Web         |
|Ingesta     |Cuenta de almacenamiento administrada de Purview: blob          |`privatelink.blob.core.windows.net`          |scaneastusabcd1234         |
|Ingesta   |Cuenta de almacenamiento administrada de Purview: cola         |`privatelink.queue.core.windows.net`         |scaneastusabcd1234         |
|Ingesta     |Cuenta de almacenamiento administrada de Purview: centro de eventos         |`privatelink.servicebus.windows.net`         |atlas-12345678-1234-1234-abcd-123456789abc         |

Para más información, consulte [Cargas de trabajo de red virtual sin un servidor DNS personalizado](../private-link/private-endpoint-dns.md#virtual-network-workloads-without-custom-dns-server) y [Cargas de trabajo locales que utilizan un reenviador DNS](../private-link/private-endpoint-dns.md#on-premises-workloads-using-a-dns-forwarder) en [Configuración de DNS para puntos de conexión privados de Azure](../private-link/private-endpoint-dns.md).

   :::image type="content" source="media/catalog-private-link/purview-name-resolution-diagram.png" alt-text="Diagrama que muestra la resolución de nombres de Azure Purview"lightbox="media/catalog-private-link/purview-name-resolution-diagram.png":::

Si va a usar un servidor DNS personalizado en la red, los clientes deben ser capaces de resolver el FQDN del punto de conexión de Azure Purview en la dirección IP del punto de conexión privado. Configure el servidor DNS para delegar el subdominio de Private Link en la zona DNS privada de la red virtual. O bien, configure los registros D para `PurviewA.privatelink.purview.azure.com` con la dirección IP del punto de conexión privado.
Una vez completada la implementación del punto de conexión privado, asegúrese de que haya un [vínculo de red virtual](../dns/private-dns-virtual-network-links.md) para la resolución de nombres en la zona DNS privada de Azure correspondiente a la red virtual de Azure donde se implementó el punto de conexión privado. 

Para más información, consulte [Configuración de DNS para puntos de conexión privados de Azure](../private-link/private-endpoint-dns.md).

## <a name="option-3---use-your-own-dns-servers"></a>Opción 3: uso de sus propios servidores DNS

Si no usa reenviadores DNS y, en su lugar, administra registros D directamente en los servidores DNS locales para resolver los puntos de conexión a través de sus direcciones IP privadas, es posible que tenga que crear los siguientes registros D en los servidores DNS.

> [!NOTE]
> Actualice todos los nombres con `PurviewA`,`scaneastusabcd1234` y `atlas-12345678-1234-1234-abcd-123456789abc` con los nombres de recursos de Azure correspondientes en el entorno. Por ejemplo, en lugar de `scaneastusabcd1234`, use el nombre de la cuenta de almacenamiento administrada de Azure Purview.

| Nombre | Tipo | Value |
| ---------- | -------- | --------------- |
| `web.purview.azure.com` | A | \<portal private endpoint IP address of Azure Purview> |
| `scaneastusabcd1234.blob.core.windows.net` | A | \<blob-ingestion private endpoint IP address of Azure Purview> |
| `scaneastusabcd1234.queue.core.windows.net` | A | \<queue-ingestion private endpoint IP address of Azure Purview> |
| `atlas-12345678-1234-1234-abcd-123456789abc.servicebus.windows.net`| A | \<namespace-ingestion private endpoint IP address of Azure Purview> |
| `PurviewA.Purview.azure.com` | A | \<account private endpoint IP address of Azure Purview> |
| `PurviewA.scan.Purview.azure.com` | A | \<account private endpoint IP address of Azure Purview> |
| `PurviewA.catalog.Purview.azure.com` | A | \<account private endpoint IP address of Azure Purview\> |
| `PurviewA.proxy.purview.azure.com` | A | \<account private endpoint IP address of Azure Purview\> |
| `PurviewA.guardian.purview.azure.com` | A | \<account private endpoint IP address of Azure Purview\> |
| `PurviewA.web.purview.azure.com` | A | \<portal private endpoint IP address of Azure Purview\> |
| `PurviewA.manifest.prod.ext.web.purview.azure.com` | A | \<portal private endpoint IP address of Azure Purview\> |
| `PurviewA.cdn.prod.ext.web.purview.azure.com` | A | \<portal private endpoint IP address of Azure Purview\> |
| `PurviewA.hub.prod.ext.web.purview.azure.com` | A | \<portal private endpoint IP address of Azure Purview\> |
| `PurviewA.catalog.prod.ext.web.purview.azure.com` | A | \<portal private endpoint IP address of Azure Purview\> |
| `PurviewA.cseo.prod.ext.web.purview.azure.com` | A | \<portal private endpoint IP address of Azure Purview\> |
| `PurviewA.datascan.prod.ext.web.purview.azure.com` | A | \<portal private endpoint IP address of Azure Purview\> |
| `PurviewA.datashare.prod.ext.web.purview.azure.com` | A | \<portal private endpoint IP address of Azure Purview\> |
| `PurviewA.datasource.prod.ext.web.purview.azure.com` | A | \<portal private endpoint IP address of Azure Purview\> |
| `PurviewA.policy.prod.ext.web.purview.azure.com` | A | \<portal private endpoint IP address of Azure Purview\> |
| `PurviewA.sensitivity.prod.ext.web.purview.azure.com` | A | \<portal private endpoint IP address of Azure Purview\> |

## <a name="verify-and-dns-test-name-resolution-and-connectivity"></a>Comprobación y conectividad de resolución de nombres de prueba de DNS 

1. Si usa zonas DNS privadas de Azure, asegúrese de que se crean las siguientes zonas DNS y los registros D correspondientes en la suscripción de Azure:

   |Punto de conexión privado  |Punto de conexión privado asociado a  |Zona DNS  |Registro D (ejemplo) |
   |---------|---------|---------|---------|
   |Cuenta     |Azure Purview         |`privatelink.purview.azure.com`         |PurviewA         |
   |Portal     |Cuenta de Azure Purview          |`privatelink.purview.azure.com`        |Web         |
   |Ingesta     |Cuenta de almacenamiento administrada de Purview: blob          |`privatelink.blob.core.windows.net`          |scaneastusabcd1234         |
   |Ingesta   |Cuenta de almacenamiento administrada de Purview: cola         |`privatelink.queue.core.windows.net`         |scaneastusabcd1234         |
   |Ingesta     |Cuenta de almacenamiento administrada de Purview: centro de eventos         |`privatelink.servicebus.windows.net`         |atlas-12345678-1234-1234-abcd-123456789abc         |

2. Cree [vínculos de red virtual](../dns/private-dns-virtual-network-links.md) en zonas DNS privadas de Azure para las redes virtuales de Azure para permitir la resolución de nombres interna.
   
3. Desde el equipo de administración y la máquina virtual del entorno de ejecución de integración autohospedado, pruebe la resolución de nombres y la conectividad de red a la cuenta de Azure Purview mediante herramientas como Nslookup.exe y PowerShell.

Para probar la resolución de nombres, debe resolver los siguientes FQDN a través de sus direcciones IP privadas: (en lugar de PurviewA, scaneastusabcd1234 o atlas-12345678-1234-1234-abcd-123456789abc, use el nombre de host asociado con el nombre de cuenta de Purview y los nombres de recursos administrados).

- `PurviewA.purview.azure.com`
- `web.purview.azure.com`
- `scaneastusabcd1234.blob.core.windows.net`
- `scaneastusabcd1234.queue.core.windows.net`
- `atlas-12345678-1234-1234-abcd-123456789abc.servicebus.windows.net`

Para probar la conectividad de red, desde una máquina virtual del entorno de ejecución de integración autohospedado, puede iniciar la consola de PowerShell y probar la conectividad mediante `Test-NetConnection`. Debe resolver cada punto de conexión por su punto de conexión privado y obtener TcpTestSucceeded como True. (En lugar de PurviewA, scaneastusabcd1234 o atlas-12345678-1234-1234-abcd-123456789abc, use el nombre de host asociado con el nombre de cuenta de Purview y los nombres de recursos administrados).

- `Test-NetConnection -ComputerName PurviewA.purview.azure.com -port 443`
- `Test-NetConnection -ComputerName web.purview.azure.com -port 443`
- `Test-NetConnection -ComputerName scaneastusabcd1234.blob.core.windows.net -port 443`
- `Test-NetConnection -ComputerName scaneastusabcd1234.queue.core.windows.net -port 443`
- `Test-NetConnection -ComputerName atlas-12345678-1234-1234-abcd-123456789abc.servicebus.windows.net -port 443` 

## <a name="next-steps"></a>Pasos siguientes

- [Solución de problemas de configuración de punto de conexión privado de la cuenta de Azure Purview](catalog-private-link-troubleshoot.md)
- [Administración de orígenes de datos en Azure Purview](./manage-data-sources.md)
