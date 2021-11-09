---
title: Implementación de cargas de trabajo de Azure IoT Edge
services: azure-arc
ms.service: azure-arc
ms.date: 03/03/2021
ms.topic: article
description: Implementación de cargas de trabajo de Azure IoT Edge
keywords: Kubernetes, Arc, Azure, K8s, contenedores
ms.openlocfilehash: 9870e6ce0904510c549033dfd64de9d377b886af
ms.sourcegitcommit: 61f87d27e05547f3c22044c6aa42be8f23673256
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/09/2021
ms.locfileid: "132056423"
---
# <a name="deploy-azure-iot-edge-workloads"></a>Implementación de cargas de trabajo de Azure IoT Edge

## <a name="overview"></a>Información general

Azure Arc y Azure IoT Edge complementan fácilmente sus correspondientes funcionalidades. 

Azure Arc proporciona mecanismos para que los operadores de clúster configuren los componentes básicos de un clúster, así como para aplicar directivas de clúster. 

Azure IoT Edge permite a los operadores de aplicaciones implementar y administrar de forma remota las cargas de trabajo a escala con una cómoda ingesta en la nube y primitivas de comunicación bidireccional. 

En el diagrama siguiente se muestra la relación entre Azure Arc y Azure IoT Edge:

![Configuración de Arc de IoT](./media/edge-arc.png)

## <a name="pre-requisites"></a>Requisitos previos

* [Registre un dispositivo IoT Edge](../../iot-edge/quickstart-linux.md#register-an-iot-edge-device) e [implemente el módulo de sensor de temperatura simulado](../../iot-edge/quickstart-linux.md#deploy-a-module). Anote la cadena de conexión del dispositivo para los *valores.yaml* que se mencionan a continuación.

* Use [la compatibilidad con Kubernetes de IoT Edge](https://aka.ms/edgek8sdoc) para implementarlo a través del operador Flux de Azure Arc.

* Descargue el archivo [*values.yaml*](https://github.com/Azure/iotedge/blob/preview/iiot/kubernetes/charts/edge-kubernetes/values.yaml) para el gráfico Helm de IoT Edge y reemplace el marcador de posición `deviceConnectionString` al final del archivo la cadena de conexión que anotó en el paso anterior. Establezca cualquier otra opción de instalación de gráficos admitida según sea necesario. Cree un espacio de nombres para la carga de trabajo de IoT Edge y genere un secreto en él:

  ```
  $ kubectl create ns iotedge

  $ kubectl create secret generic dcs --from-file=fully-qualified-path-to-values.yaml --namespace iotedge
  ```

  También se puede establecer de forma remota mediante el [ejemplo de configuración de clúster](./tutorial-use-gitops-connected-cluster.md).

## <a name="connect-a-cluster"></a>Conexión de un clúster

Use la extensión `connectedk8s` de la CLI de Azure `az` para conectar un clúster de Kubernetes a Azure Arc:

  ```
  az connectedk8s connect --name AzureArcIotEdge --resource-group AzureArcTest
  ```

## <a name="create-a-configuration-for-iot-edge"></a>Creación de una configuración para IoT Edge

El [repositorio de Git de ejemplo](https://github.com/veyalla/edgearc) apunta al gráfico de Helm de IoT Edge y hace referencia al secreto creado en la sección de requisitos previos.

Use la extensión `k8s-configuration` de la CLI de Azure `az` a fin de crear una configuración que vincule el clúster conectado al repositorio de Git:

  ```
  az k8s-configuration create --name iotedge --cluster-name AzureArcIotEdge --resource-group AzureArcTest --operator-instance-name iotedge --operator-namespace azure-arc-iot-edge --enable-helm-operator --helm-operator-chart-version 0.6.0 --helm-operator-chart-values "--set helm.versions=v3" --repository-url "git://github.com/veyalla/edgearc.git" --cluster-scoped
  ```

En unos minutos, debería ver los módulos de la carga de trabajo de IoT Edge implementados en el espacio de nombres `iotedge` del clúster. 

Visualice los registros del pod `SimulatedTemperatureSensor` en ese espacio de nombres para ver los valores de ejemplo que se generan. También puede ver los mensajes que llegan al centro de IoT mediante la [extensión Azure IoT Hub Toolkit para Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-toolkit).

## <a name="cleanup"></a>Limpieza

Quite la configuración mediante:

```
az k8s-configuration delete -g AzureArcTest --cluster-name AzureArcIotEdge --name iotedge
```

## <a name="next-steps"></a>Pasos siguientes

Aprenda a [usar Azure Policy para controlar la configuración del clúster](./use-azure-policy.md).
