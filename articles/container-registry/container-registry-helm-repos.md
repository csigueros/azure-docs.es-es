---
title: Almacenamiento de gráficos de Helm
description: Información sobre cómo almacenar gráficos de Helm para las aplicaciones de Kubernetes mediante repositorios en Azure Container Registry
ms.topic: article
ms.date: 07/19/2021
ms.openlocfilehash: cdc4b0c6fb5aabdb96597cfbbe151598b16a2cc0
ms.sourcegitcommit: 7d63ce88bfe8188b1ae70c3d006a29068d066287
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 07/22/2021
ms.locfileid: "114438914"
---
# <a name="push-and-pull-helm-charts-to-an-azure-container-registry"></a>Inserción y extracción de gráficos de Helm en Azure Container Registry

Para administrar e implementar rápidamente aplicaciones de Kubernetes, puede usar el [administrador de paquetes de código abierto de Helm][helm]. Con Helm, los paquetes de aplicación se definen como [gráficos](https://helm.sh/docs/topics/charts/) que se recopilan y almacenan en un [repositorio de gráficos de Helm](https://helm.sh/docs/topics/chart_repository/).

En este artículo se muestra cómo hospedar repositorios gráficos de Helm en un registro de contenedor de Azure mediante comandos de Helm 3 y el almacenamiento de los gráficos como [artefactos de OCI](container-registry-image-formats.md#oci-artifacts). En la mayoría de los escenarios, creará y cargará sus propios gráficos para las aplicaciones que desarrolla. Para obtener más información sobre cómo crear sus propios gráficos de Helm, consulte la [guía para desarrolladores de plantillas de gráficos][develop-helm-charts] (en inglés). También puede almacenar un gráfico de Helm existente desde otro repositorio de Helm.

## <a name="helm-3-or-helm-2"></a>¿Helm 3 o Helm 2?

Para almacenar, administrar e instalar gráficos de Helm, se usan comandos en la CLI de Helm. Las versiones principales de Helm incluyen Helm 3 y Helm 2. Para obtener más información sobre las diferencias de versión, vea las [preguntas más frecuentes sobre la versión](https://helm.sh/docs/faq/). 

Helm 3 se debe usar para hospedar gráficos de Helm en Azure Container Registry. Con Helm 3:

* Puede almacenar y administrar gráficos de Helm en repositorios de un registro de contenedor de Azure.
* Almacenar gráficos de Helm en el registro como [artefactos de OCI](container-registry-image-formats.md#oci-artifacts). Azure Container Registry proporciona compatibilidad con GA para artefactos OCI, incluidos los gráficos de Helm.
* Autenticarse con el registro mediante el comando `helm registry login` o `az acr login`.
* Usar comandos `helm chart` para insertar, extraer y administrar gráficos de Helm en un registro.
* Use `helm install` para instalar gráficos en un clúster de Kubernetes desde una caché del repositorio local.

### <a name="feature-support"></a>Compatibilidad de características

Azure Container Registry admite características específicas para la administración de gráficos de Helm en función de si usa Helm 3 (actual) o Helm 2 (en desuso).

| Característica | Helm 2 | Helm 3 |
| ---- | ---- | ---- |
| Administración de gráficos con comandos `az acr helm` | :heavy_check_mark: | |
| Almacenamiento de gráficos como artefactos de OCI | | :heavy_check_mark:  |
| Administración de gráficos con comandos `az acr repository` y la hoja **Repositorios** de Azure Portal| | :heavy_check_mark:  |


> [!NOTE]
> A partir de Helm 3, los comandos [az acr helm][az-acr-helm] que se usan con el cliente de Helm 2 están en desuso. Antes de quitar el comando, se proporcionará un aviso de 3 meses como mínimo.

### <a name="chart-version-compatibility"></a>Compatibilidad con versiones de gráficos

Las [versiones de gráficos](https://helm.sh/docs/topics/charts/#the-apiversion-field) de Helm siguientes se pueden almacenar en Azure Container Registry y los clientes de Helm 2 y Helm 3 pueden instalarlas. 

| Versión | Helm 2 | Helm 3 |
| ---- | ---- | ---- |
| apiVersion v1 | :heavy_check_mark: | :heavy_check_mark: |
| apiVersion v2 | | :heavy_check_mark: |

### <a name="migrate-from-helm-2-to-helm-3"></a>Migración de Helm 2 a Helm 3

Si anteriormente almacenó e implementó gráficos con Helm 2 y Azure Container Registry, se recomienda migrar a Helm 3. Vea:

* [Migración de Helm 2 a 3](https://helm.sh/docs/topics/v2_v3_migration/) en la documentación de Helm.
* [Migración del registro para almacenar artefactos de OCI de Helm](#migrate-your-registry-to-store-helm-oci-artifacts), más adelante en este artículo

## <a name="prerequisites"></a>Requisitos previos

Los siguientes recursos son necesarios para el escenario de este artículo:

- **Una instancia de Azure Container Registry** en la suscripción a Azure. Si es necesario, cree un registro mediante [Azure Portal](container-registry-get-started-portal.md) o la [CLI de Azure](container-registry-get-started-azure-cli.md).
- **Versión de cliente de Helm 3.1.0 o posterior**: ejecute `helm version` para buscar la versión actual. Para más información sobre cómo instalar y usar Helm, consulte [Instalación de Helm][helm-install].
- **Un clúster de Kubernetes** donde instalar un gráfico de Helm. Si es necesario, cree [un clúster de Azure Kubernetes Service][aks-quickstart]. 
- **CLI de Azure versión 2.0.71 o posterior**: ejecute `az --version` para buscar la versión. Si necesita instalarla o actualizarla, vea [Instalación de la CLI de Azure][azure-cli-install].

## <a name="enable-oci-support"></a>Habilitar la compatibilidad de OCI

Use el comando `helm version` para comprobar que tiene instalado Helm 3:

```console
helm version
```

Establezca la siguiente variable de entorno para habilitar la compatibilidad de OCI en el cliente de Helm 3. Actualmente, esta compatibilidad es experimental y está sujeta a cambios. 

```console
export HELM_EXPERIMENTAL_OCI=1
```

## <a name="create-a-sample-chart"></a>Crear un gráfico de ejemplo

Cree un gráfico de prueba con los siguientes comandos:

```console
mkdir helmtest

cd helmtest
helm create hello-world
```

Como ejemplo básico, cambie el directorio a la carpeta `templates` y, en primer lugar, elimine el contenido aquí:

```console
cd hello-world/templates
rm -rf *
```

En la carpeta `templates`, cree un archivo llamado `configmap.yaml` mediante la ejecución del comando siguiente:

```console
cat <<EOF > configmap.yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: hello-world-configmap
data:
  myvalue: "Hello World"
EOF
```

Para más información sobre cómo crear y ejecutar este ejemplo, consulte [Introducción](https://helm.sh/docs/chart_template_guide/getting_started/) en los documentos de Helm.

## <a name="save-chart-to-local-registry-cache"></a>Guardar el gráfico en la caché del registro local

Cambie el directorio al subdirectorio `hello-world`. A continuación, ejecute `helm chart save` para guardar una copia del gráfico localmente y crear también un alias con el nombre completo del registro (todo en minúsculas) y el repositorio y la etiqueta de destino. 

En el ejemplo siguiente, el nombre del registro es *mycontainerregistry*, el repositorio de destino es *helm/hello-world* y la etiqueta del gráfico de destino es *0.1.0*. A fin de extraer correctamente las dependencias, el nombre y la etiqueta de la imagen del gráfico de destino deben coincidir con el nombre y la versión en `Chart.yaml`.

```console
cd ..
helm chart save . hello-world:0.1.0
helm chart save . mycontainerregistry.azurecr.io/helm/hello-world:0.1.0
```

Ejecute `helm chart list` para confirmar que ha guardado los gráficos en la caché del registro local. La salida es parecida a esta:

```console
REF                                                      NAME            VERSION DIGEST  SIZE            CREATED
hello-world:0.1.0                                        hello-world      0.1.0   5899db0 3.2 KiB        2 minutes 
mycontainerregistry.azurecr.io/helm/hello-world:0.1.0    hello-world      0.1.0   5899db0 3.2 KiB        2 minutes
```

## <a name="authenticate-with-the-registry"></a>Autenticación con el registro

Ejecute `helm registry login` para autenticarse con el registro. Puede pasar las [credenciales de registro](container-registry-authentication.md) adecuadas para el escenario, como las credenciales de entidad de servicio, o un token con ámbito de repositorio.

Por ejemplo, cree una entidad de servicio de Azure Active Directory [con permisos de extracción e inserción](container-registry-auth-service-principal.md#create-a-service-principal) (rol AcrPush) para el registro. A continuación, proporcione las credenciales de la entidad de servicio para `helm registry login`. En el ejemplo siguiente se proporciona la contraseña mediante una variable de entorno:

```console
echo $spPassword | helm registry login mycontainerregistry.azurecr.io \
  --username <service-principal-id> \
  --password-stdin
```

> [!TIP]
> También puede iniciar sesión en el registro con su [identidad individual de Azure AD](container-registry-authentication.md?tabs=azure-cli#individual-login-with-azure-ad) para insertar y extraer gráficos de Helm.

## <a name="push-chart-to-registry"></a>Inserción del gráfico en el registro

Ejecute el comando `helm chart push` de la CLI de Helm 3 para insertar el gráfico al repositorio de destino completo:

```console
helm chart push mycontainerregistry.azurecr.io/helm/hello-world:0.1.0
```

Después de una inserción correcta, la salida es similar a:

```output
The push refers to repository [mycontainerregistry.azurecr.io/helm/hello-world]
ref:     mycontainerregistry.azurecr.io/helm/hello-world:0.1.0
digest:  5899db028dcf96aeaabdadfa5899db025899db025899db025899db025899db02
size:    3.2 KiB
name:    hello-world
version: 0.1.0
```

## <a name="list-charts-in-the-repository"></a>Lista de gráficos en el repositorio

Al igual que con las imágenes almacenadas en una instancia de Azure Container Registry, puede usar comandos [az acr repository][az-acr-repository] para mostrar los repositorios que hospedan sus gráficos, así como los manifiestos y las etiquetas de estos. 

Por ejemplo, ejecute [az acr repository show][az-acr-repository-show] para ver las propiedades del repositorio que creó en el paso anterior:

```azurecli
az acr repository show \
  --name mycontainerregistry \
  --repository helm/hello-world
```

La salida es parecida a esta:

```output
{
  "changeableAttributes": {
    "deleteEnabled": true,
    "listEnabled": true,
    "readEnabled": true,
    "writeEnabled": true
  },
  "createdTime": "2020-03-20T18:11:37.6701689Z",
  "imageName": "helm/hello-world",
  "lastUpdateTime": "2020-03-20T18:11:37.7637082Z",
  "manifestCount": 1,
  "registry": "mycontainerregistry.azurecr.io",
  "tagCount": 1
}
```

Ejecute el comando [az acr repository show-manifests][az-acr-repository-show-manifests] para ver los detalles del gráfico almacenado en el repositorio. Por ejemplo:

```azurecli
az acr repository show-manifests \
  --name mycontainerregistry \
  --repository helm/hello-world --detail
```

La salida, abreviada en este ejemplo, muestra un valor de `configMediaType` de `application/vnd.cncf.helm.config.v1+json`:

```output
[
  {
    [...]
    "configMediaType": "application/vnd.cncf.helm.config.v1+json",
    "createdTime": "2020-03-20T18:11:37.7167893Z",
    "digest": "sha256:0c03b71c225c3ddff53660258ea16ca7412b53b1f6811bf769d8c85a1f0663ee",
    "imageSize": 3301,
    "lastUpdateTime": "2020-03-20T18:11:37.7167893Z",
    "mediaType": "application/vnd.oci.image.manifest.v1+json",
    "tags": [
      "0.1.0"
    ]
```

## <a name="pull-chart-to-local-cache"></a>Extraer el gráfico en la caché local

Para instalar un gráfico de Helm en Kubernetes, el gráfico debe estar en la memoria caché local. En este ejemplo, ejecute primero `helm chart remove` para quitar el gráfico local existente denominado `mycontainerregistry.azurecr.io/helm/hello-world:0.1.0`:

```console
helm chart remove mycontainerregistry.azurecr.io/helm/hello-world:0.1.0
```

Ejecute `helm chart pull` para descargar el gráfico desde Azure Container Registry en la memoria caché local:

```console
helm chart pull mycontainerregistry.azurecr.io/helm/hello-world:0.1.0
```

## <a name="export-helm-chart"></a>Exportar el gráfico de Helm

Para seguir trabajando con el gráfico, expórtelo a un directorio local mediante `helm chart export`. Por ejemplo, exporte el gráfico que extrajo al directorio `install`:

```console
helm chart export mycontainerregistry.azurecr.io/helm/hello-world:0.1.0 \
  --destination ./install
```

Para ver información del gráfico exportado en el repositorio, ejecute el comando `helm show chart` en el directorio donde lo exportó.

```console
cd install
helm show chart hello-world
```

Helm devuelve información detallada sobre la versión más reciente del gráfico, tal como se muestra en la siguiente salida de ejemplo:

```output
apiVersion: v2
appVersion: 1.16.0
description: A Helm chart for Kubernetes
name: hello-world
type: application
version: 0.1.0    
```

## <a name="install-helm-chart"></a>Instalar el gráfico de Helm

Ejecute `helm install` para instalar el gráfico de Helm que extrajo en la caché local y exportó. Especifique un nombre de versión, como *myhelmtest* o bien pase el parámetro `--generate-name`. Por ejemplo:

```console
helm install myhelmtest ./hello-world
```

La salida después de la instalación correcta del gráfico es similar a:

```console
NAME: myhelmtest
LAST DEPLOYED: Fri Mar 20 14:14:42 2020
NAMESPACE: default
STATUS: deployed
REVISION: 1
TEST SUITE: None
```

Para comprobar la instalación, ejecute el comando `helm get manifest`. 

```console
helm get manifest myhelmtest
```

El comando devuelve los datos de YAML en el archivo de plantilla `configmap.yaml`.

Ejecute `helm uninstall` para desinstalar la versión del gráfico en el clúster:

```console
helm uninstall myhelmtest
```

## <a name="delete-chart-from-the-registry"></a>Eliminación del gráfico del registro

Para eliminar un gráfico del registro de contenedor, use el comando [az acr repository delete][az-acr-repository-delete]. Ejecute el siguiente comando y confirme la operación cuando se le solicite:

```azurecli
az acr repository delete --name mycontainerregistry --image helm/hello-world:0.1.0
```

## <a name="migrate-your-registry-to-store-helm-oci-artifacts"></a>Migración del registro para almacenar artefactos de OCI de Helm

Si previamente configuró el registro de contenedor de Azure como un repositorio de gráficos con Helm 2 y los comandos `az acr helm`, se recomienda [actualizar][helm-install] al cliente de Helm 3. A continuación, siga estos pasos para almacenar los gráficos como artefactos de OCI en el registro. 

> [!IMPORTANT]
> * Una vez que complete la migración desde un repositorio de gráficos con el estilo de Helm 2 (basado en index.yaml) hasta repositorios de artefactos de OCI, use la CLI de Helm y los comandos `az acr repository` para administrar los gráficos. Consulte las secciones anterior de este artículo. 
> * Los repositorios de artefactos de OCI de Helm no se detectan con comandos de Helm como `helm search` y `helm repo list`. Para más información sobre los comandos de Helm que se utilizan para almacenar gráficos como artefactos de OCI, consulte la [documentación sobre Helm](https://helm.sh/docs/topics/registries/).

### <a name="enable-oci-support"></a>Habilitar la compatibilidad de OCI

Asegúrese de utilizar el cliente de Helm 3:

```console
helm version
```

Habilite la compatibilidad de OCI en el cliente de Helm 3. Actualmente, esta compatibilidad es experimental y está sujeta a cambios.

```console
export HELM_EXPERIMENTAL_OCI=1
```

### <a name="list-current-charts"></a>Enumeración de los gráficos actuales

Enumere los gráficos actualmente almacenados en el registro, que aquí se denomina *myregistry*:

```console
helm search repo myregistry
```

En la salida se muestran los gráficos y sus versiones:

```
NAME                            CHART VERSION   APP VERSION     DESCRIPTION                                       
myregistry/ingress-nginx        3.20.1          0.43.0          Ingress controller for Kubernetes...
myregistry/wordpress            9.0.3           5.3.2           Web publishing platform for building...
[...]
```

### <a name="save-charts-as-oci-artifacts"></a>Almacenamiento de gráficos como artefactos de OCI

Para cada gráfico que esté en el repositorio, extraiga el gráfico de manera local y guárdelo como un artefacto de OCI. Ejemplo:

```console 
helm pull myregisry/ingress-nginx --untar
cd ingress-nginx
helm chart save . myregistry.azurecr.io/ingress-nginx:3.20.1
```

### <a name="push-charts-to-registry"></a>Inserción de gráficos en el registro

Inicie sesión en el registro:

```azurecli
az acr login --name myregistry
```

Inserte cada gráfico en el registro:

```console
helm chart push myregistry.azurecr.io/ingress-nginx:3.20.1
```

Después de insertar un gráfico, confirme que esté almacenado en el registro:

```azurecli
az acr repository list --name myregistry
```

Después de insertar todos los gráfico, también puede quitar del registro el repositorio de gráficos con el estilo de Helm 2. Al hacerlo, se reduce el almacenamiento en el registro:

```console
helm repo remove myregistry
```

## <a name="next-steps"></a>Pasos siguientes

* Para obtener más información sobre cómo crear e implementar sus propios gráficos de Helm, consulte cómo [desarrollar gráficos de Helm][develop-helm-charts].
* Más información sobre la instalación de aplicaciones con Helm en [Azure Kubernetes Service (AKS)](../aks/kubernetes-helm.md).
* Los gráficos de helm pueden usarse como parte del proceso de compilación de contenedor. Para más información, consulte [Uso de Azure Container Registry Tasks][acr-tasks].

<!-- LINKS - external -->
[helm]: https://helm.sh/
[helm-install]: https://helm.sh/docs/intro/install/
[develop-helm-charts]: https://helm.sh/docs/chart_template_guide/

<!-- LINKS - internal -->
[azure-cli-install]: /cli/azure/install-azure-cli
[aks-quickstart]: ../aks/kubernetes-walkthrough.md
[acr-bestpractices]: container-registry-best-practices.md
[az-acr-login]: /cli/azure/acr#az_acr_login
[az-acr-helm]: /cli/azure/acr/helm
[az-acr-repository]: /cli/azure/acr/repository
[az-acr-repository-show]: /cli/azure/acr/repository#az_acr_repository_show
[az-acr-repository-delete]: /cli/azure/acr/repository#az_acr_repository_delete
[az-acr-repository-show-manifests]: /cli/azure/acr/repository#az_acr_repository_show_manifests
[acr-tasks]: container-registry-tasks-overview.md
