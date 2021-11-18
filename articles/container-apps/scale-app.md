---
title: Escalado en Azure Container Apps
description: Obtenga información sobre cómo las aplicaciones se reducen y escalan horizontalmente en Azure Container Apps.
services: container-apps
author: craigshoemaker
ms.service: container-apps
ms.topic: conceptual
ms.date: 11/02/2021
ms.author: cshoe
ms.custom: ignite-fall-2021
ms.openlocfilehash: 683545b892db4830e01f71faa2f77a097f9e8a9a
ms.sourcegitcommit: 61f87d27e05547f3c22044c6aa42be8f23673256
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/09/2021
ms.locfileid: "132061180"
---
# <a name="set-scaling-rules-in-azure-container-apps"></a>Establecer reglas de escalado en Azure Container Apps

Azure Container Apps administra el escalado horizontal automático a través de un conjunto de reglas de escalado declarativas. A medida que una aplicación contenedora se escala horizontalmente, se crean nuevas instancias de la aplicación contenedora a petición. Estas instancias se conocen como réplicas.

Las reglas de escalado se definen en `resources.properties.template.scale`la sección de la [configuración](overview.md). Hay dos propiedades de escala que se aplican a todas las reglas de la aplicación contenedora.

| Propiedad de escala | Descripción | Valor predeterminado | Valor mínimo | Valor máximo |
|---|---|---|---|---|
| `minReplicas` | Número mínimo de réplicas que se ejecutan para la aplicación contenedora. | 0 | 1 | 25 |
| `maxReplicas` | Número máximo de réplicas que se ejecutan para la aplicación contenedora. | N/D | 1 | 25 |

- Si la aplicación contenedora se escala a cero, no se le facturará.
- Las reglas de escalado se definen en la matriz `rules`.
- Si desea asegurarse de que una instancia de la aplicación siempre se está ejecutando, establezca `minReplicas` en 1 o superior.
- Las réplicas que no se están procesando, pero que permanecen en memoria, se facturan en la categoría "cargo inactivo".
- Los cambios en las reglas de escalado son un cambio de [ámbito de revisión](overview.md).
- Cuando se usan reglas de escalado de eventos que no son HTTP, se recomienda establecer `activeRevisionMode` en `single`.

> [!IMPORTANT]
> Las cantidades de réplica son una cantidad de destino, no una garantía. Incluso si establece `maxReplicas` en `1`, no hay ninguna garantía de seguridad para subprocesos.

## <a name="scale-triggers"></a>Desencadenadores de escalado

Container Apps admite un gran número de desencadenadores de escalado. Para obtener más información sobre los desencadenadores de escalado admitidos, consulte las [escalas KEDA](https://keda.sh/docs/scalers/).

La documentación de KEDA muestra ejemplos de código en YAML, mientras que la plantilla de Resource Manager de Container Apps está en JSON. A medida que transforme ejemplos de KEDA para sus necesidades, asegúrese de cambiar los nombres de propiedad de mayúsculas y minúsculas [kebab](https://en.wikipedia.org/wiki/Naming_convention_(programming)#Delimiter-separated_words) a mayúsculas y minúsculas [camel](https://en.wikipedia.org/wiki/Naming_convention_(programming)#Letter_case-separated_words).

## <a name="http"></a>HTTP

Con una regla de escalado HTTP, tiene control sobre el umbral que determina cuándo escalar horizontalmente.

| Propiedad de escala | Descripción | Valor predeterminado | Valor mínimo | Valor máximo |
|---|---|---|---|---|
| `concurrentRequests`| Una vez que el número de solicitudes supera este valor, se agregan más réplicas, hasta la cantidad de `maxReplicas`. | 50 | 1 | N/D |

```json
{
  ...
  "resources": {
    ...
    "properties": {
      ...
      "template": {
        ...
        "scale": {
          "minReplicas": 0,
          "maxReplicas": 5,
          "rules": [{
            "name": "http-rule",
            "http": {
              "metadata": {
                  "concurrentRequests": 100
              }
            }
          }]
        }
      }
    }
  }
}
```

En este ejemplo, la aplicación contenedora se escala horizontalmente hasta cinco réplicas y puede reducirse verticalmente a cero instancias. El umbral de escalado se establece en 100 solicitudes simultáneas por segundo.

## <a name="event-driven"></a>Controlado por eventos

Container Apps se puede escalar en función de una amplia variedad de tipos de eventos. Cualquier evento compatible con [KEDA](https://keda.sh/docs/scalers/) se admite en Container Apps.

Cada tipo de evento presenta propiedades diferentes en la sección `metadata` de la definición de KEDA. Use estas propiedades para definir una regla de escalado en Container Apps.

En el ejemplo siguiente se muestra cómo crear una regla de escalado basada en un desencadenador [Azure Service Bus](https://keda.sh/docs/scalers/azure-service-bus/).

```json
{
  ...
  "resources": {
    ...
    "properties": {
      "configuration": {
        "secrets": [{
          "name": "servicebusconnectionstring",
          "value": "<MY-CONNECTION-STRING-VALUE>"
        }],
      },
      "template": {
        ...
        "scale": {
          "minReplicas": "0",
          "maxReplicas": "10",
          "rules": [
          {
            "name": "queue-based-autoscaling",
            "custom": {
              "type": "azure-servicebus",
              "metadata": {
                "queueName": "myServiceBusQueue",
                "messageCount": "20"
              },
              "auth": [{
                "secretRef": "servicebusconnectionstring",
                "triggerParameter": "connection"
              }]
        }
    }]
}
```

En este ejemplo, la aplicación contenedora se escala según el siguiente comportamiento:

- A medida que el número de mensajes de la cola supera los 20, se crean nuevas réplicas.
- La cadena de conexión a la cola se proporciona como un parámetro para el archivo de configuración y se hace referencia a través de la propiedad `secretRef`.

## <a name="cpu"></a>CPU

El escalado de CPU permite que la aplicación se reduzca o escale horizontalmente en función de la cantidad de CPU que se esté utilizando. El escalado de CPU no permite que la aplicación contenedora se escale a 0. Para obtener más información acerca de este desencadenador, consulte [desencadenador de escalado de CPU KEDA](https://keda.sh/docs/scalers/cpu/).

En el ejemplo siguiente se muestra cómo crear una regla de escalado de CPU.

```json
{
  ...
  "resources": {
    ...
    "properties": {
      ...
      "template": {
        ...
        "scale": {
          "minReplicas": "0",
          "maxReplicas": "10",
          "rules": [{
            "name": "cpuScalingRule",
            "custom": {
              "type": "cpu",
              "metadata": {
                "type": "Utilization",
                "value": "50"
              }
            }
          }]
        }
      }
    }
  }
}
```

- En este ejemplo, la aplicación contenedora se escala cuando el uso de CPU supera el 50 %.
- Como mínimo, una única réplica permanece en memoria para las aplicaciones que se escalan en función del uso de CPU.

## <a name="memory"></a>Memoria

El escalado de memoria permite que la aplicación se reduzca o escale horizontalmente en función de la cantidad de memoria que se esté utilizando. El escalado de memoria no permite que la aplicación contenedora se escale a 0. Para obtener más información acerca de esta escala, consulte [escala de memoria KEDA](https://keda.sh/docs/scalers/memory/).

En el ejemplo siguiente se muestra cómo crear una regla de escalado de memoria.

```json
{
  ...
  "resources": {
    ...
    "properties": {
      ...
      "template": {
        ...
        "scale": {
          "minReplicas": "0",
          "maxReplicas": "10",
          "rules": [{
            "name": "memoryScalingRule",
            "custom": {
              "type": "memory",
              "metadata": {
                "type": "Utilization",
                "value": "50"
              }
            }
          }]
        }
      }
    }
  }
}
```

- En este ejemplo, la aplicación contenedora se escala cuando el uso de memoria supera el 50 %.
- Como mínimo, una única réplica permanece en memoria para las aplicaciones que se escalan en función del uso de memoria.


## <a name="considerations"></a>Consideraciones

- No se admite el escalado vertical.
- Las cantidades de réplica son una cantidad de destino, no una garantía.
  - Incluso si establece `maxReplicas` en `1`, no hay ninguna garantía de seguridad para subprocesos.

## <a name="next-steps"></a>Pasos siguientes

> [!div class="nextstepaction"]
> [Protección de la aplicación de contenedor](secure-app.md)
