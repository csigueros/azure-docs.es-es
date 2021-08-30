---
title: Estrategias de copia de seguridad de Oracle Database en Azure Virtual Machines
description: Opciones para realizar copias de seguridad de instancias de Oracle Database en un entorno de máquina virtual Linux de Azure.
author: cro27
ms.service: virtual-machines
ms.subservice: oracle
ms.collection: linux
ms.topic: article
ms.date: 01/28/2021
ms.author: cholse
ms.reviewer: dbakevlar
ms.openlocfilehash: dbe0fe6d0dadea6e99b406f2de6e16ed4d667741
ms.sourcegitcommit: 7d63ce88bfe8188b1ae70c3d006a29068d066287
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 07/22/2021
ms.locfileid: "114461544"
---
# <a name="oracle-database-in-azure-linux-vm-backup-strategies"></a>Estrategias de copia de seguridad de Oracle Database en máquinas virtuales Linux de Azure

Las copias de seguridad de base de datos protegen la base de datos contra la pérdida de datos debido a errores en los componentes de almacenamiento y en el centro de datos. También pueden ser un medio de recuperación de errores humanos y una manera de clonar una base de datos con fines de desarrollo o pruebas. 

En Azure, dado que todo el almacenamiento es muy redundante y la pérdida de uno o más discos no conducirá a una interrupción de la base de datos, las copias de seguridad se usan con mayor frecuencia para protegerse frente a errores humanos, para la clonación de operaciones o la conservación de datos con fines normativos. También son un medio para protegerse frente a una interrupción regional en la que no se usa una tecnología de recuperación ante desastres como Data Guard. En este caso, las copias de seguridad deben almacenarse en diferentes regiones de Azure mediante la replicación con redundancia geográfica, de modo que estén disponibles fuera de la región de la base de datos principal.

## <a name="azure-storage"></a>Azure Storage 

Los [servicios de Azure Storage](../../../storage/common/storage-introduction.md) son la solución de almacenamiento en la nube de Microsoft para los escenarios modernos de almacenamiento de datos. Azure Storage ofrece una serie de servicios que se pueden usar para montar el almacenamiento externo en la máquina virtual Linux de Azure, que resulta adecuado como medio de copia de seguridad para las bases de datos de Oracle. Se necesita una herramienta de copia de seguridad, como Oracle RMAN, para iniciar una operación de copia de seguridad o restauración y copiar la copia de seguridad desde o hacia el servicio de Azure Storage.
 
Los servicios de Azure Storage ofrecen las siguientes ventajas:

-  Durabilidad y elevada disponibilidad. La redundancia garantiza que los datos están seguros en caso de errores de hardware transitorios. De manera predeterminada, todo el almacenamiento está reflejado tres veces. También puede optar por replicar datos entre centros de datos o regiones geográficas para obtener protección adicional frente a catástrofes locales o desastres naturales. Los datos replicados de esta manera permanecen con una alta disponibilidad en caso de una interrupción inesperada.

-  Seguro. Todos los datos escritos en una cuenta de Azure Storage se cifran mediante el servicio. Azure Storage proporciona un control pormenorizado sobre quién tiene acceso a los datos.

-  Escalable. Azure Storage está diseñado para poderse escalar de forma masiva para satisfacer las necesidades de rendimiento y almacenamiento de datos de las aplicaciones de hoy en día.

-  Administrado. Azure controla automáticamente el mantenimiento, las actualizaciones y los problemas críticos del hardware.

-  Accesibilidad. Es posible acceder a los datos de Azure Storage desde cualquier parte del mundo a través de HTTP o HTTPS. Microsoft proporciona bibliotecas cliente para Azure Storage en diversos lenguajes, incluidos .NET, Java, Node.js, Python, PHP, Ruby, Go y otros, así como una API REST consolidada. Azure Storage admite la escritura en Azure PowerShell o la CLI de Azure. Y Azure Portal y el Explorador de Azure Storage ofrecen soluciones visuales sencillas para trabajar con los datos.

La plataforma de Azure Storage incluye los siguientes servicios de datos que son adecuados para usarse como medios de copia de seguridad para la base de datos de Oracle:

-  Blobs de Azure: un almacén de objetos que se puede escalar de forma masiva para datos de texto y binarios. También incluye compatibilidad con el análisis de macrodatos a través de Data Lake Storage Gen2.

-  Azure Files: recursos compartidos de archivos administrados para implementaciones locales y en la nube.

-  Azure Disks: volúmenes de almacenamiento en el nivel de bloque para máquinas virtuales de Azure.

### <a name="cross-regional-storage-mounting"></a>Montaje de almacenamiento entre regiones

La capacidad de acceder al almacenamiento de copia de seguridad entre regiones es un aspecto importante de BCDR y resulta útil para la clonación de bases de datos a partir de copias de seguridad en diferentes regiones geográficas. El almacenamiento en la nube de Azure proporciona cinco niveles de [redundancia](../../../storage/common/storage-redundancy.md).
- El almacenamiento con redundancia local [(LRS)](../../../storage/common/storage-redundancy.md#locally-redundant-storage), donde los datos se replican tres veces en una única ubicación física de la región primaria.  
- El almacenamiento con redundancia de zona [(ZRS)](../../../storage/common/storage-redundancy.md#zone-redundant-storage), donde los datos están protegidos por LRS en la región primaria y se replican de manera sincrónica en tres zonas de disponibilidad (AZ) de la región primaria. Cada zona de disponibilidad también tiene protección LRS. 
- El almacenamiento con redundancia geográfica [(GRS)](../../../storage/common/storage-redundancy.md#geo-redundant-storage), donde los datos están protegidos por LRS en la región primaria y se replican de manera asincrónica en una región secundaria en la que también están protegidos por LRS.
- El almacenamiento con redundancia de zona geográfica [(GZRS)](../../../storage/common/storage-redundancy.md#geo-redundant-storage) copia los datos de manera sincrónica en tres zonas de disponibilidad de Azure de la región primaria mediante ZRS. Luego copia los datos de forma asincrónica en una única ubicación física en la región secundaria. En todas las ubicaciones, los datos también están protegidos por LRS. 
- El almacenamiento con redundancia geográfica con acceso de lectura [(RA-GRS)](../../../storage/common/storage-redundancy.md#geo-redundant-storage) y el almacenamiento con redundancia de zona geográfica con acceso de lectura [(RA-GZRS)](../../../storage/common/storage-redundancy.md#geo-redundant-storage) permite acceso de solo lectura a los datos replicados en la región secundaria en todo momento. 

#### <a name="blob-storage-and-file-storage"></a>Almacenamiento de blobs y almacenamiento de archivos

Al usar Azure Files con protocolos SMB o NFS 4.1 (versión preliminar) para realizar el montaje como almacenamiento de copia de seguridad, tenga en cuenta que Azure Files no admite el almacenamiento con redundancia geográfica con acceso de lectura (RA-GRS) ni el almacenamiento con redundancia de zona geográfica con acceso de lectura (RA-GZRS). 

Además, si el requisito de almacenamiento de copia de seguridad es mayor que 5 TiB, Azure Files requiere que se habilite la [característica de recursos compartidos de archivos grandes](../../../storage/files/storage-files-planning.md), que no admite la redundancia GRS o GZRS, solo admite LRS. 

El blob de Azure montado mediante el protocolo NFS 3.0 (versión preliminar) actualmente solo admite la redundancia LRS y ZRS.  

El blob de Azure configurado con cualquier opción de redundancia se puede montar mediante Blobfuse.

#### <a name="recovery-services-vault"></a>Almacén de Recovery Services 

Un almacén de Recovery Services es una entidad de administración que almacena los puntos de recuperación creados a lo largo del tiempo y proporciona una interfaz para realizar operaciones relacionadas con la copia de seguridad. Esto incluye realizar copias de seguridad a petición, realizar restauraciones y crear directivas de copia de seguridad.
Azure Backup administra automáticamente el almacenamiento para el almacén. Debe especificar cómo se replica ese almacenamiento en el momento de la creación. No se puede cambiar después de que los elementos estén protegidos en el almacén. Para la redundancia regional, elija la configuración con redundancia geográfica.

Si piensa realizar la restauración en una [región emparejada de Azure](../../../best-practices-availability-paired-regions.md) secundaria, habilite la característica de [restauración entre regiones](../../../backup/backup-create-rs-vault.md). Cuando está habilitada la restauración entre regiones, el almacenamiento de copia de seguridad se mueve de GRS al almacenamiento con redundancia geográfica con acceso de lectura (RA-GRS). 

### <a name="azure-blob-storage"></a>Azure Blob Storage

Azure Blob Storage es un servicio de almacenamiento basado en la nube muy rentable, duradero y seguro que se usa para almacenar grandes cantidades de datos no estructurados y es un medio ideal para usar copias de seguridad de Oracle Database. Azure Blob Storage se puede montar en máquinas virtuales Linux de Azure mediante el protocolo NFS v3.0 o Blobfuse (FUSE de Linux).

#### <a name="azure-blob-blobfuse"></a>Azure Blob Blobfuse

[Blobfuse](../../../storage/blobs/storage-how-to-mount-container-linux.md) es un proyecto de código abierto desarrollado para ofrecer un sistema de archivos virtual respaldado por Azure Blob Storage. Usa la biblioteca de código abierto libfuse para comunicarse con el módulo de kernel FUSE de Linux e implementa las operaciones del sistema de archivos mediante las API REST de Azure Storage Blob. Blobfuse está disponible actualmente para las distribuciones Ubuntu y Centos/RedHat. También está disponible para Kubernetes mediante el [controlador CSI](https://github.com/kubernetes-sigs/blob-csi-driver). 

Aunque está omnipresente en todas las regiones de Azure y funciona con todos los tipos de cuenta de almacenamiento, incluidas las de uso general v1/v2 y Azure Data Lake Store Gen2, el rendimiento que ofrece Blobfuse ha demostrado ser menor que el de protocolos alternativos, como SMB o NFS. Para que funcione mejor como medio de copia de seguridad de base de datos, se recomienda usar los protocolos SMB o [NFS](../../../storage/blobs/storage-how-to-mount-container-linux.md) para montar Azure Blob Storage. 

#### <a name="azure-blob-nfs-v30-preview"></a>Azure Blob NFS v3.0 (versión preliminar)

El soporte técnico de Azure para el protocolo Network File System (NFS) v3.0 ahora está en versión preliminar. La compatibilidad con [NFS](../../../storage/blobs/network-file-system-protocol-support.md) permite a los clientes de Windows y Linux montar un contenedor de almacenamiento de blobs en una máquina virtual de Azure. 

La versión preliminar pública de NFS 3.0 admite cuentas de almacenamiento GPV2 con un rendimiento de nivel estándar en las siguientes regiones: 
- Este de Australia
- Centro de Corea del Sur
- Centro-sur de EE. UU. 

Para garantizar la seguridad de la red, la cuenta de almacenamiento que se use para el montaje de NFS debe estar contenida en una red virtual. La seguridad de Azure Active Directory (AD) y las listas de control de acceso (ACL) no se admiten todavía en cuentas que tienen habilitada la compatibilidad con el protocolo NFS 3.0.

### <a name="azure-files"></a>Azure Files

[Azure Files](../../../storage/files/storage-files-introduction.md) es un sistema de archivos distribuido totalmente administrado basado en la nube que se puede montar en clientes de Windows, Linux o macOS basados en la nube o locales.

Azure Files ofrece recursos compartidos de archivos totalmente administrados multiplataforma en la nube, a los que se puede obtener acceso mediante los protocolos Bloque de mensajes del servidor (SMB) y Network File System (NFS) (versión preliminar). Actualmente, Azure Files no admite el acceso multiprotocolo, por lo que un recurso compartido solo puede ser un recurso compartido NFS o SMB. Se recomienda determinar qué protocolo se ajusta mejor a sus necesidades antes de crear recursos compartido de archivos.

Los recursos compartidos de archivos de Azure también se pueden proteger a través de Azure Backup en el almacén de Recovery Services, lo que proporciona una capa adicional de protección para las copias de seguridad de Oracle RMAN.

#### <a name="azure-files-nfs-v41-preview"></a>Azure Files NFS v4.1 (versión preliminar)

Los recursos compartidos de archivos de Azure se pueden montar en distribuciones de Linux mediante el protocolo Network File System (NFS) v4.1. En la versión preliminar hay varias limitaciones en cuanto a las características admitidas. Para más información, consulte [Montaje de un recurso compartido de archivos de Azure NFS (versión preliminar)](../../../storage/files/storage-files-how-to-mount-nfs-shares.md). 

En la versión preliminar de Azure Files NFS v4.1 también está restringida a las siguientes [regiones](../../../storage/files/storage-files-how-to-mount-nfs-shares.md):
- Este de EE. UU. (LRS y ZRS)
- Este de EE. UU. 2
- Oeste de EE. UU. 2
- Oeste de Europa
- Sudeste de Asia
- Sur de Reino Unido 2
- Este de Australia

#### <a name="azure-files-smb-30"></a>Azure Files SMB 3.0

Los recursos compartidos de archivos de Azure se pueden montar en distribuciones de Linux mediante el cliente kernel de SMB. El protocolo de sistema de archivos de Internet común (CIFS), disponible en distribuciones de Linux, es un dialecto de SMB. Al montar Azure Files SMB en máquinas virtuales Linux, se monta como un sistema de archivos de tipo CIFS y el paquete CIFS debe instalarse. 

Azure Files SMB está disponible con carácter general en todas las regiones de Azure y muestra las mismas características de rendimiento que los protocolos NFS v3.0 y v4.1, por lo que actualmente es el método recomendado para proporcionar medios de almacenamiento de copia de seguridad a máquinas virtuales Linux de Azure.  

Hay dos versiones de SMB admitidas disponibles, SMB 2.1 y SMB 3.0; se recomienda la segunda, ya que admite el cifrado en tránsito. Sin embargo, las distintas versiones de kernels de Linux tienen una compatibilidad diferente con SMB 2.1 y 3.0. Para más información, consulte [Montaje de un recurso compartido de archivos de Azure de SMB en Linux](../../../storage/files/storage-how-to-use-files-linux.md) para asegurarse de que la aplicación admita SMB 3.0. 

Ya que Azure Files está diseñado para ser un servicio de recursos compartidos de archivos multiusuario, hay ciertas características que debe ajustar para que sea más adecuado como medio de almacenamiento de copia de seguridad. Se recomienda desactivar el almacenamiento en caché y establecer los id. de usuario y de grupo para los archivos creados.

## <a name="azure-netapp-files"></a>Azure NetApp Files

El servicio [Azure NetApp Files](../../../azure-netapp-files/azure-netapp-files-solution-architectures.md) es una solución de almacenamiento completa para las instancias de Oracle Database en máquinas virtuales de Azure. Está basado en almacenamiento de archivos de nivel empresarial, alto rendimiento y medido, admite todo tipo de cargas de trabajo y tiene alta disponibilidad de manera predeterminada. Junto con el controlador Oracle Direct NFS (dNFS), Azure NetApp Files ofrece una capa de almacenamiento altamente optimizada para Oracle Database.

Azure NetApp Files proporciona copias de instantáneas basadas en almacenamiento eficientes del sistema de almacenamiento subyacente que usa un mecanismo de redirección en la escritura (RoW). Aunque las copias de instantáneas se toman y restauran muy rápidamente, solo sirven como una primera línea de defensa para abordar la inmensa mayoría de las operaciones de restauración que necesita cualquier organización, que a menudo es la recuperación de errores humanos. Sin embargo, las copias de las instantáneas no son una copia de seguridad completa. Para cubrir todos los requisitos de creación y restauración de copias de seguridad, las réplicas de instantáneas externas u otras copias de seguridad deben crearse en una zona geográfica remota para protegerse de las interrupciones regionales. Para obtener más información sobre el uso de NetApp Giles para instancias de Oracle Database en Azure, consulte este [informe](https://www.netapp.com/pdf.html?item=/media/17105-tr4780pdf.pdf).

## <a name="azure-backup-service"></a>Servicio Azure Backup

[Azure Backup](../../../backup/backup-overview.md) es una PaaS totalmente administrada que proporciona soluciones sencillas, seguras y rentables tanto para realizar copias de seguridad de datos de la nube de Microsoft Azure como para recuperarlos. Azure Backup puede crear y restaurar copias de seguridad de clientes locales, máquinas virtuales de Azure, recursos compartidos de Azure Files, así como bases de datos de SQL Server, Oracle, MySQL, PostreSQL y SAP HANA en máquinas virtuales de Azure. 

Azure Backup proporciona copias de seguridad independientes y aisladas para evitar la destrucción accidental de datos originales. Las copias de seguridad se almacenan en un [almacén de Recovery Services](../../../backup/backup-azure-recovery-services-vault-overview.md) con administración integrada de puntos de recuperación. La configuración y la escalabilidad son sencillas, las copias de seguridad están optimizadas y puede restaurarlas fácilmente cuando sea necesario. Usa la eficacia subyacente y la escala ilimitada de la nube de Azure para proporcionar alta disponibilidad, sin sobrecarga de mantenimiento o supervisión. Azure Backup no limita la cantidad de datos de entrada o salida que se transfieren ni cobra por ellos, y los datos están protegidos tanto en tránsito como en reposo. 

Para garantizar la durabilidad, Azure Backup ofrece varios tipos de replicación para mantener la alta disponibilidad de los datos de copia de seguridad.

- El almacenamiento con redundancia local [(LRS)](../../../storage/common/storage-redundancy.md#locally-redundant-storage) replica los datos tres veces (crea tres copias de los datos) en una unidad de escalado de almacenamiento de un centro de datos.
- El almacenamiento con redundancia geográfica [(GRS)](../../../storage/common/storage-redundancy.md#geo-redundant-storage) es la opción de replicación predeterminada y recomendada. GRS replica los datos en una región secundaria (a cientos de kilómetros de la ubicación principal de los datos de origen).

Un almacén creado con redundancia GRS incluye la opción para configurar la característica de [restauración entre regiones](../../../backup/backup-create-rs-vault.md#set-storage-redundancy), que permite restaurar los datos en una región secundaria de Azure emparejada.

El servicio de Azure Backup proporciona un [marco](../../../backup/backup-azure-linux-app-consistent.md) para lograr la coherencia de la aplicación durante las copias de seguridad de las máquinas virtuales Windows y Linux para diversas aplicaciones, como Oracle, MySQL, Mongo DB, SAP HANA y PostgreSQL, llamadas instantáneas coherentes con la aplicación. Esto implica invocar un script previo (para poner en modo inactivo las aplicaciones) antes de tomar una instantánea de los discos y llamar un script posterior (comandos para liberar las aplicaciones) una vez completada la instantánea, de modo que las aplicaciones vuelven al modo normal. Aunque que los scripts previos y posteriores de ejemplo se proporcionan en GitHub, la creación y mantenimiento de los mismos es su responsabilidad. En el caso de Oracle, la base de datos debe estar en modo ARCHIVELOG para permitir copias de seguridad en línea, y los comandos adecuados de inicio y finalización se ejecutan en los scripts previos y posteriores, que debe crear y mantener. 

Azure Backup ahora proporciona un [marco de scripts previos y posteriores mejorado](https://github.com/Azure/azure-linux-extensions/tree/master/VMBackup/main/workloadPatch/DefaultScripts), actualmente en versión preliminar, de modo que el servicio de Azure Backup proporcionará scripts previos y posteriores empaquetados para aplicaciones específicas. Los usuarios de Azure Backup solo tienen que asignar un nombre a la aplicación y, a continuación, la copia de seguridad de la máquina virtual de Azure invocará automáticamente los scripts previos y posteriores correspondientes. El equipo de Azure Backup dará mantenimiento a los scripts previos y posteriores empaquetados, por lo que los usuarios podrán estar seguros de la compatibilidad, propiedad y validez de estos scripts. Actualmente, las aplicaciones compatibles con el marco mejorado son Oracle y MySQL, y se tiene previsto agregar más tipos de aplicaciones en el futuro. La instantánea será una copia completa del almacenamiento y no una instantánea incremental o de copia por escritura, por lo que es un medio eficaz para restaurar la base de datos.

## <a name="next-steps"></a>Pasos siguientes

- [Inicio rápido para la creación de Oracle Database](oracle-database-quick-create.md)
- [Copia de seguridad de Oracle Database en Azure Files](oracle-database-backup-azure-storage.md)
- [Copia de seguridad de Oracle Database con el servicio de Azure Backup](oracle-database-backup-azure-backup.md)
