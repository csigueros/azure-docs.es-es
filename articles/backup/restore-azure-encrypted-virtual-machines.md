---
title: Restauración de máquinas virtuales de Azure cifradas
description: Se describe cómo restaurar máquinas virtuales de Azure cifradas con el servicio Azure Backup.
ms.topic: conceptual
ms.date: 08/20/2021
ms.openlocfilehash: 95d665fb2ae2e1dc0427090842cddbecfb3c3706
ms.sourcegitcommit: ef448159e4a9a95231b75a8203ca6734746cd861
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/30/2021
ms.locfileid: "123187816"
---
# <a name="restore-encrypted-azure-virtual-machines"></a>Copia de seguridad y restauración de máquinas virtuales de Azure cifradas

En este artículo se describe cómo restaurar máquinas virtuales (VM) Windows o Linux de Azure con discos cifrados mediante el servicio [Azure Backup](backup-overview.md). Para más información, consulte [Cifrado de copias de seguridad de VM de Azure](backup-azure-vms-introduction.md#encryption-of-azure-vm-backups).


## <a name="before-you-start"></a>Antes de comenzar

Revise las limitaciones conocidas antes de iniciar la restauración de una máquina virtual cifrada.

- Puede realizar una copia de seguridad de VM cifradas con ADE, y restaurarlas, en la misma suscripción.
- Azure Backup admite máquinas virtuales cifradas con claves independientes. Actualmente no se admite ninguna clave que forme parte de un certificado usado para cifrar una máquina virtual.
- Las VM cifradas con ADE no se pueden recuperar a nivel de archivo o carpeta. Deberá recuperar toda la máquina virtual para restaurar archivos y carpetas.
- Al restaurar una VM, no puede usar la opción para [reemplazar la VM existente](backup-azure-arm-restore-vms.md#restore-options) en las VM cifradas con ADE. Esta opción solo se admite con discos administrados sin cifrar.


## <a name="restore-an-encrypted-vm"></a>Restauración de una máquina virtual cifrada

Las máquinas virtuales cifradas solo se pueden restaurar si se restaura el disco de la máquina virtual y se crea una instancia de máquina virtual, como se explica a continuación. Actualmente no se admiten el **reemplazo del disco existente en la máquina virtual existente**, la **creación de una máquina virtual a partir de puntos de restauración** y la **restauración de archivos o de nivel de carpeta**.
 
Siga estos pasos para restaurar máquinas virtuales cifradas:

### <a name="step-1-restore-the-vm-disk"></a>**Paso 1**: Restauración del disco de máquina virtual

1. En **Restaurar configuración** > **Crear nuevo** > **Tipo de restauración**, seleccione **Restaurar discos**.
1. En **Grupo de recursos**, seleccione un grupo de recursos existente para los discos restaurados o cree uno con un nombre único global.
1. En **Ubicación de almacenamiento provisional**, especifique la cuenta de almacenamiento en la que se deben copiar los discos duros virtuales. [Más información](backup-azure-arm-restore-vms.md#storage-accounts).

    ![Selección de un grupo de recursos y una ubicación de ensayo](./media/backup-azure-arm-restore-vms/trigger-restore-operation1.png)

1. Seleccione **Restaurar** para desencadenar la operación de restauración.

Cuando la máquina virtual usa discos administrados y selecciona la opción **Crear máquina virtual**, Azure Backup no usa la cuenta de almacenamiento especificada. En el caso de **Restaurar discos** y **Restauración instantánea**, la cuenta de almacenamiento solo se usa para almacenar la plantilla. Los discos administrados se crean en el grupo de recursos especificado.
Cuando la máquina virtual usa discos no administrados, se restauran como blobs en la cuenta de almacenamiento.

   > [!NOTE]
   > Después de restaurar el disco de VM, puede intercambiar manualmente el disco del sistema operativo de la VM original por el disco de la VM restaurado sin volver a crearlo. [Más información](https://azure.microsoft.com/blog/os-disk-swap-managed-disks/).

### <a name="step-2-recreate-the-virtual-machine-instance"></a>**Paso 2**: Volver a crear la instancia de máquina virtual 

Realice alguna de las siguientes acciones:

- Use la plantilla que se ha generado durante la operación de restauración para personalizar la configuración de la máquina virtual y desencadenar su implementación. [Más información](backup-azure-arm-restore-vms.md#use-templates-to-customize-a-restored-vm).
  >[!NOTE]
   >Al implementar la plantilla, compruebe los contenedores de la cuenta de almacenamiento y la configuración pública o privada.
- Cree una máquina virtual a partir de los discos restaurados mediante PowerShell. [Más información](backup-azure-vms-automation.md#create-a-vm-from-restored-disks).

### <a name="step-3-restore-an-encrypted-linux-vm"></a>**Paso 3**: Restauración de una máquina virtual Linux cifrada

Vuelva a instalar la extensión ADE para que los discos de datos estén abiertos y montados.

## <a name="cross-region-restore-for-an-encrypted-azure-vm"></a>Restauración entre regiones para una máquina virtual de Azure cifrada

Azure Backup admite la restauración entre regiones de máquinas virtuales de Azure cifradas en las [regiones emparejadas de Azure](../best-practices-availability-paired-regions.md). Aprenda a [realizar la restauración entre regiones](backup-create-rs-vault.md#configure-cross-region-restore) para una máquina virtual de Azure cifrada.

## <a name="move-an-encrypted-azure-vm"></a>Traslado de una máquina virtual de Azure cifrada

El traslado de una máquina virtual cifrada entre el almacén o el grupo de recursos es lo mismo que el de una máquina virtual de Azure de la que se ha realizado una copia de seguridad. Vea,

- [Pasos para mover una máquina virtual de Azure a otro almacén de Recovery Services](backup-azure-move-recovery-services-vault.md#move-an-azure-virtual-machine-to-a-different-recovery-service-vault)
- [Pasos para mover una máquina virtual de Azure a otro grupo de recursos o suscripción](../azure-resource-manager/management/move-resource-group-and-subscription.md)


## <a name="next-steps"></a>Pasos siguientes

Si experimenta algún problema, consulte estos artículos:

- [Errores comunes](backup-azure-vms-troubleshoot.md) al realizar una copia de seguridad de máquinas virtuales de Azure cifradas y restaurarlas.
- Problemas de la [extensión de copia de seguridad o del agente de máquina virtual de Azure](backup-azure-troubleshoot-vm-backup-fails-snapshot-timeout.md).



