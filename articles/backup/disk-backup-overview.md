---
title: Introducción a Azure Disk Backup
description: Obtenga información sobre la solución Azure Disk Backup.
ms.topic: conceptual
ms.date: 05/27/2021
ms.openlocfilehash: f1c27241e0b61cc4ae491f7bf4a4281d24cd09b6
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/13/2021
ms.locfileid: "121728908"
---
# <a name="overview-of-azure-disk-backup"></a>Introducción a la Azure Disk Backup

Azure Disk Backup es una solución de copia de seguridad nativa y basada en la nube que protege los datos en los discos administrados. Se trata de una solución sencilla, segura y rentable que le permite configurar la protección de los discos administrados en unos pocos pasos. Asimismo, le garantiza la recuperación de los datos en caso de que se produzca un desastre.

Azure Disk Backup ofrece una solución de tipo "llave en mano" que le proporciona la oportunidad de administrar el ciclo de vida de las instantáneas para los discos administrados; para ello, solo debe automatizar la creación periódica de instantáneas y guardarlas durante la duración configurada mediante la directiva de copia de seguridad. Puede administrar las instantáneas de disco sin costos de infraestructura, sin necesidad de realizar un scripting personalizado y sin sufrir ninguna sobrecarga de administración. Esta solución de copia de seguridad coherente con los bloqueos realiza en un momento dado una copia de seguridad de un disco administrado mediante [instantáneas incrementales](../virtual-machines/disks-incremental-snapshots.md), y admite la realización de varias copias de seguridad al día. También es una solución sin agente que no afecta al rendimiento de las aplicaciones de producción. Igualmente, admite las copias de seguridad y la restauración de los discos de datos y del sistema operativo (incluidos los discos compartidos), tanto si están conectados actualmente a una máquina virtual de Azure en ejecución como si no.

Si necesita realizar una copia de seguridad de la máquina virtual coherente con la aplicación que incluya los discos de datos, o si necesita tener una opción para restaurar toda la máquina virtual a partir de una copia de seguridad, restaurar un archivo o una carpeta o restaurar a una región secundaria, use la solución de [copia de seguridad de Azure VM](backup-azure-vms-introduction.md). Azure Backup ofrece compatibilidad en paralelo con la copia de seguridad de los discos administrados mediante Disk Backup, además de soluciones de [copia de seguridad de Azure VM](./backup-azure-vms-introduction.md). Esto resulta útil cuando es necesario realizar una vez al día copias de seguridad de máquinas virtuales coherentes con la aplicación y también copias de seguridad más frecuentes de los discos del sistema operativo o de un disco de datos específico que sean coherentes con los bloqueos y que no afecten al rendimiento de la aplicación de producción.

Azure Disk Backup está integrado con el Centro de copias de seguridad, lo que proporciona una **experiencia única de administración unificada** en Azure para que las empresas controlen, supervisen, administren y analicen las copias de seguridad a gran escala.

## <a name="key-benefits-of-disk-backup"></a>Principales ventajas de Disk Backup

Azure Disk Backup es una solución sin agente y coherente ante los bloqueos que usa [instantáneas incrementales](../virtual-machines/disks-incremental-snapshots.md) y ofrece las siguientes ventajas:

- Copias de seguridad más frecuentes y rápidas sin interrumpir la máquina virtual.
- No afecta al rendimiento de la aplicación de producción.
- No tiene problemas de seguridad, ya que no es necesario ejecutar scripts personalizados ni instalar agentes.
- Es una solución rentable para realizar copias de seguridad de discos específicos, si se compara con las copias de seguridad de toda una máquina virtual.

La solución Azure Disk Backup es útil en los siguientes casos:

- Si debe realizar varias copias de seguridad al día sin que la aplicación esté inactiva.
- Si hay aplicaciones que se ejecutan en un escenario de clúster, tanto el clúster de conmutación por error de Windows Server como los clústeres de Linux escriben en discos compartidos.
- Si necesita específicamente realizar una copia de seguridad sin agente debido a problemas de seguridad o de rendimiento en la aplicación.
- Si la copia de seguridad coherente con la aplicación de la VM no es factible, ya que las aplicaciones de línea de negocio no admiten el Servicio de instantáneas de volumen (VSS).

Considere la posibilidad de usar Azure Disk Backup en estos casos:

- Si una aplicación crítica se ejecuta en una instancia de Azure Virtual Machine que solicita varias copias de seguridad al día para así cumplir el objetivo de punto de recuperación, pero sin afectar al entorno de producción o al rendimiento de la aplicación.
- Si la organización o el reglamento del sector restringen la instalación de agentes debido a problemas de seguridad.
- Si la ejecución de scripts personalizados anteriores o posteriores y la invocación de operaciones de inmovilización y reanudación en máquinas virtuales Linux para obtener una copia de seguridad coherente con la aplicación supone una sobrecarga excesiva en la disponibilidad de la carga de trabajo de producción.
- Si las aplicaciones contenedorizadas que se ejecutan en Azure Kubernetes Service (clúster de AKS) usan discos administrados como almacenamiento persistente. En la actualidad, debe realizar las copias de seguridad del disco administrado mediante scripts de automatización que son difíciles de administrar.
- Si un disco administrado contiene datos empresariales críticos, se usa como un recurso compartido de archivos o contiene archivos de copia de seguridad de la base de datos, y quiere optimizar el costo de las copias de seguridad sin tener que invertir en la solución de copia de seguridad de Azure VM.
- Si tiene muchas máquinas virtuales de un solo disco de Linux y Windows (es decir, una máquina virtual con un solo disco de sistema operativo y sin discos de datos conectados) que hospedan el servidor web o máquinas sin estado, o que actúan como un entorno de ensayo con opciones de configuración de la aplicación, y necesita una solución de copia de seguridad rentable para proteger el disco del sistema operativo. Por ejemplo, para desencadenar una copia de seguridad rápida a petición antes de actualizar o aplicar revisiones a la máquina virtual.
- Si una máquina virtual ejecuta una configuración del sistema operativo que no es compatible con la solución de copia de seguridad de Azure VM (por ejemplo, el servidor de Windows 2008 de 32 bits).

## <a name="how-the-backup-and-restore-process-works"></a>Cómo funciona el proceso de restauración y de copia de seguridad

- El primer paso para configurar la copia de seguridad de Azure Managed Disks es crear un [almacén de Azure Backup](backup-vault-overview.md). El almacén proporciona una vista consolidada de las copias de seguridad configuradas en diferentes cargas de trabajo. La copia de seguridad de discos de Azure solo admite la copia de seguridad del nivel operativo. No se admite la copia de copias de seguridad en el nivel de acceso del almacén. Por consiguiente, el valor de redundancia de almacenamiento del almacén de Azure Backup (LRS/GRS) no se aplica a las copias de seguridad almacenadas en el nivel operativo.

- A continuación, cree una directiva de copia de seguridad que le permita configurar la frecuencia de las copias de seguridad y la duración de la retención.

- Para configurar una copia de seguridad, vaya al almacén de Backup, asigne una directiva de copia de seguridad, seleccione el disco administrado del que se debe realizar la copia de seguridad y proporcione el grupo de recursos en el que se almacenarán y administrarán las instantáneas. Azure Backup desencadena automáticamente los trabajos de copia de seguridad programados que crean una instantánea incremental del disco según la frecuencia de copia de seguridad. Las instantáneas más antiguas se eliminarán según la duración de retención especificada en la directiva de copia de seguridad.

- Azure Backup usa [instantáneas incrementales](../virtual-machines/disks-incremental-snapshots.md#restrictions) del disco administrado. Las instantáneas incrementales son una forma rentable de realizar copias de seguridad en un momento dado de los discos administrados que se facturan en función de los cambios diferenciales que se realizaron en el disco desde la última instantánea. Siempre se almacenan en el almacenamiento HDD estándar más rentable, independientemente del tipo de almacenamiento de los discos principales. La primera instantánea del disco ocupará el tamaño que use el disco, y las instantáneas incrementales consecutivas almacenarán cambios diferenciales en el disco desde la última instantánea. Azure Backup asigna automáticamente una etiqueta a las instantáneas que crea para identificarlas de forma única. 

- Una vez configurada la copia de seguridad de un disco administrado, se creará una instancia de copia de seguridad en el almacén de Backup. Con la instancia de copia de seguridad puede buscar el estado de las operaciones de copia de seguridad, desencadenar copias de seguridad a petición y realizar operaciones de restauración. Asimismo, también puede ver el estado de las copias de seguridad en varios almacenes e instancias de copia de seguridad mediante el Centro de copia de seguridad, que proporciona un único panel de visualización sencilla.

- Para realizar una restauración, solo tiene que seleccionar el punto de recuperación desde el que desea restaurar el disco. Proporcione el grupo de recursos donde se creará el disco restaurado a partir de la instantánea. Azure Backup proporciona una experiencia de restauración instantánea, ya que las instantáneas se almacenan localmente en la suscripción.

- El almacén de Backup usa la identidad administrada para obtener acceso a otros recursos de Azure. Para configurar la copia de seguridad de un disco administrado y realizar una restauración a partir de una copia de seguridad anterior, la identidad administrada del almacén de Backup debe tener un conjunto de permisos en el disco de origen, el grupo de recursos de instantáneas donde se crean y administran las instantáneas y el grupo de recursos de destino en el que quiere restaurar la copia de seguridad. Puede conceder permisos a la identidad administrada mediante el control de acceso basado en roles de Azure (Azure RBAC). Tenga en cuenta que una identidad administrada es una entidad de servicio de un tipo especial que solo se puede usar con recursos de Azure. Obtenga más información sobre las [identidades administradas](../active-directory/managed-identities-azure-resources/overview.md).

- Actualmente, Azure Disk Backup admite la copia de seguridad operativa de discos administrados y no guarda las copias de seguridad en el almacén de Backup. Consulte la [matriz de compatibilidad](disk-backup-support-matrix.md) para obtener una lista detallada de los escenarios admitidos y no admitidos y la disponibilidad de la región.

## <a name="pricing"></a>Precios

Azure Backup usa [instantáneas incrementales](../virtual-machines/disks-incremental-snapshots.md) del disco administrado. Las instantáneas incrementales se cobran por GiB del almacenamiento ocupado por los cambios diferenciales desde la última instantánea. Por ejemplo, si usa un disco administrado con un tamaño aprovisionado de 128 GiB, con 100 GiB usados, la primera instantánea incremental se factura solo por el tamaño usado de 100 GiB. Se agregan 20 GiB de datos en el disco antes de crear la segunda instantánea. Ahora, la segunda instantánea incremental se factura por solo 20 GiB. 

Las instantáneas incrementales siempre se guardan en el almacenamiento estándar, independientemente del tipo de almacenamiento de los discos administrados por el elemento principal, y se cobran en función de los precios del almacenamiento estándar. Por ejemplo, las instantáneas incrementales de un SSD prémium-disco administrado se almacenan en el almacenamiento estándar. De forma predeterminada, se almacenan en ZRS en aquellas regiones que admiten ZRS. De lo contrario, se usa el almacenamiento con redundancia local (LRS). El precio por GiB de ambas opciones, LRS y ZRS, es el mismo. 

Las instantáneas que crea Azure Backup se almacenan en el grupo de recursos en la suscripción de Azure y se aplican cargos por almacenamiento de instantáneas. Para más información sobre los precios de las instantáneas, consulte los [precios de Managed Disk](https://azure.microsoft.com/pricing/details/managed-disks/). Dado que las instantáneas no se copian en el almacén de Backup, Azure Backup no cobra una cuota por instancia protegida y no se aplica el costo del almacenamiento de copia de seguridad. 

Durante una operación de copia de seguridad, el servicio Azure Backup crea una cuenta de almacenamiento en el grupo de recursos de instantáneas, donde se almacenan las instantáneas. Las instantáneas incrementales del disco administrado son recursos de Azure Resource Manager creados en el grupo de recursos, no en una cuenta de almacenamiento. 

La cuenta de almacenamiento se usa para almacenar metadatos para cada punto de recuperación. El servicio Azure Backup crea un contenedor de blobs por cada instancia de copia de seguridad de disco. En cada punto de recuperación, se crea un blob en bloques para almacenar información de metadatos que describe el punto de recuperación, como la suscripción, el identificador de disco, los atributos de disco, etc., que ocupa un espacio pequeño (en unos pocos KiB). 

La cuenta de almacenamiento se crea como almacenamiento con redundancia de zona geográfica del Agente de reconfiguración si la región admite la redundancia zonal. Si la región no la admite, la cuenta de almacenamiento se crea como almacenamiento con redundancia de zona geográfica del Agente de reconfiguración. Si la directiva existente impide la creación de cuentas de almacenamiento en la suscripción o el grupo de recursos con redundancia de GRS, la cuenta de almacenamiento se crea como LRS. La cuenta de almacenamiento creada es De uso general v2, donde los blobs en bloques se almacenan en el nivel de acceso frecuente del contenedor de blobs. Se le cobrará por la cuenta de almacenamiento en función de la redundancia de la cuenta de almacenamiento. Estos cargos se cobran por el tamaño de los blobs en bloques. Sin embargo, será una cantidad mínima, ya que solo almacena metadatos, que son unos pocos KiB por punto de recuperación. 

El número de puntos de recuperación viene determinado por la directiva de copia de seguridad que se usa para configurar las copias de seguridad de las instancias de copia de seguridad del disco. Los blobs en bloques antiguos se eliminan de acuerdo con el proceso de recolección de elementos no utilizados, ya que se suprimen los puntos de recuperación más antiguos correspondientes.

## <a name="next-steps"></a>Pasos siguientes

[Matriz de compatibilidad de Azure Disk Backup](disk-backup-support-matrix.md)
