---
title: Esquema YAML del trabajo de barrido de la CLI (v2)
titleSuffix: Azure Machine Learning
description: Documentación de referencia del esquema YAML del trabajo de barrido de la CLI (v2).
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: lostmygithubaccount
ms.author: copeters
ms.date: 08/03/2021
ms.reviewer: laobri
ms.openlocfilehash: 5c0520b37c98395845ef1adf074e380b264a09d1
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/13/2021
ms.locfileid: "121780572"
---
# <a name="cli-v2-sweep-job-yaml-schema"></a>Esquema YAML del trabajo de barrido de la CLI (v2)

[!INCLUDE [preview disclaimer](../../includes/machine-learning-preview-generic-disclaimer.md)]

## <a name="schema"></a>Schema

El esquema JSON de origen se puede encontrar en https://azuremlschemas.azureedge.net/latest/sweepJob.schema.json. El esquema se proporciona a continuación en formatos JSON y YAML para mayor comodidad.

# <a name="json"></a>[JSON](#tab/json)

:::code language="json" source="~/azureml-examples-main/cli/.schemas/jsons/latest/sweepJob.schema.json":::

# <a name="yaml"></a>[YAML](#tab/yaml)

:::code language="yaml" source="~/azureml-examples-main/cli/.schemas/yamls/latest/sweepJob.schema.yml":::

---

## <a name="remarks"></a>Comentarios

El comando `az ml job` se puede usar para administrar los trabajos de Azure Machine Learning.

## <a name="next-steps"></a>Pasos siguientes

- [Instalación y uso de la CLI (v2)](how-to-configure-cli.md)
