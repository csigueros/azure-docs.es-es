---
title: Creación de un perfil y un punto de conexión de Content Delivery Network (CDN) con la CLI de Azure
description: Scripts de ejemplo de la CLI de Azure para crear un perfil de Azure CDN, un punto de conexión, un grupo de origen, un origen y un dominio personalizado.
author: duongau
ms.author: duau
manager: danielgi
ms.date: 03/09/2021
ms.topic: sample
ms.service: azure-cdn
ms.devlang: azurecli
ms.custom: devx-track-azurecli
ms.openlocfilehash: 46966ed6e4e0db1be50919d368bf1ff2fd0fa85a
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/03/2021
ms.locfileid: "131476859"
---
# <a name="create-an-azure-cdn-profile-and-endpoint-using-the-azure-cli"></a>Creación de un perfil y un punto de conexión de Azure CDN con la CLI de Azure

Como alternativa a Azure Portal, puede usar estos scripts de ejemplo de la CLI de Azure para administrar las siguientes operaciones de CDN:

- Cree un perfil de CDN.
- Crear un punto de conexión de CDN.
- Cree un grupo de origen de CDN y conviértalo en el grupo predeterminado.
- Cree un origen de CDN.
- Cree un dominio personalizado y habilite HTTPS.

[!INCLUDE [azure-cli-prepare-your-environment.md](../../../../includes/azure-cli-prepare-your-environment.md)]

## <a name="sample-scripts"></a>Muestras de scripts

Si aún no tiene un grupo de recursos para el perfil de CDN, créelo con el comando `az group create`:

```azurecli
# Create a resource group to use for the CDN.
az group create --name MyResourceGroup --location eastus

```

El siguiente script de la CLI de Azure crea un perfil de CDN y un punto de conexión de CDN:

```azurecli
# Create a CDN profile.
az cdn profile create --resource-group MyResourceGroup --name MyCDNProfile --sku Standard_Microsoft

# Create a CDN endpoint.
az cdn endpoint create --resource-group MyResourceGroup --name MyCDNEndpoint --profile-name MyCDNProfile --origin www.contoso.com

```

El siguiente script de la CLI de Azure crea un grupo de origen de CDN, establece el grupo de origen predeterminado para un punto de conexión y crea un nuevo origen:

```azurecli
# Create an origin group.
az cdn origin-group create --resource-group MyResourceGroup --endpoint-name MyCDNEndpoint --profile-name MyCDNProfile --name MyOriginGroup --origins origin-0

# Make the origin group the default group of an endpoint.
az cdn endpoint update --resource-group MyResourceGroup --name MyCDNEndpoint --profile-name MyCDNProfile --default-origin-group MyOriginGroup
                           
# Create another origin for an endpoint.
az cdn origin create --resource-group MyResourceGroup --endpoint-name MyCDNEndpoint --profile-name MyCDNProfile --name origin-1 --host-name example.contoso.com

```

El siguiente script de la CLI de Azure crea un dominio personalizado de CDN y habilita HTTPS. Para poder asociar un dominio personalizado con un punto de conexión de Azure CDN, primero debe crear un registro de nombre canónico (CNAME) con Azure DNS o con su proveedor de DNS que apunte a su punto de conexión de CDN. Para más información, consulte [Creación de un registro DNS de CNAME](../../../cdn/cdn-map-content-to-custom-domain.md#create-a-cname-dns-record).

```azurecli
# Associate a custom domain with an endpoint.
az cdn custom-domain create --resource-group MyResourceGroup --endpoint-name MyCDNEndpoint --profile-name MyCDNProfile --name MyCustomDomain --hostname www.example.com

# Enable HTTPS on the custom domain.
az cdn custom-domain enable-https --resource-group MyResourceGroup --endpoint-name MyCDNEndpoint --profile-name MyCDNProfile --name MyCustomDomain

```

## <a name="clean-up-resources"></a>Limpieza de recursos

Después de ejecutar los scripts de ejemplo, use el comando siguiente para quitar el grupo de recursos y todos los recursos asociados a él.

```azurecli
# Delete the resource group.
az group delete --name MyResourceGroup

```

## <a name="azure-cli-commands-used-in-this-article"></a>Comandos de la CLI de Azure usados en este artículo

- [az cdn endpoint create](/cli/azure/cdn/endpoint#az_cdn_endpoint_create)
- [az cdn endpoint update](/cli/azure/cdn/endpoint#az_cdn_endpoint_update)
- [az cdn origin create](/cli/azure/cdn/origin#az_cdn_origin_create)
- [az cdn origin-group create](/cli/azure/cdn/origin-group#az_cdn_origin_group_create)
- [az cdn profile create](/cli/azure/cdn/profile#az_cdn_profile_create)
- [az group create](/cli/azure/group#az_group_create)
- [az group delete](/cli/azure/group#az_group_delete)
- [az cdn custom-domain create](/cli/azure/cdn/custom-domain#az_cdn_custom_domain_create)
- [az cdn custom-domain enable-https](/cli/azure/cdn/custom-domain#az_cdn_custom_domain_enable_https)
