---
title: Acceso a recursos de Kubernetes desde Azure Portal
services: azure-arc
ms.service: azure-arc
ms.date: 10/31/2021
ms.topic: article
author: shashankbarsin
ms.author: shasb
description: Aprenda a interactuar con recursos de Kubernetes para administrar un clúster de Kubernetes habilitado para Azure Arc en Azure Portal.
ms.openlocfilehash: 5af26c853df67b213b1951224ae9603d15a9dd25
ms.sourcegitcommit: 27ddccfa351f574431fb4775e5cd486eb21080e0
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/08/2021
ms.locfileid: "132000588"
---
# <a name="access-kubernetes-resources-from-azure-portal"></a>Acceso a recursos de Kubernetes desde Azure Portal

Azure Portal incluye un visor de recursos de Kubernetes para facilitar el acceso a los recursos de Kubernetes en cualquier clúster de Kubernetes habilitado para Azure Arc. Ver los recursos de Kubernetes en Azure Portal reduce la necesidad de cambiar de contexto entre el Azure Portal y la herramienta de línea de comandos `kubectl`. De este modo, se simplifican la visualización y edición de recursos de Kubernetes. El visor de recursos actualmente incluye varios tipos de recursos, como implementaciones, pods y conjuntos de réplicas.

[!INCLUDE [preview features note](./includes/preview/preview-callout.md)]

## <a name="prerequisites"></a>Prerrequisitos

- Un clúster de Kubernetes existente [conectado](quickstart-connect-cluster.md) a Azure como recurso de Kubernetes habilitado para Azure Arc.

- La característica [Conexión de clúster](cluster-connect.md#enable-cluster-connect-feature) debe estar habilitada en el clúster de Kubernetes habilitado para Azure Arc.

- [Token de cuenta de servicio](cluster-connect.md#service-account-token-authentication-option) para la autenticación en el clúster.

## <a name="view-kubernetes-resources"></a>Visualización de recursos de Kubernetes

Para ver los recursos de Kubernetes, acceda al clúster de AKS en Azure Portal. El panel de navegación de la izquierda se usa para acceder a los recursos. Los recursos incluyen:

- **Espacios de nombres** muestra los espacios de nombres del clúster. El filtro situado en la parte superior de la lista de espacios de nombres permite filtrar y visualizar de forma rápida los recursos del espacio de nombres.
- **Cargas de trabajo** muestra información sobre implementaciones, pods, conjuntos de réplicas, objetos StatefulSet, conjuntos de demonios, trabajos y trabajos cron que se han implementado en el clúster.
- **Servicios y entradas** muestra todos los recursos de entrada y servicio del clúster.
- **Almacenamiento** muestra las clases de almacenamiento de Azure y la información del volumen persistente.
- **Configuración** muestra los secretos y objetos ConfigMap del clúster.

[ ![Información de las cargas de trabajo de Kubernetes que se muestran en Azure Portal](media/kubernetes-resource-view/workloads.png) ](media/kubernetes-resource-view/workloads.png#lightbox)

## <a name="edit-yaml"></a>Edición de YAML

La vista de recursos de Kubernetes también incluye un editor YAML. Tener un editor YAML integrado significa que puede actualizar objetos de Kubernetes desde el portal y aplicar los cambios de inmediato.

Después de editar el archivo YAML, los cambios se aplicarán al seleccionar **revisar + guardar**, confirmarlos y, a continuación, volver a guardar.

[ ![ Editor YAML para objetos de Kubernetes en Azure Portal](media/kubernetes-resource-view/yaml-editor.png) ](media/kubernetes-resource-view/yaml-editor.png#lightbox)

>[!WARNING]
> No se recomienda realizar cambios directos de producción a través de la interfaz de usuario o de la CLI y debe plantearse la posibilidad de usar [configuraciones (GitOps)](tutorial-use-gitops-connected-cluster.md) para entornos de producción. Las funcionalidades de administración de Kubernetes en Azure Portal y el editor YAML se han creado para aprender sobre nuevas implementaciones y lanzarlas en una configuración de desarrollo y pruebas.

## <a name="next-steps"></a>Pasos siguientes

Azure Monitor para contenedores proporciona información más detallada sobre los nodos y contenedores del clúster, en comparación con la vista lógica de los recursos de Kubernetes disponibles con la vista de recursos de Kubernetes descrita en este artículo. Aprenda a [implementar Azure Monitor para contenedores](../../azure-monitor/containers/container-insights-enable-arc-enabled-clusters.md?toc=/azure/azure-arc/kubernetes/toc.json) en un clúster.