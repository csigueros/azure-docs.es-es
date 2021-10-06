---
title: Parámetros personalizados en una plantilla de Resource Manager
description: Aprenda a usar parámetros personalizados en una plantilla de Resource Manager con la integración y entrega continuas en Azure Data Factory.
ms.service: data-factory
ms.subservice: ci-cd
author: nabhishek
ms.author: abnarain
ms.reviewer: jburchel
ms.topic: conceptual
ms.date: 09/24/2021
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 22c64b5489cbcf0206624e613fb9d3449fa4a4d1
ms.sourcegitcommit: e8c34354266d00e85364cf07e1e39600f7eb71cd
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/29/2021
ms.locfileid: "129219351"
---
# <a name="use-custom-parameters-with-the-resource-manager-template"></a>Usar parámetros personalizados con la plantilla de Resource Manager

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

Si la instancia de desarrollo tiene un repositorio de Git asociado, puede invalidar los parámetros de plantilla de Resource Manager predeterminados de la plantilla de Resource Manager que se genera mediante la publicación o exportación de la plantilla. Es posible que desee reemplazar la configuración de parámetros de Resource Manager predeterminada en estos escenarios:

* Se usa CI/CD automatizada y se quieren cambiar algunas propiedades durante la implementación de Resource Manager, pero las propiedades no están parametrizadas de forma predeterminada.
* La fábrica es tan grande que la plantilla de Resource Manager predeterminada no es válida porque contiene más parámetros que el número máximo permitido (256).

    Para controlar el límite de 256 parámetros personalizados, hay tres opciones:    
  
    * Use el archivo de parámetros personalizado y quite las propiedades que no necesitan parametrización, es decir, las que pueden mantener un valor predeterminado y, por tanto, reducir el recuento de parámetros.
    * Refactorice la lógica en el flujo de datos para reducir los parámetros; por ejemplo, todos los parámetros de canalización tienen el mismo valor, solo puede usar parámetros globales en su lugar.
    * Divida una factoría de datos en varios flujos de datos.

Para reemplazar la configuración de parámetros de Resource Manager predeterminada, vaya al centro **Administrar** y seleccione **Plantilla de ARM** en la sección "Control de código fuente". En la sección **Configuración de parámetros de ARM**, haga clic en el icono **Editar** en "Editar configuración de parámetros" para abrir el editor de código de configuración de parámetros de Resource Manager.

:::image type="content" source="media/author-management-hub/management-hub-custom-parameters.png" alt-text="Administración de parámetros personalizados":::

> [!NOTE]
> La **configuración del parámetros de ARM** solo está habilitada en "modo GIT". Actualmente está deshabilitada en el" modo activo" o el modo de "Data Factory".

Al crear una configuración de parámetros de Resource Manager se crea un archivo denominado **arm-template-parameters-definition.json** en la carpeta raíz de la rama de Git. Debe usar ese nombre de archivo exacto.

:::image type="content" source="media/continuous-integration-delivery/custom-parameters.png" alt-text="Archivo de parámetros personalizados":::

Al realizar la publicación desde la rama de colaboración, Data Factory leerá este archivo y usará su configuración para generar las propiedades que se parametrizarán. Si no se encuentra ningún archivo, se usa la plantilla predeterminada.

Al exportar una plantilla de Resource Manager, Data Factory lee este archivo desde la rama en la que se está trabajando en ese momento, no la rama de colaboración. Puede crear o editar el archivo desde una rama privada, donde pueda probar los cambios si selecciona **Export ARM Template** (Exportar plantilla de ARM) en la interfaz de usuario. Después, puede combinar el archivo en la rama de colaboración.

> [!NOTE]
> Una configuración de parámetros de Resource Manager personalizada no cambia el límite de 256 parámetros de la plantilla de Resource Manager. Le permite elegir y reducir el número de propiedades parametrizadas.

## <a name="custom-parameter-syntax"></a>Sintaxis de los parámetros personalizados

A continuación se indican algunas de las instrucciones que se deben seguir para crear el archivo de parámetros personalizados, **arm-template-parameters-definition.json**. El archivo consta de una sección para cada tipo de entidad: desencadenador, canalización, servicio vinculado, conjunto de datos, entorno de ejecución de integración i flujo de datos.

* Escriba la ruta de acceso de la propiedad en el tipo de entidad correspondiente.
* El establecimiento de un nombre de propiedad en `*` indica que quiere parametrizar todas las propiedades que incluye (solo hasta el primer nivel, no de forma recursiva). También puede proporcionar excepciones a esta configuración.
* El establecimiento del valor de una propiedad como una cadena indica que quiere parametrizar la propiedad. Use el formato `<action>:<name>:<stype>`.
   *  `<action>` puede ser uno de estos caracteres:
      * `=` significa que el valor actual se debe conservar como el predeterminado para el parámetro.
      * `-` significa que no se debe conservar el valor predeterminado para el parámetro.
      * `|` es un caso especial para los secretos de Azure Key Vault para cadenas de conexión o claves.
   * `<name>` es el nombre del parámetro. Si está en blanco, toma el nombre de la propiedad. Si el valor empieza por un carácter `-`, el nombre está abreviado. Por ejemplo, `AzureStorage1_properties_typeProperties_connectionString` se abreviará a `AzureStorage1_connectionString`.
   * `<stype>` es el tipo del parámetro. Si `<stype>` está en blanco, el tipo predeterminado es `string`. Valores admitidos: `string`, `securestring`, `int`, `bool`, `object`, `secureobject` y `array`.
* Al especificar una matriz en el archivo de definición indica que la propiedad coincidente en la plantilla es una matriz. Data Factory recorre en iteración todos los objetos de la matriz mediante la definición especificada en el objeto del entorno de ejecución de integración de la matriz. El segundo objeto, una cadena, se convierte en el nombre de la propiedad, que se utiliza como el nombre del parámetro para cada iteración.
* Una definición no puede ser específica de una instancia de recurso. Cualquier definición se aplica a todos los recursos de ese tipo.
* De forma predeterminada, todas las cadenas seguras, como los secretos de Key Vault, las cadenas de conexión, las claves y los tokens, están parametrizadas.
 
## <a name="sample-parameterization-template"></a>Plantilla de parametrización de ejemplo

Este es un ejemplo del aspecto que podría tener una configuración de parámetros de Resource Manager:

```json
{
    "Microsoft.DataFactory/factories/pipelines": {
        "properties": {
            "activities": [{
                "typeProperties": {
                    "waitTimeInSeconds": "-::int",
                    "headers": "=::object"
                }
            }]
        }
    },
    "Microsoft.DataFactory/factories/integrationRuntimes": {
        "properties": {
            "typeProperties": {
                "*": "="
            }
        }
    },
    "Microsoft.DataFactory/factories/triggers": {
        "properties": {
            "typeProperties": {
                "recurrence": {
                    "*": "=",
                    "interval": "=:triggerSuffix:int",
                    "frequency": "=:-freq"
                },
                "maxConcurrency": "="
            }
        }
    },
    "Microsoft.DataFactory/factories/linkedServices": {
        "*": {
            "properties": {
                "typeProperties": {
                    "accountName": "=",
                    "username": "=",
                    "connectionString": "|:-connectionString:secureString",
                    "secretAccessKey": "|"
                }
            }
        },
        "AzureDataLakeStore": {
            "properties": {
                "typeProperties": {
                    "dataLakeStoreUri": "="
                }
            }
        }
    },
    "Microsoft.DataFactory/factories/datasets": {
        "properties": {
            "typeProperties": {
                "*": "="
            }
        }
    }
}
```
Esta es una explicación de cómo se construye la plantilla anterior, desglosada por tipo de recurso.

### <a name="pipelines"></a>Procesos
    
* Cualquier propiedad de la ruta de acceso `activities/typeProperties/waitTimeInSeconds` está parametrizada. Cualquier actividad en una canalización que tiene una propiedad de nivel de código denominada `waitTimeInSeconds` (por ejemplo, la actividad `Wait`) está parametrizada como un número, con un nombre predeterminado. Sin embargo, no tendrá un valor predeterminado en la plantilla de Resource Manager. Será una entrada obligatoria durante la implementación de Resource Manager.
* De forma similar, una propiedad denominada `headers` (por ejemplo, en una actividad `Web`) está parametrizada con el tipo `object` (JObject). Tiene un valor predeterminado, que es el mismo que el de la factoría de origen.

### <a name="integrationruntimes"></a>IntegrationRuntimes

* Todas las propiedades bajo la ruta de acceso `typeProperties` están parametrizadas con sus valores predeterminados correspondientes. Por ejemplo, hay dos propiedades en las propiedades de tipo `IntegrationRuntimes`: `computeProperties` y `ssisProperties`. Ambos tipos de propiedades se crean con sus valores predeterminados y tipos (objeto) respectivos.

### <a name="triggers"></a>Desencadenadores

* En `typeProperties`, hay dos propiedades parametrizadas. La primera de ellas es `maxConcurrency`, que tiene especificado un valor predeterminado y es de tipo `string`. Tiene el nombre de parámetro predeterminado `<entityName>_properties_typeProperties_maxConcurrency`.
* La propiedad `recurrence` también está parametrizada. En ella, se especifica que todas las propiedades de ese nivel están parametrizadas como cadenas, con valores predeterminados y los nombres de parámetro. Una excepción es la propiedad `interval`, que está parametrizada como el tipo `int`. El sufijo del nombre del parámetro es `<entityName>_properties_typeProperties_recurrence_triggerSuffix`. De forma similar, la propiedad `freq` es una cadena y está parametrizada como una cadena. Sin embargo, la propiedad `freq` está parametrizada sin un valor predeterminado. El nombre está abreviado y con un sufijo. Por ejemplo, `<entityName>_freq`.

### <a name="linkedservices"></a>LinkedServices

* Los servicios vinculados son únicos. Dado que los servicios vinculados y los conjuntos de datos tienen una gran variedad de tipos, puede proporcionar una personalización específica de tipo. En este ejemplo, para todos los servicios vinculados de tipo `AzureDataLakeStore`, se aplicará una plantilla específica. Para todos los demás (a través de `*`), se aplicará otra plantilla.
* La propiedad `connectionString` se parametrizará como un valor `securestring`. No tendrá un valor predeterminado. Tendrá un nombre de parámetro abreviado con el sufijo `connectionString`.
* La propiedad `secretAccessKey` resulta ser `AzureKeyVaultSecret` (por ejemplo, en un servicio vinculado de Amazon S3). Se parametriza automáticamente como un secreto de Azure Key Vault y se captura desde el almacén de claves configurado. También puede parametrizar el propio almacén de claves.

### <a name="datasets"></a>Conjuntos de datos

* Aunque la personalización específica de tipos está disponible para conjuntos de datos, puede proporcionar configuración sin necesidad de una configuración explícita de nivel \*. En el ejemplo anterior, todas las propiedades del conjunto de datos de `typeProperties` están parametrizadas.

> [!NOTE]
> Si las **alertas y matrices de Azure** están configuradas para una canalización, no se admiten actualmente como parámetros para las implementaciones de ARM. Para volver a aplicar las alertas y las matrices en el nuevo entorno, siga las instrucciones de [Supervisión, alertas y matrices de Data Factory.](./monitor-metrics-alerts.md)
> 

## <a name="default-parameterization-template"></a>Plantilla de parametrización predeterminada

A continuación se muestra la plantilla de parametrización predeterminada actual. Si solo tiene que agregar algunos parámetros, la modificación directa de esta plantilla puede ser una buena idea porque no perderá la estructura de parametrización existente.

```json
{
    "Microsoft.DataFactory/factories": {
        "properties": {
            "globalParameters": {
                "*": {
                    "value": "="
                }
            }
        },
        "location": "="
    },
    "Microsoft.DataFactory/factories/pipelines": {
    },
    "Microsoft.DataFactory/factories/dataflows": {
    },
    "Microsoft.DataFactory/factories/integrationRuntimes":{
        "properties": {
            "typeProperties": {
                "ssisProperties": {
                    "catalogInfo": {
                        "catalogServerEndpoint": "=",
                        "catalogAdminUserName": "=",
                        "catalogAdminPassword": {
                            "value": "-::secureString"
                        }
                    },
                    "customSetupScriptProperties": {
                        "sasToken": {
                            "value": "-::secureString"
                        }
                    }
                },
                "linkedInfo": {
                    "key": {
                        "value": "-::secureString"
                    },
                    "resourceId": "="
                },
                "computeProperties": {
                    "dataFlowProperties": {
                        "externalComputeInfo": [{
                                "accessToken": "-::secureString"
                            }
                        ]
                    }
                }
            }
        }
    },
    "Microsoft.DataFactory/factories/triggers": {
        "properties": {
            "pipelines": [{
                    "parameters": {
                        "*": "="
                    }
                },  
                "pipelineReference.referenceName"
            ],
            "pipeline": {
                "parameters": {
                    "*": "="
                }
            },
            "typeProperties": {
                "scope": "="
            }
        }
    },
    "Microsoft.DataFactory/factories/linkedServices": {
        "*": {
            "properties": {
                "typeProperties": {
                    "accountName": "=",
                    "username": "=",
                    "userName": "=",
                    "accessKeyId": "=",
                    "servicePrincipalId": "=",
                    "userId": "=",
                    "host": "=",
                    "clientId": "=",
                    "clusterUserName": "=",
                    "clusterSshUserName": "=",
                    "hostSubscriptionId": "=",
                    "clusterResourceGroup": "=",
                    "subscriptionId": "=",
                    "resourceGroupName": "=",
                    "tenant": "=",
                    "dataLakeStoreUri": "=",
                    "baseUrl": "=",
                    "database": "=",
                    "serviceEndpoint": "=",
                    "batchUri": "=",
                    "poolName": "=",
                    "databaseName": "=",
                    "systemNumber": "=",
                    "server": "=",
                    "url":"=",
                    "functionAppUrl":"=",
                    "environmentUrl": "=",
                    "aadResourceId": "=",
                    "sasUri": "|:-sasUri:secureString",
                    "sasToken": "|",
                    "connectionString": "|:-connectionString:secureString",
                    "hostKeyFingerprint": "="
                }
            }
        },
        "Odbc": {
            "properties": {
                "typeProperties": {
                    "userName": "=",
                    "connectionString": {
                        "secretName": "="
                    }
                }
            }
        }
    },
    "Microsoft.DataFactory/factories/datasets": {
        "*": {
            "properties": {
                "typeProperties": {
                    "folderPath": "=",
                    "fileName": "="
                }
            }
        }
    },
    "Microsoft.DataFactory/factories/managedVirtualNetworks/managedPrivateEndpoints": {
        "properties": {
            "*": "="
        }
    }
}
```

## <a name="example-parameterizing-an-existing-azure-databricks-interactive-cluster-id"></a>Ejemplo: Parametrización de un identificador de clúster interactivo de Azure Databricks existente

En el ejemplo siguiente se muestra cómo agregar un único valor a la plantilla de parametrización predeterminada. Solamente se quiere agregar al archivo de parámetros un identificador de clúster interactivo de Azure Databricks existente para un servicio vinculado de Databricks. Tenga en cuenta que este archivo es el mismo que el anterior, salvo por la adición de `existingClusterId` en el campo de propiedades de `Microsoft.DataFactory/factories/linkedServices`.

```json
{
    "Microsoft.DataFactory/factories": {
        "properties": {
            "globalParameters": {
                "*": {
                    "value": "="
                }
            }
        },
        "location": "="
    },
    "Microsoft.DataFactory/factories/pipelines": {
    },
    "Microsoft.DataFactory/factories/dataflows": {
    },
    "Microsoft.DataFactory/factories/integrationRuntimes":{
        "properties": {
            "typeProperties": {
                "ssisProperties": {
                    "catalogInfo": {
                        "catalogServerEndpoint": "=",
                        "catalogAdminUserName": "=",
                        "catalogAdminPassword": {
                            "value": "-::secureString"
                        }
                    },
                    "customSetupScriptProperties": {
                        "sasToken": {
                            "value": "-::secureString"
                        }
                    }
                },
                "linkedInfo": {
                    "key": {
                        "value": "-::secureString"
                    },
                    "resourceId": "="
                }
            }
        }
    },
    "Microsoft.DataFactory/factories/triggers": {
        "properties": {
            "pipelines": [{
                    "parameters": {
                        "*": "="
                    }
                },  
                "pipelineReference.referenceName"
            ],
            "pipeline": {
                "parameters": {
                    "*": "="
                }
            },
            "typeProperties": {
                "scope": "="
            }
 
        }
    },
    "Microsoft.DataFactory/factories/linkedServices": {
        "*": {
            "properties": {
                "typeProperties": {
                    "accountName": "=",
                    "username": "=",
                    "userName": "=",
                    "accessKeyId": "=",
                    "servicePrincipalId": "=",
                    "userId": "=",
                    "clientId": "=",
                    "clusterUserName": "=",
                    "clusterSshUserName": "=",
                    "hostSubscriptionId": "=",
                    "clusterResourceGroup": "=",
                    "subscriptionId": "=",
                    "resourceGroupName": "=",
                    "tenant": "=",
                    "dataLakeStoreUri": "=",
                    "baseUrl": "=",
                    "database": "=",
                    "serviceEndpoint": "=",
                    "batchUri": "=",
                    "poolName": "=",
                    "databaseName": "=",
                    "systemNumber": "=",
                    "server": "=",
                    "url":"=",
                    "aadResourceId": "=",
                    "connectionString": "|:-connectionString:secureString",
                    "existingClusterId": "-"
                }
            }
        },
        "Odbc": {
            "properties": {
                "typeProperties": {
                    "userName": "=",
                    "connectionString": {
                        "secretName": "="
                    }
                }
            }
        }
    },
    "Microsoft.DataFactory/factories/datasets": {
        "*": {
            "properties": {
                "typeProperties": {
                    "folderPath": "=",
                    "fileName": "="
                }
            }
        }}
}
```

## <a name="next-steps"></a>Pasos siguientes

- [Información general de integración y entrega continuas](continuous-integration-delivery.md)
- [Automatización de la integración continua mediante versiones de Azure Pipelines](continuous-integration-delivery-automate-azure-pipelines.md)
- [Promoción manual de una plantilla de Resource Manager para cada entorno](continuous-integration-delivery-manual-promotion.md)
- [Plantillas vinculadas de Resource Manager](continuous-integration-delivery-linked-templates.md)
- [Uso de un entorno de producción de revisión](continuous-integration-delivery-hotfix-environment.md)
- [Script de ejemplo anterior y posterior a la implementación](continuous-integration-delivery-sample-script.md)