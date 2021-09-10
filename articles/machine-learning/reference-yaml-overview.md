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
ms.date: 08/03/2021
ms.reviewer: laobri
ms.openlocfilehash: 4d98ebbebbb25e2ab903e06162114f8fa026fbda
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/13/2021
ms.locfileid: "121781191"
---
# <a name="cli-v2-yaml-schemas"></a>Esquemas YAML de la CLI (v2)

La CLI de Azure Machine Learning (v2), una extensión de la CLI de Azure, usa a menudo y a veces requiere archivos YAML con esquemas específicos. En este artículo se enumeran los documentos de referencia y el esquema de origen de los archivos YAML.

[!INCLUDE [preview disclaimer](../../includes/machine-learning-preview-generic-disclaimer.md)]

## <a name="assets"></a>Recursos

Referencia | Identificador URI
- | -
[Área de trabajo](reference-yaml-workspace.md) | https://azuremlschemas.azureedge.net/latest/workspace.schema.json
[Proceso](reference-yaml-compute.md) | https://azuremlschemas.azureedge.net/latest/compute.schema.json
[Entorno](reference-yaml-environment.md) | https://azuremlschemas.azureedge.net/latest/environment.schema.json
[Dataset](reference-yaml-dataset.md) | https://azuremlschemas.azureedge.net/latest/dataset.schema.json
[Modelo](reference-yaml-model.md) | https://azuremlschemas.azureedge.net/latest/model.schema.json

## <a name="datastores"></a>Almacenes de datos

Referencia | Identificador URI
- | -
[Azure Blob](reference-yaml-datastore-blob.md) | https://azuremlschemas.azureedge.net/latest/azureBlob.schema.json
[Archivos de Azure](reference-yaml-datastore-files.md) | https://azuremlschemas.azureedge.net/latest/azureFile.schema.json
[Azure Data Lake Gen1](reference-yaml-datastore-data-lake-gen1.md) | https://azuremlschemas.azureedge.net/latest/azureDataLakeGen1.schema.json
[Azure Data Lake Gen2](reference-yaml-datastore-data-lake-gen2.md) | https://azuremlschemas.azureedge.net/latest/azureDataLakeGen2.schema.json

## <a name="jobs"></a>Trabajos

Referencia | Identificador URI
- | -
[Comando](reference-yaml-job-command.md) | https://azuremlschemas.azureedge.net/latest/commandJob.schema.json
[Barrido](reference-yaml-job-sweep.md) | https://azuremlschemas.azureedge.net/latest/sweepJob.schema.json

## <a name="endpoints"></a>Puntos de conexión

Referencia | Identificador URI
- | -
[Administrado en línea (en tiempo real)](reference-yaml-endpoint-managed-online.md) | https://azuremlschemas.azureedge.net/latest/managedOnlineEndpoint.schema.json
[Administrado por lotes](reference-yaml-endpoint-managed-batch.md) | https://azuremlschemas.azureedge.net/latest/batchEndpoint.schema.json
[Kubernetes (k8s) en línea (en tiempo real)](reference-yaml-endpoint-k8s-online.md) | https://azuremlschemas.azureedge.net/latest/k8sOnlineEndpoint.schema.json

## <a name="deployments"></a>Implementaciones

Referencia | Identificador URI
- | -
[Administrado en línea (en tiempo real)](reference-yaml-deployment-managed-online.md) | https://azuremlschemas.azureedge.net/latest/managedOnlineDeployment.schema.json
[Administrado por lotes](reference-yaml-deployment-managed-batch.md) | https://azuremlschemas.azureedge.net/latest/batchDeployment.schema.json
[Kubernetes (k8s) en línea (en tiempo real)](reference-yaml-deployment-k8s-online.md) | https://azuremlschemas.azureedge.net/latest/k8sOnelineDeployment.schema.json

## <a name="next-steps"></a>Pasos siguientes

- [Instalación y uso de la CLI (v2)](how-to-configure-cli.md)
