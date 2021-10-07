---
title: Migración del almacén de Azure Recovery Services a otra región
description: En este artículo va a aprender a garantizar copias de seguridad continuas después de mover los recursos entre regiones.
ms.topic: conceptual
ms.date: 09/24/2021
ms.custom: how-to
ms.openlocfilehash: 658b4965c008105957165a858987442f23630c14
ms.sourcegitcommit: 48500a6a9002b48ed94c65e9598f049f3d6db60c
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/26/2021
ms.locfileid: "129058887"
---
# <a name="back-up-resources-in-recovery-services-vault-after-moving-across-regions"></a>Copia de seguridad de recursos en el almacén de Recovery Services después de una migración entre regiones

Azure Resource Mover permite mover varios recursos entre regiones. Al trasladar los recursos de una región a otra, puede asegurarse de que se mantengan protegidos. Como Azure Backup permite proteger varias cargas de trabajo, es posible que deba realizar algunos pasos para seguir teniendo el mismo nivel de protección en la nueva región.

Para comprender los pasos detallados para lograrlo, consulte las secciones siguientes.

>[!Note]
>Azure Backup actualmente no permite el traslado de datos de copia de seguridad de un almacén de Recovery Services a otro. Para proteger el recurso en la nueva región, este debe registrarse y copiarse en un almacén nuevo o existente de la nueva región. Al trasladar los recursos de una región a otra, los datos de copia de seguridad de los almacenes de Recovery Services existentes en la región anterior se pueden conservar o eliminar según sus necesidades. Si decide conservar los datos de los almacenes antiguos, incurre en los cargos de copia de seguridad correspondientes.

## <a name="back-up-azure-virtual-machine-after-moving-across-regions"></a>Copia de seguridad de una máquina virtual de Azure después de un traslado entre regiones

Cuando una máquina virtual (VM) de Azure protegida en un almacén de Recovery Services se traslada de una región a otra, ya no se pueden hacer copias de seguridad de ella en el almacén anterior. Las copias de seguridad del almacén anterior empezarán a devolver los errores **BCMV2VMNotFound** o [**ResourceNotFound**](./backup-azure-vms-troubleshoot.md#320001-resourcenotfound---could-not-perform-the-operation-as-vm-no-longer-exists--400094-bcmv2vmnotfound---the-virtual-machine-doesnt-exist--an-azure-virtual-machine-wasnt-found). Para obtener más información sobre cómo proteger las máquinas virtuales en la nueva región, consulte las secciones siguientes.

### <a name="prepare-to-move-azure-vms"></a>Preparación del traslado de las máquinas virtuales de Azure

Antes de trasladar una máquina virtual, asegúrese de que se cumplen los requisitos previos siguientes:

1. Consulte los [requisitos previos asociados al traslado de una máquina virtual](/azure/resource-mover/tutorial-move-region-virtual-machines#prerequisites) y asegúrese de que la máquina virtual es apta para el traslado.
1. [Seleccione la máquina virtual en la pestaña **Elementos de copia de seguridad**](./backup-azure-delete-vault.md#delete-protected-items-in-the-cloud) del panel del almacén existente y seleccione **Detener protección** seguido de los datos de conservación o eliminación según sus requisitos. Cuando los datos de copia de seguridad de una VM se detienen con datos de retención, los puntos de recuperación se conservan indefinidamente y no cumplen ninguna directiva. Esto garantiza que siempre tenga los datos de copia de seguridad listos para una restauración.
   >[!Note]
   >La conservación de datos en el almacén anterior conlleva cargos de copia de seguridad. Si ya no quiere conservar los datos para evitar su facturación, debe eliminar los datos de copia de seguridad conservados mediante la opción [Eliminar datos](./backup-azure-manage-vms.md#delete-backup-data).
1. Asegúrese de que las máquinas virtuales están activadas. Todos los discos de las máquinas virtuales que tienen que estar disponibles en la región de destino están conectados a las máquinas virtuales y se han inicializado.
1. Compruebe que las máquinas virtuales dispongan de los certificados raíz de confianza más recientes, así como de una lista actualizada de revocación de certificados (CRL). Para ello:
   - En las máquinas virtuales Windows, instale las actualizaciones de Windows más recientes.
   - En las máquinas virtuales Linux, consulte las indicaciones del distribuidor para asegurarse de que las máquinas disponen de los certificados y la lista de revocación de certificados más recientes.
1. Permita la conectividad saliente desde las máquinas virtuales:
   - Si utiliza un proxy de firewall basado en direcciones URL para controlar la conectividad de salida, debe facilitar el acceso a [estas URL](/azure/resource-mover/support-matrix-move-region-azure-vm#url-access).
   - Si utiliza reglas de grupo de seguridad de red (NSG) para controlar la conectividad de salida, cree [estas reglas de etiquetas de servicio](/azure/resource-mover/support-matrix-move-region-azure-vm#nsg-rules).

### <a name="move-azure-vms"></a>Traslado de máquinas virtuales de Azure

Traslade la VM a la nueva región mediante [Azure Resource Mover](../resource-mover/tutorial-move-region-virtual-machines.md).

### <a name="protect-azure-vms-using-azure-backup"></a>Protección de las máquinas virtuales de Azure mediante Azure Backup

Comience a proteger la VM de un almacén de Recovery Services nuevo o existente de la nueva región. Si necesita restaurar desde las copias de seguridad anteriores, aún puede hacerlo desde el almacén de Recovery Services anterior siempre que haya elegido conservar los datos de copia de seguridad. 

Los pasos anteriores deberían ayudar a garantizar que los recursos cuenten con copias de seguridad también en la nueva región.

## <a name="back-up-azure-file-share-after-moving-across-regions"></a>Copia de seguridad de un recurso compartido de archivos de Azure después de un traslado entre regiones

Azure Backup ofrece una [solución de administración de instantáneas](./backup-afs.md) para Azure Files. Esto significa que no se trasladan los datos del recurso compartido de archivos a los almacenes de Recovery Services. Además, como las instantáneas no se trasladan con la cuenta de almacenamiento, de hecho tendrá todas las copias de seguridad (instantáneas) en la región existente solamente y protegidas en el almacén existente. Sin embargo, si traslada las cuentas de almacenamiento junto con los recursos compartidos de archivos entre regiones o crea nuevos recursos compartidos de archivos en la nueva región, consulte las secciones siguientes para asegurarse de que Azure Backup las protege.

### <a name="prepare-to-move-azure-file-share"></a>Preparación del traslado del recurso compartido de archivos de Azure

Antes de trasladar la cuenta de almacenamiento, asegúrese de que se cumplen los siguientes requisitos previos:

1.  Consulte los [requisitos previos para trasladar una cuenta de almacenamiento](/azure/storage/common/storage-account-move?tabs=azure-portal#prerequisites). 
1. Exporte y modifique una plantilla de traslado de recursos. Para obtener más información, consulte [Preparación de la cuenta de almacenamiento para el traslado de región](/azure/storage/common/storage-account-move?tabs=azure-portal#prepare).

### <a name="move-azure-file-share"></a>Traslado de un recurso compartido de archivos de Azure

Para trasladar las cuentas de almacenamiento junto con los recursos compartidos de archivos de Azure incluidos en ellas de una región a otra, consulte [Traslado de una cuenta de Azure Storage a otra región](../storage/common/storage-account-move.md).

>[!Note]
>Cuando se copia un recurso compartido de archivos de Azure entre regiones, sus instantáneas asociadas no se trasladan con él. Para trasladar los datos de instantáneas a la nueva región, debe trasladar los archivos y directorios individuales de las instantáneas a la cuenta de almacenamiento de la nueva región mediante [AzCopy](../storage/common/storage-use-azcopy-files.md#copy-all-file-shares-directories-and-files-to-another-storage-account).

### <a name="protect-azure-file-share-using-azure-backup"></a>Protección del recurso compartido de archivos de Azure mediante Azure Backup

Comience a proteger el recurso compartido de archivos de Azure copiado en la nueva cuenta de almacenamiento en un almacén de Recovery Services nuevo o existente de la nueva región.  

Una vez que el recurso compartido de archivos de Azure se copie en la nueva región, puede optar por detener la protección y conservar o eliminar las instantáneas (y los puntos de recuperación correspondientes) del recurso compartido de archivos de Azure original según sus necesidades. Para ello tiene que seleccionar el recurso compartido de archivos en la [pestaña Elementos de copia de seguridad](./backup-azure-delete-vault.md#delete-protected-items-in-the-cloud) del panel del almacén original. Cuando los datos de copia de seguridad de un recurso compartido de archivos de Azure dejan de conservarse, los puntos de recuperación se conservan indefinidamente y no cumplen ninguna directiva.
   
Esto garantiza que siempre tenga instantáneas listas para su restauración desde el almacén anterior. 
 
## <a name="back-up-sql-serversap-hana-in-azure-vm-after-moving-across-regions"></a>Realización de una copia de seguridad de SQL Server o SAP HANA en la VM de Azure después de trasladarse entre regiones

Cuando se traslada una VM con servidores SQL o SAP HANA a otra región, las bases de datos SQL y SAP HANA de esas VM ya no se pueden copiar en el almacén de la región anterior. Para proteger los servidores SQL y SAP HANA que se ejecutan en la VM de Azure de la nueva región, consulte las secciones siguientes.

### <a name="prepare-to-move-sql-serversap-hana-in-azure-vm"></a>Preparación del traslado de SQL Server o SAP HANA en la VM de Azure

Antes de trasladar SQL Server o SAP HANA en ejecución en una VM a una nueva región, asegúrese de que se cumplen los siguientes requisitos previos:

1. Consulte los [requisitos previos asociados al traslado de una máquina virtual](/azure/resource-mover/tutorial-move-region-virtual-machines#prerequisites) y asegúrese de que la máquina virtual es apta para el traslado. 
1. Seleccione la VM en la [pestaña Elementos de copia de seguridad](./backup-azure-delete-vault.md#delete-protected-items-in-the-cloud) del panel del almacén existente y seleccione las _bases de datos_ cuyas copias de seguridad han de detenerse. Seleccione **Detener protección** seguido de los datos de conservación o eliminación según sus requisitos. Cuando los datos de copia de seguridad se detienen con datos de retención, los puntos de recuperación se conservan indefinidamente y no cumplen ninguna directiva. Esto garantiza que siempre tenga los datos de copia de seguridad listos para una restauración.
   >[!Note]
   >La conservación de datos en el almacén anterior conlleva cargos de copia de seguridad. Si ya no quiere conservar los datos para evitar su facturación, debe eliminar los datos de copia de seguridad conservados mediante la opción [Eliminar datos](./backup-azure-manage-vms.md#delete-backup-data).
1. Asegúrese de que las máquinas virtuales que se van a trasladar están activadas. Todos los discos de las máquinas virtuales que tienen que estar disponibles en la región de destino están conectados a las máquinas virtuales y se han inicializado.
1. Compruebe que las máquinas virtuales dispongan de los certificados raíz de confianza más recientes, así como de una lista actualizada de revocación de certificados (CRL). Para ello:
   - En las máquinas virtuales Windows, instale las actualizaciones de Windows más recientes.
   - En las máquinas virtuales Linux, consulte las indicaciones del distribuidor y asegúrese de que las máquinas disponen de los certificados y la lista de revocación de certificados más recientes.
1. Permita la conectividad saliente desde las máquinas virtuales:
   - Si utiliza un proxy de firewall basado en direcciones URL para controlar la conectividad de salida, debe facilitar el acceso a [estas URL](/azure/resource-mover/support-matrix-move-region-azure-vm#url-access).
   - Si utiliza reglas de grupo de seguridad de red (NSG) para controlar la conectividad de salida, cree [estas reglas de etiquetas de servicio](/azure/resource-mover/support-matrix-move-region-azure-vm#nsg-rules).

### <a name="move-sql-serversap-hana-in-azure-vm"></a>Traslado de SQL Server o SAP HANA en una VM de Azure

Traslade la VM a la nueva región mediante [Azure Resource Mover](../resource-mover/tutorial-move-region-virtual-machines.md).

### <a name="protect-sql-serversap-hana-in-azure-vm-using-azure-backup"></a>Protección de SQL Server o SAP HANA en una VM de Azure mediante Azure Backup

Comience a proteger la VM en un almacén de Recovery Services nuevo o existente de la nueva región. Si necesita restaurar desde las copias de seguridad anteriores, todavía puede hacerlo desde el antiguo almacén de Recovery Services.
 
Los pasos anteriores deberían ayudar a garantizar que los recursos cuenten con copias de seguridad también en la nueva región.