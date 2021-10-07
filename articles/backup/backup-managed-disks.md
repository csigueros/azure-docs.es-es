---
title: Copia de seguridad de Azure Managed Disks
description: Obtenga información sobre cómo realizar una copia de seguridad de Azure Managed Disks desde Azure Portal.
ms.topic: conceptual
ms.date: 09/17/2021
ms.openlocfilehash: 4561ff53588c6662759b0e86529f20e7cf837bbe
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/24/2021
ms.locfileid: "128668369"
---
# <a name="back-up-azure-managed-disks"></a>Copia de seguridad de Azure Managed Disks

En este artículo se explica cómo realizar copias de seguridad de [Azure Managed Disks](../virtual-machines/managed-disks-overview.md) desde Azure Portal.

En este artículo, aprenderá a:

- Creación de un almacén de Backup

- Crear una directiva de copia de seguridad

- Configurar una copia de seguridad de Azure Disk

- Ejecutar un trabajo de copia de seguridad a petición.

Para obtener información sobre la disponibilidad de la región de la copia de seguridad de Azure Disk, los escenarios admitidos y las limitaciones, consulte la [matriz de compatibilidad](disk-backup-support-matrix.md).

## <a name="create-a-backup-vault"></a>Creación de un almacén de Backup

Un almacén de Backup es una entidad de almacenamiento de Azure que contiene los datos de las copias de seguridad de varias cargas de trabajo recientes que admite Azure Backup, como los servidores de Azure Database for PostgreSQL y los discos de Azure Disk. Los almacenes de Backup facilitan la tarea de organizar los datos de copia de seguridad, al mismo tiempo que reducen al mínimo la sobrecarga administrativa. Los almacenes de copias de seguridad se basan en el modelo de Azure Resource Manager de Azure, que proporciona características para proteger los datos de las copias de seguridad.

1. Inicie sesión en Azure Portal en [https://portal.azure.com](https://portal.azure.com/).
1. Escriba **Backup center** (Centro de copias de seguridad) en el cuadro de búsqueda.
1. En **Servicios**, seleccione **Centro de copias de seguridad**.
1. En la página del **Centro de copias de seguridad**, seleccione **Almacén**.

   ![Seleccionar Almacén en el Centro de copias de seguridad](./media/backup-managed-disks/backup-center.png)

1. En la pantalla **Initiate: Create Vault** (Iniciar: crear almacén), seleccione **Almacén de Backup** y **Continuar**.

   ![Iniciar: Crear almacén](./media/backup-managed-disks/initiate-create-vault.png)

1. En la pestaña **Datos básicos**, proporcione la suscripción, el grupo de recursos, el nombre del almacén de Backup, la región y la redundancia del almacenamiento de copias de seguridad. A continuación, seleccione **Revisar y crear**. Obtenga más información sobre cómo [crear un almacén de Backup](./backup-vault-overview.md#create-a-backup-vault).

   ![Revisar y crear almacén](./media/backup-managed-disks/review-and-create.png)

## <a name="create-backup-policy"></a>Creación de una directiva de copia de seguridad

1. En el **almacén de Backup** denominado *DemoVault* que creó en el paso anterior, vaya a **directivas de copia de seguridad** y seleccione **Agregar**.

   ![Agregar una directiva de copia de seguridad](./media/backup-managed-disks/backup-policies.png)

1. En la pestaña **Datos básicos**, proporcione el nombre de la directiva y seleccione un **tipo de origen de datos** como **Azure Disk**. El almacén ya se habrá rellenado previamente, y se mostrarán las propiedades seleccionadas del almacén.

   >[!NOTE]
   > Aunque el almacén seleccionado puede tener la configuración de redundancia global, Azure Disk Backup solo admite el almacén de datos de la instantánea. Todas las copias de seguridad se almacenan en un grupo de recursos de la suscripción y no se copian en el almacén de copias de seguridad.

   ![Selección del tipo de origen de datos](./media/backup-managed-disks/datasource-type.png)

1. En la pestaña **Directiva de copia de seguridad**, seleccione la frecuencia de programación de la copia de seguridad.

   ![Selección de la frecuencia de programación de la copia de seguridad](./media/backup-managed-disks/backup-schedule-frequency.png)

   Azure Disk Backup le ofrece varias copias de seguridad al día. Si necesita realizar copias de seguridad con más frecuencia, elija la frecuencia de copia de seguridad **Cada hora**, ya que le ofrece la posibilidad de realizar copias de seguridad a intervalos de 4, 6, 8 o 12 horas. Las copias de seguridad se programan en función del intervalo de **tiempo** seleccionado. Por ejemplo, si selecciona **Cada 4 horas**, las copias de seguridad se realizan aproximadamente cada 4 horas, por lo que las mismas se distribuyen equitativamente a lo largo del día. Si realizar una copia de seguridad al día es suficiente, elija la frecuencia **Diaria**. En la frecuencia de copia de seguridad diaria, puede especificar la hora del día en la que se realizarán las copias de seguridad. Es importante tener en cuenta que la hora del día indica la hora de inicio de la copia de seguridad y no la hora en que se completa la misma. El tiempo necesario para completar la operación de copia de seguridad depende de varios factores, como el tamaño del disco y la tasa de abandono entre copias de seguridad consecutivas. No obstante, Azure Disk Backup es una opción de copias de seguridad sin agente que usa [instantáneas incrementales](../virtual-machines/disks-incremental-snapshots.md), lo que no afecta al rendimiento de la aplicación de producción.

1. En la pestaña **Directiva de copia de seguridad**, seleccione la configuración de retención que cumpla el requisito del objetivo de punto de recuperación (RPO).

   La regla de retención predeterminada se aplica si no se especifica ninguna otra regla de retención. Asimismo, la regla de retención predeterminada se puede modificar para cambiar la duración de la retención, pero no se puede eliminar. Puede agregar una nueva regla de retención seleccionando **Add retention rule** (Agregar regla de retención).

   ![Agregar una regla de retención](./media/backup-managed-disks/add-retention-rule.png)

   Puede elegir la **primera copia de seguridad correcta** realizada diariamente o semanalmente, y proporcionar la duración de la retención, para indicar el tiempo que se conservarán las copias de seguridad específicas antes de que se eliminen. Esta opción es útil para conservar copias de seguridad específicas del día o de la semana durante un período de tiempo más largo. Todas las demás copias de seguridad frecuentes se pueden conservar durante menos tiempo.

   ![Configuración de retención](./media/backup-managed-disks/retention-settings.png)

   >[!NOTE]
   >Azure Backup para Managed Disks usa instantáneas incrementales que se limitan a 200 instantáneas por disco. Para que pueda realizar copias de seguridad a petición además de las copias de seguridad programadas, la directiva de copia de seguridad limita el número total de copias de seguridad a 180. Obtenga más información sobre las [instantáneas incrementales](../virtual-machines/disks-incremental-snapshots.md#restrictions) del disco administrado.

1. Para completar la creación de la directiva de copia de seguridad, seleccione **Revisar y crear**.

## <a name="configure-backup"></a>Configuración de la copia de seguridad

- La copia de seguridad de disco de Azure solo admite la copia de seguridad de nivel operativo. No se admite la copia de copias de seguridad en el nivel de acceso de almacén. El valor de redundancia de almacenamiento del almacén de copia de seguridad (LRS/GRS) no se aplica a las copias de seguridad almacenadas en el nivel operativo. 
  Las instantáneas incrementales se almacenan en un almacenamiento de HDD estándar, con independencia del tipo de almacenamiento del disco principal. Para aumentar la confiabilidad, se almacenan instantáneas incrementales en [almacenamiento con redundancia de zona (ZRS)](/azure/storage/common/storage-redundancy) de forma predeterminada en aquellas regiones que lo admitan.

- La copia de seguridad de disco de Azure admite copias de seguridad y restauraciones entre suscripciones, donde el almacén de copia de seguridad está en una suscripción y el disco de origen en otra. Como resultado, no se admite la copia de seguridad y la restauración entre regiones y esto permite la copia de seguridad del almacén de copia de seguridad y del disco en la misma o en diferentes suscripciones. Sin embargo, tanto el almacén de copia de seguridad como el disco que se va a copiar deben estar en la misma región.

- Una vez configurada la copia de seguridad de disco, no se puede cambiar el grupo de recursos de instantáneas asignado a una instancia de copia de seguridad.

Para configurar la copia de seguridad de disco, siga estos pasos:

1. Para comenzar a configurar la copia de seguridad del disco, vaya a **Backup center** ->  (Centro de copia de seguridad) **Información general** y haga clic en **+ Copia de seguridad**.

   :::image type="content" source="./media/backup-managed-disks/start-configuring-backup-of-disk-inline.png" alt-text="Captura de pantalla que muestra la opción para iniciar la copia de seguridad de disco de Azure." lightbox="./media/backup-managed-disks/start-configuring-backup-of-disk-expanded.png":::

1. En la lista desplegable **Tipo de origen de datos**, seleccione **Azure Disks** y, luego, haga clic en **Continuar**.

   :::image type="content" source="./media/backup-managed-disks/select-azure-disks-as-datasource-type-inline.png" alt-text="Captura de pantalla que muestra el proceso para seleccionar Azure Disks como tipo de protección de datos." lightbox="./media/backup-managed-disks/select-azure-disks-as-datasource-type-expanded.png":::

1. Seleccione un almacén de copia de seguridad y haga clic en **Siguiente** para continuar.

   >[!Note]
   >- Asegúrese de que tanto el almacén de copia de seguridad como el disco del que se va a realizar la copia de seguridad se encuentran en la misma ubicación.
   >- Azure Backup usa [_instantáneas incrementales_](/azure/virtual-machines/disks-incremental-snapshots#restrictions) de discos administrados, que solo almacenan los cambios diferenciales en el disco desde la última instantánea en almacenamiento HDD estándar, independientemente del tipo de almacenamiento del disco principal. Para aumentar la confiabilidad, se almacenan instantáneas incrementales en almacenamiento con redundancia de zona (ZRS) de forma predeterminada en aquellas regiones que lo admitan. Actualmente, Azure Disk Backup admite la copia de seguridad operativa de discos administrados que no copie las copias de seguridad en el almacenamiento del almacén de copia de seguridad. Por consiguiente, el valor de redundancia del almacenamiento de copia de seguridad no se aplica a los puntos de recuperación.

   :::image type="content" source="./media/backup-managed-disks/select-backup-vault-inline.png" alt-text="Captura de pantalla que muestra el proceso para seleccionar un almacén de copia de seguridad." lightbox="./media/backup-managed-disks/select-backup-vault-expanded.png":::

1. En la pestaña **Directivas de copia de seguridad**, elija una directiva de copia de seguridad.

   :::image type="content" source="./media/backup-managed-disks/choose-backup-policy-inline.png" alt-text="Captura de pantalla que muestra el proceso para elegir una directiva de copia de seguridad." lightbox="./media/backup-managed-disks/choose-backup-policy-expanded.png":::

1. En la pestaña **Orígenes de datos**, haga clic en **+ Agregar/Editar** para elegir una o varias instancias de Azure Managed Disks para las que quiere configurar la copia de seguridad.

   :::image type="content" source="./media/backup-managed-disks/choose-azure-managed-disks-inline.png" alt-text="Captura de pantalla que muestra el proceso para elegir instancias de Azure Managed Disks." lightbox="./media/backup-managed-disks/choose-azure-managed-disks-expanded.png":::

   >[!Note]
   >Aunque el portal le permite seleccionar varios discos y configurar la copia de seguridad, tenga en cuenta que cada disco es una instancia de copia de seguridad individual. Actualmente, Azure Disk Backup solo admite la copia de seguridad de discos individuales. No se pueden realizar copias de seguridad en un momento dado de varios discos que estén conectados a una máquina virtual.
   >
   >En Azure Portal, solo puede seleccionar discos que pertenezcan a la misma suscripción. Si tiene varios discos de los que va a hacer una copia de seguridad o si los discos están en distintas suscripciones, puede usar scripts ([PowerShell](/azure/backup/backup-managed-disks-ps)/[CLI](/azure/backup/backup-managed-disks-cli)) para automatizar el proceso. 
   >
   >Para más información sobre la disponibilidad regional de la copia de seguridad de discos de Azure, los escenarios admitidos y las limitaciones, consulte la [matriz de compatibilidad](/azure/backup/disk-backup-support-matrix).

1. Para iniciar la comprobación de los requisitos previos, seleccione **Snapshot resource group** (Grupo de recursos de instantáneas) y haga clic en **Validar**.

   Elección del grupo de recursos para almacenar y administrar instantáneas:

   - No seleccione el mismo grupo de recursos que el del disco de origen.
   
   - Se recomienda crear un grupo de recursos dedicado como un almacén de datos de instantáneas que usará el servicio Azure Backup. Si tiene un grupo de recursos dedicado, podrá restringir los permisos de acceso en el grupo de recursos para asegurar y administrar fácilmente los datos de copia de seguridad.

   - Puede usar este grupo de recursos para almacenar instantáneas en varios discos que se vayan a incluir (o que tenga planeado incluir) en la copia de seguridad.

   - No puede crear una instantánea incremental de un disco determinado fuera de la suscripción de ese disco. Por consiguiente, elija el grupo de recursos de la misma suscripción que el disco del que se va a realizar la copia de seguridad. [Más información](/azure/virtual-machines/disks-incremental-snapshots#restrictions) sobre las instantáneas incrementales de discos administrados.

   - Una vez que configure la copia de seguridad de un disco, no puede cambiar el grupo de recursos de instantáneas asignado a una instancia de copia de seguridad.

   - Durante una operación de copia de seguridad, Azure Backup crea una cuenta de almacenamiento en el grupo de recursos de instantáneas. Solo se crea una cuenta de almacenamiento por cada grupo de recursos de instantáneas. La cuenta se reutiliza en varias instancias de copia de seguridad de disco que usan el mismo grupo de recursos que el grupo de recursos de instantáneas.

     - La cuenta de almacenamiento no almacena las instantáneas. Las instantáneas incrementales del disco administrado son recursos de ARM creados en el grupo de recursos, no en una cuenta de almacenamiento.
     - La cuenta de almacenamiento almacena los metadatos de cada punto de recuperación. El servicio Azure Backup crea un contenedor de blobs por cada instancia de copia de seguridad de disco. Para cada punto de recuperación, se creará un blob en bloques para almacenar los metadatos que describen el punto de recuperación (por ejemplo, la suscripción, el identificador de disco, los atributos de disco, etc.) que ocupa un espacio pequeño (en unos pocos KiB).
     - La cuenta de almacenamiento se crea con almacenamiento con redundancia de zona geográfica (GZRS) de RA si la región admite redundancia zonal. Si la región no la admite, la cuenta de almacenamiento se crea como almacenamiento con redundancia de zona geográfica del Agente de reconfiguración.
       Si alguna directiva existente impide la creación de una cuenta de almacenamiento en la suscripción o el grupo de recursos con redundancia GRS, la cuenta de almacenamiento se crea con almacenamiento con redundancia local (LRS). La cuenta de almacenamiento que se crea es **De uso general v2**, donde los blobs en bloques se almacenan en el nivel de acceso frecuente del contenedor de blobs.
     - El número de puntos de recuperación viene determinado por la directiva de copia de seguridad que se usa para configurar la copia de seguridad de la instancia de copia de seguridad del disco. Los blobs en bloques antiguos se eliminan de acuerdo con el proceso de recolección de elementos no utilizados, ya que se suprimen los puntos de recuperación más antiguos correspondientes.

   - No aplique directivas ni bloqueos de recursos en el grupo de recursos de instantáneas o en la cuenta de almacenamiento, creados por el servicio Azure Backup. El servicio crea y administra los recursos de este grupo de recursos de instantáneas que se asignan a una instancia de copia de seguridad al configurar la copia de seguridad de disco. El servicio crea la cuenta de almacenamiento y sus recursos, y no se debe eliminar ni mover.

     >[!Note]
     >Si se elimina una cuenta de almacenamiento, las copias de seguridad no se harán correctamente ni tampoco la restauración en todos los puntos de recuperación existentes.

   :::image type="content" source="./media/backup-managed-disks/validate-snapshot-resource-group-inline.png" alt-text="Captura de pantalla que muestra el proceso para iniciar comprobaciones de requisitos previos." lightbox="./media/backup-managed-disks/validate-snapshot-resource-group-expanded.png":::

1. Una vez completada la validación, compruebe si hay errores notificados en la columna de preparación para la copia de seguridad.

   >[!Note]
   >La validación podría tardar unos minutos en completarse. Puede producirse un error de validación si:
   >
   >- No se admite un disco. Consulte la [matriz de compatibilidad](/azure/backup/disk-backup-support-matrix) para ver los escenarios que no se admiten.
   >- La identidad administrada del almacén de copia de seguridad no tiene asignaciones de roles válidas en el _disco_ del que se va a realizar una copia de seguridad o en el _grupo de recursos de instantáneas_ donde se almacenan las instantáneas incrementales.

   Si el mensaje de error _Role assignment not done_ (Asignación de roles no realizada) aparece en la columna **Backup readiness** (Preparación para copia de seguridad), la identidad administrada del almacén de copia de seguridad necesita permisos de rol en los discos seleccionados o en el grupo de recursos de instantáneas. 

   :::image type="content" source="./media/backup-managed-disks/role-assignment-not-done-error-inline.png" alt-text="Captura de pantalla que muestra el mensaje de error Role assignment not done (Asignación de roles no realizada)." lightbox="./media/backup-managed-disks/role-assignment-not-done-error-expanded.png":::

   Para configurar la copia de seguridad de discos administrados, se requieren los siguientes requisitos previos:

   >[!Note]
   >El almacén de Backup usa la identidad administrada para obtener acceso a otros recursos de Azure. Para configurar una copia de seguridad de los discos administrados, la identidad administrada del almacén de copia de seguridad necesita un conjunto de permisos en los discos de origen y los grupos de recursos donde se crean y administran las instantáneas.

   Solo hay una identidad administrada asignada por cada recurso y está asociada al ciclo de vida del recurso. Para conceder permisos a la identidad administrada, use el control de acceso basado en rol de Azure (Azure RBAC). Tenga en cuenta que una identidad administrada es una entidad de servicio de un tipo especial que solo se puede usar con recursos de Azure. Obtenga más información sobre las [identidades administradas](/azure/active-directory/managed-identities-azure-resources/overview).

   - Asigne el rol **Lector de copias de seguridad de disco** a la identidad administrada del almacén de copia de seguridad del disco de origen del que se tiene que hacer la copia de seguridad.
   - Asigne el rol de Colaborador de instantáneas de discos a la identidad administrada del almacén de Backup que se encuentra en el grupo de recursos donde el servicio Azure Backup crea y administra las copias de seguridad. Las instantáneas de disco se almacenan en un grupo de recursos de la suscripción. Para permitir que el servicio Azure Backup cree, almacene y administre instantáneas, debe proporcionar permisos al almacén de copia de seguridad.

   >[!Note]
   >El flujo Configurar copia de seguridad con Azure Portal ayuda a conceder los permisos de rol necesarios a los recursos anteriores. 

1. Active la casilla situada junto a cada fila con el estado del mensaje de error _Role assignment not done_ (Asignación de roles no realizada) en la columna Backup readiness (Preparación para copia de seguridad) y haga clic en **Add missing roles** (Agregar roles que faltan) para conceder automáticamente los permisos de rol necesarios para la identidad administrada del almacén de copia de seguridad en los recursos seleccionados.

   :::image type="content" source="./media/backup-managed-disks/add-missing-roles-inline.png" alt-text="Captura de pantalla que muestra el proceso para agregar roles que faltan." lightbox="./media/backup-managed-disks/add-missing-roles-expanded.png":::

1. Haga clic en **Confirmar** para proporcionar consentimiento. Azure Backup propagará automáticamente los cambios de asignación de roles en su nombre e intentará volver a validarlos.

   Si quiere conceder permiso para la identidad administrada del almacén de copia de seguridad a los discos y el grupo de recursos de instantáneas seleccionados, elija **Recurso** en la lista desplegable **Ámbito**. 

   :::image type="content" source="./media/backup-managed-disks/confirm-role-propagation-inline.png" alt-text="Captura de pantalla que muestra la opción de proporcionar consentimiento a Azure Backup para la asignación automática de roles." lightbox="./media/backup-managed-disks/confirm-role-propagation-expanded.png":::

   >[!Tip]
   >Si tiene previsto configurar la copia de seguridad de otros discos del mismo grupo de recursos o de la misma suscripción en el futuro, puede optar por proporcionar permiso en el ámbito del grupo de recursos o la suscripción.

   :::image type="content" source="./media/backup-managed-disks/permission-deployment-in-progress-inline.png" alt-text="Captura de pantalla que muestra la implementación de permisos." lightbox="./media/backup-managed-disks/permission-deployment-in-progress-expanded.png":::

   :::image type="content" source="./media/backup-managed-disks/permission-waiting-to-propagate-inline.png" alt-text="Captura de pantalla que muestra el permiso a la espera de propagarse al grupo de recursos." lightbox="./media/backup-managed-disks/permission-waiting-to-propagate-expanded.png":::

   :::image type="content" source="./media/backup-managed-disks/revalidating-permission-propagation-inline.png" alt-text="Captura de pantalla que muestra los intentos de volver a validar la propagación del permiso en escenarios con errores." lightbox="./media/backup-managed-disks/revalidating-permission-propagation-expanded.png":::

   >[!Note]
   >- En algunos casos, las asignaciones de roles pueden tardar hasta 30 minutos en propagarse, lo que provoca un error de revalidación. En este escenario, vuelva a intentarlo pasado un tiempo.
   >- Si la acción **Add missing roles** (Agregar roles que faltan) no puede asignar permisos y aparece un error que dice que no hay permisos suficientes para la asignación de roles en la columna Backup readiness (Preparación para copia de seguridad), significa que no tiene el privilegio para asignar permisos de rol. Elija Download role assignment template (Descargar plantilla de asignación de roles) para descargar asignaciones de roles como scripts y solicite soporte técnico al administrador de TI para ejecutar los scripts a fin de completar los requisitos previos. 

   :::image type="content" source="./media/backup-managed-disks/permission-propagation-taking-long-time-inline.png" alt-text="Captura de pantalla que muestra que el permiso propaga instancias que tardan más tiempo, hasta 30 segundos." lightbox="./media/backup-managed-disks/permission-propagation-taking-long-time-expanded.png":::

1. Después de realizar una validación correcta, haga clic en **Siguiente** para pasar a la pestaña **Revisar y configurar** y, luego, haga clic en **Configurar copia de seguridad** para configurar la copia de seguridad de los discos seleccionados.

   :::image type="content" source="./media/backup-managed-disks/configure-backup-of-selected-disks-inline.png" alt-text="Captura de pantalla que muestra el proceso para configurar la copia de seguridad de los discos seleccionados." lightbox="./media/backup-managed-disks/configure-backup-of-selected-disks-expanded.png":::

## <a name="run-an-on-demand-backup"></a>Ejecución de una copia de seguridad a petición

1. En el **almacén de Backup** denominado *DemoVault* que creó en el paso anterior, vaya a **Instancias de copia de seguridad** y seleccione una instancia de copia de seguridad.

   ![Selección de una instancia de copia de seguridad](./media/backup-managed-disks/select-backup-instance.png)

1. En la pantalla **Instancias de copia de seguridad**, encontrará:

   - **Información esencial**, incluido el nombre del disco de origen, el grupo de recursos de instantáneas donde se almacenan las instantáneas incrementales, el almacén de Backup y la directiva de copia de seguridad.
   - El **estado del trabajo** que muestra un resumen de las operaciones de copia de seguridad y restauración, así como de su estado en los últimos siete días.
   - Una lista de **puntos de restauración** del período de tiempo seleccionado.

1. Seleccione **Realizar copia de seguridad** para realizar el proceso de copias de seguridad a petición.

   ![Seleccionar Realizar copia de seguridad ahora](./media/backup-managed-disks/backup-now.png)

1. Seleccione una de las reglas de retención asociadas a la directiva de copia de seguridad. Esta regla de retención determinará la duración de la retención de la copia de seguridad a petición. Seleccione **Realizar copia de seguridad ahora** para iniciar la copia de seguridad.

   ![Iniciar una copia de seguridad](./media/backup-managed-disks/initiate-backup.png)

## <a name="track-a-backup-operation"></a>Seguimiento de una operación de copia de seguridad

El servicio Azure Backup crea un trabajo para las copias de seguridad programadas o si desencadena una operación de copia de seguridad a petición para su seguimiento. Para ver el estado del trabajo de copia de seguridad:

1. Vaya a la pantalla **Instancia de copia de seguridad**. En ella se muestra el panel de trabajos que cuenta con la operación y el estado de los últimos siete días.

   ![Panel de trabajos](./media/backup-managed-disks/jobs-dashboard.png)

1. Para ver el estado de la operación de copia de seguridad, seleccione **Ver todos** para mostrar los trabajos en curso y pasados de esta instancia de copia de seguridad.

   ![Selección de la opción Ver todo](./media/backup-managed-disks/view-all.png)

1. Revise la lista de trabajos de copia de seguridad y restauración, así como su estado. Seleccione un trabajo de la lista de trabajos para ver los detalles del mismo.

   ![Selección del trabajo para ver los detalles](./media/backup-managed-disks/select-job.png)

## <a name="next-steps"></a>Pasos siguientes

- [Restauración de Azure Managed Disks](restore-managed-disks.md)