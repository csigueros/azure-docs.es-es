---
title: Supervisión y funcionamiento de copias de seguridad con el Centro de copias de seguridad
description: En este artículo se explica cómo supervisar y usar las copias de seguridad a gran escala con el Centro de copias de seguridad.
ms.topic: conceptual
ms.date: 10/20/2021
ms.openlocfilehash: 45e190fdd495cd5ea074386f601eb6296a90a14a
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/02/2021
ms.locfileid: "131035933"
---
# <a name="monitor-and-operate-backups-using-backup-center"></a>Supervisión y funcionamiento de copias de seguridad con el Centro de copias de seguridad

Como administrador de copias de seguridad, puede usar el Centro de copias de seguridad como un panel único para supervisar los trabajos y el inventario de copia de seguridad de manera diaria. También puede usar el Centro de copias de seguridad para realizar las operaciones habituales, como responder a solicitudes de copia de seguridad a petición, restaurar copias de seguridad, crear directivas de copia de seguridad, etc.

## <a name="supported-scenarios"></a>Escenarios admitidos

* El Centro de copias de seguridad es compatible actualmente con copias de seguridad de VM de Azure, SQL en VM de Azure, SAP HANA en VM de Azure, Azure Files y Blobs de Azure, Azure Managed Disks y servidor de Azure Database for PostgreSQL.
* Consulte la [matriz de compatibilidad](backup-center-support-matrix.md) para una lista detallada de los escenarios admitidos y no admitidos.

## <a name="backup-instances"></a>Instancias de copia de seguridad

El Centro de copias de seguridad permite realizar búsquedas y detectabilidad sencillas de las instancias de copia de seguridad en todo el conjunto de copias de seguridad.

Seleccionar la pestaña **Instancias de copia de seguridad** en el Centro de copias de seguridad le permite ver detalles de todas las instancias de copia de seguridad a las que tiene acceso.

 Puede ver la información siguiente sobre cada una de las instancias de copia de seguridad:

* Suscripción del origen de datos
* Grupo de recursos del origen de datos
* Punto de recuperación más reciente
* Almacén asociado con la instancia de copia de seguridad
* Estado de protección

 También puede filtrar la lista de instancias de copia de seguridad según los parámetros siguientes:

* Suscripción del origen de datos
* Grupo de recursos del origen de datos
* Ubicación del origen de datos
* Tipo de origen de datos
* Almacén
* Estado de protección
* Etiquetas del origen de datos

Al hacer clic con el botón derecho en cualquiera de los elementos de la cuadrícula, puede realizar acciones en la instancia de copia de seguridad determinada, como navegar al recurso, desencadenar copias de seguridad y restauraciones a petición o detener la copia de seguridad.

![Centro de copias de seguridad: instancias](./media/backup-center-monitor-operate/backup-center-instances.png)

## <a name="backup-jobs"></a>Trabajos de copia de seguridad

El Centro de copias de seguridad le permite ver información detallada sobre todos los trabajos que se crearon en el conjunto de copias de seguridad y tomar las medidas adecuadas para los trabajos con errores.

Al seleccionar el elemento de menú **Trabajos de copia de seguridad** del Centro de copias de seguridad, puede ver todos los trabajos. Cada trabajo contiene la información siguiente:

* Instancia de copia de seguridad asociada con el trabajo
* Suscripción del origen de datos
* Grupo de recursos del origen de datos
* Ubicación del origen de datos
* Operación del trabajo
* Estado del trabajo
* Hora de inicio del trabajo
* Duración del trabajo

La selección de un elemento en la cuadrícula le permite ver más detalles sobre el trabajo determinado. Al hacer clic con el botón derecho en un elemento, puede desplazarse hasta el recurso para llevar a cabo las medidas necesarias.

![Centro de copias de seguridad: trabajos](./media/backup-center-monitor-operate/backup-center-jobs.png)

En la pestaña **Trabajos de copia de seguridad**, puede ver trabajos de hasta siete días atrás. Para ver trabajos anteriores, use [Informes de Backup](backup-center-obtain-insights.md).

## <a name="metrics"></a>Métricas

Azure Backup ofrece un conjunto de métricas integradas a través de Azure Monitor que le permiten supervisar el estado de sus copias de seguridad. Para consultar las métricas, vaya al **Centro de copias de seguridad** y haga clic en **Métricas** en el menú.

:::image type="content" source="./media/metrics-overview/metrics-chart-inline.png" alt-text="Captura de pantalla en la que se muestran las métricas integradas para supervisar el estado de las copias de seguridad." lightbox="./media/metrics-overview/metrics-chart-expanded.png":::

Azure Backup ofrece estas funcionalidades clave:

* Capacidad de ver métricas de fábrica relacionadas con el estado de las copias de seguridad y restauraciones de los elementos de copia de seguridad, junto con las tendencias asociadas.
* Capacidad de escribir reglas de alertas personalizadas en estas métricas para supervisar de forma eficaz el estado de los elementos de copia de seguridad.
* Capacidad de enrutar las alertas de métricas desencadenadas por distintos canales de notificación admitidos por Azure Monitor, como correo electrónico, ITSM, webhook, aplicaciones lógicas, entre otros.

[Obtenga más información sobre las métricas de Azure Backup](metrics-overview.md).

## <a name="alerts"></a>Alertas

Para consultar las alertas, vaya al **Centro de copias de seguridad** y haga clic en **Alertas** en el menú.

También puede ver un resumen de las alertas abiertas en las últimas 24 horas en el panel **Información general**.

:::image type="content" source="./media/backup-azure-monitoring-laworkspace/backup-center-azure-monitor-alert-inline.png" alt-text="Captura de pantalla en la que se muestran las alertas en el Centro de copias de seguridad." lightbox="./media/backup-azure-monitoring-laworkspace/backup-center-azure-monitor-alert-expanded.png":::

Actualmente, los tipos de alertas siguientes se muestran en el Centro de copias de seguridad:

* **Alertas predeterminadas de Azure Monitor para Azure Backup (versión preliminar)** : incluye las alertas de seguridad integradas y las alertas configuradas que Azure Backup proporciona a través de Azure Monitor. [Obtenga más información sobre los escenarios de alerta que admite esta solución](backup-azure-monitoring-built-in-monitor.md#azure-monitor-alerts-for-azure-backup-preview).
* **Alertas de métricas para Azure Backup (versión preliminar)** : incluye las alertas que se activan en función de las reglas de alertas de métricas que creó. [Obtenga más información sobre las alertas de métricas de Azure Backup](metrics-overview.md).

>[!NOTE]
>- Actualmente, en el Centro de copias de seguridad solo se muestran alertas para las cargas de trabajo basadas en Azure. Para ver las alertas de los recursos locales, vaya al almacén de Recovery Services y haga clic en **Alertas** en el menú.
>- En el Centro de copias de seguridad solo se muestran alertas de Azure Monitor. Las alertas generadas por la solución de alertas anterior (a las que se accede en la pestaña [Alertas de copias de seguridad](backup-azure-monitoring-built-in-monitor.md#backup-alerts-in-recovery-services-vault) del almacén de Recovery Services) no se muestran en el Centro de copias de seguridad.

Para información más detallada sobre las alertas de Azure Monitor, consulte [Información general sobre las alertas en Microsoft Azure](../azure-monitor/alerts/alerts-overview.md).

### <a name="datasource-and-global-alerts"></a>Orígenes de datos y alertas globales

Se muestran estas clases de alertas:

* **Datasource Alerts** (Alertas de origen de datos): las alertas asociadas a un origen de datos específico del que se está haciendo una copia de seguridad (como errores de copia de seguridad o restauración de una máquina virtual, eliminación de datos de copia de seguridad de una base de datos, entre otros) aparecen en la sección **Datasource Alerts**. En el caso de las alertas de métricas, si la alerta activada tiene una dimensión de identificador de origen de datos asociada, la alerta activada aparece en **Datasource Alerts**.
* **Global Alerts** (Alertas globales): las alertas que no están asociadas a un origen de datos específico (como desactivando la funcionalidad de eliminación temporal para un almacén) aparecen en la sección **Global Alerts**. En el caso de las alertas de métricas, si la alerta activada no tiene un identificador de origen de datos asociado, la alerta activada aparece en **Global Alerts**.

## <a name="vaults"></a>Almacenes

Seleccionar el elemento de menú **Almacenes** del Centro de copias de seguridad le permite ver una lista de todos los [Almacenes de Recovery Services](backup-azure-recovery-services-vault-overview.md) y los [Almacenes de Backup](backup-vault-overview.md) a los que tiene acceso. Puede filtrar la lista con los parámetros siguientes:

* Suscripción del almacén
* Grupo de recursos del almacén
* Nombre del almacén
* Tipo de origen de datos asociado con la directiva

La selección de cualquier elemento de la lista permite desplazarse a un almacén determinado.

![Centro de copias de seguridad: almacenes](./media/backup-center-monitor-operate/backup-center-vaults.png)

## <a name="backup-policies"></a>Directivas de copia de seguridad

El Centro de copias de seguridad permite ver y editar la información clave de cualquiera de las directivas de copia de seguridad.

Al seleccionar el elemento de menú **Directivas de copia de seguridad**, podrá ver todas las directivas que ha creado en el conjunto de copias de seguridad. Puede filtrar la lista por suscripción de almacén, grupo de recursos, tipo de origen de datos y almacén. Al hacer clic con el botón derecho en un elemento de la cuadrícula, puede ver los elementos asociados a esa directiva, editarla o incluso eliminarla, en caso de ser necesario.

![Centro de copias de seguridad: directivas](./media/backup-center-monitor-operate/backup-center-policies.png)


## <a name="resource-centric-views"></a>Vistas centralizadas de recursos

Si su organización hace una copia de seguridad de varios recursos en un almacén común y cada propietario de recursos solo quiere ver información de copia de seguridad de los recursos que posee, puede usar la vista centralizada de recursos en el Centro de copias de seguridad. Para usar la vista centralizada de recursos, active la casilla "Mostrar solo información sobre los orígenes de datos a los que tengo acceso". Actualmente, esta opción es compatible con las siguientes pestañas: **Información general**, **Instancias de Backup**, **Trabajos** y **Alertas**. Las cargas de trabajo admitidas son VM de Azure, SQL en VM de Azure, SAP HANA en VM de Azure, blobs de Azure y discos de Azure.

> [!NOTE]
> Los usuarios seguirán necesitando tener los permisos de RBAC en el almacén, incluso si usan la vista centralizada de recursos. El propósito de esta vista es que los usuarios individuales eviten ver la información de los recursos (por ejemplo, las VM ) que no poseen.

## <a name="next-steps"></a>Pasos siguientes

* [Gobernanza del conjunto de copias de seguridad](backup-center-govern-environment.md)
* [Acciones con el Centro de copias de seguridad](backup-center-actions.md)
* [Obtención de conclusiones sobre las copias de seguridad](backup-center-obtain-insights.md)
