---
title: Planeamiento de una implementación de Azure Files | Microsoft Docs
description: Conozca cómo se planea una implementación de Azure Files. Puede montar directamente un recurso compartido de archivos de Azure file share o almacenar en la caché un recurso compartido de archivos de Azure de forma local con Azure File Sync.
author: roygara
ms.service: storage
ms.topic: conceptual
ms.date: 07/02/2021
ms.author: rogarana
ms.subservice: files
ms.custom: references_regions
ms.openlocfilehash: e1736d94c50d5c145a66fc845936c5c26a8725cb
ms.sourcegitcommit: f4e04fe2dfc869b2553f557709afaf057dcccb0b
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 07/02/2021
ms.locfileid: "113224065"
---
# <a name="planning-for-an-azure-files-deployment"></a>Planeamiento de una implementación de Azure Files
[Azure Files](storage-files-introduction.md) se puede implementar de dos formas principales: montando directamente los recursos compartidos de archivos de Azure sin servidor o almacenando en caché recursos compartidos de archivos de Azure localmente mediante Azure File Sync. La opción de implementación que elija cambiará todo aquello que debe tener en cuenta a la hora de planear la implementación. 

- **Montaje directo de un recurso compartido de archivos de Azure**: dado que Azure Files proporciona acceso mediante Bloque de mensajes del servidor (SMB) o Network File System (NFS), puede montar recursos compartidos de archivos de Azure en el entorno local o en la nube mediante los clientes SMB o NFS estándar (versión preliminar) disponibles en el sistema operativo. Dado que los recursos compartidos de archivos de Azure no tienen servidor, la implementación en escenarios de producción no requiere la administración de un servidor de archivos o un dispositivo NAS, lo que significa que no tiene que aplicar revisiones de software ni intercambiar discos físicos. 

- **Almacenamiento en caché de recursos compartidos de archivos de Azure localmente con Azure File Sync**: Azure File Sync le permite centralizar los recursos compartidos de archivos de su organización en Azure Files sin renunciar a la flexibilidad, el rendimiento y la compatibilidad de un servidor de archivos local. Azure File Sync transforma una instancia de Windows Server local (o en la nube) en una caché rápida de su recurso compartido de archivos SMB de Azure. 

En este artículo se abordan principalmente las consideraciones de implementación de un recurso compartido de archivos de Azure que se va a montar directamente mediante un cliente local o en la nube. Para planear una implementación de Azure File Sync, consulte [Planeamiento de una implementación de Azure File Sync](../file-sync/file-sync-planning.md).

## <a name="available-protocols"></a>Protocolos disponibles
Azure Files ofrece dos protocolos estándar del sector para el montaje de recursos compartidos de archivos de Azure: el protocolo [Bloque de mensajes del servidor (SMB)](files-smb-protocol.md) y el protocolo [Network File System (NFS)](files-nfs-protocol.md). Azure Files le permite seleccionar el protocolo del sistema de archivos más adecuado para su carga de trabajo. Los recursos compartidos de archivos de Azure no admiten los protocolos SMB y NFS en el mismo recurso compartido de archivos, aunque puede crear recursos compartidos de archivos de Azure SMB y NFS dentro de la misma cuenta de almacenamiento. Actualmente, NFS 4.1 solo se admite en el nuevo tipo de cuenta de almacenamiento **FileStorage** (solo recursos compartidos de archivos premium).

Con los recursos compartidos de archivos SMB y NFS, Azure Files ofrece recursos compartidos de archivos de nivel empresarial que se pueden escalar verticalmente para satisfacer sus necesidades de almacenamiento y a los que pueden acceder simultáneamente miles de clientes.

| Característica | SMB | NFS (versión preliminar) |
|---------|-----|---------------|
| Versiones de protocolo admitidas | SMB 3.1.1, SMB 3.0, SMB 2.1 | NFS 4.1 |
| SO recomendado | <ul><li>Windows 10, versión 21H1 o posterior</li><li>Windows Server 2019 o posterior</li><li>Versión 5.3 o posterior del kernel de Linux</li></ul> | Versión posterior a la 4.3 del kernel de Linux |
| [Niveles disponibles](storage-files-planning.md#storage-tiers)  | Premium, optimizado para transacciones, acceso frecuente y acceso esporádico | Premium |
| Modelo de facturación | <ul><li>[Capacidad aprovisionada para recursos compartidos de archivos premium](./understanding-billing.md#provisioned-model)</li><li>[Pago por uso para recursos compartidos de archivos estándar](./understanding-billing.md#pay-as-you-go-model)</li></ul> | [Capacidad aprovisionada](./understanding-billing.md#provisioned-model) |
| [Redundancia](storage-files-planning.md#redundancy) | LRS, ZRS, GRS, GZRS | LRS, ZRS |
| Semántica del sistema de archivos | Win32 | POSIX |
| Authentication | Autenticación basada en identidad (Kerberos), autenticación de clave compartida (NTLMv2) | Autenticación basada en host |
| Authorization | Listas de control de acceso (ACL) de estilo Win32 | Permisos de estilo UNIX |
| Distinción entre mayúsculas y minúsculas | Sin distinción de mayúsculas y minúsculas, conservación de mayúsculas y minúsculas | Distingue mayúsculas de minúsculas |
| Eliminación o modificación de archivos abiertos | Solo con bloqueo | Sí |
| Uso compartido de archivos | [Modo de uso compartido de Windows](/windows/win32/fileio/creating-and-opening-files) | Network Lock Manager con bloqueo aconsejado de intervalo de bytes |
| Compatibilidad con vínculos físicos | No compatible | Compatible |
| Compatibilidad con los vínculos simbólicos | No compatible | Compatible |
| Opcionalmente accesible desde Internet | Sí (solo SMB 3.0 o posterior) | No |
| Admite FileREST | Sí | Subconjunto: <br /><ul><li>[Operaciones en `FileService`](/rest/api/storageservices/operations-on-the-account--file-service-)</li><li>[Operaciones en `FileShares`](/rest/api/storageservices/operations-on-shares--file-service-)</li><li>[Operaciones en `Directories`](/rest/api/storageservices/operations-on-directories)</li><li>[Operaciones en `Files`](/rest/api/storageservices/operations-on-files)</li></ul> |

## <a name="management-concepts"></a>Conceptos de administración
[!INCLUDE [storage-files-file-share-management-concepts](../../../includes/storage-files-file-share-management-concepts.md)]

Al implementar recursos compartidos de archivos de Azure en cuentas de almacenamiento, se recomienda:

- Implementar solo recursos compartidos de archivos de Azure en cuentas de almacenamiento con otros recursos compartidos de archivos de Azure. Aunque las cuentas de almacenamiento de GPv2 permiten tener cuentas de almacenamiento de propósito combinado, dado que los recursos de almacenamiento como los recursos compartidos de archivos de Azure y los contenedores de blobs comparten los límites de la cuenta de almacenamiento, la combinación de recursos puede dificultar la solución de problemas de rendimiento más adelante. 

- Prestar atención a las limitaciones de IOPS de la cuenta de almacenamiento al implementar recursos compartidos de archivos de Azure. Lo ideal sería asignar recursos compartidos de archivos 1:1 a cuentas de almacenamiento; sin embargo, quizás no sea posible debido a diversos límites y restricciones, tanto de su organización como de Azure. Cuando no sea posible tener un solo recurso compartido de archivos implementado en una cuenta de almacenamiento, tenga en cuenta qué recursos compartidos estarán muy activos y cuales estarán menos activos, con el fin de asegurarse de que los recursos compartidos de archivos más activos no se colocan en la misma cuenta de almacenamiento.

- Implementar solamente cuentas de GPv2 y FileStorage, y actualizar las cuentas de almacenamiento clásicas y de GPv1 cuando las encuentre en su entorno. 

## <a name="identity"></a>Identidad
Para acceder a un recurso compartido de archivos de Azure, el usuario debe estar autenticado y tener la debida autorización. Esto se hace en función de la identidad del usuario que accede al recurso compartido de archivos. Azure Files se integra con tres proveedores de identidades principales:
- **Active Directory Domain Services (AD DS) local (AD DS local)** : Las cuentas de almacenamiento de Azure pueden estar unidas a un dominio de Active Directory Domain Services propiedad del cliente, al igual que un servidor de archivos de Windows Server o un dispositivo NAS. Puede implementar un controlador de dominio en el entorno local, en una VM de Azure o, incluso, como VM en otro proveedor de nube. Azure Files es independiente de la ubicación donde se hospeda el controlador de dominio. Una vez que una cuenta de almacenamiento está unida a un dominio, el usuario final puede montar un recurso compartido de archivos con la cuenta de usuario con la que inició sesión en su equipo. La autenticación basada en AD usa el protocolo de autenticación Kerberos.
- **Azure Active Directory Domain Services (Azure AD DS)** : Azure AD DS proporciona un controlador de dominio administrado por Microsoft que se puede usar para los recursos de Azure. La unión a un dominio de la cuenta de almacenamiento a Azure AD DS proporciona ventajas similares a la unión a un dominio de dicha cuenta a una instancia de Active Directory propiedad del cliente. Esta opción de implementación es especialmente útil para escenarios de migración mediante lift-and-shift de aplicaciones que requieren permisos basados en AD. Dado que Azure AD DS proporciona autenticación basada en AD, esta opción también usa el protocolo de autenticación Kerberos.
- **Clave de la cuenta de Azure Storage**: los recursos compartidos de archivos de Azure también se pueden montar con una clave de cuenta de almacenamiento de Azure. Para montar un recurso compartido de archivos de esta forma, el nombre de la cuenta de almacenamiento se usa como nombre de usuario y la clave de la cuenta de almacenamiento se usa como contraseña. El uso de la clave de la cuenta de almacenamiento para montar el recurso compartido de archivos de Azure es realmente una operación de administrador, ya que el recurso compartido de archivos montado tendrá permisos completos para todos los archivos y todas las carpetas del recurso compartido, aunque tengan ACL. Cuando se usa la clave de la cuenta de almacenamiento para el montaje a través de SMB, se usa el protocolo de autenticación NTLMv2.

En el caso de los clientes que realizan la migración desde servidores de archivos locales o que crean nuevos recursos compartidos de archivos en Azure Files destinados a comportarse como servidores de archivos de Windows o dispositivos NAS, la unión a un dominio de la cuenta de almacenamiento a **Active Directory propiedad del cliente** es la opción recomendada. Para más información acerca de la unión a un dominio de la cuenta de almacenamiento a una instancia de Active Directory propiedad del cliente, consulte la [introducción a Active Directory de Azure Files](storage-files-active-directory-overview.md).

Si tiene previsto usar la clave de la cuenta de almacenamiento para acceder a los recursos compartidos de archivos de Azure, se recomienda usar puntos de conexión de servicio como se describe en la sección [Redes](#networking).

## <a name="networking"></a>Redes
Los recursos compartidos de archivos de Azure son accesibles desde cualquier lugar a través del punto de conexión público de la cuenta de almacenamiento. Esto significa que las solicitudes autenticadas, como las solicitudes autorizadas por la identidad de inicio de sesión de un usuario, pueden originarse de forma segura dentro o fuera de Azure. En muchos entornos de cliente, se producirá un error en el montaje inicial del recurso compartido de archivos de Azure en la estación de trabajo local, aunque los montajes desde las máquinas virtuales de Azure se realicen correctamente. El motivo es que muchas organizaciones y proveedores de servicios de Internet (ISP) bloquean el puerto que usa SMB para comunicarse, el puerto 445. Para ver el resumen de los ISP que permiten o deniegan el acceso desde el puerto 445, visite [TechNet](https://social.technet.microsoft.com/wiki/contents/articles/32346.azure-summary-of-isps-that-allow-disallow-access-from-port-445.aspx).

Para desbloquear el acceso al recurso compartido de archivos de Azure, tiene dos opciones principales:

- Desbloquear el puerto 445 para la red local de su organización. Solo se puede acceder externamente a recursos compartidos de archivos de Azure a través del punto de conexión público mediante protocolos seguros para Internet, como SMB 3.x y la API de FileREST. Esta es la manera más fácil de acceder al recurso compartido de archivos de Azure desde el entorno local, ya que no requiere una configuración de red avanzada más allá de cambiar las reglas de puerto de salida de la organización. Sin embargo, se recomienda quitar las versiones heredadas y en desuso del protocolo SMB, concretamente SMB 1.0. Para obtener información sobre cómo hacerlo, consulte [Protección de Windows y Windows Server](/windows-server/storage/file-server/troubleshoot/detect-enable-and-disable-smbv1-v2-v3) y [Protección de Linux](files-remove-smb1-linux.md).

- Acceder a recursos compartidos de archivos de Azure a través de una conexión de ExpressRoute o VPN. Al acceder al recurso compartido de archivos de Azure a través de un túnel de red, puede montar el recurso compartido de archivos de Azure como un recurso compartido de archivos local, ya que el tráfico SMB no atraviesa el límite de la organización.   

Aunque desde una perspectiva técnica es considerablemente más fácil montar los recursos compartidos de archivos de Azure a través del punto de conexión público, esperamos que la mayoría de los clientes opten por montar sus recursos compartidos de archivos de Azure a través de una conexión de ExpressRoute o VPN. El montaje con estas opciones es posible con ambos recursos compartidos, SMB y NFS. Para ello, deberá configurar las opciones siguientes para su entorno:  

- **Tunelización de red mediante ExpressRoute o una VPN de sitio a sitio o de punto a sitio**: la tunelización en una red virtual permite acceder a recursos compartidos de archivos de Azure desde el entorno local, aunque el puerto 445 esté bloqueado.
- **Puntos de conexión privados**: los puntos de conexión privados proporcionan a su cuenta de almacenamiento una dirección IP dedicada desde el espacio de direcciones de la red virtual. Esto permite la tunelización de red sin necesidad de abrir redes locales en todos los intervalos de direcciones IP que son propiedad de los clústeres de almacenamiento de Azure. 
- **Reenvío DNS**: configure su DNS local para resolver el nombre de la cuenta de almacenamiento (`storageaccount.file.core.windows.net` para las regiones de la nube pública) para resolver la dirección IP de sus puntos de conexión privados.

> [!Important]  
> Azure Files admite varias opciones de enrutamiento de red. La opción predeterminada, el enrutamiento de Microsoft, funciona con todas las configuraciones de Azure Files. La opción de enrutamiento de Internet no admite escenarios de unión a un dominio de AD ni Azure File Sync.

Para planear las redes asociadas a la implementación de un recurso compartido de archivos de Azure, consulte [Consideraciones de redes para Azure Files](storage-files-networking-overview.md).

## <a name="encryption"></a>Cifrado
Azure Files admite dos tipos de cifrado diferentes: el cifrado en tránsito, que se relaciona con el cifrado que se usa al montar el recurso compartido de archivos de Azure y acceder a este, y el cifrado en reposo, relacionado con la forma en que se cifran los datos cuando se almacenan en el disco. 

### <a name="encryption-in-transit"></a>Cifrado en tránsito

> [!IMPORTANT]
> En esta sección se tratan los detalles del cifrado en tránsito para recursos compartidos SMB. Para más información sobre el cifrado en tránsito con recursos compartidos de NFS, consulte [Seguridad y redes](files-nfs-protocol.md#security-and-networking).

De forma predeterminada, todas las cuentas de Azure Storage tienen habilitado el cifrado en tránsito. Esto significa que, al montar un recurso compartido de archivos a través de SMB o acceder a él a través del protocolo de FileREST (por ejemplo, a través de Azure Portal, la CLI o PowerShell, o los SDK de Azure), Azure Files solo permitirá la conexión si se realiza con una versión posterior a SMB 3.x con cifrado o HTTPS. Los clientes que no admiten SMB 3.x, o los clientes que admiten SMB 3.x pero no el cifrado SMB, no podrán montar el recurso compartido de archivos de Azure si está habilitado el cifrado en tránsito. Para más información sobre qué sistemas operativos admiten SMB 3.x con cifrado, consulte nuestra documentación detallada para [Windows](storage-how-to-use-files-windows.md), [macOS](storage-how-to-use-files-mac.md) y [Linux](storage-how-to-use-files-linux.md). Todas las versiones actuales de PowerShell, la CLI y los SDK admiten HTTPS.  

Puede deshabilitar el cifrado en tránsito para una cuenta de almacenamiento de Azure. Cuando el cifrado está deshabilitado, Azure Files también permite el uso de SMB 2.1, SMB 3.x sin cifrado y llamadas a la API de FileREST sin cifrar a través de HTTP. La razón principal para deshabilitar el cifrado en tránsito es admitir una aplicación heredada que debe ejecutarse en un sistema operativo anterior, como Windows Server 2008 R2 o una distribución de Linux anterior. Azure Files solo permite conexiones SMB 2.1 dentro de la misma región de Azure del recurso compartido de archivos de Azure. Un cliente SMB 2.1 fuera de la región de Azure del recurso compartido de archivos de Azure (por ejemplo, en un entorno local o en una región de Azure diferente) no podrá acceder al recurso compartido de archivos.

Se recomienda encarecidamente asegurarse de que está habilitado el cifrado de los datos en tránsito.

Para obtener más información sobre el cifrado en tránsito, consulte [Requerir transferencia segura en Azure Storage](../common/storage-require-secure-transfer.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json).

### <a name="encryption-at-rest"></a>Cifrado en reposo
[!INCLUDE [storage-files-encryption-at-rest](../../../includes/storage-files-encryption-at-rest.md)]

## <a name="data-protection"></a>Protección de los datos
Azure Files tiene un enfoque de varias capas para garantizar la copia de seguridad de los datos, su recuperación y su protección contra amenazas de seguridad.

### <a name="soft-delete"></a>Eliminación temporal
La eliminación temporal de recursos compartidos de archivos (versión preliminar) es una configuración de nivel de cuenta de almacenamiento que le permite recuperar el recurso compartido de archivos cuando se elimina accidentalmente. Cuando se elimina un recurso compartido de archivos, pasa a un estado de eliminación temporal, en lugar de borrarse de forma permanente. Se puede configurar el tiempo durante el que los datos eliminados de forma temporal se pueden recuperar antes de que se eliminen permanentemente y durante este período de retención el recurso compartido se puede recuperar en cualquier momento. 

Se recomienda activar la eliminación temporal para la mayoría de los recursos compartidos de archivos. Si tiene un flujo de trabajo en el que la eliminación de recursos compartidos es común y se espera, puede que decida tener un período de retención corto o no tener habilitada la eliminación temporal.

Para obtener más información acerca de la eliminación temporal, consulte [Evitar la eliminación accidental de datos](./storage-files-prevent-file-share-deletion.md).

### <a name="backup"></a>Copia de seguridad
Puede realizar una copia de seguridad del recurso compartido de archivos de Azure a través de [instantáneas de recurso compartido](./storage-snapshots-files.md), que son copias de solo lectura de un momento dado del recurso compartido. Las instantáneas son incrementales, lo que significa que solo contienen los datos que han cambiado desde la instantánea anterior. Puede tener hasta 200 instantáneas por recurso compartido de archivos y conservarlas durante un máximo de diez años. Puede realizar estas instantáneas manualmente en Azure Portal, a través de PowerShell o en la interfaz de la línea de comandos (CLI), o bien puede usar [Azure Backup](../../backup/azure-file-share-backup-overview.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json). Las instantáneas se almacenan en el recurso compartido de archivos, lo que significa que si lo elimina, también se eliminarán las instantáneas. Para proteger las copias de seguridad de instantáneas contra eliminaciones accidentales, asegúrese de que la eliminación temporal está habilitada para el recurso compartido.

[Azure Backup para recursos compartidos de archivos de Azure](../../backup/azure-file-share-backup-overview.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json) controla la programación y retención de instantáneas. Sus capacidades de abuelo-padre-hijo (GFS) significan que puede tomar instantáneas diarias, semanales, mensuales y anuales, cada una con su propio período de retención distinto. Azure Backup también organiza la habilitación de la eliminación temporal y toma un bloqueo de eliminación en una cuenta de almacenamiento en cuanto se configura un recurso compartido de archivos en ella para la copia de seguridad. Por último, Azure Backup proporciona ciertas capacidades clave de supervisión y alertas que permiten a los clientes tener una vista consolidada de su copia de seguridad.

Puede realizar restauraciones de nivel de elemento y de nivel de recurso compartido en Azure Portal mediante Azure Backup. Lo único que debe hacer es elegir el punto de restauración (una instantánea concreta), el archivo o directorio en cuestión si es pertinente y, a continuación, la ubicación (original o alternativa) en la que quiere realizar la restauración. El servicio de copia de seguridad controla la copia de los datos de instantáneas y muestra el progreso de la restauración en el portal.

Para obtener más información sobre Azure Backup, vea [Acerca de la copia de seguridad de recursos compartidos de archivos de Azure](../../backup/azure-file-share-backup-overview.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json).

### <a name="azure-defender-for-azure-files"></a>Azure Defender para Azure Files 
Azure Defender para Azure Storage (antes Protección contra amenazas avanzada para Azure Storage) ofrece una capa adicional de inteligencia de seguridad que proporciona alertas cuando detecta actividades anómalas en la cuenta de almacenamiento; por ejemplo, intentos no habituales de acceso. También ejecuta análisis de reputación de hash de malware y generará una alerta sobre malware conocido. Puede configurar Azure Defender en un nivel de suscripción o de cuenta de almacenamiento a través de Azure Security Center. 

Para más información, consulte [Introducción a Azure Defender para Storage](../../security-center/defender-for-storage-introduction.md).

## <a name="storage-tiers"></a>Niveles de almacenamiento
[!INCLUDE [storage-files-tiers-overview](../../../includes/storage-files-tiers-overview.md)]

#### <a name="limitations"></a>Limitaciones
[!INCLUDE [storage-files-tiers-large-file-share-availability](../../../includes/storage-files-tiers-large-file-share-availability.md)]

## <a name="redundancy"></a>Redundancia
[!INCLUDE [storage-files-redundancy-overview](../../../includes/storage-files-redundancy-overview.md)]

## <a name="migration"></a>Migración
En muchos casos, no se establecerá un nuevo recurso compartido de archivos para su organización, sino que se migrará uno existente de un servidor de archivos local o un dispositivo NAS a Azure Files. La elección de la herramienta y estrategia de migración adecuadas para el escenario es importante para el éxito de la migración. 

En el [artículo de introducción a la migración](storage-files-migration-overview.md) se describen brevemente los aspectos básicos y se incluye una tabla que le conduce a guías de migración que probablemente cubran su escenario.

## <a name="next-steps"></a>Pasos siguientes
* [Planeamiento de una implementación de Azure File Sync](../file-sync/file-sync-planning.md)
* [Implementación de Azure Files](./storage-how-to-create-file-share.md)
* [Implementación de Azure File Sync](../file-sync/file-sync-deployment-guide.md)
* [Consulte el artículo de introducción a la migración para encontrar la guía de migración para su escenario.](storage-files-migration-overview.md)