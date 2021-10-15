---
title: Restauración de Azure Database for PostgreSQL
description: Aprenda a restaurar copias de seguridad de Azure Database for PostgreSQL.
ms.topic: how-to
ms.date: 10/01/2021
ms.custom: devx-track-azurecli
ms.openlocfilehash: a5de8e74fb05eea45e5cb730515b3280c4952951
ms.sourcegitcommit: 87de14fe9fdee75ea64f30ebb516cf7edad0cf87
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/01/2021
ms.locfileid: "129359515"
---
# <a name="restore-azure-database-for-postgresql-backups-preview"></a>Restauración de copias de seguridad de Azure Database for PostgreSQL (versión preliminar)

En este artículo se explica cómo restaurar una base de datos en un servidor de Azure PostgreSQL copiadas mediante la copia de seguridad de Azure Backup.

Puede restaurar una base de datos en cualquier servidor de Azure PostgreSQL dentro de la misma suscripción, si el servicio tiene el [conjunto de permisos](backup-azure-database-postgresql-overview.md#azure-backup-authentication-with-the-postgresql-server) adecuado en el servidor de destino.

## <a name="restore-a-database-on-the-target-storage-account"></a>Restauración de una base de datos en la cuenta de Storage de destino


1. Vaya a **Almacén de Backup** -> **Instancias de Backup**. Seleccione una base de datos y haga clic en **Restaurar**.

   :::image type="content" source="./media/restore-azure-database-postgresql/select-database-for-restore-inline.png" alt-text="Captura de pantalla en la que se muestra el proceso para seleccionar una base de datos y restaurarla." lightbox="./media/restore-azure-database-postgresql/select-database-for-restore-expanded.png":::

   También puede navegar a esta página desde el [Centro de copias de seguridad](/azure/backup/backup-center-overview).   
  
1. En la página **Seleccionar punto de restauración**, seleccione un punto de recuperación en la lista de todas las copias de seguridad completas disponibles para la instancia de copia de seguridad seleccionada. De forma predeterminada, se selecciona el punto de recuperación más reciente.

   :::image type="content" source="./media/restore-azure-database-postgresql/select-restore-point-inline.png" alt-text="Captura de pantalla en la que se muestra el proceso para seleccionar un punto de recuperación." lightbox="./media/restore-azure-database-postgresql/select-restore-point-expanded.png":::

   Si el punto de restauración está en el nivel de archivo, debe rehidratar el punto de recuperación antes de restaurarlo. Proporcione los siguientes parámetros adicionales necesarios para la rehidratación:

   - **Prioridad de la rehidratación**: el valor predeterminado es **Estándar**.
   - **Duración de la rehidratación**: la duración máxima de la rehidratación es de 30 días y la mínima, de 10 días. El valor predeterminado es **15 días**. El punto de recuperación se almacena en el **almacén de datos de Backup** durante el período especificado.

1. En la página **Restaurar parámetros**, seleccione uno de estos tipos de restauración: **Restaurar como base de datos** o **Restaurar como archivos**.

   - **Restaurar como base de datos**

     El servidor de destino puede ser el mismo que el servidor de origen. Sin embargo, no se puede sobrescribir la base de datos original. Puede elegir el servidor entre todas las suscripciones, pero debe estar en la misma región que el almacén.

     1. En la lista desplegable **Select key vault and the secret** (Seleccionar el almacén de claves y el secreto), seleccione un almacén que almacene las credenciales para conectarse al servidor de destino.

     1. Seleccione **Revisar y restaurar** para desencadenar una validación para comprobar si el servicio cuenta con [permisos de restauración en el servidor de destino](backup-azure-database-postgresql-overview.md#set-of-permissions-needed-for-azure-postgresql-database-restore). Estos permisos se deben [conceder manualmente](backup-azure-database-postgresql-overview.md#grant-access-on-the-azure-postgresql-server-and-key-vault-manually).

     :::image type="content" source="./media/restore-azure-database-postgresql/restore-as-database-inline.png" alt-text="Captura de pantalla en la que se muestra el tipo de restauración seleccionado como Restaurar como base de datos." lightbox="./media/restore-azure-database-postgresql/restore-as-database-expanded.png":::

   - **Restaurar como archivos: vuelve los archivos de copia de seguridad en la cuenta de almacenamiento de destino (blobs).**

     Puede elegir la cuenta de almacenamiento entre todas las suscripciones, pero debe estar en la misma región que el almacén.     

     1. En la lista desplegable **Seleccionar el contenedor de destino**, seleccione uno de los contenedores filtrados para la cuenta de almacenamiento seleccionada.
     1. Seleccione **Revisar y restaurar** para desencadenar la validación y comprobar si el servicio de copia de seguridad cuenta con los [permisos de restauración en la cuenta de almacenamiento de destino](#restore-permissions-on-the-target-storage-account).

     :::image type="content" source="./media/restore-azure-database-postgresql/restore-as-files-inline.png" alt-text="Captura de pantalla en la que se muestra el tipo de restauración seleccionado como Restaurar como archivos." lightbox="./media/restore-azure-database-postgresql/restore-as-files-expanded.png":::
   
1. Envíe la operación de restauración y haga un seguimiento del trabajo desencadenado en **Trabajos de copia de seguridad**.
   
   :::image type="content" source="./media/restore-azure-database-postgresql/track-triggered-job-inline.png" alt-text="Captura de pantalla en la que se muestra el trabajo desencadenado del que se hace seguimiento en Trabajos de copia de seguridad." lightbox="./media/restore-azure-database-postgresql/track-triggered-job-expanded.png":::

>[!NOTE]
>La compatibilidad con archivos de Azure Database for PostgreSQL está en versión preliminar pública limitada.

## <a name="restore-permissions-on-the-target-storage-account"></a>Permisos de restauración en la cuenta de almacenamiento de destino

Asigne al valor MSI del almacén de Backup permiso para acceder a los contenedores de la cuenta de almacenamiento mediante Azure Portal.

1. Vaya a **Cuenta de almacenamiento** -> **Control de acceso** -> **Agregar asignación de rol**.

1. Seleccione el rol **Colaborador de datos de blobs de almacenamiento** en la lista desplegable **Rol** para el valor MSI del almacén de Backup.

   :::image type="content" source="./media/restore-azure-database-postgresql/assign-vault-msi-permission-to-access-storage-account-containers-azure-portal-inline.png" alt-text="Captura de pantalla en la que se muestra el proceso para asignar al valor MSI del almacén de Backup permiso para acceder a los contenedores de la cuenta de almacenamiento mediante Azure Portal." lightbox="./media/restore-azure-database-postgresql/assign-vault-msi-permission-to-access-storage-account-containers-azure-portal-expanded.png":::

También puede conceder permisos pormenorizados al contenedor específico en el que va a realizar la restauración mediante el comando [az role assignment](/cli/azure/role/assignment) create de la CLI de Azure.

```azurecli
az role assignment create --assignee $VaultMSI_AppId  --role "Storage Blob Data Contributor"   --scope $id
```
Reemplace el parámetro assignee por el valor de _Id. de aplicación_ de la identidad MSI del almacén y modifique el parámetro scope para que haga referencia al contenedor específico. Si desea obtener el valor de **Id. de aplicación** de la identidad MSI del almacén, seleccione **Todas las aplicaciones** en **Tipo de aplicación**. Busque el nombre del almacén y copie el identificador de la aplicación.

 :::image type="content" source="./media/restore-azure-database-postgresql/select-application-type-for-id-inline.png" alt-text="Captura de pantalla en la que se muestra el proceso para obtener el identificador de la aplicación del valor MSI del almacén." lightbox="./media/restore-azure-database-postgresql/select-application-type-for-id-expanded.png":::

 :::image type="content" source="./media/restore-azure-database-postgresql/copy-vault-id-inline.png" alt-text="Captura de pantalla en la que se muestra el proceso para copiar el identificador de la aplicación del almacén." lightbox="./media/restore-azure-database-postgresql/copy-vault-id-expanded.png":::
 
## <a name="next-steps"></a>Pasos siguientes

[Solución de problemas de la copia de seguridad de base de datos de PostgreSQL con Azure Backup](backup-azure-database-postgresql-troubleshoot.md)
