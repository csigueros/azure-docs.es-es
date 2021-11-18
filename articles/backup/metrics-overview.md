---
title: Supervisión del estado de las copias de seguridad mediante Azure Backup Metrics (versión preliminar)
description: En este artículo, aprenderá sobre las métricas a disposición en Azure Backup para supervisar el estado de las copias de seguridad.
ms.topic: conceptual
author: v-amallick
ms.date: 10/20/2021
ms.author: v-amallick
ms.service: backup
ms.openlocfilehash: 158235789186748b4acb887a7861299d749a984d
ms.sourcegitcommit: 0415f4d064530e0d7799fe295f1d8dc003f17202
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/17/2021
ms.locfileid: "132709885"
---
# <a name="monitor-the-health-of-your-backups-using-azure-backup-metrics-preview"></a>Supervisión del estado de las copias de seguridad mediante Azure Backup Metrics (versión preliminar)

Azure Backup ofrece un conjunto de métricas integradas a través de Azure Monitor que le permiten supervisar el estado de sus copias de seguridad. También le permite configurar reglas de alertas que se desencadenan cuando las métricas superan los umbrales definidos.

Azure Backup ofrece las siguientes funcionalidades clave:

* Capacidad de ver métricas de fábrica relacionadas con el estado de las copias de seguridad y restauraciones de los elementos de copia de seguridad, junto con las tendencias asociadas.
* Capacidad de escribir reglas de alertas personalizadas en estas métricas para supervisar de forma eficaz el estado de los elementos de copia de seguridad.
* Capacidad de enrutar las alertas de métricas desencadenadas por distintos canales de notificación admitidos por Azure Monitor, como correo electrónico, ITSM, webhook, aplicaciones lógicas, entre otros.

[Más información sobre las métricas de Azure Monitor](../azure-monitor/essentials/data-platform-metrics.md).

## <a name="supported-scenarios"></a>Escenarios admitidos

- Admite métricas integradas para los siguientes tipos de cargas de trabajo:

  - VM de Azure, bases de datos SQL en VM de Azure
  - Bases de datos de SAP HANA en VM de Azure
  - Azure Files

  Actualmente no se admiten métricas para el tipo de carga de trabajo de instancias de HANA.

- Las métricas se pueden ver para todos los almacenes de Recovery Services de cada región y suscripción a la vez. Actualmente no se admite la visualización de métricas para un ámbito mayor en Azure Portal. Los mismos límites también se aplican para configurar reglas de alertas de métricas.

## <a name="supported-built-in-metrics"></a>Métricas integradas admitidas

Actualmente, Azure Backup admite las siguientes métricas:

- **Backup Health Events** (Eventos de estado de copia de seguridad): El valor de esta métrica representa el recuento de eventos de estado relacionados con el estado del trabajo de copia de seguridad, que se han desencadenado para el almacén en un plazo específico. Cuando se completa un trabajo de copia de seguridad, el servicio Azure Backup crea un evento de estado de copia de seguridad. En función del estado del trabajo (por ejemplo, Correcto o Error), las dimensiones asociadas al evento varían.

- **Restore Health Events** (Eventos de estado de restauración): El valor de esta métrica representa el recuento de eventos de estado relacionados con el estado del trabajo de restauración, que se han desencadenado para el almacén en un plazo específico. Cuando se completa un trabajo de restauración, el servicio Azure Backup crea un evento de estado de restauración. En función del estado del trabajo (por ejemplo, Correcto o Error), las dimensiones asociadas al evento varían.

De manera predeterminada, los recuentos se encuentran en el nivel de almacén. Para ver los recuentos de un elemento de copia de seguridad y estado de trabajo en particular, puede filtrar las métricas según cualquiera de las dimensiones admitidas.

En la tabla siguiente se enumeran las dimensiones que admiten las métricas Backup Health Events y Restore Health Events:
 
| **Nombre de la dimensión**        | **Descripción**   | 
| ----------------------------| ----------------- |
| Id. de origen de datos               | Id. exclusivo del [origen de datos](azure-backup-glossary.md#datasource) asociado al trabajo. <br><br> <ul><li> En el caso de los recursos de Azure, como VM y Files, contiene el identificador de Azure Resource Manager (id. de ARM) del recurso. <br> Por ejemplo: `/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testRG/providers/Microsoft.Compute/virtualMachines/testVM` </li><br><br> <li> Para bases de datos SQL o HANA dentro de VM, contiene el id. de ARM de la VM, seguido de los detalles de la base de datos. <br> Por ejemplo: `/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testRG/providers/Microsoft.Compute/virtualMachines/testVM/providers/Microsoft.RecoveryServices/backupProtectedItem/SQLDataBase;mssqlserver;msdb` </li></ul>  <br><br> Para la copia de seguridad de bases de datos del grupo de disponibilidad de SQL, el campo **Datasource ID** (Id. de origen de datos) está vacío, ya que no hay ningún origen de datos (VM) en estos escenarios. Para ver las métricas de una base de datos determinada dentro de un grupo de disponibilidad, use el campo **Id. de instancia de Backup**.|
| Tipo de origen de datos             | Tipo de [origen de datos](azure-backup-glossary.md#datasource) asociado al trabajo. A continuación se enumeran los tipos de orígenes de datos admitidos: <br><br> <ul><li> Microsoft.Compute/virtualMachines (Azure Virtual Machines)</li> <br><br> <li> Microsoft.Storage/storageAccounts/fileServices/shares (Azure Files) </li>  <br><br> <li> SQLDatabase (SQL en VM de Azure) </li><br><br> <li> SAPHANADataBase (SAP HANA en VM de Azure)</li></ul>   |
| Id. de instancia de Backup          | Id. de ARM de la [instancia de copia de seguridad](azure-backup-glossary.md#backup-instance--backup-item) asociada al trabajo. <br><br> Por ejemplo: `/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testRG/providers/Microsoft.RecoveryServices/vaults/testVault/backupFabrics/Azure/protectionContainers/IaasVMContainer;iaasvmcontainerv2;testRG;testVM/protectedItems/VM;iaasvmcontainerv2;testRG;testVM` |
| Nombre de la instancia de copia de seguridad        | Nombre descriptivo de la instancia de copia de seguridad para facilitar la legibilidad. Tiene el formato `{protectedContainerName};{backupItemFriendlyName}`. <br><br> Por ejemplo: `testStorageAccount;testFileShare`      |
| Estado de mantenimiento               | Representa el estado del elemento de copia de seguridad una vez completado el trabajo. Puede tomar uno de los siguientes valores: Healthy (Correcto), Transient Unhealthy (Incorrecto transitorio), Persistent Unhealthy (Incorrecto persistente), Transient Degraded (Degradado transitorio), Persistent Degraded (Degradado persistente). <br> <br> <ul> <li> Cuando un trabajo de copia de seguridad o restauración se completa correctamente, aparece un evento con el estado _Correcto_. </li><br><br><li>_Incorrecto_ aparece cuando un trabajo no se completa correctamente debido a errores del servicio, y _Degradado_ aparece ante problemas debido a errores del usuario. </li> <br><br><li> Cuando un mismo error se produce repetidamente para el mismo elemento de copia de seguridad, el estado cambia de _Transient Unhealthy/Degraded_ a _Persistent Unhealthy/Degraded_. </li></ul> |

## <a name="monitoring-scenarios"></a>Escenarios de supervisión

### <a name="view-metrics-in-the-azure-portal"></a>Visualización de métricas en Azure Portal

Para ver las métricas en Azure Portal, siga estos pasos:

1. Vaya al **Centro de copias de seguridad** en Azure Portal y haga clic en **Métricas** en el menú.

   :::image type="content" source="./media/metrics-overview/backup-center-metrics-selection-inline.png" alt-text="Captura de pantalla que muestra cómo seleccionar Métricas en el Centro de copias de seguridad." lightbox="./media/metrics-overview/backup-center-metrics-selection-expanded.png":::

1. Seleccione un almacén o un grupo de almacenes para los que quiera ver las métricas.

   Actualmente, el ámbito máximo para el que puede ver las métricas es: todos los almacenes de Recovery Services de una suscripción y región determinadas. Por ejemplo, todos los almacenes de Recovery Services del Este de EE. UU. en _TestSubscription1_.

1. **Seleccione una métrica** para ver _Backup Health Events o Restore Health Events_.

   Esto representa un gráfico que muestra el recuento de eventos de estado de los almacenes. Puede ajustar el intervalo de tiempo y la granularidad de agregación mediante los filtros de la parte superior de la pantalla.

   :::image type="content" source="./media/metrics-overview/metrics-chart-inline.png" alt-text="Captura de pantalla que muestra el proceso para seleccionar una métrica." lightbox="./media/metrics-overview/metrics-chart-expanded.png":::

1. Para filtrar las métricas por diferentes dimensiones, haga clic en el botón **Agregar filtro** y seleccione los valores de dimensión pertinentes. 

   - Por ejemplo, si quiere ver los recuentos de eventos de estado solo para las copias de seguridad de VM de Azure, agregue un filtro `Datasource Type = Microsoft.Compute/virtualMachines`. 
   - Para ver los eventos de estado de un origen de datos o una instancia de copia de seguridad determinados dentro del almacén, use los filtros de identificador de origen de datos o identificador de instancia de Backup.
   - Para ver los eventos de estado solo para las copias de seguridad con errores, use un filtro en HealthStatus; para ello, seleccione los valores correspondientes al estado de estado incorrecto o degradado.

   :::image type="content" source="./media/metrics-overview/metrics-filters-inline.png" alt-text="Captura de pantalla que muestra el proceso para filtrar las métricas según distintas dimensiones." lightbox="./media/metrics-overview/metrics-filters-expanded.png":::

### <a name="configure-alerts-and-notifications-on-your-metrics"></a>Configuración de alertas y notificaciones para las métricas

Para configurar alertas y notificaciones para las métricas, siga estos pasos:

1. Haga clic en **Nueva regla de alertas** que se encuentra en la parte superior del gráfico de métricas.

1. Seleccione el ámbito para el que quiere crear las alertas.   <br><br>    Los límites de ámbito son los mismos que los que se describen en la sección [Visualización de métricas](#view-metrics-in-the-azure-portal).

1. Seleccione la condición con la que se debe activar la alerta.      <br><br>   De manera predeterminada, algunos campos se rellenarán previamente en función de las selecciones del gráfico de métricas. Puede editar los parámetros según sea necesario. Para generar alertas individuales para cada origen de datos del almacén, use la selección de **dimensiones** en la regla de alertas de las métricas. A continuación encontrará algunos escenarios:

   - Activación de alertas en trabajos de copia de seguridad con errores para cada origen de datos:

     **Regla de alertas: Activar una alerta si los eventos de estado de copia > 0 en las últimas 24 horas para**:
     - Dimensiones ["HealthStatus"]= "Persistent Unhealthy / Transient Unhealthy"
     - Dimensiones ["DatasourceId"]= "Todos los valores actuales y futuros"

   - Activar alertas si todas las copias de seguridad del almacén se completaron correctamente durante el día:

     **Regla de alertas: Activar una alerta si los eventos de estado de copia < 1 en las últimas 24 horas para**:
     - Dimensiones ["HealthStatus"]="Persistent Unhealthy / Transient Unhealthy / Persistent Degraded / Transient Degraded"

   :::image type="content" source="./media/metrics-overview/metric-alert-condition-inline.png" alt-text="Captura de pantalla que muestra la opción para seleccionar la condición en la que se debe activar la alerta." lightbox="./media/metrics-overview/metric-alert-condition-expanded.png":::

   >[!NOTE]
   >Si selecciona más dimensiones como parte de la condición de la regla de alertas, el costo aumenta (es proporcional al número de combinaciones únicas de valores de dimensión posibles). La selección de más dimensiones permite obtener más contexto acerca de una alerta que se ha activado.


1. Para configurar notificaciones para estas alertas mediante grupos de acciones, configure un grupo de acciones como parte de la regla de alertas o cree una regla de acción independiente.

   Se admiten varios canales de notificación, como correo electrónico, ITSM, webhook, aplicación lógica y SMS. [Más información sobre los grupos de acciones](../azure-monitor/alerts/action-groups.md).

   :::image type="content" source="./media/metrics-overview/action-group-inline.png" alt-text="Captura de pantalla que muestra el proceso para configurar notificaciones para estas alertas mediante grupos de acciones." lightbox="./media/metrics-overview/action-group-expanded.png":::

1. Configure el comportamiento de resolución automática: puede configurar las alertas de métricas como _sin estado_ o _con estado_ según sea necesario.

   - Para generar una alerta por cada error de trabajo independientemente de que el error se deba a la misma causa subyacente (comportamiento sin estado), anule la selección de la opción **Automatically resolve alerts** (Resolver alertas automáticamente) en la regla de alertas.
   - Como alternativa, para configurar las alertas como con estado, active la misma casilla. Por lo tanto, cuando se active una alerta de métrica en el ámbito, otro error no creará una nueva alerta de métrica. La alerta se resuelve automáticamente si la condición de generación de la alerta se evalúa como falsa durante tres ciclos de evaluación sucesivos. Se generan nuevas alertas si la condición vuelve a evaluarse como verdadera.

[Obtenga más información sobre el comportamiento con estado y sin estado de las alertas de métricas de Azure Monitor](../azure-monitor/alerts/alerts-troubleshoot-metric.md#make-metric-alerts-occur-every-time-my-condition-is-met).

:::image type="content" source="./media/metrics-overview/auto-resolve-alert-inline.png" alt-text="Captura de pantalla que muestra el proceso para configurar el comportamiento de resolución automática." lightbox="./media/metrics-overview/auto-resolve-alert-expanded.png":::

### <a name="managing-alerts"></a>Administración de alertas

Para ver las alertas de métricas activadas, siga estos pasos:

1. Vaya al **Centro de copias de seguridad** > **Alertas**.
1. Filtre por **Tipo de señal** = **Métrica** y **Tipo** = **Configured alert** (Alerta configurada).
1. Haga clic en una alerta para ver más detalles sobre la alerta y cambiar su estado.

   :::image type="content" source="./media/metrics-overview/backup-center-metric-alerts-inline.png" alt-text="Captura de pantalla que muestra el proceso para ver las alertas de métricas activadas." lightbox="./media/metrics-overview/backup-center-metric-alerts-expanded.png":::

>[!NOTE]
>La alerta tiene dos campos: **Condición de supervisión (desencadenada/resuelta)** y **Estado de alerta (Nuevo/Confirmado/Cerrado)** .
>- **Estado de alerta**: Puede editar este campo (como se muestra en la captura de pantalla anterior).
>- **Condición de supervisión**: Este campo no se puede editar. Este campo se usa más en escenarios donde el propio servicio resuelve la alerta. Por ejemplo, el comportamiento de resolución automática en las alertas de métricas usa el campo **Condición de supervisión** para resolver una alerta.


#### <a name="datasource-alerts-and-global-alerts"></a>Alertas del origen de datos y alertas globales

Según la configuración de las reglas de alertas, la alerta activada aparece en la sección **Datasource alerts** (Alertas del origen de datos) o en la sección **Global alerts** (Alertas globales) del Centro de copia de seguridad:

- Si la alerta tiene asociada una dimensión de identificador de origen de datos, la alerta activada aparece en **Datasource alerts**.
- Si la alerta no tiene asociada una dimensión de identificador de origen de datos, la alerta activada aparece en **Global alerts**, ya que no hay ninguna información que vincule la alerta a un origen de datos específico.

[Obtenga más información sobre las alertas del origen de datos y globales aquí.](backup-center-monitor-operate.md#alerts)

### <a name="accessing-metrics-programmatically"></a>Acceso a las métricas mediante programación

Para acceder a la funcionalidad de métricas, puede usar los distintos clientes de programación, como PowerShell, la CLI o la API REST. Consulte la [documentación de la API REST de Azure Monitor](../azure-monitor/essentials/rest-api-walkthrough.md) para obtener más detalles.

## <a name="next-steps"></a>Pasos siguientes
- [Más información sobre la supervisión y la generación de informes en Azure Backup](monitoring-and-alerts-overview.md).
- [Más información sobre las métricas de Azure Monitor](../azure-monitor/essentials/data-platform-metrics.md).
- [Más información sobre las alertas de Azure](../azure-monitor/alerts/alerts-overview.md).