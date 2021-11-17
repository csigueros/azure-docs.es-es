---
title: Acceso a recursos de Azure desde un punto de conexión administrado
titleSuffix: Azure Machine Learning
description: Acceda de forma segura a los recursos de Azure para la implementación de modelo de Machine Learning con un punto de conexión en línea administrado y una identidad administrada asignada por el sistema o por el usuario.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.author: seramasu
ms.reviewer: laobri
author: rsethur
ms.date: 10/21/2021
ms.topic: how-to
ms.custom: devplatv2
ms.openlocfilehash: ff8778dfa69552deaf26dd13438ac73fed260658
ms.sourcegitcommit: e41827d894a4aa12cbff62c51393dfc236297e10
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/04/2021
ms.locfileid: "131565915"
---
# <a name="access-azure-resources-from-a-managed-online-endpoint-preview-with-a-managed-identity"></a>Acceso a recursos de Azure desde un punto de conexión en línea administrado (versión preliminar) con una identidad administrada 

Aprenda a acceder de forma segura a los recursos de Azure desde el script de puntuación con un punto de conexión en línea administrado y una identidad administrada asignada por el sistema o una identidad administrada asignada por el usuario. 

Los puntos de conexión administrados (versión preliminar) permiten a Azure Machine Learning administrar la carga de aprovisionar el recurso de proceso e implementar el modelo de Machine Learning. Normalmente, el modelo necesita acceder a recursos de Azure, como Azure Container Registry o el almacenamiento de blobs para la inferencia. Con una identidad administrada puede acceder a estos recursos sin necesidad de administrar las credenciales en el código. [Obtenga más información sobre las identidades administradas](../active-directory/managed-identities-azure-resources/overview.md).

En esta guía se da por supuesto que no tiene una identidad administrada, una cuenta de almacenamiento o un punto de conexión en línea administrado. Si ya tiene estos componentes, vaya a la sección [Concesión de permiso de acceso a la identidad administrada](#give-access-permission-to-the-managed-identity). 

[!INCLUDE [preview disclaimer](../../includes/machine-learning-preview-generic-disclaimer.md)]

## <a name="prerequisites"></a>Requisitos previos

* Para usar Azure Machine Learning, es preciso tener una suscripción a Azure. Si no tiene una suscripción de Azure, cree una cuenta gratuita antes de empezar. Pruebe hoy mismo la [versión gratuita o de pago de Azure Machine Learning](https://azure.microsoft.com/free/).

* Instale y configure la CLI de Azure y la extensión de Machine Learning. Para más información, consulte [Instalación, configuración y uso de la CLI 2.0 (versión preliminar)](how-to-configure-cli.md).

* Un grupo de recursos de Azure, en el que usted (o la entidad de servicio que use) necesita tener acceso de `User Access Administrator` y `Contributor`. Tendrá un grupo de recursos de este tipo si configuró la extensión de Machine Learning según el artículo anterior.

* Un área de trabajo de Azure Machine Learning. Tendrá un área de trabajo si configuró la extensión de ML según el artículo anterior.

* Un modelo de Machine Learning listo para la puntuación y la implementación. Si sigue junto con el ejemplo, se proporciona un modelo.

* Si aún no ha establecido los valores predeterminados de la CLI de Azure, guarde la configuración predeterminada. Para evitar pasar los valores de la suscripción, el área de trabajo y el grupo de recursos varias veces, ejecute este código:

   ```azurecli
   az account set --subscription <subscription ID>
   az configure --defaults workspace=<Azure Machine Learning workspace name> group=<resource group>
   ```

* Para seguir el ejemplo, clone el repositorio de ejemplos.

    ```azurecli
    git clone https://github.com/Azure/azureml-examples --depth 1
    cd azureml-examples/cli
    ```

## <a name="define-configuration-yaml-file-for-deployment"></a>Definición del archivo de configuración YAML para su implementación

Para implementar un punto de conexión administrado con la CLI, debe definir la configuración en un archivo YAML. Para más información sobre el esquema de YAML, consulte el documento de [referencia de YAML del punto de conexión en línea](reference-yaml-endpoint-managed-online.md).

Los archivos YAML de los ejemplos siguientes se usan para crear puntos de conexión en línea. 

# <a name="system-assigned-managed-identity"></a>[Identidad administrada asignada por el sistema](#tab/system-identity)

El siguiente ejemplo de YAML se encuentra en `endpoints/online/managed/managed-identities/1-sai-create-endpoint`. El archivo 

* Define el nombre por el que se desea hacer referencia al punto de conexión, `my-sai-endpoint`.
* Especifica el tipo de autorización que se usará para acceder al punto de conexión, `auth-mode: key`.

:::code language="yaml" source="~/azureml-examples-cli-preview/cli/endpoints/online/managed/managed-identities/1-sai-create-endpoint.yml":::

Este ejemplo de YAML, `2-sai-deployment.yml`,

* Especifica que el tipo de punto de conexión que desea crear es un punto de conexión `online`.
* Indica que el punto de conexión tiene una implementación asociada llamada `blue`.
* Configura los detalles de la implementación como, por ejemplo, qué modelo se va a implementar y qué entorno y script de puntuación se usarán.

:::code language="yaml" source="~/azureml-examples-cli-preview/cli/endpoints/online/managed/managed-identities/2-sai-deployment.yml":::

# <a name="user-assigned-managed-identity"></a>[Identidad administrada asignada por el usuario](#tab/user-identity)

El siguiente ejemplo de YAML se encuentra en `endpoints/online/managed/managed-identities/1-uai-create-endpoint`. El archivo 

* Define el nombre por el que se desea hacer referencia al punto de conexión, `my-uai-endpoint`.
* Especifica el tipo de autorización que se usará para acceder al punto de conexión, `auth-mode: key`.
* Indica el tipo de identidad que se usará, `type: user_assigned`.

:::code language="yaml" source="~/azureml-examples-cli-preview/cli/endpoints/online/managed/managed-identities/1-uai-create-endpoint.yml":::

Este ejemplo de YAML, `2-sai-deployment.yml`,

* Especifica que el tipo de punto de conexión que desea crear es un punto de conexión `online`.
* Indica que el punto de conexión tiene una implementación asociada llamada `blue`.
* Configura los detalles de la implementación como, por ejemplo, qué modelo se va a implementar y qué entorno y script de puntuación se usarán.

:::code language="yaml" source="~/azureml-examples-cli-preview/cli/endpoints/online/managed/managed-identities/2-uai-deployment.yml":::

---

## <a name="configure-variables-for-deployment"></a>Configuración de variables para la implementación

Configure los nombres de las variables para el área de trabajo, la ubicación del área de trabajo y el punto de conexión que desea crear para usar con la implementación.

# <a name="system-assigned-managed-identity"></a>[Identidad administrada asignada por el sistema](#tab/system-identity)

El código siguiente exporta estos valores como variables de entorno en el punto de conexión:

::: code language="azurecli" source="~/azureml-examples-cli-preview/cli/deploy-managed-online-endpoint-access-resource-sai.sh" id="set_variables" :::

A continuación, especifique el nombre que desea para la cuenta de almacenamiento de blobs, el contenedor de blobs y el archivo. Estos nombres de variable se definen aquí y se hace referencia a ellos en los comandos `az storage account create` y `az storage container create` de la sección siguiente.

El código siguiente exporta esos valores como variables de entorno:

::: code language="azurecli" source="~/azureml-examples-cli-preview/cli/deploy-managed-online-endpoint-access-resource-sai.sh" id="configure_storage_names" :::

Una vez exportadas estas variables, cree un archivo de texto localmente. Cuando se implementa el punto de conexión, el script de puntuación accederá a este archivo de texto mediante la identidad administrada asignada por el sistema que se genera tras la creación del punto de conexión.

# <a name="user-assigned-managed-identity"></a>[Identidad administrada asignada por el usuario](#tab/user-identity)

Decida el nombre del punto de conexión, el área de trabajo y la ubicación del área de trabajo, y exporte ese valor como una variable de entorno:

::: code language="azurecli" source="~/azureml-examples-cli-preview/cli/deploy-managed-online-endpoint-access-resource-uai.sh" id="set_variables" :::

A continuación, especifique el nombre que desea para la cuenta de almacenamiento de blobs, el contenedor de blobs y el archivo. Estos nombres de variable se definen aquí y se hace referencia a ellos en los comandos `az storage account create` y `az storage container create` de la sección siguiente.

::: code language="azurecli" source="~/azureml-examples-cli-preview/cli/deploy-managed-online-endpoint-access-resource-uai.sh" id="configure_storage_names" :::

Una vez exportadas estas variables, cree un archivo de texto localmente. Cuando se implementa el punto de conexión, el script de puntuación accederá a este archivo de texto mediante la identidad administrada asignada por el usuario usada en el punto de conexión. 

Decida el nombre de su identidad de usuario y exporte ese valor como una variable de entorno:

::: code language="azurecli" source="~/azureml-examples-cli-preview/cli/deploy-managed-online-endpoint-access-resource-uai.sh" id="set_user_identity_name" :::

---

## <a name="create-the-managed-identity"></a>Creación de la identidad administrada 
Para acceder a los recursos de Azure, cree una identidad administrada asignada por el sistema o asignada por el usuario para el punto de conexión. 

# <a name="system-assigned-managed-identity"></a>[Identidad administrada asignada por el sistema](#tab/system-identity)

Cuando se [crea un punto de conexión en línea](#create-a-managed-online-endpoint), se genera automáticamente una identidad administrada asignada por el sistema, por lo que no es necesario crear una independiente. 

# <a name="user-assigned-managed-identity"></a>[Identidad administrada asignada por el usuario](#tab/user-identity)

Para crear una identidad administrada asignada por el usuario, use lo siguiente:

::: code language="azurecli" source="~/azureml-examples-cli-preview/cli/deploy-managed-online-endpoint-access-resource-uai.sh" id="create_user_identity" :::

---

## <a name="create-storage-account-and-container"></a>Creación de una cuenta de almacenamiento y un contenedor

En este ejemplo, creará una cuenta de almacenamiento de blobs y un contenedor de blobs y, a continuación, cargará el archivo de texto creado anteriormente en el contenedor de blobs. Se trata de la cuenta de almacenamiento y el contenedor de blobs a los que va a proporcionar acceso al punto de conexión y a la identidad administrada. 

# <a name="system-assigned-managed-identity"></a>[Identidad administrada asignada por el sistema](#tab/system-identity)

Primero, cree una cuenta de almacenamiento.

::: code language="azurecli" source="~/azureml-examples-cli-preview/cli/deploy-managed-online-endpoint-access-resource-sai.sh" id="create_storage_account" :::

A continuación, cree el contenedor de blobs en la cuenta de almacenamiento.

::: code language="azurecli" source="~/azureml-examples-cli-preview/cli/deploy-managed-online-endpoint-access-resource-sai.sh" id="create_storage_container" :::

Después, cargue el archivo de texto en el contenedor de blobs.

::: code language="azurecli" source="~/azureml-examples-cli-preview/cli/deploy-managed-online-endpoint-access-resource-sai.sh" id="upload_file_to_storage" :::

# <a name="user-assigned-managed-identity"></a>[Identidad administrada asignada por el usuario](#tab/user-identity)

Primero, cree una cuenta de almacenamiento.  

::: code language="azurecli" source="~/azureml-examples-cli-preview/cli/deploy-managed-online-endpoint-access-resource-uai.sh" id="create_storage_account" :::

También puede recuperar un identificador de cuenta de almacenamiento existente con lo siguiente. 

::: code language="azurecli" source="~/azureml-examples-cli-preview/cli/deploy-managed-online-endpoint-access-resource-uai.sh" id="get_storage_account_id" :::

A continuación, cree el contenedor de blobs en la cuenta de almacenamiento. 

::: code language="azurecli" source="~/azureml-examples-cli-preview/cli/deploy-managed-online-endpoint-access-resource-uai.sh" id="create_storage_container" :::

A continuación, cargue el archivo en el contenedor. 

::: code language="azurecli" source="~/azureml-examples-cli-preview/cli/deploy-managed-online-endpoint-access-resource-uai.sh" id="upload_file_to_storage" :::

---

## <a name="create-a-managed-online-endpoint"></a>Creación de un punto de conexión en línea administrado

El código siguiente crea un punto de conexión en línea administrado sin especificar una implementación. 

# <a name="system-assigned-managed-identity"></a>[Identidad administrada asignada por el sistema](#tab/system-identity)
Al crear un punto de conexión administrado, se crea de forma predeterminada una identidad administrada asignada por el sistema para el punto de conexión.

>[!IMPORTANT]
> Las identidades administradas asignadas por el sistema son inmutables y no se pueden cambiar una vez creadas.

::: code language="azurecli" source="~/azureml-examples-cli-preview/cli/deploy-managed-online-endpoint-access-resource-sai.sh" id="create_endpoint" :::

Compruebe el estado del punto de conexión con lo siguiente.

::: code language="azurecli" source="~/azureml-examples-cli-preview/cli/deploy-managed-online-endpoint-access-resource-sai.sh" id="check_endpoint_Status" :::

Si experimenta algún problema, consulte [Solución de problemas de implementación y puntuación de puntos de conexión en línea administrados (versión preliminar)](how-to-troubleshoot-managed-online-endpoints.md).

# <a name="user-assigned-managed-identity"></a>[Identidad administrada asignada por el usuario](#tab/user-identity)

::: code language="azurecli" source="~/azureml-examples-cli-preview/cli/deploy-managed-online-endpoint-access-resource-uai.sh" id="create_endpoint" :::

Compruebe el estado del punto de conexión con lo siguiente.

::: code language="azurecli" source="~/azureml-examples-cli-preview/cli/deploy-managed-online-endpoint-access-resource-uai.sh" id="check_endpoint_Status" :::

Si experimenta algún problema, consulte [Solución de problemas de implementación y puntuación de puntos de conexión en línea administrados (versión preliminar)](how-to-troubleshoot-managed-online-endpoints.md).

---

## <a name="give-access-permission-to-the-managed-identity"></a>Concesión de permiso a la identidad administrada

>[!IMPORTANT] 
> Los puntos de conexión en línea requieren permiso de extracción de Azure Container Registry y permiso de AcrPull en el registro de contenedor, y permiso de lector de datos de Storage Blob en el almacén de datos predeterminado del área de trabajo.

Puede permitir que el permiso de punto de conexión administrado acceda al almacenamiento a través de su identidad administrada asignada por el sistema o conceder permiso a la identidad administrada asignada por el usuario para acceder a la cuenta de almacenamiento creada en la sección anterior.

# <a name="system-assigned-managed-identity"></a>[Identidad administrada asignada por el sistema](#tab/system-identity)

Recupere la identidad administrada asignada por el sistema que se creó para el punto de conexión.

::: code language="azurecli" source="~/azureml-examples-cli-preview/cli/deploy-managed-online-endpoint-access-resource-sai.sh" id="get_system_identity" :::

Desde aquí, puede conceder a la identidad administrada asignada por el sistema permiso para acceder al almacenamiento.

::: code language="azurecli" source="~/azureml-examples-cli-preview/cli/deploy-managed-online-endpoint-access-resource-sai.sh" id="give_permission_to_user_storage_account" :::

# <a name="user-assigned-managed-identity"></a>[Identidad administrada asignada por el usuario](#tab/user-identity)

Recupere el identificador de cliente de la identidad administrada asignada por el usuario.

::: code language="azurecli" source="~/azureml-examples-cli-preview/cli/deploy-managed-online-endpoint-access-resource-uai.sh" id="get_user_identity_client_id" :::

Recupere el identificador de la identidad administrada asignada por el usuario.

::: code language="azurecli" source="~/azureml-examples-cli-preview/cli/deploy-managed-online-endpoint-access-resource-uai.sh" id="get_user_identity_id" :::

Obtenga el registro de contenedor asociado al área de trabajo.

::: code language="azurecli" source="~/azureml-examples-cli-preview/cli/deploy-managed-online-endpoint-access-resource-uai.sh" id="get_container_registry_id" :::

Recupere el almacenamiento predeterminado del área de trabajo.

::: code language="azurecli" source="~/azureml-examples-cli-preview/cli/deploy-managed-online-endpoint-access-resource-uai.sh" id="get_workspace_storage_id" :::

Conceda permiso de cuenta de almacenamiento a la identidad administrada asignada por el usuario.  

::: code language="azurecli" source="~/azureml-examples-cli-preview/cli/deploy-managed-online-endpoint-access-resource-uai.sh" id="give_permission_to_user_storage_account" :::

Conceda permiso de registro de contenedor a la identidad administrada asignada por el usuario.

::: code language="azurecli" source="~/azureml-examples-cli-preview/cli/deploy-managed-online-endpoint-access-resource-uai.sh" id="give_permission_to_container_registry" :::

Conceda permiso de almacenamiento de área de trabajo predeterminado a la identidad administrada asignada por el usuario.

::: code language="azurecli" source="~/azureml-examples-cli-preview/cli/deploy-managed-online-endpoint-access-resource-uai.sh" id="give_permission_to_workspace_storage_account" :::

---

## <a name="scoring-script-to-access-azure-resource"></a>Script de puntuación para acceder al recurso de Azure

Consulte el siguiente script para saber cómo usar el token de identidad para acceder a los recursos de Azure, en este escenario, la cuenta de almacenamiento creada en las secciones anteriores. 

:::code language="python" source="~/azureml-examples-cli-preview/cli/endpoints/online/model-1/onlinescoring/score_managedidentity.py":::

## <a name="create-a-deployment-with-your-configuration"></a>Creación de una implementación con la configuración

Cree una implementación asociada al punto de conexión administrado. [Obtenga más información sobre la implementación en puntos de conexión en línea administrados](how-to-deploy-managed-online-endpoints.md).

>[!WARNING]
> Esta implementación puede tardar aproximadamente entre 8 y 14 minutos, dependiendo de si el entorno o la imagen subyacentes se están creando por primera vez. Las implementaciones subsiguientes que usen el mismo entorno serán más rápidas.

# <a name="system-assigned-managed-identity"></a>[Identidad administrada asignada por el sistema](#tab/system-identity)

::: code language="azurecli" source="~/azureml-examples-cli-preview/cli/deploy-managed-online-endpoint-access-resource-sai.sh" id="deploy" :::

>[!NOTE]
> El valor del argumento `--name` puede invalidar la clave `name` dentro del archivo YAML.

Compruebe el estado de la implementación.

::: code language="azurecli" source="~/azureml-examples-cli-preview/cli/deploy-managed-online-endpoint-access-resource-sai.sh" id="check_deploy_Status" :::

Para refinar la consulta anterior para que solo devuelva datos específicos, consulte [Resultados de los comandos de consulta de la CLI de Azure](/cli/azure/query-azure-cli).

> [!NOTE]
> El método init del script de puntuación lee el archivo de la cuenta de almacenamiento mediante el token de identidad administrada asignado por el sistema.

Para comprobar la salida del método init, consulte el registro de implementación con el código siguiente. 

::: code language="azurecli" source="~/azureml-examples-cli-preview/cli/deploy-managed-online-endpoint-access-resource-sai.sh" id="check_deployment_log" :::


# <a name="user-assigned-managed-identity"></a>[Identidad administrada asignada por el usuario](#tab/user-identity)

::: code language="azurecli" source="~/azureml-examples-cli-preview/cli/deploy-managed-online-endpoint-access-resource-uai.sh" id="create_endpoint" :::

>[!Note]
> El valor del argumento `--name` puede invalidar la clave `name` dentro del archivo YAML.

Una vez que se ejecuta el comando, puede comprobar el estado de la implementación.

::: code language="azurecli" source="~/azureml-examples-cli-preview/cli/deploy-managed-online-endpoint-access-resource-uai.sh" id="check_endpoint_Status" :::

Para refinar la consulta anterior para que solo devuelva datos específicos, consulte [Resultados de los comandos de consulta de la CLI de Azure](/cli/azure/query-azure-cli).

::: code language="azurecli" source="~/azureml-examples-cli-preview/cli/deploy-managed-online-endpoint-access-resource-uai.sh" id="check_deployment_log" :::

> [!NOTE]
> El método init del script de puntuación lee el archivo de la cuenta de almacenamiento mediante el token de identidad administrada asignado por el sistema.

Para comprobar la salida del método init, consulte el registro de implementación con el código siguiente. 

::: code language="azurecli" source="~/azureml-examples-cli-preview/cli/deploy-managed-online-endpoint-access-resource-uai.sh" id="check_deployment_log" :::

---

Una vez completada la implementación, el modelo, el entorno y el punto de conexión se registran en el área de trabajo de Azure Machine Learning.

## <a name="confirm-your-endpoint-deployed-successfully"></a>Confirmación de que el punto de conexión se implementó correctamente

Una vez implementado el punto de conexión, confirme que funciona correctamente. Los detalles de la inferencia varían de un modelo a otro. En esta guía, los parámetros de la consulta JSON son parecidos a los siguientes: 

:::code language="json" source="~/azureml-examples-cli-preview/cli/endpoints/online/model-1/sample-request.json" :::

Para llamar al punto de conexión, ejecute:

# <a name="system-assigned-managed-identity"></a>[Identidad administrada asignada por el sistema](#tab/system-identity)

::: code language="azurecli" source="~/azureml-examples-cli-preview/cli/deploy-managed-online-endpoint-access-resource-sai.sh" id="test_endpoint" :::

# <a name="user-assigned-managed-identity"></a>[Identidad administrada asignada por el usuario](#tab/user-identity)

::: code language="azurecli" source="~/azureml-examples-cli-preview/cli/deploy-managed-online-endpoint-access-resource-uai.sh" id="test_endpoint" :::

---

## <a name="delete-the-endpoint-and-storage-account"></a>Eliminación del punto de conexión y la cuenta de almacenamiento

Si no tiene previsto seguir usando el punto de conexión y el almacenamiento implementados, elimínelos para reducir los costos. Al eliminar el punto de conexión, también se eliminan todas sus implementaciones asociadas.

# <a name="system-assigned-managed-identity"></a>[Identidad administrada asignada por el sistema](#tab/system-identity)
 
::: code language="azurecli" source="~/azureml-examples-cli-preview/cli/deploy-managed-online-endpoint-access-resource-sai.sh" id="delete_endpoint" :::
::: code language="azurecli" source="~/azureml-examples-cli-preview/cli/deploy-managed-online-endpoint-access-resource-sai.sh" id="delete_storage_account" :::

# <a name="user-assigned-managed-identity"></a>[Identidad administrada asignada por el usuario](#tab/user-identity)

::: code language="azurecli" source="~/azureml-examples-cli-preview/cli/deploy-managed-online-endpoint-access-resource-uai.sh" id="delete_endpoint" :::
::: code language="azurecli" source="~/azureml-examples-cli-preview/cli/deploy-managed-online-endpoint-access-resource-uai.sh" id="delete_storage_account" :::
::: code language="azurecli" source="~/azureml-examples-cli-preview/cli/deploy-managed-online-endpoint-access-resource-uai.sh" id="delete_user_identity" :::

---

## <a name="next-steps"></a>Pasos siguientes

* [Implementación y puntuación de un modelo de Machine Learning con un punto de conexión en línea administrado (versión preliminar)](how-to-deploy-managed-online-endpoints.md).
* Para más información sobre una implementación, consulte [Implementación segura para puntos de conexión en línea (versión preliminar)](how-to-safely-rollout-managed-endpoints.md).
* Para más información sobre cómo usar la CLI, consulte [Uso de la extensión de la CLI para Azure Machine Learning](reference-azure-machine-learning-cli.md).
* Para ver qué recursos de proceso puede usar, consulte [Lista de SKU de puntos de conexión en línea administrados (versión preliminar)](reference-managed-online-endpoints-vm-sku-list.md).
* Para más información sobre los costos, consulte [Visualización de los costos de un punto de conexión en línea administrado de Azure Machine Learning (versión preliminar)](how-to-view-online-endpoints-costs.md).
* Para más información sobre la supervisión de puntos de conexión, consulte [Supervisión de puntos de conexión en línea administrados (versión preliminar)](how-to-monitor-online-endpoints.md).
* Para más información sobre los límites relacionados con los puntos de conexión administrados, consulte [Administración y aumento de las cuotas de recursos con Azure Machine Learning](how-to-manage-quotas.md#azure-machine-learning-managed-online-endpoints-preview).
