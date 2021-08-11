---
title: 'Protocolos de Azure Files disponibles: NFS y SMB'
description: Antes de crear un recurso compartido de archivos de Azure, conozca los protocolos disponibles, como Bloque de mensajes del servidor (SMB) y Sistema de archivos de red (NFS).
author: roygara
ms.service: storage
ms.topic: conceptual
ms.date: 12/04/2020
ms.author: rogarana
ms.subservice: files
ms.custom: references_regions
ms.openlocfilehash: 792f869aff0e7a91eeacd23e52c795d2c1243c0c
ms.sourcegitcommit: 17345cc21e7b14e3e31cbf920f191875bf3c5914
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 05/19/2021
ms.locfileid: "110062387"
---
# <a name="azure-file-share-protocols"></a>Protocolos de recurso compartido de archivos de Azure

Azure Files ofrece dos protocolos para conectar y montar los recursos compartidos de archivos de Azure. El [protocolo Bloque de mensajes del servidor (SMB)](/windows/win32/fileio/microsoft-smb-protocol-and-cifs-protocol-overview) y el [protocolo Network File System (NFS)](https://en.wikipedia.org/wiki/Network_File_System) (versión preliminar). Actualmente, Azure Files no admite el acceso multiprotocolo, por lo que un recurso compartido solo puede ser un recurso compartido NFS o SMB. Por eso se recomienda determinar qué protocolo se ajusta mejor a sus necesidades antes de crear recursos compartido de archivos.

## <a name="differences-at-a-glance"></a>Diferencias de un vistazo

|Característica  |NFS (versión preliminar)  |SMB  |
|---------|---------|---------|
|Protocolos de acceso     |NFS 4.1         |SMB 3.1.1, SMB 3.0, SMB 2.1         |
|SO recomendado     |Versión posterior a la 4.3 del kernel de Linux         |Posterior Windows 2008 R2, versión posterior a la 4.11+         |
|[Niveles disponibles](storage-files-planning.md#storage-tiers)     |Premium Storage         |Premium Storage, optimizado para transacciones, acceso frecuente, acceso esporádico         |
|Modelo de facturación         |[Pago por capacidad aprovisionada](./understanding-billing.md#provisioned-model)         |[Pago por capacidad aprovisionada para el nivel Prémium](./understanding-billing.md#provisioned-model) y [pago por uso para el nivel estándar](./understanding-billing.md#pay-as-you-go-model)         |
|[Redundancia](storage-files-planning.md#redundancy)     |LRS, ZRS         |LRS, ZRS, GRS         |
|Autenticación     |Solo autenticación basada en host        |Autenticación basada en identidad, autenticación basada en usuario         |
|Permisos     |Permisos de estilo UNIX         |Permisos de estilo NTFS         |
|Semántica del sistema de archivos     |Compatible con POSIX         |No compatible con POSIX         |
|Distinción entre mayúsculas y minúsculas     |Distingue mayúsculas de minúsculas         |No distingue mayúsculas de minúsculas         |
|Compatibilidad con vínculos físicos     |Compatible         |No compatible         |
|Compatibilidad con vínculos simbólicos     |Compatible         |No compatible         |
|Eliminación o modificación de archivos abiertos     |Compatible         |No compatible         |
|Bloqueo     |Network Lock Manager con bloqueo aconsejado de intervalo de bytes         |Compatible         |
|Lista segura de IP públicas | No compatible | Compatible|
|Interoperabilidad de protocolos| No compatible | FileREST|

## <a name="nfs-shares-preview"></a>Recursos compartidos NFS (versión preliminar)

El montaje de recursos compartidos de archivos de Azure con NFS 4.1 se encuentra actualmente en versión preliminar. Ofrece una integración más estrecha con Linux. Se trata de una oferta totalmente compatible con POSIX que es un estándar en las variantes de UNIX y otros sistemas operativos basados en *nix. Este servicio de almacenamiento de archivos de nivel empresarial se escala verticalmente para satisfacer sus necesidades de almacenamiento y miles de instancias de proceso pueden acceder a él.

### <a name="limitations"></a>Limitaciones

[!INCLUDE [files-nfs-limitations](../../../includes/files-nfs-limitations.md)]

#### <a name="regional-availability"></a>Disponibilidad regional

[!INCLUDE [files-nfs-regional-availability](../../../includes/files-nfs-regional-availability.md)]

### <a name="best-suited"></a>Idoneidad

NFS con Azure Files es ideal para:

- Cargas de trabajo que requieren recursos compartidos de archivos compatibles con POSIX, distinción entre mayúsculas y minúsculas o permisos de estilo Unix (UID/GID).
- Cargas de trabajo centradas en Linux que no requieren acceso a Windows.

### <a name="security"></a>Seguridad

Todos los datos de Azure Files se cifran en reposo. En el caso del cifrado en tránsito, Azure proporciona una capa de cifrado para todos los datos en tránsito entre los centros de datos de Azure mediante [MACSec](https://en.wikipedia.org/wiki/IEEE_802.1AE). Gracias a esto, se produce el cifrado cuando se transfieren datos entre centros de datos de Azure. A diferencia de Azure Files cuando usa el protocolo SMB, los recursos compartidos de archivos que utilizan el protocolo NFS no ofrecen autenticación basada en usuario. La autenticación de los recursos compartidos NFS se basa en las reglas de seguridad de red configuradas. Por eso, para tener la certeza de que solo se establecen conexiones seguras con su recurso compartido de NFS, debe usar puntos de conexión de servicio o puntos de conexión privados. Si desea acceder a recursos compartidos desde un entorno local, además de un punto de conexión privado, debe configurar una VPN o ExpressRoute. Las solicitudes que no tengan los siguientes orígenes se rechazarán:

- [Un punto de conexión privado](storage-files-networking-overview.md#private-endpoints)
- [Azure VPN Gateway](../../vpn-gateway/vpn-gateway-about-vpngateways.md)
    - [Una VPN de punto a sitio (P2S)](../../vpn-gateway/point-to-site-about.md)
    - [De sitio a sitio](../../vpn-gateway/design.md#s2smulti)
- [ExpressRoute](../../expressroute/expressroute-introduction.md)
- [Un punto de conexión público restringido](storage-files-networking-overview.md#storage-account-firewall-settings)

Para más información sobre las opciones de red disponibles, consulte [Consideraciones de redes para Azure Files](storage-files-networking-overview.md).

## <a name="smb-shares"></a>Recursos compartidos de SMB

Los recursos compartidos de archivos de Azure montados con SMB ofrecen más características de Azure Files y no tienen restricciones de características de Azure Files ya que por lo general están disponibles.

### <a name="features"></a>Características

- Sincronización de archivos de Azure
- Habilitación basada en identidad
- Soporte técnico de Azure Backup
- Instantáneas
- Eliminación temporal
- Cifrado en tránsito y cifrado en reposo

### <a name="best-suited"></a>Idoneidad

SMB con Azure Files es ideal para:

- Entornos de producción
- Clientes que requieren cualquiera de las características enumeradas en [Características](#features)

## <a name="next-steps"></a>Pasos siguientes

- [Creación de un recurso compartido de archivos NFS](storage-files-how-to-create-nfs-shares.md)
- [Crear un recurso compartido de archivos SMB](storage-how-to-create-file-share.md)