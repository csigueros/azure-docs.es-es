---
title: Habilitación de Cloud Controller Manager (versión preliminar)
description: Obtenga información sobre cómo habilitar el proveedor de nube fuera del árbol.
services: container-service
ms.topic: article
ms.date: 8/25/2021
ms.author: juda
ms.openlocfilehash: 533f4317c6c189e0691ab26822fe917c7567c1ce
ms.sourcegitcommit: f2d0e1e91a6c345858d3c21b387b15e3b1fa8b4c
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/07/2021
ms.locfileid: "123544389"
---
# <a name="enable-cloud-controller-manager-preview"></a>Habilitación de Cloud Controller Manager (versión preliminar)

Como proveedor de nube, Microsoft Azure trabaja en estrecha colaboración con la comunidad de Kubernetes para posibilitar nuestra infraestructura en nombre de los usuarios.

Actualmente, la integración del proveedor de nube en Kubernetes es "en el árbol", donde cualquier cambio en las características específicas de la nube debe seguir el ciclo de versiones estándar de Kubernetes.  Cuando se encuentran, se corrigen problemas o es necesario lanzar mejoras, es necesario hacerlo dentro del ciclo de lanzamiento de la comunidad de Kubernetes.

Ahora, la comunidad de Kubernetes está adoptando un modelo "fuera del árbol" en el que los proveedores de nube controlarán sus versiones independientemente de la programación principal de versiones de Kubernetes mediante el componente [cloud-provider-azure][cloud-provider-azure].  Ya hemos lanzado los controladores Cloud Storage Interface (CSI) para que sean la opción predeterminada en la versión 1.21 de Kubernetes y versiones posteriores.

> [!Note]
> Al habilitar Cloud Controller Manager en el clúster de AKS, también se habilitarán los controladores CSI fuera del árbol.

Cloud Controller Manager será el controlador predeterminado a partir de Kubernetes 1.22, compatible con AKS.


[!INCLUDE [preview features callout](./includes/preview/preview-callout.md)]

## <a name="before-you-begin"></a>Antes de empezar

Debe tener instalado el siguiente recurso:

* La CLI de Azure
* Extensión `aks-preview`, versión 0.5.5 o posterior
* Kubernetes versión 1.20.x o posterior


### <a name="register-the-enablecloudcontrollermanager-feature-flag"></a>Registro de la marca de característica `EnableCloudControllerManager`

Para usar la característica Cloud Controller Manager, debe habilitar la marca de característica `EnableCloudControllerManager` en la suscripción. 

```azurecli
az feature register --name EnableCloudControllerManager --namespace Microsoft.ContainerService
```
Puede comprobar el estado del registro con el comando [az feature list][az-feature-list]:

```azurecli-interactive
az feature list -o table --query "[?contains(name, 'Microsoft.ContainerService/EnableCloudControllerManager')].{Name:name,State:properties.state}"
```

Cuando haya terminado, actualice el registro del proveedor de recursos *Microsoft.ContainerService* con el comando [az provider register][az-provider-register]:

```azurecli-interactive
az provider register --namespace Microsoft.ContainerService
```

## <a name="create-an-aks-cluster-with-cloud-controller-manager"></a>Creación de un clúster de AKS con Cloud Controller Manager

Para crear un clúster con Cloud Controller Manager, pase `EnableCloudControllerManager=True` como encabezado de cliente a la API de Azure mediante la CLI de Azure.

```azurecli-interactive
az group create --name myResourceGroup --location eastus
az aks create -n aks -g myResourceGroup --aks-custom-headers EnableCloudControllerManager=True
```

## <a name="upgrade-an-aks-cluster-to-cloud-controller-manager"></a>Actualización de un clúster de AKS a Cloud Controller Manager

Para actualizar un clúster para que use Cloud Controller Manager, pase `EnableCloudControllerManager=True` como encabezado de cliente a la API de Azure mediante la CLI de Azure.

```azurecli-interactive
az aks upgrade -n aks -g myResourceGroup --aks-custom-headers EnableCloudControllerManager=True
```

## <a name="next-steps"></a>Pasos siguientes

- Para más información sobre los controladores CSI y el comportamiento predeterminado de las versiones de Kubernetes posteriores a la 1.21, consulte nuestra [documentación][csi-docs].

- Puede encontrar más información sobre la dirección de la comunidad de Kubernetes con respecto a los proveedores fuera del árbol en la [entrada de blog de la comunidad][community-blog].


<!-- LINKS - internal -->
[az-provider-register]: /cli/azure/provider#az_provider_register
[az-feature-register]: /cli/azure/feature#az_feature_register
[az-feature-list]: /cli/azure/feature#az_feature_list
[csi-docs]: csi-storage-drivers.md

<!-- LINKS - External -->
[community-blog]: https://kubernetes.io/blog/2019/04/17/the-future-of-cloud-providers-in-kubernetes
[cloud-provider-azure]: https://github.com/kubernetes-sigs/cloud-provider-azure
