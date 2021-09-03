---
title: Esquema YAML del almacén de datos de Azure Data Lake Gen1 de la CLI (v2)
titleSuffix: Azure Machine Learning
description: Documentación de referencia para el esquema YAML del almacén de datos de Azure Data Lake Gen1 de la CLI (v2).
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: lostmygithubaccount
ms.author: copeters
ms.date: 08/03/2021
ms.reviewer: laobri
ms.openlocfilehash: 58b8f0ac3cbdb69d828544cfaf1d095c7784e966
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/13/2021
ms.locfileid: "121781336"
---
# <a name="cli-v2-azure-data-lake-gen1-yaml-schema"></a>Esquema YAML de Azure Data Lake Gen1 de la CLI (v2)

[!INCLUDE [preview disclaimer](../../includes/machine-learning-preview-generic-disclaimer.md)]

## <a name="schema"></a>Schema

El esquema JSON de origen se puede encontrar en https://azuremlschemas.azureedge.net/latest/azureDataLakeGen1.schema.json. El esquema se proporciona a continuación en formatos JSON y YAML para mayor comodidad.

# <a name="json"></a>[JSON](#tab/json)

:::code language="json" source="~/azureml-examples-main/cli/.schemas/jsons/latest/azureDataLakeGen1.schema.json":::

# <a name="yaml"></a>[YAML](#tab/yaml)

:::code language="yaml" source="~/azureml-examples-main/cli/.schemas/yamls/latest/azureDataLakeGen1.schema.yml":::

---

## <a name="remarks"></a>Comentarios

El comando `az ml datastore` se puede usar para administrar los almacenes de datos de Azure Machine Learning.

## <a name="next-steps"></a>Pasos siguientes

- [Instalación y uso de la CLI (v2)](how-to-configure-cli.md)
