---
title: Instalación y configuración de la CLI (versión 2)
titleSuffix: Azure Machine Learning
description: Aprenda a instalar y configurar la extensión de la CLI de Azure para Machine Learning.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: how-to
author: lostmygithubaccount
ms.author: copeters
ms.date: 05/25/2021
ms.reviewer: laobri
ms.custom: devx-track-azurecli, devplatv2
ms.openlocfilehash: 8034b9e6027f85d9dd5385fda18f81523830944f
ms.sourcegitcommit: 2da83b54b4adce2f9aeeed9f485bb3dbec6b8023
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/24/2021
ms.locfileid: "122772713"
---
# <a name="install-and-set-up-the-cli-v2"></a>Instalación y configuración de la CLI (versión 2)

La extensión `ml` (versión preliminar) para la [CLI de Azure](/cli/azure/) es la interfaz mejorada de Azure Machine Learning. Permite entrenar e implementar modelos desde la línea de comandos, e incluye características que aceleran el escalado vertical y horizontal de la ciencia de datos al tiempo que se hace un seguimiento del ciclo de vida del modelo.

[!INCLUDE [preview disclaimer](../../includes/machine-learning-preview-generic-disclaimer.md)]

## <a name="prerequisites"></a>Requisitos previos

- Para usar la CLI, debe tener una suscripción de Azure. Si no tiene una suscripción de Azure, cree una cuenta gratuita antes de empezar. Pruebe hoy mismo la [versión gratuita o de pago de Azure Machine Learning](https://azure.microsoft.com/free/).
- Para usar los comandos de la CLI de este documento desde su **entorno local**, necesita la [CLI de Azure](/cli/azure/install-azure-cli).

## <a name="installation"></a>Instalación

La nueva extensión para Machine Learning **requiere la versión `>=2.15.0` de la CLI de Azure**. Asegúrese de que se cumple este requisito:

:::code language="azurecli" source="~/azureml-examples-main/cli/misc.sh" id="az_version":::

Si no es así, [actualice la CLI de Azure](/cli/azure/update-azure-cli).

Compruebe las extensiones de la CLI de Azure que ha instalado:

:::code language="azurecli" source="~/azureml-examples-main/cli/misc.sh" id="az_extension_list":::

Asegúrese de que no haya instalada ninguna extensión en conflicto con el espacio de nombres `ml`, incluida la extensión `azure-cli-ml`:

:::code language="azurecli" source="~/azureml-examples-main/cli/misc.sh" id="az_extension_remove":::

Ahora, instale la extensión `ml`:

:::code language="azurecli" source="~/azureml-examples-main/cli/setup.sh" id="az_ml_install":::

Ejecute el comando de ayuda para comprobar la instalación y comprobar los subcomandos disponibles:

:::code language="azurecli" source="~/azureml-examples-main/cli/misc.sh" id="az_ml_verify":::

Puede actualizar la extensión a la versión más reciente:

:::code language="azurecli" source="~/azureml-examples-main/cli/misc.sh" id="az_ml_update":::

### <a name="installation-on-linux"></a>Instalación en Linux

Si usa Linux, la manera más rápida de instalar la versión necesaria de la CLI y la extensión Machine Learning es la siguiente:

:::code language="bash" source="~/azureml-examples-main/cli/misc.sh" id="az_extension_install_linux":::

Para obtener más información, consulte [Instalación de la CLI de Azure para Linux](/cli/azure/install-azure-cli-linux).

## <a name="set-up"></a>Configuración

Inicio de sesión:

:::code language="azurecli" source="~/azureml-examples-main/cli/misc.sh" id="az_login":::

Si tiene acceso a varias suscripciones de Azure, puede establecer la suscripción de Azure.

:::code language="azurecli" source="~/azureml-examples-main/cli/misc.sh" id="az_account_set":::

Si aún no existe, cree el grupo de recursos de Azure:

:::code language="azurecli" source="~/azureml-examples-main/cli/setup.sh" id="az_group_create":::

Los subcomandos de Machine Learning requieren los parámetros `--workspace/-w` y `--resource-group/-g`. Para evitar escribirlos repetidamente, configure los valores predeterminados:

:::code language="azurecli" source="~/azureml-examples-main/cli/setup.sh" id="az_configure_defaults":::

> [!TIP]
> La mayoría de los ejemplos de código suponen que ha establecido un área de trabajo y un grupo de recursos predeterminados. Puede invalidar estos valores en la línea de comandos.

Ahora cree un área de trabajo de aprendizaje automático:

:::code language="azurecli" source="~/azureml-examples-main/cli/setup.sh" id="az_ml_workspace_create":::

## <a name="next-steps"></a>Pasos siguientes

- [Entrenamiento de modelos mediante la extensión de CLI para Machine Learning (versión preliminar)](how-to-train-cli.md)
- [Configuración de la extensión Azure Machine Learning para Visual Studio Code](how-to-setup-vs-code.md)
- [Entrenamiento e implementación de un modelo de TensorFlow de clasificación de imágenes con la extensión Azure Machine Learning para Visual Studio Code (versión preliminar)](tutorial-train-deploy-image-classification-model-vscode.md)
