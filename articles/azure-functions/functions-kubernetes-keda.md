---
title: Azure Functions en Kubernetes con KEDA
description: Aprenda a ejecutar Azure Functions en Kubernetes en la nube o en el entorno local mediante KEDA, el escalado automático controlado por eventos basado en Kubernetes.
author: eamonoreilly
ms.topic: conceptual
ms.date: 11/18/2019
ms.author: eamono
ms.openlocfilehash: 736945109cd18bd7c6f3a6b9a16b6549f7c65652
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/13/2021
ms.locfileid: "121741395"
---
# <a name="azure-functions-on-kubernetes-with-keda"></a>Azure Functions en Kubernetes con KEDA

El tiempo de ejecución de Azure Functions proporciona flexibilidad de hospedaje dónde y cómo desee.  [KEDA](https://keda.sh) (escalado automático controlado por eventos basado en Kubernetes) se empareja sin problemas con el tiempo de ejecución y las herramientas de Azure Functions para proporcionar la escala controlada por eventos en Kubernetes.

## <a name="how-kubernetes-based-functions-work"></a>Funcionamiento de las funciones basadas en Kubernetes

El servicio de Azure Functions está formado por dos componentes clave: un tiempo de ejecución y un controlador de escala.  El tiempo de ejecución de Functions se ejecuta y ejecuta el código.  El tiempo de ejecución incluye lógica sobre cómo desencadenar, registrar y administrar las ejecuciones de funciones.  Azure Functions Runtime puede ejecutarse *en cualquier parte*.  El otro componente es un controlador de escala.  El controlador de escala supervisa la tasa de eventos que se dirigen a la función y escala de forma proactiva el número de instancias que ejecutan la aplicación.  Para más información, consulte [Escalado y hospedaje de Azure Functions](functions-scale.md).

Las funciones basadas en Kubernetes proporcionan el tiempo de ejecución de Functions en un [Contenedor de Docker](functions-create-function-linux-custom-image.md) con escalado controlado por eventos a través de KEDA.  KEDA puede reducir horizontalmente a 0 instancias (cuando no se produce ningún evento) y escalar horizontalmente hasta *n* instancias. Lo hace mediante la exposición de las métricas personalizadas de Kubernetes Autoscaler (Horizontal Pod Autoscaler).  El uso de contenedores de Functions con KEDA hace posible replicar las capacidades de la función sin servidor en cualquier clúster de Kubernetes.  Estas funciones también se pueden implementar mediante la característica de [nodos virtuales de Azure Kubernetes Service (AKS)](../aks/virtual-nodes-cli.md) para la infraestructura sin servidor.

## <a name="managing-keda-and-functions-in-kubernetes"></a>Administración de KEDA y Functions en Kubernetes

Para ejecutar Functions en el clúster de Kubernetes, debe instalar el componente KEDA. Puede instalar este complemento de una de estas formas:

+ Azure Functions Core Tools: mediante el [comando `func kubernetes install`](functions-core-tools-reference.md#func-kubernetes-install).

+ Helm: hay varias maneras de instalar KEDA en un clúster de Kubernetes, incluido Helm.  Las opciones de implementación se documentan en el [sitio de KEDA](https://keda.sh/docs/deploy/).

## <a name="deploying-a-function-app-to-kubernetes"></a>Implementación de una aplicación de función en Kubernetes

Puede implementar cualquier aplicación de función en un clúster de Kubernetes mediante la ejecución de KEDA.  Puesto que las funciones se ejecutan en un contenedor de Docker, el proyecto debe ser un Dockerfile.  Puede crear un Dockerfile mediante la [opción`--docker`][func init] al llamar a `func init` para crear el proyecto. Si olvidó hacerlo, siempre puede volver a llamar a `func init` desde la raíz del proyecto de Functions, esta vez con la [opción `--docker-only`][func init], como se muestra en el ejemplo siguiente. 

```command
func init --docker-only
```

Para más información sobre la generación del Dockerfile, consulte la referencia de [`func init`][func init]. 

Para crear una imagen e implementar las funciones en Kubernetes, ejecute el siguiente comando:

```command
func kubernetes deploy --name <name-of-function-deployment> --registry <container-registry-username>
```

En este ejemplo, reemplace `<name-of-function-deployment>` por el nombre de la aplicación de funciones.

El comando de implementación hace lo siguiente:

1. El Dockerfile creado anteriormente se usa para compilar una imagen local para la aplicación de funciones.
1. La imagen local se etiqueta y se inserta en el registro de contenedor en el que el usuario ha iniciado sesión.
1. Se crea un manifiesto y se aplica al clúster que define un recurso `Deployment` de Kubernetes, un recurso `ScaledObject` y `Secrets`, que incluye las variables de entorno importadas desde su archivo `local.settings.json`.

Para obtener más información, consulte el [comando `func kubernetes deploy`](functions-core-tools-reference.md#func-kubernetes-deploy).

### <a name="deploying-a-function-app-from-a-private-registry"></a>Implementación de una aplicación de funciones desde un registro privado

El flujo anterior también funciona con registros privados.  Si extrae la imagen de contenedor de un registro privado, incluya la marca `--pull-secret` que hace referencia al secreto de Kubernetes que contiene las credenciales del registro privado al ejecutar `func kubernetes deploy`.

## <a name="removing-a-function-app-from-kubernetes"></a>Eliminación de una aplicación de función de Kubernetes

Después de la implementación puede quitar una función mediante la eliminación de los `Deployment`, `ScaledObject` asociados y `Secrets` creados.

```command
kubectl delete deploy <name-of-function-deployment>
kubectl delete ScaledObject <name-of-function-deployment>
kubectl delete secret <name-of-function-deployment>
```

## <a name="uninstalling-keda-from-kubernetes"></a>Desinstalación de KEDA de Kubernetes

Puede quitar KEDA del clúster de una de las maneras siguientes:

+ Azure Functions Core Tools: mediante el [comando `func kubernetes remove`](functions-core-tools-reference.md#func-kubernetes-remove).

+ Helm: consulte los pasos de desinstalación [en el sitio de KEDA](https://keda.sh/docs/deploy/).

## <a name="supported-triggers-in-keda"></a>Desencadenadores admitidos en KEDA

KEDA es compatible con los siguientes desencadenadores de Azure Functions:

* [Colas de Azure Storage](functions-bindings-storage-queue.md)
* [Colas de Azure Service Bus](functions-bindings-service-bus.md)
* [Azure Event / IoT Hubs](functions-bindings-event-hubs.md)
* [Apache Kafka](https://github.com/azure/azure-functions-kafka-extension)
* [Cola de RabbitMQ](https://github.com/azure/azure-functions-rabbitmq-extension)

### <a name="http-trigger-support"></a>Compatibilidad con el desencadenador HTTP

Puede usar instancias de Azure Functions que expongan desencadenadores HTTP, pero KEDA no los administra directamente.  Puede aprovechar el desencadenador prometheus de KEDA para [escalar Azure Functions de HTTP de 1 a *n* instancias](https://dev.to/anirudhgarg_99/scale-up-and-down-a-http-triggered-function-app-in-kubernetes-using-keda-4m42).

## <a name="next-steps"></a>Pasos siguientes
Para obtener más información, consulte los siguientes recursos:

* [Creación de una función con una imagen personalizada](functions-create-function-linux-custom-image.md)
* [Codificación y comprobación de Azure Functions en un entorno local](functions-develop-local.md)
* [Cómo funciona el plan de consumo de Azure Functions](functions-scale.md)

[func init]: functions-core-tools-reference.md#func-init