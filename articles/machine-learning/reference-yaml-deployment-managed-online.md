---
title: Esquema YAML de implementación en línea administrada de la CLI (v2)
titleSuffix: Azure Machine Learning
description: Documentación de referencia del esquema YAML de implementación en línea administrada de la CLI (v2).
services: machine-learning
ms.service: machine-learning
ms.subservice: mlops
ms.topic: reference
author: lostmygithubaccount
ms.author: copeters
ms.date: 10/21/2021
ms.reviewer: laobri
ms.openlocfilehash: 82c215dd0b7bccfcb53da0952cba82f89105c38a
ms.sourcegitcommit: 838413a8fc8cd53581973472b7832d87c58e3d5f
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/10/2021
ms.locfileid: "132137842"
---
# <a name="cli-v2-managed-online-deployment-yaml-schema"></a>Esquema YAML de implementación en línea administrada de la CLI (v2)

El esquema JSON de origen se puede encontrar en https://azuremlschemas.azureedge.net/latest/managedOnlineDeployment.schema.json.

[!INCLUDE [preview disclaimer](../../includes/machine-learning-preview-generic-disclaimer.md)]

## <a name="yaml-syntax"></a>Sintaxis de YAML

| Clave | Tipo | Descripción | Valores permitidos | Valor predeterminado |
| --- | ---- | ----------- | -------------- | ------------- |
| `$schema` | string | Esquema de YAML. Si usa la extensión VS Code de Azure Machine Learning para crear el archivo YAML, la inclusión de `$schema` en la parte superior del archivo le permite invocar las finalizaciones del esquema y los recursos. | | |
| `name` | string | **Obligatorio.** Nombre de la implementación. <br><br> Las reglas de nomenclatura se definen [aquí](how-to-manage-quotas.md#azure-machine-learning-managed-online-endpoints-preview).| | |
| `description` | string | Descripción de la implementación. | | |
| `tags` | object | Diccionario de etiquetas para la implementación. | | |
| `endpoint_name` | string | **Obligatorio.** Nombre del punto de conexión en el que se creará la implementación. | | |
| `model` | cadena u objeto | Modelo que se usará para la implementación. Este valor puede ser una referencia a un modelo con versiones existente en el área de trabajo o una especificación de modelo en línea. <br><br> Para hacer referencia a un modelo existente, use la sintaxis `azureml:<model-name>:<model-version>`. <br><br> Para definir un modelo en línea, siga el [Esquema del modelo](reference-yaml-model.md#yaml-syntax). <br><br> Como procedimiento recomendado para escenarios de producción, debe crear el modelo por separado y hacerle referencia aquí. <br><br> Este campo es opcional para escenarios de [implementación de contenedores personalizados](how-to-deploy-custom-container.md).| | |
| `model_mount_path` | string | Ruta de acceso para montar el modelo en un contenedor personalizado. Aplicable solo para escenarios de [implementación de contenedores personalizados](how-to-deploy-custom-container.md). Si se especifica el campo `model`, se monta en esta ruta de acceso en el contenedor. | | |
| `code_configuration` | object | Configuración de la lógica de código de puntuación. <br><br> Este campo es opcional para escenarios de [implementación de contenedores personalizados](how-to-deploy-custom-container.md). | | |
| `code_configuration.code.local_path` | string | Ruta de acceso local al directorio de código fuente para la puntuación del modelo. | | |
| `code_configuration.scoring_script` | string | Ruta de acceso relativa al archivo de puntuación en el directorio de código fuente. | | |
| `environment` | cadena u objeto | **Obligatorio.** El entorno que se usará para la implementación. Este valor puede ser una referencia a un entorno con versiones existente en el área de trabajo o una especificación de entorno en línea. <br><br> Para hacer referencia a un entorno existente, use la sintaxis `azureml:<environment-name>:<environment-version>`. <br><br> Para definir un entorno en línea, siga el [Esquema de entorno](reference-yaml-environment.md#yaml-syntax). <br><br> Como procedimiento recomendado para escenarios de producción, debe crear el entorno por separado y hacerle referencia aquí. | | |
| `instance_type` | string | **Obligatorio.** Tamaño de máquina virtual que se usará para la implementación. Para la lista de tamaños admitidos, consulte [Lista de SKU de puntos de conexión en línea administrados](reference-managed-online-endpoints-vm-sku-list.md). | | |
| `instance_count` | integer | **Obligatorio.** El número de instancias que se usarán para la implementación. Especifique el valor en función de la carga de trabajo que espera. Para conseguir alta disponibilidad, Microsoft recomienda establecerlo en al menos `3`. <br><br> `instance_count` se puede actualizar después de la creación de la implementación mediante el comando `az ml online-deployment update`. | | |
| `app_insights_enabled` | boolean | Si desea habilitar la integración con la instancia de Azure Application Insights asociada al área de trabajo. | | `false` |
| `scale_settings` | object | La configuración de escala para la implementación. Actualmente solo se admite el tipo de escala `default`, por lo que no es necesario especificar esta propiedad. <br><br> Con el tipo de escala `default`, puede 1) escalar y reducir verticalmente de forma manual el recuento de instancias después de la creación de la implementación mediante la actualización de la propiedad `instance_count` o 2) crear una [directiva de escalado automático](). | | |
| `scale_settings.type` | string | El tipo de escala. | `default` | `default` |
| `request_settings` | object | Configuración de la solicitud de puntuación para la implementación. Consulte [RequestSettings](#requestsettings) para el conjunto de propiedades configurables. | | |
| `liveness_probe` | object | Configuración del sondeo de ejecución para supervisar el estado del contenedor con regularidad. Consulte [ProbeSettings](#probesettings) para el conjunto de propiedades configurables. | | |
| `readiness_probe` | object | Configuración del sondeo de preparación para validar si el contenedor está listo para atender el tráfico. Consulte [ProbeSettings](#probesettings) para el conjunto de propiedades configurables. | | |

### <a name="requestsettings"></a>RequestSettings

| Clave | Tipo | Descripción | Valor predeterminado |
| --- | ---- | ----------- | ------------- |
| `request_timeout_ms` | integer | Tiempo de espera de la puntuación, en milisegundos. | `5000` |
| `max_concurrent_requests_per_instance` | integer | El número máximo de solicitudes simultáneas por instancia permitidas para la implementación. <br><br> **No cambie este valor a menos que se lo indique el soporte técnico de Microsoft o un miembro del equipo de Azure Machine Learning.** | `1` |
| `max_queue_wait_ms` | integer | La cantidad máxima de tiempo en milisegundos que una solicitud permanecerá en la cola. | `500` |

### <a name="probesettings"></a>ProbeSettings

| Clave | Tipo | Descripción | Valor predeterminado |
| --- | ---- | ----------- | ------------- |
| `period` | integer | Frecuencia (en segundos) con que se ejecutará el sondeo. | `10` |
| `initial_delay` | integer | El número de segundos después de que se haya iniciado el contenedor y antes de que se inicie el sondeo. El valor mínimo es `1`. | `10` |
| `timeout` | integer | El número de segundos después de los cuales se agota el tiempo de espera del sondeo. El valor mínimo es `1`. | `2` |
| `success_threshold` | integer | El número mínimo de valores correctos consecutivos para que el sondeo se considere correcto después de que se haya producido un error. El valor mínimo es `1`. | `1` |
| `failure_threshold` | integer | Cuando un sondeo genera un error, el sistema efectuará `failure_threshold` intentos antes de abandonar. Si se abandona en el caso del sondeo de ejecución significa reiniciar el contenedor. En el caso del sondeo de preparación, el contenedor se marcará como que no está listo. El valor mínimo es `1`. | `30` |

## <a name="remarks"></a>Observaciones

El comando `az ml online-deployment` se puede usar para administrar implementaciones en línea administradas de Azure Machine Learning.

## <a name="examples"></a>Ejemplos

Hay ejemplos disponibles en el [repositorio de GitHub de ejemplos](https://github.com/Azure/azureml-examples/tree/main/cli/endpoints/online). A continuación se muestran varios.

## <a name="yaml-basic"></a>YAML: básico

:::code language="yaml" source="~/azureml-examples-main/cli/endpoints/online/managed/sample/blue-deployment.yml":::

:::code language="yaml" source="~/azureml-examples-main/cli/endpoints/online/managed/sample/green-deployment.yml":::

## <a name="yaml-system-assigned-identity"></a>YAML: identidad asignada por el sistema

:::code language="yaml" source="~/azureml-examples-main/cli/endpoints/online/managed/managed-identities/2-sai-deployment.yml":::

## <a name="yaml-user-assigned-identity"></a>YAML: identidad asignada por el usuario

:::code language="yaml" source="~/azureml-examples-main/cli/endpoints/online/managed/managed-identities/2-uai-deployment.yml":::

## <a name="next-steps"></a>Pasos siguientes

- [Instalación y uso de la CLI (v2)](how-to-configure-cli.md)
