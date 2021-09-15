---
title: Implementación segura para puntos de conexión en línea
titleSuffix: Azure Machine Learning
description: Implementación de versiones más recientes de modelos de Machine Learning sin interrupciones.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.author: seramasu
ms.reviewer: laobri
author: rsethur
ms.date: 08/05/2021
ms.topic: how-to
ms.custom: how-to, devplatv2
ms.openlocfilehash: 5fe647fba68150f45c450d71f208cf6a53dd0b98
ms.sourcegitcommit: 34aa13ead8299439af8b3fe4d1f0c89bde61a6db
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/18/2021
ms.locfileid: "122419278"
---
# <a name="safe-rollout-for-online-endpoints-preview"></a>Implementación segura para puntos de conexión en línea (versión preliminar)

Tiene un modelo existente implementado en producción y desea implementar una nueva versión del modelo. ¿Cómo puede implementar el nuevo modelo de Machine Learning sin provocar interrupciones? Una buena respuesta es la implementación azul-verde, un método en el que se introduce una nueva versión de un servicio web en producción mediante la implementación del cambio en un pequeño subconjunto de usuarios o solicitudes antes de implementarlo por completo. En este artículo se da por supuesto que usa puntos de conexión en línea; para más información, vea [¿Qué son los puntos de conexión de Azure Machine Learning (versión preliminar)?](concept-endpoints.md)

En este artículo aprenderá lo siguiente:

> [!div class="checklist"]
> * Implementar un nuevo punto de conexión en línea denominado "azul" que sirva la versión 1 del modelo.
> * Escalar esta implementación para que pueda controlar más solicitudes.
> * Implementar la versión 2 del modelo en un punto de conexión denominado "verde" que no acepta tráfico en directo.
> * Probar la implementación verde de forma aislada. 
> * Enviar el 10 % del tráfico en directo a la implementación verde.
> * Migrar completamente todo el tráfico en directo a la implementación verde.
> * Eliminar la implementación azul de la versión 1 que ya no se usa.

[!INCLUDE [preview disclaimer](../../includes/machine-learning-preview-generic-disclaimer.md)]

## <a name="prerequisites"></a>Requisitos previos

* Para usar Azure Machine Learning, debe tener una suscripción a Azure. Si no tiene una suscripción de Azure, cree una cuenta gratuita antes de empezar. Pruebe hoy mismo la [versión gratuita o de pago de Azure Machine Learning](https://azure.microsoft.com/free/).

* Debe instalar y configurar la CLI de Azure y la extensión de Machine Learning. Para más información, consulte [Instalación, configuración y uso de la CLI v2 (versión preliminar)](how-to-configure-cli.md). 

* Debe tener un grupo de recursos de Azure en el que el usuario (o la entidad de servicio que use) tenga acceso de `Contributor`. Tendrá un grupo de recursos de este tipo si configuró la extensión de Machine Learning según el artículo anterior. 

* Debe tener un área de trabajo de Azure Machine Learning. Tendrá este tipo de área de trabajo si configuró la extensión de ML según el artículo anterior.

* Si aún no ha establecido los valores predeterminados de la CLI de Azure, debe guardar la configuración predeterminada. Para evitar tener que pasar repetidamente los valores, ejecute:

```azurecli
az account set --subscription <subscription id>
az configure --defaults workspace=<azureml workspace name> group=<resource group>
```

* Un punto de conexión administrado ya existente. En este artículo se da por supuesto que la implementación es tal y como se describe en [Implementación y puntuación de un modelo de aprendizaje automático con un punto de conexión en línea administrado (versión preliminar)](how-to-deploy-managed-online-endpoints.md).

* Si aún no ha establecido la variable de entorno $ENDPOINT_NAME, hágalo ahora:

:::code language="azurecli" source="~/azureml-examples-main/cli/deploy-declarative-safe-rollout-online-endpoints.sh" ID="set_endpoint_name":::

* (Recomendado) Clone el repositorio de ejemplos y cambie al directorio `cli/` del repositorio: 

```azurecli
git clone https://github.com/Azure/azureml-examples
cd azureml-examples/cli
```

Los comandos de este tutorial están en el archivo `deploy-declarative-safe-rollout-online-endpoints.sh` y los archivos de configuración YAML se encuentran en el subdirectorio `endpoints/online/managed/canary-declarative-flow/`.

## <a name="confirm-your-existing-deployment-is-created"></a>Confirmación de la creación de la implementación existente

Puede ver el estado de la implementación existente si ejecuta: 

```azurecli
az ml endpoint show --name $ENDPOINT_NAME 
```

Debería ver el punto de conexión identificado por `$ENDPOINT_NAME` y una implementación denominada `blue`. 

## <a name="scale-your-existing-deployment-to-handle-more-traffic"></a>Escalado de la implementación existente para controlar más tráfico

En la implementación descrita en [Implementación y puntuación de un modelo de aprendizaje automático con un punto de conexión en línea administrado (versión preliminar)](how-to-deploy-managed-online-endpoints.md), se establece `instance_count` en el valor `1`. Para controlar más tráfico, la segunda versión del archivo YAML (`2-scale-blue.yml`) cambia el valor a `2`:

:::code language="yaml" source="~/azureml-examples-main/cli/endpoints/online/managed/canary-declarative-flow/2-scale-blue.yml" range="29":::

Actualice la implementación:

:::code language="azurecli" source="~/azureml-examples-main/cli/deploy-declarative-safe-rollout-online-endpoints.sh" ID="scale_blue" :::

> [!IMPORTANT]
> La actualización mediante YAML es declarativa. Es decir, los cambios en el archivo YAML se reflejarán en los recursos de Azure Resource Manager subyacentes (puntos de conexión e implementaciones). Este enfoque facilita [GitOps](https://www.atlassian.com/git/tutorials/gitops): *TODOS* los cambios en los puntos de conexión o implementaciones pasan por YAML (incluso `instance_count`). Como efecto secundario, si quita una implementación de YAML y ejecuta `az ml endpoint update` con el archivo, esa implementación se eliminará. 

## <a name="deploy-a-new-model-but-send-it-no-traffic-yet"></a>Implementación de un nuevo modelo sin que aún reciba tráfico

Para implementar el nuevo modelo, agregue una nueva sección a la sección `deployments` del archivo de configuración, pero especifique en la sección `traffic` que debe recibir un 0 % del tráfico. El archivo `3-create-green.yml` incorpora este cambio:

:::code language="yaml" source="~/azureml-examples-main/cli/endpoints/online/managed/canary-declarative-flow/3-create-green.yml" range="7,35-56":::

Actualice la implementación: 

:::code language="azurecli" source="~/azureml-examples-main/cli/deploy-declarative-safe-rollout-online-endpoints.sh" ID="create_green" :::

### <a name="test-the-new-deployment"></a>Prueba de la nueva implementación

La configuración ha especificado que se envíe un 0 % del tráfico a la implementación `green` recién creada. Para probarla, puede invocarla directamente especificando el nombre `--deployment`:

:::code language="azurecli" source="~/azureml-examples-main/cli/deploy-declarative-safe-rollout-online-endpoints.sh" ID="test_green" :::

Si desea usar un cliente REST para invocar la implementación directamente sin pasar por reglas de tráfico, establezca el siguiente encabezado HTTP: `azureml-model-deployment: <deployment-name>`.

## <a name="test-the-new-deployment-with-a-small-percentage-of-live-traffic"></a>Prueba de la nueva implementación con un pequeño porcentaje de tráfico en directo

Una vez que haya probado la implementación `green`, el archivo `4-flight-green.yml` muestra cómo servir un porcentaje del tráfico mediante la modificación de la configuración de `traffic` en el archivo de configuración:

:::code language="yaml" source="~/azureml-examples-main/cli/endpoints/online/managed/canary-declarative-flow/4-flight-green.yml" range="5-7":::

Con la salvedad de las líneas resaltadas, el archivo de configuración no cambia. Actualice la implementación con:

:::code language="azurecli" source="~/azureml-examples-main/cli/deploy-declarative-safe-rollout-online-endpoints.sh" ID="green_10pct_traffic" :::

Ahora, la implementación `green` recibirá el 10 % de las solicitudes. 

## <a name="send-all-traffic-to-your-new-deployment"></a>Envío de todo el tráfico a la nueva implementación

Cuando la implementación `green` le resulte totalmente satisfactoria, cambie todo el tráfico a ella. En el fragmento de código siguiente solo se muestra el código pertinente del archivo de configuración, que no se modifica:

:::code language="yaml" source="~/azureml-examples-main/cli/endpoints/online/managed/canary-declarative-flow/5-full-green.yml" range="5-7":::

Y actualice la implementación: 

:::code language="azurecli" source="~/azureml-examples-main/cli/deploy-declarative-safe-rollout-online-endpoints.sh" ID="green_100pct_traffic" :::

## <a name="remove-the-old-deployment"></a>Eliminación de la implementación anterior

Complete el cambio al nuevo modelo con la eliminación de la implementación `blue` anterior. El archivo de configuración final tiene el siguiente aspecto:

:::code language="yaml" source="~/azureml-examples-main/cli/endpoints/online/managed/canary-declarative-flow/6-delete-blue.yml" :::

Actualice la implementación:

:::code language="azurecli" source="~/azureml-examples-main/cli/deploy-declarative-safe-rollout-online-endpoints.sh" ID="delete_blue" :::

## <a name="delete-the-endpoint-and-deployment"></a>Eliminación del punto de conexión y la implementación

Si no va a usar la implementación, debe eliminarla con:

:::code language="azurecli" source="~/azureml-examples-main/cli/deploy-declarative-safe-rollout-online-endpoints.sh" ID="delete_endpoint" :::


## <a name="next-steps"></a>Pasos siguientes
- [Implementación de modelos con REST (versión preliminar)](how-to-deploy-with-rest.md)
- [Creación y uso de puntos de conexión en línea administrados (versión preliminar) en Estudio](how-to-use-managed-online-endpoint-studio.md)
- [Tutorial: Acceso a recursos de Azure con un punto de conexión en línea administrado e identidad administrada por el sistema (versión preliminar)](tutorial-deploy-managed-endpoints-using-system-managed-identity.md)
- [Supervisión de puntos de conexión en línea administrados (versión preliminar)](how-to-monitor-online-endpoints.md)
- [Administración y aumento de las cuotas de los recursos con Azure Machine Learning](how-to-manage-quotas.md#azure-machine-learning-managed-online-endpoints-preview)
- [Visualización de los costos de un punto de conexión en línea administrado de Azure Machine Learning (versión preliminar)](how-to-view-online-endpoints-costs.md)
- [Lista de SKU de puntos de conexión en línea administrados (versión preliminar)](reference-managed-online-endpoints-vm-sku-list.md)
- [Solución de problemas de implementación y puntuación de puntos de conexión en línea administrados (versión preliminar)](how-to-troubleshoot-managed-online-endpoints.md)
- [Referencia de YAML sobre puntos de conexión en línea administrados (versión preliminar)](reference-yaml-endpoint-managed-online.md)