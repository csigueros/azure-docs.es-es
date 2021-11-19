---
title: Requisitos previos | Modo de conexión directa
description: Requisitos previos para implementar el controlador de datos en modo de conexión directa.
author: dnethi
ms.author: dinethi
ms.reviewer: mikeray
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
ms.date: 11/03/2021
ms.topic: overview
ms.openlocfilehash: cd1c12eaf4c4d5df575c7774386da007c9124712
ms.sourcegitcommit: 05c8e50a5df87707b6c687c6d4a2133dc1af6583
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/16/2021
ms.locfileid: "132549838"
---
# <a name="prerequisites-to-deploy-the-data-controller-in-direct-connectivity-mode"></a>Requisitos previos para implementar el controlador de datos en modo de conectividad directa

En este artículo se describe cómo prepararse para implementar un controlador de servicios de datos habilitados para Azure Arc en modo de conexión directa. Antes de implementar un controlador de datos de Azure Arc, comprenda los conceptos descritos en [Planeamiento de la implementación de servicios de datos habilitados para Azure Arc](plan-azure-arc-data-services.md).

En un nivel alto, los requisitos previos para crear un controlador de datos de Azure Arc en el modo de conectividad **directa** incluyen los siguientes:

1. Conexión del clúster de Kubernetes a Azure mediante Kubernetes habilitado para Azure Arc
2. Creación de un controlador de datos de servicios de datos habilitados para Azure Arc. Este paso implica la creación de
    - una extensión de servicios de datos de Azure Arc
    - Ubicación personalizada
    - un controlador de datos de Azure Arc
3. Si se desea la carga automática de registros en Azure Log Analytics, se necesitan el identificador del área de trabajo de Log Analytics y la clave de acceso compartido como parte de la implementación.

## <a name="1-connect-kubernetes-cluster-to-azure-using-azure-arc-enabled-kubernetes"></a>1. Conexión del clúster de Kubernetes a Azure mediante Kubernetes habilitado para Azure Arc

Para conectar el clúster de Kubernetes a Azure, use la CLI de Azure `az` con las siguientes extensiones o Helm.

### <a name="install-tools"></a>Instalación de herramientas

- Helm 3.3+[(instalar](https://helm.sh/docs/intro/install/))
- Instalar la última versión de la CLI de Azure o actualizar a esa versión ([descargar](https://aka.ms/installazurecliwindows))

### <a name="add-extensions-for-azure-cli"></a>Agregar extensiones para la CLI de Azure

Instale las versiones más recientes de las siguientes extensiones az:
- `k8s-extension`
- `connectedk8s`
- `k8s-configuration`
- `customlocation`

Ejecute el siguiente comando para instalar las extensiones az de la CLI:

```azurecli
az extension add --name k8s-extension
az extension add --name connectedk8s
az extension add --name k8s-configuration
az extension add --name customlocation
```

Si ya instaló previamente las extensiones `k8s-extension`, `connectedk8s` `k8s-configuration` y `customlocation`, actualícelas a la versión más reciente con el siguiente comando:

```azurecli
az extension update --name k8s-extension
az extension update --name connectedk8s
az extension update --name k8s-configuration
az extension update --name customlocation
```

### <a name="connect-your-cluster-to-azure"></a>Conexión del clúster a Azure

Para completar esta tarea, siga los pasos descritos en [Conexión de un clúster de Kubernetes existente a Azure Arc](../kubernetes/quickstart-connect-cluster.md).

## <a name="2-optionally-keep-the-log-analytics-workspace-id-and-shared-access-key-ready"></a>2. Opcionalmente, tenga el identificador del área de trabajo de Log Analytics y la clave de acceso compartido preparados.

Al implementar un controlador de datos habilitado para Azure Arc, puede habilitar la carga automática de métricas y registros durante la instalación. La carga de métricas usa la identidad administrada asignada por el sistema. Sin embargo, la carga de registros requiere un identificador de área de trabajo y la clave de acceso para el área de trabajo. 

También puede habilitar o deshabilitar la carga automática de métricas y registros después de implementar el controlador de datos. 

## <a name="3-create-azure-arc-data-services"></a>3. Creación de servicios de datos de Azure Arc

Una vez completados estos requisitos previos, puede consultar [Implementación del controlador de datos de Azure Arc | Modo de conexión directa](create-data-controller-direct-azure-portal.md).
