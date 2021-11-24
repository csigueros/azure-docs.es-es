---
title: Artefactos de cadena de suministro de inserción y extracción
description: Artefactos de cadena de suministro de inserción y extracción, mediante un registro de contenedor privado en Azure
author: SteveLasker
manager: gwallace
ms.topic: article
ms.date: 11/11/2021
ms.author: stevelas
ms.custom: references_regions
ms.openlocfilehash: 4a8d3a4b73993a386d2cd49e2daa784a8bb7aced
ms.sourcegitcommit: 362359c2a00a6827353395416aae9db492005613
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/15/2021
ms.locfileid: "132495302"
---
# <a name="push-and-pull-supply-chain-artifacts-using-a-private-container-registry-in-azure-preview"></a>Artefactos de cadena de suministro de inserción y extracción, mediante un registro de contenedor privado en Azure (versión preliminar)

Use un registro de contenedor de Azure para almacenar y administrar un gráfico de artefactos, incluidas firmas, lista de materiales de software (SBoM), resultados de análisis de seguridad u otros tipos. 

![Grafo de artefactos, incluida una imagen de contenedor, una firma y una lista de materiales de software firmado](./media/container-registry-artifacts/oras-artifact-graph.svg)

Para demostrar esta funcionalidad, en este artículo se muestra cómo usar la herramienta [Registro de OCI como almacenamiento (ORAS)](https://oras.land) para insertar y extraer un grafo de artefactos en un registro de contenedor de Azure.

La compatibilidad con ORAS Artifacts es una característica en vista previa y está sujeta a [limitaciones](#preview-limitations). Requiere [redundancia de zona](zone-redundancy.md), que está disponible en el nivel de servicio Premium. Para obtener información sobre los límites y los niveles de servicio de los registros, vea [Niveles de servicio de Azure Container Registry](container-registry-skus.md).

## <a name="prerequisites"></a>Requisitos previos

* **CLI de ORAS**: la CLI de ORAS permite insertar, detectar y extraer artefactos en un registro ORAS Artifacts habilitado.
* **CLI de Azure**: para crear una identidad, enumerar y eliminar repositorios, necesita una instalación local de la CLI de Azure. Se recomienda la versión 2.29.1 o posterior. Ejecute `az --version ` para encontrar la versión. Si necesita instalarla o actualizarla, vea [Instalación de la CLI de Azure](/cli/azure/install-azure-cli).
* **Docker (opcional)** : para completar el tutorial, se hace referencia a una imagen de contenedor. Puede usar Docker instalado localmente para compilar e insertar una imagen de contenedor, o bien hacer referencia a una imagen de contenedor existente. Docker proporciona paquetes que permiten configurar Docker fácilmente en cualquier sistema [macOS][docker-mac], [Windows][docker-windows] o [Linux][docker-linux].

## <a name="preview-limitations"></a>Limitaciones de vista previa

La compatibilidad ORAS Artifacts se limita a la región Centro-sur de EE. UU., y compatibilidad con la zona de disponibilidad.

* Los registros con replicación geográfica no replicarán artefactos a los que se hace referencia en otras regiones. Como las regiones adicionales son compatibles con ORAS Artifacts, se replicarán los artefactos a los que se hace referencia.

## <a name="oras-installation"></a>Instalación de ORAS

Descargue e instale una versión preliminar de ORAS para el sistema operativo. Consulte [Instrucciones de instalación de ORAS][oras-install-docs] para obtener información sobre cómo extraer e instalar el archivo para el sistema operativo, haciendo referencia a una compilación de versión preliminar de Alpha.1 desde el [repositorio de GitHub ORAS][oras-preview-install]

## <a name="configure-a-private-registry"></a>Configuración de un registro privado

Configure variables de entorno para copiar y pegar comandos fácilmente en el shell. Los comandos se pueden ejecutar en [Azure Cloud Shell](https://shell.azure.com/)

```console
ACR_NAME=myregistry
REGISTRY=$ACR_NAME.azurecr.io
REPO=net-monitor
TAG=v1
IMAGE=$REGISTRY/${REPO}:$TAG
```

### <a name="create-a-resource-group"></a>Crear un grupo de recursos

Si es necesario, ejecute el comando [az group create](/cli/azure/group#az_group_create) para crear un grupo de recursos para el registro.

```azurecli
az group create --name $ACR_NAME --location southcentralus
```
### <a name="create-oras-artifact-enabled-registry"></a>Creación de un registro habilitado para ORAS Artifacts

La compatibilidad con la versión preliminar de ORAS Artifacts requiere redundancia de zona, que a su vez, requiere un nivel de servicio Premium en la región Centro-sur de EE. UU. Ejecute el comando [az acr create](/cli/azure/acr#az_acr_create) para crear un registro habilitado de ORAS Artifacts. Consulte la ayuda del comando `az acr create` para ver más opciones de registro.

```azurecli
az acr create \
  --resource-group $ACR_NAME \
  --name $ACR_NAME \
  --zone-redundancy enabled \
  --sku Premium \
  --output jsonc
```

En la salida del comando, observe la propiedad `zoneRedundancy` del registro. Cuando se habilita, el registro tiene redundancia de zona y ORAS Artifact habilitado:

```JSON
{
  [...]
  "zoneRedundancy": "Enabled",
}
```

### <a name="sign-in-with-azure-cli"></a>Inicio de sesión con la CLI de Azure

[Inicie sesión](/cli/azure/authenticate-azure-cli) en la CLI de Azure con su identidad para insertar y extraer artefactos del registro de contenedor.

A continuación, use el comando [az acr login](/cli/azure/acr#az_acr_login) de la CLI de Azure para acceder al registro.

```azurecli
az login
az acr login --name $ACR_NAME
```

> [!NOTE]
> `az acr login` usa el cliente de Docker para establecer un token de Azure Active Directory en el archivo `docker.config`. El cliente de Docker debe estar instalado y en ejecución para completar el flujo de autenticación individual.

## <a name="sign-in-with-oras"></a>Inicio de sesión con ORAS

En esta sección se muestran las opciones para iniciar sesión en el registro. Elija el método adecuado para su entorno.

Ejecute `oras login` para autenticarse con el registro. Puede pasar las [credenciales de registro](container-registry-authentication.md) adecuadas para el escenario, como las credenciales de la entidad de servicio, una identidad de usuario o un token con ámbito de repositorio (versión preliminar).

- Autentíquese con la [identidad individual de Azure AD](container-registry-authentication.md?tabs=azure-cli#individual-login-with-azure-ad) para usar un token de AD.

  ```bash
  USER_NAME="00000000-0000-0000-0000-000000000000"
  PASSWORD=$(az acr login --name $ACR_NAME --expose-token --output tsv --query accessToken)
  ```

- Autentíquese con un [token con ámbito de repositorio](container-registry-repository-scoped-permissions.md) (versión preliminar) para usar tokens no basados en AD.

  ```bash
  USER_NAME="oras-token"
  PASSWORD=$(az acr token create -n $USER_NAME \
                    -r $ACR_NAME \
                    --repository $REPO content/write \
                    --only-show-errors \
                    --query "credentials.passwords[0].value" -o tsv)
  ```

- Autentíquese con una entidad de servicio de Azure Active Directory [con permisos de extracción e inserción](container-registry-auth-service-principal.md#create-a-service-principal) (rol AcrPush) para el registro.

  ```bash
  SERVICE_PRINCIPAL_NAME="oras-sp"
  ACR_REGISTRY_ID=$(az acr show --name $ACR_NAME --query id --output tsv)
  PASSWORD=$(az ad sp create-for-rbac --name $SERVICE_PRINCIPAL_NAME \
            --scopes $(az acr show --name $ACR_NAME --query id --output tsv) \
             --role acrpush \
            --query "password" --output tsv)
  USER_NAME=$(az ad sp list --display-name $SERVICE_PRINCIPAL_NAME --query "[].appId" --output tsv)
  ```

### <a name="sign-in-with-oras"></a>Inicio de sesión con ORAS

Proporcione las credenciales a `oras login`.

  ```bash
  oras login $REGISTRY \
    --username $USER_NAME \
    --password $PASSWORD
  ```

Para leer la contraseña desde Stdin, use `--password-stdin`.

## <a name="push-a-container-image"></a>Inserción de una imagen de contenedor

En este ejemplo se asocia un grafo de artefactos a una imagen de contenedor. Compile e inserte una imagen de contenedor o haga referencia a una imagen existente en el registro privado.

```bash
docker build -t $IMAGE https://github.com/wabbit-networks/net-monitor.git#main
docker push $IMAGE
```

## <a name="create-a-sample-signature-to-the-container-image"></a>Creación de una firma de ejemplo en la imagen de contenedor

```bash
echo '{"artifact": "'${IMAGE}'", "signature": "pat hancock"}' > signature.json
```

### <a name="push-a-signature-to-the-registry-as-a-reference-to-the-container-image"></a>Inserción de una firma en el registro, como referencia a la imagen de contenedor

El comando ORAS inserta la firma en un repositorio, haciendo referencia a otro artefacto a través del parámetro `subject`. El `--artifact-type` proporciona artefactos de diferenciación, de forma similar a las extensiones de archivo que habilitan diferentes tipos de archivo. Se pueden insertar uno o varios archivos especificando `file:mediaType`

```bash
oras push $REGISTRY/$REPO \
    --artifact-type 'signature/example' \
    --subject $IMAGE \
    ./signature.json:application/json
```

Para más información sobre inserción con ORAS, consulte [Documentación ORAS][oras-push-docs].

## <a name="push-a-multi-file-artifact-as-a-reference"></a>Inserción de un artefacto de varios archivos como referencia

Creación de documentación en torno a un artefacto

```bash
echo 'Readme Content' > readme.md
echo 'Detailed Content' > readme-details.md
```

Inserción del artefacto de varios archivos como referencia

```bash
oras push $REGISTRY/$REPO \
    --artifact-type 'readme/example' \
    --subject $IMAGE \
    ./readme.md:application/markdown \
    ./readme-details.md:application/markdown
```

## <a name="discovering-artifact-references"></a>Descubrimiento de referencias de artefacto

La especificación de ORAS Artifacts define una [API de otigen de referencia][oras-artifacts-referrers] para detectar referencias a un artefacto `subject`. El comando `oras discover` puede mostrar la lista de referencias a la imagen de contenedor.

Con `oras discover`, consulte el grafo de artefactos ahora almacenados en el registro

```bash
oras discover -o tree $IMAGE
```

La salida muestra el principio de un grafo de artefactos, donde la firma y los documentos se ven como elementos secundarios de la imagen de contenedor

```output
myregistry.azurecr.io/net-monitor:v1
├── signature/example
│   └── sha256:555ea91f39e7fb30c06f3b7aa483663f067f2950dcb...
└── readme/example
    └── sha256:1a118663d1085e229ff1b2d4d89b5f6d67911f22e55...
```

## <a name="creating-a-deep-graphs-of-artifacts"></a>Creación de grafos profundos de artefactos

La especificación de ORAS Artifacts permite grafos profundos, lo que permite la lista de materiales de software firmados (SBoM) y otros tipos de artefactos.

### <a name="create-a-sample-sbom"></a>Crear una SBoM de muestra

```bash
echo '{"version": "0.0.0.0", "artifact": "'${IMAGE}'", "contents": "good"}' > sbom.json
```

### <a name="push-a-sample-sbom-to-the-registry"></a>Inserción de una SBoM de muestra en el registro

```bash
oras push $REGISTRY/$REPO \
  --artifact-type 'sbom/example' \
  --subject $IMAGE \
  ./sbom.json:application/json
```

### <a name="sign-the-sbom"></a>Firmar el SBoM

Los artefactos que se insertan como referencias, normalmente no tienen etiquetas, ya que se consideran parte del artefacto del sujeto. Para insertar una firma en un artefacto que es un elemento secundario de otro artefacto, use el `oras discover` con el filtrado `--artifact-type` para buscar la síntesis del mensaje.

```bash
SBOM_DIGEST=$(oras discover -o json \
                --artifact-type sbom/example \
                $IMAGE | jq -r ".references[0].digest")
```

Creación de una firma de un SBoM

```bash
echo '{"artifact": "'$REGISTRY/${REPO}@$SBOM_DIGEST'", "signature": "pat hancock"}' > sbom-signature.json
```

### <a name="push-the-sbom-signature"></a>Inserción de la firma de SBoM

```bash
oras push $REGISTRY/$REPO \
  --artifact-type 'signature/example' \
  --subject $REGISTRY/$REPO@$SBOM_DIGEST \
  ./sbom-signature.json:application/json
```

### <a name="view-the-graph"></a>Consulte el grafo

```bash
oras discover -o tree $IMAGE
```

Se genera el siguiente código resultado:

```output
myregistry.azurecr.io/net-monitor:v1
├── signature/example
│   └── sha256:555ea91f39e7fb30c06f3b7aa483663f067f2950dcb...
├── readme/example
│   └── sha256:1a118663d1085e229ff1b2d4d89b5f6d67911f22e55...
└── sbom/example
    └── sha256:4280eef9adb632b42cf200e7cd5a822a456a558e4f3142da6b...
        └── signature/example
            └── sha256:a31ab875d37eee1cca68dbb14b2009979d05594d44a075bdd7...
```

## <a name="pull-a-referenced-artifact"></a>Extracción de un artefacto al que se hace referencia

Para extraer un tipo al que se hace referencia, la síntesis del mensaje de referencia se detecta con el comando `oras discover`

```bash
DOC_DIGEST=$(oras discover -o json \
              --artifact-type 'readme/example' \
              $IMAGE | jq -r ".references[0].digest")
```

### <a name="create-a-clean-directory-for-downloading"></a>Creación de un directorio limpio para la descarga

```bash
mkdir ./download
```

### <a name="pull-the-docs-into-the-download-directory"></a>Extracción de los documentos en el directorio de descarga
```bash
oras pull -a -o ./download $REGISTRY/$REPO@$DOC_DIGEST
```
### <a name="view-the-docs"></a>Visualización de los documentos

```bash
ls ./download
```

## <a name="view-the-repository-and-tag-listing"></a>Visualización del repositorio y la lista de etiquetas

ORAS Artifacts permite insertar, detectar, extraer y copiar grafos de artefactos sin tener que asignar etiquetas. Esto permite que una lista de etiquetas se centre en los artefactos en los que los usuarios piensan, en lugar de las firmas y las SBoMs asociadas a las imágenes de contenedor, gráficos de Helm y otros artefactos.

### <a name="view-a-list-of-tags"></a>Consulte una lista de etiquetas

```azurecli
az acr repository show-tags \
  -n $ACR_NAME \
  --repository $REPO \
  -o jsonc
```

### <a name="view-a-list-of-manifests"></a>Visualización de una lista de manifiestos

Un repositorio puede tener una lista de manifiestos etiquetados y sin etiquetar

```azurecli
az acr repository show-manifests \
  -n $ACR_NAME \
  --repository $REPO \
  --detail -o jsonc
```

Tenga en cuenta que los manifiestos de imagen de contenedor tienen `"tags":`

```json
{
  "architecture": "amd64",
  "changeableAttributes": {
    "deleteEnabled": true,
    "listEnabled": true,
    "readEnabled": true,
    "writeEnabled": true
  },
  "configMediaType": "application/vnd.docker.container.image.v1+json",
  "createdTime": "2021-11-12T00:18:54.5123449Z",
  "digest": "sha256:a0fc570a245b09ed752c42d600ee3bb5b4f77bbd70d8898780b7ab4...",
  "imageSize": 2814446,
  "lastUpdateTime": "2021-11-12T00:18:54.5123449Z",
  "mediaType": "application/vnd.docker.distribution.manifest.v2+json",
  "os": "linux",
  "tags": [
    "v1"
  ]
}
```

La firma no está etiquetada, pero se realiza un seguimiento como referencia `oras.artifact.manifest` a la imagen de contenedor

```json
{
  "changeableAttributes": {
    "deleteEnabled": true,
    "listEnabled": true,
    "readEnabled": true,
    "writeEnabled": true
  },
  "createdTime": "2021-11-12T00:19:10.987156Z",
  "digest": "sha256:555ea91f39e7fb30c06f3b7aa483663f067f2950dcbcc0b0d...",
  "imageSize": 85,
  "lastUpdateTime": "2021-11-12T00:19:10.987156Z",
  "mediaType": "application/vnd.cncf.oras.artifact.manifest.v1+json"
}
```
## <a name="delete-all-artifacts-in-the-graph"></a>Eliminación de todos los artefactos del grafo

La compatibilidad con la especificación ORAS Artifacts permite eliminar el grafo de artefactos asociados al artefacto raíz. Use el comando [az acr repository delete][az-acr-repository-delete] para eliminar la firma, la SBoM y la firma de SBoM.

```bash
az acr repository delete \
  -n $ACR_NAME \
  -t ${REPO}:$TAG -y
```

### <a name="view-the-remaining-manifests"></a>Visualización de los manifiestos restantes

```azurecli
az acr repository show-manifests \
  -n $ACR_NAME \
  --repository $REPO \
  --detail -o jsonc
```

## <a name="next-steps"></a>Pasos siguientes

* Obtenga más información sobre [la CLI de ORAS](https://oras.land)
* Obtenga más información sobre [ORAS Artifacts][oras-artifacts] cómo insertar, detectar, extraer y copiar un grafo de artefactos de cadena de suministro

<!-- LINKS - external -->
[docker-linux]:         https://docs.docker.com/engine/installation/#supported-platforms
[docker-mac]:           https://docs.docker.com/docker-for-mac/
[docker-windows]:       https://docs.docker.com/docker-for-windows/
[oras-install-docs]:    https://oras.land/cli/
[oras-preview-install]: https://github.com/oras-project/oras/releases/tag/v0.2.1-alpha.1
[oras-push-docs]:       https://oras.land/cli/1_pushing/
[oras-artifacts]:       https://github.com/oras-project/artifacts-spec/
<!-- LINKS - internal -->
[az-acr-repository-show]: /cli/azure/acr/repository?#az_acr_repository_show
[az-acr-repository-delete]: /cli/azure/acr/repository#az_acr_repository_delete
