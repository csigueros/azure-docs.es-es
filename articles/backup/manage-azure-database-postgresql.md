---
title: Administración de un servidor de Azure Database for PostgreSQL
description: Obtenga información sobre cómo administrar servidores de Azure Database for PostgreSQL.
ms.topic: conceptual
ms.date: 09/21/2021
ms.openlocfilehash: 2546279f22df9a39f454b926d9960f38cf42f2fc
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/24/2021
ms.locfileid: "128631255"
---
# <a name="manage-azure-database-for-postgresql-server"></a>Administración de un servidor de Azure Database for PostgreSQL

En este artículo se describe cómo administrar máquinas virtuales de Azure cuya copia de seguridad se realiza con el servicio Azure Backup.

## <a name="change-policy"></a>Cambiar la directiva

Puede cambiar la directiva asociada a una instancia de copia de seguridad.

1. Seleccione la opción **Instancia de copia de seguridad** -> **Cambiar directiva**.


   :::image type="content" source="./media/manage-azure-database-postgresql/change-policy.png" alt-text="Captura de pantalla que muestra la opción para cambiar la directiva.":::
   
1. Seleccione la nueva directiva que desea aplicar a la base de datos.

   :::image type="content" source="./media/manage-azure-database-postgresql/reassign-policy.png" alt-text="Captura de pantalla que muestra la opción para reasignar la directiva.":::

## <a name="stop-protection-preview"></a>Detener protección (versión preliminar)

Hay tres maneras de dejar de proteger un servidor de Azure Database for PostgreSQL.

- **Detener la protección y retener los datos (Retener indefinidamente)** : esta opción lo ayuda a impedir que todos los trabajos de copia de seguridad futuros protejan el servidor de Azure Database for PostgreSQL. Sin embargo, el servicio Azure Backup retendrá los puntos de recuperación de los que se ha realizado una copia de seguridad indefinidamente. Deberá pagar para mantener los puntos de recuperación en el almacén (consulte [Precios de Azure Backup](https://azure.microsoft.com/pricing/details/backup/) para obtener detalles). Si es necesario, podrá restaurar desde estos puntos de recuperación. Para reanudar la protección, use la opción **Reanudar copia de seguridad**.

- **Detener protección y retener datos (Retener según la directiva)** : esta opción lo ayuda a impedir que todos los trabajos de copia de seguridad futuros protejan el servidor de Azure Database for PostgreSQL. Los puntos de recuperación se retendrán según la directiva y se cobrarán según los [precios de Azure Backup](https://azure.microsoft.com/pricing/details/backup/). Sin embargo, el punto de recuperación más reciente se retendrá indefinidamente.

- **Detener la protección y eliminar los datos**: esta opción lo ayuda a impedir que todos los trabajos de copia de seguridad futuros protejan el servidor Azure Database for PostgreSQL y elimine todos los puntos de recuperación. No podrá restaurar la máquina virtual ni usar la opción **Reanudar copia de seguridad**.

### <a name="stop-protection-and-retain-data"></a>Detener protección y retener datos

1. Vaya al **Centro de copia de seguridad** y seleccione **Azure Database for PostgreSQL (versión preliminar)** .

1. En la lista de instancias de copia de seguridad del servidor, seleccione la instancia que desea retener.

1. Seleccione **Detener copia de seguridad (versión preliminar)** .

   :::image type="content" source="./media/manage-azure-database-postgresql/select-postgresql-server-backup-instance-to-delete-inline.png" alt-text="Captura de pantalla que muestra la selección de la instancia de copia de seguridad del servidor de Azure Database for PostgreSQL que se va a detener." lightbox="./media/manage-azure-database-postgresql/select-postgresql-server-backup-instance-to-delete-expanded.png":::

1. Seleccione una de las siguientes opciones de retención de datos:

   1. Retener indefinidamente
   1. Retener según la directiva
   
   :::image type="content" source="./media/manage-azure-database-postgresql/data-retention-options-inline.png" alt-text="Captura de pantalla que muestra las opciones para la retención de datos que se va a seleccionar." lightbox="./media/manage-azure-database-postgresql/data-retention-options-expanded.png":::

   También puede seleccionar el motivo por el que se detienen las copias de seguridad en la lista desplegable.

1. Haga clic en **Detener copia de seguridad**.

1. Seleccione **Confirmar** para detener la copia de seguridad.

   :::image type="content" source="./media/manage-azure-database-postgresql/confirmation-to-stop-backup-inline.png" alt-text="Captura de pantalla de la confirmación para detener la copia de seguridad." lightbox="./media/manage-azure-database-postgresql/confirmation-to-stop-backup-expanded.png":::

### <a name="stop-protection-and-delete-data"></a>Detener protección y eliminar datos

1. Vaya al **Centro de copia de seguridad** y seleccione **Azure Database for PostgreSQL (versión preliminar)** .

1.  En la lista de instancias de copia de seguridad del servidor, seleccione la instancia que desea eliminar.

1. Haga clic en **Detener copia de seguridad (versión preliminar)** .

1. Seleccione **Eliminar datos de copia de seguridad**.

   Proporcione el nombre de la instancia de copia de seguridad, el motivo de la eliminación y cualquier otro comentario.

   :::image type="content" source="./media/manage-azure-database-postgresql/delete-backup-data-and-provide-details-inline.png" alt-text="Captura de pantalla que muestra la opción de eliminar los datos de copia de seguridad y los detalles necesarios para agregarse." lightbox="./media/manage-azure-database-postgresql/delete-backup-data-and-provide-details-expanded.png":::

1. Seleccione **Detener copia de seguridad**.

1. Seleccione **Confirmar** para detener la copia de seguridad.

   :::image type="content" source="./media/manage-azure-database-postgresql/confirmation-to-stop-backup-inline.png" alt-text="Captura de pantalla de la confirmación para detener la copia de seguridad." lightbox="./media/manage-azure-database-postgresql/confirmation-to-stop-backup-expanded.png":::

## <a name="resume-protection"></a>Reanudación de la protección

Si ha seleccionado la opción **Detener protección y retener datos** al detener la copia de seguridad de datos, puede reanudar la protección del servidor dee Azure Database for PostgreSQL.

>[!Note]
>Cuando se empieza a proteger una base de datos, la directiva de copia de seguridad también se aplica a los datos retenidos. Se limpiarán los puntos de recuperación que han expirado según la directiva.

Siga estos pasos:

1. Vaya al **Centro de copia de seguridad** y seleccione **Azure Database for PostgreSQL (versión preliminar)** .

1. En la lista de instancias de copia de seguridad del servidor, seleccione la instancia que desea reanudar.

1. Seleccione **Reanudar copia de seguridad (versión preliminar)** .

   :::image type="content" source="./media/manage-azure-database-postgresql/resume-data-protection-inline.png" alt-text="Captura de pantalla que muestra la opción de reanudar la protección de datos." lightbox="./media/manage-azure-database-postgresql/resume-data-protection-expanded.png":::

1. Seleccione **Reanudar copia de seguridad**.

   :::image type="content" source="./media/manage-azure-database-postgresql/resume-data-backup-inline.png" alt-text="Captura de pantalla que muestra la opción de reanudar la copia de seguridad de datos." lightbox="./media/manage-azure-database-postgresql/resume-data-backup-expanded.png":::

## <a name="next-steps"></a>Pasos siguientes

[Introducción a los almacenes de Backup](backup-vault-overview.md)
