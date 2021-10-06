---
title: Administración de Azure Managed Disks
description: Obtenga información sobre la administración de Azure Managed Disks desde Azure Portal.
ms.topic: conceptual
ms.date: 09/23/2021
ms.openlocfilehash: 4b40a71a71ce543c66bf347ec745fea6ae384779
ms.sourcegitcommit: 3ef5a4eed1c98ce76739cfcd114d492ff284305b
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/24/2021
ms.locfileid: "128708920"
---
# <a name="manage-azure-managed-disks"></a>Administración de Azure Managed Disks

En este artículo se explica cómo administrar Azure Managed Disks desde Azure Portal.

## <a name="stop-protection-preview"></a>Detención de la protección (versión preliminar)


Hay tres maneras de dejar de proteger un disco de Azure:

- **Detener la protección y conservar los datos (Conservar indefinidamente)** : esta opción ayuda a evitar que todos los trabajos de copia de seguridad futuros protejan el disco. Pero el servicio Azure Backup conserva indefinidamente los puntos de recuperación cuya copia de seguridad se ha realizado. Deberá pagar para mantener los puntos de recuperación en el almacén (consulte [Precios de Azure Backup](https://azure.microsoft.com/pricing/details/backup/) para obtener detalles). Puede restaurar el disco en caso necesario. Para reanudar la protección del disco, use la opción **Reanudar copia de seguridad**.

- **Detener la protección y conservar los datos (Conservar según directiva)** : esta opción ayuda a evitar que todos los trabajos de copia de seguridad futuros protejan el disco. Los puntos de recuperación se conservan según la directiva y se cobran conforme a los [Precios de Azure Backup](https://azure.microsoft.com/pricing/details/backup/). Pero el punto de recuperación más reciente se conserva indefinidamente.

- **Detener la protección y eliminar los datos**: esta opción ayuda a evitar que todos los trabajos de copia de seguridad futuros protejan los discos y elimina todos los puntos de recuperación. No va a poder restaurar el disco ni usar la opción **Reanudar copia de seguridad**.

### <a name="stop-protection-and-retain-data"></a>Detener la protección y conservar los datos

1. Vaya al **Centro de copias de seguridad** y seleccione **Azure Disks**.

1. En la lista de instancias de copia de seguridad de discos, seleccione la instancia que quiere conservar.

1. Seleccione **Detener copia de seguridad (versión preliminar)** .

   :::image type="content" source="./media/manage-azure-managed-disks/select-disk-backup-instance-to-stop-inline.png" alt-text="Captura de pantalla que muestra la selección de la instancia de copia de seguridad de disco de Azure que se va a detener." lightbox="./media/manage-azure-managed-disks/select-disk-backup-instance-to-stop-expanded.png":::
 
1. Seleccione una de las siguientes opciones de retención de datos:

   1. Conservar indefinidamente
   1. Conservar según directiva
 
   :::image type="content" source="./media/manage-azure-managed-disks/data-retention-options-for-disk-inline.png" alt-text="Captura de pantalla que muestra las opciones para detener la protección de instancias de copia de seguridad de disco." lightbox="./media/manage-azure-managed-disks/data-retention-options-for-disk-expanded.png":::

   También puede seleccionar el motivo por el que se detienen las copias de seguridad en la lista desplegable.

1. Haga clic en **Detener copia de seguridad**.

1. Seleccione **Confirmar** para detener la protección de datos.

   :::image type="content" source="./media/manage-azure-managed-disks/confirm-stopping-disk-backup-inline.png" alt-text="Captura de pantalla que muestra las opciones de retención de instancias de copia de seguridad de disco que se van a seleccionar." lightbox="./media/manage-azure-managed-disks/confirm-stopping-disk-backup-expanded.png":::

### <a name="stop-protection-and-delete-data"></a>Detener la protección y eliminar los datos

1. Vaya al **Centro de copias de seguridad** y seleccione **Azure Disks**.

1. En la lista de instancias de copia de seguridad de discos, seleccione la instancia que quiere eliminar.

1. Haga clic en **Detener copia de seguridad (versión preliminar)** .

1. Seleccione **Eliminar datos de copia de seguridad**.

   Proporcione el nombre de la instancia de copia de seguridad, el motivo de la eliminación y cualquier otro comentario.

   :::image type="content" source="./media/manage-azure-managed-disks/details-to-stop-disk-backup-inline.png" alt-text="Captura de pantalla de la confirmación para detener la copia de seguridad de discos." lightbox="./media/manage-azure-managed-disks/details-to-stop-disk-backup-expanded.png":::

1. Seleccione **Detener copia de seguridad**.

1. Seleccione **Confirmar** para detener la protección de datos.

   :::image type="content" source="./media/manage-azure-managed-disks/confirm-stopping-disk-backup-inline.png" alt-text="Captura de pantalla que muestra las opciones de retención de instancias de copia de seguridad de disco que se van a seleccionar." lightbox="./media/manage-azure-managed-disks/confirm-stopping-disk-backup-expanded.png":::

## <a name="resume-protection"></a>Reanudación de la protección

Si ha seleccionado la opción de **detener la protección y conservar los datos**, puede reanudar la protección de los discos.

>[!Note]
>Cuando se empieza a proteger un disco, la directiva de copia de seguridad también se aplica a los datos conservados. Se limpian los puntos de recuperación que han expirado según la directiva.

Siga estos pasos:

1. Vaya al **Centro de copias de seguridad** y seleccione **Azure Disks**.

1. En la lista de instancias de copia de seguridad de discos, seleccione la instancia que quiere reanudar.

1. Seleccione **Reanudar copia de seguridad (versión preliminar)** .

   :::image type="content" source="./media/manage-azure-managed-disks/resume-disk-protection-inline.png" alt-text="Captura de pantalla que muestra la opción de reanudar la protección de los discos." lightbox="./media/manage-azure-managed-disks/resume-disk-protection-expanded.png":::

1. Seleccione **Reanudar copia de seguridad**.

   :::image type="content" source="./media/manage-azure-managed-disks/resume-disk-backup-inline.png" alt-text="Captura de pantalla que muestra la opción de reanudar la copia de seguridad de los discos." lightbox="./media/manage-azure-managed-disks/resume-disk-backup-expanded.png":::

## <a name="delete-backup-instance"></a>Eliminación de la instancia de copia de seguridad

Si decide detener todos los trabajos de copia de seguridad programados y eliminar todas las copias de seguridad existentes, use **Eliminar instancia de copia de seguridad**.

>[!Note]
>La eliminación de una instancia de copia de seguridad produce un error si el grupo de recursos de instantáneas se elimina manualmente o se revoca el permiso a la identidad administrada del almacén de copias de seguridad. En estos casos de error, cree temporalmente el grupo de recursos de instantáneas (con el mismo nombre) y proporcione a la identidad administrada del almacén de copias de seguridad los permisos de rol necesarios como se documenta [aquí](/azure/backup/backup-managed-disks-ps#assign-permissions). En la pestaña **Información esencial** de la pantalla **Instancia de copia de seguridad** se puede encontrar el nombre del grupo de recursos de instantáneas. 

Para eliminar una instancia de copia de seguridad de disco, siga estos pasos:

1. Haga clic en **Eliminar** en la pantalla de instancia de copia de seguridad.

   :::image type="content" source="./media/manage-azure-managed-disks/initiate-deleting-backup-instance-inline.png" alt-text="Captura de pantalla que muestra el proceso para eliminar una instancia de copia de seguridad." lightbox="./media/manage-azure-managed-disks/initiate-deleting-backup-instance-expanded.png":::

1. Proporcione detalles de confirmación, incluido el nombre de la instancia de copia de seguridad, el motivo de la eliminación y comentarios adicionales.

   :::image type="content" source="./media/manage-azure-managed-disks/confirm-deleting-backup-instance-inline.png" alt-text="Captura de pantalla que muestra cómo confirmar la eliminación de instancias de copia de seguridad." lightbox="./media/manage-azure-managed-disks/confirm-deleting-backup-instance-expanded.png":::

1. Haga clic en **Eliminar** para confirmar y continuar con la eliminación de la instancia de copia de seguridad.

## <a name="next-steps"></a>Pasos siguientes

[Introducción a los almacenes de Backup](backup-vault-overview.md)
