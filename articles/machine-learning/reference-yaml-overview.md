---
title: Introducción a los esquemas YAML de la CLI (v2)
titleSuffix: Azure Machine Learning
description: Información general e índice de los esquemas YAML de la CLI (v2).
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: lostmygithubaccount
ms.author: copeters
ms.date: 10/21/2021
ms.reviewer: laobri
ms.openlocfilehash: 541cb9049be64977858a10cd486605f9572b3f30
ms.sourcegitcommit: e41827d894a4aa12cbff62c51393dfc236297e10
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/04/2021
ms.locfileid: "131561354"
---
# <a name="cli-v2-yaml-schemas"></a>Esquemas YAML de la CLI (v2)

La CLI de Azure Machine Learning (v2), una extensión de la CLI de Azure, usa a menudo y a veces requiere archivos YAML con esquemas específicos. En este artículo se enumeran los documentos de referencia y el esquema de origen de los archivos YAML. Hay ejemplos insertados en artículos individuales.

[!INCLUDE [preview disclaimer](../../includes/machine-learning-preview-generic-disclaimer.md)]

## <a name="workspace"></a>Área de trabajo

| Referencia | Identificador URI |
| - | - |
| [Área de trabajo](reference-yaml-workspace.md) | https://azuremlschemas.azureedge.net/latest/workspace.schema.json |

## <a name="environment"></a>Entorno

| Referencia | Identificador URI |
| - | - |
| [Entorno](reference-yaml-environment.md) | https://azuremlschemas.azureedge.net/latest/environment.schema.json |

## <a name="dataset"></a>Dataset

| Referencia | Identificador URI |
| - | - |
| [Dataset](reference-yaml-dataset.md) | https://azuremlschemas.azureedge.net/latest/dataset.schema.json |

## <a name="model"></a>Modelo

| Referencia | Identificador URI |
| - | - |
| [Modelo](reference-yaml-model.md) | https://azuremlschemas.azureedge.net/latest/model.schema.json |

## <a name="compute"></a>Proceso

| Referencia | Identificador URI |
| - | - |
| [Clúster de proceso (AmlCompute)](reference-yaml-compute-aml.md) | https://azuremlschemas.azureedge.net/latest/amlCompute.schema.json |
| [Instancia de proceso](reference-yaml-compute-instance.md) | https://azuremlschemas.azureedge.net/latest/computeInstance.schema.json |
| [Máquina virtual conectada](reference-yaml-compute-vm.md) | https://azuremlschemas.azureedge.net/latest/vmCompute.schema.json |

## <a name="job"></a>Trabajo

| Referencia | Identificador URI |
| - | - |
| [Comando](reference-yaml-job-command.md) | https://azuremlschemas.azureedge.net/latest/commandJob.schema.json |
| [Barrido](reference-yaml-job-sweep.md) | https://azuremlschemas.azureedge.net/latest/sweepJob.schema.json |
| [Canalización](reference-yaml-job-pipeline.md) | https://azuremlschemas.azureedge.net/latest/pipelineJob.schema.json |

## <a name="datastore"></a>Almacén de datos

| Referencia | Identificador URI |
| - | - |
| [Azure Blob](reference-yaml-datastore-blob.md) | https://azuremlschemas.azureedge.net/latest/azureBlob.schema.json |
| [Archivos de Azure](reference-yaml-datastore-files.md) | https://azuremlschemas.azureedge.net/latest/azureFile.schema.json |
| [Azure Data Lake Gen1](reference-yaml-datastore-data-lake-gen1.md) | https://azuremlschemas.azureedge.net/latest/azureDataLakeGen1.schema.json |
| [Azure Data Lake Gen2](reference-yaml-datastore-data-lake-gen2.md) | https://azuremlschemas.azureedge.net/latest/azureDataLakeGen2.schema.json |

## <a name="endpoint"></a>Punto de conexión

| Referencia | Identificador URI |
| - | - |
| [Administrado en línea (en tiempo real)](reference-yaml-endpoint-managed-online.md) | https://azuremlschemas.azureedge.net/latest/managedOnlineEndpoint.schema.json |
| [Batch](reference-yaml-endpoint-batch.md) | https://azuremlschemas.azureedge.net/latest/batchEndpoint.schema.json |

## <a name="deployment"></a>Implementación

| Referencia | Identificador URI |
| - | - |
| [Administrado en línea (en tiempo real)](reference-yaml-deployment-managed-online.md) | https://azuremlschemas.azureedge.net/latest/managedOnlineDeployment.schema.json |
| [Batch](reference-yaml-deployment-batch.md) | https://azuremlschemas.azureedge.net/latest/batchDeployment.schema.json |

## <a name="component"></a>Componente

| Referencia | Identificador URI |
| - | - |
| [Comando](reference-yaml-component-command.md) | https://azuremlschemas.azureedge.net/latest/commandComponent.schema.json |

## <a name="next-steps"></a>Pasos siguientes

- [Instalación y uso de la CLI (v2)](how-to-configure-cli.md)
