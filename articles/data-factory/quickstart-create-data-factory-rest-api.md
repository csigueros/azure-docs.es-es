---
title: Creación de una factoría de datos de Azure con la API REST
description: Cree una canalización de Azure Data Factory para copiar los datos de una ubicación de Azure Blob Storage a otra.
author: linda33wj
ms.service: data-factory
ms.subservice: data-movement
ms.devlang: rest-api
ms.topic: quickstart
ms.date: 05/31/2021
ms.author: jingwang
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 1fb0686d975c2342bcd562e75f739d83ee0a51f6
ms.sourcegitcommit: deb5717df5a3c952115e452f206052737366df46
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/23/2021
ms.locfileid: "122681540"
---
# <a name="quickstart-create-an-azure-data-factory-and-pipeline-by-using-the-rest-api"></a>Inicio rápido: Creación de una instancia de Azure Data Factory y una canalización mediante la API de REST

> [!div class="op_single_selector" title1="Seleccione la versión del servicio Data Factory que usa:"]
> * [Versión 1](v1/data-factory-copy-data-from-azure-blob-storage-to-sql-database.md)
> * [Versión actual](quickstart-create-data-factory-rest-api.md)

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

Azure Data Factory es un servicio de integración de datos basado en la nube que le permite crear flujos de trabajo basados en datos en la nube a fin de coordinar y automatizar el movimiento y la transformación de datos. Mediante Azure Data Factory puede crear y programar flujos de trabajo orientados a datos (llamados canalizaciones) que ingieren datos de almacenes de datos dispares, procesan o transforman los datos mediante servicios de proceso, como Azure HDInsight Hadoop, Spark, Azure Data Lake Analytics y Azure Machine Learning, y publican los datos de salida en almacenes de datos, como Azure Synapse Analytics, para que los consuman las aplicaciones de inteligencia empresarial (BI).

En esta guía de inicio rápido se describe cómo usar la API de REST para crear una instancia de Azure Data Factory. La canalización de esta factoría de datos copia los datos de una ubicación a otra en una instancia de Azure Blob Storage.

Si no tiene una suscripción a Azure, cree una cuenta [gratuita](https://azure.microsoft.com/free/) antes de empezar.

## <a name="prerequisites"></a>Requisitos previos

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

* **Suscripción de Azure**. Si no tiene ninguna suscripción, puede crear una [cuenta de evaluación gratuita](https://azure.microsoft.com/pricing/free-trial/).
* **Cuenta de Azure Storage**. El almacenamiento de blobs se puede usar como almacén de datos de **origen** y **receptor**. Si no tiene una cuenta de almacenamiento de Azure, consulte el artículo [Crear una cuenta de almacenamiento](../storage/common/storage-account-create.md) para ver los pasos para su creación.
* Cree un **contenedor de blobs** en Blob Storage, cree una **carpeta** de entrada en el contenedor y cargue algunos archivos en la carpeta. Puede usar herramientas como el [Explorador de Azure Storage](https://azure.microsoft.com/features/storage-explorer/) para conectarse a Azure Blob Storage, crear un contenedor de blobs, cargar el archivo de entrada y comprobar el archivo de salida.
* Instale **Azure PowerShell**. Siga las instrucciones de [Instalación y configuración de Azure PowerShell](/powershell/azure/install-Az-ps). Esta guía de inicio rápido usa PowerShell para invocar llamadas a la API de REST.
* **Cree una aplicación en Azure Active Directory**. Para ello, siga [estas instrucciones](../active-directory/develop/howto-create-service-principal-portal.md#register-an-application-with-azure-ad-and-create-a-service-principal). Tome nota de los valores siguientes; los usará en pasos posteriores:**application ID**, **clientSecrets** y **tenant ID**. Asigne la aplicación al rol "**Colaborador**" en el nivel de suscripción o grupo de recursos.
>[!NOTE]
>   En el caso de nubes soberanas, debe usar los puntos de conexión adecuados específicos de la nube para ActiveDirectoryAuthority y ResourceManagerUrl (BaseUri). Puede usar PowerShell para obtener fácilmente las direcciones URL de las diversas nubes ejecutando "Get-AzEnvironment | Format-List ", que devolverá una lista de puntos de conexión para cada entorno de nube.  
>    
## <a name="set-global-variables"></a>Definición de variables globales

1. Inicie **PowerShell**. Mantenga Azure PowerShell abierto hasta el final de esta guía de inicio rápido. Si lo cierra y vuelve a abrirlo, deberá ejecutar los comandos de nuevo.

    Ejecute el siguiente comando y escriba el nombre de usuario y la contraseña que utiliza para iniciar sesión en Azure Portal:

    ```powershell
    Connect-AzAccount
    ```
    Ejecute el siguiente comando para ver todas las suscripciones de esta cuenta:

    ```powershell
    Get-AzSubscription
    ```
    Ejecute el comando siguiente para seleccionar la suscripción con la que desea trabajar. Reemplace **SubscriptionId** con el identificador de la suscripción de Azure:

    ```powershell
    Select-AzSubscription -SubscriptionId "<SubscriptionId>"
    ```
2. Ejecute los comandos siguientes después de reemplazar los marcadores de posición con sus propios valores para establecer las variables globales que se usará en pasos posteriores.

    ```powershell
    $tenantID = "<your tenant ID>"
    $appId = "<your application ID>"
    $clientSecrets = "<your clientSecrets for the application>"
    $subscriptionId = "<your subscription ID to create the factory>"
    $resourceGroupName = "<your resource group to create the factory>"
    $factoryName = "<specify the name of data factory to create. It must be globally unique.>"
    $apiVersion = "2018-06-01"
    ```

## <a name="authenticate-with-azure-ad"></a>Autenticación mediante Azure Active Directory

Ejecute los comandos siguientes para autenticarse con Azure Active Directory (AAD):

```powershell
$credentials = Get-Credential -UserName $appId
Connect-AzAccount -ServicePrincipal  -Credential $credentials -Tenant $tenantID
```
Se le pedirá que introduzca la contraseña y use el valor de la variable clientSecrets.

Si necesita obtener el token de acceso 

```powershell

GetToken

```

## <a name="create-a-data-factory"></a>Crear una factoría de datos

Ejecute los siguientes comandos para crear una factoría de datos:

```powershell
$body = @"
{
    "location": "East US",
    "properties": {},
    "identity": {
        "type": "SystemAssigned"
    }
}
"@

$response =   Invoke-AzRestMethod -SubscriptionId ${subscriptionId}  -ResourceGroupName ${resourceGroupName} -ResourceProviderName  Microsoft.DataFactory -ResourceType "factories" -Name  ${factoryName} -ApiVersion ${apiVersion} -Method PUT -Payload ${body}
$response.Content  

```

Tenga en cuenta los siguientes puntos:

* El nombre de la instancia de Azure Data Factory debe ser único de forma global. Si recibe el siguiente error, cambie el nombre y vuelva a intentarlo.

    ```
    Data factory name "ADFv2QuickStartDataFactory" is not available.
    ```
* Para una lista de las regiones de Azure en las que Data Factory está disponible actualmente, seleccione las regiones que le interesen en la página siguiente y expanda **Análisis** para poder encontrar **Data Factory**: [Productos disponibles por región](https://azure.microsoft.com/global-infrastructure/services/). Los almacenes de datos (Azure Storage, Azure SQL Database, etc.) y los procesos (HDInsight, etc.) que usa la factoría de datos pueden encontrarse en otras regiones.

Este es el contenido de la respuesta de ejemplo:

```json

{  
    "name":"<dataFactoryName>",
    "identity":{  
        "type":"SystemAssigned",
        "principalId":"<service principal ID>",
        "tenantId":"<tenant ID>"
    },
    "id":"/subscriptions/<subscriptionId>/resourceGroups/<resourceGroupName>/providers/Microsoft.DataFactory/factories/<dataFactoryName>",
    "type":"Microsoft.DataFactory/factories",
    "properties":{  
        "provisioningState":"Succeeded",
        "createTime":"2019-09-03T02:10:27.056273Z",
        "version":"2018-06-01"
    },
    "eTag":"\"0200c876-0000-0100-0000-5d6dcb930000\"",
    "location":"East US",
    "tags":{  

    }
}
```
## <a name="create-linked-services"></a>Crear servicios vinculados

Los servicios vinculados se crean en una factoría de datos para vincular los almacenes de datos y los servicios de proceso con la factoría de datos. En esta guía de inicio rápido, basta con crear un servicio vinculado de Azure Storage como almacén de origen de copia y receptor. En el ejemplo, se denomina "AzureStorageLinkedService".

Ejecute los comandos siguientes para crear un servicio vinculado denominado **AzureStorageLinkedService**:

Reemplace &lt;accountName&gt; y &lt;accountKey&gt; con el nombre y la clave de su cuenta de Azure Storage antes de ejecutar los comandos.

```powershell
$path = "/subscriptions/${subscriptionId}/resourceGroups/${resourceGroupName}/providers/Microsoft.DataFactory/factories/${factoryName}/linkedservices/AzureStorageLinkedService?api-version=${apiVersion}"

$body = @"
{  
    "name":"AzureStorageLinkedService",
    "properties":{  
        "annotations":[  

        ],
        "type":"AzureBlobStorage",
        "typeProperties":{  
            "connectionString":"DefaultEndpointsProtocol=https;AccountName=<accountName>;AccountKey=<accountKey>"
        }
    }
}
"@
$response = Invoke-RestMethod -Method PUT -Uri $request -Header $authHeader -Body $body
$response.content
```

Este es la salida de ejemplo:

```json
{  
    "id":"/subscriptions/<subscriptionId>/resourceGroups/<resourceGroupName>/providers/Microsoft.DataFactory/factories/<dataFactoryName>/linkedservices/AzureStorageLinkedService",
    "name":"AzureStorageLinkedService",
    "type":"Microsoft.DataFactory/factories/linkedservices",
    "properties":{  
        "annotations":[  

        ],
        "type":"AzureBlobStorage",
        "typeProperties":{  
            "connectionString":"DefaultEndpointsProtocol=https;AccountName=<accountName>;"
        }
    },
    "etag":"07011a57-0000-0100-0000-5d6e14a20000"
}
```
## <a name="create-datasets"></a>Creación de conjuntos de datos

Se define un conjunto de datos que representa los datos que se copian de un origen a un receptor. En este ejemplo, va a crear dos conjuntos de datos: InputDataset y OutputDataset. Hacen referencia al servicio vinculado de Azure Storage que creó en la sección anterior. El conjunto de datos de entrada representa los datos de origen en la carpeta de entrada. En la definición del conjunto de datos de entrada, se especifica el contenedor de blobs (adftutorial), la carpeta (input) y el archivo (emp.txt) que contiene los datos de origen. El conjunto de datos de salida representa los datos que se copian en el destino. En la definición del conjunto de datos de salida, se especifica el contenedor de blobs (adftutorial), la carpeta (output) y el archivo en el que se copian los datos.

**Creación de InputDataset**

```powershell

$path = "/subscriptions/${subscriptionId}/resourceGroups/${resourceGroupName}/providers/Microsoft.DataFactory/factories/${factoryName}/datasets/InputDataset?api-version=${apiVersion}"

$body = @"
{  
    "name":"InputDataset",
    "properties":{  
        "linkedServiceName":{  
            "referenceName":"AzureStorageLinkedService",
            "type":"LinkedServiceReference"
        },
        "annotations":[  

        ],
        "type":"Binary",
        "typeProperties":{  
            "location":{  
                "type":"AzureBlobStorageLocation",
                "fileName":"emp.txt",
                "folderPath":"input",
                "container":"adftutorial"
            }
        }
    }
}
"@

$response =  Invoke-AzRestMethod  -Path ${path}  -Method PUT -Payload $body
$response  

```

Este es la salida de ejemplo:

```json
{  
    "id":"/subscriptions/<subscriptionId>/resourceGroups/<resourceGroupName>/providers/Microsoft.DataFactory/factories/<dataFactoryName>/datasets/InputDataset",
    "name":"InputDataset",
    "type":"Microsoft.DataFactory/factories/datasets",
    "properties":{  
        "linkedServiceName":{  
            "referenceName":"AzureStorageLinkedService",
            "type":"LinkedServiceReference"
        },
        "annotations":[  

        ],
        "type":"Binary",
        "typeProperties":{  
            "location":"@{type=AzureBlobStorageLocation; fileName=emp.txt; folderPath=input; container=adftutorial}"
        }
    },
    "etag":"07011c57-0000-0100-0000-5d6e14b40000"
}
```
**Creación de OutputDataset**

```powershell
$path = "/subscriptions/${subscriptionId}/resourceGroups/${resourceGroupName}/providers/Microsoft.DataFactory/factories/${factoryName}/datasets/OutputDataset?api-version=${apiVersion}"

$body = @"
{  
    "name":"OutputDataset",
    "properties":{  
        "linkedServiceName":{  
            "referenceName":"AzureStorageLinkedService",
            "type":"LinkedServiceReference"
        },
        "annotations":[  

        ],
        "type":"Binary",
        "typeProperties":{  
            "location":{  
                "type":"AzureBlobStorageLocation",
                "folderPath":"output",
                "container":"adftutorial"
            }
        }
    }
}
"@

$response =  Invoke-AzRestMethod  -Path ${path}  -Method PUT -Payload $body
$response.content
```

Este es la salida de ejemplo:

```json
{  
    "id":"/subscriptions/<subscriptionId>/resourceGroups/<resourceGroupName>/providers/Microsoft.DataFactory/factories/<dataFactoryName>/datasets/OutputDataset",
    "name":"OutputDataset",
    "type":"Microsoft.DataFactory/factories/datasets",
    "properties":{  
        "linkedServiceName":{  
            "referenceName":"AzureStorageLinkedService",
            "type":"LinkedServiceReference"
        },
        "annotations":[  

        ],
        "type":"Binary",
        "typeProperties":{  
            "location":"@{type=AzureBlobStorageLocation; folderPath=output; container=adftutorial}"
        }
    },
    "etag":"07013257-0000-0100-0000-5d6e18920000"
}
```
## <a name="create-a-pipeline"></a>Crear una canalización

En este ejemplo, esta canalización contiene una actividad de copia. La actividad de copia hace referencia a los conjunto de datos "InputDataset" y "OutputDataset" creados en el paso anterior como entrada y salida.

```powershell
$path = "/subscriptions/${subscriptionId}/resourceGroups/${resourceGroupName}/providers/Microsoft.DataFactory/factories/${factoryName}/pipelines/Adfv2QuickStartPipeline?api-version=${apiVersion}"

$body = @"
{
    "name": "Adfv2QuickStartPipeline",
    "properties": {
        "activities": [
            {
                "name": "CopyFromBlobToBlob",
                "type": "Copy",
                "dependsOn": [],
                "policy": {
                    "timeout": "7.00:00:00",
                    "retry": 0,
                    "retryIntervalInSeconds": 30,
                    "secureOutput": false,
                    "secureInput": false
                },
                "userProperties": [],
                "typeProperties": {
                    "source": {
                        "type": "BinarySource",
                        "storeSettings": {
                            "type": "AzureBlobStorageReadSettings",
                            "recursive": true
                        }
                    },
                    "sink": {
                        "type": "BinarySink",
                        "storeSettings": {
                            "type": "AzureBlobStorageWriteSettings"
                        }
                    },
                    "enableStaging": false
                },
                "inputs": [
                    {
                        "referenceName": "InputDataset",
                        "type": "DatasetReference"
                    }
                ],
                "outputs": [
                    {
                        "referenceName": "OutputDataset",
                        "type": "DatasetReference"
                    }
                ]
            }
        ],
        "annotations": []
    }
}
"@
$response =  Invoke-AzRestMethod  -Path ${path}  -Method PUT -Payload $body
$response.content
```

Este es la salida de ejemplo:

```json
{  
    "id":"/subscriptions/<subscriptionId>/resourceGroups/<resourceGroupName>/providers/Microsoft.DataFactory/factories/<dataFactoryName>/pipelines/Adfv2QuickStartPipeline",
    "name":"Adfv2QuickStartPipeline",
    "type":"Microsoft.DataFactory/factories/pipelines",
    "properties":{  
        "activities":[  
            "@{name=CopyFromBlobToBlob; type=Copy; dependsOn=System.Object[]; policy=; userProperties=System.Object[]; typeProperties=; inputs=System.Object[]; outputs=System.Object[]}"
        ],
        "annotations":[  

        ]
    },
    "etag":"07012057-0000-0100-0000-5d6e14c00000"
}
```

## <a name="create-pipeline-run"></a>Creación de una ejecución de canalización

En este paso, desencadenará una ejecución de canalización. El id. de ejecución de canalización que se devuelve en el cuerpo de la respuesta se usa más adelante en la API de supervisión.

```powershell
$path = "/subscriptions/${subscriptionId}/resourceGroups/${resourceGroupName}/providers/Microsoft.DataFactory/factories/${factoryName}/pipelines/Adfv2QuickStartPipeline/createRun?api-version=${apiVersion}"

$response =  Invoke-AzRestMethod  -Path ${path}  -Method POST 
$response.content 
```

Este es la salida de ejemplo:

```json
{  
    "runId":"04a2bb9a-71ea-4c31-b46e-75276b61bafc"
}
```

También puede obtener este runId con el comando siguiente: 
```powershell

($response.content | ConvertFrom-Json).runId

```

## <a name="parameterize-your-pipeline"></a>Parametrización de la canalización 

Puede crear una canalización con parámetros. En el ejemplo siguiente, crearemos un conjunto de datos de entrada y un conjunto de datos de salida que pueden tomar nombres de archivo de entrada y salida como parámetros dados a la canalización. 


## <a name="create-parameterized-input-dataset"></a>Creación de un conjunto de datos de entrada parametrizados 

Defina un parámetro denominado strInputFileName y úselo como nombre de archivo para el conjunto de datos. 

```powershell

$path = "/subscriptions/${subscriptionId}/resourceGroups/${resourceGroupName}/providers/Microsoft.DataFactory/factories/${factoryName}/datasets/ParamInputDataset?api-version=${apiVersion}"

$body = @"
{
    "name": "ParamInputDataset",
    "properties": {
        "linkedServiceName": {
            "referenceName": "AzureStorageLinkedService",
            "type": "LinkedServiceReference"
        },
        "parameters": {
            "strInputFileName": {
                "type": "string"
            }
        },
        "annotations": [],
        "type": "Binary",
        "typeProperties": {
            "location": {
                "type": "AzureBlobStorageLocation",
                "fileName": {
                    "value": "@dataset().strInputFileName",
                    "type": "Expression"
                },
                "folderPath": "input",
                "container": "adftutorial"
            }
        }
    },
    "type": "Microsoft.DataFactory/factories/datasets"
}
"@

$response =  Invoke-AzRestMethod  -Path ${path}  -Method PUT -Payload $body
$response.content

```

Este es la salida de ejemplo: 

```json
{
    "id": "/subscriptions/<subscriptionId>/resourceGroups/<resourceGroupName>/providers/Microsoft.DataFactory/factories/<factoryName>/datasets/ParamInputDataset",
    "name": "ParamInputDataset",
    "type": "Microsoft.DataFactory/factories/datasets",
    "properties": {
        "linkedServiceName": {
            "referenceName": "AzureStorageLinkedService",
            "type": "LinkedServiceReference"
        },
        "parameters": {
            "strInputFileName": {
                "type": "string"
            }
        },
        "annotations": [],
        "type": "Binary",
        "typeProperties": {
            "location": {
                "type": "AzureBlobStorageLocation",
                "fileName": {
                    "value": "@dataset().strInputFileName",
                    "type": "Expression"
                },
                "folderPath": "input",
                "container": "adftutorial"
            }
        }
    },
    "etag": "00000000-0000-0000-0000-000000000000"
}

```


## <a name="create-parameterized-output-dataset"></a>Creación de un conjunto de datos de salida parametrizados 


Defina un parámetro denominado strOutputFileName y úselo como nombre de archivo para el conjunto de datos. 

```powershell


$path = "/subscriptions/${subscriptionId}/resourceGroups/${resourceGroupName}/providers/Microsoft.DataFactory/factories/${factoryName}/datasets/ParamOutputDataset?api-version=${apiVersion}"
$body = @"
{
    "name": "ParamOutputDataset",
    "properties": {
        "linkedServiceName": {
            "referenceName": "AzureStorageLinkedService",
            "type": "LinkedServiceReference"
        },
        "parameters": {
            "strOutPutFileName": {
                "type": "string"
            }
        },
        "annotations": [],
        "type": "Binary",
        "typeProperties": {
            "location": {
                "type": "AzureBlobStorageLocation",
                "fileName": {
                    "value": "@dataset().strOutPutFileName",
                    "type": "Expression"
                },
                "folderPath": "output",
                "container": "adftutorial"
            }
        }
    },
    "type": "Microsoft.DataFactory/factories/datasets"
}

"@

$response =  Invoke-AzRestMethod  -Path ${path}  -Method PUT -Payload $body
$response.content

```

Este es la salida de ejemplo: 

```json
{
    "id": "/subscriptions/<subscriptionId>/resourceGroups/<resourceGroupName>/providers/Microsoft.DataFactory/factories/<factoryName>/datasets/ParamOutputDataset",
    "name": "ParamOutputDataset",
    "type": "Microsoft.DataFactory/factories/datasets",
    "properties": {
        "linkedServiceName": {
            "referenceName": "AzureStorageLinkedService",
            "type": "LinkedServiceReference"
        },
        "parameters": {
            "strOutPutFileName": {
                "type": "string"
            }
        },
        "annotations": [],
        "type": "Binary",
        "typeProperties": {
            "location": {
                "type": "AzureBlobStorageLocation",
                "fileName": {
                    "value": "@dataset().strOutPutFileName",
                    "type": "Expression"
                },
                "folderPath": "output",
                "container": "adftutorial"
            }
        }
    },
    "etag": "00000000-0000-0000-0000-000000000000"
}
```

## <a name="create-parameterized-pipeline"></a>Creación de una canalización parametrizada 

Defina una canalización con dos parámetros de nivel de canalización: strParamInputFileName y strParamOutputFileName. A continuación, vincule estos dos parámetros a los parámetros strInputFileName y strOutputFileName de los conjuntos de datos. 

```powershell

$path = "/subscriptions/${subscriptionId}/resourceGroups/${resourceGroupName}/providers/Microsoft.DataFactory/factories/${factoryName}/pipelines/Adfv2QuickStartParamPipeline?api-version=${apiVersion}"

$body = @"
{
    "name": "Adfv2QuickStartParamPipeline",
    "properties": {
        "activities": [
            {
                "name": "CopyFromBlobToBlob",
                "type": "Copy",
                "dependsOn": [],
                "policy": {
                    "timeout": "7.00:00:00",
                    "retry": 0,
                    "retryIntervalInSeconds": 30,
                    "secureOutput": false,
                    "secureInput": false
                },
                "userProperties": [],
                "typeProperties": {
                    "source": {
                        "type": "BinarySource",
                        "storeSettings": {
                            "type": "AzureBlobStorageReadSettings",
                            "recursive": true
                        }
                    },
                    "sink": {
                        "type": "BinarySink",
                        "storeSettings": {
                            "type": "AzureBlobStorageWriteSettings"
                        }
                    },
                    "enableStaging": false
                },
                "inputs": [
                    {
                        "referenceName": "ParamInputDataset",
                        "type": "DatasetReference",
                        "parameters": {
                            "strInputFileName": {
                                "value": "@pipeline().parameters.strParamInputFileName",
                                "type": "Expression"
                            }
                        }
                    }
                ],
                "outputs": [
                    {
                        "referenceName": "ParamOutputDataset",
                        "type": "DatasetReference",
                        "parameters": {
                            "strOutPutFileName": {
                                "value": "@pipeline().parameters.strParamOutputFileName",
                                "type": "Expression"
                            }
                        }
                    }
                ]
            }
        ],   

        "parameters": {
            "strParamInputFileName": {
              "type": "String"
            },
            "strParamOutputFileName": {
              "type": "String"
            }
          }
    }
}
"@

$response =  Invoke-AzRestMethod  -Path ${path}  -Method PUT -Payload $body
$response.content


```

Este es la salida de ejemplo: 

```json

{
    "id": "/subscriptions/<subscriptionId>/resourceGroups/<resourceGroupName>/providers/Microsoft.DataFactory/factories/<factoryName>/pipelines/Adfv2QuickStartParamPipeline",
    "name": "Adfv2QuickStartParamPipeline",
    "type": "Microsoft.DataFactory/factories/pipelines",
    "properties": {
        "activities": [
            {
                "name": "CopyFromBlobToBlob",
                "type": "Copy",
                "dependsOn": [],
                "policy": {
                    "timeout": "7.00:00:00",
                    "retry": 0,
                    "retryIntervalInSeconds": 30,
                    "secureOutput": false,
                    "secureInput": false
                },
                "userProperties": [],
                "typeProperties": {
                    "source": {
                        "type": "BinarySource",
                        "storeSettings": {
                            "type": "AzureBlobStorageReadSettings",
                            "recursive": true
                        }
                    },
                    "sink": {
                        "type": "BinarySink",
                        "storeSettings": {
                            "type": "AzureBlobStorageWriteSettings"
                        }
                    },
                    "enableStaging": false
                },
                "inputs": [
                    {
                        "referenceName": "ParamInputDataset",
                        "type": "DatasetReference",
                        "parameters": {
                            "strInputFileName": {
                                "value": "@pipeline().parameters.strParamInputFileName",
                                "type": "Expression"
                            }
                        }
                    }
                ],
                "outputs": [
                    {
                        "referenceName": "ParamOutputDataset",
                        "type": "DatasetReference",
                        "parameters": {
                            "strOutPutFileName": {
                                "value": "@pipeline().parameters.strParamOutputFileName",
                                "type": "Expression"
                            }
                        }
                    }
                ]
            }
        ],
        "parameters": {
            "strParamInputFileName": {
                "type": "String"
            },
            "strParamOutputFileName": {
                "type": "String"
            }
        }
    },
    "etag": "5e01918d-0000-0100-0000-60d569a90000"
}

```

## <a name="create-pipeline-run-with-parameters"></a>Creación de una ejecución de canalización con parámetros 

Ahora puede especificar valores del parámetro en el momento de crear la ejecución de la canalización. 

```powershell

$path = "/subscriptions/${subscriptionId}/resourceGroups/${resourceGroupName}/providers/Microsoft.DataFactory/factories/${factoryName}/pipelines/Adfv2QuickStartParamPipeline/createRun?api-version=${apiVersion}"

$body = @"
{  
        "strParamInputFileName": "emp2.txt",
        "strParamOutputFileName": "aloha.txt"
}
"@

$response =  Invoke-AzRestMethod  -Path ${path}  -Method POST -Payload $body
$response.content
$runId  = ($response.content | ConvertFrom-Json).runId

```
Este es la salida de ejemplo: 

```json
{"runId":"ffc9c2a8-d86a-46d5-9208-28b3551007d8"}
```


## <a name="monitor-pipeline"></a>Supervisión de la canalización

1. Ejecute el script siguiente para comprobar continuamente el estado de ejecución de la canalización hasta que termine de copiar los datos.

    ```powershell
        $path = "/subscriptions/${subscriptionId}/resourceGroups/${resourceGroupName}/providers/Microsoft.DataFactory/factories/${factoryName}/pipelineruns/${runId}?api-version=${apiVersion}"
        
        
        while ($True) {
        
            $response =  Invoke-AzRestMethod  -Path ${path}  -Method GET 
            $response = $response.content | ConvertFrom-Json
        
            Write-Host  "Pipeline run status: " $response.Status -foregroundcolor "Yellow"
        
            if ( ($response.Status -eq "InProgress") -or ($response.Status -eq "Queued") -or ($response.Status -eq "In Progress") ) {
                Start-Sleep -Seconds 10
            }
            else {
                $response | ConvertTo-Json
                break
            }
        }
    ```

    Este es la salida de ejemplo:

    ```json
        {
          "id": "/subscriptions/<subscriptionId>/resourceGroups/<resourceGroupName>/providers/Microsoft.DataFactory/factories/<factoryName>/pipelineruns/ffc9c2a8-d86a-46d5-9208-28b3551007d8",
          "runId": "ffc9c2a8-d86a-46d5-9208-28b3551007d8",
          "debugRunId": null,
          "runGroupId": "ffc9c2a8-d86a-46d5-9208-28b3551007d8",
          "pipelineName": "Adfv2QuickStartParamPipeline",
          "parameters": {
            "strParamInputFileName": "emp2.txt",
            "strParamOutputFileName": "aloha.txt"
          },
          "invokedBy": {
            "id": "9c0275ed99994c18932317a325276544",
            "name": "Manual",
            "invokedByType": "Manual"
          },
          "runStart": "2021-06-25T05:34:06.8424413Z",
          "runEnd": "2021-06-25T05:34:13.2936585Z",
          "durationInMs": 6451,
          "status": "Succeeded",
          "message": "",
          "lastUpdated": "2021-06-25T05:34:13.2936585Z",
          "annotations": [],
          "runDimension": {},
          "isLatest": true
        }
    ```

2. Ejecute el script siguiente para recuperar detalles de la ejecución de la actividad de copia, como el tamaño de los datos leídos o escritos.

    ```powershell
         $path = "/subscriptions/${subscriptionId}/resourceGroups/${resourceGroupName}/providers/Microsoft.DataFactory/factories/${factoryName}/pipelineruns/${runId}/queryActivityruns?api-version=${apiVersion}"
        
        
        while ($True) {
        
            $response =  Invoke-AzRestMethod  -Path ${path}  -Method POST 
            $responseContent = $response.content | ConvertFrom-Json
            $responseContentValue = $responseContent.value
        
            Write-Host  "Activity run status: " $responseContentValue.Status -foregroundcolor "Yellow"
        
            if ( ($responseContentValue.Status -eq "InProgress") -or ($responseContentValue.Status -eq "Queued") -or ($responseContentValue.Status -eq "In Progress") ) {
                Start-Sleep -Seconds 10
            }
            else {
                $responseContentValue | ConvertTo-Json
                break
            }
        }
    ```
    Este es la salida de ejemplo:

    ```json
        {
          "activityRunEnd": "2021-06-25T05:34:11.9536764Z",
          "activityName": "CopyFromBlobToBlob",
          "activityRunStart": "2021-06-25T05:34:07.5161151Z",
          "activityType": "Copy",
          "durationInMs": 4437,
          "retryAttempt": null,
          "error": {
            "errorCode": "",
            "message": "",
            "failureType": "",
            "target": "CopyFromBlobToBlob",
            "details": ""
          },
          "activityRunId": "40bab243-9bbf-4538-9336-b797a2f98e2b",
          "iterationHash": "",
          "input": {
            "source": {
              "type": "BinarySource",
              "storeSettings": "@{type=AzureBlobStorageReadSettings; recursive=True}"
            },
            "sink": {
              "type": "BinarySink",
              "storeSettings": "@{type=AzureBlobStorageWriteSettings}"
            },
            "enableStaging": false
          },
          "linkedServiceName": "",
          "output": {
            "dataRead": 134,
            "dataWritten": 134,
            "filesRead": 1,
            "filesWritten": 1,
            "sourcePeakConnections": 1,
            "sinkPeakConnections": 1,
            "copyDuration": 3,
            "throughput": 0.044,
            "errors": [],
            "effectiveIntegrationRuntime": "DefaultIntegrationRuntime (East US)",
            "usedDataIntegrationUnits": 4,
            "billingReference": {
              "activityType": "DataMovement",
              "billableDuration": ""
            },
            "usedParallelCopies": 1,
            "executionDetails": [
              "@{source=; sink=; status=Succeeded; start=06/25/2021 05:34:07; duration=3; usedDataIntegrationUnits=4; usedParallelCopies=1; profile=; detailedDurations=}"
            ],
            "dataConsistencyVerification": {
              "VerificationResult": "NotVerified"
            },
            "durationInQueue": {
              "integrationRuntimeQueue": 0
            }
          },
          "userProperties": {},
          "pipelineName": "Adfv2QuickStartParamPipeline",
          "pipelineRunId": "ffc9c2a8-d86a-46d5-9208-28b3551007d8",
          "status": "Succeeded",
          "recoveryStatus": "None",
          "integrationRuntimeNames": [
            "defaultintegrationruntime"
          ],
          "executionDetails": {
            "integrationRuntime": [
              "@{name=DefaultIntegrationRuntime; type=Managed; location=East US; nodes=}"
            ]
          },
          "id": "/subscriptions/<subscriptionId>/resourceGroups/<resourceGroupName>/providers/Microsoft.DataFactory/factories/<factoryName>/pipelineruns/ffc9c2a8-d86a-46d5-9208-28b3551007d8/activityruns/40bab243-9bbf-4538-9336-b797a2f98e2b"
        }
    ```
## <a name="verify-the-output"></a>Comprobación del resultado

Use Explorador de Azure Storage para comprobar que el archivo se copia a "outputPath" desde "inputPath", como se especificó al crear la ejecución de canalización.

## <a name="clean-up-resources"></a>Limpieza de recursos
Hay dos forma de eliminar los recursos que ha creado en la guía de inicio rápido. Puede eliminar el [grupo de recursos de Azure](../azure-resource-manager/management/overview.md), lo que incluye todos los recursos del grupo de recursos. Si quiere mantener intacto el resto de recursos, elimine solo la factoría de datos que creó en este tutorial.

Ejecute el comando siguiente para eliminar el grupo de recursos completo:
```powershell
Remove-AzResourceGroup -ResourceGroupName $resourcegroupname
```

Ejecute el comando siguiente para eliminar solo la factoría de datos:

```powershell
Remove-AzDataFactoryV2 -Name "<NameOfYourDataFactory>" -ResourceGroupName "<NameOfResourceGroup>"
```

## <a name="next-steps"></a>Pasos siguientes
La canalización de este ejemplo copia los datos de una ubicación a otra en una instancia de Azure Blob Storage. Consulte los [tutoriales](tutorial-copy-data-dot-net.md) para obtener información acerca del uso de Data Factory en otros escenarios.
