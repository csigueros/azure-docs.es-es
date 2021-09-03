---
title: Validación de Kubernetes habilitado para Azure Arc
services: azure-arc
ms.service: azure-arc
ms.date: 03/03/2021
ms.topic: article
author: shashankbarsin
ms.author: shasb
description: Describe el programa de validación de Arc para distribuciones de Kubernetes
keywords: Kubernetes, Arc, Azure, K8s, validación
ms.openlocfilehash: 5d8e48a6e9dc5d8d95dacb649fb0748e4ae4daa5
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/13/2021
ms.locfileid: "121742676"
---
# <a name="azure-arc-enabled-kubernetes-validation"></a>Validación de Kubernetes habilitado para Azure Arc

Kubernetes habilitado para Azure Arc funciona con cualquier clúster de Kubernetes certificado por Cloud Native Computing Foundation (CNCF). El equipo de Azure Arc también ha trabajado con proveedores clave del sector que ofrecen Kubernetes para asegurarse de Kubernetes habilitado para Azure Arc con sus distribuciones de Kubernetes. Se asegurará la compatibilidad de las futuras versiones principales y secundarias de las distribuciones de Kubernetes publicadas por estos proveedores con Kubernetes habilitado para Azure Arc.

## <a name="validated-distributions"></a>Distribuciones validadas

Los siguientes proveedores de distribuciones e infraestructuras de Kubernetes proporcionados por Microsoft han superado correctamente las pruebas de conformidad de Kubernetes habilitado para Azure Arc:

| Proveedor de distribuciones e infraestructuras | Versión |
| ---------------------------------------- | ------- |
| Proveedor de Cluster API en Azure            | Versión de lanzamiento: [0.4.12](https://github.com/kubernetes-sigs/cluster-api-provider-azure/releases/tag/v0.4.12); versión de Kubernetes: [1.18.2](https://github.com/kubernetes/kubernetes/releases/tag/v1.18.2) |
| AKS en Azure Stack HCI                   | Versión de lanzamiento: [actualización de diciembre de 2020](https://github.com/Azure/aks-hci/releases/tag/AKS-HCI-2012); versión de Kubernetes: [1.18.8](https://github.com/kubernetes/kubernetes/releases/tag/v1.18.8) |

Los siguientes proveedores y sus distribuciones de Kubernetes correspondientes han superado correctamente las pruebas de conformidad de Kubernetes habilitado para Azure Arc:

| Nombre del proveedor | Nombre de distribución | Versión |
| ------------ | ----------------- | ------- |
| RedHat       | [OpenShift Container Platform](https://www.openshift.com/products/container-platform) | [4.5.41+](https://docs.openshift.com/container-platform/4.5/release_notes/ocp-4-5-release-notes.html), [4.6.35+](https://docs.openshift.com/container-platform/4.6/release_notes/ocp-4-6-release-notes.html), [4.7.18+](https://docs.openshift.com/container-platform/4.7/release_notes/ocp-4-7-release-notes.html) |
| VMware       | [Tanzu Kubernetes Grid](https://tanzu.vmware.com/kubernetes-grid) | Versión de Kubernetes: v1.17.5 |
| Canonical    | [Charmed Kubernetes](https://ubuntu.com/kubernetes) | [1.19](https://ubuntu.com/kubernetes/docs/1.19/components) |
| SUSE Rancher      | [Rancher Kubernetes Engine](https://rancher.com/products/rke/) | Versión de la CLI de RKE: [v1.2.4](https://github.com/rancher/rke/releases/tag/v1.2.4); versiones de Kubernetes: [1.19.6](https://github.com/kubernetes/kubernetes/releases/tag/v1.19.6), [1.18.14](https://github.com/kubernetes/kubernetes/releases/tag/v1.18.14), [1.17.16](https://github.com/kubernetes/kubernetes/releases/tag/v1.17.16)  |
| Nutanix      | [Karbon](https://www.nutanix.com/products/karbon)    | Versión 2.2.1 |

El equipo de Azure Arc también ha realizado las pruebas de conformidad y se ha asegurado de los escenarios de Kubernetes habilitado para Azure Arc en los siguientes proveedores de nube pública:

| Nombre del proveedor de nube pública | Nombre de distribución | Versión |
| -------------------------- | ----------------- | ------- |
| Amazon Web Services        | Elastic Kubernetes Service (EKS) | v1.18.9  |
| Google Cloud Platform      | Google Kubernetes Engine (GKE) | v1.17.15 |

## <a name="scenarios-validated"></a>Escenarios validados

Las pruebas de conformidad que se realizan como parte de la validación de Kubernetes habilitado para Azure Arc cubren los escenarios siguientes:

1. Conexión de clústeres de Kubernetes a Azure Arc: 
    * Implemente el gráfico de Helm del agente de Kubernetes habilitado para Azure Arc en el clúster.
    * Configure el certificado de Managed System Identity (MSI) en el clúster.
    * Los agentes envían metadatos de clúster a Azure.

2. Configuración: 
    * Cree la configuración a partir del recurso de Kubernetes habilitado para Azure Arc.
    * [Flux](https://docs.fluxcd.io/), necesario para configurar el flujo de trabajo de GitOps, se implementa en el clúster.
    * Flux extrae los manifiestos y los gráficos de Helm del repositorio de Git de demostración y los implementa en el clúster.

## <a name="next-steps"></a>Pasos siguientes

Aprenda a conectar un clúster a Azure Arc.
> [!div class="nextstepaction"]
> [Conexión de un clúster a Azure Arc](./quickstart-connect-cluster.md)
