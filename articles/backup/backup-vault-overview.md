---
title: Información general de los almacenes de Backup
description: Información general de los almacenes de Backup.
ms.topic: conceptual
ms.date: 09/08/2021
ms.custom: references_regions
ms.openlocfilehash: 8d617dd2d2b1fa61a797d14c709ce285dc28b9bf
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/13/2021
ms.locfileid: "124799538"
---
# <a name="backup-vaults-overview"></a>Información general de los almacenes de Backup

En este artículo se describen las características de un almacén de Backup. Un almacén de Backup es una entidad de almacenamiento de Azure que hospeda los datos de copia de seguridad para ciertas cargas de trabajo más recientes que Azure Backup admite. Puede usar almacenes de Backup para almacenar los datos de copia de seguridad de varios servicios de Azure, como servidores de Azure Database for PostgreSQL, y cargas de trabajo más recientes que Azure Backup admitirá. Los almacenes de Backup facilitan la tarea de organizar los datos de copia de seguridad, al mismo tiempo que reducen al mínimo la sobrecarga administrativa. Los almacenes de Backup se basan en el modelo de Azure Resource Manager de Azure, que proporciona características como las siguientes:

- **Funcionalidades mejoradas para ayudar a proteger datos de copia de seguridad**: con los almacenes de Backup, Azure Backup proporciona funcionalidades de seguridad para proteger las copias de seguridad en la nube. Estas características de seguridad garantizan que puede proteger las copias de seguridad y recuperar datos de forma segura, incluso si los servidores de producción y copia de seguridad están en peligro. [Más información](backup-azure-security-feature.md)

- **Control de acceso basado en roles de Azure (Azure RBAC)** : Azure RBAC permite un control muy detallado de la administración de acceso en Azure. [Azure proporciona diversas funciones integradas](../role-based-access-control/built-in-roles.md) y Azure Backup tiene tres [roles integrados para administrar puntos de recuperación](backup-rbac-rs-vault.md). Los almacenes de Backup son compatibles con Azure RBAC, que restringe el acceso de copia de seguridad y restauración al conjunto definido de roles de usuario. [Más información](backup-rbac-rs-vault.md)

## <a name="storage-settings-in-the-backup-vault"></a>Configuración de almacenamiento en el almacén de Backup

Un almacén de Backup es una entidad que almacena las copias de seguridad y los puntos de recuperación creados a lo largo del tiempo. El almacén de Backup también contiene las directivas de copia de seguridad asociadas a las máquinas virtuales protegidas.

- Azure Backup administra automáticamente el almacenamiento para el almacén. Elija la redundancia de almacenamiento que se ajuste a sus necesidades empresariales al crear el almacén de Backup.

- Para obtener más información acerca de la redundancia de almacenamiento, consulte estos artículos sobre redundancia [geográfica](../storage/common/storage-redundancy.md#geo-redundant-storage) y [local](../storage/common/storage-redundancy.md#locally-redundant-storage).

## <a name="encryption-settings-in-the-backup-vault"></a>Configuración de cifrado en el almacén de Backup

En esta sección se describen las opciones disponibles para cifrar los datos de copia de seguridad almacenados en el almacén de Backup. El servicio Azure Backup usa la aplicación **Backup Management Service** para acceder a Azure Key Vault, pero no a la identidad administrada del almacén de Backup.


### <a name="encryption-of-backup-data-using-platform-managed-keys"></a>Cifrado de datos de copia de seguridad mediante claves administradas por la plataforma

De forma predeterminada, todos los datos se cifran mediante claves administradas por la plataforma. No es necesario realizar ninguna acción explícita de su parte para habilitar este cifrado. Se aplica a todas las cargas de trabajo de las que se realiza una copia de seguridad en el almacén de Backup.

## <a name="create-a-backup-vault"></a>Creación de un almacén de Backup

Un almacén de Backup es una entidad de administración que almacena los puntos de recuperación creados a lo largo del tiempo y proporciona una interfaz para realizar operaciones relacionadas con la copia de seguridad. Esto incluye realizar copias de seguridad a petición, realizar restauraciones y crear directivas de copia de seguridad.

Para crear un almacén de Backup, siga estos pasos.

### <a name="sign-in-to-azure"></a>Inicio de sesión en Azure

Inicie sesión en Azure Portal en <https://portal.azure.com>.

### <a name="create-backup-vault"></a>Creación de un almacén de Backup

1. Escriba **almacenes de copia de seguridad** en el cuadro de búsqueda.
2. En **Servicios**, seleccione **Almacenes de Backup**.
3. En la página **Almacenes de Backup**, seleccione **Agregar**.
4. En la pestaña **Aspectos básicos**, en **Detalles del proyecto**, asegúrese de que esté seleccionada la suscripción correcta y, en el grupo de recursos, elija **Crear nuevo**. Escriba *myResourceGroup* para el nombre.

    ![Crear un grupo de recursos](./media/backup-vault-overview/new-resource-group.png)

5. En **Detalles de instancia**, escriba *myVault* como **Nombre del almacén de Backup** y seleccione la región de su elección, en este caso, *Este de EE. UU.* como su **Región**.
6. Ahora elija la **redundancia de almacenamiento**. La redundancia de almacenamiento no se puede cambiar después de proteger los elementos en el almacén.
7. Se recomienda que, si usa Azure como punto de conexión del almacenamiento de copia de seguridad principal, siga utilizando la configuración **con redundancia geográfica** predeterminada.
8. Si no usa Azure como punto de conexión de almacenamiento de copia de seguridad principal, elija **Redundancia local** para reducir los costos de almacenamiento de Azure. Obtenga más información sobre la redundancia [geográfica](../storage/common/storage-redundancy.md#geo-redundant-storage) y [local](../storage/common/storage-redundancy.md#locally-redundant-storage).

    ![Elección de la redundancia de almacenamiento](./media/backup-vault-overview/storage-redundancy.png)

9. Seleccione el botón Revisar y crear de la parte inferior de la página.

    ![Selección de Revisar y crear](./media/backup-vault-overview/review-and-create.png)

## <a name="delete-a-backup-vault"></a>Eliminación de un almacén de Backup

En esta sección se describe cómo eliminar un almacén de Backup. Contiene instrucciones para quitar las dependencias y, a continuación, eliminar un almacén.

### <a name="before-you-start"></a>Antes de comenzar

No se puede eliminar un almacén de Backup que tenga alguna de las siguientes dependencias:

- No se puede eliminar un almacén que contenga orígenes de datos protegidos (por ejemplo, servidores de Azure Database for PostgreSQL).
- No puede eliminar un almacén que contiene datos de copia de seguridad.

Si intenta eliminar el almacén sin quitar las dependencias, se producirá uno de los siguientes mensajes de error:

>No se puede eliminar el almacén de Backup porque existen instancias o directivas de copia de seguridad en el almacén. Elimine todas las instancias y directivas de copia de seguridad del almacén y vuelva a intentar eliminarlo.

Asegúrese de recorrer las opciones de filtro de **Tipo de origen de datos** en el **Centro de copia de seguridad** para no perder ninguna directiva ni instancia de Backup existente que deba quitarse, antes de poder eliminar el almacén de Backup.

![Tipos de orígenes de datos](./media/backup-vault-overview/datasource-types.png)

### <a name="proper-way-to-delete-a-vault"></a>Manera adecuada de eliminar un almacén

>[!WARNING]
La operación siguiente es destructiva y no se puede deshacer. Todos los datos de copia de seguridad y los elementos de copia de seguridad asociados con el servidor protegido se eliminarán de forma permanente. Proceda con precaución.

Para eliminar correctamente un almacén, debe seguir los pasos en este orden:

- Compruebe si hay algún elemento protegido:
  - Vaya a **Instancias de Backup** en la barra de navegación izquierda. Todos los elementos que aparecen aquí deben eliminarse primero.

Después de completar estos pasos, puede continuar y eliminar el almacén.

### <a name="delete-the-backup-vault"></a>Eliminación del almacén de Backup

Cuando no haya ningún otro elemento en el almacén, seleccione **Eliminar** en el panel del almacén. Verá un texto de confirmación que le pregunta si quiere eliminar el almacén.

![Eliminación del almacén](./media/backup-vault-overview/delete-vault.png)

1. Para verificar que desea eliminar el almacén, seleccione **Sí**. El almacén se elimina. El portal vuelve al menú **Nuevo** servicio.

## <a name="monitor-and-manage-the-backup-vault"></a>Supervisión y administración del almacén de Backup

En esta sección se describe cómo usar el panel de **información general** del almacén de Backup para supervisar y administrar los almacenes de Backup. El panel de información general contiene dos iconos: **Trabajos** e **Instancias**.

![Panel de información general](./media/backup-vault-overview/overview-dashboard.png)

### <a name="manage-backup-instances"></a>Administración de instancias de Backup

En el icono **Trabajos**, obtendrá una vista resumida de todos los trabajos relacionados con la copia de seguridad y restauración en el almacén de copia de seguridad. La selección de cualquiera de los números de este icono le permite ver más información sobre los trabajos de un tipo de origen de datos, tipo de operación y estado en concreto.

![Instancias de Backup](./media/backup-vault-overview/backup-instances.png)

### <a name="manage-backup-jobs"></a>Administración de trabajos de copia de seguridad

En el icono **Instancias de Backup**, se obtiene una vista resumida de todas las instancias de copia de seguridad del almacén de Backup. La selección de cualquiera de los números de este icono le permite ver más información sobre las instancias de copia de seguridad para un tipo de origen de datos y estado de protección en concreto.

![Trabajos de copia de seguridad](./media/backup-vault-overview/backup-jobs.png)

## <a name="move-a-backup-vault-across-azure-subscriptionsresource-groups-public-preview"></a>Traslado de un almacén de Backup entre suscripciones o grupos de recursos de Azure (versión preliminar pública)

En esta sección se explica cómo trasladar un almacén de Backup (configurado para Azure Backup) entre suscripciones y grupos de recursos de Azure mediante Azure Portal.

>[!Note]
>También puede trasladar almacenes de Backup a otro grupo de recursos o suscripción mediante [PowerShell](/powershell/module/az.resources/move-azresource?view=azps-6.3.0&preserve-view=true) y la [CLI](/cli/azure/resource?view=azure-cli-latest&preserve-view=true#az_resource_move).

### <a name="supported-regions"></a>Regiones admitidas

El traslado del almacén entre suscripciones y grupos de recursos se admite actualmente en las siguientes regiones: Oeste de EE. UU., Centro-sur de EE. UU., Este de Asia, Norte de Suiza, Norte de Sudáfrica, Oeste de Reino Unido, Centro-norte de EE. UU., Norte de Emiratos Árabes Unidos, Este de Noruega, Sudeste de Australia, Japón Occidental, Este de Canadá, Centro de Corea del Sur, Centro de Australia, Centro-oeste de EE. UU., Centro de la India, Oeste de la India, Sur de la India, Centro de Emiratos Árabes Unidos, Oeste de Sudáfrica, Oeste de Noruega, Oeste de Suiza

### <a name="use-azure-portal-to-move-backup-vault-to-a-different-resource-group"></a>Uso de Azure Portal para trasladar un almacén de Backup a otro grupo de recursos

1. Inicie sesión en [Azure Portal](https://portal.azure.com/).

1. Abra la lista de almacenes de Backup y seleccione el almacén que quiere trasladar.

   El panel del almacén muestra los detalles del almacén.

   :::image type="content" source="./media/backup-vault-overview/vault-dashboard-to-move-to-resource-group-inline.png" alt-text="Captura de pantalla en la que se muestra el panel del almacén que se va a trasladar a otro grupo de recursos." lightbox="./media/backup-vault-overview/vault-dashboard-to-move-to-resource-group-expanded.png"::: 

1. En el menú **Información general** del almacén, haga clic en **Mover** y después seleccione **Mover a otro grupo de recursos**.

   :::image type="content" source="./media/backup-vault-overview/select-move-to-another-resource-group-inline.png" alt-text="Captura de pantalla en la que se muestra la opción para trasladar el almacén de Backup a otro grupo de recursos." lightbox="./media/backup-vault-overview/select-move-to-another-resource-group-expanded.png":::
   >[!Note]
   >Solo la suscripción de administrador tiene los permisos necesarios para trasladar un almacén.

1. En la lista desplegable del **Grupo de recursos**, seleccione un grupo de recursos existente, o bien seleccione **Crear nuevo** para crear un nuevo grupo de recursos.

   La suscripción sigue siendo la misma y se rellena automáticamente.

   :::image type="content" source="./media/backup-vault-overview/select-existing-or-create-resource-group-inline.png" alt-text="Captura de pantalla en la que se muestra la selección de un grupo de recursos existente o la creación de un nuevo grupo de recursos." lightbox="./media/backup-vault-overview/select-existing-or-create-resource-group-expanded.png":::

1. En la pestaña **Recursos que hay que mover**, el almacén de Backup que se debe trasladar se someterá a validación. Este proceso puede tardar unos minutos. Espere hasta que se complete la validación.

   :::image type="content" source="./media/backup-vault-overview/move-validation-process-to-move-to-resource-group-inline.png" alt-text="Captura de pantalla en la que se muestra el estado de la validación del almacén de Backup." lightbox="./media/backup-vault-overview/move-validation-process-to-move-to-resource-group-expanded.png"::: 

1. Seleccione la casilla _Comprendo que las herramientas y los scripts asociados con recursos movidos no funcionarán hasta que los actualice para que usen nuevos identificadores de recursos_ para confirmar y después seleccione **Mover**.
 
   >[!Note]
   >La ruta de acceso del recurso cambia después de trasladar el almacén entre grupos de recursos o suscripciones. Asegúrese de actualizar las herramientas y los scripts con la nueva ruta de acceso del recurso una vez completada la operación de traslado.

Espere hasta que se complete la operación de traslado para realizar otras operaciones en el almacén. Se producirá un error en las operaciones realizadas en el almacén de Backup si dichas operaciones se realizan mientras el traslado está en curso. Una vez completado el proceso, el almacén de Backup debería aparecer en el grupo de recursos de destino.

>[!Important]
>Si se produce algún error al trasladar el almacén, consulte la sección [Códigos de error y solución de problemas](#error-codes-and-troubleshooting).  

### <a name="use-azure-portal-to-move-backup-vault-to-a-different-subscription"></a>Uso de Azure Portal para mover un almacén de Backup a otra suscripción

1. Inicie sesión en [Azure Portal](https://portal.azure.com/).

1. Abra la lista de almacenes de Backup y seleccione el almacén que quiere trasladar.
   
   El panel del almacén muestra los detalles del almacén.

   :::image type="content" source="./media/backup-vault-overview/vault-dashboard-to-move-to-another-subscription-inline.png" alt-text="Captura de pantalla en la que se muestra el panel del almacén que se va a mover a otra suscripción de Azure." lightbox="./media/backup-vault-overview/vault-dashboard-to-move-to-another-subscription-expanded.png"::: 

1. En el menú **Información general** del almacén, haga clic en **Mover** y después seleccione **Mover a otra suscripción**.

   :::image type="content" source="./media/backup-vault-overview/select-move-to-another-subscription-inline.png" alt-text="Captura de pantalla en la que se muestra la opción para mover el almacén de Backup a otra suscripción de Azure." lightbox="./media/backup-vault-overview/select-move-to-another-subscription-expanded.png"::: 
   >[!Note]
   >Solo la suscripción de administrador tiene los permisos necesarios para trasladar un almacén.

1. En la lista desplegable **Suscripción**, seleccione una suscripción existente.

   Para trasladar almacenes entre suscripciones, la suscripción de destino debe existir en el mismo inquilino que la suscripción de origen. Para trasladar un almacén a otro inquilino, consulte [Transferencia de una suscripción a otro directorio](/azure/role-based-access-control/transfer-subscription).

1. En la lista desplegable del **Grupo de recursos**, seleccione un grupo de recursos existente, o bien seleccione **Crear nuevo** para crear un nuevo grupo de recursos.

   :::image type="content" source="./media/backup-vault-overview/select-existing-or-create-resource-group-to-move-to-other-subscription-inline.png" alt-text="Captura de pantalla en la que se muestra la selección de un grupo de recursos existente o la creación de un nuevo grupo de recursos en otra suscripción de Azure." lightbox="./media/backup-vault-overview/select-existing-or-create-resource-group-to-move-to-other-subscription-expanded.png":::

1. En la pestaña **Recursos que hay que mover**, el almacén de Backup que se debe trasladar se someterá a validación. Este proceso puede tardar unos minutos. Espere hasta que se complete la validación.

   :::image type="content" source="./media/backup-vault-overview/move-validation-process-to-move-to-another-subscription-inline.png" alt-text="Captura de pantalla en la que se muestra el estado de la validación del almacén de Backup que se va a trasladar a otra suscripción de Azure." lightbox="./media/backup-vault-overview/move-validation-process-to-move-to-another-subscription-expanded.png"::: 

1. Seleccione la casilla _Comprendo que las herramientas y los scripts asociados con recursos movidos no funcionarán hasta que los actualice para que usen nuevos identificadores de recursos_ para confirmar y después seleccione **Mover**.
 
   >[!Note]
   >La ruta de acceso del recurso cambia después de trasladar el almacén entre grupos de recursos o suscripciones. Asegúrese de actualizar las herramientas y los scripts con la nueva ruta de acceso del recurso una vez completada la operación de traslado.

Espere hasta que se complete la operación de traslado para realizar otras operaciones en el almacén. Se producirá un error en las operaciones realizadas en el almacén de Backup si dichas operaciones se realizan mientras el traslado está en curso. Cuando se complete el proceso, el almacén de Backup debería aparecer en el grupo de recursos y la suscripción de destino.

>[!Important]
>Si se produce algún error al trasladar el almacén, consulte la sección [Códigos de error y solución de problemas](#error-codes-and-troubleshooting).

### <a name="error-codes-and-troubleshooting"></a>Códigos de error y solución de problemas

Solucione los siguientes problemas comunes que pueden surgir durante el traslado del almacén de Backup:

#### <a name="backupvaultmoveresourcespartiallysucceeded"></a>BackupVaultMoveResourcesPartiallySucceeded   

**Causa**: puede encontrarse con este error cuando el traslado del almacén de Backup se realiza solo parcialmente.

**Recomendación**: el problema debería resolverse automáticamente en un plazo de 36 horas. Si persiste, póngase en contacto con Soporte técnico de Microsoft.

#### <a name="backupvaultmoveresourcescriticalfailure"></a>BackupVaultMoveResourcesCriticalFailure 

**Causa**: puede encontrarse con este error cuando se produce un error crítico al trasladar el almacén de Backup. 

**Recomendación**: el problema debería resolverse automáticamente en un plazo de 36 horas. Si persiste, póngase en contacto con Soporte técnico de Microsoft. 

#### <a name="usererrorbackupvaultresourcemoveinprogress"></a>UserErrorBackupVaultResourceMoveInProgress 

**Causa**: puede encontrarse con este error si intenta realizar operaciones en el almacén de Backup mientras se está trasladando. 

**Recomendación:** espere hasta que se complete la operación de traslado y vuelva a intentarlo. 
#### <a name="usererrorbackupvaultresourcemovenotallowedformultipleresources"></a>UserErrorBackupVaultResourceMoveNotAllowedForMultipleResources

**Causa**: Puede encontrarse con este error si intenta mover varios almacenes de Backup en un solo intento. 

**Recomendación**: asegúrese de que solo se selecciona un almacén de Backup en cada operación de traslado. 
#### <a name="usererrorbackupvaultresourcemovenotalloweduntilresourceprovisioned"></a>UserErrorBackupVaultResourceMoveNotAllowedUntilResourceProvisioned

**Causa:** puede encontrarse con este error si el almacén aún no está aprovisionado. 

**Recomendación**: vuelva a intentar la operación más tarde.

#### <a name="backupvaultresourcemoveisnotenabled"></a>BackupVaultResourceMoveIsNotEnabled 

**Causa**: actualmente no se admite el traslado de recursos para el almacén de Backup en la región de Azure seleccionada.

**Recomendación**: asegúrese de que ha seleccionado una de las regiones admitidas para trasladar los almacenes de Backup. Consulte las [regiones admitidas](#supported-regions).

## <a name="next-steps"></a>Pasos siguientes

- [Configuración de la copia de seguridad en bases de datos de Azure PostgreSQL](backup-azure-database-postgresql.md#configure-backup-on-azure-postgresql-databases)
