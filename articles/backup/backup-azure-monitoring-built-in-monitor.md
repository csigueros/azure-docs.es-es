---
title: Supervisión de cargas de trabajo protegidas de Azure Backup
description: En este artículo se obtiene información sobre las funcionalidades de supervisión y notificación de las cargas de trabajo de Azure Backup mediante Azure Portal.
ms.topic: conceptual
ms.date: 08/06/2021
ms.assetid: 86ebeb03-f5fa-4794-8a5f-aa5cbbf68a81
ms.openlocfilehash: 4b20448896de05e888de33b2a680623b662b5e5a
ms.sourcegitcommit: 2d412ea97cad0a2f66c434794429ea80da9d65aa
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/14/2021
ms.locfileid: "122178442"
---
# <a name="monitoring-azure-backup-workloads"></a>Supervisión de cargas de trabajo de Azure Backup

Azure Backup proporciona varias soluciones de copia de seguridad según los requisitos de copia de seguridad y la topología de la infraestructura (local o en Azure). Cualquier usuario o administrador de copias de seguridad debería poder ver lo que está ocurriendo en todas las soluciones y esperar recibir notificaciones en los escenarios importantes. En este artículo se detallan las capacidades de supervisión y notificación que presta el servicio de Azure Backup.

[!INCLUDE [backup-center.md](../../includes/backup-center.md)]

## <a name="backup-items-in-recovery-services-vault"></a>Elementos de copia de seguridad en el almacén de Recovery Services

Puede supervisar todos los elementos de copia de seguridad por medio de un almacén de Recovery Services. Al ir a la sección **Elementos de copia de seguridad** del almacén se abre una vista que proporciona el número de elementos de copia de seguridad de cada tipo de carga de trabajo asociado al almacén. Al hacer clic en cualquier fila se abre una vista detallada que enumera todos los elementos de copia de seguridad del tipo de carga de trabajo dado, con información sobre el último estado de copia de seguridad de cada elemento, el último punto de restauración disponible, etc.

![Captura de pantalla de visualización de los elementos de copia de seguridad del almacén de Recovery Services](media/backup-azure-monitoring-laworkspace/backup-items-view.png)

> [!NOTE]
> En el caso de los elementos cuya copia de seguridad en Azure se ha efectuado mediante DPM, la lista muestra todos los orígenes de datos protegidos (tanto en disco como en línea) mediante el servidor de DPM. Si se detiene la protección del origen de datos con datos de copia de seguridad conservados, el origen de datos sigue apareciendo en el portal. Puede ir a los detalles del origen de datos para ver si los puntos de recuperación están presentes en disco, en línea o ambos. Además, en los orígenes de datos cuya protección en línea se ha detenido pero cuyos datos se conservan, la facturación de los puntos de recuperación en línea continúa hasta que los datos se eliminan por completo.
>
> La versión de DPM debe ser DPM 1807 (5.1.378.0) o DPM 2019 (versión 10.19.58.0 o superior) para que los elementos de copia de seguridad sean visibles en el portal del almacén de Recovery Services.

## <a name="backup-jobs-in-recovery-services-vault"></a>Trabajos de copia de seguridad en el almacén de Recovery Services

Azure Backup proporciona capacidades de supervisión y notificación relativas a las cargas de trabajo bajo protección de Azure Backup. En la sección **Supervisión** de la configuración del almacén de Recovery Services encontrará trabajos y alertas integrados.

![Captura de pantalla de la supervisión integrada del almacén de Recovery Services](media/backup-azure-monitoring-laworkspace/rs-vault-inbuilt-monitoring-menu.png)

Los trabajos se generan cuando se realizan operaciones como configurar copias de seguridad, hacer copias de seguridad, restaurar copias de seguridad, eliminar copias de seguridad, etc.

Aquí se muestran los trabajos de las siguientes soluciones de Azure Backup:

- Copia de seguridad de máquina virtual de Azure
- Copia de seguridad de archivos de Azure
- Copia de seguridad de cargas de trabajo de Azure, como SQL y SAP HANA
- Agente de Microsoft Azure Recovery Services (MARS)

Los trabajos procedentes de System Center Data Protection Manager (SC-DPM) y Microsoft Azure Backup Server (MABS) no se muestran.

> [!NOTE]
> Las cargas de trabajo de Azure, como las copias de seguridad de SQL y SAP HANA en VM de Azure, tienen gran número de trabajos de copia de seguridad. Por ejemplo, se pueden ejecutar copias de seguridad de los registros cada 15 minutos. Por lo tanto, en el caso de estas cargas de trabajo de base de datos, solamente se muestran las operaciones desencadenadas por el usuario. No se muestran las operaciones de copia de seguridad programadas.

## <a name="backup-alerts-in-recovery-services-vault"></a>Supervisión de alertas de copia de seguridad en el almacén de Recovery Services

Las alertas son, fundamentalmente, los escenarios donde los usuarios reciben notificaciones para que puedan tomar las medidas oportunas. La sección **Alertas de copia de seguridad** muestra las alertas generadas por el servicio de Azure Backup. Estas alertas las define el servicio; es decir, el usuario no puede crear ninguna alerta personalizada.

### <a name="alert-scenarios"></a>Escenarios de alerta

El servicio define los siguientes escenarios como escenarios susceptibles de generar alertas.

- Errores de copia de seguridad y restauración
- Copia de seguridad correcta con advertencias para el agente de Microsoft Azure Recovery Services (MARS)
- Detener la protección con conservación de datos/Detener la protección con eliminación de datos

### <a name="alerts-from-the-following-azure-backup-solutions-are-shown-here"></a>Aquí se muestran las alertas de las siguientes soluciones de Azure Backup

- Copias de seguridad de máquinas virtuales de Azure
- Copias de seguridad de archivos de Azure
- Copias de seguridad de cargas de trabajo de Azure, como SQL y SAP HANA
- Agente de Microsoft Azure Recovery Services (MARS)

> [!NOTE]
> Las alertas procedentes de System Center Data Protection Manager (SC-DPM) y Microsoft Azure Backup Server (MABS) no se muestran aquí.

### <a name="consolidated-alerts"></a>Alertas consolidadas

En el caso de soluciones de copia de seguridad de la carga de trabajo de Azure, como SQL y SAP HANA, las copias de seguridad de registros se pueden generar con mucha frecuencia (hasta cada 15 minutos, según la directiva). Por lo tanto, también es posible que los errores de copia de seguridad de registros sean muy frecuentes (hasta cada 15 minutos). En este escenario, el usuario final puede verse abrumado si se genera una alerta para cada caso de error. Así, se envía una alerta para el primer caso y, si los errores posteriores se deben a la misma causa principal, no se generan más alertas. La primera alerta se actualiza con el recuento de errores. Pero, si el usuario la desactiva, el siguiente caso desencadenará otra alerta y se tratará como la primera alerta de ese caso. Así es cómo Azure Backup realiza la consolidación de alertas para las copias de seguridad de SQL y SAP HANA.

### <a name="exceptions-when-an-alert-is-not-raised"></a>Excepciones cuando una alerta no se genera

Hay algunas excepciones en las que no se genera una alerta en caso de error. Son las siguientes:

- Un usuario ha cancelado expresamente el trabajo en ejecución.
- Se ha producido un error en el trabajo porque otro trabajo de copia de seguridad está en curso (no hay nada que se pueda hacer, solo hay que esperar a que el trabajo anterior finalice).
- Se ha producido un error en el trabajo de copia de seguridad de máquina virtual porque la máquina virtual de Azure de la que se está haciendo la copia de seguridad ya no existe.
- [Alertas consolidadas](#consolidated-alerts)

Las excepciones están diseñadas bajo el supuesto de que el resultado de estas operaciones (desencadenadas principalmente por el usuario) se muestra inmediatamente en los clientes de Portal/PowerShell/CLI. Por lo tanto, el usuario es consciente de ello de inmediato y no necesita que se le notifique.

### <a name="alert-types"></a>Tipos de alerta

Se pueden definir tres tipos alertas, según la gravedad:

- **Crítico**: en principio, cualquier error en una copia de seguridad o una recuperación (sea programada o desencadenada por el usuario) daría lugar a la generación de una alerta, que se mostraría como una alerta crítica junto con operaciones de destrucción como eliminar la copia de seguridad.
- **Advertencia**: si la operación de copia de seguridad se realiza correctamente, pero con algunas advertencias, estas se muestran como alertas de advertencia. Las alertas de advertencia solo están disponibles actualmente para las copias de seguridad del agente de Azure Backup.
- **Informativo**: actualmente, el servicio de Azure Backup aún no ha generado ninguna alerta informativa.

## <a name="notification-for-backup-alerts"></a>Notificación de alertas de copia de seguridad

> [!NOTE]
> La configuración de notificaciones solo se puede realizar a través de Azure Portal. No se admite PowerShell, la CLI, la API de REST ni la plantilla de Azure Resource Manager.

Después de que se genere una alerta, se notifica a los usuarios. Azure Backup proporciona un mecanismo de notificación integrado por correo electrónico. Se pueden especificar direcciones de correo electrónico individuales o listas de distribución para recibir una notificación cuando una alerta se genere. También puede optar por recibir una notificación con cada alerta individual, o bien agruparlas en un resumen cada hora y, luego, recibir la notificación correspondiente.

![Captura de pantalla de la notificación por correo electrónico integrada del almacén de Recovery Services](media/backup-azure-monitoring-laworkspace/rs-vault-inbuiltnotification.png)

Cuando configure una notificación, recibirá un correo electrónico de bienvenida o de introducción. De esta forma, se confirma que Azure Backup puede enviar mensajes de correo electrónico a estas direcciones cuando se genere una alerta.<br>

Si la frecuencia se ha establecido en un resumen cada hora y una alerta se genera y resuelve en el plazo de una hora, dicha alerta no formará parte del próximo resumen de cada hora.

> [!NOTE]
>
> - Si se lleva a cabo una operación de destrucción, como la **detención de la protección con eliminación de datos**, se generará una alerta y se enviará un correo electrónico a los propietarios de la suscripción, los administradores y los coadministradores, aun cuando las notificaciones no estén configuradas en el almacén de Recovery Services.
> - Para configurar notificaciones que informen de los trabajos realizados correctamente, use [Log Analytics](backup-azure-monitoring-use-azuremonitor.md#using-log-analytics-workspace).

## <a name="inactivating-alerts"></a>Desactivación de alertas

Para desactivar o resolver una alerta activa, puede seleccionar el elemento de lista correspondiente a la alerta que desea desactivar. Se abre una pantalla en la que se muestra información detallada sobre la alerta, con un botón **Desactivar** en la parte superior. Al seleccionar este botón, el estado de la alerta cambiará a **Inactivo**. También puede desactivar una alerta si hace clic con el botón derecho en el elemento de lista correspondiente a esa alerta y selecciona **Desactivar**.

![Captura de pantalla de la desactivación de alertas del Centro de copias de seguridad](media/backup-azure-monitoring-laworkspace/vault-alert-inactivate.png)

## <a name="azure-monitor-alerts-for-azure-backup-preview"></a>Alertas de Azure Monitor para Azure Backup (versión preliminar)

Azure Backup también proporciona alertas a través de Azure Monitor, a fin de permitir que los usuarios tengan una experiencia coherente de administración de alertas en distintos servicios de Azure, incluida la copia de seguridad. Con las alertas de Azure Monitor, puede enrutar las alertas a cualquier canal de notificación que Azure Monitor admita, como el correo electrónico, ITSM, un webhook, una aplicación lógica, etc.

Actualmente, Azure Backup tiene dos tipos principales de alertas integradas disponibles:

* **Alertas de seguridad**: en escenarios como la eliminación de datos de copia de seguridad o la desactivación de la funcionalidad de eliminación temporal para un almacén, se desencadenan alertas de seguridad (de gravedad 0) y se muestran en Azure Portal o se consumen a través de otros clientes (PowerShell, CLI y API de REST). Las alertas de seguridad se generan de manera predeterminada y no se pueden desactivar. Sin embargo, puede controlar los escenarios para los que se deben desencadenar las notificaciones (por ejemplo, los correos electrónicos). Para obtener más información sobre cómo configurar notificaciones, consulte [Reglas de acción](../azure-monitor/alerts/alerts-action-rules.md).
* **Job Failure Alerts** (Alertas de error de trabajo): en escenarios como errores de copia de seguridad y errores de restauración, Azure Backup proporciona alertas integradas a través de Azure Monitor (de gravedad 1). A diferencia de las alertas de seguridad, puede optar por desactivar las alertas de Azure Monitor en escenarios de error de trabajo. Por ejemplo, si ya ha configurado reglas de alertas personalizadas para errores de trabajo a través de Log Analytics y no necesita que se desencadenen alertas integradas para cada error de trabajo. De manera predeterminada, las alertas de errores de trabajo están desactivadas. Para obtener más información, consulte la [sección sobre activación de alertas para estos escenarios](#turning-on-azure-monitor-alerts-for-job-failure-scenarios).
 
En la tabla siguiente se resumen las distintas alertas de copia de seguridad disponibles actualmente (en versión preliminar) a través de Azure Monitor y los tipos de carga de trabajo o almacén admitidos:

| **Categoría de la alerta** | **Nombre de la alerta** | **Tipos de carga de trabajo o tipos de almacén admitidos** | **Descripción** | 
| ------------------ | -------------  | ------------------------------------------ | -------- |
| Seguridad | Eliminación de datos de copia de seguridad | <li> Máquina virtual de Azure <br><br> <li> SQL en máquinas virtuales de Azure (escenarios que no son de AG) <br><br> <li> SAP HANA en la máquina virtual de Azure <br><br> <li> Azure Backup Agent <br><br> <li> DPM <br><br> <li> Azure Backup Server <br><br> <li> Servidor de Azure Database for PostgreSQL <br><br> <li> Azure Blobs <br><br> <li> Azure Managed Disks  | Esta alerta se desencadena cuando un usuario detiene la copia de seguridad y elimina los datos de la copia de seguridad (Nota: Si la característica de eliminación temporal está deshabilitada para el almacén, no se recibe la alerta Eliminar datos de copia de seguridad) |
| Seguridad | Próxima purga | <li> Máquina virtual de Azure <br><br> <li> SQL en máquinas virtuales de Azure (escenarios que no son de AG) <br><br> <li> SAP HANA en la máquina virtual de Azure | Para todas las cargas de trabajo que admiten la eliminación temporal, esta alerta se desencadena cuando los datos de copia de seguridad de un elemento están a 2 días de ser purgados permanentemente por el servicio de Azure Backup | 
| Seguridad | Purga completada | <li> Máquina virtual de Azure <br><br> <li> SQL en máquinas virtuales de Azure (escenarios que no son de AG) <br><br> <li> SAP HANA en la máquina virtual de Azure | Eliminación de datos de copia de seguridad |
| Seguridad | La eliminación temporal está deshabilitada para el almacén | Almacenes de Recovery Services | Esta alerta se desencadena cuando el servicio de Azure Backup elimina permanentemente los datos de copia de seguridad eliminados temporalmente de un elemento | 
| Trabajos | Error de copia de seguridad | <li> Máquina virtual de Azure <br><br> <li> SQL en máquinas virtuales de Azure (escenarios que no son de AG) <br><br> <li> SAP HANA en la máquina virtual de Azure <br><br> <li> Azure Backup Agent <br><br> <li> Azure Files <br><br> <li> Servidor de Azure Database for PostgreSQL <br><br> <li> Azure Managed Disks | Esta alerta se desencadena cuando se produce un error en un trabajo de copia de seguridad. De manera predeterminada, las alertas de errores de copia de seguridad están desactivadas. Para obtener más información, consulte la [sección sobre activación de alertas para este escenario](#turning-on-azure-monitor-alerts-for-job-failure-scenarios). | 
| Trabajos | Error de restauración | <li> Máquina virtual de Azure <br><br> <li> SQL en máquinas virtuales de Azure (escenarios que no son de AG) <br><br> <li> SAP HANA en la máquina virtual de Azure <br><br> <li> Azure Backup Agent <br><br> <li> Azure Files <br><br> <li> Servidor de Azure Database for PostgreSQL <br><br> <li> Azure Blobs <br><br> <li> Azure Managed Disks| Esta alerta se desencadena cuando se produce un error en un trabajo de restauración. De manera predeterminada, las alertas de errores de restauración están desactivadas. Para obtener más información, consulte la [sección sobre activación de alertas para este escenario](#turning-on-azure-monitor-alerts-for-job-failure-scenarios). | 

### <a name="turning-on-azure-monitor-alerts-for-job-failure-scenarios"></a>Activación de alertas de Azure Monitor para escenarios de error de trabajo

Para optar por las alertas de Azure Monitor para escenarios de error de copia de seguridad y de restauración, siga los siguientes pasos:

1. Vaya a Azure Portal y busque **Preview Features** (Características de la versión preliminar).

    ![Captura de pantalla de visualización de las características en vista previa en el portal](media/backup-azure-monitoring-laworkspace/portal-preview-features.png)

2. Puede ver una lista con todas las características en vista previa disponibles para elegir.

    * Si desea recibir alertas de error de trabajo para las cargas de trabajo de las que se ha hecho una copia de seguridad en almacenes de Recovery Services, seleccione la marca denominada **EnableAzureBackupJobFailureAlertsToAzureMonitor** correspondiente al proveedor Microsoft.RecoveryServices (columna 3).
    * Si desea recibir alertas de error de trabajo para las cargas de trabajo de las que se ha hecho una copia de seguridad en almacenes de copia de seguridad, seleccione la marca denominada **EnableAzureBackupJobFailureAlertsToAzureMonitor** correspondiente al proveedor Microsoft.DataProtection (columna 3).

    ![Captura de pantalla del registro de la versión preliminar de alertas](media/backup-azure-monitoring-laworkspace/alert-preview-feature-flags.png)

3. Haga clic en **Registrar** para habilitar esta característica para su suscripción.
    > [!NOTE]
    > El registro puede tardar hasta 24 horas en surtir efecto. Para habilitar esta característica para varias suscripciones, repita el proceso anterior seleccionando la suscripción correspondiente en la parte superior de la pantalla. También se recomienda volver a registrar la marca de versión preliminar si se ha creado un nuevo recurso en la suscripción después del registro inicial para seguir recibiendo alertas.

### <a name="viewing-fired-alerts-in-the-azure-portal"></a>Visualización de alertas desencadenadas en Azure Portal 

Una vez que se desencadena una alerta para un almacén, puede ver la alerta en Azure Portal navegando al Centro de copias de seguridad. En la pestaña **Información general** puede ver un resumen de las alertas activas clasificadas por gravedad. Se muestran dos tipos de alertas:

* **Datasource Alerts** (Alertas de origen de datos): las alertas asociadas a un origen de datos específico del que se está haciendo una copia de seguridad (por ejemplo, errores de copia de seguridad o restauración de una máquina virtual, eliminación de datos de copia de seguridad de una base de datos, entre otros) aparecen en la sección **Datasource Alerts** (Alertas del origen de datos).
* **Global Alerts** (Alertas globales): las alertas que no están asociadas a un origen de datos específico (por ejemplo, desactivando la funcionalidad de eliminación temporal para un almacén) aparecen en la sección **Global Alerts** (Alertas globales).

Cada uno de los tipos anteriores de alertas se divide a su vez en alertas del tipo **Seguridad** y **Configured** (Configurada). Actualmente, las alertas de seguridad incluyen los escenarios de eliminación de datos de copia de seguridad o desactivación de la eliminación temporal para el almacén (para las cargas de trabajo aplicables, como se detalla en la sección anterior). Las alertas configuradas incluyen errores de copia de seguridad y de restauración, ya que estas alertas solo se desencadenan después de registrar la característica en el portal de versión preliminar.

![Captura de pantalla de visualización de alertas en el Centro de copias de seguridad](media/backup-azure-monitoring-laworkspace/backup-center-azure-monitor-alerts.png)

Al hacer clic en cualquiera de los números (o en el elemento de menú **Alertas**), se abre una lista de todas las alertas activas desencadenadas con los filtros pertinentes aplicados. Puede filtrar por un intervalo de propiedades, como suscripción, grupo de recursos, almacén, gravedad y estado, entre otras opciones. Puede hacer clic en cualquiera de las alertas para obtener más detalles sobre la alerta, como el origen de datos afectado, la descripción de la alerta, la acción recomendada, etc.

![Captura de pantalla de visualización de detalles de la alerta](media/backup-azure-monitoring-laworkspace/backup-center-alert-details.png) 

Puede cambiar el estado de una alerta a **Acknowledged** (Con confirmación) o **Closed** (Cerrada) haciendo clic en **Cambiar el estado de alerta**.

![Captura de pantalla de cambio del estado de la alerta](media/backup-azure-monitoring-laworkspace/backup-center-change-alert-state.png) 

> [!NOTE]
> - Actualmente, en el Centro de copias de seguridad solo se muestran alertas de cargas de trabajo de Azure. Para ver las alertas de los recursos en el entorno local, navegue al almacén de Recovery Services y haga clic en el elemento de menú **Alertas**.
> - En el Centro de copias de seguridad solo se muestran las alertas de Azure Monitor. Las alertas generadas por la solución de alertas anterior (a las que se accede a través de la pestaña [Alertas de copias de seguridad](#backup-alerts-in-recovery-services-vault) del almacén de Recovery Services) no se muestran en el Centro de copias de seguridad.
Para más información sobre las alertas de Azure Monitor, consulte [Información general sobre las alertas en Microsoft Azure](../azure-monitor/alerts/alerts-overview.md).

### <a name="configuring-notifications-for-alerts"></a>Configuración de notificaciones de alertas

Para configurar las notificaciones de las alertas de Azure Monitor, debe crear una regla de acción. En los pasos siguientes se muestra cómo crear una regla de acción para enviar notificaciones por correo electrónico a una dirección de correo electrónico determinada. Se aplicarán instrucciones similares para enrutar estas alertas a otros canales de notificación, como ITSM, webhook, aplicación lógica, etc.

1. En Azure Portal, vaya a **Backup center** (Centro de copias de seguridad). Haga clic en el elemento de menú **Alertas** y seleccione **Administrar acciones**.

    ![Captura de pantalla de administración de acciones en el Centro de copias de seguridad](media/backup-azure-monitoring-laworkspace/backup-center-manage-actions.png) 

2. Navegue a la pestaña **Reglas de acción (versión preliminar)** y haga clic en **Nueva regla de acción**.

    ![Captura de pantalla de creación de una nueva regla de acción](media/backup-azure-monitoring-laworkspace/azure-monitor-create-action-rule.png) 

3. Seleccione el ámbito para el que se debe aplicar la regla de acción. Puede aplicar la regla de acción para todos los recursos de una suscripción. Opcionalmente, también puede aplicar filtros en las alertas, por ejemplo, para generar solo notificaciones para alertas de una gravedad determinada.

    ![Captura de pantalla de establecimiento del ámbito de la regla de acción](media/backup-azure-monitoring-laworkspace/azure-monitor-action-rule-scope.png) 

4. Cree un grupo de acciones. Un grupo de acciones es el destino al que se debe enviar la notificación de una alerta, por ejemplo, una dirección de correo electrónico.
 
    ![Captura de pantalla de creación de un nuevo grupo de acciones](media/backup-azure-monitoring-laworkspace/azure-monitor-create-action-group.png) 

5. En la pestaña **Datos básicos**, seleccione el nombre del grupo de acciones y la suscripción y el grupo de recursos en los que se debe crear.

    ![Captura de pantalla de las propiedades básicas del grupo de acciones](media/backup-azure-monitoring-laworkspace/azure-monitor-action-groups-basic.png) 

6. En la pestaña **Notificaciones**, seleccione **Correo electrónico/Mensaje SMS/Notificación push/Voz** y escriba el ID de correo electrónico del destinatario.

    ![Captura de pantalla de establecimiento de las propiedades de notificación](media/backup-azure-monitoring-laworkspace/azure-monitor-email.png) 

7. Haga clic en **Revisar y crear** y, luego, en **Crear** para implementar el grupo de acciones.

8. Por último, guarde la regla de acción.

[Obtenga más información sobre las reglas de acción en Azure Monitor](../azure-monitor/alerts/alerts-action-rules.md)


## <a name="next-steps"></a>Pasos siguientes

[Supervisión de cargas de trabajo de Azure Backup mediante Azure Monitor](backup-azure-monitoring-use-azuremonitor.md)