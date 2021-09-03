---
title: Supervisión del costo de Container Insights | Microsoft Docs
description: En este artículo se describe el costo de supervisión de las métricas y los datos de inventario recopilados por Container Insights para ayudar a los clientes a administrar su uso y los costos asociados.
ms.topic: conceptual
ms.date: 05/29/2020
ms.openlocfilehash: 2ff3616b3c78bbd7202acb08ffb3e46e0f7591b0
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/13/2021
ms.locfileid: "121738503"
---
# <a name="understand-monitoring-costs-for-container-insights"></a>Descripción de los costos de supervisión de Container Insights

En este artículo se proporcionan una guía de precios de Container Insights, que le ayuda a entender lo siguiente:

* Cómo calcular los costos por adelantado antes de habilitar esta información

* Cómo medir los costos después de habilitar Container Insights en uno o varios contenedores

* Cómo controlar la recopilación de datos y reducir los costos

Los registros de Azure Monitor recopilan, indexan y almacenan los datos generados por el clúster de Kubernetes. 

El modelo de precios de Azure Monitor se basa principalmente en la cantidad de datos ingeridos en gigabytes por día en el área de trabajo de Log Analytics. Los costos de un área de trabajo de Log Analytics no se basan solo en el volumen de datos recopilados, sino que también dependen del plan seleccionado y de cuánto tiempo se decida almacenar los datos generados a partir de los clústeres.

>[!NOTE]
>Los tamaños y los precios solo se proporcionan a título estimativo. Consulte la página de [precios](https://azure.microsoft.com/pricing/details/monitor/) de Azure Monitor para ver los precios más recientes basándose en el modelo de precios de Azure Monitor Log Analytics y en la región de Azure.

A continuación se ofrece un resumen de los tipos de datos que se recopilan de un clúster de Kubernetes con Container Insights que influyen en el costo y se pueden personalizar en función del uso:

- Registros de contenedor de stdout y stderr de cada contenedor supervisado en cada espacio de nombres de Kubernetes del clúster

- Variables de entorno de contenedor de cada contenedor supervisado en el clúster

- Trabajos y pods de Kubernetes completados en el clúster que no requieren supervisión

- Extracción activa de métricas de Prometheus

- [Recopilación de registros de diagnóstico](../../aks/monitor-aks.md#configure-monitoring) del nodo maestro de Kubernetes en el clúster de AKS para analizar los datos de registro generados por los componentes maestros, como *kube-apiserver* y *kube-controller-manager*.

## <a name="what-is-collected-from-kubernetes-clusters"></a>Qué se recopila de los clústeres de Kubernetes

Container Insights incluye un conjunto predefinido de métricas y elementos de inventario recopilados que se escriben como datos de registro en el área de trabajo de Log Analytics. Todas las métricas que se muestran a continuación se recopilan de forma predeterminada cada minuto.

### <a name="node-metrics-collected"></a>Métricas de nodo recopiladas

En la siguiente lista se muestran las 24 métricas por nodo que se recopilan:

- cpuUsageNanoCores
- cpuCapacityNanoCores
- cpuAllocatableNanoCores
- memoryRssBytes
- memoryWorkingSetBytes
- memoryCapacityBytes
- memoryAllocatableBytes
- restartTimeEpoch
- used (disk)
- free (disk)
- used_percent (disk)
- io_time (diskio)
- writes (diskio)
- reads (diskio)
- write_bytes (diskio)
- write_time (diskio)
- iops_in_progress (diskio)
- read_bytes (diskio)
- read_time (diskio)
- err_in (net)
- err_out (net)
- bytes_recv (net)
- bytes_sent (net)
- Kubelet_docker_operations (kubelet)

### <a name="container-metrics"></a>Métricas de contenedor

En la lista siguiente se muestran las ocho métricas por contenedor recopiladas:

- cpuUsageNanoCores
- cpuRequestNanoCores
- cpuLimitNanoCores
- memoryRssBytes
- memoryWorkingSetBytes
- memoryRequestBytes
- memoryLimitBytes
- restartTimeEpoch

### <a name="cluster-inventory"></a>Inventario de clústeres

En la siguiente lista se incluyen los datos de inventario de clústeres recopilados de forma predeterminada:

- KubePodInventory: 1 por minuto y contenedor
- KubeNodeInventory: 1 por nodo y minuto
- KubeServices: 1 por servicio y minuto
- ContainerInventory: 1 por contenedor y minuto

## <a name="estimating-costs-to-monitor-your-aks-cluster"></a>Estimación de costos para supervisar un clúster de AKS

La estimación siguiente se basa en un clúster de Azure Kubernetes Service (AKS) con el siguiente ejemplo de ajuste de tamaño. Además, la estimación solo se aplica a las métricas y los datos de inventario recopilados. En el caso de los registros de contenedor (stdout, stderr y variables de entorno), varía en función de los tamaños de registro generados por la carga de trabajo y están excluidos de nuestra estimación.

Si ha habilitado la supervisión de un clúster de AKS configurado de la siguiente manera:

- Tres nodos
- Dos discos por nodo
- Una interfaz de red por nodo
- 20 pods (un contenedor en cada pod = 20 contenedores en total)
- Dos espacios de nombres de Kubernetes
- Cinco servicios de Kubernetes (incluyen pods, servicios y espacio de nombres de kube-system)
- Frecuencia de recopilación = 60 seg (valor predeterminado)

Puede ver las tablas y el volumen de datos generados por hora en el área de trabajo de Log Analytics asignada. Para más información sobre cada una de estas tablas, consulte [Tablas de registros de Azure Monitor](../../aks/monitor-aks-reference.md#azure-monitor-logs-tables).

|Tabla | Estimación de tamaño (MB/hora) |
|------|---------------|
|Perf | 12.9 |
|InsightsMetrics | 11.3 |
|KubePodInventory | 1.5 |
|KubeNodeInventory | 0,75 |
|KubeServices | 0,13 |
|ContainerInventory | 3.6 |
|KubeHealth | 0,1 |
|KubeMonAgentEvents |0,005 |

Total = 31 MB/hora = 23,1 GB/mes (un mes = 31 días)

Si utiliza los [precios](https://azure.microsoft.com/pricing/details/monitor/) predeterminados para Log Analytics, que es un modelo de Pago por uso, puede calcular el costo de Azure Monitor al mes. Después de incluir una reserva de capacidad, el precio mensual aumentaría en función de la reserva seleccionada.

## <a name="controlling-ingestion-to-reduce-cost"></a>Control de la ingesta para reducir los costos

Tenga en cuenta la posibilidad de un escenario en el que las distintas unidades de negocio de su organización compartieran la infraestructura de Kubernetes y un área de trabajo de Log Analytics. Con cada unidad de negocio separada por un espacio de nombres de Kubernetes. Puede visualizar la cantidad de datos que se ingieren en cada área de trabajo con el runbook **Uso de datos** que está disponible en el menú desplegable **Ver libros**.

[![Lista desplegable Ver libros](media/container-insights-cost/workbooks-dropdown.png)](media/container-insights-cost/workbooks-dropdown.png#lightbox)


Este libro le ayuda a visualizar el origen de los datos sin tener que crear su propia biblioteca de consultas desde lo que compartimos nuestra documentación. En este libro, hay gráficos con los que puede ver los datos facturables desde perspectivas como:

- Datos facturables totales ingeridos en GB por solución
- Datos facturables ingeridos por registros de contenedor (registros de aplicación)
- Datos de registros de contenedores facturables ingeridos por espacio de nombres de Kubernetes
- Datos de registros de contenedores facturables ingeridos segregados por nombre de clúster
- Datos de registros de contenedores facturables ingeridos por entrada de origen de registro
- Datos de diagnóstico facturables ingeridos por registros de nodo maestro de diagnóstico

[![Libro de uso de datos](media/container-insights-cost/data-usage-workbook.png)](media/container-insights-cost/data-usage-workbook.png#lightbox)

Para más información sobre cómo administrar derechos y permisos en el libro, consulte [Control de acceso](../visualize/workbooks-access-control.md).

Después de completar el análisis para determinar qué origen u orígenes están generando la mayor parte de los datos o más datos que estén superando sus requisitos, puede volver a configurar la recopilación de datos. Los detalles sobre la configuración de la recopilación de stdout, stderr y variables de entorno se describen en el artículo sobre la [configuración de los valores de recopilación de datos del agente](container-insights-agent-config.md).

A continuación se muestran ejemplos de los cambios que se pueden aplicar al clúster mediante la modificación del archivo ConfigMap, que ayudan a controlar los costos.

1. Deshabilite los registros de stdout en todos los espacios de nombres del clúster; para ello, modifique lo siguiente en el archivo ConfigMap:

    ```
    [log_collection_settings]       
       [log_collection_settings.stdout]          
          enabled = false
    ```

2. Deshabilite la recopilación de registros de stderr del espacio de nombres de desarrollo (por ejemplo, **dev-test**) y siga recopilando registros de stderr de otros espacios de nombres (por ejemplo, **prod** y **default**); para ello, modifique lo siguiente en el archivo ConfigMap:

    >[!NOTE]
    >La recopilación de registros de kube-system está deshabilitada de forma predeterminada. Se conserva la configuración predeterminada y se agrega el espacio de nombres **dev-test** a la lista de espacios de nombres de exclusión que se aplica a la recopilación de registros de stderr.

    ```
    [log_collection_settings.stderr]          
       enabled = true          
          exclude_namespaces = ["kube-system", "dev-test"]
    ```

3. Deshabilite la recopilación de variables de entorno en el clúster; para ello, modifique lo siguiente en el archivo ConfigMap. Esto es aplicable a todos los contenedores de cada espacio de nombres de Kubernetes. 

    ```
    [log_collection_settings.env_var]
        enabled = false
    ```

4. Para limpiar los trabajos completados, especifique la directiva de limpieza en la definición del trabajo; para ello, modifique lo siguiente en el archivo ConfigMap:

    ```
    apiVersion: batch/v1
    kind: Job
    metadata:
      name: pi-with-ttl
    spec:
      ttlSecondsAfterFinished: 100
    ```

Después de aplicar uno o varios de estos cambios a ConfigMaps, consulte [Aplicación de ConfigMap actualizado](container-insights-prometheus-integration.md#applying-updated-configmap) para aplicarlo al clúster.

### <a name="prometheus-metrics-scraping"></a>Recopilación de métricas de Prometheus

Si utiliza la [extracción de métricas de Prometheus](container-insights-prometheus-integration.md), asegúrese de tener en cuenta lo siguiente para limitar el número de métricas que se recopilan del clúster:

- Asegúrese de que la frecuencia de extracción esté establecida de forma óptima (el valor predeterminado es de 60 segundos). Aunque puede aumentar la frecuencia a 15 segundos, debe asegurarse de que las métricas que se van a extraer se publiquen con esa frecuencia. De lo contrario, habrá muchas métricas extraídas que se enviarán a su área de trabajo de Log Analytics a intervalos, lo que aumentará los costos de ingesta de datos y de retención, pero con menos valor. 

- Container Insights admite listas de inclusión y exclusión por nombres de métrica. Por ejemplo, si va a extraer métricas de **kubedns** en el clúster, puede haber cientos de ellas que se extraigan de forma predeterminada, pero lo más probable es que solo esté interesado en un subconjunto. Confirme que haya especificado una lista de métricas para extraer o excluya otras excepto algunas para guardarlas en el volumen de ingesta de datos. Es fácil que habilite la extracción y no utilice muchas de esas métricas, lo que supondrá unos costos adicionales en la factura de Log Analytics.

- Al extraer mediante anotaciones de pod, asegúrese de filtrar por espacio de nombres para excluir la extracción de las métricas de pod de los espacios de nombres que no utiliza (por ejemplo, el espacio de nombres **dev-test**).

## <a name="next-steps"></a>Pasos siguientes

Para obtener más información sobre cómo comprender cuáles son los costos que se basarán probablemente en los patrones de uso recientes de los datos recopilados con Container Insights, vea [Administrar el uso y los costos con los registros de Azure Monitor](../logs/manage-cost-storage.md).