---
title: Implementación de un grupo de contenedores zonales en Azure Container Instances (ACI)
description: Aprenda a implementar un grupo de contenedores en una zona de disponibilidad.
ms.topic: article
ms.date: 10/13/2021
ms.custom: devx-track-js, devx-track-azurecli
ms.openlocfilehash: a2cce289dc95ecc876ba308ed9406fffb37b9e18
ms.sourcegitcommit: 8946cfadd89ce8830ebfe358145fd37c0dc4d10e
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/05/2021
ms.locfileid: "131858479"
---
# <a name="deploy-an-azure-container-instances-aci-container-group-in-an-availability-zone-preview"></a>Implementación de un grupo de contenedores de Azure Container Instances (ACI) en una zona de disponibilidad (versión preliminar)

Una [zona de disponibilidad][availability-zone-overview] es una zona separada físicamente en una región de Azure. Puede usar zonas de disponibilidad para proteger las aplicaciones contenedorizadas de un error improbable o la pérdida de un centro de datos completo. Azure Container Instances (ACI) admite implementaciones de grupos de contenedores zonales, lo que significa que la instancia está anclada a una zona de disponibilidad específica y seleccionada automáticamente. La zona de disponibilidad se especifica en el nivel de grupo de contenedores. Los contenedores de un grupo de contenedores no pueden tener zonas de disponibilidad únicas. Para cambiar la zona de disponibilidad del grupo de contenedores, debe eliminar el grupo de contenedores y crear otro grupo de contenedores con la nueva zona de disponibilidad.

> [!IMPORTANT]
> Esta funcionalidad actualmente está en su versión preliminar. Las versiones preliminares están a su disposición con la condición de que acepte los términos de uso adicionales.

> [!IMPORTANT]
> Las implementaciones de grupos de contenedores zonales se admiten en la mayoría de las regiones en las que ACI está disponible para grupos de contenedores de Linux y Windows Server 2019. Para obtener más información, consulte [Regiones y disponibilidad de recursos][container-regions].

> [!NOTE]
> Los ejemplos que aparecen en este artículo están formateados para el shell de Bash. Si prefiere otro shell, ajuste los caracteres de continuación de línea en consecuencia.

## <a name="limitations"></a>Limitaciones

> [!IMPORTANT]
> Esta característica no está disponible actualmente para Azure Portal.

* Los grupos de contenedores con recursos de GPU no admiten zonas de disponibilidad en este momento.
* Los grupos de contenedores insertados por Virtual Network no admiten zonas de disponibilidad en este momento.
* Los grupos de contenedores de Windows Server 2016 no admiten zonas de disponibilidad en este momento.

### <a name="version-requirements"></a>Requisitos de versión

* Si usa CLI de Azure, asegúrese de que esté instalada la versión `2.30.0` o posterior.
* Si usa PowerShell, asegúrese de que esté instalada la versión `2.1.1-preview` o posterior.
* Si usa el SDK de Java, asegúrese de que esté instalada la versión `2.9.0` o posterior.
* La compatibilidad con la zona de disponibilidad solo está disponible en la versión de la API de ACI `09-01-2021` o posterior.

## <a name="deploy-a-container-group-using-an-azure-resource-manager-arm-template"></a>Implementación de un grupo de contenedores mediante una plantilla de Azure Resource Manager (ARM)

### <a name="create-the-arm-template"></a>Creación de la plantilla de Resource Manager

Para empezar, copie el siguiente código JSON en un nuevo archivo denominado `azuredeploy.json`. Esta plantilla de ejemplo implementa un grupo de contenedores con un único contenedor en la zona de disponibilidad 1 de la región Este de EE. UU.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "metadata": {
        "_generator": {
            "name": "bicep",
            "version": "0.4.1.14562",
            "templateHash": "12367894147709986470"
        }
    },
    "parameters": {
        "name": {
            "type": "string",
            "defaultValue": "acilinuxpublicipcontainergroup",
            "metadata": {
                "description": "Name for the container group"
            }
        },
        "image": {
            "type": "string",
            "defaultValue": "mcr.microsoft.com/azuredocs/aci-helloworld",
            "metadata": {
                "description": "Container image to deploy. Should be of the form repoName/imagename:tag for images stored in public Docker Hub, or a fully qualified URI for other registries. Images from private registries require additional registry credentials."
            }
        },
        "port": {
            "type": "int",
            "defaultValue": 80,
            "metadata": {
                "description": "Port to open on the container and the public IP address."
            }
        },
        "cpuCores": {
            "type": "int",
            "defaultValue": 1,
            "metadata": {
                "description": "The number of CPU cores to allocate to the container."
            }
        },
        "memoryInGb": {
            "type": "int",
            "defaultValue": 2,
            "metadata": {
                "description": "The amount of memory to allocate to the container in gigabytes."
            }
        },
        "restartPolicy": {
            "type": "string",
            "defaultValue": "Always",
            "allowedValues": [
                "Always",
                "Never",
                "OnFailure"
            ],
            "metadata": {
                "description": "The behavior of Azure runtime if container has stopped."
            }
        },
        "location": {
            "type": "string",
            "defaultValue": "eastus",
            "metadata": {
                "description": "Location for all resources."
            }
        }
    },
    "functions": [],
    "resources": [
        {
            "type": "Microsoft.ContainerInstance/containerGroups",
            "apiVersion": "2021-09-01",
            "zones": [
                "1"
            ],
            "name": "[parameters('name')]",
            "location": "[parameters('location')]",
            "properties": {
                "containers": [
                    {
                        "name": "[parameters('name')]",
                        "properties": {
                            "image": "[parameters('image')]",
                            "ports": [
                                {
                                    "port": "[parameters('port')]",
                                    "protocol": "TCP"
                                }
                            ],
                            "resources": {
                                "requests": {
                                    "cpu": "[parameters('cpuCores')]",
                                    "memoryInGB": "[parameters('memoryInGb')]"
                                }
                            }
                        }
                    }
                ],
                "osType": "Linux",
                "restartPolicy": "[parameters('restartPolicy')]",
                "ipAddress": {
                    "type": "Public",
                    "ports": [
                        {
                            "port": "[parameters('port')]",
                            "protocol": "TCP"
                        }
                    ]
                }
            }
        }
    ],
    "outputs": {
        "containerIPv4Address": {
            "type": "string",
            "value": "[reference(resourceId('Microsoft.ContainerInstance/containerGroups', parameters('name'))).ipAddress.ip]"
        }
    }
}
```

### <a name="deploy-the-arm-template"></a>Implementación de la plantilla de ARM

Cree un grupo de recursos con el comando [az group create][az-group-create]:

```azurecli
az group create --name myResourceGroup --location eastus
```

Implemente la plantilla con el comando [az deployment group create][az-deployment-group-create]:

```azurecli
az deployment group create \
  --resource-group myResourceGroup \
  --template-file azuredeploy.json
```

## <a name="get-container-group-details"></a>Obtención de detalles del grupo de contenedores

Para comprobar que el grupo de contenedores se implementó correctamente en una zona de disponibilidad, vea los detalles del grupo de contenedores con el comando [az container show][az-container-show]:

```azurecli
az containershow --name acilinuxcontainergroup --resource-group myResourceGroup
```

<!-- LINKS - Internal -->
[az-container-create]: /cli/azure/container#az_container_create
[container-regions]: container-instances-region-availability.md
[az-container-show]: /cli/azure/container#az_container_show
[az-group-create]: /cli/azure/group#az_group_create
[az-deployment-group-create]: /cli/azure/deployment#az_deployment_group_create
[availability-zone-overview]: /availability-zones/az-overview.md