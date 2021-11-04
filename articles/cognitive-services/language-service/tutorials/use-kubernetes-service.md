---
title: Implementación de un contenedor de extracción de frases clave en Azure Kubernetes Service
titleSuffix: Azure Cognitive Services
description: Implemente una imagen de contenedor de extracción de frases clave en Azure Kubernetes Service y pruébela en un explorador web.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-service
ms.topic: conceptual
ms.date: 11/02/2021
ms.author: aahi
ms.custom: ignite-fall-2021
ms.openlocfilehash: a71910bde7718c0a60eda87ea51f6c49e473304a
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/02/2021
ms.locfileid: "131092951"
---
# <a name="deploy-a-key-phrase-extraction-container-to-azure-kubernetes-service"></a>Implementación de un contenedor de extracción de frases clave en Azure Kubernetes Service

Aprenda a implementar una [imagen de contenedor de Docker de extracción de frases clave](../key-phrase-extraction/how-to/use-containers.md) en Azure Kubernetes Service (AKS). En este procedimiento se muestra cómo crear un recurso de Language, cómo asociar una imagen de contenedor y cómo ejecutar la orquestación de los dos desde un explorador. El uso de contenedores puede desviar la atención de la administración de la infraestructura y centrarla en el desarrollo de aplicaciones. Aunque en este artículo se usa el contenedor de extracción de frases clave como ejemplo, puede usar este proceso para otros contenedores que ofrece Azure Cognitive Service para Language.

## <a name="prerequisites"></a>Requisitos previos

Este procedimiento requiere varias herramientas que se deben instalar y ejecutar localmente. No use Azure Cloud Shell. Necesita lo siguiente:

* Suscripción a Azure. Si no tiene una suscripción a Azure, cree una [cuenta gratuita](https://azure.microsoft.com/free/cognitive-services) antes de empezar.
* Un editor de texto; por ejemplo, [Visual Studio Code](https://code.visualstudio.com/download).
* La [CLI de Azure](/cli/azure/install-azure-cli) instalada.
* La [CLI de Kubernetes](https://kubernetes.io/docs/tasks/tools/install-kubectl/) instalada.
* Un recurso de Azure con el plan de tarifa correcto. No todos los planes de tarifa funcionan con este contenedor:
    * El recurso **Azure Language** solo con los planes de tarifa estándar o F0.
    * El recurso **Azure Cognitive Services** con el plan de tarifa S0.

[!INCLUDE [Create a Cognitive Services Language resource](../includes/containers/create-text-analytics-resource.md)]

[!INCLUDE [Create a language container on Azure Kubernetes Service (AKS)](../../containers/includes/create-aks-resource.md)]


## <a name="deploy-the-key-phrase-extraction-container-to-an-aks-cluster"></a>Implementación del contenedor de extracción de frases clave en un clúster de AKS

1. Abra la CLI de Azure e inicie sesión en Azure.

    ```azurecli
    az login
    ```

1. Inicie sesión en el clúster de AKS. Reemplace `your-cluster-name` y `your-resource-group` por los valores adecuados.

    ```azurecli
    az aks get-credentials -n your-cluster-name -g -your-resource-group
    ```

    Después de la ejecución de este comando, se muestra un mensaje similar al siguiente:

    ```output
    Merged "your-cluster-name" as current context in /home/username/.kube/config
    ```

    > [!WARNING]
    > Si tiene varias suscripciones disponibles en su cuenta de Azure y el comando `az aks get-credentials` devuelve un error, puede deberse a un problema común de uso de la suscripción incorrecta. Establezca el contexto de la sesión de la CLI de Azure para usar la misma suscripción con la que creó los recursos y vuelva a intentarlo.
    > ```azurecli
    >  az account set -s subscription-id
    > ```

1. Abra el editor de texto que prefiera. En este ejemplo se utiliza Visual Studio Code.

    ```console
    code .
    ```

1. En el editor de texto, cree un nuevo archivo llamado *keyphrase.yaml* y pegue el siguiente código YAML en él. Asegúrese de reemplazar `billing/value` y `apikey/value` por su propia información.

    ```yaml
    apiVersion: apps/v1beta1
    kind: Deployment
    metadata:
      name: keyphrase
    spec:
      template:
        metadata:
          labels:
            app: keyphrase-app
        spec:
          containers:
          - name: keyphrase
            image: mcr.microsoft.com/azure-cognitive-services/keyphrase
            ports:
            - containerPort: 5000
            resources:
              requests:
                memory: 2Gi
                cpu: 1
              limits:
                memory: 4Gi
                cpu: 1
            env:
            - name: EULA
              value: "accept"
            - name: billing
              value: # {ENDPOINT_URI}
            - name: apikey
              value: # {API_KEY}
     
    --- 
    apiVersion: v1
    kind: Service
    metadata:
      name: keyphrase
    spec:
      type: LoadBalancer
      ports:
      - port: 5000
      selector:
        app: keyphrase-app
    ```

1. Guarde el archivo y cierre el editor de texto.
1. Ejecute el comando `apply` de Kubernetes con el archivo *keyphrase.yaml* como destino:

    ```console
    kubectl apply -f keyphrase.yaml
    ```

    Una vez que el comando ha aplicado correctamente la configuración de implementación, se muestra un mensaje similar al siguiente:

    ```output
    deployment.apps "keyphrase" created
    service "keyphrase" created
    ```
1. Compruebe que el pod está implementado:

    ```console
    kubectl get pods
    ```

    La salida del estado de ejecución del pod:

    ```output
    NAME                         READY     STATUS    RESTARTS   AGE
    keyphrase-5c9ccdf575-mf6k5   1/1       Running   0          1m
    ```

1. Compruebe que el servicio está disponible y obtenga la dirección IP.

    ```console
    kubectl get services
    ```

    La salida del estado de ejecución del servicio *keyphrase* en el pod:

    ```output
    NAME         TYPE           CLUSTER-IP    EXTERNAL-IP      PORT(S)          AGE
    kubernetes   ClusterIP      10.0.0.1      <none>           443/TCP          2m
    keyphrase    LoadBalancer   10.0.100.64   168.61.156.180   5000:31234/TCP   2m
    ```

## <a name="verify-the-key-phrase-extraction-container-instance"></a>Comprobación de la instancia del contenedor para la extracción de frases clave

1. Seleccione la pestaña **Información general** y copie la dirección IP.
1. Abra una nueva pestaña del explorador y escriba la dirección IP. Por ejemplo, escriba `http://<IP-address>:5000 (http://55.55.55.55:5000`). Se mostrará la página principal del contenedor, que le permitirá saber que el contenedor se está ejecutando.

    ![Visualización de la página principal del contenedor para comprobar que se está ejecutando](../media/swagger-container-documentation.png)

1. Seleccione el vínculo de **Descripción de API de servicio** para ir a la página Swagger del contenedor.

1. Elija cualquiera de las API **POST** y seleccione **Probar**. Se muestran los parámetros, que incluyen la entrada de este ejemplo:

    ```json
    {
      "documents": [
        {
          "id": "1",
          "text": "Hello world"
        },
        {
          "id": "2",
          "text": "Bonjour tout le monde"
        },
        {
          "id": "3",
          "text": "La carretera estaba atascada. Había mucho tráfico el día de ayer."
        },
        {
          "id": "4",
          "text": ":) :( :D"
        }
      ]
    }
    ```

1. Reemplace la entrada con el contenido JSON siguiente:

    ```json
    {
      "documents": [
        {
          "language": "en",
          "id": "7",
          "text": "I was fortunate to attend the KubeCon Conference in Barcelona, it is one of the best conferences I have ever attended. Great people, great sessions and I thoroughly enjoyed it!"
        }
      ]
    }
    ```

1. Establezca **showStats** en `true`.

1. Seleccione **Execute** (Ejecutar) para determinar la opinión del texto.

    El modelo empaquetado en el contenedor genera una puntuación comprendida entre 0 y 1, donde 0 es negativa y 1 es positiva.

    La respuesta JSON devuelta incluye las opiniones de la entrada de texto actualizado:

    ```json
    {
      "documents": [
        {
          "id": "7",
          "keyPhrases": [
            "Great people",
            "great sessions",
            "KubeCon Conference",
            "Barcelona",
            "best conferences"
          ],
          "statistics": {
            "charactersCount": 176,
            "transactionsCount": 1
          }
        }
      ],
      "errors": [],
      "statistics": {
        "documentsCount": 1,
        "validDocumentsCount": 1,
        "erroneousDocumentsCount": 0,
        "transactionsCount": 1
      }
    }
    ```

Ahora podemos correlacionar el documento `id` de los datos JSON de la carga de respuesta con el documento `id`de carga de solicitud original. El documento resultante tiene una matriz `keyPhrases` que contiene la lista de frases clave que se han extraído del documento de entrada correspondiente. Además, hay varias estadísticas, como `characterCount` y `transactionCount`, para cada documento resultante.

## <a name="next-steps"></a>Pasos siguientes

* Uso de otros [contenedores de Cognitive Services](../../cognitive-services-container-support.md)
* [Información general sobre Extracción de frases clave](../overview.md)
