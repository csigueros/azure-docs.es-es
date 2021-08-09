---
title: Autenticación con una instancia de Azure Container Registry mediante un secreto de extracción de Kubernetes
description: Obtenga información sobre cómo proporcionar a un clúster de Kubernetes acceso a las imágenes en su instancia de Azure Container Registry al crear un secreto de extracción con una entidad de servicio.
ms.topic: article
author: karolz-ms
ms.author: karolz
ms.reviewer: danlep
ms.date: 06/02/2021
ms.openlocfilehash: 149035de0fc84c75cdcaa73c91d6cd5379c53498
ms.sourcegitcommit: 070122ad3aba7c602bf004fbcf1c70419b48f29e
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 06/04/2021
ms.locfileid: "111439627"
---
# <a name="pull-images-from-an-azure-container-registry-to-a-kubernetes-cluster-using-a-pull-secret"></a>Extracción de imágenes de una instancia de Azure Container Registry a un clúster de Kubernetes mediante un secreto de extracción

Puede usar una instancia de Azure Container Registry como origen de las imágenes de contenedor con cualquier clúster de Kubernetes, incluidos los clústeres de Kubernetes "locales", como [minikube](https://minikube.sigs.k8s.io/) y [kind](https://kind.sigs.k8s.io/). En este artículo se muestra cómo crear un secreto de extracción de Kubernetes mediante credenciales para una instancia de Azure Container Registry. A continuación, use el secreto para extraer imágenes de una instancia de Azure Container Registry en una implementación de pods.

En este ejemplo se crea un secreto de extracción mediante [credenciales de entidad de servicio](container-registry-auth-service-principal.md) de Azure Active Directory. También puede configurar un secreto de extracción con otras credenciales de Azure Container Registry, como un [token de acceso con ámbito de repositorio.](container-registry-repository-scoped-permissions.md)

> [!NOTE]
> Aunque los secretos de extracción se usan habitualmente, conllevan una sobrecarga de administración adicional. Si usa [Azure Kubernetes Service](../aks/intro-kubernetes.md), se recomiendan [otras opciones](authenticate-kubernetes-options.md), como usar la identidad administrada o la entidad de servicio del clúster para extraer la imagen de manera segura sin un valor `imagePullSecrets` adicional en cada pod.

## <a name="prerequisites"></a>Prerrequisitos

En este artículo se supone que ya ha creado una instancia privada de Azure Container Registry. Además, debe tener un clúster de Kubernetes en ejecución y accesible a través de la herramienta de línea de comandos `kubectl`.

[!INCLUDE [container-registry-service-principal](../../includes/container-registry-service-principal.md)]

Si no guarda ni recuerda la contraseña de la entidad de servicio, puede restablecerla con el comando [az ad sp credential reset][az-ad-sp-credential-reset]:

```azurecli
az ad sp credential reset  --name http://<service-principal-name> --query password --output tsv
```

Este comando devuelve una nueva contraseña válida para la entidad de servicio.

## <a name="create-an-image-pull-secret"></a>Creación de un secreto de extracción de imágenes

Kubernetes usa un *secreto de extracción de imágenes* para almacenar la información necesaria para autenticarse en el registro. Para crear el secreto de extracción para una instancia de Azure Container Registry, debe proporcionar el identificador de la entidad de servicio, la contraseña y la dirección URL del registro. 

Cree un secreto de extracción de imágenes con el siguiente comando `kubectl`:

```console
kubectl create secret docker-registry <secret-name> \
    --namespace <namespace> \
    --docker-server=<container-registry-name>.azurecr.io \
    --docker-username=<service-principal-ID> \
    --docker-password=<service-principal-password>
```
donde:

| Value | Descripción |
| :--- | :--- |
| `secret-name` | Nombre del secreto de extracción de imágenes, por ejemplo, *acr-secret* |
| `namespace` | Espacio de nombres de Kubernetes donde colocar el secreto <br/> Solo es necesario si desea colocar el secreto en un espacio de nombres distinto del espacio de nombres predeterminado |
| `container-registry-name` | Nombre de la instancia de Azure Container Registry, por ejemplo, *miregistro*<br/><br/>`--docker-server` es el nombre completo del servidor de inicio de sesión del registro  |
| `service-principal-ID` | Identificador de la entidad de servicio que usará Kubernetes para tener acceso al registro |
| `service-principal-password` | Contraseña de la entidad de servicio |

## <a name="use-the-image-pull-secret"></a>Uso del secreto de extracción de imágenes

Una vez que haya creado el secreto de extracción de imágenes, puede usarlo para crear implementaciones y pods de Kubernetes. Proporcione el nombre del secreto en `imagePullSecrets` en el archivo de implementación. Por ejemplo:

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: my-awesome-app-pod
  namespace: awesomeapps
spec:
  containers:
    - name: main-app-container
      image: myregistry.azurecr.io/my-awesome-app:v1
      imagePullPolicy: IfNotPresent
  imagePullSecrets:
    - name: acr-secret
```

En el ejemplo anterior, `my-awesome-app:v1` es el nombre de la imagen que se va a extraer de Azure Container Registry, y `acr-secret` es el nombre del secreto de extracción que creó para tener acceso al registro. Al implementar el pod, Kubernetes extrae automáticamente la imagen del registro, si aún no está presente en el clúster.


## <a name="next-steps"></a>Pasos siguientes

* Para más información sobre cómo trabajar con entidades de servicio y Azure Container Registry, consulte [Autenticación de Azure Container Registry con entidades de servicio](container-registry-auth-service-principal.md).
* Más información sobre los secretos de extracción de imágenes en la [documentación de Kubernetes](https://kubernetes.io/docs/concepts/containers/images/#specifying-imagepullsecrets-on-a-pod)


<!-- IMAGES -->

<!-- LINKS - External -->
[acr-scripts-cli]: https://github.com/Azure/azure-docs-cli-python-samples/tree/master/container-registry
[acr-scripts-psh]: https://github.com/Azure/azure-docs-powershell-samples/tree/master/container-registry

<!-- LINKS - Internal -->
[az-ad-sp-credential-reset]: /cli/azure/ad/sp/credential#az_ad_sp_credential_reset