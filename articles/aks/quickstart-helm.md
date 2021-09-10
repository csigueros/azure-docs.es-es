---
title: Desarrollo en Azure Kubernetes Service (AKS) con Helm
description: Use Helm con AKS y Azure Container Registry para empaquetar y ejecutar contenedores de aplicaciones en un clúster.
services: container-service
author: zr-msft
ms.topic: article
ms.date: 07/15/2021
ms.author: zarhoads
ms.openlocfilehash: cc060b0b23cbcef0551ec2660856d453b8a3b0e2
ms.sourcegitcommit: 7d63ce88bfe8188b1ae70c3d006a29068d066287
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 07/22/2021
ms.locfileid: "114454154"
---
# <a name="quickstart-develop-on-azure-kubernetes-service-aks-with-helm"></a>Inicio rápido: Desarrollo en Azure Kubernetes Service (AKS) con Helm

[Helm][helm] es una herramienta de empaquetado de código abierto que ayuda a instalar y administrar el ciclo de vida de las aplicaciones de Kubernetes. Al igual que los administradores de paquetes de Linux, como *APT* y *Yum*, Helm administra los gráficos de Kubernetes, que son paquetes de recursos de Kubernetes preconfigurados.

En esta guía de inicio rápido, usará Helm para empaquetar y ejecutar una aplicación en AKS. Para más información sobre cómo instalar una aplicación existente con Helm, consulte la guía paso a paso [Instalación de aplicaciones existentes con Helm en AKS][helm-existing].

## <a name="prerequisites"></a>Requisitos previos

* Suscripción a Azure. Si no tiene una suscripción a Azure, puede crear una [cuenta gratuita](https://azure.microsoft.com/free).
* [La CLI de Azure instalada](/cli/azure/install-azure-cli).
* [Helm v3 instalado][helm-install].

## <a name="create-an-azure-container-registry"></a>Creación de una instancia de Azure Container Registry
Tendrá que almacenar las imágenes de contenedor en una instancia de Azure Container Registry (ACR) para ejecutar la aplicación en el clúster de AKS mediante Helm. Proporcione su propio nombre de registro único en Azure que contenga entre 5 y 50 caracteres alfanuméricos. La SKU *básica* es un punto de entrada optimizado para costo con fines de desarrollo que proporciona un equilibrio entre almacenamiento y rendimiento.

En el ejemplo siguiente se usa [az acr create][az-acr-create] para crear una instancia de ACR llamada *MyHelmACR* en *MyResourceGroup* con la SKU *Básica*.

```azurecli
az group create --name MyResourceGroup --location eastus
az acr create --resource-group MyResourceGroup --name MyHelmACR --sku Basic
```

El resultado será similar al ejemplo siguiente. Tome nota del valor de *loginServer* para la instancia de ACR, puesto que lo usará en un paso posterior. En el ejemplo siguiente, *myhelmacr.azurecr.io* es el valor de *loginServer* para *MyHelmACR*.

```console
{
  "adminUserEnabled": false,
  "creationDate": "2019-06-11T13:35:17.998425+00:00",
  "id": "/subscriptions/<ID>/resourceGroups/MyResourceGroup/providers/Microsoft.ContainerRegistry/registries/MyHelmACR",
  "location": "eastus",
  "loginServer": "myhelmacr.azurecr.io",
  "name": "MyHelmACR",
  "networkRuleSet": null,
  "provisioningState": "Succeeded",
  "resourceGroup": "MyResourceGroup",
  "sku": {
    "name": "Basic",
    "tier": "Basic"
  },
  "status": null,
  "storageAccount": null,
  "tags": {},
  "type": "Microsoft.ContainerRegistry/registries"
}
```

## <a name="create-an-aks-cluster"></a>Creación de un clúster de AKS

El nuevo clúster de AKS necesita acceder a la instancia de ACR para extraer las imágenes de contenedor y ejecutarlas. Use el comando siguiente para:
* Cree un clúster de AKS denominado *MyAKS* y asocie *MyHelmACR*.
* Conceda al clúster *MyAKS* acceso a la instancia de ACR *MyHelmACR*.


```azurecli
az aks create -g MyResourceGroup -n MyAKS --location eastus  --attach-acr MyHelmACR --generate-ssh-keys
```

## <a name="connect-to-your-aks-cluster"></a>Conectarse al clúster AKS

Para conectar un clúster de Kubernetes de manera local, use [kubectl][kubectl], el cliente de línea de comandos de Kubernetes. Si usa Azure Cloud Shell, `kubectl` ya está instalado. 

1. Instale `kubectl` localmente mediante el comando `az aks install-cli`:

    ```azurecli
    az aks install-cli
    ```

2. Para configurar `kubectl` para conectarse a su clúster de Kubernetes, use el comando `az aks get-credentials`. En el comando de ejemplo siguiente se obtienen las credenciales del clúster de AKS llamado *MyAKS* en *MyResourceGroup*:  

    ```azurecli
    az aks get-credentials --resource-group MyResourceGroup --name MyAKS
    ```

## <a name="download-the-sample-application"></a>Descarga de la aplicación de ejemplo

En este inicio rápido usa la [aplicación Azure Vote][azure-vote-app]. Clone la aplicación desde GitHub y vaya al directorio `azure-vote`.

```console
git clone https://github.com/Azure-Samples/azure-voting-app-redis.git
cd azure-voting-app-redis/azure-vote/
```

## <a name="build-and-push-the-sample-application-to-the-acr"></a>Compilación e inserción de la aplicación de ejemplo en la instancia de ACR

Con el archivo Dockerfile anterior, ejecute el comando [az acr build][az-acr-build] para compilar e insertar una imagen en el registro. El signo `.` al final del comando establece la ubicación del archivo Dockerfile (en este caso, el directorio actual).

```azurecli
az acr build --image azure-vote-front:v1 \
  --registry MyHelmACR \
  --file Dockerfile .
```

> [!NOTE]
> Además de importar imágenes de contenedor en el ACR, también puede importar gráficos de Helm en el ACR. Para más información, consulte [Inserción y extracción de gráficos de Helm en Azure Container Registry][acr-helm].

## <a name="create-your-helm-chart"></a>Creación del gráfico de Helm

Genere el gráfico de Helm con el comando `helm create`.

```console
helm create azure-vote-front
```

Actualice *azure-vote-front/Chart.yaml* para agregar una dependencia para el gráfico *redis* desde el repositorio de gráficos `https://charts.bitnami.com/bitnami` y actualice `appVersion` a `v1`. Por ejemplo:

```yml
apiVersion: v2
name: azure-vote-front
description: A Helm chart for Kubernetes

dependencies:
  - name: redis
    version: 14.7.1
    repository: https://charts.bitnami.com/bitnami

...
# This is the version number of the application being deployed. This version number should be
# incremented each time you make changes to the application.
appVersion: v1
```

Actualice las dependencias de gráficos de Helm mediante `helm dependency update`:

```console
helm dependency update azure-vote-front
```

Actualice *azure-vote-front/values.yaml*:
* Agregue una sección *redis* para establecer los detalles de la imagen, el puerto del contenedor y el nombre de la implementación.
* Agregue un *backendName* para conectar la parte de front-end a la implementación de *redis*.
* Cambie *image.repository* a `<loginServer>/azure-vote-front`.
* Cambie *image.tag* a `v1`.
* Cambie *service.type* a *LoadBalancer*.

Por ejemplo:

```yml
# Default values for azure-vote-front.
# This is a YAML-formatted file.
# Declare variables to be passed into your templates.

replicaCount: 1
backendName: azure-vote-backend-master
redis:
  image:
    registry: mcr.microsoft.com
    repository: oss/bitnami/redis
    tag: 6.0.8
  fullnameOverride: azure-vote-backend
  auth:
    enabled: false

image:
  repository: myhelmacr.azurecr.io/azure-vote-front
  pullPolicy: IfNotPresent
  tag: "v1"
...
service:
  type: LoadBalancer
  port: 80
...
```

Agregue una sección `env` a *azure-vote-front/templates/deployment.yaml* para pasar el nombre de la implementación de *redis*.

```yml
...
      containers:
        - name: {{ .Chart.Name }}
          securityContext:
            {{- toYaml .Values.securityContext | nindent 12 }}
          image: "{{ .Values.image.repository }}:{{ .Values.image.tag | default .Chart.AppVersion }}"
          imagePullPolicy: {{ .Values.image.pullPolicy }}
          env:
          - name: REDIS
            value: {{ .Values.backendName }}
...
```

## <a name="run-your-helm-chart"></a>Ejecución del gráfico de Helm

Instale la aplicación con el gráfico de Helm mediante el comando `helm install`.

```console
helm install azure-vote-front azure-vote-front/
```

El servicio puede tardar unos minutos en devolver una dirección IP pública. Para supervisar el progreso, utilice el comando `kubectl get service` con el argumento `--watch`.

```console
$ kubectl get service azure-vote-front --watch
NAME               TYPE           CLUSTER-IP    EXTERNAL-IP     PORT(S)        AGE
azure-vote-front   LoadBalancer   10.0.18.228   <pending>       80:32021/TCP   6s
...
azure-vote-front   LoadBalancer   10.0.18.228   52.188.140.81   80:32021/TCP   2m6s
```

Vaya al equilibrador de carga de la aplicación en un explorador mediante la `<EXTERNAL-IP>` para ver la aplicación de ejemplo.

## <a name="delete-the-cluster"></a>Eliminación del clúster

Use el comando [az group delete][az-group-delete] para quitar el grupo de recursos, el clúster de AKS, el registro de contenedores, las imágenes de contenedor almacenadas en la instancia de ACR y todos los recursos relacionados.

```azurecli-interactive
az group delete --name MyResourceGroup --yes --no-wait
```

> [!NOTE]
> Cuando elimina el clúster, la entidad de servicio Azure Active Directory que utiliza el clúster de AKS no se quita. Para conocer los pasos que hay que realizar para quitar la entidad de servicio, consulte [Consideraciones principales y eliminación de AKS][sp-delete].
> 
> Si usó una identidad administrada, esta está administrada por la plataforma y no requiere que la quite.

## <a name="next-steps"></a>Pasos siguientes

Para más información sobre el uso de Helm, consulte la documentación de Helm.

> [!div class="nextstepaction"]
> [Documentación de Helm][helm-documentation]

[az-acr-create]: /cli/azure/acr#az_acr_create
[az-acr-build]: /cli/azure/acr#az_acr_build
[az-group-delete]: /cli/azure/group#az_group_delete
[az aks get-credentials]: /cli/azure/aks#az_aks_get_credentials
[az aks install-cli]: /cli/azure/aks#az_aks_install_cli
[azure-vote-app]: https://github.com/Azure-Samples/azure-voting-app-redis.git
[kubectl]: https://kubernetes.io/docs/user-guide/kubectl/
[helm]: https://helm.sh/
[helm-documentation]: https://helm.sh/docs/
[helm-existing]: kubernetes-helm.md
[helm-install]: https://helm.sh/docs/intro/install/
[sp-delete]: kubernetes-service-principal.md#additional-considerations
[acr-helm]: ../container-registry/container-registry-helm-repos.md