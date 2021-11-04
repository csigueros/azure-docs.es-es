---
title: Glosario de Azure Backup
description: En este artículo se definen los términos útiles para su uso con Azure Backup.
ms.topic: conceptual
ms.date: 12/21/2020
ms.openlocfilehash: 774fc63002677aa9fbb986d40a5d8fe2b3e99f7d
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/03/2021
ms.locfileid: "131465200"
---
# <a name="azure-backup-glossary"></a>Glosario de Azure Backup

Este glosario de términos puede ser útil para usar Azure Backup.

>[!NOTE]
>
> - Los términos que se marcan con el prefijo "(término específico de la carga de trabajo)" hacen referencia a los términos que solo son pertinentes en el contexto de un subconjunto específico de cargas de trabajo admitido por Azure Backup.
> - Para los términos que se usan habitualmente en la documentación de Azure Backup pero que hacen referencia a otros servicios de Azure, se proporciona un vínculo directo a la documentación del servicio de Azure correspondiente.

## <a name="afs-azure-file-shares"></a>AFS (recursos compartidos de archivos de Azure)

Consulte la [documentación de Azure Files](../storage/files/storage-files-introduction.md).

## <a name="alternate-location-recovery"></a>Recuperación de ubicación alternativa

La realizada desde el punto de recuperación a una ubicación distinta de la original de donde proceden las copias de seguridad. Cuando se usa la copia de seguridad de máquina virtual de Azure, esto significaría restaurar la máquina virtual en un servidor distinto del servidor original donde se realizaron las copias de seguridad. Cuando se usa la copia de seguridad de recursos compartidos de archivos de Azure, esto significaría restaurar los datos en un recurso compartido de archivos distinto del de la copia de seguridad.

## <a name="application-consistent-backup"></a>Copia de seguridad coherente con la aplicación

(término específico de la carga de trabajo)

Las copias de seguridad coherentes con las aplicaciones capturan el contenido de la memoria y las operaciones de E/S pendientes. Las instantáneas coherentes con la aplicación usan el escritor [VSS](#vss-windows-volume-shadow-copy-service) (o scripts anteriores o posteriores para Linux) para garantizar la coherencia de los datos de la aplicación antes de que se produzca una copia de seguridad. [Más información](backup-azure-vms-introduction.md).

## <a name="azure-resource-manager-arm-templates"></a>Plantillas de Azure Resource Manager (ARM)

Consulte la [documentación de las plantillas de ARM](../azure-resource-manager/templates/overview.md).

## <a name="autoprotection-for-databases"></a>Protección automática (para bases de datos)

(término específico de la carga de trabajo)

Funcionalidad que permite proteger automáticamente todas las bases de datos de una instancia de SQL Server independiente o un grupo de disponibilidad AlwaysOn de SQL Server. No solo habilita las copias de seguridad de las bases de datos existentes, sino que también protege todas las bases de datos que pueda agregar en el futuro.

## <a name="availability-storage-replication-types"></a>Disponibilidad (tipos de replicación de almacenamiento)

Azure Backup ofrece tres tipos de replicación para mantener la alta disponibilidad de los datos y del almacenamiento:

### <a name="lrs"></a>LRS

El [almacenamiento con redundancia local (LRS)](../storage/common/storage-redundancy.md#locally-redundant-storage) replica los datos de la copia de seguridad tres veces (crea tres copias de los datos de la copia de seguridad) en una unidad de escalado de almacenamiento de un centro de datos. Todas las copias de los datos con copia de seguridad se encuentran en la misma región. LRS es una opción de bajo costo para proteger los datos con copia de seguridad de los errores de hardware local.

### <a name="grs"></a>GRS

El [almacenamiento con redundancia geográfica (GRS)](../storage/common/storage-redundancy.md#geo-redundant-storage) es la opción de replicación predeterminada y recomendada. Replica los datos de la copia de seguridad en una región secundaria (a cientos de kilómetros de la ubicación principal de los datos de origen). Cuesta más que el almacenamiento con redundancia local, pero proporciona un mayor durabilidad de los datos de la copia de seguridad, aunque se produzca una interrupción regional.

>[!NOTE]
>Para los almacenes con redundancia geográfica con la característica de restauración entre regiones habilitada, el almacenamiento de copia de seguridad se actualiza de GRS a RA-GRS (almacenamiento con redundancia geográfica con acceso de lectura).

### <a name="zrs"></a>ZRS

El [almacenamiento con redundancia de zona (ZRS)](../storage/common/storage-redundancy.md#zone-redundant-storage) replica los datos de la copia de seguridad en [zonas de disponibilidad](../availability-zones/az-overview.md#availability-zones), lo cual garantiza la residencia de los datos de la copia de seguridad y la resistencia en la misma región. Por lo tanto, se pueden realizar copias de seguridad en el almacenamiento con redundancia de zona de las cargas de trabajo críticas que requieren [residencia de datos](https://azure.microsoft.com/resources/achieving-compliant-data-residency-and-security-with-azure/).

## <a name="azure-command-line-interface-cli"></a>Interfaz de la línea de comandos (CLI) de Azure

Consulte la [documentación de la CLI de Azure](/cli/azure/what-is-azure-cli).

## <a name="azure-policy"></a>Azure Policy

Consulte la [documentación de Azure Policy](../governance/policy/overview.md).

## <a name="azure-powershell"></a>Azure PowerShell

Consulte la [documentación de Azure PowerShell](/powershell/azure/).

## <a name="azure-resource-manager-arm"></a>Azure Resource Manager (ARM)

Consulte la [documentación de Azure Resource Manager](../azure-resource-manager/management/overview.md).

## <a name="azure-disk-encryption-ade"></a>Azure Disk Encryption (ADE)

Consulte la [documentación de Azure Disk Encryption](../security/fundamentals/azure-disk-encryption-vms-vmss.md).

## <a name="backend-storage--cloud-storage--backup-storage"></a>Almacenamiento de back-end/almacenamiento en la nube/almacenamiento de copia de seguridad

Almacenamiento real utilizado por una instancia de copia de seguridad. Incluye el tamaño de todos los puntos de retención que existen para la instancia de copia de seguridad (tal y como se define en la directiva de copia de seguridad y retención).

## <a name="bare-metal-backup"></a>Copia de seguridad de un equipo sin sistema operativo

Copia de seguridad de los archivos del sistema operativo y de todos los datos de volúmenes críticos, excepto de los datos de usuario. Por definición, una copia de seguridad de un equipo sin sistema operativo incluye una copia de seguridad del estado del sistema. Proporciona protección cuando un equipo no se inicia y es necesario recuperarlo todo. [Más información](backup-mabs-system-state-and-bmr.md).

## <a name="backup-extensions--vm-extensions"></a>Extensiones de copia de seguridad/extensiones de máquina virtual

(específico del tipo de carga de trabajo de máquina virtual de Azure)

Las extensiones de máquina virtual (VM) de Azure son aplicaciones pequeñas que realizan tareas de automatización y configuración posterior a la implementación en máquinas virtuales de Azure. Azure Backup realiza una copia de seguridad de máquinas virtuales de Azure instalando una extensión en el agente de máquina virtual de Azure que se ejecuta en la máquina. Azure Backup administra automáticamente estas extensiones y los usuarios no necesitan actualizarlas manualmente.

## <a name="backup-instance--backup-item"></a>Instancia/elemento de copia de seguridad

Copia de seguridad de un origen de datos en un almacén con una directiva de retención y copia de seguridad determinada.

## <a name="backup-rule--backup-policy"></a>Regla/directiva de copia de seguridad

Regla definida por el usuario que especifica cuándo y con qué frecuencia se deben realizar copias de seguridad en un origen de datos. En algunos tipos de carga de trabajo, la directiva de copia de seguridad también proporciona una manera de especificar el método de instantánea que se va a aplicar en el origen de datos (completo, incremental, diferencial). Las directivas de copia de seguridad se crean a menudo como combinación de reglas de copia de seguridad y de retención.

## <a name="backup-vault"></a>Almacén de Backup

Recurso de Azure Resource Manager del tipo *Microsoft.DataProtection/BackupVaults*. Actualmente, los almacenes de Backup se usan para realizar copias de seguridad del servidor de Azure Database for PostgreSQL. [Más información acerca de los almacenes de Backup](backup-azure-recovery-services-vault-overview.md).

## <a name="bcdr-business-continuity-and-disaster-recovery"></a>BCDR (continuidad empresarial y recuperación ante desastres)

Incluye un conjunto de procesos que una organización debe adoptar para asegurarse de que las aplicaciones y las cargas de trabajo funcionen correctamente durante el servicio planeado y no planeado o las interrupciones de Azure con interrupción mínima de la empresa. [Más información](https://azure.microsoft.com/solutions/backup-and-disaster-recovery/#overview) sobre los distintos servicios que Azure ofrece para ayudar a crear una buena estrategia de continuidad empresarial y recuperación ante desastres.

## <a name="churn"></a>Renovación

Porcentaje de cambios en los datos entre una copia de seguridad y otra consecutiva. Esto puede deberse a la incorporación de nuevos datos o a la modificación o eliminación de los existentes.

## <a name="crash-consistent-backup"></a>Copia de seguridad coherente frente a bloqueos

(término específico de la carga de trabajo)

Las instantáneas coherentes con bloqueos suelen producirse si una VM de Azure se apaga en el momento en que se hace la copia de seguridad. Solamente se capturan y se hace una copia de seguridad de los datos que ya existen en el disco en el momento de la copia de seguridad. [Más información](backup-azure-vms-introduction.md#snapshot-consistency).

## <a name="cross-region-restore-crr"></a>Restauración entre regiones (CRR)

Una de las [opciones de restauración](backup-azure-arm-restore-vms.md#restore-options); permite restaurar elementos de copia de seguridad en una región secundaria, una [región emparejada de Azure](../best-practices-availability-paired-regions.md#what-are-paired-regions).

## <a name="data-box"></a>Data box

Consulte la [documentación de Data Box](../databox/data-box-overview.md).

## <a name="datasource"></a>Origen de datos

Un recurso (recurso de Azure, recurso de proxy o recurso local) candidato a la copia de seguridad. Por ejemplo, una máquina virtual de Azure o un recurso compartido de archivos de Azure.

## <a name="dpm-data-protection-manager"></a>DPM (Data Protection Manager)

(término específico de la carga de trabajo)

Consulte la [documentación de DPM](/system-center/dpm/dpm-overview).

## <a name="expressroute"></a>ExpressRoute

consulte la [documentación de ExpressRoute](../expressroute/expressroute-introduction.md).

## <a name="file-system-consistent-backup"></a>Copia de seguridad coherente con el sistema de archivos

(término específico de la carga de trabajo)

Las copias de seguridad coherentes con el sistema de archivos proporcionan coherencia al hacer una instantánea de todos los archivos al mismo tiempo. [Más información](backup-azure-vms-introduction.md#snapshot-consistency).

## <a name="frontend-storage--source-size"></a>Almacenamiento de front-end/tamaño de origen

Tamaño de los datos de los que se va a realizar una copia de seguridad para un origen de datos. El tamaño de front-end de un origen de datos determina su recuento de [instancias protegidas](#protected-instance).

## <a name="full-backup"></a>Copia de seguridad completa

En ella se almacena una copia de todo el origen de datos en cada copia de seguridad.

## <a name="gfs-backup-policy"></a>Directiva de copia de seguridad GFS

Una directiva de copia de seguridad GFS (abuelo-padre-hijo) es aquella que permite definir programaciones de copia de seguridad semanales, mensuales y anuales además de la diaria. Las copias de seguridad semanales se conocen como "hijos"; las mensuales, como "padres" y las anuales, como "abuelos". Cada uno de estos conjuntos de copias de seguridad se puede configurar con duración de retención distinta para mayor personalización de las opciones de retención de las copias de seguridad. Las directivas GFS son útiles para conservar las copias de seguridad durante mayores períodos de tiempo con almacenamiento más eficaz.

## <a name="iaas-vms--azure-vms"></a>Máquinas virtuales IaaS o máquinas virtuales de Azure

Consulte la [documentación de las máquinas virtuales de Azure](../virtual-machines/index.yml).

## <a name="incremental-backup"></a>Copia de seguridad incremental

Almacena solo los bloques de datos que cambiaron desde la copia de seguridad anterior.

## <a name="instant-restore"></a>Restauración instantánea

(Término específico de la carga de trabajo) Implica restaurar al instante una máquina directamente desde su instantánea de copia de seguridad en lugar de hacerlo desde la copia de la instantánea del almacén. Las restauraciones instantáneas son más rápidas que las que se realizan desde un almacén. El número de puntos de restauración instantánea disponibles depende de la duración de retención configurada para las instantáneas. Actualmente solo se aplica a la copia de seguridad de máquina virtual de Azure.

## <a name="iops"></a>E/S

Operaciones de entrada/salida por segundo.

## <a name="item-level-restore"></a>Restauración en el nivel de elemento

(término específico de la carga de trabajo)

Restauración de archivos o carpetas individuales del interior de la máquina desde el punto de recuperación.

## <a name="job"></a>Trabajo

Tarea relacionada con la copia de seguridad que crea un usuario o el servicio Azure Backup. Los trabajos pueden ser programados o a petición (ad hoc). Hay distintos tipos de trabajos: copia de seguridad, restauración, configuración de la protección, etc. [Más información sobre los trabajos](backup-azure-monitoring-built-in-monitor.md#backup-jobs-in-backup-center).

## <a name="mabs--azure-backup-server"></a>MABS / Azure Backup Server

(término específico de la carga de trabajo)

Con Azure Backup Server, puede proteger cargas de trabajo de aplicaciones como máquinas virtuales de Hyper-V, Microsoft SQL Server, SharePoint Server, Microsoft Exchange y clientes Windows desde una única consola. Hereda gran parte de la funcionalidad de copia de seguridad de la carga de trabajo de DPM, pero con algunas diferencias. [Más información](backup-azure-microsoft-azure-backup.md)

## <a name="managed-disks"></a>Discos administrados

Consulte la [documentación de los discos administrados](../virtual-machines/managed-disks-overview.md).

## <a name="mars-agent"></a>Agente de MARS

(término específico de la carga de trabajo)

También conocido como **agente de Azure Backup** o **agente de Recovery Services**, sirve para que Azure Backup realice copias de seguridad de los datos de máquinas locales y máquinas virtuales de Azure en un almacén de Recovery Services de copia de seguridad de Azure. [Más información](backup-support-matrix-mars-agent.md).

## <a name="nsg-network-security-group"></a>NSG (grupo de seguridad de red)

Consulte la [documentación de los grupos de seguridad de red](../virtual-network/network-security-groups-overview.md).

## <a name="offline-seeding"></a>Propagación sin conexión

Hace referencia al proceso de transferir la copia de seguridad inicial (completa) sin conexión sin usar el ancho de banda de red. Proporciona un mecanismo para copiar los datos de copia de seguridad en dispositivos de almacenamiento físico que luego se envían a un centro de datos de Azure cercano y se cargan en un almacén de Recovery Services. [Más información](offline-backup-overview.md).

## <a name="on-demand-backup--ad-hoc-backup"></a>Copia de seguridad a petición/ad hoc

Trabajo de copia de seguridad que desencadena un usuario por una necesidad en particular y no se basa en la programación de la copia de seguridad (directiva) que se ha configurado para el recurso.

## <a name="original-location-recovery-olr"></a>Recuperación de ubicación original (OLR)

La que se realiza desde el punto de restauración a la ubicación de origen desde la que se realizaron las copias de seguridad, reemplazándola por el estado almacenado en el punto de recuperación. Cuando se usa la copia de seguridad de máquina virtual de Azure, esto significaría restaurar la máquina virtual en el servidor original donde se realizaron las copias de seguridad. Cuando se usa la copia de seguridad de recursos compartidos de archivos de Azure, esto significaría restaurar los datos en el recurso compartido de archivos de la copia de seguridad.

## <a name="passphrase"></a>Passphrase

(término específico de la carga de trabajo)

Una frase de contraseña se usa para cifrar y descifrar los datos durante la copia de seguridad o la restauración del entrono local o la máquina local mediante el agente de MARS en Azure o desde Azure.

## <a name="private-endpoint"></a>Punto de conexión privado

Consulte la [documentación del punto de conexión privado](../private-link/private-endpoint-overview.md).

## <a name="protected-instance"></a>Instancia protegida

Hace referencia al equipo, físico o servidor virtual, que se usa para configurar la copia de seguridad en Azure.  Desde el **punto de vista de la facturación**, el recuento de instancias protegidas de una máquina depende del tamaño de su front-end. Por lo tanto, una sola instancia de copia de seguridad (como una máquina virtual con copia de seguridad en Azure) puede corresponder a varias instancias protegidas, en función de su tamaño de front-end. [Más información](https://azure.microsoft.com/pricing/details/backup/).

## <a name="rbac-role-based-access-control"></a>Control de acceso basado en rol (RBAC)

Consulte la [documentación de RBAC](../role-based-access-control/overview.md).

## <a name="recovery-point-restore-point-retention-point--point-in-time-pit"></a>Punto de recuperación/punto de restauración/punto de retención/a un momento dado (PIT)

Copia de los datos originales de los que se hace una copia de seguridad. Un punto de retención está asociado a una marca de tiempo, la cual puede usar para restaurar un elemento a un momento dado.

## <a name="recovery-services-vault"></a>Almacén de Recovery Services

Recurso de Azure Resource Manager del tipo *Microsoft.RecoveryServices/vaults*. Actualmente se usan almacenes de Recovery Services para realizar copias de seguridad de las siguientes cargas de trabajo: Máquinas virtuales de Azure, SQL en máquinas virtuales de Azure, SAP HANA en máquinas virtuales de Azure y recursos compartidos de archivos de Azure. También se usa para realizar copias de seguridad de cargas de trabajo locales con MARS, Azure Backup Server (MABS) y System Center DPM. [Más información sobre los almacenes de Recovery Services](backup-azure-recovery-services-vault-overview.md).

## <a name="resource-group"></a>Resource group

Consulte la [documentación de Azure Resource Manager](../azure-resource-manager/management/manage-resource-groups-portal.md#what-is-a-resource-group).

## <a name="rest-api"></a>API DE REST

Consulte la [documentación de las API REST de Azure](/rest/api/azure/).

## <a name="retention-rule"></a>Regla de retención

Regla definida por el usuario que especifica cuánto tiempo se deben conservar las copias de seguridad.

## <a name="rpo-recovery-point-objective"></a>RPO (objetivo de punto de recuperación)

RPO indica la pérdida máxima de datos posible en caso de que se produzca. Esto lo determina la frecuencia de copia de seguridad.

## <a name="rto-recovery-time-objective"></a>RTO (objetivo de tiempo de recuperación)

RTO indica el tiempo máximo posible en el que se deben restaurar los datos al último momento dado disponible después de un escenario de pérdida de datos.

## <a name="scheduled-backup"></a>Copia de seguridad programada

Trabajo de copia de seguridad que se desencadena automáticamente mediante la directiva de copia de seguridad configurada para el elemento determinado.

## <a name="secondary-region--paired-region"></a>Región secundaria/emparejada

Un par regional consta de dos regiones dentro de la misma ubicación geográfica. Una es la región primaria y la otra es la secundaria. Algunos servicios de Azure usan regiones emparejadas (incluido Azure Backup con la configuración GRS) para garantizar la continuidad empresarial y proteger de la pérdida de datos. [Más información](../best-practices-availability-paired-regions.md).

## <a name="soft-delete"></a>Eliminación temporal

Característica que ayuda a proteger de la eliminación accidental los datos de la copia de seguridad. Con la eliminación temporal, aunque un actor malintencionado elimine una copia de seguridad (o se eliminen por accidente datos de la copia de seguridad), los datos de la copia de seguridad se conservan durante un tiempo adicional, lo que permite la recuperación de ese elemento de copia de seguridad sin pérdida de datos. [Más información](backup-azure-security-feature-cloud.md).

## <a name="snapshot"></a>Instantánea

Una instantánea es una copia completa de solo lectura de una unidad de disco duro virtual (VHD) o de un recurso compartido de archivos de Azure. Más información sobre [instantáneas de disco](../virtual-machines/windows/snapshot-copy-managed-disk.md) e [instantáneas de archivos](../storage/files/storage-snapshots-files.md).

## <a name="storage-account"></a>Cuenta de almacenamiento

Consulte la [documentación de la cuenta de almacenamiento](../storage/common/storage-account-overview.md).

## <a name="subscription"></a>Subscription

Una suscripción de Azure es un contenedor lógico que se usa para aprovisionar recursos en Azure. Contiene los detalles de todos los recursos, como máquinas virtuales, bases de datos, etc.

## <a name="system-state-backup"></a>Copia de seguridad del estado de sistema

(término específico de la carga de trabajo)

realiza una copia de seguridad de los archivos del sistema operativo. Esta copia de seguridad le permite recuperar cuando un equipo se inicia, pero los archivos de sistema y el registro se pierden. [Más información](backup-mabs-system-state-and-bmr.md).

## <a name="tenant"></a>Inquilino

Un inquilino es la representación de una organización. Se trata de una instancia dedicada de Azure AD que una organización o el desarrollador de la aplicación reciben cuando crean una relación con Microsoft, como al registrarse en Azure, Microsoft Intune o Microsoft 365.

## <a name="tier"></a>Nivel

Actualmente, Azure Backup admite los siguientes niveles de almacenamiento de copias de seguridad:

### <a name="snapshot-tier"></a>Nivel de instantánea

(Término específico de carga de trabajo) En la primera fase de la copia de seguridad de máquinas virtuales, la instantánea que se toma se almacena junto con el disco. Esta forma de almacenamiento se denomina nivel de instantánea. Las restauraciones de nivel de instantánea son más rápidas (que la restauración desde un almacén) porque eliminan el tiempo de espera para que las instantáneas se copien desde el almacén antes de desencadenar la operación de restauración.

### <a name="vault-standard-tier"></a>Nivel de almacén estándar

Los datos de copia de seguridad de todas las cargas de trabajo que admite Azure Backup se guardan en almacenes con almacenamiento de copia de seguridad, un conjunto de escalado automático de cuentas de almacenamiento que administra Azure Backup. El nivel de almacén estándar es una capa de almacenamiento en línea que permite almacenar una copia aislada de los datos de copia de seguridad en un inquilino administrado por Microsoft, con lo que se crea una capa adicional de protección. En las cargas de trabajo en las que se admite el nivel de instantánea, hay una copia de los datos de copia de seguridad tanto en el nivel de instantánea como en el nivel de almacén estándar. El nivel de almacén estándar garantiza que los datos de copia de seguridad estén disponibles incluso si se elimina o se pone en peligro el origen de datos del que se realiza la copia de seguridad.

## <a name="unmanaged-disk"></a>Disco no administrado

Consulte la [documentación los discos no administrados](../storage/common/storage-disaster-recovery-guidance.md#azure-unmanaged-disks).

## <a name="vault"></a>Almacén

Entidad de almacenamiento de Azure que alberga los datos de las copias de seguridad. También es una unidad de RBAC y de facturación. Actualmente hay dos tipos de almacenes: el de Recovery Services y el de copia de seguridad.

## <a name="vault-credentials"></a>Credenciales de almacén

El archivo de credenciales de almacén es un certificado generado por el portal para cada almacén. Se usa al registrar un servidor local en el almacén. [Más información](backup-azure-dpm-introduction.md).

## <a name="vnet-virtual-network"></a>Red virtual (VNET)

Consulte la [documentación de las redes virtuales](../virtual-network/virtual-networks-overview.md).

## <a name="vss-windows-volume-shadow-copy-service"></a>VSS (Servicio de instantáneas de volumen de Windows)

Consulte la [documentación de VSS](/windows-server/storage/file-server/volume-shadow-copy-service).

## <a name="next-steps"></a>Pasos siguientes

- [Introducción a Azure Backup](backup-overview.md)
- [Arquitectura y componentes de Azure Backup](backup-architecture.md)