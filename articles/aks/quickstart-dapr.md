---
title: Implementación de una aplicación con la extensión de clúster de Dapr (versión preliminar) para Azure Kubernetes Service (AKS)
description: Uso de la extensión de clúster de Dapr (versión preliminar) para Azure Kubernetes Service (AKS) para implementar una aplicación
author: nickomang
ms.author: nickoman
ms.service: container-service
ms.topic: quickstart
ms.date: 11/01/2021
ms.custom: template-quickstart
ms.openlocfilehash: 1590bcc48260e4b6729cd12cb80b047ba9fe4acb
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/03/2021
ms.locfileid: "131478205"
---
# <a name="quickstart-deploy-an-application-using-the-dapr-cluster-extension-preview-for-azure-kubernetes-service-aks"></a>Inicio rápido: Implementación de una aplicación mediante la extensión de clúster de Dapr (versión preliminar) para Azure Kubernetes Service (AKS)

En este inicio rápido, se familiarizará con el uso de la [extensión de clúster de Dapr][dapr-overview] en un clúster de AKS. Implementará un ejemplo de hola mundo, que consta de una aplicación de Python que genera mensajes y una aplicación de Node que los consume y los conserva.

[!INCLUDE [preview features callout](./includes/preview/preview-callout.md)]

## <a name="prerequisites"></a>Prerrequisitos

* Suscripción a Azure. Si no tiene una suscripción a Azure, puede crear una [cuenta gratuita](https://azure.microsoft.com/free).
* [La CLI de Azure instalada](/cli/azure/install-azure-cli).
* Un clúster de AKS con la [extensión del clúster de Dapr][dapr-overview] habilitada

## <a name="clone-the-repository"></a>Clonación del repositorio

Para obtener los archivos que va a usar para implementar la aplicación de ejemplo, clone el [repositorio de inicios rápidos][hello-world-gh] y cambie al directorio `hello-kubernetes`:

```bash
git clone https://github.com/dapr/quickstarts.git
cd quickstarts/hello-kubernetes
```

## <a name="create-and-configure-a-state-store"></a>Creación y configuración de un almacén de estado

Dapr puede usar varios almacenes de estado (Redis, CosmosDB, DynamoDB, Cassandra, etc.) para conservar y recuperar el estado. En este ejemplo, se va a usar Redis.

### <a name="create-a-redis-store"></a>Creación de un almacén de Redis

1. Abra [Azure Portal][azure-portal-cache] para iniciar el flujo de creación de Azure Cache for Redis.
2. Rellene la información necesaria.
3. Haga clic en "Crear" para iniciar la implementación de la instancia de Redis.
4. Anote el nombre de host de la instancia de Redis; dicho nombre se puede recuperar de la "información general" de Azure. Debería ser parecido a este: `xxxxxx.redis.cache.windows.net:6380`.
5. Una vez creada la instancia, deberá usar su clave de acceso. Vaya a "Claves de acceso" en "Configuración" y cree un secreto de Kubernetes para almacenar la contraseña de Redis:

```bash
kubectl create secret generic redis --from-literal=redis-password=<your-redis-password>
```

### <a name="configure-the-dapr-components"></a>Configuración de los componentes de Dapr

Una vez creado el almacén, deberá agregar las claves al archivo redis.yaml del directorio deploy del repositorio Hello world. Reemplace el valor `redisHost` por su propia dirección maestra de Redis y `redisPassword` por su propio secreto. Puede obtener más información [aquí][dapr-component-secrets].

También deberá agregar las dos líneas siguientes después de `redisPassword` para habilitar la conexión a través de TLS:

```yml
- name: redisPassword
    secretKeyRef:
      name: redis
      key: redis-password
- name: enableTLS
  value: true
```

### <a name="apply-the-configuration"></a>Aplicación de la configuración

Aplique el archivo `redis.yaml`:

```bash
kubectl apply -f ./deploy/redis.yaml
``` 

Y compruebe que el almacén de estado se ha configurado correctamente en la salida:

```bash
component.dapr.io/statestore created
```

## <a name="deploy-the-nodejs-app-with-the-dapr-sidecar"></a>Implementación de la aplicación de Node.js con el sidecar de Dapr

Aplique la implementación de la aplicación de Node.js al clúster:

```bash
kubectl apply -f ./deploy/node.yaml
```

> [!NOTE]
> Las implementaciones de Kubernetes son asincrónicas, lo que significa que antes de continuar con el paso siguiente hay que esperar a que se complete la implementación. Para ello, use el siguiente comando:
> ```bash
> kubectl rollout status deploy/nodeapp
> ```

De esta forma se implementará la aplicación de Node.js en Kubernetes. El plano de control de Dapr inyectará automáticamente el sidecar de Dapr en el pod. Si echa un vistazo al archivo `node.yaml`, verá que Dapr está habilitado para esa implementación:

* `dapr.io/enabled: true`: indica al plano de control de Dapr que inyecte un sidecar a esta implementación.

* `dapr.io/app-id: nodeapp`: asigna un identificador o nombre únicos a la aplicación de Dapr, con el fin de que se puedan enviar mensajes a otras aplicaciones de Dapr y comunicarse con ellas.

Para acceder al servicio, obtenga el valor de `EXTERNAL-IP` a través de `kubectl` y anótelo:

```bash
kubectl get svc nodeapp
```

### <a name="verify-the-service"></a>Comprobación del servicio

Para llamar al servicio, ejecute:

```bash
curl $EXTERNAL_IP/ports
```

Debería ver un resultado similar al siguiente:

```bash
{"DAPR_HTTP_PORT":"3500","DAPR_GRPC_PORT":"50001"}
```

A continuación, envíe una orden a la aplicación:

```bash
curl --request POST --data "@sample.json" --header Content-Type:application/json $EXTERNAL_IP/neworder
```

Confirme que la orden es persistente; para ello, solicítelo:

```bash
curl $EXTERNAL_IP/order
```

Debería ver un resultado similar al siguiente:

```bash
{ "orderId": "42" }
```

> [!TIP]
> Este es un buen momento para familiarizarse con el panel de Dapr, que es una cómoda interfaz para comprobar el estado, la información y los registros de las aplicaciones que se ejecutan en Dapr. El siguiente comando hará que esté disponible en `http://localhost:8080/`:
> ```bash
> kubectl port-forward svc/dapr-dashboard -n dapr-system 8080:8080
> ```

## <a name="deploy-the-python-app-with-the-dapr-sidecar"></a>Implementación de la aplicación de Python con el sidecar de Dapr

Eche un vistazo rápido a la aplicación de Python. Vaya al directorio de aplicaciones de Python en el inicio rápido de `hello-kubernetes` y abra `app.py`.

Se trata de una aplicación básica de Python que publica mensajes JSON en `localhost:3500`, que es el puerto de escucha predeterminado para Dapr. Puede invocar el punto de conexión `neworder` de la aplicación de Node.js mediante la publicación en `v1.0/invoke/nodeapp/method/neworder`. El mensaje contiene algunos datos con un `orderId` que se incrementa una vez por segundo:

```python
n = 0
while True:
    n += 1
    message = {"data": {"orderId": n}}

    try:
        response = requests.post(dapr_url, json=message)
    except Exception as e:
        print(e)

    time.sleep(1)
```

Implemente la aplicación de Python en el clúster de Kubernetes:

```bash
kubectl apply -f ./deploy/python.yaml
```

> [!NOTE]
> Al igual que con lo anterior, el siguiente comando esperará hasta que se complete la implementación:
> ```bash
> kubectl rollout status deploy/pythonapp
> ```

## <a name="observe-messages-and-confirm-persistence"></a>Observación de mensajes y confirmación de persistencia

Ahora que se han implementado las aplicaciones de Node.js y Python, vea cómo llegan los mensajes.

Obtenga los registros de la aplicación de Node.js:

```bash
kubectl logs --selector=app=node -c node --tail=-1
```

Si las implementaciones se han realizado correctamente, debería ver registros como este:

```bash
Got a new order! Order ID: 1
Successfully persisted state
Got a new order! Order ID: 2
Successfully persisted state
Got a new order! Order ID: 3
Successfully persisted state
```

Llame al punto de conexión de la orden de la aplicación de Node.js para obtener la orden más reciente. Tome la dirección IP externa que guardó antes, anexe "/order" y realice una solicitud GET en ella (use el explorador, Postman o `curl`):

```bash
curl $EXTERNAL_IP/order
{"orderID":"42"}
```

Debería ver el JSON más reciente en la respuesta.

## <a name="clean-up-resources"></a>Limpieza de recursos

Use el comando [az group delete][az-group-delete] para eliminar el grupo de recursos, el clúster de AKS, el espacio de nombres y todos los recursos relacionados.

```azurecli-interactive
az group delete --name MyResourceGroup
```

## <a name="next-steps"></a>Pasos siguientes

Después de implementar correctamente esta aplicación de ejemplo:
> [!div class="nextstepaction"]
> [Más información sobre otras extensiones de clúster][cluster-extensions]

<!-- LINKS -->
<!-- INTERNAL -->
[cluster-extensions]: ./cluster-extensions.md
[dapr-overview]: ./dapr.md
[az-group-delete]: /cli/azure/group#az_group_delete

<!-- EXTERNAL -->
[hello-world-gh]: https://github.com/dapr/quickstarts/tree/v1.4.0/hello-kubernetes
[azure-portal-cache]: https://ms.portal.azure.com/#create/Microsoft.Cache
[dapr-component-secrets]: https://docs.dapr.io/operations/components/component-secrets/