---
title: Migración del almacén de Azure Recovery Services a otra región
description: En este artículo va a aprender a garantizar copias de seguridad continuas después de mover los recursos entre regiones.
ms.topic: conceptual
ms.date: 08/27/2021
ms.custom: references_regions
ms.openlocfilehash: faf6ad49234d9753a0479151bf931714a1eba226
ms.sourcegitcommit: dcf1defb393104f8afc6b707fc748e0ff4c81830
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/27/2021
ms.locfileid: "123114467"
---
# <a name="back-up-resources-in-recovery-services-vault-after-moving-across-regions"></a>Copia de seguridad de recursos en el almacén de Recovery Services después de una migración entre regiones

Azure Resource Mover permite mover varios recursos entre regiones. Al mover los recursos de una región a otra, puede asegurarse de que se mantengan protegidos. Como Azure Backup permite proteger varias cargas de trabajo, es posible que deba realizar algunos pasos para seguir teniendo el mismo nivel de protección en la nueva región.

Para comprender los pasos detallados para lograrlo, vea las secciones siguientes.

>[!Note]
>Azure Backup actualmente no permite el movimiento de datos de copia de seguridad de un almacén de Recovery Services a otro. Para proteger el recurso en la nueva región, este debe registrarse y copiarse en un almacén nuevo o existente de la nueva región. Al mover los recursos de una región a otra, los datos de copia de seguridad de los almacenes de Recovery Services existentes en la región anterior se pueden conservar o eliminar según sus necesidades. Si decide conservar los datos de los almacenes antiguos, incurre en los cargos de copia de seguridad correspondientes.

## <a name="back-up-azure-virtual-machine-after-moving-across-regions"></a>Copia de seguridad de una máquina virtual de Azure después de mover entre regiones

Cuando una máquina virtual (VM) de Azure protegida en un almacén de Recovery Services se mueve de una región a otra, ya no se pueden realizar copias de seguridad de ella en el almacén anterior. Las copias de seguridad del almacén antiguo comienzan a experimentar los errores **BCMV2VMNotFound** o [**ResourceNotFound**](/azure/backup/backup-azure-vms-troubleshoot#320001-resourcenotfound---could-not-perform-the-operation-as-vm-no-longer-exists--400094-bcmv2vmnotfound---the-virtual-machine-doesnt-exist--an-azure-virtual-machine-wasnt-found).

Para proteger la máquina virtual en la nueva región, debe seguir estos pasos:

1. Antes de mover la máquina virtual, [selecciónela en la pestaña **Elementos de copia de seguridad**](/azure/backup/backup-azure-delete-vault#delete-protected-items-in-the-cloud) del panel del almacén existente y seleccione **Detener protección** seguido de los datos de conservación o eliminación según sus requisitos. Cuando los datos de copia de seguridad de una máquina virtual dejan de conservarse, los puntos de recuperación permanecen indefinidamente y no cumplen ninguna directiva. Esto garantiza que siempre tenga los datos de copia de seguridad listos para una restauración.

   >[!Note]
   >La conservación de datos en el almacén anterior conlleva cargos de copia de seguridad. Si ya no quiere conservar los datos para evitar su facturación, debe eliminar los datos de copia de seguridad conservados mediante la opción [Eliminar datos](/azure/backup/backup-azure-manage-vms#delete-backup-data).

1. Mueva la máquina virtual a la nueva región mediante [Azure Resource Mover](/azure/resource-mover/tutorial-move-region-virtual-machines).

1. Comience a proteger la máquina virtual en un almacén de Recovery Services nuevo o existente de la nueva región.
   Si necesita restaurar desde las copias de seguridad anteriores, puede seguir haciéndolo desde el almacén de Recovery Services antiguo siempre que haya elegido conservar los datos de copia de seguridad. 

Los pasos anteriores deberían ayudar a garantizar que los recursos cuenten con copias de seguridad también en la nueva región.

## <a name="back-up-azure-file-share-after-moving-across-regions"></a>Copia de seguridad de un recurso compartido de archivos de Azure después de mover entre regiones

Para mover las cuentas de almacenamiento junto con los recursos compartidos de archivos incluidos en ellas de una región a otra, vea [Traslado de una cuenta de Azure Storage a otra región](/azure/storage/common/storage-account-move).

>[!Note]
>Cuando se copia un recurso compartido de archivos de Azure entre regiones, sus instantáneas asociadas no se trasladan con él. Para mover los datos de instantáneas a la nueva región, debe mover los archivos y directorios individuales de las instantáneas a la cuenta de almacenamiento de la nueva región mediante [AzCopy](/azure/storage/common/storage-use-azcopy-files#copy-all-file-shares-directories-and-files-to-another-storage-account).

Azure Backup ofrece una [solución de administración de instantáneas](/azure/backup/backup-afs#discover-file-shares-and-configure-backup) para Azure Files. Esto significa que no se mueven los datos del recurso compartido de archivos a los almacenes de Recovery Services. Además, como las instantáneas no se mueven con la cuenta de almacenamiento, de hecho tendrá todas las copias de seguridad (instantáneas) en la región existente solamente y protegidas en el almacén existente. Pero puede asegurarse de que los nuevos recursos compartidos de archivos que cree en la nueva región estén protegidos mediante Azure Backup con estos pasos:

1. Comience a proteger el recurso compartido de archivos de Azure copiado en la nueva cuenta de almacenamiento en un almacén de Recovery Services nuevo o existente de la nueva región.  

1. Una vez que el recurso compartido de archivos de Azure se ha copiado en la nueva región, puede optar por detener la protección y conservar o eliminar las instantáneas (y los puntos de recuperación correspondientes) del recurso compartido de archivos de Azure original según sus necesidades. Para ello tiene que seleccionar el recurso compartido de archivos en la pestaña [Elementos de copia de seguridad](/azure/backup/backup-azure-delete-vault#delete-protected-items-in-the-cloud) del panel del almacén original. Cuando los datos de copia de seguridad de un recurso compartido de archivos de Azure dejan de conservarse, los puntos de recuperación permanecen indefinidamente y no cumplen ninguna directiva.
   
   Esto garantiza que siempre tenga instantáneas listas para su restauración desde el almacén anterior. 
 
## <a name="back-up-sql-server-in-azure-vmsap-hana-in-azure-vm"></a>Copia de seguridad de SQL Server en máquinas virtuales de Azure o SAP HANA en Azure VM

Cuando se mueve una máquina virtual con servidores SQL o SAP HANA a otra región, las bases de datos SQL y SAP HANA de esas máquinas virtuales ya no se pueden copiar en el almacén de la región anterior. Para proteger los servidores SQL y SAP HANA que se ejecutan en máquinas virtuales de Azure de la nueva región, debe seguir estos pasos:
 
1. Antes de mover la máquina virtual con SQL Server o SAP HANA a una nueva región, selecciónela en la pestaña [Elementos de copia de seguridad](/azure/backup/backup-azure-delete-vault#delete-protected-items-in-the-cloud) del panel del almacén existente y seleccione las _bases de datos_ cuyas copias de seguridad han de detenerse. Seleccione **Detener protección** seguido de los datos de conservación o eliminación según sus requisitos. Cuando los datos de copia de seguridad dejan de conservarse, los puntos de recuperación permanecen indefinidamente y no cumplen ninguna directiva. Esto garantiza que siempre tenga los datos de copia de seguridad listos para una restauración.

   >[!Note]
   >La conservación de datos en el almacén anterior conlleva cargos de copia de seguridad. Si ya no quiere conservar los datos para evitar su facturación, debe eliminar los datos de copia de seguridad conservados mediante la opción [Eliminar datos](/azure/backup/backup-azure-manage-vms#delete-backup-data).

1. Mueva la máquina virtual con SQL Server o SAP HANA a la nueva región mediante [Azure Resource Mover](/azure/resource-mover/tutorial-move-region-virtual-machines).

1. Comience a proteger la máquina virtual en un almacén de Recovery Services nuevo o existente de la nueva región. Si necesita restaurar desde las copias de seguridad anteriores, puede seguir haciéndolo desde el antiguo almacén de Recovery Services.
 
Los pasos anteriores deberían ayudar a garantizar que los recursos cuenten con copias de seguridad también en la nueva región.