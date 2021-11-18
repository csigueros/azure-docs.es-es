---
title: Especificación de la API de plantilla de ARM de Container Apps (versión preliminar)
description: Información sobre cómo explorar las propiedades disponibles en la plantilla de ARM de Container Apps
services: container-apps
author: craigshoemaker
ms.service: container-apps
ms.topic: reference
ms.date: 11/02/2021
ms.author: cshoe
ms.custom: ignite-fall-2021
ms.openlocfilehash: b0f0c4c360b8b9b3cdbe78c833df2a76c2b27d84
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/11/2021
ms.locfileid: "132323531"
---
# <a name="container-apps-preview-arm-template-api-specification"></a>Especificación de la API de plantilla de ARM de Container Apps (versión preliminar)

Las implementaciones de Azure Container Apps se basan en una plantilla de Azure Resource Manager (ARM). En las tablas siguientes se describen las propiedades disponibles en la plantilla de ARM de la aplicación de contenedor.

[Plantilla de ARM de ejemplo de ejemplos de uso](#example)

## <a name="resources"></a>Recursos

Las entradas de la matriz `resources` de la plantilla de ARM tienen las siguientes propiedades:

| Propiedad | Descripción | Tipo de datos |
|---|---|--|
| `name` | Nombre de la aplicación de Container Apps | string |
| `location` | Región de Azure donde se implementa la instancia de Container Apps | string |
| `tags` | Colección de etiquetas de Azure asociadas a la aplicación de contenedor | array |
| `type` | El punto de conexión de ARM `Microsoft.Web/containerApps` siempre determina a qué API reenviar  | string |

En este ejemplo, colocará los valores en lugar de los tokens de marcador de posición entre corchetes `<>`.

## <a name="properties"></a>properties

El objeto `properties` de un recurso tiene las siguientes propiedades:

| Propiedad | Descripción | Tipo de datos | Solo lectura |
|---|---|---|---|
| `provisioningState` | Estado de una operación de larga duración, por ejemplo, cuando se crea una revisión de contenedor. Los valores posibles son "aprovisionando", "aprovisionado" y "error". Compruebe si la aplicación está en funcionamiento. | string | Sí |
| `environmentId` | Identificador de entorno de la aplicación de contenedor. **Es una propiedad necesaria.** | string | No |
| `latestRevisionName` | Nombre de la revisión más reciente. | string | Sí |
| `latestRevisionFqdn` | Dirección URL de la revisión más reciente. | string | Sí |

El valor `environmentId` toma la forma siguiente:

```console
/subscriptions/<SUBSCRIPTION_ID>/resourcegroups/<RESOURCE_GROUP_NAME>/providers/Microsoft.Web/environmentId/<ENVIRONMENT_NAME>
```

En este ejemplo, colocará los valores en lugar de los tokens de marcador de posición entre corchetes `<>`.

## <a name="propertiesconfiguration"></a>properties.configuration

El objeto `properties.configuration` de un recurso tiene las siguientes propiedades:

| Propiedad | Descripción | Tipo de datos |
|---|---|---|
| `activeRevisionsMode` | Si se establece en `multiple`, permite conservar varias revisiones. Si se establece en `single`, se desactivan automáticamente las revisiones antiguas y solo se conserva la última revisión activa. | string |
| `secrets` | Define los valores secretos de la aplicación de contenedor. | object |
| `ingress` | Objeto que define la configuración de accesibilidad pública de una aplicación de contenedor. | object |
| `registries` | Objeto de configuración que hace referencia a las credenciales de los registros de contenedores privados. Las entradas definidas con `secretref` hacen referencia al objeto de configuración de secretos. | object |

Los cambios realizados en la sección `configuration` son [cambios en el ámbito de la aplicación](revisions.md#application-scope-changes), lo que no desencadena una nueva revisión.

## <a name="propertiestemplate"></a>properties.template

El objeto `properties.template` de un recurso tiene las siguientes propiedades:

| Propiedad | Descripción | Tipo de datos |
|---|---|---|
| `revisionSuffix` | Nombre descriptivo de una revisión. Este valor debe ser único, ya que el runtime rechaza los conflictos con los valores de sufijo de nombre de revisión existentes. | string |
| `containers` | Objeto de configuración que define qué imágenes de contenedor se incluyen en la aplicación de contenedor. | object |
| `scale` | Objeto de configuración que define las reglas de escalado de la aplicación de contenedor. | object |
| `dapr` | Objeto de configuración que define la configuración de Dapr de la aplicación de contenedor. | object  |

Los cambios realizados en la sección `template` son [cambios en el ámbito de la revisión](revisions.md#revision-scope-changes), lo que sí desencadena una nueva revisión.

## <a name="example"></a>Ejemplo

Esta es una plantilla de ARM de ejemplo que se usa para implementar una aplicación de contenedor.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-08-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "containerappName": {
            "defaultValue": "mycontainerapp",
            "type": "String"
        },
        "location": {
            "defaultValue": "canadacentral",
            "type": "String"
        },
        "environment_name": {
            "defaultValue": "myenvironment",
            "type": "String"
        }
    },
    "variables": {},
    "resources": [
        {
            "apiVersion": "2021-03-01",
            "type": "Microsoft.Web/containerApps",
            "name": "[parameters('containerappName')]",
            "location": "[parameters('location')]",
            "properties": {
                "kubeEnvironmentId": "[resourceId('Microsoft.Web/kubeEnvironments', parameters('environment_name'))]",
                "configuration": {
                    "secrets": [
                        {
                            "name": "mysecret",
                            "value": "thisismysecret"
                        }
                    ],
                    "ingress": {
                        "external": true,
                        "targetPort": 80,
                        "allowInsecure": false,
                        "traffic": [
                            {
                                "latestRevision": true,
                                "weight": 100
                            }
                        ]
                    }
                },
                "template": {
                    "revisionSuffix": "myrevision",
                    "containers": [
                        {
                            "name": "nginx",
                            "image": "nginx",
                            "env": [
                                {
                                    "name": "HTTP_PORT",
                                    "value": "80"
                                },
                                {
                                    "name": "SECRET_VAL",
                                    "secretRef": "mysecret"
                                }
                            ],
                            "resources": {
                                "cpu": 0.5,
                                "memory": "1Gi"
                            }
                        }
                    ],
                    "scale": {
                        "minReplicas": 1,
                        "maxReplicas": 3
                    }
                }
            }
        }
    ]
}
```
