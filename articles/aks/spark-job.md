---
title: Ejecución de un trabajo de Apache Spark con Azure Kubernetes Service (AKS)
description: Use Azure Kubernetes Service (AKS) para crear y ejecutar un trabajo de Apache Spark para el procesamiento de datos a gran escala.
ms.topic: conceptual
ms.date: 10/18/2019
ms.custom: mvc, devx-track-azurecli
ms.openlocfilehash: ffb204d02e27498c7cf82a9bc4d0a591c7e13964
ms.sourcegitcommit: 61e7a030463debf6ea614c7ad32f7f0a680f902d
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/28/2021
ms.locfileid: "129093770"
---
# <a name="running-apache-spark-jobs-on-aks"></a>Ejecución de trabajos de Apache Spark en AKS

[Apache Spark][apache-spark] es un motor rápido para el procesamiento de datos a gran escala. Desde la [versión de Spark 2.3.0][spark-kubernetes-earliest-version], Apache Spark admite la integración nativa con clústeres de Kubernetes. Azure Kubernetes Service (AKS) es un entorno de Kubernetes administrado que se ejecuta en Azure. Este documento detalla la preparación y ejecución de trabajos de Apache Spark en un clúster de Azure Kubernetes Service (AKS).

## <a name="prerequisites"></a>Requisitos previos

Para completar los pasos de este artículo, necesita lo siguiente.

* Conocimientos básicos de Kubernetes y [Apache Spark][spark-quickstart].
* Una cuenta de [Docker Hub][docker-hub] o un registro de [Azure Container Registry][acr-create].
* La CLI de Azure [instalada][azure-cli] en el sistema de desarrollo.
* [JDK 8][java-install] instalado en el sistema.
* [Apache Maven][maven-install] instalado en el sistema.
* SBT ([Scala Build Tool][sbt-install]) instalado en el sistema.
* Herramientas de línea de comandos GIT instaladas en el sistema.

## <a name="create-an-aks-cluster"></a>Creación de un clúster de AKS

Spark se usa para el procesamiento de datos a gran escala y requiere que los nodos Kubernetes tengan el tamaño adecuado para cumplir los requisitos de los recursos de Spark. Se recomienda un tamaño mínimo de `Standard_D3_v2` para los nodos de Azure Kubernetes Service (AKS).

Si necesita un clúster de AKS que cumpla esta recomendación mínima, ejecute los siguientes comandos.

Cree un grupo de recursos para el clúster.

```azurecli
az group create --name mySparkCluster --location eastus
```

Cree una entidad de servicio para el clúster. Una vez creada, necesitará el appId y la contraseña de la entidad de servicio para el siguiente comando.

```azurecli
az ad sp create-for-rbac --name SparkSP
```

Cree el clúster de AKS con nodos de tamaño `Standard_D3_v2` y con los valores de appId y contraseña pasados como parámetros service-principal y client-secret.

```azurecli
az aks create --resource-group mySparkCluster --name mySparkCluster --node-vm-size Standard_D3_v2 --generate-ssh-keys --service-principal <APPID> --client-secret <PASSWORD>
```

Conéctese al clúster de AKS.

```azurecli
az aks get-credentials --resource-group mySparkCluster --name mySparkCluster
```

Si usa Azure Container Registry (ACR) para almacenar las imágenes del contenedor, configure la autenticación entre AKS y ACR. Consulte la [documentación de autenticación de ACR][acr-aks] para realizar estos pasos.

## <a name="build-the-spark-source"></a>Compilación del código fuente de Spark

Antes de ejecutar trabajos de Spark en un clúster de AKS, es necesario compilar el código fuente de Spark y empaquetarlo en una imagen de contenedor. El código fuente de Spark incluye scripts que puede utilizar para completar este proceso.

Clone el repositorio del proyecto de Spark en el sistema de desarrollo.

```bash
git clone -b branch-2.4 https://github.com/apache/spark
```

Cambie al directorio del repositorio clonado y guarde la ruta de acceso al código fuente de Spark en una variable.

```bash
cd spark
sparkdir=$(pwd)
```

Si tiene instaladas varias versiones del JDK, establezca `JAVA_HOME` para utilizar la versión 8 en la sesión actual.

```bash
export JAVA_HOME=`/usr/libexec/java_home -d 64 -v "1.8*"`
```

Ejecute el siguiente comando para compilar el código fuente de Spark con compatibilidad con Kubernetes.

```bash
./build/mvn -Pkubernetes -DskipTests clean package
```

El siguiente comando crea la imagen del contenedor de Spark y la inserta en un registro de imagen de contenedor. Reemplace `registry.example.com` con el nombre de su registro de contenedor y `v1` con la etiqueta que prefiera utilizar. Si usa Docker Hub, este valor es el nombre del registro. Si utiliza Azure Container Registry (ACR), este valor es el nombre del servidor de inicio de sesión de ACR.

```bash
REGISTRY_NAME=registry.example.com
REGISTRY_TAG=v1
```

```bash
./bin/docker-image-tool.sh -r $REGISTRY_NAME -t $REGISTRY_TAG build
```

Inserte la imagen del contenedor en el registro de imagen de contenedor.

```bash
./bin/docker-image-tool.sh -r $REGISTRY_NAME -t $REGISTRY_TAG push
```

## <a name="prepare-a-spark-job"></a>Preparación de un trabajo de Spark

A continuación, prepare un trabajo de Spark. Se usa un archivo jar para contener el trabajo de Spark y es necesario para ejecutar el comando `spark-submit`. El archivo jar puede hacerse accesible a través de una dirección URL pública o preempaquetado dentro de una imagen de contenedor. En este ejemplo, se crea un archivo jar de ejemplo para calcular el valor de Pi. Este archivo jar, a continuación, se carga en Azure Storage. Si tiene un archivo jar existente, no dude en sustituirlo

Cree un directorio donde desea crear el proyecto para un trabajo de Spark.

```bash
mkdir myprojects
cd myprojects
```

Cree un nuevo proyecto de Scala desde una plantilla.

```bash
sbt new sbt/scala-seed.g8
```

Cuando se le solicite, escriba `SparkPi` como nombre del proyecto.

```bash
name [Scala Seed Project]: SparkPi
```

Vaya al directorio del proyecto recién creado.

```bash
cd sparkpi
```

Ejecute los comandos siguientes para agregar un complemento de SBT que permite empaquetar el proyecto como un archivo jar.

```bash
touch project/assembly.sbt
echo 'addSbtPlugin("com.eed3si9n" % "sbt-assembly" % "0.14.10")' >> project/assembly.sbt
```

Ejecute estos comandos para copiar el código de ejemplo en el proyecto recién creado y agregar todas las dependencias necesarias.

```bash
EXAMPLESDIR="src/main/scala/org/apache/spark/examples"
mkdir -p $EXAMPLESDIR
cp $sparkdir/examples/$EXAMPLESDIR/SparkPi.scala $EXAMPLESDIR/SparkPi.scala

cat <<EOT >> build.sbt
// https://mvnrepository.com/artifact/org.apache.spark/spark-sql
libraryDependencies += "org.apache.spark" %% "spark-sql" % "2.3.0" % "provided"
EOT

sed -ie 's/scalaVersion.*/scalaVersion := "2.11.11"/' build.sbt
sed -ie 's/name.*/name := "SparkPi",/' build.sbt
```

Para empaquetar el proyecto en un archivo jar, ejecute el siguiente comando.

```bash
sbt assembly
```

Después de empaquetarlo correctamente, debería ver una salida similar a la siguiente.

```bash
[info] Packaging /Users/me/myprojects/sparkpi/target/scala-2.11/SparkPi-assembly-0.1.0-SNAPSHOT.jar ...
[info] Done packaging.
[success] Total time: 10 s, completed Mar 6, 2018 11:07:54 AM
```

## <a name="copy-job-to-storage"></a>Copia del trabajo a Azure Storage

Cree una cuenta de Azure Storage y un contenedor para almacenar el archivo jar.

```azurecli
RESOURCE_GROUP=sparkdemo
STORAGE_ACCT=sparkdemo$RANDOM
az group create --name $RESOURCE_GROUP --location eastus
az storage account create --resource-group $RESOURCE_GROUP --name $STORAGE_ACCT --sku Standard_LRS
export AZURE_STORAGE_CONNECTION_STRING=`az storage account show-connection-string --resource-group $RESOURCE_GROUP --name $STORAGE_ACCT -o tsv`
```

Cargue el archivo jar en la cuenta de Azure Storage con los siguientes comandos.

```azurecli
CONTAINER_NAME=jars
BLOB_NAME=SparkPi-assembly-0.1.0-SNAPSHOT.jar
FILE_TO_UPLOAD=target/scala-2.11/SparkPi-assembly-0.1.0-SNAPSHOT.jar

echo "Creating the container..."
az storage container create --name $CONTAINER_NAME
az storage container set-permission --name $CONTAINER_NAME --public-access blob

echo "Uploading the file..."
az storage blob upload --container-name $CONTAINER_NAME --file $FILE_TO_UPLOAD --name $BLOB_NAME

jarUrl=$(az storage blob url --container-name $CONTAINER_NAME --name $BLOB_NAME | tr -d '"')
```

La variable `jarUrl` ahora contiene la ruta de acceso pública al archivo jar.

## <a name="submit-a-spark-job"></a>Envío de un trabajo de Spark

Inicie kube-proxy en otra línea de comandos con el código siguiente.

```bash
kubectl proxy
```

Vuelva hasta la raíz del repositorio de Spark.

```bash
cd $sparkdir
```

Cree una cuenta de servicio que tenga permisos suficientes para ejecutar un trabajo.

```bash
kubectl create serviceaccount spark
kubectl create clusterrolebinding spark-role --clusterrole=edit --serviceaccount=default:spark --namespace=default
```

Envíe el trabajo mediante `spark-submit`.

```bash
./bin/spark-submit \
  --master k8s://http://127.0.0.1:8001 \
  --deploy-mode cluster \
  --name spark-pi \
  --class org.apache.spark.examples.SparkPi \
  --conf spark.executor.instances=3 \
  --conf spark.kubernetes.authenticate.driver.serviceAccountName=spark \
  --conf spark.kubernetes.container.image=$REGISTRY_NAME/spark:$REGISTRY_TAG \
  $jarUrl
```

Esta operación inicia el trabajo de Spark, que transmite el estado del trabajo a la sesión de shell. Mientras se ejecuta el trabajo, puede ver el pod del controlador de Spark y los pod del ejecutor mediante el comando kubectl get pods. Abra una segunda sesión de terminal para ejecutar estos comandos.

```console
kubectl get pods
```

```output
NAME                                               READY     STATUS     RESTARTS   AGE
spark-pi-2232778d0f663768ab27edc35cb73040-driver   1/1       Running    0          16s
spark-pi-2232778d0f663768ab27edc35cb73040-exec-1   0/1       Init:0/1   0          4s
spark-pi-2232778d0f663768ab27edc35cb73040-exec-2   0/1       Init:0/1   0          4s
spark-pi-2232778d0f663768ab27edc35cb73040-exec-3   0/1       Init:0/1   0          4s
```

Mientras se ejecuta el trabajo, también puede acceder a la interfaz de usuario de Spark. En la segunda sesión de terminal, use el comando `kubectl port-forward` para proporcionar acceso a la interfaz de usuario de Spark.

```bash
kubectl port-forward spark-pi-2232778d0f663768ab27edc35cb73040-driver 4040:4040
```

Para acceder a la interfaz de usuario de Spark, abra la dirección `127.0.0.1:4040` en un explorador.

![Interfaz de usuario de Spark](media/aks-spark-job/spark-ui.png)

## <a name="get-job-results-and-logs"></a>Obtención de los resultados y registros del trabajo

Cuando haya finalizado el trabajo, el pod del controlador estará en un estado "Completado". Puede obtener el nombre del pod con el siguiente comando.

```bash
kubectl get pods --show-all
```

Salida:

```output
NAME                                               READY     STATUS      RESTARTS   AGE
spark-pi-2232778d0f663768ab27edc35cb73040-driver   0/1       Completed   0          1m
```

Use el comando `kubectl logs` para obtener los registros del pod del controlador de Spark. Sustituya el nombre del pod por el nombre del pod del controlador.

```bash
kubectl logs spark-pi-2232778d0f663768ab27edc35cb73040-driver
```

En estos registros, puede ver el resultado del trabajo de Spark, que es el valor de Pi.

```output
Pi is roughly 3.152155760778804
```

## <a name="package-jar-with-container-image"></a>Paquete jar con la imagen de contenedor

En el ejemplo anterior, el archivo jar de Spark se cargó en Azure Storage. Otra opción consiste en empaquetar el archivo jar en imágenes de Docker personalizadas.

Para ello, busque el archivo `dockerfile` de la imagen de Spark ubicado en el directorio `$sparkdir/resource-managers/kubernetes/docker/src/main/dockerfiles/spark/`. Agregue una instrucción `ADD` al trabajo de Spark `jar` en algún lugar entre las declaraciones `WORKDIR` y `ENTRYPOINT`.

Actualice la ruta de acceso del archivo jar con la ubicación del archivo `SparkPi-assembly-0.1.0-SNAPSHOT.jar` en el sistema de desarrollo. También puede utilizar su propio archivo jar personalizado.

```bash
WORKDIR /opt/spark/work-dir

ADD /path/to/SparkPi-assembly-0.1.0-SNAPSHOT.jar SparkPi-assembly-0.1.0-SNAPSHOT.jar

ENTRYPOINT [ "/opt/entrypoint.sh" ]
```

Compile e inserte la imagen con los scripts de Spark incluidos.

```bash
./bin/docker-image-tool.sh -r <your container repository name> -t <tag> build
./bin/docker-image-tool.sh -r <your container repository name> -t <tag> push
```

Cuando se ejecuta el trabajo, en lugar de indicar una dirección URL del archivo jar remoto, puede usar el esquema `local://` con la ruta de acceso al archivo jar en la imagen de Docker.

```bash
./bin/spark-submit \
    --master k8s://https://<k8s-apiserver-host>:<k8s-apiserver-port> \
    --deploy-mode cluster \
    --name spark-pi \
    --class org.apache.spark.examples.SparkPi \
    --conf spark.executor.instances=3 \
    --conf spark.kubernetes.authenticate.driver.serviceAccountName=spark \
    --conf spark.kubernetes.container.image=<spark-image> \
    local:///opt/spark/work-dir/<your-jar-name>.jar
```

> [!WARNING]
> De la [documentación de Spark][spark-docs]: "Actualmente, el programador de Kubernetes es experimental. En versiones futuras, puede haber cambios de comportamiento en la configuración, las imágenes de contenedor y los puntos de entrada."

## <a name="next-steps"></a>Pasos siguientes

Consulte la documentación de Spark para más detalles.

> [!div class="nextstepaction"]
> [Documentación de Spark][spark-docs]

<!-- LINKS - external -->
[apache-spark]: https://spark.apache.org/
[docker-hub]: https://docs.docker.com/docker-hub/
[java-install]: /azure/developer/java/fundamentals/java-support-on-azure
[maven-install]: https://maven.apache.org/install.html
[sbt-install]: https://www.scala-sbt.org/1.0/docs/Setup.html
[spark-docs]: https://spark.apache.org/docs/latest/running-on-kubernetes.html
[spark-kubernetes-earliest-version]: https://spark.apache.org/releases/spark-release-2-3-0.html
[spark-quickstart]: https://spark.apache.org/docs/latest/quick-start.html


<!-- LINKS - internal -->
[acr-aks]: cluster-container-registry-integration.md
[acr-create]: ../container-registry/container-registry-get-started-azure-cli.md
[aks-quickstart]: ./index.yml
[azure-cli]: /cli/azure/
[storage-account]: ../storage/blobs/storage-quickstart-blobs-cli.md
