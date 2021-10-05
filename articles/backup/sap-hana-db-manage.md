---
title: Administración de bases de datos de SAP HANA con copia de seguridad en máquinas virtuales de Azure
description: En este artículo, aprenderá las tareas comunes para administrar y supervisar las bases de datos de SAP HANA que se ejecutan en máquinas virtuales de Azure.
ms.topic: conceptual
ms.date: 11/12/2019
ms.openlocfilehash: d514a8f790f906a4830672dc726c03ad2cf41a2c
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/24/2021
ms.locfileid: "128662255"
---
# <a name="manage-and-monitor-backed-up-sap-hana-databases"></a>Administración y supervisión de bases de datos de SAP HANA de las que se ha realizado copia de seguridad

En este artículo se describen las tareas comunes para administrar y supervisar bases de datos de SAP HANA que se ejecutan en una máquina virtual de Azure cuya copia de seguridad se ha realizado en un almacén de Azure Backup Recovery Services con el servicio [Azure Backup](./backup-overview.md). Aprenderá cómo supervisar los trabajos y las alertas, desencadenar las copias de seguridad a petición, detener y reanudar la protección de la base de datos y anular el registro de una máquina virtual de las copias de seguridad.

Si todavía no ha configurado las copias de seguridad para las bases de datos de SAP HANA, consulte [Creación de copias de seguridad de bases de datos de SAP HANA en máquinas virtuales de Azure](./backup-azure-sap-hana-database.md).

## <a name="monitor-manual-backup-jobs-in-the-portal"></a>Supervisión de trabajos de copia de seguridad manuales en el portal

Azure Backup muestra todos los trabajos desencadenados manualmente en la sección **Trabajos de copia de seguridad** de Azure Portal.

![Sección Trabajos de copia de seguridad](./media/sap-hana-db-manage/backup-jobs.png)

Los trabajos que vea en este portal incluyen operaciones de detección y registro de base de datos, y copia de seguridad y restauración. Los trabajos programados, incluidas las copias de seguridad de registros, no se muestran en esta sección. Las copias de seguridad desencadenadas manualmente desde los clientes nativos de SAP HANA (Studio/Cockpit/DBA Cockpit) tampoco se muestran aquí.

![Lista de trabajos de copia de seguridad](./media/sap-hana-db-manage/backup-jobs-list.png)

Para obtener más información sobre la supervisión, vaya a [Supervisión en Azure Portal](./backup-azure-monitoring-built-in-monitor.md) y [Supervisión con Azure Monitor](./backup-azure-monitoring-use-azuremonitor.md).

## <a name="view-backup-alerts"></a>Visualización de alertas de copia de seguridad

Las alertas son un medio sencillo de supervisar las copias de seguridad de bases de datos de SAP HANA. Las alertas le ayudan a centrarse en los eventos que más le interesan sin perderse en la multitud de eventos que genera una copia de seguridad. Azure Backup permite establecer alertas y se pueden supervisar de la siguiente manera:

* Inicie sesión en [Azure Portal](https://portal.azure.com/).
* En el panel del almacén, seleccione **Alertas de copia de seguridad**.

  ![Alertas de copia de seguridad en el panel del almacén](./media/sap-hana-db-manage/backup-alerts-dashboard.png)

* Podrá ver las alertas:

  ![Lista de alertas de copia de seguridad](./media/sap-hana-db-manage/backup-alerts-list.png)

* Seleccione las alertas para ver más detalles:

  ![Detalles de alertas](./media/sap-hana-db-manage/alert-details.png)

En la actualidad, Azure Backup permite el envío de alertas por correo electrónico. Estas alertas:

* Se desencadenan para todos los errores de copia de seguridad.
* Se consolidan en el nivel de base de datos por código de error.
* Se envían solo para el primer error de copia de seguridad de una base de datos.

Para obtener más información sobre la supervisión, vaya a [Supervisión en Azure Portal](./backup-azure-monitoring-built-in-monitor.md) y [Supervisión con Azure Monitor](./backup-azure-monitoring-use-azuremonitor.md).

## <a name="management-operations"></a>Operaciones de administración

Azure Backup facilita la administración de una base de datos de SAP HANA de la que se hizo una copia de seguridad a través de una gran cantidad de operaciones de administración con las que es compatible. En las siguientes secciones se detalla cada una de estas operaciones.

### <a name="run-an-on-demand-backup"></a>Ejecución de una copia de seguridad a petición

Las copias de seguridad se ejecutan según la programación de la directiva. Puede ejecutar una copia de seguridad a petición siguiendo estos pasos:

1. En el menú del almacén, seleccione **Elementos de copia de seguridad**.
2. En **Elementos de copia de seguridad**, seleccione la VM que ejecuta la base de datos de SAP HANA y, a continuación, seleccione **Hacer copia de seguridad ahora**.
3. En **Hacer copia de seguridad ahora**, elija el tipo de copia de seguridad que desea realizar. Después, seleccione **Aceptar**. Esta copia de seguridad se conservará de acuerdo con la directiva asociada a este elemento de copia de seguridad.
4. Supervise las notificaciones del portal. Puede supervisar el progreso del trabajo en el panel del almacén > **Trabajos de copia de seguridad** > **En curso**. Según el tamaño de la base de datos, la creación de la copia de seguridad inicial puede tardar un tiempo.

De forma predeterminada, la retención de copias de seguridad a petición es de 45 días.

### <a name="hana-native-client-integration"></a>Integración de cliente nativo de HANA

#### <a name="backup"></a>Copia de seguridad

Las copias de seguridad a petición desencadenadas desde cualquiera de los clientes nativos de HANA (a **Backint**) se mostrarán en la lista de copia de seguridad de la página **Elementos de copia de seguridad**.

![Última ejecución de copias de seguridad](./media/sap-hana-db-manage/last-backups.png)

También puede [supervisar estas copias de seguridad](#monitor-manual-backup-jobs-in-the-portal) desde la página **Trabajos de copia de seguridad**.

Estas copias de seguridad a petición también se mostrarán en la lista de puntos de restauración para la restauración.

![Lista de puntos de restauración](./media/sap-hana-db-manage/list-restore-points.png)

#### <a name="restore"></a>Restauración

Las restauraciones desencadenadas a partir de clientes nativos de HANA (mediante **Backint**) para restaurar en la misma máquina pueden [supervisarse](#monitor-manual-backup-jobs-in-the-portal) desde la página **Trabajos de copia de seguridad**.

#### <a name="delete"></a>Eliminar

La operación de eliminación de clientes nativos de HANA **NO** es compatible con Azure Backup, ya que la directiva de copia de seguridad determina el ciclo de vida de las copias de seguridad en el almacén de Azure Recovery Services.

### <a name="run-sap-hana-native-client-backup-to-local-disk-on-a-database-with-azure-backup-enabled"></a>Ejecución de una copia de seguridad del cliente nativo de SAP HANA en el disco local de una base de datos con Azure Backup habilitado

Si quiere hacer una copia de seguridad local (mediante HANA Studio/Cockpit) de una base de datos de la que se está haciendo una copia de seguridad con Azure Backup, haga lo siguiente:

1. Espere a que finalicen las copias de seguridad completas o de registro de la base de datos. Compruebe el estado en SAP HANA Studio o Cockpit.
2. para la base de datos pertinente
    1. Anule los parámetros de BackInt. Para ello, haga doble clic en **systemdb** > **Configuración** > **Seleccionar base de datos** > **Filtro (registro)** .
        * enable_auto_log_backup: No
        * log_backup_using_backint: False
        * catalog_backup_using_backint:False
3. A continuación, haga una copia de seguridad completa a petición de la base de datos
4. e invierta los pasos. Para la misma base de referencia pertinente mencionada anteriormente,
    1. vuelva a habilitar los parámetros de BackInt
        1. catalog_backup_using_backint:True
        1. log_backup_using_backint: True
        1. enable_auto_log_backup: Sí

### <a name="manage-or-clean-up-the-hana-catalog-for-a-database-with-azure-backup-enabled"></a>Administre o limpie el catálogo de HANA para una base de datos con Azure Backup habilitado

Si desea editar o limpiar el catálogo de copias de seguridad, haga lo siguiente:

1. Espere a que finalicen las copias de seguridad completas o de registro de la base de datos. Compruebe el estado en SAP HANA Studio o Cockpit.
2. para la base de datos pertinente
    1. Anule los parámetros de BackInt. Para ello, haga doble clic en **systemdb** > **Configuración** > **Seleccionar base de datos** > **Filtro (registro)** .
        * enable_auto_log_backup: No
        * log_backup_using_backint: False
        * catalog_backup_using_backint:False
3. Edite el catálogo y quite las entradas anteriores
4. e invierta los pasos. Para la misma base de referencia pertinente mencionada anteriormente,
    1. vuelva a habilitar los parámetros de BackInt
        1. catalog_backup_using_backint:True
        1. log_backup_using_backint: True
        1. enable_auto_log_backup: Sí

### <a name="change-policy"></a>Cambiar la directiva

Puede cambiar la directiva subyacente para un elemento de copia de seguridad de SAP HANA.

* En el panel del almacén, vaya a **Elementos de copia de seguridad**:

  ![Selección de elementos de copia de seguridad](./media/sap-hana-db-manage/backup-items.png)

* Elija **SAP HANA en Azure VM**.

  ![Elección de SAP HANA en Azure VM](./media/sap-hana-db-manage/sap-hana-in-azure-vm.png)

* Elija el elemento de copia de seguridad cuya directiva subyacente desea cambiar.
* Seleccione la directiva de copia de seguridad existente.

  ![Selección de la directiva de copia de seguridad existente](./media/sap-hana-db-manage/existing-backup-policy.png)

* Cambie la directiva, realizando una elección en la lista. [Cree una nueva directiva de copia de seguridad](./backup-azure-sap-hana-database.md#create-a-backup-policy), si es necesario.

  ![Elección de una directiva en la lista desplegable](./media/sap-hana-db-manage/choose-backup-policy.png)

* Guarde los cambios.

  ![Guarde los cambios.](./media/sap-hana-db-manage/save-changes.png)

* La modificación de directivas afectará a todos los elementos de Backup asociados y al desencadenador de los trabajos de **configuración de la protección** correspondientes.

>[!NOTE]
> Cualquier cambio en el período de retención se aplicará de manera retrospectiva a todos los puntos de recuperación más antiguos, además de los nuevos.

### <a name="modify-policy"></a>Modificación de directivas

Modifique la directiva para cambiar los tipos, las frecuencias o la duración de retención de las copias de seguridad.

>[!NOTE]
>Cualquier cambio en el período de retención se aplicará con efectos retroactivos a todos los puntos de recuperación anteriores, además de a los nuevos.

1. En el panel del almacén, vaya a **Administrar > Directivas de copia de seguridad** y elija la directiva que desea editar.

   ![Selección de la directiva que editar](./media/sap-hana-db-manage/manage-backup-policies.png)

1. Seleccione **Modificar**.

   ![Selección de Modificar](./media/sap-hana-db-manage/modify-policy.png)

1. Elija la frecuencia para los tipos de copia de seguridad.

   ![Selección de frecuencia de copia de seguridad](./media/sap-hana-db-manage/choose-frequency.png)

La modificación de directivas afectará a todos los elementos de copia de seguridad asociados y al desencadenador de los trabajos de **configuración de la protección** correspondientes.

### <a name="inconsistent-policy"></a>Directiva incoherente

A veces, una operación de modificación de directiva puede conducir a una versión de directiva **incoherente** para algunos elementos de copia de seguridad. Esto sucede cuando el trabajo de **protección de configuración**  correspondiente fracasa para el elemento de copia de seguridad después de que se desencadene una operación de modificación de directiva. Aparece como se indica a continuación en la vista del elemento de copia de seguridad:

![Directiva incoherente](./media/sap-hana-db-manage/inconsistent-policy.png)

Puede corregir la versión de directiva de todos los elementos afectados en un solo clic:

![Corrección de la versión de la directiva](./media/sap-hana-db-manage/fix-policy-version.png)

### <a name="stop-protection-for-an-sap-hana-database"></a>Detención de la protección para una base de datos de SAP HANA

Puede dejar de proteger una base de datos SAP HANA de dos maneras:

* Detener todos los trabajos futuros de copia de seguridad y eliminar todos los puntos de recuperación.
* Detener todos los trabajos futuros de copia de seguridad y dejar intactos los puntos de recuperación.

Si decide dejar los puntos de recuperación, tenga en cuenta estos detalles:

* Todos los puntos de recuperación permanecerán intactos para siempre y, al detenerse la protección de los datos, se detendrá la eliminación de todos los puntos y se conservarán los datos.
* Se le cobrará la instancia protegida y el almacenamiento consumido. Para más información, consulte [Precios de Azure Backup](https://azure.microsoft.com/pricing/details/backup/).
* Si elimina un origen de datos sin detener las copias de seguridad, las nuevas copias de seguridad producirán errores.

Para detener la protección de una base de datos:

* En el panel del almacén, seleccione **Elementos de copia de seguridad**.
* En **Tipo de administración de copias de seguridad**, seleccione **SAP HANA en la VM de Azure**.

  ![Selección de SAP HANA en la VM de Azure](./media/sap-hana-db-manage/sap-hana-azure-vm.png)

* Seleccione la base de datos para la que quiere detener la protección:

  ![Seleccionar la base de datos en la que detener la protección](./media/sap-hana-db-manage/select-database.png)

* En el menú de la base de datos, seleccione **Detener copia de seguridad**.

  ![Seleccionar Detener copia de seguridad](./media/sap-hana-db-manage/stop-backup.png)

* En el menú **Detener copia de seguridad**, seleccione si desea conservar o eliminar los datos. También puede proporcionar una razón y un comentario.

  ![Seleccionar Conservar o eliminar datos](./media/sap-hana-db-manage/retain-backup-data.png)

* Seleccione **Detener copia de seguridad**.

### <a name="resume-protection-for-an-sap-hana-database"></a>Reanudación de la protección para una base de datos de SAP HANA

Si seleccionó la opción **Retener datos de copia de seguridad** al detener la protección de la base de datos de SAP HANA, se puede reanudar la protección más adelante. Si no retiene los datos de copia de seguridad, no puede reanudar la protección.

Para reanudar la protección para una base de datos de SAP HANA:

* Abra el elemento de copia de seguridad y seleccione **Reanudar copia de seguridad**.

   ![Seleccione Reanudar la copia de seguridad](./media/sap-hana-db-manage/resume-backup.png)

* En el menú **Directiva de copia de seguridad**, seleccione una directiva y, después, seleccione **Guardar**.

### <a name="upgrading-from-sdc-to-mdc"></a>Actualización de SDC a MDC

Obtenga información sobre cómo continuar con la copia de seguridad de una base de datos SAP HANA [después de actualizar de SDC a MDC](backup-azure-sap-hana-database-troubleshoot.md#sdc-to-mdc-upgrade-with-a-change-in-sid).

### <a name="upgrading-from-sdc-to-mdc-without-a-sid-change"></a>Actualización de SDC a MDC sin un cambio de SID

Sepa cómo continuar con la copia de seguridad de una base de datos SAP HANA cuyo [SID no haya cambiado después de la actualización de SDC a MDC](backup-azure-sap-hana-database-troubleshoot.md#sdc-to-mdc-upgrade-with-no-change-in-sid).

### <a name="upgrading-to-a-new-version-in-either-sdc-or-mdc"></a>Actualización a una nueva versión en SDC o MDC

Obtenga información sobre cómo continuar con la copia de seguridad de una base de datos SAP HANA [cuya versión está actualizada](backup-azure-sap-hana-database-troubleshoot.md#sdc-version-upgrade-or-mdc-version-upgrade-on-the-same-vm).

### <a name="unregister-an-sap-hana-instance"></a>Anulación del registro de una instancia de SAP HANA

Anule el registro de una instancia de SAP HANA después de deshabilitar la protección, pero antes de eliminar el almacén:

* En el panel del almacén, en **Administrar**, seleccione **Infraestructura de Backup**.

   ![Seleccionar Infraestructura de Backup](./media/sap-hana-db-manage/backup-infrastructure.png)

* Seleccione el **Tipo de administración de copia de seguridad** como **Carga de trabajo en la VM de Azure**.

   ![Seleccione el Tipo de administración de copia de seguridad como Carga de trabajo en la VM de Azure.](./media/sap-hana-db-manage/backup-management-type.png)

* En **Servidores protegidos**, seleccione la instancia de la que quiere anular el registro. Para eliminar el almacén, debe anular el registro de todos los servidores o instancias.

* Haga clic con el botón derecho en la instancia protegida y seleccione **Anular registro**.

   ![Seleccionar Anular registro](./media/sap-hana-db-manage/unregister.png)

### <a name="re-register-extension-on-the-sap-hana-server-vm"></a>Volver a registrar la extensión en la máquina virtual de SAP HANA

A veces, la extensión de la carga de trabajo en la máquina virtual puede verse afectada por diversas razones. En tales casos, todas las operaciones que se desencadenen en la máquina virtual comenzarán a generar errores. Quizás tenga que volver a registrar la extensión en la máquina virtual. La operación de repetición del registro vuelve a instalar la extensión de copia de seguridad de cargas de trabajo en la máquina virtual para que las operaciones puedan continuar.

Use esta opción con precaución; cuando se desencadena en una máquina virtual que ya tiene una extensión correcta, esta operación hará que la extensión se reinicie. Esto puede dar lugar a errores en todos los trabajos en curso. Compruebe uno o varios de los [síntomas](backup-azure-sap-hana-database-troubleshoot.md#re-registration-failures) antes de desencadenar la operación de repetición del registro.

## <a name="next-steps"></a>Pasos siguientes

* Obtenga información acerca de cómo [solucionar problemas comunes al realizar copias de seguridad de bases de datos de SAP HANA](./backup-azure-sap-hana-database-troubleshoot.md).
