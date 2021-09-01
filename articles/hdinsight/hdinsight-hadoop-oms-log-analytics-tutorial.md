---
title: Uso de los registros de Azure Monitor para supervisar clústeres de Azure HDInsight
description: Aprenda a usar los registros de Azure Monitor para supervisar trabajos que se ejecutan en un clúster de HDInsight.
ms.service: hdinsight
ms.topic: how-to
ms.custom: seoapr2020, devx-track-azurepowershell, references_regions
ms.date: 08/02/2021
ms.openlocfilehash: 0627cbb6c590178c5f393cfd519fb4a4504d050f
ms.sourcegitcommit: 2d412ea97cad0a2f66c434794429ea80da9d65aa
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/14/2021
ms.locfileid: "122180491"
---
# <a name="use-azure-monitor-logs-to-monitor-hdinsight-clusters"></a>Uso de los registros de Azure Monitor para supervisar clústeres de HDInsight

Aprenda a usar habilitar los registros de Azure Monitor para supervisar las operaciones de los clústeres de Hadoop en HDInsight. Y a agregar una solución de supervisión de HDInsight.

Los [registros de Azure Monitor](../azure-monitor/logs/log-query-overview.md) son un servicio de Azure Monitor que supervisa los entornos locales y en la nube. La supervisión sirve para mantener su disponibilidad y rendimiento. Recopila los datos generados por los recursos en los entornos local y de nube y mediante otras herramientas de supervisión. Los datos se usan para proporcionar análisis en varios orígenes.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

Si no tiene una suscripción a Azure, cree una [cuenta gratuita](https://azure.microsoft.com/free/) antes de empezar.

#### <a name="new-azure-monitor-experience"></a>[Nueva experiencia de Azure Monitor](#tab/new)

> [!Important]
> La nueva experiencia de Azure Monitor solo está disponible en Este de EE. UU. y Oeste de Europa como característica en versión preliminar.  
>

## <a name="prerequisites"></a>Prerrequisitos

* Un área de trabajo de Log Analytics. Considere el área de trabajo como un entorno de registros de Azure Monitor único, con su propio repositorio de datos, sus propios orígenes de datos y sus propias soluciones. Para más instrucciones, consulte [Creación de un área de trabajo de Log Analytics](../azure-monitor/vm/monitor-virtual-machine.md).

* Un clúster de HDInsight de Azure. Actualmente, puede usar los registros de Azure Monitor con los siguientes tipos de clúster de HDInsight:

  * Hadoop
  * HBase
  * Interactive Query
  * Kafka
  * Spark
  * Storm

  Para instrucciones sobre cómo crear un clúster en HDInsight, consulte el artículo de [introducción a Azure HDInsight](hadoop/apache-hadoop-linux-tutorial-get-started.md).  

* Si usa PowerShell, necesitará el [Módulo Az](/powershell/azure/). Asegúrese de disponer de la versión más reciente. Si es necesario, ejecute `Update-Module -Name Az`.

* Si quiere usar la CLI de Azure y todavía no la ha instalado, consulte [Instalación de la CLI de Azure](/cli/azure/install-azure-cli).

> [!NOTE]  
> La nueva experiencia de Azure Monitor solo está disponible en Este de EE. UU. y Oeste de Europa como característica en versión preliminar. Se recomienda colocar el clúster de HDInsight y el área de trabajo de Log Analytics en la misma región para mejorar el rendimiento. Los registros de Azure Monitor no están disponible en todas las regiones de Azure.
>

## <a name="enable-azure-monitor-using-the-portal"></a>Habilitación de Azure Monitor mediante el portal

En esta sección, configurará un clúster de HDInsight Hadoop existente para usar un área de trabajo de Azure Log Analytics para supervisar trabajos, depurar registros, etc.

1. En [Azure Portal](https://portal.azure.com/), seleccione su clúster. El clúster se abre en una nueva página del portal.

2. En el menú de la izquierda, en Supervisión, seleccione  **Monitor Integration** (Integración de Monitor). 

3. En la vista principal, en  **Azure Monitor for HDInsight Clusters Integration** (Integración de Azure Monitor para clústeres de HDInsight), seleccione  **Habilitar**. 

4. En la lista desplegable  **Seleccionar un área de trabajo** , seleccione un área de trabajo de Log Analytics existente. 

5. Seleccione  **Guardar**. Guardar la configuración tarda unos minutos. 

    :::image type="content" source="./media/hdinsight-hadoop-oms-log-analytics-tutorial/hdinsight-enable-azure-monitor.png" alt-text="Habilitación de la supervisión para clústeres de HDInsight":::

Si desea deshabilitar Azure Monitor, puede hacer lo mismo en este portal. 

## <a name="enable-azure-monitor-using-azure-powershell"></a>Habilitación de Azure Monitor mediante Azure PowerShell

Puede habilitar los registros de Azure Monitor con el cmdlet [Enable-AzHDInsightAzureMonitor](/powershell/module/az.hdinsight/enable-azhdinsightazuremonitor?view=azps-6.2.1&preserve-view=true) del módulo Az de Azure PowerShell.

```powershell
# Enter user information
$resourceGroup = "<your-resource-group>"
$cluster = "<your-cluster>"
$LAW = "<your-Log-Analytics-workspace>"
# End of user input

# obtain workspace id for defined Log Analytics workspace
$WorkspaceId = (Get-AzOperationalInsightsWorkspace `
                    -ResourceGroupName $resourceGroup `
                    -Name $LAW).CustomerId

# obtain primary key for defined Log Analytics workspace
$PrimaryKey = (Get-AzOperationalInsightsWorkspace `
                    -ResourceGroupName $resourceGroup `
                    -Name $LAW | Get-AzOperationalInsightsWorkspaceSharedKeys).PrimarySharedKey

# Enables monitoring and relevant logs will be sent to the specified workspace.
Enable-AzHDInsightAzureMonitor `
    -ResourceGroupName $resourceGroup `
    -ClusterName $cluster `
    -WorkspaceId $WorkspaceId `
    -PrimaryKey $PrimaryKey

# Gets the status of monitoring installation on the cluster.
Get-AzHDInsightAzureMonitor `
    -ResourceGroupName $resourceGroup `
    -ClusterName $cluster
```

Para deshabilitarlos, use el cmdlet [Disable-AzHDInsightAzureMonitor](/powershell/module/az.hdinsight/disable-azhdinsightazuremonitor?view=azps-6.2.1&preserve-view=true):

```powershell
Disable-AzHDInsightAzureMonitor -ResourceGroupName $resourceGroup `
-ClusterName $cluster
```

## <a name="enable-azure-monitor-using-azure-cli"></a>Habilitación de Azure Monitor mediante la CLI de Azure

Puede habilitar los registros de Azure Monitor mediante el comando [`az hdinsight azure-monitor enable`](/cli/azure/hdinsight/azure-monitor?view=azure-cli-latest&preserve-view=true) de la CLI de Azure.

```azurecli
# set variables
export resourceGroup=RESOURCEGROUPNAME
export cluster=CLUSTERNAME
export LAW=LOGANALYTICSWORKSPACENAME

# Enable the Azure Monitor logs integration on an HDInsight cluster.
az hdinsight azure-monitor enable --name $cluster --resource-group $resourceGroup --workspace $LAW

# Get the status of Azure Monitor logs integration on an HDInsight cluster.
az hdinsight azure-monitor show --name $cluster --resource-group $resourceGroup
```

Para deshabilitarlos, use el comando [`az hdinsight monitor disable`](/cli/azure/hdinsight/monitor#az_hdinsight_monitor_disable).

```azurecli
az hdinsight azure-monitor disable --name $cluster --resource-group $resourceGroup
```

## <a name="use-hdinsight-out-of-box-insights-to-monitor-a-single-cluster"></a>Uso de información lista para usar de HDInsight para supervisar un único clúster

HDInsight proporciona un libro específico de la carga de trabajo para ayudarle a obtener información rápidamente. Este libro recopila importantes métricas de rendimiento del clúster de HDInsight y proporciona visualizaciones y paneles para los escenarios más comunes. La información lista para usar proporciona una vista completa de un único clúster de HDInsight, incluido el uso de recursos y el estado de la aplicación.

Libros de HDInsight disponibles:

- Libro de Spark para HDInsight
- Libro de Kafka para HDInsight
- Libro de HBase para HDInsight
- Libro de Hive/LLAP para HDInsight
- Libro de Storm para HDInsight

Captura de pantalla del libro de Spark:::image type="content" source="./media/hdinsight-hadoop-oms-log-analytics-tutorial/hdinsight-spark-workbook.png" alt-text="Captura de pantalla del libro de Spark":::

## <a name="use-at-scale-insights-to-monitor-multiple-clusters"></a>Uso de información a gran escala para supervisar varios clústeres

Puede iniciar sesión en Azure Portal y seleccionar Supervisión. En la sección **Información**, puede seleccionar **Insights Hub** (Centro de conclusiones). A continuación, puede encontrar clústeres de HDInsight.

En esta vista, puede supervisar varios clústeres de HDInsight desde un solo lugar.
    :::image type="content" source="./media/hdinsight-hadoop-oms-log-analytics-tutorial/hdinsight-monitor-insights.png" alt-text="Captura de pantalla de información de supervisión de clústeres":::

Puede seleccionar la suscripción y los clústeres de HDInsight que desea supervisar. 
 - **Monitored Clusters** (Clústeres supervisados) muestra el número de clústeres en los que ha habilitado la integración con Azure Monitor.
 - **Unmonitored Cluster** (Clúster no supervisado) muestra el número de clústeres en los que no ha habilitado la integración con Azure Monitor.

Puede ver la lista detallada de clústeres en cada sección. 

En el separador **Overview** (Información general) de **Monitored Clusters** (Clústeres supervisados), puede ver el tipo de clúster, las alertas críticas y el uso de recursos.
    :::image type="content" source="./media/hdinsight-hadoop-oms-log-analytics-tutorial/hdinsight-cluster-alerts.png" alt-text="Captura de pantalla de alertas de supervisión de clústeres":::

También puede ver los clústeres en cada tipo de carga de trabajo, incluidos Spark, HBase, Hive, Kafka y Storm.

Se presentarán las métricas generales de cada tipo de carga de trabajo, lo que incluye cuántos administradores de nodos activos hay, cuántas aplicaciones en ejecución, etc.

:::image type="content" source="./media/hdinsight-hadoop-oms-log-analytics-tutorial/spark-metrics.png" alt-text="Métricas de Spark de supervisión de clúster":::

## <a name="configuring-performance-counters"></a>Configuración de contadores de rendimiento

Azure Monitor admite la recopilación y el análisis de las métricas de rendimiento de los nodos del clúster. Para obtener más información, consulte [Orígenes de datos de rendimiento de Windows y Linux en Azure Monitor](../azure-monitor/agents/data-sources-performance-counters.md#linux-performance-counters).

## <a name="cluster-auditing"></a>Auditoría de clústeres

HDInsight admite la auditoría de clústeres con registros de Azure Monitor, mediante la importación de los siguientes tipos de registros:

* `log_gateway_audit_CL`: esta tabla proporciona registros de auditoría de nodos de puerta de enlace de clúster que muestran intentos de inicio de sesión correctos y erróneos.
* `log_auth_CL`: esta tabla proporciona registros SSH con intentos de inicio de sesión correctos y erróneos.
* `log_ambari_audit_CL`: esta tabla proporciona registros de auditoría de Ambari.
* `log_ranger_audti_CL`: esta tabla proporciona registros de auditoría de Apache Ranger en clústeres ESP.


#### <a name="classic-azure-monitor-experience"></a>[Experiencia clásica de Azure Monitor](#tab/previous)

## <a name="prerequisites"></a>Prerrequisitos

* Un área de trabajo de Log Analytics. Considere el área de trabajo como un entorno de registros de Azure Monitor único, con su propio repositorio de datos, sus propios orígenes de datos y sus propias soluciones. Para más instrucciones, consulte [Creación de un área de trabajo de Log Analytics](../azure-monitor/vm/monitor-virtual-machine.md).

* Un clúster de HDInsight de Azure. Actualmente, puede usar los registros de Azure Monitor con los siguientes tipos de clúster de HDInsight:

  * Hadoop
  * HBase
  * Interactive Query
  * Kafka
  * Spark
  * Storm

  Para instrucciones sobre cómo crear un clúster en HDInsight, consulte el artículo de [introducción a Azure HDInsight](hadoop/apache-hadoop-linux-tutorial-get-started.md).  

* Si usa PowerShell, necesitará el [Módulo Az](/powershell/azure/). Asegúrese de disponer de la versión más reciente. Si es necesario, ejecute `Update-Module -Name Az`.

* Si quiere usar la CLI de Azure y todavía no la ha instalado, consulte [Instalación de la CLI de Azure](/cli/azure/install-azure-cli).

> [!NOTE]  
> Se recomienda colocar el clúster de HDInsight y el área de trabajo de Log Analytics en la misma región para mejorar el rendimiento. Los registros de Azure Monitor no están disponible en todas las regiones de Azure.

## <a name="enable-azure-monitor-using-the-portal"></a>Habilitación de Azure Monitor mediante el portal

En esta sección, configurará un clúster de HDInsight Hadoop existente para usar un área de trabajo de Azure Log Analytics para supervisar trabajos, depurar registros, etc.

1. En [Azure Portal](https://portal.azure.com/), seleccione su clúster. El clúster se abre en una nueva página del portal.

1. En el menú de la izquierda, en **Supervisión**, seleccione **Azure Monitor**.

1. En la vista principal, en **Integración de Azure Monitor**, seleccione **Habilitar**.

1. En la lista desplegable **Seleccionar un área de trabajo**, seleccione un área de trabajo de Log Analytics existente.

1. Seleccione **Guardar**.  Guardar la configuración tarda unos minutos.

    :::image type="content" source="./media/hdinsight-hadoop-oms-log-analytics-tutorial/azure-portal-monitoring.png" alt-text="Habilitación de la supervisión para clústeres de HDInsight":::

## <a name="enable-azure-monitor-using-azure-powershell"></a>Habilitación de Azure Monitor mediante Azure PowerShell

Puede habilitar los registros de Azure Monitor con el cmdlet [Enable-AzHDInsightMonitoring](/powershell/module/az.hdinsight/enable-azhdinsightmonitoring) del módulo Az de Azure PowerShell.

```powershell
# Enter user information
$resourceGroup = "<your-resource-group>"
$cluster = "<your-cluster>"
$LAW = "<your-Log-Analytics-workspace>"
# End of user input

# obtain workspace id for defined Log Analytics workspace
$WorkspaceId = (Get-AzOperationalInsightsWorkspace `
                    -ResourceGroupName $resourceGroup `
                    -Name $LAW).CustomerId

# obtain primary key for defined Log Analytics workspace
$PrimaryKey = (Get-AzOperationalInsightsWorkspace `
                    -ResourceGroupName $resourceGroup `
                    -Name $LAW | Get-AzOperationalInsightsWorkspaceSharedKeys).PrimarySharedKey

# Enables monitoring and relevant logs will be sent to the specified workspace.
Enable-AzHDInsightMonitoring `
    -ResourceGroupName $resourceGroup `
    -Name $cluster `
    -WorkspaceId $WorkspaceId `
    -PrimaryKey $PrimaryKey

# Gets the status of monitoring installation on the cluster.
Get-AzHDInsightMonitoring `
    -ResourceGroupName $resourceGroup `
    -Name $cluster
```

Para deshabilitarlos, use el cmdlet [Disable-AzHDInsightMonitoring](/powershell/module/az.hdinsight/disable-azhdinsightmonitoring):

```powershell
Disable-AzHDInsightMonitoring -Name "<your-cluster>"
```

## <a name="enable-azure-monitor-using-azure-cli"></a>Habilitación de Azure Monitor mediante la CLI de Azure

Puede habilitar los registros de Azure Monitor mediante el comando `[az hdinsight monitor enable`](/cli/azure/hdinsight/monitor#az_hdinsight_monitor_enable) de la CLI de Azure.

```azurecli
# set variables
export resourceGroup=RESOURCEGROUPNAME
export cluster=CLUSTERNAME
export LAW=LOGANALYTICSWORKSPACENAME

# Enable the Azure Monitor logs integration on an HDInsight cluster.
az hdinsight monitor enable --name $cluster --resource-group $resourceGroup --workspace $LAW

# Get the status of Azure Monitor logs integration on an HDInsight cluster.
az hdinsight monitor show --name $cluster --resource-group $resourceGroup
```

Para deshabilitarlos, use el comando [`az hdinsight monitor disable`](/cli/azure/hdinsight/monitor#az_hdinsight_monitor_disable).

```azurecli
az hdinsight monitor disable --name $cluster --resource-group $resourceGroup
```

## <a name="install-hdinsight-cluster-management-solutions"></a>Instalación de soluciones de administración de clústeres de HDInsight

HDInsight proporciona soluciones de administración específicas de clúster que se pueden agregar a los registros de Azure Monitor. Las [soluciones de administración](../azure-monitor/insights/solutions.md) agregan funcionalidad a los registros de Azure Monitor, y proporcionan datos y herramientas de análisis adicionales. Estas soluciones recopilan importantes métricas de rendimiento de los clústeres de HDInsight. Y proporcionan las herramientas para buscar las métricas. También proporcionan visualizaciones y paneles para la mayoría de los tipos de clúster admitidos en HDInsight. Mediante el uso de las métricas que se recopilan con la solución, puede crear alertas y reglas de supervisión personalizadas.

Soluciones de HDInsight disponibles:

* Supervisión de HDInsight Hadoop
* Supervisión de HDInsight HBase
* Supervisión de HDInsight Interactive Query
* Supervisión de HDInsight Kafka
* Supervisión de HDInsight Spark
* Supervisión de HDInsight Storm

Para instrucciones sobre una solución de administración, consulte [Soluciones de administración en Azure](../azure-monitor/insights/solutions.md#install-a-monitoring-solution). Para experimentar, instale una solución de supervisión de HDInsight Hadoop. Cuando haya finalizado, verá un icono **HDInsightHadoop** en **Resumen**. Seleccione el icono **HDInsightHadoop**. La solución HDInsightHadoop tiene el siguiente aspecto:

:::image type="content" source="media/hdinsight-hadoop-oms-log-analytics-tutorial/hdinsight-oms-hdinsight-hadoop-monitoring-solution.png" alt-text="Vista de solución de supervisión de HDInsight":::

Dado que el clúster es un clúster nuevo de marca, el informe no muestra ninguna actividad.

## <a name="configuring-performance-counters"></a>Configuración de contadores de rendimiento

Azure Monitor admite la recopilación y el análisis de las métricas de rendimiento de los nodos del clúster. Para obtener más información, consulte [Orígenes de datos de rendimiento de Windows y Linux en Azure Monitor](../azure-monitor/agents/data-sources-performance-counters.md#linux-performance-counters).

## <a name="cluster-auditing"></a>Auditoría de clústeres

HDInsight admite la auditoría de clústeres con registros de Azure Monitor, mediante la importación de los siguientes tipos de registros:

* `log_gateway_audit_CL`: esta tabla proporciona registros de auditoría de nodos de puerta de enlace de clúster que muestran intentos de inicio de sesión correctos y erróneos.
* `log_auth_CL`: esta tabla proporciona registros SSH con intentos de inicio de sesión correctos y erróneos.
* `log_ambari_audit_CL`: esta tabla proporciona registros de auditoría de Ambari.
* `log_ranger_audti_CL`: esta tabla proporciona registros de auditoría de Apache Ranger en clústeres ESP.

## <a name="next-steps"></a>Pasos siguientes

* [Consulta a los registros de Azure Monitor para supervisar clústeres de HDInsight](hdinsight-hadoop-oms-log-analytics-use-queries.md)
* [Supervisión de la disponibilidad del clúster con los registros de Apache Ambari y Azure Monitor](./hdinsight-cluster-availability.md)