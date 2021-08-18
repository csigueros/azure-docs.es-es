---
title: Visualización de métricas en tiempo real con Container Insights | Microsoft Docs
description: En este artículo se describe cómo ver las métricas en tiempo real sin usar kubectl con Container Insights.
ms.topic: conceptual
ms.date: 10/15/2019
ms.custom: references_regions
ms.openlocfilehash: 5dffc98e73a9d9e85b301d3272869b7fb5118ab5
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/13/2021
ms.locfileid: "121747663"
---
# <a name="how-to-view-metrics-in-real-time"></a>Cómo ver métricas en tiempo real

La característica de datos en directo de Container Insights (versión preliminar) permite visualizar las métricas sobre el estado del nodo y el pod en un clúster en tiempo real. Emula el acceso directo a los comandos `kubectl top nodes`, `kubectl get pods –all-namespaces` y `kubectl get nodes` para llamar, analizar y visualizar los datos de los gráficos de rendimiento que se incluyen con esta conclusión.

En este artículo se proporciona una introducción detallada y le ayuda a entender cómo usar esta característica.

>[!NOTE]
>No se admiten los clústeres de AKS habilitados como [clústeres privados](https://azure.microsoft.com/updates/aks-private-cluster/) con esta característica. Esta característica se basa en el acceso directo a la API de Kubernetes a través de un servidor proxy desde el explorador. La habilitación de la seguridad de red para bloquear la API de Kubernetes desde este proxy bloqueará este tráfico.

Para obtener ayuda para configurar o solucionar problemas de la característica de datos en directo (versión preliminar), revise la [guía de instalación](container-insights-livedata-setup.md).

## <a name="how-it-works"></a>Funcionamiento

La característica de datos en directo (versión preliminar) accede directamente a la API Kubernetes y se puede encontrar información adicional sobre el modelo de autenticación [aquí](https://kubernetes.io/docs/concepts/overview/kubernetes-api/).

Esta característica realiza una operación de sondeo en los puntos de conexión de métricas (incluidos `/api/v1/nodes`, `/apis/metrics.k8s.io/v1beta1/nodes` y `/api/v1/pods`), que se realiza cada cinco segundos de forma predeterminada. Estos datos se almacenan en caché en el explorador y se muestran en los cuatro gráficos de rendimiento incluidos en Container Insights en la pestaña **Clúster** al seleccionar **Go Live (versión preliminar)** . Cada sondeo subsiguiente se representa en una ventana de visualización sucesiva de cinco minutos.

![Opción Go Live en la vista de clúster](./media/container-insights-livedata-metrics/cluster-view-go-live-example-01.png)

El intervalo de sondeo se configura desde la lista desplegable **Configurar intervalo**, lo que le permite establecer el sondeo de los nuevos datos cada 1, 5, 15 y 30 segundos.

![Intervalo de sondeo desplegable de Go Live](./media/container-insights-livedata-metrics/cluster-view-polling-interval-dropdown.png)

>[!IMPORTANT]
>Se recomienda establecer el intervalo de sondeo en un segundo mientras se soluciona un problema durante un breve período de tiempo. Estas solicitudes pueden afectar la disponibilidad y limitación de la API de Kubernetes en el clúster. Después, vuelva a establecer el intervalo de sondeo más largo.

>[!IMPORTANT]
>Durante el funcionamiento de esta característica, no se almacenan datos de forma permanente. Toda la información capturada durante la sesión se elimina al cerrar el explorador o cuando sale de ella. Los datos solo permanecen para su visualización en la ventana por un período de cinco minutos; las métricas con una antigüedad superior a cinco minutos se eliminan de forma permanente.

Estos gráficos no se pueden anclar al último panel de Azure que vio en modo activo.

## <a name="metrics-captured"></a>Métricas capturadas

### <a name="node-cpu-utilization---node-memory-utilization-"></a>% de uso de CPU de nodo / % uso de memoria de nodo

Estos dos gráficos de rendimiento se asignan a un equivalente de invocar `kubectl top nodes` y capturar los resultados de las columnas **CPU%** y **MEMORY%** en el gráfico correspondiente.

![Resultados de ejemplo de los nodos principales de Kubectl](./media/container-insights-livedata-metrics/kubectl-top-nodes-example.png)

![Gráfico de porcentaje de uso de la CPU del nodo](./media/container-insights-livedata-metrics/cluster-view-node-cpu-util.png)

![Gráfico de porcentaje de uso de la memoria del nodo](./media/container-insights-livedata-metrics/cluster-view-node-memory-util.png)

Los cálculos de percentiles funcionarán en clústeres más grandes para ayudar a identificar los nodos de valores atípicos en el clúster. Por ejemplo, para saber si los nodos están infrautilizados para reducir la escala. Con la agregación **Min** puede ver qué nodos tienen poco uso en el clúster. Para realizar una investigación más detallada, seleccione la pestaña **Nodos** y ordene la cuadrícula según el uso de la CPU o de la memoria.

Esto también le ayuda a comprender qué nodos se están insertando en sus límites y si puede ser necesario escalar horizontalmente. El uso de las agregaciones **Max** y **P95** puede ayudarle a ver si hay nodos en el clúster con un uso elevado de los recursos. Para realizar una investigación más detallada, debe cambiar a la pestaña **Nodos**.

### <a name="node-count"></a>Número de nodos

Este gráfico de rendimiento se asigna a un equivalente de invocar `kubectl get nodes` y asignar la columna de **estado** a un gráfico agrupado por tipos de estado.

![Kubectl get nodes example results](./media/container-insights-livedata-metrics/kubectl-get-nodes-example.png)

![Gráfico de número de nodos](./media/container-insights-livedata-metrics/cluster-view-node-count-01.png)

Los nodos se informan en un estado **Ready** (Listo) o **Not Ready** (No listo). Se cuentan (se crea un recuento total) y los resultados de estas dos agregaciones se muestran en un gráfico.
Por ejemplo, para saber si los nodos entran en estados con errores. Con la agregación **Not Ready**, puede ver rápidamente el número de nodos del clúster actualmente en el estado **Not Ready**.

### <a name="active-pod-count"></a>Número de pods activos

Este gráfico de rendimiento se asigna a un equivalente de invocar `kubectl get pods –all-namespaces` y asignar la columna de **Estado** a un gráfico agrupado por tipos de estado.

![Resultados del ejemplo de obtención de pods de Kubectl](./media/container-insights-livedata-metrics/kubectl-get-pods-example.png)

![Gráfico de recuento de pod de nodos](./media/container-insights-livedata-metrics/cluster-view-node-pod-count.png)

>[!NOTE]
>Es posible que los nombres de estado que interpreta `kubectl` no coincidan exactamente con el gráfico.

## <a name="next-steps"></a>Pasos siguientes

Vea [ejemplos de consultas de registro](container-insights-log-query.md) para ver ejemplos y consultas predefinidas para crear alertas, visualizaciones o realizar análisis adicionales de los clústeres.
