---
title: Importación de imágenes de contenedor
description: Importe imágenes de contenedor en un registro de contenedor de Azure mediante las API de Azure, sin necesidad de ejecutar comandos de Docker.
ms.topic: article
ms.date: 09/13/2021
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 14d2008599c0740bd36108760e3d4e50054b5f4d
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/24/2021
ms.locfileid: "128582276"
---
# <a name="import-container-images-to-a-container-registry"></a>Importación de imágenes de contenedor en un registro de contenedor

Puede importar (copiar) fácilmente imágenes de contenedor (copia) en un registro de contenedor de Azure, sin usar comandos de Docker. Por ejemplo, importar imágenes de un registro de desarrollo en un registro de producción, o copiar las imágenes base desde un registro público.

Azure Container Registry controla una serie de escenarios comunes para copiar imágenes y otros artefactos de un registro existente:

* Importación de imágenes desde un registro público

* Importación de imágenes o artefactos OCI, incluidos los gráficos Helm 3 de otra instancia de Azure Container Registry, al mismo u otro inquilino o suscripción de Azure

* Importación desde un registro de contenedor privado de Azure

La importación de imágenes en un registro de contenedor de Azure tiene las siguientes ventajas con respecto al uso de comandos de la CLI de Docker:

* Dado que el entorno de cliente no necesita una instalación local de Docker, importe cualquier imagen de contenedor, independientemente del tipo de sistema operativo compatible.

* Al importar imágenes de varias arquitecturas (por ejemplo, las imágenes oficiales de Docker), se copian las imágenes de todas las arquitecturas y plataformas especificadas en la lista de manifiesto.

* El acceso al registro de destino no tiene que usar el punto de conexión público del registro.

## <a name="limitations"></a>Limitaciones

* El número máximo de manifiestos para una imagen importada es 50.
* El tamaño máximo de capa para una imagen importada desde un registro público es de 2 GiB.

### <a name="azure-cli"></a>[CLI de Azure](#tab/azure-cli)

Para importar imágenes de contenedor, este artículo requiere que ejecute la CLI de Azure en Azure Cloud Shell o localmente (se recomienda la versión 2.0.55 o posterior). Ejecute `az --version` para encontrar la versión. Si necesita instalarla o actualizarla, vea [Instalación de la CLI de Azure][azure-cli].

### <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

Para importar imágenes de contenedor, este artículo requiere que ejecute Azure PowerShell en Azure Cloud Shell o localmente (se recomienda la versión 5.9.0 o posterior). Ejecute `Get-InstalledModule -Name Az` para encontrar la versión. Si tiene que instalarla o actualizarla, consulte [Instalación del módulo Azure Az PowerShell][install-the-azure-az-powershell-module].

---

[!INCLUDE [container-registry-geo-replication-include](../../includes/container-registry-geo-replication-include.md)]

> [!IMPORTANT]
> Se han producido cambios en la importación de imágenes entre dos registros de contenedor de Azure a partir de enero de 2021:
> * La importación hacia un registro de contenedor de Azure con restricción de red, o desde este, requiere que el registro restringido [**permita el acceso por parte de servicios de confianza**](allow-access-trusted-services.md) para omitir la red. De forma predeterminada, la opción está habilitada, lo que permite la importación. Si el valor no está habilitado en un registro recién creado con un punto de conexión privado o con reglas de firewall de registro, se producirá un error en la importación.
> * En un registro de contenedor de Azure con restricción de red existente que se use como origen o destino de la importación, la habilitación de esta característica de seguridad de red es opcional pero recomendable.

## <a name="prerequisites"></a>Requisitos previos

### <a name="azure-cli"></a>[CLI de Azure](#tab/azure-cli)

Si aún no tiene un registro de contenedor de Azure, cree un registro. Para obtener instrucciones, consulte [Guía de inicio rápido: Creación de un registro de contenedor privado con la CLI de Azure](container-registry-get-started-azure-cli.md).

### <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

Si aún no tiene un registro de contenedor de Azure, cree un registro. Para obtener instrucciones, vea [Inicio rápido: Creación de un registro de contenedor privado con Azure PowerShell](container-registry-get-started-powershell.md).

---

Para importar una imagen en un registro de contenedor de Azure, la identidad debe tener permisos de escritura en el registro de destino (al menos el rol de colaborador o un rol personalizado que permita realizar la acción importImage). Consulte [Roles y permisos de Azure Container Registry](container-registry-roles.md#custom-roles).

## <a name="import-from-a-public-registry"></a>Importación desde un registro público

### <a name="import-from-docker-hub"></a>Importación desde Docker Hub

### <a name="azure-cli"></a>[CLI de Azure](#tab/azure-cli)

Por ejemplo, use el comando [az acr import][az-acr-import] para importar la imagen `hello-world:latest` de varias arquitecturas desde Docker Hub en un registro denominado *myregistry*. Dado que `hello-world` es una imagen oficial de Docker Hub, esta imagen está en el repositorio `library` predeterminado. Incluya el nombre del repositorio y, opcionalmente, una etiqueta en el valor del parámetro de imagen `--source`. (Puede, opcionalmente, identificar una imagen por su hash de manifiesto en vez de por etiqueta, lo que garantiza una versión determinada de una imagen).

```azurecli
az acr import \
  --name myregistry \
  --source docker.io/library/hello-world:latest \
  --image hello-world:latest
```

Para comprobar que varios manifiestos estén asociados a esta imagen, ejecute el comando `az acr repository show-manifests`:

```azurecli
az acr repository show-manifests \
  --name myregistry \
  --repository hello-world
```

Si tiene una [cuenta de Docker Hub](https://www.docker.com/pricing), se recomienda usar las credenciales al importar una imagen desde Docker Hub. Pase el nombre de usuario y la contraseña de Docker Hub o un [token de acceso personal](https://docs.docker.com/docker-hub/access-tokens/) como parámetros a `az acr import`. En el siguiente ejemplo se importa una imagen pública del repositorio `tensorflow` en Docker Hub mediante las credenciales de este:

```azurecli
az acr import \
  --name myregistry \
  --source docker.io/tensorflow/tensorflow:latest-gpu \
  --image tensorflow:latest-gpu
  --username <Docker Hub user name>
  --password <Docker Hub token>
```

### <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

Por ejemplo, use el comando [Import-AzContainerRegistryImage][import-azcontainerregistryimage] para importar la imagen `hello-world:latest` de varias arquitecturas desde Docker Hub en un registro denominado *myregistry*. Dado que `hello-world` es una imagen oficial de Docker Hub, esta imagen está en el repositorio `library` predeterminado. Incluya el nombre del repositorio y, opcionalmente, una etiqueta en el valor del parámetro `-SourceImage`. (Puede, opcionalmente, identificar una imagen por su hash de manifiesto en vez de por etiqueta, lo que garantiza una versión determinada de una imagen).

```azurepowershell
Import-AzContainerRegistryImage -RegistryName myregistry -ResourceGroupName myResourceGroup -SourceRegistryUri docker.io -SourceImage library/hello-world:latest
```

Para comprobar que varios manifiestos estén asociados a esta imagen, ejecute el cmdlet `Get-AzContainerRegistryManifest`:

```azurepowershell
Get-AzContainerRegistryManifest -RepositoryName library/hello-world -RegistryName myregistry
```

Si tiene una [cuenta de Docker Hub](https://www.docker.com/pricing), se recomienda usar las credenciales al importar una imagen desde Docker Hub. Pase el nombre de usuario y la contraseña de Docker Hub o un [token de acceso personal](https://docs.docker.com/docker-hub/access-tokens/) como parámetros a `Import-AzContainerRegistryImage`. En el siguiente ejemplo se importa una imagen pública del repositorio `tensorflow` en Docker Hub mediante las credenciales de este:

```azurepowershell
Import-AzContainerRegistryImage -RegistryName myregistry -ResourceGroupName myResourceGroup -SourceRegistryUri docker.io -SourceImage tensorflow/tensorflow:latest-gpu -Username <Docker Hub user name> -Password <Docker Hub token>
```

---

### <a name="import-from-microsoft-container-registry"></a>Importación desde el Registro de contenedor de Microsoft

Por ejemplo, importe la imagen de Windows Server Core `ltsc2019` del repositorio de `windows` en Microsoft Container Registry.

### <a name="azure-cli"></a>[CLI de Azure](#tab/azure-cli)

```azurecli
az acr import \
--name myregistry \
--source mcr.microsoft.com/windows/servercore:ltsc2019 \
--image servercore:ltsc2019
```

### <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

```azurepowershell
Import-AzContainerRegistryImage -RegistryName myregistry -ResourceGroupName myResourceGroup -SourceRegistryUri mcr.microsoft.com -SourceImage windows/servercore:ltsc2019
```

---

## <a name="import-from-an-azure-container-registry-in-the-same-ad-tenant"></a>Importación de un registro de contenedor de Azure del mismo inquilino de AD

Puede importar una imagen desde un registro de contenedor de Azure del mismo inquilino de AD con los permisos integrados de Azure Active Directory.

* La identidad debe tener permisos de Azure Active Directory para leer desde el registro de origen (rol de lector) e importar en el registro de destino (rol de colaborador o un [rol personalizado](container-registry-roles.md#custom-roles) que permita realizar la acción importImage).

* El registro puede estar en la misma suscripción de Azure, u otra diferente, en el mismo inquilino de Active Directory.

* El [acceso público](container-registry-access-selected-networks.md#disable-public-network-access) al registro de origen puede estar deshabilitado. Si el acceso público está deshabilitado, use el identificador del recurso para especificar el registro de origen, en lugar del nombre del servidor de inicio de sesión del registro.

* Si el registro de origen o el de destino tienen un punto de conexión privado o se aplican reglas de firewall de registro, asegúrese de que el registro restringido [permita que los servicios de confianza](allow-access-trusted-services.md) accedan a la red.

### <a name="import-from-a-registry-in-the-same-subscription"></a>Importación desde un registro en la misma suscripción

Por ejemplo, importe la imagen `aci-helloworld:latest` desde un registro de origen *mysourceregistry* a *myregistry* en la misma suscripción de Azure.

### <a name="azure-cli"></a>[CLI de Azure](#tab/azure-cli)

```azurecli
az acr import \
  --name myregistry \
  --source mysourceregistry.azurecr.io/aci-helloworld:latest \
  --image aci-helloworld:latest
```

En el ejemplo siguiente se importa la imagen `aci-helloworld:latest` a *myregistry* desde el registro de origen *mysourceregistry*, en el que está deshabilitado el acceso al punto de conexión público del registro. Proporcione el identificador de recurso del registro de origen con el parámetro `--registry`. Tenga en cuenta que el parámetro `--source` especifica solo el repositorio de origen y el nombre de la imagen, no el nombre del servidor de inicio de sesión del registro.

```azurecli
az acr import \
  --name myregistry \
  --source aci-helloworld:latest \
  --image aci-helloworld:latest \
  --registry /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/sourceResourceGroup/providers/Microsoft.ContainerRegistry/registries/mysourceregistry
```

En el ejemplo siguiente se importa una imagen mediante el hash del manifiesto (hash de SHA-256, representado como `sha256:...`) en lugar de mediante etiqueta:

```azurecli
az acr import \
  --name myregistry \
  --source mysourceregistry.azurecr.io/aci-helloworld@sha256:123456abcdefg
```

### <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

```azurepowershell
Import-AzContainerRegistryImage -RegistryName myregistry -ResourceGroupName myResourceGroup -SourceRegistryUri mysourceregistry.azurecr.io -SourceImage aci-helloworld:latest
```

En el ejemplo siguiente se importa la imagen `aci-helloworld:latest` a *myregistry* desde el registro de origen *mysourceregistry*, en el que está deshabilitado el acceso al punto de conexión público del registro. Proporcione el identificador de recurso del registro de origen con el parámetro `--registry`. Tenga en cuenta que el parámetro `--source` especifica solo el repositorio de origen y el nombre de la imagen, no el nombre del servidor de inicio de sesión del registro.

```azurepowershell
Import-AzContainerRegistryImage -RegistryName myregistry -ResourceGroupName myResourceGroup -SourceRegistryResourceId '/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/sourceResourceGroup/providers/Microsoft.ContainerRegistry/registries/mysourceregistry' -SourceImage aci-helloworld:latest
```

En el ejemplo siguiente se importa una imagen mediante el hash del manifiesto (hash de SHA-256, representado como `sha256:...`) en lugar de mediante etiqueta:

```azurepowershell
Import-AzContainerRegistryImage -RegistryName myregistry -ResourceGroupName myResourceGroup -SourceRegistryUri mysourceregistry.azurecr.io -SourceImage aci-helloworld@sha256:123456abcdefg
```

---

### <a name="import-from-a-registry-in-a-different-subscription"></a>Importación desde un registro en una suscripción distinta

### <a name="azure-cli"></a>[CLI de Azure](#tab/azure-cli)

En el ejemplo siguiente, *mysourceregistry* está en una suscripción diferente de *myregistry* en el mismo inquilino de Active Directory. Proporcione el identificador de recurso del registro de origen con el parámetro `--registry`. Tenga en cuenta que el parámetro `--source` especifica solo el repositorio de origen y el nombre de la imagen, no el nombre del servidor de inicio de sesión del registro.

```azurecli
az acr import \
  --name myregistry \
  --source samples/aci-helloworld:latest \
  --image aci-hello-world:latest \
  --registry /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/sourceResourceGroup/providers/Microsoft.ContainerRegistry/registries/mysourceregistry
```

### <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

En el ejemplo siguiente, *mysourceregistry* está en una suscripción diferente de *myregistry* en el mismo inquilino de Active Directory. Proporcione el identificador de recurso del registro de origen con el parámetro `--registry`. Tenga en cuenta que el parámetro `--source` especifica solo el repositorio de origen y el nombre de la imagen, no el nombre del servidor de inicio de sesión del registro.

```azurepowershell
Import-AzContainerRegistryImage -RegistryName myregistry -ResourceGroupName myResourceGroup -SourceRegistryResourceId '/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/sourceResourceGroup/providers/Microsoft.ContainerRegistry/registries/mysourceregistry' -SourceImage aci-helloworld:latest
```

---

### <a name="import-from-a-registry-using-service-principal-credentials"></a>Importación desde un registro con credenciales de entidad de servicio

Para importar desde un registro al que no se puede acceder mediante los permisos integrados de Active Directory, puede usar credenciales de entidad de servicio (si están disponibles) para el registro de origen. Proporcione el appID y la contraseña de la [entidad de servicio](container-registry-auth-service-principal.md) de Active Directory que tiene acceso ACRPull al registro de origen. El uso de una entidad de servicio es útil para sistemas de compilación y otros sistemas desatendidos que deban importar imágenes en el registro.

### <a name="azure-cli"></a>[CLI de Azure](#tab/azure-cli)

```azurecli
az acr import \
  --name myregistry \
  --source sourceregistry.azurecr.io/sourcerrepo:tag \
  --image targetimage:tag \
  --username <SP_App_ID> \
  --password <SP_Passwd>
```

### <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

```azurepowershell
Import-AzContainerRegistryImage -RegistryName myregistry -ResourceGroupName myResourceGroup -SourceRegistryUri sourceregistry.azurecr.io -SourceImage sourcerrepo:tag -Username <SP_App_ID> -Password <SP_Passwd>
```

---

## <a name="import-from-an-azure-container-registry-in-a-different-ad-tenant"></a>Importación de un registro de contenedor de Azure de otro inquilino de AD

Para importar desde un registro de contenedor de Azure de otro inquilino de Azure Active Directory, especifique el registro de origen mediante el nombre del servidor de inicio de sesión y proporcione las credenciales que permiten el acceso de extracción al registro.

### <a name="cross-tenant-import-with-username-and-password"></a>Importación entre inquilinos con nombre de usuario y contraseña
Por ejemplo, use un [token de ámbito de repositorio](container-registry-repository-scoped-permissions.md) y la contraseña, o el appID y la contraseña de una [entidad de servicio](container-registry-auth-service-principal.md) de Active Directory que tenga acceso ACRPull al registro de origen.

### <a name="azure-cli"></a>[CLI de Azure](#tab/azure-cli)

```azurecli
az acr import \
  --name myregistry \
  --source sourceregistry.azurecr.io/sourcerrepo:tag \
  --image targetimage:tag \
  --username <SP_App_ID> \
  --password <SP_Passwd>
```

### <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

```azurepowershell
Import-AzContainerRegistryImage -RegistryName myregistry -ResourceGroupName myResourceGroup -SourceRegistryUri sourceregistry.azurecr.io -SourceImage sourcerrepo:tag -Username <SP_App_ID> -Password <SP_Passwd>
```

---

### <a name="cross-tenant-import-with-access-token"></a>Importación entre inquilinos con token de acceso

Para acceder al registro de origen mediante una identidad en el inquilino de origen que tiene permisos de registro, puede obtener un token de acceso:

### <a name="azure-cli"></a>[CLI de Azure](#tab/azure-cli)

```azurecli
# Login to Azure CLI with the identity, for example a user-assigned managed identity
az login --identity --username <identity_ID>

# Get access token returned by `az account get-access-token`
az account get-access-token
```

En el inquilino de destino, pase el token de acceso como contraseña al comando `az acr import`. El registro de origen se especifica con el nombre del servidor de inicio de sesión. Observe que no se necesita ningún nombre de usuario en este comando:

```azurecli
az acr import \
  --name myregistry \
  --source sourceregistry.azurecr.io/sourcerrepo:tag \
  --image targetimage:tag \
  --password <access-token>
```

### <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

```azurepowershell
# Login to Azure PowerShell with the identity, for example a user-assigned managed identity
Connect-AzAccount -Identity -AccountId <identity_ID>

# Get access token returned by `Get-AzAccessToken`
Get-AzAccessToken
```

En el inquilino de destino, pase el token de acceso como contraseña al cmdlet `Import-AzContainerRegistryImage`. El registro de origen se especifica con el nombre del servidor de inicio de sesión. Observe que no se necesita ningún nombre de usuario en este comando:

```azurepowershell
Import-AzContainerRegistryImage -RegistryName myregistry -ResourceGroupName myResourceGroup -SourceRegistryUri sourceregistry.azurecr.io -SourceImage sourcerrepo:tag -Password <access-token>
```

---

## <a name="import-from-a-non-azure-private-container-registry"></a>Importación desde un registro de contenedor privado de Azure

Importe una imagen desde un registro privado que no sea de Azure mediante la especificación de credenciales que permitan el acceso de extracción al registro. Por ejemplo, extraiga una imagen de un registro privado de Docker:

### <a name="azure-cli"></a>[CLI de Azure](#tab/azure-cli)

```azurecli
az acr import \
  --name myregistry \
  --source docker.io/sourcerepo/sourceimage:tag \
  --image sourceimage:tag \
  --username <username> \
  --password <password>
```

### <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)
```azurepowershell
Import-AzContainerRegistryImage -RegistryName myregistry -ResourceGroupName myResourceGroup -SourceRegistryUri docker.io/sourcerepo -SourceImage sourcerrepo:tag -Username <username> -Password <password>
```

---

## <a name="next-steps"></a>Pasos siguientes

En este artículo, ha aprendido sobre la importación de imágenes de contenedor en un registro de contenedor de Azure desde un registro público u otro registro privado.

### <a name="azure-cli"></a>[CLI de Azure](#tab/azure-cli)

* Para obtener opciones de importación de imágenes adicionales, vea la referencia del comando [az acr import][az-acr-import].

### <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

* Para obtener opciones de importación de imágenes adicionales, vea la referencia de comando [Import-AzContainerRegistryImage][import-azcontainerregistryimage].

---

* La importación de imágenes puede ayudarlo a mover contenido a un registro de contenedor en otro inquilino de Azure AD, otra suscripción u otra región de Azure. Para más información, consulte el artículo sobre cómo [trasladar manualmente un registro de contenedor a otra región](manual-regional-move.md).

* Aprenda a [deshabilitar un artefacto de compilación](data-loss-prevention.md) desde un registro de contenedor restringido por red.


<!-- LINKS - Internal -->
[az-login]: /cli/azure/reference-index#az_login
[az-acr-import]: /cli/azure/acr#az_acr_import
[azure-cli]: /cli/azure/install-azure-cli
[install-the-azure-az-powershell-module]: /powershell/azure/install-az-ps
[import-azcontainerregistryimage]: /powershell/module/az.containerregistry/import-azcontainerregistryimage
