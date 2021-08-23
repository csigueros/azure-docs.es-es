---
title: Requisitos previos | Modo de conexión directa
description: Requisitos previos para implementar el controlador de datos en modo de conexión directa.
author: dnethi
ms.author: dinethi
ms.reviewer: mikeray
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
ms.date: 03/31/2021
ms.topic: overview
ms.openlocfilehash: 143a601f89301d3ed4302efa6e978cba53539e2c
ms.sourcegitcommit: ee8ce2c752d45968a822acc0866ff8111d0d4c7f
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 07/14/2021
ms.locfileid: "113734077"
---
# <a name="prerequisites-to-deploy-the-data-controller-in-direct-connectivity-mode"></a>Requisitos previos para implementar el controlador de datos en modo de conectividad directa

En este artículo se describe cómo prepararse para implementar un controlador de servicios de datos habilitados para Azure Arc en modo de conexión directa. La implementación del controlador de datos de Azure Arc requiere conocimientos y conceptos adicionales, tal como se describe en [Plan de implementación de servicios de datos habilitados para Azure Arc](plan-azure-arc-data-services.md).

[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

En un nivel alto, los requisitos previos para crear un controlador de datos de Azure Arc en el modo de conectividad **directa** incluyen los siguientes:

1. Conexión del clúster de Kubernetes a Azure mediante Kubernetes habilitado para Azure Arc
2. Creación de la entidad de servicio y configuración de roles para métricas
3. Creación de un controlador de datos de servicios de datos habilitados para Azure Arc. Este paso implica la creación de
    - una extensión de servicios de datos de Azure Arc
    - una ubicación personalizada
    - un controlador de datos de Azure Arc

## <a name="1-connect-kubernetes-cluster-to-azure-using-azure-arc-enabled-kubernetes"></a>1. Conexión del clúster de Kubernetes a Azure mediante Kubernetes habilitado para Azure Arc

La conexión del clúster de Kubernetes a Azure se puede llevar a cabo mediante la CLI de ```az```, con las siguientes extensiones, así como Helm.

#### <a name="install-tools"></a>Instalación de herramientas

- Helm 3.3+[(instalar](https://helm.sh/docs/intro/install/))
- Instalar o actualizar a la última versión de la CLI de Azure ([instalar](/sql/azdata/install/deploy-install-azdata))

#### <a name="add-extensions-for-azure-cli"></a>Agregar extensiones para la CLI de Azure

Instale las versiones más recientes de las siguientes extensiones az:
- ```k8s-extension```
- ```connectedk8s```
- ```k8s-configuration```
- `customlocation`

Ejecute el siguiente comando para instalar las extensiones az de la CLI:

```azurecli
az extension add --name k8s-extension
az extension add --name connectedk8s
az extension add --name k8s-configuration
az extension add --name customlocation
```

Si ya instaló previamente las extensiones ```k8s-extension```, ```connectedk8s``` ```k8s-configuration``` y `customlocation`, actualícelas a la versión más reciente con el siguiente comando:

```azurecli
az extension update --name k8s-extension
az extension update --name connectedk8s
az extension update --name k8s-configuration
az extension update --name customlocation
```
#### <a name="connect-your-cluster-to-azure"></a>Conexión del clúster a Azure

Para completar esta tarea, siga los pasos descritos en [Conexión de un clúster de Kubernetes existente a Azure Arc](../kubernetes/quickstart-connect-cluster.md).

Después de conectar el clúster a Azure, continúe con la creación de una entidad de servicio. 

## <a name="2-create-service-principal-and-configure-roles-for-metrics"></a>2. Creación de una entidad de servicio y configuración de roles para métricas

Siga los pasos detallados en el artículo sobre la [carga de métricas](upload-metrics-and-logs-to-azure-monitor.md), cree una entidad de servicio y conceda los roles tal como se describe en el artículo. 

La información de ClientID, TenantID y secreto de cliente de SPN será necesaria para [implementar el controlador de datos de Azure Arc](create-data-controller-direct-azure-portal.md). 

## <a name="3-create-azure-arc-data-services"></a>3. Creación de servicios de datos de Azure Arc

Una vez completados estos requisitos previos, puede consultar [Implementación del controlador de datos de Azure Arc | Modo de conexión directa](create-data-controller-direct-azure-portal.md).


