---
title: Uso de contenedores del servicio de voz con Kubernetes y Helm
titleSuffix: Azure Cognitive Services
description: Mediante el empleo de Kubernetes y Helm para definir las imágenes de contenedor de conversión de voz en texto y de texto en voz, se va a crear un paquete de Kubernetes. Este paquete se va a implementar en un clúster de Kubernetes en el entorno local.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 07/22/2021
ms.author: aahi
ms.openlocfilehash: 636ab6e2353c2268c75dfa961987d3e0e79e189c
ms.sourcegitcommit: 7d63ce88bfe8188b1ae70c3d006a29068d066287
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 07/22/2021
ms.locfileid: "114454188"
---
# <a name="use-speech-service-containers-with-kubernetes-and-helm"></a>Uso de contenedores del servicio de voz con Kubernetes y Helm

Una opción para administrar los contenedores de Speech locales es usar Kubernetes y Helm. Mediante el empleo de Kubernetes y Helm para definir las imágenes de contenedor de conversión de voz en texto y de texto en voz, se va a crear un paquete de Kubernetes. Este paquete se va a implementar en un clúster de Kubernetes en el entorno local. Por último, se va a explicar cómo probar los servicios implementados y diversas opciones de configuración. Para más información sobre cómo ejecutar contenedores de Docker sin orquestación de Kubernetes, consulte [Instalación y ejecución de contenedores de servicio de voz](speech-container-howto.md).

## <a name="prerequisites"></a>Requisitos previos

Requisitos previos para poder usar los contenedores de Voz en el entorno local:

| Obligatorio | Propósito |
|----------|---------|
| Cuenta de Azure | Si no tiene una suscripción a Azure, cree una [cuenta gratuita][free-azure-account] antes de empezar. |
| Acceso a Container Registry | Para incorporar las imágenes de Docker al clúster, Kubernetes necesita acceso al registro de contenedor. |
| CLI de Kubernetes | La [CLI de Kubernetes][kubernetes-cli] es necesaria para administrar las credenciales compartidas desde el registro de contenedor. Kubernetes también se necesita antes que Helm, que es el administrador de paquetes de Kubernetes. |
| CLI de Helm | Instale la [CLI de Helm][helm-install], que se usa para instalar un gráfico de Helm (definición de paquete de contenedor). |
|Recurso de Voz |Para usar estos contenedores, debe tener:<br><br>Un recurso de _Voz_ de Azure para obtener la clave de facturación asociada y el URI del punto de conexión de facturación. Ambos valores están disponibles en las páginas de claves y de introducción de **Voz** de Azure Portal y son necesarios para iniciar el contenedor.<br><br>**{API_KEY}** : clave de recurso<br><br>**{ENDPOINT_URI}** : el ejemplo de URI de punto de conexión es: `https://westus.api.cognitive.microsoft.com/sts/v1.0`|

## <a name="the-recommended-host-computer-configuration"></a>Configuración de equipo host recomendada

Vea los detalles del [equipo host del contenedor del servicio de voz][speech-container-host-computer] como referencia. En este *gráfico de Helm* se calculan automáticamente los requisitos de CPU y memoria en función de cuántas descodificaciones (solicitudes simultáneas) especifica el usuario. Además, se ajusta en función de si las optimizaciones de entrada de audio o texto están configuradas como `enabled`. Los valores predeterminados del gráfico de Helm son dos solicitudes simultáneas y la deshabilitación de la optimización.

| Servicio | CPU o contenedor | Memoria o contenedor |
|--|--|--|
| **Voz a texto** | un descodificador requiere un mínimo de 1150 milésimas de núcleo. Si `optimizedForAudioFile` está habilitado, se requieren 1950 milésimas de núcleo. (valor predeterminado: dos descodificadores) | Requerido: 2 GB<br>Limitado:  4 GB |
| **Texto a voz** | una solicitud simultánea requiere un mínimo de 500 milésimas de núcleo. Si `optimizeForTurboMode` está habilitado, se requieren 1000 milésimas de núcleo. (valor predeterminado: dos solicitudes simultáneas) | Requerido: 1 GB<br> Limitado: 2 GB |

## <a name="connect-to-the-kubernetes-cluster"></a>Conectar al clúster de Kubernetes

Se espera que el equipo host tenga un clúster de Kubernetes disponible. Vea este tutorial sobre la [implementación de un clúster de Kubernetes](../../aks/tutorial-kubernetes-deploy-cluster.md) para lograr un reconocimiento conceptual de cómo implementar un clúster de Kubernetes en un equipo host.

## <a name="configure-helm-chart-values-for-deployment"></a>Configuración de los valores del gráfico de Helm para la implementación

Visite [Microsoft Helm Hub][ms-helm-hub] para ver todos los gráficos de Helm disponibles públicamente que ofrece Microsoft. En Microsoft Helm Hub se encuentra el **gráfico Cognitive Services Speech On-Premises**. **Cognitive Services Speech On-Premises** es el gráfico que se va a instalar, aunque primero se debe crear un archivo `config-values.yaml` con configuraciones explícitas. Vamos a comenzar por agregar el repositorio de Microsoft a la instancia de Helm.

```console
helm repo add microsoft https://microsoft.github.io/charts/repo
```

Luego vamos a configurar los valores del gráfico de Helm. Copie y pegue el siguiente YAML en un archivo denominado `config-values.yaml`. Para obtener más información sobre la personalización del **gráfico de Helm Cognitive Services Speech On-Premises**, vea [Personalizar gráficos de Helm](#customize-helm-charts). Reemplace los comentarios `# {ENDPOINT_URI}` y `# {API_KEY}` por sus propios valores.

```yaml
# These settings are deployment specific and users can provide customizations
# speech-to-text configurations
speechToText:
  enabled: true
  numberOfConcurrentRequest: 3
  optimizeForAudioFile: true
  image:
    registry: mcr.microsoft.com
    repository: azure-cognitive-services/speechservices/speech-to-text
    tag: latest
    pullSecrets:
      - mcr # Or an existing secret
    args:
      eula: accept
      billing: # {ENDPOINT_URI}
      apikey: # {API_KEY}

# text-to-speech configurations
textToSpeech:
  enabled: true
  numberOfConcurrentRequest: 3
  optimizeForTurboMode: true
  image:
    registry: mcr.microsoft.com
    repository: azure-cognitive-services/speechservices/speech-to-text
    tag: latest
    pullSecrets:
      - mcr # Or an existing secret
    args:
      eula: accept
      billing: # {ENDPOINT_URI}
      apikey: # {API_KEY}
```

> [!IMPORTANT]
> Si no se proporcionan los valores `billing` y `apikey`, los servicios expiran pasados 15 minutos. Del mismo modo, se produce un error de comprobación, ya que los servicios no están disponibles.

### <a name="the-kubernetes-package-helm-chart"></a>Paquete de Kubernetes (gráfico de Helm)

El *gráfico de Helm* contiene la configuración de las imágenes de Docker que se van a extraer del registro de contenedor `mcr.microsoft.com`.

> Un [gráfico de Helm][helm-charts] es una colección de archivos que describen un conjunto relacionado de recursos de Kubernetes. Un solo gráfico se podría usar para implementar algo sencillo, como un pod almacenado en memoria, o complejo, como una pila de aplicación web completa con servidores HTTP, bases de datos, memorias caché, etc.

Los *gráficos de Helm* proporcionados extraen las imágenes de Docker del servicio de voz, los servicios de conversión de texto en voz y de voz en texto desde el registro de contenedor `mcr.microsoft.com`.

## <a name="install-the-helm-chart-on-the-kubernetes-cluster"></a>Instalación del gráfico de Helm en el clúster de Kubernetes

Para instalar el *gráfico de Helm*, se debe ejecutar el comando [`helm install`][helm-install-cmd], reemplazando `<config-values.yaml>` por el argumento de ruta de acceso y nombre de archivo adecuado. El gráfico de Helm `microsoft/cognitive-services-speech-onpremise` al que se hace referencia a continuación está disponible en [Microsoft Helm Hub aquí][ms-helm-hub-speech-chart].

```console
helm install onprem-speech microsoft/cognitive-services-speech-onpremise \
    --version 0.1.1 \
    --values <config-values.yaml> 
```

Este es el resultado de ejemplo que se puede ver tras una ejecución de instalación correcta:

```console
NAME:   onprem-speech
LAST DEPLOYED: Tue Jul  2 12:51:42 2019
NAMESPACE: default
STATUS: DEPLOYED

RESOURCES:
==> v1/Pod(related)
NAME                             READY  STATUS             RESTARTS  AGE
speech-to-text-7664f5f465-87w2d  0/1    Pending            0         0s
speech-to-text-7664f5f465-klbr8  0/1    ContainerCreating  0         0s
text-to-speech-56f8fb685b-4jtzh  0/1    ContainerCreating  0         0s
text-to-speech-56f8fb685b-frwxf  0/1    Pending            0         0s

==> v1/Service
NAME            TYPE          CLUSTER-IP    EXTERNAL-IP  PORT(S)       AGE
speech-to-text  LoadBalancer  10.0.252.106  <pending>    80:31811/TCP  1s
text-to-speech  LoadBalancer  10.0.125.187  <pending>    80:31247/TCP  0s

==> v1beta1/PodDisruptionBudget
NAME                                MIN AVAILABLE  MAX UNAVAILABLE  ALLOWED DISRUPTIONS  AGE
speech-to-text-poddisruptionbudget  N/A            20%              0                    1s
text-to-speech-poddisruptionbudget  N/A            20%              0                    1s

==> v1beta2/Deployment
NAME            READY  UP-TO-DATE  AVAILABLE  AGE
speech-to-text  0/2    2           0          0s
text-to-speech  0/2    2           0          0s

==> v2beta2/HorizontalPodAutoscaler
NAME                       REFERENCE                  TARGETS        MINPODS  MAXPODS  REPLICAS  AGE
speech-to-text-autoscaler  Deployment/speech-to-text  <unknown>/50%  2        10       0         0s
text-to-speech-autoscaler  Deployment/text-to-speech  <unknown>/50%  2        10       0         0s


NOTES:
cognitive-services-speech-onpremise has been installed!
Release is named onprem-speech
```

La implementación de Kubernetes puede tardar varios minutos en completarse. Para confirmar que los pods y los servicios se han implementado correctamente y están disponibles, ejecute el siguiente comando:

```console
kubectl get all
```

Debería ver algo parecido al resultado siguiente:

```console
NAME                                  READY     STATUS    RESTARTS   AGE
pod/speech-to-text-7664f5f465-87w2d   1/1       Running   0          34m
pod/speech-to-text-7664f5f465-klbr8   1/1       Running   0          34m
pod/text-to-speech-56f8fb685b-4jtzh   1/1       Running   0          34m
pod/text-to-speech-56f8fb685b-frwxf   1/1       Running   0          34m

NAME                     TYPE           CLUSTER-IP     EXTERNAL-IP      PORT(S)        AGE
service/kubernetes       ClusterIP      10.0.0.1       <none>           443/TCP        3h
service/speech-to-text   LoadBalancer   10.0.252.106   52.162.123.151   80:31811/TCP   34m
service/text-to-speech   LoadBalancer   10.0.125.187   65.52.233.162    80:31247/TCP   34m

NAME                             DESIRED   CURRENT   UP-TO-DATE   AVAILABLE   AGE
deployment.apps/speech-to-text   2         2         2            2           34m
deployment.apps/text-to-speech   2         2         2            2           34m

NAME                                        DESIRED   CURRENT   READY     AGE
replicaset.apps/speech-to-text-7664f5f465   2         2         2         34m
replicaset.apps/text-to-speech-56f8fb685b   2         2         2         34m

NAME                                                            REFERENCE                   TARGETS   MINPODS   MAXPODS   REPLICAS   AGE
horizontalpodautoscaler.autoscaling/speech-to-text-autoscaler   Deployment/speech-to-text   1%/50%    2         10        2          34m
horizontalpodautoscaler.autoscaling/text-to-speech-autoscaler   Deployment/text-to-speech   0%/50%    2         10        2          34m
```

### <a name="verify-helm-deployment-with-helm-tests"></a>Comprobación de la implementación de Helm con pruebas de Helm

Los gráficos de Helm instalados definen *pruebas de Helm*, que resultan cómodas para la comprobación. Estas pruebas validan la disponibilidad del servicio. Para comprobar los servicios de conversión de **voz en texto** y de **texto en voz**, se ejecuta el comando [Helm test][helm-test].

```console
helm test onprem-speech
```

> [!IMPORTANT]
> Se produce un error en estas pruebas si el estado de POD no es `Running` o si la implementación no aparece en la columna `AVAILABLE`. Tenga paciencia, ya que esto puede tardar más de diez minutos en completarse.

Estas pruebas generan distintos resultados de estado:

```console
RUNNING: speech-to-text-readiness-test
PASSED: speech-to-text-readiness-test
RUNNING: text-to-speech-readiness-test
PASSED: text-to-speech-readiness-test
```

Como alternativa a la ejecución de *pruebas de Helm*, puede recopilar las direcciones *IP externas* y los puertos correspondientes desde el comando `kubectl get all`. Con la dirección IP y el puerto, abra un explorador web y vaya a `http://<external-ip>:<port>:/swagger/index.html` para ver las páginas de Swagger de API.

## <a name="customize-helm-charts"></a>Personalizar gráficos de Helm

Los gráficos de Helm son jerárquicos. La jerarquía permite la herencia de gráficos y, además, proporciona el concepto de especificidad, que consiste en que los valores más específicos invalidan las reglas heredadas.

[!INCLUDE [Speech umbrella-helm-chart-config](includes/speech-umbrella-helm-chart-config.md)]

[!INCLUDE [Speech-to-Text Helm Chart Config](includes/speech-to-text-chart-config.md)]

[!INCLUDE [Text-to-Speech Helm Chart Config](includes/text-to-speech-chart-config.md)]

## <a name="next-steps"></a>Pasos siguientes

Para obtener más información sobre la instalación de aplicaciones con Helm en Azure Kubernetes Service (AKS), [visite esta página][installing-helm-apps-in-aks].

> [!div class="nextstepaction"]
> [Contenedores de Cognitive Services][cog-svcs-containers]

<!-- LINKS - external -->
[free-azure-account]: https://azure.microsoft.com/free
[git-download]: https://git-scm.com/downloads
[azure-cli]: /cli/azure/install-azure-cli
[docker-engine]: https://www.docker.com/products/docker-engine
[kubernetes-cli]: https://kubernetes.io/docs/tasks/tools/install-kubectl
[helm-install]: https://helm.sh/docs/intro/install/
[helm-install-cmd]: https://helm.sh/docs/intro/using_helm/#helm-install-installing-a-package
[tiller-install]: https://helm.sh/docs/install/#installing-tiller
[helm-charts]: https://helm.sh/docs/topics/charts/
[kubectl-create]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#create
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[helm-test]: https://v2.helm.sh/docs/helm/#helm-test
[ms-helm-hub]: https://hub.helm.sh/charts/microsoft
[ms-helm-hub-speech-chart]: https://hub.helm.sh/charts/microsoft/cognitive-services-speech-onpremise

<!-- LINKS - internal -->
[speech-container-host-computer]: speech-container-howto.md#host-computer-requirements-and-recommendations
[installing-helm-apps-in-aks]: ../../aks/kubernetes-helm.md
[cog-svcs-containers]: ../cognitive-services-container-support.md