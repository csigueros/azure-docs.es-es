---
title: Uso compartido de un disco administrado de Azure entre máquinas virtuales
description: Aprenda sobre el uso compartido de los discos administrados de Azure en varias máquinas virtuales Linux.
author: roygara
ms.service: storage
ms.topic: conceptual
ms.date: 09/03/2021
ms.author: rogarana
ms.subservice: disks
ms.openlocfilehash: 56ba97d5a13744ee034024f510eac70d4f343877
ms.sourcegitcommit: 48500a6a9002b48ed94c65e9598f049f3d6db60c
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/26/2021
ms.locfileid: "129052811"
---
# <a name="share-an-azure-managed-disk"></a>Uso compartido de un disco administrado de Azure

**Se aplica a:** :heavy_check_mark: Máquinas virtuales Linux :heavy_check_mark: Máquinas virtuales Windows :heavy_check_mark: Conjuntos de escalado flexibles :heavy_check_mark: Conjuntos de escalado uniformes

Los discos compartidos de Azure son una nueva característica de los discos administrados de Azure que permite conectar un disco administrado a varias máquinas virtuales (VM) al mismo tiempo. Si adjunta un disco administrado en varias VM, podrá implementar nuevas aplicaciones en clúster o migrar las existentes a Azure.

## <a name="how-it-works"></a>Funcionamiento

Las VM del clúster pueden leer o escribir en el disco adjunto en función de la reserva elegida por la aplicación en clúster mediante las [reservas persistentes de SCSI](https://www.t10.org/members/w_spc3.htm) (SCSI PR). SCSI PR es un estándar del sector que usan las aplicaciones que se ejecutan en el entorno local de la red de área de almacenamiento (SAN). La habilitación de SCSI PR en un disco administrado le permite migrar estas aplicaciones a Azure tal cual.

Los discos administrados compartidos ofrecen almacenamiento en bloque compartido, al que pueden acceder varias VM, y se exponen como números de unidad lógica (LUN). Los LUN se presentan como un iniciador (VM) de un destino (disco). Estos LUN tienen el aspecto de un almacenamiento conectado directamente (DAS) o de una unidad local en la VM.

Los discos administrados compartidos no ofrecen de forma nativa un sistema de archivos totalmente administrado al que se pueda obtener acceso mediante SMB/NFS. Debe usar un administrador de clústeres, como el Clúster de conmutación por error de Windows Server (WSFC) o Pacemaker, que controle la comunicación del nodo de clúster, así como el bloqueo de escritura.

## <a name="limitations"></a>Limitaciones

[!INCLUDE [virtual-machines-disks-shared-limitations](../../includes/virtual-machines-disks-shared-limitations.md)]

### <a name="operating-system-requirements"></a>Requisitos de sistema operativo

Los discos compartidos admiten varios sistemas operativos. Consulte las secciones [Windows](#windows) o [Linux](#linux) en los sistemas operativos compatibles.

## <a name="disk-sizes"></a>Tamaños de disco

[!INCLUDE [virtual-machines-disks-shared-sizes](../../includes/virtual-machines-disks-shared-sizes.md)]

## <a name="sample-workloads"></a>Ejemplo de cargas de trabajo

### <a name="windows"></a>Windows

Los discos compartidos de Azure se admiten en Windows Server 2008 y versiones más recientes. La mayoría de los clústeres basados en Windows se compilan en WSFC, que controla toda la infraestructura central de la comunicación del nodo de clúster, lo que permite que las aplicaciones aprovechen los patrones de acceso en paralelo. WSFC habilita tanto las opciones basadas en CSV como las que no están basadas en CSV en función de la versión de Windows Server. Para obtener más información, consulte [Creación de un clúster de conmutación por error](/windows-server/failover-clustering/create-failover-cluster).

Entre las aplicaciones populares que se ejecutan en WSFC se incluyen:

- [Creación de una FCI con discos compartidos de Azure (SQL Server en VM de Azure)](../azure-sql/virtual-machines/windows/failover-cluster-instance-azure-shared-disks-manually-configure.md)
    - [Migración de la instancia del clúster de conmutación por error a SQL Server en máquinas virtuales de Azure con discos compartidos](../azure-sql/migration-guides/virtual-machines/sql-server-failover-cluster-instance-to-sql-on-azure-vm.md)
- Servidor de archivos de escalabilidad horizontal (SoFS) [plantilla] (https://aka.ms/azure-shared-disk-sofs-template)
- ASCS/SCS de SAP [plantilla] (https://aka.ms/azure-shared-disk-sapacs-template)
- Servidor de archivos para uso general (carga de trabajo IW)
- Disco de perfil de usuario del servidor de Escritorio remoto (RDS UPD)

### <a name="linux"></a>Linux

Los discos compartidos de Azure se admiten en:
- [SUSE SLE HA 15 SP1 y versiones superiores](https://www.suse.com/c/azure-shared-disks-excercise-w-sles-for-sap-or-sle-ha/)
- [Ubuntu 18.04 y versiones posteriores](https://discourse.ubuntu.com/t/ubuntu-high-availability-corosync-pacemaker-shared-disk-environments/14874)
- [RHEL Developer Preview en cualquier versión 8 de RHEL](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/8/html-single/deploying_red_hat_enterprise_linux_8_on_public_cloud_platforms/index?lb_target=production#azure-configuring-shared-block-storage_configuring-rhel-high-availability-on-azure)
- [Oracle Enterprise Linux](https://docs.oracle.com/en/operating-systems/oracle-linux/8/availability/hacluster-1.html)

Los clústeres de Linux pueden usar administradores de clústeres, como [Pacemaker](https://wiki.clusterlabs.org/wiki/Pacemaker). Pacemaker se basa en [Corosync](http://corosync.github.io/corosync/), lo que permite las comunicaciones de clúster para las aplicaciones implementadas en entornos de alta disponibilidad. Algunos sistemas de archivos en clúster comunes incluyen [ocfs2](https://oss.oracle.com/projects/ocfs2/) y [gfs2](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/global_file_system_2/ch-overview-gfs2). Puede usar modelos de agrupación en clústeres basados en la reserva persistente SCSI (SCSI PR) o un dispositivo de bloque STONITH (SBD) para arbitrar el acceso al disco. Al usar SCSI PR, puede manipular las reservas y los registros mediante utilidades como [fence_scsi](http://manpages.ubuntu.com/manpages/eoan/man8/fence_scsi.8.html) y [sg_persist](https://linux.die.net/man/8/sg_persist).

## <a name="persistent-reservation-flow"></a>Flujo de reserva persistente

En el siguiente diagrama se ilustra un ejemplo de aplicación de base de datos en clúster de dos nodos que usa SCSI PR para permitir la conmutación por error de un nodo a otro.

![Clúster de dos nodos. Una aplicación que se ejecuta en el clúster controla el acceso al disco.](media/virtual-machines-disks-shared-disks/shared-disk-updated-two-node-cluster-diagram.png)

El flujo es el siguiente:

1. La aplicación en clúster que se ejecuta tanto en la VM1 de Azure como en la VM2 registra su intención para leer o escribir en el disco.
1. La instancia de la aplicación en la VM1 toma una reserva exclusiva para escribir en el disco.
1. Esta reserva se aplica en el disco de Azure y la base de datos ya puede escribir en el disco de forma exclusiva. Las escrituras de la instancia de la aplicación en la VM2 no se realizarán correctamente.
1. Si la instancia de la aplicación en la VM1 deja de funcionar, la instancia en la VM2 ya puede iniciar una conmutación por error de la base de datos y hacerse cargo del disco.
1. Ahora, esta reserva se aplica en el disco de Azure y este ya no aceptará escrituras de la VM1. Solo aceptará escrituras de la VM2.
1. La aplicación en clúster puede completar la conmutación por error de la base de datos y atender las solicitudes de la VM2.

En el diagrama siguiente se ilustra otra carga de trabajo en clúster común que consta de varios nodos que leen datos del disco para la ejecución de procesos paralelos, como el entrenamiento de modelos de Machine Learning.

![Clúster de VM de cuatro nodos; cada nodo registra la intención que hay que escribir y la aplicación toma la reserva exclusiva para controlar correctamente los resultados de escritura.](media/virtual-machines-disks-shared-disks/shared-disk-updated-machine-learning-trainer-model.png)

El flujo es el siguiente:

1. La aplicación en clúster que se ejecuta en todas las VM registra la intención de leer o escribir en el disco.
1. La instancia de la aplicación en la VM1 toma una reserva exclusiva para escribir en el disco mientras abre las lecturas en el disco de otras VM.
1. Esta reserva se aplica en el disco de Azure.
1. Ya se pueden leer todos los nodos del clúster desde el disco. Solo un nodo reescribe los resultados en el disco en nombre de los demás nodos del clúster.

### <a name="ultra-disks-reservation-flow"></a>Flujo de reserva de Ultra Disks

Ultra Disks ofrece una limitación adicional, para un total de dos limitaciones. Debido a esto, el flujo de reserva de Ultra Disks puede funcionar tal y como se describe en la sección anterior, o puede limitar y distribuir el rendimiento de manera más pormenorizada.

:::image type="content" source="media/virtual-machines-disks-shared-disks/ultra-reservation-table.png" alt-text="Imagen de una tabla que muestra el acceso `ReadOnly` (de solo lectura) o `Read/Write` (de lectura y escritura) para el Reservation Holder (titular de reserva), el Registered (registrado) y Others (otros).":::

## <a name="performance-throttles"></a>Limitaciones de rendimiento

### <a name="premium-ssd-performance-throttles"></a>Limitaciones de rendimiento de SSD Premium

Con SSD Premium, la IOPS y el rendimiento del disco son fijos, por ejemplo, la IOPS de un P30 es 5000. Este valor permanece si el disco se comparte entre 2 máquinas virtuales o 5 máquinas virtuales. Los límites de disco se pueden alcanzar desde una sola máquina virtual o dividirse entre dos o más máquinas virtuales. 

### <a name="ultra-disk-performance-throttles"></a>Limitaciones de rendimiento de Ultra Disks

Ultra Disks tiene la capacidad única de permitirle establecer el rendimiento mediante la exposición de atributos modificables y la posibilidad de modificarlos. De forma predeterminada, solo hay dos atributos modificables, pero las instancias compartidas de Ultra Disks tienen dos atributos adicionales.


|Atributo  |Descripción  |
|---------|---------|
|DiskIOPSReadWrite     |El número total de IOPS permitido en todas las máquinas virtuales que montan el disco compartido con acceso de escritura.         |
|DiskMBpsReadWrite     |El rendimiento total (MB/s) permitido en todas las máquinas virtuales que montan el disco compartido con acceso de escritura.         |
|DiskIOPSReadOnly*     |El número total de IOPS permitido en todas las máquinas virtuales que montan el disco compartido como `ReadOnly`.         |
|DiskMBpsReadOnly*     |El rendimiento total (MB/s) permitido en todas las máquinas virtuales que montan el disco compartido como `ReadOnly`.         |

\* Solo se aplica a instancias compartidas de Ultra Disks.

Las fórmulas siguientes explican cómo se pueden establecer los atributos de rendimiento, ya que el usuario puede modificarlos:

- DiskIOPSReadWrite/DiskIOPSReadOnly: 
    - Límites de IOPS de 300 IOPS/GiB y hasta un máximo de IOPS de 160 K por disco.
    - Mínimo de 100 IOPS
    - DiskIOPSReadWrite  + DiskIOPSReadOnly es al menos 2 IOPS/GiB
- DiskMBpsRead Write/DiskMBpsReadOnly:
    - El límite de rendimiento de un solo disco es de 256 KiB/s por cada IOPS aprovisionada, y hasta 2000 MBps como máximo por disco.
    - El rendimiento mínimo garantizado por disco es 4 KiB/s por cada IOPS aprovisionada, con una base de referencia total mínima de 1 MBps.

#### <a name="examples"></a>Ejemplos

En los siguientes ejemplos se muestran algunos escenarios en los que se muestra cómo la limitación puede funcionar, en concreto, con instancias compartidas de Ultra Disks.

##### <a name="two-nodes-cluster-using-cluster-shared-volumes"></a>Clúster de dos nodos con volúmenes compartidos en clúster

A continuación, se presenta un ejemplo de un WSFC de dos nodos mediante volúmenes compartidos en clúster. Con esta configuración, ambas máquinas virtuales tienen acceso de escritura simultáneo al disco, lo que da lugar a que se divida la limitación `ReadWrite` entre las dos máquinas virtuales y a que no se use la limitación `ReadOnly`.

:::image type="content" source="media/virtual-machines-disks-shared-disks/ultra-two-node-example.png" alt-text="Ejemplo de disco Ultra de dos nodos en CSV":::

##### <a name="two-node-cluster-without-cluster-share-volumes"></a>Clúster de dos nodos sin volúmenes compartidos en clúster

A continuación, se presenta un ejemplo de un WSFC de dos nodos que no usa volúmenes compartidos en clúster. Con esta configuración, solo una máquina virtual tiene acceso de escritura al disco. Esto da como resultado que la limitación `ReadWrite` se use exclusivamente para la máquina virtual principal y que solo la secundaria use la limitación `ReadOnly`.

:::image type="content" source="media/virtual-machines-disks-shared-disks/ultra-two-node-no-csv.png" alt-text="Ejemplo de disco Ultra no CSV de dos nodos en CSV":::

##### <a name="four-node-linux-cluster"></a>Clúster Linux de cuatro nodos

A continuación, se proporciona un ejemplo de un clúster Linux de cuatro nodos con un solo escritor y tres lectores de escalabilidad horizontal. Con esta configuración, solo una máquina virtual tiene acceso de escritura al disco. Esto da como resultado que la limitación `ReadWrite` se use exclusivamente para la máquina virtual principal y que la limitación `ReadOnly` se divida entre las máquinas virtuales secundarias.

:::image type="content" source="media/virtual-machines-disks-shared-disks/ultra-four-node-example.png" alt-text="Ejemplo de limitación en disco Ultra de cuatro nodos":::

#### <a name="ultra-pricing"></a>Precios de Ultra

Los discos compartidos Ultra tienen un precio basado en la capacidad aprovisionada, IOPS aprovisionadas totales (diskIOPSReadWrite + diskIOPSReadOnly) y el rendimiento (MBps) aprovisionado total (diskMBpsReadWrite + diskMBpsReadOnly). No hay ningún cargo adicional por cada montaje de máquina virtual adicional. Por ejemplo, un disco compartido Ultra con la siguiente configuración (diskSizeGB: 1024, DiskIOPSReadWrite: 10 000, DiskMBpsReadWrite: 600, DiskIOPSReadOnly: 100, DiskMBpsReadOnly: 1) se cobra con 1024 GiB, 10 100 IOPS y 601 MBps independientemente de si se monta en dos o cinco máquinas virtuales.

## <a name="frequently-asked-questions"></a>Preguntas más frecuentes

**P: ¿Se admite la característica de discos compartidos para discos no administrados o blobs en páginas?**

**R:** No. La característica solo se admite en el caso de discos Ultra y discos SSD administrados prémium.

**P: ¿En qué regiones se admiten los discos compartidos?**

**R:** Para información regional, consulte nuestro [artículo conceptual](/azure/virtual-machines/disks-shared).

**P: ¿Se pueden usar discos compartidos como disco del sistema operativo?**

**R:** No. Los discos compartidos solo se admiten como discos de datos.

**P: ¿Qué tamaños de disco admiten los discos compartidos?**

**R:** Para conocer los tamaños admitidos, consulte nuestro [artículo conceptual](/azure/virtual-machines/disks-shared).

**P: Si tengo un disco, ¿puedo habilitar discos compartidos en él?**

**R:** Todos los discos administrados que se crean mediante la versión de API 2019-07-01 o una versión posterior pueden habilitar los discos compartidos. Para ello, debe desmontar el disco de todas las máquinas virtuales a las que esté conectado. Después, edite la propiedad maxShares en el disco.

**P: Si ya no quiero usar un disco en modo compartido, ¿cómo puedo deshabilitarlo?**

**R:** Desmonte el disco de todas las máquinas virtuales a las que esté conectado. Luego, cambie la propiedad maxShare del disco por **1**.

**P: ¿Se puede cambiar el tamaño de un disco compartido?**

**R:** Sí.

**P: ¿Se puede habilitar el acelerador de escritura en un disco que también tenga habilitados los discos compartidos?**

**R:** No. No se puede habilitar el acelerador de escritura en un disco que también tenga habilitados discos compartidos.

**P ¿Puedo habilitar el almacenamiento en caché de host en un disco que tenga habilitados los discos compartidos?**

**R:** La única opción de almacenamiento en caché de host admitida es **Ninguno**.

## <a name="next-steps"></a>Pasos siguientes

Si está interesado en habilitar y usar discos compartidos para los discos administrados, continúe con nuestro artículo [Habilitación de discos compartidos](disks-shared-enable.md).