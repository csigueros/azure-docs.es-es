---
title: Visualización de implementaciones de Container Insights (versión preliminar) | Microsoft Docs
description: En este artículo se describe la visualización en tiempo real de las implementaciones de Kubernetes sin usar kubectl en Container Insights.
ms.topic: conceptual
ms.date: 10/15/2019
ms.custom: references_regions
ms.openlocfilehash: 6b3ae515f2addf21f8183aed83ab26922e77f981
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/13/2021
ms.locfileid: "121734355"
---
# <a name="how-to-view-deployments-preview-in-real-time"></a>Cómo ver implementaciones (versión preliminar) en tiempo real

Con Container Insights, la característica de visualización de implementaciones (versión preliminar) emula el acceso directo a los objetos de implementación de Kubernetes en tiempo real mediante la exposición de los comandos `kubeclt get deployments` y `kubectl describe deployment {your deployment}`.

>[!NOTE]
>No se admiten los clústeres de AKS habilitados como [clústeres privados](https://azure.microsoft.com/updates/aks-private-cluster/) con esta característica. Esta característica se basa en el acceso directo a la API de Kubernetes a través de un servidor proxy desde el explorador. La habilitación de la seguridad de red para bloquear la API de Kubernetes desde este proxy bloqueará este tráfico.

Para más información, consulte la documentación de Kubernetes sobre las [Implementaciones](https://kubernetes.io/docs/concepts/workloads/controllers/deployment/).

## <a name="how-it-works"></a>Funcionamiento

La característica de datos en directo (versión preliminar) accede directamente a la API Kubernetes y se puede encontrar información adicional sobre el modelo de autenticación [aquí](https://kubernetes.io/docs/concepts/overview/kubernetes-api/).

La característica Implementaciones (versión preliminar) realiza una carga de una vez (actualizable) en el punto de conexión de las implementaciones `/apis/apps/v1/deployments`. Permite seleccionar una implementación determinada y cargar los detalles detallados de esa implementación específica en el punto de conexión de implementación `/apis/apps/v1/namespaces/${nameSpace}/deployments/${deploymentName}`.

Al seleccionar **Actualizar** en la parte superior izquierda de la página se actualiza la lista de implementación. Esto simula volver a ejecutar el comando `kubectl`.

>[!IMPORTANT]
>Durante el funcionamiento de esta característica, no se almacenan datos de forma permanente. Toda la información capturada durante la sesión se elimina al cerrar el explorador o salir de ella.

>[!NOTE]
>No se pueden anclar datos en directo (vista preliminar) desde la consola a un panel de Azure.

## <a name="deployments-describe"></a>Descripción de las implementaciones

Para ver los detalles de la descripción de una implementación, que es equivalente a `kubectl describe deployment`, realice los pasos siguientes.

1. En el Azure Portal, vaya al grupo de recursos de clúster de AKS y elija el recurso de AKS.

2. En el panel del clúster de AKS, en **Supervisión** a la izquierda, elija **Insights**.

3. Seleccione la pestaña **Implementaciones (vista preliminar)** .

    ![Visualización de las implementaciones en el Azure Portal](./media/container-insights-livedata-deployments/deployment-view.png)

La vista muestra una lista de todas las implementaciones en ejecución junto con el espacio de nombres y otra información detallada, emulando la ejecución del comando `kubectl get deployments –all-namespaces`. Puede organizar los resultados seleccionando una de las columnas.

![Detalles del panel de propiedades de las implementaciones](./media/container-insights-livedata-deployments/deployment-properties-pane-details.png)

Al seleccionar una implementación de la lista, se muestra automáticamente un panel de propiedades en el lado derecho de la página. Muestra información relacionada con la implementación seleccionada que se verá si ejecutó el comando `kubectl describe deployment {deploymentName}`. Es posible que haya observado que en la información de descripción faltan algunos detalles. En concreto, falta la **Plantilla**. La selección de la pestaña **Raw** le permite navegar a los detalles de la descripción sin analizar.

![Detalles raw del panel de propiedades de las implementaciones](./media/container-insights-livedata-deployments/deployment-properties-pane-raw.png)

Mientras examina los detalles de las implementaciones, puede ver los registros de contenedor y eventos en tiempo real. Elija la **Mostrar la consola en directo** y el panel de consola de datos en directo (vista preliminar) aparecerá debajo de la cuadrícula de datos de las implementaciones, donde puede ver los datos de registro en directo en un flujo continuo. Si el indicador de estado de captura muestra una marca de verificación verde, que se encuentra en el extremo derecho del panel, significa que pueden recuperar los datos y comienzan a transmitirse a la consola.

También puede filtrar por los eventos de nivel de clúster o de espacio de nombres. Para más información sobre la visualización de datos en tiempo real en la consola, consulte [Visualización de datos en directo (versión preliminar) con Container Insights](container-insights-livedata-overview.md).

![Visualización de las implementaciones de los datos en directo en la consola](./media/container-insights-livedata-deployments/deployments-console-view-events.png)

## <a name="next-steps"></a>Pasos siguientes

- Para continuar aprendiendo a usar Azure Monitor y supervisar otros aspectos de su clúster de AKS, consulte [Comprender el rendimiento del clúster de AKS con Azure Monitor para contenedores](container-insights-analyze.md).

- Vea [ejemplos de consultas de registro](container-insights-log-query.md) para ver ejemplos y consultas predefinidas para crear alertas, visualizaciones o realizar análisis adicionales de los clústeres.
