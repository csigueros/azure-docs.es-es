---
title: Seguridad de una aplicación en la versión preliminar de Azure Container Apps
description: Aprenda a proteger las aplicaciones en Azure Container Apps.
services: app-service
author: craigshoemaker
ms.service: app-service
ms.topic: how-to
ms.date: 11/02/2021
ms.author: cshoe
ms.custom: ignite-fall-2021
ms.openlocfilehash: 6c5fe92da4eb891ab1d5b264ff1585730352db36
ms.sourcegitcommit: 591ffa464618b8bb3c6caec49a0aa9c91aa5e882
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/06/2021
ms.locfileid: "131894172"
---
# <a name="secure-an-app-in-azure-container-apps-preview"></a>Seguridad de una aplicación en la versión preliminar de Azure Container Apps

Azure Container Apps permite que la aplicación almacene de forma segura valores de configuración confidenciales. Una vez definidos en el nivel de aplicación, los valores protegidos están disponibles para los contenedores, en función de las reglas de escalado y a través de Dapr.

- Los secretos están limitados a una aplicación, fuera de cualquier revisión específica de una aplicación.
- Agregar, quitar o cambiar secretos no genera nuevas revisiones.
- Cada revisión de aplicación puede hacer referencia a uno o varios secretos.
- Varias revisiones pueden hacer referencia a los mismos secretos.

Cuando se actualiza o elimina un secreto, puede responder a los cambios de una de estas dos maneras:

 1. Implementación de una nueva revisión.
 2. Reinicio de una revisión existente.

Un secreto actualizado o eliminado no vuelve a iniciar automáticamente una revisión.

- Antes de eliminar un secreto, implemente una nueva revisión que ya no haga referencia al secreto anterior.
- Si cambia un valor secreto, deberá reiniciar la revisión para consumir el nuevo valor.

## <a name="defining-secrets"></a>Definición de secretos

# <a name="arm-template"></a>[Plantilla ARM](#tab/arm-template)

Los secretos se definen a nivel de aplicación en la sección `resources.properties.configuration.secrets`.

```json
"resources": [
{
    ...
    "properties": {
        "configuration": {
            "secrets": [
            {
                "name": "queue-connection-string",
                "value": "<MY-CONNECTION-STRING-VALUE>"
            }],
        }
    }
}
```

En este caso, se declara una cadena de conexión en una cuenta de Queue Storage en la matriz `secrets`. Para usar esta configuración, reemplace `<MY-CONNECTION-STRING-VALUE>` por el valor de la cadena de conexión.

# <a name="azure-cli"></a>[CLI de Azure](#tab/azure-cli)

Los secretos se definen mediante el parámetro `--secrets`.

- El parámetro acepta un conjunto delimitado por comas de pares nombre-valor.
- Cada par está delimitado por el signo igual (`=`).

```bash
az containerapp create \
  --resource-group "my-resource-group" \
  --name queuereader \
  --environment "my-environment-name" \
  --image demos/queuereader:v1 \
  --secrets "queue-connection-string=$CONNECTION_STRING" \
```

En este caso, se declara una cadena de conexión en una cuenta de Queue Storage en el parámetro `--secrets`. El valor de `queue-connection-string` procede de una variable de entorno denominada `$CONNECTION_STRING`.

# <a name="powershell"></a>[PowerShell](#tab/powershell)

Los secretos se definen mediante el parámetro `--secrets`.

- El parámetro acepta un conjunto delimitado por comas de pares nombre-valor.
- Cada par está delimitado por el signo igual (`=`).

```azurecli
az containerapp create `
  --resource-group "my-resource-group" `
  --name queuereader `
  --environment "my-environment-name" `
  --image demos/queuereader:v1 `
  --secrets "queue-connection-string=$CONNECTION_STRING" `
```

En este caso, se declara una cadena de conexión en una cuenta de Queue Storage en el parámetro `--secrets`. El valor de `queue-connection-string` procede de una variable de entorno denominada `$CONNECTION_STRING`.

---

## <a name="using-secrets"></a>Uso de secretos

Se hace referencia a los secretos de aplicación a través de la propiedad `secretref`. Los valores secretos se asignan a secretos de nivel de aplicación donde el valor `secretref` coincide con el nombre del secreto declarado en el nivel de aplicación.

## <a name="example"></a>Ejemplo

En el ejemplo siguiente se muestra una aplicación que declara una cadena de conexión en el nivel de aplicación y que se usa en toda la configuración a través de `secretref`.

# <a name="arm-template"></a>[Plantilla ARM](#tab/arm-template)

En este ejemplo, la cadena de conexión de la aplicación se declara como `queue-connection-string` y está disponible en otra parte de las secciones de configuración.

:::code language="json" source="code/secure-app-arm-template.json" highlight="11,12,13,27,28,29,30,31,44,45,61,62":::

Aquí, la variable de entorno denominada `connection-string` obtiene su valor del secreto de nivel de aplicación `queue-connection-string`. Asimismo, la configuración de autorización de la regla de escalado de Azure Queue Storage usa `queue-connection-string` a medida que se establece una conexión.

Para evitar confirmar valores secretos en el control de código fuente con la plantilla de ARM, pase los valores secretos como parámetros de plantilla de ARM.

# <a name="azure-cli"></a>[CLI de Azure](#tab/azure-cli)

En este ejemplo, creará una aplicación con un secreto al que se hace referencia en una variable de entorno mediante la CLI de Azure.

```bash
az containerapp create \
  --resource-group "my-resource-group" \
  --name myQueueApp \
  --environment "my-environment-name" \
  --image demos/myQueueApp:v1 \
  --secrets "queue-connection-string=$CONNECTIONSTRING" \
  --environment-variables "QueueName=myqueue,ConnectionString=secretref:queue-connection-string"
```

Aquí, la variable de entorno denominada `connection-string` obtiene su valor del secreto de nivel de aplicación `queue-connection-string` mediante `secretref`.

# <a name="powershell"></a>[PowerShell](#tab/powershell)

En este ejemplo, creará una aplicación con un secreto al que se hace referencia en una variable de entorno mediante la CLI de Azure.

```azurecli
az containerapp create `
  --resource-group "my-resource-group" `
  --name myQueueApp `
  --environment "my-environment-name" `
  --image demos/myQueueApp:v1 `
  --secrets "queue-connection-string=$CONNECTIONSTRING" `
  --environment-variables "QueueName=myqueue,ConnectionString=secretref:queue-connection-string"
```

Aquí, la variable de entorno denominada `connection-string` obtiene su valor del secreto de nivel de aplicación `queue-connection-string` mediante `secretref`.

---

## <a name="next-steps"></a>Pasos siguientes

> [!div class="nextstepaction"]
> [Contenedores](containers.md)
