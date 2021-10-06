---
title: Implementación de una aplicación Java con Open Liberty o WebSphere Liberty en un clúster de Azure Kubernetes Service (AKS)
description: Implemente una aplicación Java con Open Liberty o WebSphere Liberty en un clúster de Azure Kubernetes Service (AKS).
author: jiangma
ms.author: jiangma
ms.service: container-service
ms.topic: conceptual
ms.date: 02/01/2021
keywords: java, jakartaee, javaee, microprofile, open-liberty, websphere-liberty, aks, kubernetes
ms.custom:
- devx-track-java
- devx-track-javaee
ms.openlocfilehash: fd36c92764f8acd9526fdd04f7ef60e4219839f3
ms.sourcegitcommit: 87de14fe9fdee75ea64f30ebb516cf7edad0cf87
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/01/2021
ms.locfileid: "129350779"
---
# <a name="deploy-a-java-application-with-open-liberty-or-websphere-liberty-on-an-azure-kubernetes-service-aks-cluster"></a>Implementación de una aplicación Java con Open Liberty o WebSphere Liberty en un clúster de Azure Kubernetes Service (AKS)

En este artículo se explica cómo:  
* Ejecutar una aplicación Java, Java EE, Yakarta EE o Microprofile en el runtime en Open Liberty o WebSphere Liberty.
* Compilar la imagen de Docker de la aplicación mediante imágenes de contenedor de Open Liberty.
* Implementar la aplicación contenedorizada en un clúster de AKS mediante el operador de Open Liberty.   

El operador de Open Liberty simplifica la implementación y administración de las aplicaciones que se ejecutan en clústeres de Kubernetes. Con este operador también puede realizar operaciones más avanzadas, como recopilar seguimientos y volcados. 

Para más detalles sobre Open Liberty, consulte [la página del proyecto Open Liberty](https://openliberty.io/). Para obtener más información sobre IBM WebSphere Liberty, consulte [la página del producto WebSphere Liberty](https://www.ibm.com/cloud/websphere-liberty).

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]

* En este artículo se necesita la versión más reciente de la CLI de Azure. Si usa Azure Cloud Shell, ya está instalada la versión más reciente.
* Si ejecuta los comandos de esta guía de forma local (en lugar de Azure Cloud Shell):
  * Prepare una máquina local con un sistema operativo similar a Unix instalado (por ejemplo, Ubuntu, macOS o Subsistema de Windows para Linux).
  * Instale una implementación de Java SE (por ejemplo, [AdoptOpenJDK OpenJDK 8 LTS/OpenJ9](https://adoptopenjdk.net/?variant=openjdk8&jvmVariant=openj9)).
  * Instale [Maven](https://maven.apache.org/download.cgi) 3.5.0 o una versión superior.
  * Instale [Docker](https://docs.docker.com/get-docker/) para el sistema operativo.

## <a name="create-a-resource-group"></a>Crear un grupo de recursos

Un grupo de recursos de Azure es un grupo lógico en el que se implementan y administran recursos de Azure.  

Cree un grupo de recursos denominado *java-liberty-project* mediante el comando [az group create](/cli/azure/group#az_group_create) en la ubicación *eastus*. Este grupo de recursos se usará más adelante para crear la instancia de Azure Container Registry (ACR) y el clúster de AKS. 

```azurecli-interactive
RESOURCE_GROUP_NAME=java-liberty-project
az group create --name $RESOURCE_GROUP_NAME --location eastus
```

## <a name="create-an-acr-instance"></a>Creación de una instancia de ACR

Use el comando [az acr create](/cli/azure/acr#az_acr_create) para crear la instancia de ACR. En el ejemplo siguiente se crea una instancia de ACR denominada *youruniqueacrname*. Asegúrese de que *youruniqueacrname* sea única en Azure.

```azurecli-interactive
export REGISTRY_NAME=youruniqueacrname
az acr create --resource-group $RESOURCE_GROUP_NAME --name $REGISTRY_NAME --sku Basic --admin-enabled
```

Tras un breve período de tiempo, debería ver una salida JSON que contiene:

```output
  "provisioningState": "Succeeded",
  "publicNetworkAccess": "Enabled",
  "resourceGroup": "java-liberty-project",
```

### <a name="connect-to-the-acr-instance"></a>Conexión a la instancia de ACR

Tendrá que iniciar sesión en la instancia de ACR para poder insertar una imagen en ella. Ejecute los comandos siguientes para comprobar la conexión:

```azurecli-interactive
export LOGIN_SERVER=$(az acr show -n $REGISTRY_NAME --query 'loginServer' -o tsv)
export USER_NAME=$(az acr credential show -n $REGISTRY_NAME --query 'username' -o tsv)
export PASSWORD=$(az acr credential show -n $REGISTRY_NAME --query 'passwords[0].value' -o tsv)

docker login $LOGIN_SERVER -u $USER_NAME -p $PASSWORD
```

Debería ver `Login Succeeded` al final de la salida de los comandos si ha iniciado sesión correctamente en la instancia de ACR.

## <a name="create-an-aks-cluster"></a>Creación de un clúster de AKS

Use el comando [az aks create](/cli/azure/aks#az_aks_create) para crear un clúster de AKS. En el siguiente ejemplo se crea un clúster denominado *myAKSCluster* con un nodo. Este proceso tardará varios minutos en completarse.

```azurecli-interactive
CLUSTER_NAME=myAKSCluster
az aks create --resource-group $RESOURCE_GROUP_NAME --name $CLUSTER_NAME --node-count 1 --generate-ssh-keys --enable-managed-identity
```

Transcurridos unos minutos, el comando se completa y devuelve información en formato JSON sobre el clúster, incluida la siguiente:

```output
  "nodeResourceGroup": "MC_java-liberty-project_myAKSCluster_eastus",
  "privateFqdn": null,
  "provisioningState": "Succeeded",
  "resourceGroup": "java-liberty-project",
```

### <a name="connect-to-the-aks-cluster"></a>Conexión al clúster de AKS

Para administrar un clúster de Kubernetes, usará [kubectl](https://kubernetes.io/docs/reference/kubectl/overview/), el cliente de línea de comandos de Kubernetes. Si usa Azure Cloud Shell, `kubectl` ya está instalado. Para instalar `kubectl` localmente, use el comando [az aks install-cli](/cli/azure/aks#az_aks_install_cli):

```azurecli-interactive
az aks install-cli
```

Para configurar `kubectl` para conectarse a su clúster de Kubernetes, use el comando [az aks get-credentials](/cli/azure/aks#az_aks_get_credentials). Con este comando se descargan las credenciales y se configura la CLI de Kubernetes para usarlas.

```azurecli-interactive
az aks get-credentials --resource-group $RESOURCE_GROUP_NAME --name $CLUSTER_NAME --overwrite-existing
```

> [!NOTE]
> El comando anterior usa la ubicación predeterminada para el [archivo de configuración de Kubernetes](https://kubernetes.io/docs/concepts/configuration/organize-cluster-access-kubeconfig/), `~/.kube/config`. Puede especificar otra ubicación para el archivo de configuración de Kubernetes con la opción *--file*.

Para comprobar la conexión al clúster, use el comando [kubectl get]( https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get) para devolver una lista de los nodos del clúster.

```azurecli-interactive
kubectl get nodes
```

La salida del ejemplo siguiente muestra el nodo único creado en los pasos anteriores. Asegúrese de que el estado del nodo es *Listo*:

```output
NAME                                STATUS   ROLES   AGE     VERSION
aks-nodepool1-xxxxxxxx-yyyyyyyyyy   Ready    agent   76s     v1.18.10
```

## <a name="install-open-liberty-operator"></a>Instalación de Operator de Open Liberty

Después de crear el clúster y conectarse a él, ejecute los comandos siguientes para instalar [Operator de Open Liberty](https://github.com/OpenLiberty/open-liberty-operator/tree/master/deploy/releases/0.7.1).

```azurecli-interactive
OPERATOR_NAMESPACE=default
WATCH_NAMESPACE='""'

# Install Custom Resource Definitions (CRDs) for OpenLibertyApplication
kubectl apply -f https://raw.githubusercontent.com/OpenLiberty/open-liberty-operator/master/deploy/releases/0.7.1/openliberty-app-crd.yaml

# Install cluster-level role-based access
curl -L https://raw.githubusercontent.com/OpenLiberty/open-liberty-operator/master/deploy/releases/0.7.1/openliberty-app-cluster-rbac.yaml \
      | sed -e "s/OPEN_LIBERTY_OPERATOR_NAMESPACE/${OPERATOR_NAMESPACE}/" \
      | kubectl apply -f -

# Install the operator
curl -L https://raw.githubusercontent.com/OpenLiberty/open-liberty-operator/master/deploy/releases/0.7.1/openliberty-app-operator.yaml \
      | sed -e "s/OPEN_LIBERTY_WATCH_NAMESPACE/${WATCH_NAMESPACE}/" \
      | kubectl apply -n ${OPERATOR_NAMESPACE} -f -
```

## <a name="build-application-image"></a>Creación de la imagen de la aplicación

Para implementar y ejecutar la aplicación de Liberty en un clúster de AKS, incluya en un contenedor la aplicación como una imagen de Docker mediante las [imágenes de contenedor de Open Liberty](https://github.com/OpenLiberty/ci.docker) o las [imágenes de contenedor de WebSphere Liberty](https://github.com/WASdev/ci.docker).

1. Clone el código de ejemplo de esta guía. El ejemplo se encuentra en [GitHub](https://github.com/Azure-Samples/open-liberty-on-aks).
1. Cambie el directorio a `javaee-app-simple-cluster` del clon local.
1. Ejecute `mvn clean package` para empaquetar la aplicación.
1. Ejecute `mvn liberty:dev` para probar la aplicación. Si el resultado de la operación es satisfactorio, debería ver `The defaultServer server is ready to run a smarter planet.` en la salida del comando. Use `CTRL-C` para detener la aplicación.
1. Recupere los valores de las propiedades `artifactId` y `version` definidas en `pom.xml`.

   ```azurecli-interactive
   artifactId=$(mvn -q -Dexec.executable=echo -Dexec.args='${project.artifactId}' --non-recursive exec:exec)
   version=$(mvn -q -Dexec.executable=echo -Dexec.args='${project.version}' --non-recursive exec:exec)
   ```
1. Ejecute `cd target` para cambiar el directorio a la compilación del ejemplo.
1. Ejecute uno de estos comandos para crear la imagen de la aplicación e insertarla en la instancia de ACR.
   * Compile con la imagen de base de Open Liberty si prefiere usar Open Liberty como Java™ Runtime ligero de código abierto:

     ```azurecli-interactive
     # Build and tag application image. This will cause the ACR instance to pull the necessary Open Liberty base images.
     az acr build -t ${artifactId}:${version} -r $REGISTRY_NAME .
     ```

   * Compile con la imagen de base de WebSphere Liberty si prefiere usar una versión comercial de Open Liberty:

     ```azurecli-interactive
     # Build and tag application image. This will cause the ACR instance to pull the necessary WebSphere Liberty base images.
     az acr build -t ${artifactId}:${version} -r $REGISTRY_NAME --file=Dockerfile-wlp .
     ```

## <a name="deploy-application-on-the-aks-cluster"></a>Implementación de una aplicación en el clúster de AKS

Siga los pasos que se indican a continuación para implementar la aplicación de Liberty en el clúster de AKS.

1. Cree un secreto de extracción para que el clúster de AKS se autentique para extraer la imagen de la instancia de ACR.

   ```azurecli-interactive
   kubectl create secret docker-registry acr-secret \
      --docker-server=${LOGIN_SERVER} \
      --docker-username=${USER_NAME} \
      --docker-password=${PASSWORD}
   ```

1. Compruebe que el directorio de trabajo actual sea `javaee-app-simple-cluster/target` del clon local.
1. Ejecute los comandos siguientes para implementar la aplicación de Liberty con tres réplicas en el clúster de AKS. La salida del comando también se muestra insertada.

   ```azurecli-interactive
   # Create OpenLibertyApplication "javaee-cafe-cluster"
   kubectl apply -f openlibertyapplication.yaml

   openlibertyapplication.openliberty.io/javaee-cafe-cluster created

   # Check if OpenLibertyApplication instance is created
   kubectl get openlibertyapplication ${artifactId}-cluster

   NAME                        IMAGE                                                   EXPOSED   RECONCILED   AGE
   javaee-cafe-cluster         youruniqueacrname.azurecr.io/javaee-cafe:1.0.25         True         59s

   # Check if deployment created by Operator is ready
   kubectl get deployment ${artifactId}-cluster --watch

   NAME                        READY   UP-TO-DATE   AVAILABLE   AGE
   javaee-cafe-cluster         0/3     3            0           20s
   ```

1. Espere hasta ver `3/3` en la columna `READY` y `3` en la columna `AVAILABLE`, use `CTRL-C` para detener el proceso de inspección `kubectl`.

### <a name="test-the-application"></a>Prueba de la aplicación

Cuando se ejecuta la aplicación, un servicio de equilibrador de carga de Kubernetes expone el front-end de la aplicación a Internet. Este proceso puede tardar en completarse.

Para supervisar el progreso, utilice el comando [kubectl get service](https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get) con el argumento `--watch`.

```azurecli-interactive
kubectl get service ${artifactId}-cluster --watch

NAME                        TYPE           CLUSTER-IP     EXTERNAL-IP     PORT(S)          AGE
javaee-cafe-cluster         LoadBalancer   10.0.251.169   52.152.189.57   80:31732/TCP     68s
```

Una vez que la dirección *EXTERNAL-IP* cambia de *pendiente* a una dirección IP pública real, use `CTRL-C` para detener el `kubectl` proceso de inspección.

Abra un explorador web en la dirección IP externa del servicio (`52.152.189.57` en el ejemplo anterior) para ver la página principal de la aplicación. Debería ver el nombre de pod de las réplicas de la aplicación en la parte superior izquierda de la página. Espere unos minutos y actualice la página para ver otro nombre de pod debido al equilibrio de carga proporcionado por el clúster de AKS.

:::image type="content" source="./media/howto-deploy-java-liberty-app/deploy-succeeded.png" alt-text="Aplicación de Liberty de Java implementada correctamente en AKS":::

>[!NOTE]
> - Actualmente, la aplicación no usa HTTPS. Se recomienda [HABILITAR TLS con sus propios certificados](ingress-own-tls.md).

## <a name="clean-up-the-resources"></a>Limpiar los recursos

Para evitar los cargos de Azure, se recomienda limpiar los recursos que no sean necesarios.  Cuando el clúster ya no se necesite, puede usar el comando [az group delete](/cli/azure/group#az_group_delete) para quitar el grupo de recursos, el servicio de contenedor, el registro de contenedor y todos los recursos relacionados.

```azurecli-interactive
az group delete --name $RESOURCE_GROUP_NAME --yes --no-wait
```

## <a name="next-steps"></a>Pasos siguientes

Puede obtener más información en las referencias que se usan en esta guía:

* [Azure Kubernetes Service](https://azure.microsoft.com/free/services/kubernetes-service/)
* [Open Liberty](https://openliberty.io/)
* [Operador de Open Liberty](https://github.com/OpenLiberty/open-liberty-operator)
* [Configuración del servidor Open Liberty](https://openliberty.io/docs/ref/config/)
* [Complemento de Maven para Liberty](https://github.com/OpenLiberty/ci.maven#liberty-maven-plugin)
* [Imágenes de contenedor de Open Liberty](https://github.com/OpenLiberty/ci.docker)
* [Imágenes de contenedor de WebSphere Liberty](https://github.com/WASdev/ci.docker)
