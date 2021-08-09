---
title: Escenarios de autenticación en Azure Container Registry desde Kubernetes
description: Introducción a las opciones y escenarios para autenticarse en una instancia de Azure Container Registry desde un clúster de Kubernetes para extraer imágenes de contenedor.
ms.topic: article
author: dlepow
ms.author: danlep
ms.date: 06/02/2021
ms.openlocfilehash: 738bdf617d17c0bd621614ee0fd32f2d0e18b729
ms.sourcegitcommit: 070122ad3aba7c602bf004fbcf1c70419b48f29e
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 06/04/2021
ms.locfileid: "111442216"
---
# <a name="scenarios-to-authenticate-with-azure-container-registry-from-kubernetes"></a>Escenarios de autenticación en Azure Container Registry desde Kubernetes


Puede usar una instancia de Azure Container Registry como origen de imágenes de contenedor para Kubernetes, incluidos los clústeres que administra, los clústeres administrados hospedados en [Azure Kubernetes Service (AKS)](../aks/intro-kubernetes.md) u otras nubes, y configuraciones de Kubernetes "locales", como [minikube](https://minikube.sigs.k8s.io/) y [kind](https://kind.sigs.k8s.io/). 

Para extraer imágenes en el clúster de Kuberentes desde una instancia de Azure Container Registry, es necesario establecer un mecanismo de autenticación y autorización. En función del entorno del clúster, elija uno de los métodos siguientes:

## <a name="scenarios"></a>Escenarios

| Clúster de Kubernetes |Método de autenticación  | Descripción  | Ejemplo | 
|---------|---------|---------|----------|
| Clúster de AKS |Identidad administrada de AKS    |  Habilite la [identidad administrada](../aks/use-managed-identity.md) de kubelet de AKS para extraer imágenes de una instancia asociada de Azure Container Registry.<br/><br/> El registro puede estar en la misma suscripción de Azure o en otra.      | [Autenticación con Azure Container Registry desde Azure Kubernetes Service](../aks/cluster-container-registry-integration.md?toc=/azure/container-registry/toc.json&bc=/azure/container-registry/breadcrumb/toc.json)| 
| Clúster de AKS | Entidad de servicio de AKS     | Habilite la [entidad de servicio de AKS](../aks/kubernetes-service-principal.md) con permisos para una instancia de Azure Container Registry de destino.<br/><br/>El registro puede estar en el mismo inquilino de Azure Active Directory o en otro.        | [Extracción de imágenes de un registro de contenedor a un clúster de AKS en un inquilino de Azure AD diferente](authenticate-aks-cross-tenant.md)
| Clúster de Kubernetes distinto de AKS |Pod [imagePullSecrets](https://kubernetes.io/docs/tasks/configure-pod-container/pull-image-private-registry/)   |  Use el mecanismo general de Kubernetes para administrar las credenciales del registro para las implementaciones de pods.<br/><br/>Configure la entidad de servicio de AD, el token con ámbito de repositorio u otras [credenciales del registro](container-registry-authentication.md).  | [Extracción de imágenes de una instancia de Azure Container Registry a un clúster de Kubernetes mediante un secreto de extracción](container-registry-auth-kubernetes.md) | 



## <a name="next-steps"></a>Pasos siguientes

* Más información sobre cómo [autenticarse con una instancia de Azure Container Registry](container-registry-authentication.md)
