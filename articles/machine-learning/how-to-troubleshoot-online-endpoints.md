---
title: Solución de problemas de implementación de puntos de conexión en línea (versión preliminar)
titleSuffix: Azure Machine Learning
description: Aprenda a solucionar algunos errores comunes de implementación y puntuación con puntos de conexión en línea.
services: machine-learning
ms.service: machine-learning
ms.subservice: mlops
author: petrodeg
ms.author: petrodeg
ms.reviewer: laobri
ms.date: 11/03/2021
ms.topic: troubleshooting
ms.custom: devplatv2
ms.openlocfilehash: 02f65a5c07536afb1fb20c3f85c444f2376c9b34
ms.sourcegitcommit: 838413a8fc8cd53581973472b7832d87c58e3d5f
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/10/2021
ms.locfileid: "132137578"
---
# <a name="troubleshooting-online-endpoints-deployment-and-scoring-preview"></a>Solución de problemas de implementación y puntuación de puntos de conexión en línea (versión preliminar)

Aprenda a resolver problemas comunes en la implementación y la puntuación de puntos de conexión en línea de Azure Machine Learning (versión preliminar).

Este documento se estructura de la manera en que debe abordar la solución de problemas:

1. Use la [implementación local](#deploy-locally) para probar y depurar los modelos localmente antes de realizar la implementación en la nube.
1. Use los [registros de contenedor](#get-container-logs) para ayudar a depurar problemas.
1. Conozca los [errores de implementación comunes](#common-deployment-errors) que pueden surgir y cómo corregirlos.

En la sección [Códigos de estado HTTP](#http-status-codes) se explica cómo se asignan los errores de invocación y predicción a los códigos de estado HTTP al puntuar puntos de conexión con solicitudes REST.

[!INCLUDE [preview disclaimer](../../includes/machine-learning-preview-generic-disclaimer.md)]

## <a name="prerequisites"></a>Requisitos previos

* Una **suscripción de Azure**. Pruebe la [versión gratuita o de pago de Azure Machine Learning](https://azure.microsoft.com/free/).
* La[CLI de Azure](/cli/azure/install-azure-cli).
* El artículo [Instalación, configuración y uso de la CLI v2 (versión preliminar)](how-to-configure-cli.md).

## <a name="deploy-locally"></a>Implementación local

Se está implementando un modelo en un entorno de Docker local con la implementación local. La implementación local es útil para pruebas y depuración antes de su implementación en la nube.

> [!TIP]
> Use Visual Studio Code para probar y depurar los puntos de conexión localmente. Para más información, consulte [Depuración local de puntos de conexión en línea en Visual Studio Code](how-to-debug-managed-online-endpoints-visual-studio-code.md).

La implementación local admite la creación, la actualización y la eliminación de un punto de conexión local. También permite invocar y obtener registros desde el punto de conexión. Para usar la implementación local, agregue `--local` al comando de la CLI adecuado:

```azurecli
az ml online-deployment create --endpoint-name <endpoint-name> -n <deployment-name> -f <spec_file.yaml> --local
```

Como parte de la implementación local, se llevan a cabo los pasos siguientes:

- Docker compila una nueva imagen de contenedor o extrae una imagen existente de la caché local de Docker. Se usa una imagen existente si hay una que coincida con la parte del entorno del archivo de especificación.
- Docker inicia un nuevo contenedor con artefactos locales montados, como archivos de modelo y código.

Para más información, consulte [Implementación y depuración locales mediante puntos de conexión locales](how-to-deploy-managed-online-endpoints.md#deploy-and-debug-locally-by-using-local-endpoints).

## <a name="get-container-logs"></a>Obtención de registros de contenedor

No se puede obtener acceso directo a la máquina virtual donde está implementado el modelo. Sin embargo, puede obtener registros de algunos de los contenedores que se ejecutan en ella. La cantidad de información depende del estado de aprovisionamiento de la implementación. Si el contenedor especificado está en funcionamiento, verá su salida de la consola; de lo contrario, verá un mensaje para intentarlo de nuevo más tarde.

Para ver la salida del registro del contenedor, use el siguiente comando de la CLI:

```azurecli
az ml online-deployment get-logs -e <endpoint-name> -n <deployment-name> -l 100
```

o

```azurecli
    az ml online-deployment get-logs --endpoint-name <endpoint-name> --name <deployment-name> --lines 100
```

Agregue `--resource-group` y `--workspace-name` a los comandos anteriores si aún no ha establecido estos parámetros a través de `az configure`.

Para ver información sobre cómo establecer estos parámetros y si los valores actuales ya están establecidos, ejecute:

```azurecli
az ml online-deployment get-logs -h
```

De forma predeterminada, los registros se extraen del servidor de inferencia. Los registros incluyen los de la consola del servidor de inferencia, que contiene instrucciones print/log del código "score.py".

> [!NOTE]
> Si usa el registro de Python, asegúrese de utilizar el orden de nivel de registro correcto para los mensajes que se van a publicar en los registros. Por ejemplo, INFO.


También puede obtener registros del contenedor de inicializadores de almacenamiento pasando `–-container storage-initializer`. Estos registros contienen información sobre si el código y los datos del modelo se descargaron correctamente en el contenedor.

Agregue `--help` o `--debug` a los comandos para ver más información. Incluya el encabezado `x-ms-client-request-id` para ayudar a solucionar problemas.

## <a name="common-deployment-errors"></a>Errores de implementación frecuentes

A continuación, se muestra una lista de errores de implementación comunes que se notifican como parte del estado de la operación de implementación.

* [OutOfQuota](#error-outofquota)
* [OutOfCapacity](#error-outofcapacity)
* [BadArgument](#error-badargument)
* [ResourceNotReady](#error-resourcenotready)
* [ResourceNotFound](#error-resourcenotfound)
* [OperationCancelled](#error-operationcancelled)
* [InternalServerError](#error-internalservererror)

### <a name="error-outofquota"></a>ERROR: OutOfQuota

A continuación, se muestra una lista de recursos comunes que podrían quedare sin cuota al usar los servicios de Azure:

* [CPU](#cpu-quota)
* [Asignaciones de roles](#role-assignment-quota)
* [Extremos](#endpoint-quota)
* [Kubernetes](#kubernetes-quota)
* [Otros](#other-quota)

#### <a name="cpu-quota"></a>Cuota de CPU

Antes de implementar un modelo, debe tener suficiente cuota de proceso. Esta cuota define la cantidad de núcleos virtuales disponibles por suscripción, área de trabajo, SKU y región. Cada implementación se resta de la cuota disponible y se vuelve a agregar después de la eliminación, en función del tipo de la SKU.

Una posible mitigación es comprobar si hay implementaciones no utilizadas que se puedan eliminar. O bien, puede enviar una [solicitud de aumento de cuota](how-to-manage-quotas.md#request-quota-increases).

#### <a name="role-assignment-quota"></a>Cuota de asignación de roles

Pruebe a eliminar algunas asignaciones de roles no utilizadas en esta suscripción. Puede consultar todas las asignaciones de roles en Azure Portal, en el menú Control de acceso.

#### <a name="endpoint-quota"></a>Cuota de puntos de conexión

Pruebe a eliminar algunos puntos de conexión que no utilice de esta suscripción.

#### <a name="kubernetes-quota"></a>Cuota de Kubernetes

No se puede satisfacer la solicitud de CPU o memoria. Ajuste la solicitud o el clúster.

#### <a name="other-quota"></a>Otras cuotas

Para ejecutar el archivo `score.py` proporcionado como parte de la implementación, Azure crea un contenedor que incluye todos los recursos que necesita `score.py` y ejecuta el script de puntuación en ese contenedor.

Si el contenedor no se pudo iniciar, significa que no se pudo realizar la puntuación. Podría ser que el contenedor solicite más recursos de los que `instance_type` puede admitir. Si es así, considere la posibilidad de actualizar el valor de `instance_type` de la implementación en línea.

Para conocer el motivo exacto de un error, ejecute: 

```azurecli
az ml online-deployment get-logs -e <endpoint-name> -n <deployment-name> -l 100
```

### <a name="error-outofcapacity"></a>ERROR: OutOfCapacity

The specified VM Size failed to provision due to a lack of Azure Machine Learning capacity. Retry later or try deploying to a different region (ERR_1101: Sin capacidad. El tamaño de máquina virtual especificado no se pudo aprovisionar debido a la falta de capacidad de Azure Machine Learning. Vuelva a intentarlo más tarde o pruebe a realizar la implementación en otra región).

### <a name="error-badargument"></a>ERROR: BadArgument

A continuación, se muestra una lista de los motivos por los que podría encontrarse con este error:

* [La solicitud de recursos era mayor que los límites](#resource-requests-greater-than-limits)
* [No se pueden descargar los recursos](#unable-to-download-resources)

#### <a name="resource-requests-greater-than-limits"></a>Solicitudes de recursos mayores que los límites

Las solicitudes de recursos deben ser inferiores o iguales a los límites. Si no establece límites, al asociar el proceso a un área de trabajo de Azure Machine Learning, se establecerán los valores predeterminados. Puede comprobar los límites en Azure Portal o utilizando el comando `az ml compute show`.

#### <a name="unable-to-download-resources"></a>No se pueden descargar los recursos

Después de aprovisionar el recurso de proceso, durante la creación de la implementación, Azure intenta extraer la imagen del contenedor de usuario del área de trabajo privada de Azure Container Registry (ACR) y montar el modelo de usuario y los artefactos de código en el contenedor de usuario desde la cuenta de almacenamiento del área de trabajo.

En primer lugar, compruebe si hay un problema de permisos al acceder a ACR.

Para extraer blobs, Azure usa [identidades administradas](../active-directory/managed-identities-azure-resources/overview.md) para acceder a la cuenta de almacenamiento.

  - Si ha creado el punto de conexión asociado con SystemAssigned, el permiso de control de acceso basado en rol (RBAC) de Azure se concede automáticamente y no se necesitan más permisos.

  - Si creó el punto de conexión asociado con UserAssigned, la identidad administrada del usuario debe tener permiso de lector de datos de blob de almacenamiento en la cuenta de almacenamiento del área de trabajo.

Durante este proceso, puede encontrarse con algunos problemas diferentes en función de la fase en la que se haya producido el error en la operación:

* [No se puede descargar la imagen del contenedor de usuario](#unable-to-download-user-container-image)
* [No se pueden descargar el modelo de usuario o los artefactos de código](#unable-to-download-user-model-or-code-artifacts)

Para más detalles sobre estos errores, ejecute:

```azurecli
az ml online-deployment get-logs -n <endpoint-name> --deployment <deployment-name> --l 100
``` 

#### <a name="unable-to-download-user-container-image"></a>No se puede descargar la imagen del contenedor de usuario

Es posible que no se pueda encontrar el contenedor de usuario.

Asegúrese de que la imagen de contenedor está disponible en la instancia de ACR del área de trabajo.

Por ejemplo, si la imagen es `testacr.azurecr.io/azureml/azureml_92a029f831ce58d2ed011c3c42d35acb:latest`, compruebe el repositorio con `az acr repository show-tags -n testacr --repository azureml/azureml_92a029f831ce58d2ed011c3c42d35acb --orderby time_desc --output table`.

#### <a name="unable-to-download-user-model-or-code-artifacts"></a>No se pueden descargar el modelo de usuario o los artefactos de código

Es posible que no se puedan encontrar el modelo de usuario o los artefactos de código.

Asegúrese de que estén registrados en la misma área de trabajo que la implementación. Use el comando `show` para mostrar los detalles de un artefacto de modelo o código en un área de trabajo. 

- Por ejemplo: 
  
  ```azurecli
  az ml model show --name <model-name>
  az ml code show --name <code-name> --version <version>
  ```
 
  También puede comprobar si los blobs existen en la cuenta de almacenamiento del área de trabajo.

- Por ejemplo, si el blob es `https://foobar.blob.core.windows.net/210212154504-1517266419/WebUpload/210212154504-1517266419/GaussianNB.pkl`, puede usar este comando para comprobar si existe:

  `az storage blob exists --account-name foobar --container-name 210212154504-1517266419 --name WebUpload/210212154504-1517266419/GaussianNB.pkl --subscription <sub-name>`

### <a name="error-resourcenotready"></a>ERROR: ResourceNotReady

Para ejecutar el archivo `score.py` proporcionado como parte de la implementación, Azure crea un contenedor que incluye todos los recursos que necesita `score.py` y ejecuta el script de puntuación en ese contenedor. El error en este escenario es que este contenedor se bloquea al ejecutarse, lo que significa que no se puede realizar la puntuación. Este error se produce en los casos siguientes:

- Hay un error en `score.py`. Utilice `get-logs` para ayudar a diagnosticar los problemas comunes:
    - Un paquete que se importó pero no está en el entorno de Conda.
    - Especifica un error de sintaxis.
    - Un error en el método `init()`.
- Si `get-logs` no genera ningún registro, normalmente significa que el contenedor no se ha podido iniciar. Para depurar este problema, intente realizar una [implementación local](https://github.com/MicrosoftDocs/azure-docs/blob/master/articles/machine-learning/how-to-troubleshoot-online-endpoints.md#deploy-locally) en su lugar.
- Los sondeos de preparación o ejecución no están configurados correctamente.
- Hay un error en la configuración del entorno del contenedor, como una dependencia que falta.

### <a name="error-resourcenotfound"></a>ERROR: ResourceNotFound

Este error se produce cuando Azure Resource Manager no encuentra un recurso necesario. Por ejemplo, recibirá este error si se hizo referencia a una cuenta de almacenamiento, pero no se encuentra en la ruta de acceso en la que se especificó. Asegúrese de comprobar de nuevo los recursos que podrían haber sido proporcionados con una ruta de acceso exacta o la ortografía de sus nombres.

Para obtener más información, consulte [Solución de errores de recurso no encontrado.](../azure-resource-manager/troubleshooting/error-not-found.md) 

### <a name="error-operationcancelled"></a>ERROR: OperationCancelled

Las operaciones de Azure tienen un determinado nivel de prioridad y se ejecutan de mayor a menor. Este error se produce cuando se ha reemplazado la operación por otra operación que tiene una prioridad más alta. Reintentar la operación podría permitir que se realice sin una cancelación.

### <a name="error-internalservererror"></a>ERROR: InternalServerError

Aunque hacemos todo lo posible para proporcionar un servicio estable y confiable, a veces las cosas no van como se espera. Si recibe este error, significa que algo no funciona por nuestra parte y es necesario corregirlo. Envíe una [incidencia de soporte técnico de cliente](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest) con toda la información relacionada y solucionaremos el problema. 

## <a name="autoscaling-issues"></a>Problemas de escalado automático

Si tiene problemas con el escalado automático, consulte [Solución de problemas de escalado automático de Azure](../azure-monitor/autoscale/autoscale-troubleshoot.md).

## <a name="http-status-codes"></a>Códigos de estado HTTP

Al acceder a los puntos de conexión en línea con solicitudes REST, los códigos de estado devueltos cumplen los estándares de los [códigos de estado HTTP](https://aka.ms/http-status-codes). A continuación, se muestran detalles sobre cómo se asignan los errores de predicción e invocación de puntos de conexión a códigos de estado HTTP.

| status code| Frase de motivo |  Motivo de que se devuelva este código. |
| --- | --- | --- |
| 200 | Aceptar | El modelo se ejecutó correctamente, dentro del límite de latencia. |
| 401 | No autorizado | No tiene permiso para realizar la acción solicitada, como puntuación, o el token ha expirado. |
| 404 | No encontrado | La dirección URL no es correcta. |
| 408 | Tiempo de espera de solicitud | La ejecución del modelo tardó más que el tiempo de espera proporcionado en `request_timeout_ms` en el elemento `request_settings` de la configuración de implementación del modelo.|
| 413 | Carga demasiado grande | La carga de la solicitud es superior a 1,5 megabytes. |
| 424 | Error del modelo | Si el contenedor del modelo devuelve una respuesta distinta de 200, Azure devuelve el error 424. Consulte los encabezados de respuesta `ms-azureml-model-error-statuscode` y `ms-azureml-model-error-reason` para más información. |
| 424 | Carga de respuesta demasiado grande | Si el contenedor devuelve una carga superior a 1,5 megabytes, Azure devuelve el error 424. |
| 429 | Limitación de frecuencia | Intentó enviar más de 100 solicitudes por segundo al punto de conexión. |
| 429 | Demasiadas solicitudes pendientes | El modelo está recibiendo más solicitudes de las que puede manejar. Se permiten 2 * `max_concurrent_requests_per_instance` * `instance_count` solicitudes cada vez. Las solicitudes adicionales se rechazan. Puede confirmar esta configuración en la configuración de implementación del modelo en `request_settings` y `scale_settings`. Si usa la escalabilidad automática, el modelo recibe solicitudes más rápido de lo que el sistema puede escalarse verticalmente. Con la escalabilidad automática, puede intentar volver a enviar solicitudes con [retroceso exponencial](https://aka.ms/exponential-backoff). Si lo hace, puede dar tiempo a que se ajuste el sistema. |
| 500 | Error interno del servidor | Error en la infraestructura aprovisionada por Azure ML. |

## <a name="next-steps"></a>Pasos siguientes

- [Implementación y puntuación de un modelo de aprendizaje automático con un punto de conexión en línea administrado (versión preliminar)](how-to-deploy-managed-online-endpoints.md)
- [Implementación segura para puntos de conexión en línea (versión preliminar)](how-to-safely-rollout-managed-endpoints.md)
- [Referencia de YAML sobre puntos de conexión en línea administrados (versión preliminar)](reference-yaml-endpoint-managed-online.md)

