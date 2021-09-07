---
title: Referencia de YAML sobre puntos de conexión en línea administrados (versión preliminar)
titleSuffix: Azure Machine Learning
description: Obtenga información sobre los archivos YAML que se usan para implementar modelos como puntos de conexión en línea administrados.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: how-to
author: rsethur
ms.author: seramasu
ms.date: 08/03/2021
ms.reviewer: laobri
ms.openlocfilehash: ebb4db34c802c1cd82e0065579cc97ffeb3fe642
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/13/2021
ms.locfileid: "121780186"
---
# <a name="cli-v2-managed-online-endpoint-yaml-schema"></a>Esquema de YAML de punto de conexión en línea administrado de la CLI (v2)

[!INCLUDE [preview disclaimer](../../includes/machine-learning-preview-generic-disclaimer.md)]

> [!NOTE]
> Como [referencia](https://azuremlschemas.azureedge.net/latest/managedOnlineEndpoint.template.yaml), hay disponible un archivo YAML de ejemplo completamente especificado para los puntos de conexión en línea administrados.

## <a name="schema"></a>Schema

El esquema JSON de origen se puede encontrar en https://azuremlschemas.azureedge.net/latest/managedOnlineEndpoint.schema.json. El esquema se proporciona a continuación en formatos JSON y YAML para mayor comodidad.

# <a name="json"></a>[JSON](#tab/json)

:::code language="json" source="~/azureml-examples-main/cli/.schemas/jsons/latest/managedOnlineEndpoint.schema.json":::

# <a name="yaml"></a>[YAML](#tab/yaml)

:::code language="yaml" source="~/azureml-examples-main/cli/.schemas/yamls/latest/managedOnlineEndpoint.schema.yml":::

---

## <a name="remarks"></a>Comentarios

| Clave | Descripción |
| --- | --- |
| $schema    | \[__Opcional__\] El esquema de YAML. Puede ver el esquema del ejemplo anterior en un explorador para examinar todas las opciones disponibles en el archivo YAML.|
| name       | Nombre del punto de conexión. Es preciso que sea único en el nivel de región de Azure.|
| traffic | Porcentaje de tráfico desde el punto de conexión que se desviará a cada implementación. Los valores de tráfico deben sumar 100. |
| auth_mode | Use `key` para la autenticación basada en claves y `aml_token` para la autenticación basada en tokens de Azure Machine Learning. `key` no expira, pero `aml_token` sí lo hace. Obtenga el token más reciente con el comando `az ml endpoint list-keys`. |
| identidad | Se usa para configurar identidades administradas asignadas por el sistema y por el usuario. |
| app_insights_enabled | `True` para habilitar la integración con Azure AppInsights asociada con el área de trabajo de Azure Machine Learning. `False` es el valor predeterminado.
| etiquetas | Diccionario de etiquetas de Azure que se asociarán al punto de conexión. |
| description | Descripción del punto de conexión. |
| Destino | Si no se define esta clave, el punto de conexión se implementará como un punto de conexión en línea administrado. Para usar AKS, establezca el valor de esta clave en el nombre del destino de proceso registrado, como `target:azureml:my-aks`. 
| deployments | Contiene una lista de implementaciones que se crearán en el punto de conexión. En este caso, solo tenemos una implementación, denominada `blue`. |

### <a name="attributes-of-the-deployments-key"></a>Atributos de la clave `deployments`
 
| Key | Descripción |
| --- | --- |
| name  | Nombre de la implementación. |
| model | Nombre de la versión del modelo registrado con el formato `model: azureml:my-model:1`. También puede especificar propiedades de modelo insertadas: `name`, `version` y `local_path`. Los archivos de modelo se cargarán y registrarán automáticamente. Una desventaja de la especificación en línea es que debe incrementar la versión manualmente si quiere actualizar los archivos del modelo.|
| code_configuration.code.local_path | Directorio que contiene todo el código fuente de Python para puntuar el modelo. Se admiten paquetes y directorios anidados. |
| code_configuration.scoring_script | El archivo de Python del directorio de puntuación anterior. Este código de Python debe tener una función `init()` y una función `run()`. Se llamará a la función `init()` después de crear o actualizar el modelo (puede usarla para almacenar en caché el modelo en memoria, etc.). Se llama a la función `run()` en cada invocación del punto de conexión para realizar la puntuación o predicción reales. |
| Environment | Contiene los detalles del entorno de Azure Machine Learning para hospedar el modelo y el código. Como procedimiento recomendado en producción, debe registrar por separado el modelo y el entorno y especificar el nombre y la versión registrados en el archivo YAML. Por ejemplo, `environment: azureml:my-env:1`. |
| instance_type | La SKU de máquina virtual que hospedará las instancias de implementación. Para más información, consulte las [SKU de máquina virtual compatibles con puntos de conexión en línea administrados](reference-managed-online-endpoints-vm-sku-list.md).|
| scale_settings.scale_type | De momento, este valor debe ser `manual`. Para realizar el escalado o la reducción vertical después de crear el punto de conexión y la implementación, actualice `instance_count` en el archivo YAML y ejecute el comando `az ml endpoint update -n $ENDPOINT_NAME --file <yaml filepath>`. |
| scale_settings.instance_count | Número de instancias de la implementación. Base el valor en la carga de trabajo esperada. Para conseguir alta disponibilidad, Microsoft recomienda establecerlo en al menos `3`. |
| scale_settings.min_instances | Número mínimo de instancias que siempre estarán presentes. |
| scale_settings.max_instances | Número máximo de instancias al que se puede escalar la implementación. La cuota se reservará para max_instances. |
| request_settings.request_timeout_ms | Tiempo de espera de la puntuación, en milisegundos. El valor predeterminado es 5000 para los puntos de conexión en línea administrados. |
| request_settings.max_concurrent_requests_per_instance | Número máximo de solicitudes simultáneas por nodo permitidas por implementación. De manera predeterminada, su valor es 1. __No cambie este valor a menos que se lo indique el soporte técnico de Microsoft o un miembro del equipo de Azure Machine Learning.__ |
| request_settings.max_queue_wait_ms | La cantidad máxima de tiempo que una solicitud permanecerá en la cola (en milisegundos). El valor predeterminado es 500. |
| liveness_probe | El sondeo de ejecución supervisa el estado del contenedor con regularidad. |
| liveness_probe.period | Frecuencia (en segundos) en que se ejecutará el sondeo de ejecución. El valor predeterminado es de 10 segundos. El valor mínimo es 1. |
| liveness_probe.initial_delay | Número de segundos después de que se haya iniciado el contenedor antes de que se inicien los sondeos de ejecución. El valor predeterminado es 10. |
| liveness_probe.timeout | Número de segundos tras los cuales el sondeo de ejecución agota el tiempo de espera. El valor predeterminado es de 2 segundos. El valor mínimo es 1. |
| liveness_probe.failure_threshold | El sistema efectuará failure_threshold intentos antes de abandonar. El valor predeterminado es 30. El valor mínimo es 1. |
| liveness_probe.success_threshold | Número mínimo de valores correctos consecutivos para que el sondeo de ejecución se considere correcto después de que se haya producido un error. De manera predeterminada, su valor es 1. El valor mínimo es 1. |
| readiness_probe | El sondeo de preparación valida si el contenedor está listo para atender el tráfico. Las propiedades y los valores predeterminados son los mismos que para el sondeo de ejecución. |
| etiquetas | Diccionario de etiquetas de Azure que desea asociar a la implementación. |
| description | Una descripción de la implementación. |

## <a name="next-steps"></a>Pasos siguientes

- [Instalación y uso de la CLI (v2)](how-to-configure-cli.md)
- Aprenda a [implementar un modelo con un punto de conexión en línea administrado](how-to-deploy-managed-online-endpoints.md).
- [Solución de problemas de implementación y puntuación de puntos de conexión en línea administrados (versión preliminar)](how-to-troubleshoot-managed-online-endpoints.md)
