---
title: Rotación de credenciales de entidad de servicio para un clúster de Red Hat OpenShift en Azure (ARO)
description: Descubra cómo rotar credenciales de entidad de servicio en Red Hat OpenShift en Azure (ARO).
author: swiencki
ms.author: b-swiencki
ms.service: azure-redhat-openshift
ms.topic: article
ms.date: 05/31/2021
ms.openlocfilehash: 286126bdd3cfcc5139549d1137431c8016c99472
ms.sourcegitcommit: 8b38eff08c8743a095635a1765c9c44358340aa8
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 06/30/2021
ms.locfileid: "113090878"
---
# <a name="rotate-service-principal-credentials-for-your-azure-red-hat-openshift-aro-cluster"></a>Rotación de credenciales de entidad de servicio para un clúster de Red Hat OpenShift en Azure (ARO)
El artículo proporciona los detalles necesarios para rotar credenciales de entidad de servicio en clústeres de Red Hat OpenShift en Azure ([ARO](https://github.com/Azure/ARO-RP)).

## <a name="before-you-begin"></a>Antes de empezar
En el artículo, se da por supuesto que hay un clúster de ARO con las actualizaciones más recientes aplicadas.

La versión mínima necesaria de la CLI de Azure para rotar credenciales de entidad de servicio en un clúster de ARO es la 2.24.0.

Para comprobar la versión de la CLI de Azure, ejecute:
```azurecli-interactive
# Azure CLI version
az --version
```
Para instalar o actualizar la CLI de Azure, siga las indicaciones que se dan en [Instalación de la CLI de Azure](/cli/azure/install-azure-cli).

En las instrucciones siguientes, se usa la sintaxis de Bash.

## <a name="service-principal-credential-rotation"></a>Rotación de credenciales de entidad de servicio
>[!IMPORTANT]
>  La rotación de credenciales de entidad de servicio puede tardar hasta dos horas, según el estado del clúster.

La rotación de credenciales de entidad de servicio tiene dos métodos:
 - [Rotación de credenciales de entidad de servicio automatizada](#Automated-Service-Principal-Credential-Rotation).
 - [Rotación de credenciales de entidad de servicio con un id. o un secreto de cliente proporcionados por el usuario](#User-Provided-client-id-and-client-secret-Service-Principal-Credential-Rotation).

### <a name="automated-service-principal-credential-rotation"></a>Rotación de credenciales de entidad de servicio automatizada <a id="Automated-Service-Principal-Credential-Rotation"></a>
>[!IMPORTANT]
>  La rotación de credenciales de entidad de servicio automatizada requiere que el clúster de ARO se haya creado con la versión 2.24.0 o posterior de la CLI de Azure.

La rotación de credenciales de entidad de servicio automatizada comprueba si la entidad de servicio existe y rota o crea una nueva entidad de servicio.

Para rotar automáticamente las credenciales de entidad de servicio, utilice el siguiente comando:

```azurecli-interactive
# Automatically rotate service principal credentials
az aro update --refresh-credentials --name MyManagedCluster --resource-group MyResourceGroup
```

### <a name="user-provided-client-id-and-client-secret-service-principal-credential-rotation"></a>Rotación de credenciales de entidad de servicio con un id. o un secreto de cliente proporcionados por el usuario <a id="User-Provided-client-id-and-client-secret-Service-Principal-Credential-Rotation"></a>


Para rotar manualmente credenciales de entidad de servicio con un id. y un secreto de cliente proporcionados por el usuario, siga estas instrucciones:

Recupere el id. de cliente (`--client-id`) de la entidad de servicio y establézcalo como la variable de entorno `SP_ID`.
```azurecli-interactive
# Retrieve the service principal clientId
SP_ID=$(az aro show --name MyManagedCluster --resource-group MyResourceGroup \
    --query servicePrincipalProfile.clientId -o tsv)
```
Genere un nuevo secreto seguro (`--client-secret`) para la entidad de servicio usando la variable `SP_ID` anterior. Almacene el nuevo secreto seguro como la variable de entorno `SP_SECRET`.
```azurecli-interactive
# Generate a new secure secret for the service principal
SP_SECRET=$(az ad sp credential reset --name $SP_ID --query password -o tsv)
```
Rote las credenciales de entidad de servicio usando las variables de entorno anteriores.
```azurecli-interactive
# Rotate service principal credentials
az aro update --client-id $SP_ID --client-secret $SP_SECRET \
    --name MyManagedCluster --resource-group MyResourceGroup
```

## <a name="troubleshoot"></a>Solucionar problemas
### <a name="service-principal-expiration-date"></a>Fecha de expiración de las entidades de servicio
Las credenciales de entidad de servicio tienen una fecha de expiración establecida de un año y deben rotarse dentro de ese período de tiempo.

Si se supera la fecha de expiración, pueden ocurrir los siguientes errores:
```bash
Failed to refresh the Token for request to MyResourceGroup StatusCode=401
Original Error: Request failed. Status Code = '401'.
[with]
Response body: {"error":"invalid_client","error_description": The provided client secret keys are expired.
[or]
Response body: {"error":"invalid_client","error_description": Invalid client secret is provided.
```
Para comprobar la fecha de expiración de las credenciales de la entidad de servicio, ejecute el siguiente código:
```azurecli-interactive
# Service principal expiry in ISO 8601 UTC format
SP_ID=$(az aro show --name MyManagedCluster --resource-group MyResourceGroup \
    --query servicePrincipalProfile.clientId -o tsv)
az ad sp credential list --id $SP_ID --query "[].endDate" -o tsv
```
Si las credenciales de entidad de servicio han expirado, actualícelas con uno de los dos métodos de rotación de credenciales.

### <a name="cluster-aad-application-contains-a-client-secret-with-an-empty-description"></a>La aplicación de AAD del clúster contiene un secreto de cliente con una descripción vacía
Cuando se usa la [rotación de credenciales de entidad de servicio automatizada](#Automated-Service-Principal-Credential-Rotation), se produce el siguiente error:
```bash
$ az aro update --refresh-credentials --name MyManagedCluster --resource-group MyResourceGroup

Cluster AAD application contains a client secret with an empty description.
Please either manually remove the existing client secret and run `az aro update --refresh-credentials`,
or manually create a new client secret and run `az aro update --client-secret <ClientSecret>`.
```
El clúster no se ha creado con la versión 2.24.0 o posterior de la CLI de Azure. Use el método de [rotación de credenciales de entidad de servicio con un id. o un secreto de cliente proporcionados por el usuario](#User-Provided-client-id-and-client-secret-Service-Principal-Credential-Rotation).

### <a name="azure-cli-aro-update-help"></a>Ayuda para la actualización de ARO con la CLI de Azure
Si desea obtener más información, utilice el siguiente comando para consultar la ayuda para la actualización de ARO con la CLI de Azure:
```azurecli-interactive
# Azure CLI ARO update help
az aro update -h
```
