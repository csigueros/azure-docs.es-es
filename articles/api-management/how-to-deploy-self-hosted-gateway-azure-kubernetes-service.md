---
title: Implementación de una puerta de enlace autohospedada en Azure Kubernetes Service
description: Aprenda a implementar un componente de puerta de enlace autohospedada de Azure API Management en Azure Kubernetes Service
author: dlepow
manager: gwallace
ms.service: api-management
ms.topic: article
ms.date: 06/11/2021
ms.author: danlep
ms.openlocfilehash: a5faca1d91596021e236e2bcb459679085ee99aa
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/24/2021
ms.locfileid: "128620870"
---
# <a name="deploy-to-azure-kubernetes-service"></a>Implementación en Azure Kubernetes Service

En este artículo se detallan los pasos para implementar un componente de puerta de enlace autohospedada de Azure API Management en [Azure Kubernetes Service](https://azure.microsoft.com/services/kubernetes-service/). Para implementar una puerta de enlace autohospedada en un clúster de Kubernetes, vea el [artículo de la guía paso a paso](how-to-deploy-self-hosted-gateway-kubernetes.md).

> [!NOTE]
> También puede implementar una puerta de enlace autohospedada en un [clúster de Kubernetes habilitado para Azure Arc](how-to-deploy-self-hosted-gateway-azure-arc.md) como una [extensión de clúster](../azure-arc/kubernetes/extensions.md).

## <a name="prerequisites"></a>Prerrequisitos

- [Creación de una instancia de Azure API Management](get-started-create-service-instance.md)
- [Creación de un clúster de Azure Kubernetes](../aks/kubernetes-walkthrough-portal.md)
- [Aprovisione un recurso de puerta de enlace en la instancia de API Management](api-management-howto-provision-self-hosted-gateway.md).

## <a name="deploy-the-self-hosted-gateway-to-aks"></a>Implementación de la puerta de enlace autohospedada en AKS

1. Seleccione **Puertas de enlace** en **Deployment and infrastructure** (Implementación e infraestructura).
2. Seleccione el recurso de puerta de enlace autohospedada que desea implementar.
3. Seleccione **Implementación**.
4. En el cuadro de texto **Token** se ha generado automáticamente un nuevo token con los valores predeterminados de **Expiración** y **Clave secreta**. Ajuste uno o ambos si lo desea y seleccione **Generar** para crear un nuevo token.
5. Asegúrese de que **Kubernetes** esté seleccionado en **Scripts de implementación**.
6. Seleccione el vínculo del archivo **\<gateway-name\>.yml** que está junto a **Implementación** para descargar el archivo.
7. Ajuste `config.service.endpoint`, las asignaciones de puertos y el nombre del contenedor en el archivo .yml según sea necesario.
8. En función de su escenario, puede que tenga que cambiar el [tipo de servicio](../aks/concepts-network.md#services). 
    * El valor predeterminado es `LoadBalancer`, que es el equilibrador de carga externo. 
    * Puede usar el [equilibrador de carga interno](../aks/internal-lb.md) para restringir el acceso a la puerta de enlace autohospedada solo a usuarios internos. 
    * En el ejemplo siguiente se usa `NodePort`.
1. Seleccione el icono **copiar** situado en el extremo derecho del cuadro de texto **Implementar** para guardar el comando `kubectl` en el portapapeles.
1. Pegue el comando en la ventana de terminal (o comando). El comando espera que el archivo de entorno descargado esté presente en el directorio actual.

   ```console
   kubectl apply -f <gateway-name>.yaml
   ```
   
1. Ejecute el comando. El comando indica al clúster de AKS que:
    * Ejecute el contenedor mediante la imagen de puerta de enlace autohospedada descargada de Microsoft Container Registry. 
    * Configure el contenedor para exponer los puertos HTTP (8080) y HTTPS (443).
1. Ejecute el siguiente comando para comprobar que el pod de puerta de enlace se está ejecutando. El nombre del pod será diferente.

   ```console
   kubectl get pods
   NAME                                   READY     STATUS    RESTARTS   AGE
   contoso-apim-gateway-59f5fb94c-s9stz   1/1       Running   0          1m
   ```

1. Ejecute el siguiente comando para comprobar que el servicio de puerta de enlace se está ejecutando. El nombre del servicio y las direcciones IP serán diferentes.
    ```console
    kubectl get services
    NAME             TYPE        CLUSTER-IP      EXTERNAL-IP   PORT(S)                      AGE
    contosogateway   NodePort    10.110.230.87   <none>        80:32504/TCP,443:30043/TCP   1m
    ```
1. Vuelva a Azure Portal y confirme que el nodo de puerta de enlace que ha implementado notifica que su estado es correcto.

> [!TIP]
> Use el comando `kubectl logs <gateway-pod-name>` para ver una instantánea del registro de la puerta de enlace autohospedada.

## <a name="next-steps"></a>Pasos siguientes

* Para más información sobre la puerta de enlace autohospedada, consulte [Introducción a la puerta de enlace autohospedada de Azure API Management](self-hosted-gateway-overview.md).
* Aprenda [cómo implementar una puerta de enlace de API Management autohospedada en clústeres de Kubernetes habilitados para Azure Arc](how-to-deploy-self-hosted-gateway-azure-arc.md).
* Obtenga más información acerca de [Azure Kubernetes Service](../aks/intro-kubernetes.md).
* Obtenga información sobre [cómo configurar y conservar los registros en la nube](how-to-configure-cloud-metrics-logs.md).
* Obtenga información sobre [cómo configurar y conservar los registros localmente](how-to-configure-local-metrics-logs.md).
