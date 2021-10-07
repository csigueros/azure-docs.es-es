---
title: Redes de Kubernetes en Azure Stack Edge Pro con GPU
description: Se describe cómo funcionan las redes de Kubernetes en un dispositivo Azure Stack Edge Pro con GPU.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: conceptual
ms.date: 09/27/2021
ms.author: alkohli
ms.openlocfilehash: a969486194f66be5c7ce4c3973352ca036a025f8
ms.sourcegitcommit: 61e7a030463debf6ea614c7ad32f7f0a680f902d
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/28/2021
ms.locfileid: "129093124"
---
# <a name="kubernetes-networking-on-azure-stack-edge-pro-gpu-device"></a>Redes de Kubernetes en un dispositivo Azure Stack Edge Pro con GPU

[!INCLUDE [applies-to-GPU-and-pro-r-and-mini-r-skus](../../includes/azure-stack-edge-applies-to-gpu-pro-r-mini-r-sku.md)]

En un dispositivo Azure Stack Edge Pro con GPU, se crea un clúster de Kubernetes al configurar el rol de proceso. Una vez creado el clúster de Kubernetes, las aplicaciones en contenedor se pueden implementar en el clúster de Kubernetes en pods. Hay distintas maneras de usar las funciones de red para los pods en el clúster de Kubernetes. 

En este artículo se describen las redes en un clúster de Kubernetes en general y específicamente en el contexto de un dispositivo Azure Stack Edge Pro con GPU. 

## <a name="networking-requirements"></a>Requisitos de red

Este es un ejemplo de una aplicación típica de dos niveles que se implementa en el clúster de Kubernetes.

- La aplicación tiene un front-end de servidor web y una aplicación de bases de datos en el back-end. 
- A cada pod se le asigna una dirección IP, pero estas direcciones IP pueden cambiar tras el reinicio y la conmutación por error del pod. 
- Cada aplicación se compone de varios pods, y debe haber un equilibrio de carga del tráfico entre todas las réplicas de los pods. 

![Requisitos de red de Kubernetes](./media/azure-stack-edge-gpu-kubernetes-networking/kubernetes-networking-1-m.png)

El escenario anterior da como resultado los siguientes requisitos de red:

 - Existe la necesidad de que el usuario de una aplicación orientada al exterior acceda a la aplicación fuera del clúster de Kubernetes mediante un nombre o una dirección IP. 
 - Las aplicaciones en el clúster de Kubernetes, por ejemplo, los pods de front-end y back-end, deben ser capaces de comunicarse entre sí.

Para resolver ambas necesidades anteriores, se introduce un servicio de Kubernetes. 


## <a name="networking-services"></a>Servicios de red

Existen dos tipos de servicios de Kubernetes: 

- **Servicio IP del clúster**: Considere este servicio como si proporcionara un punto de conexión constante para los pods de aplicación. No se puede tener acceso a ningún pod asociado a estos servicios desde fuera del clúster de Kubernetes. La dirección IP usada con estos servicios procede del espacio de direcciones de la red privada. 
    
    Para exponer los pods dentro del clúster de Kubernetes para su acceso como otros pods y no como un servicio de equilibrador de carga expuesto externamente, consulte cómo [exponer un servicio Kubernetes como servicio IP del clúster para la comunicación interna]().

- **IP del equilibrador de carga**: Como el servicio IP del clúster, pero la dirección IP asociada procede de la red externa y se puede acceder a ella desde fuera del clúster de Kubernetes.


<!--## Networking example for an app


![Kubernetes networking example](./media/azure-stack-edge-gpu-kubernetes-networking/kubernetes-networking-2.png)

Each of these applications pods has a label associated with it. For example, the web server application pods have a label `app = WS` and the service has a label selector which the same as `app = WS`. Whenever a service of type load balancer or cluster IP is created, there is a control loop that runs in the master and publishes an endpoint corresponding to this service. This service uses a combination of labels and label selectors to discover the pods associated with this service. As a pod gets created, the new endpoint for the pod is added to the endpoint mapping. Whenever a pod is deleted, it gets deleted from the endpoint mapping. Using this endpoint controller, the service has a most up-to-date view of the pods that make up this application.

For discovery of applications within the cluster, Kubernetes cluster has an inbuilt DNS server pod. This is a cluster DNS that resolves service names to cluster IP. Anytime a cluster IP service is created, a DNS record is added to the DNS server that maps the name of the service to the cluster internal IP. That is how the applications within the cluster can discover each other. For load balancing, there is also the `kube-proxy`. This runs on every node and captures the traffic that comes in through the cluster IP and then distributes the traffic across the pods. 

When an application or the end user would first use the IP address associated with the service of type load balancer to discover the service. Then it would use the label select `app = WS` to discover the pods associated with the application. The `kube-proxy` component would then distribute the traffic and ensure that it hits one of the web server application pods. If the web server app wanted to talk to the database app, then it would simply use the name of the service and using the name and the DNS server pod, resolve the name to an IP address. Again using labels and label selector, it would discover the pods associated with the database application. The `kube-proxy` would then distribute the traffic across each of the database app nodes.-->

## <a name="kubernetes-network-configuration"></a>Configuración de red de Kubernetes

Las direcciones IP que se usan para los nodos de Kubernetes y los servicios externos se proporcionan mediante la página **Proceso** en la interfaz de usuario local del dispositivo.

![Asignación de la dirección IP de Kubernetes en la interfaz de usuario local](./media/azure-stack-edge-gpu-kubernetes-networking/kubernetes-ip-assignment-local-ui-1.png)

La asignación de IP es para:

- **Kubernetes node IPs** (Direcciones IP del nodo de Kubernetes): Este intervalo IP se usa para los nodos maestro y de trabajo de Kubernetes. Estas direcciones IP se usan cuando los nodos de Kubernetes se comunican entre sí.
- **Kubernetes external service IPs** (Direcciones IP del servicio externo de Kubernetes): Este intervalo IP se usa para los servicios externos (también conocidos como servicios del equilibrador de carga) que se exponen fuera del clúster de Kubernetes.

## <a name="kubernetes-networking-components"></a>Componentes de red de Kubernetes

Calico, Metallb y Core DNS son todos los componentes que se instalan para las redes en el dispositivo Azure Stack Edge Pro con GPU. 

- **Calico** asigna una dirección IP de un intervalo IP privado a cada pod, y configura las redes para estos pods de modo que el pod de un nodo pueda comunicarse con el pod de otro nodo. 
- **Metallb** se ejecuta en un pod en el clúster y asigna la dirección IP a servicios de tipo `load balancer`. Las direcciones IP del equilibrador de carga se eligen del intervalo IP del servicio, proporcionado por medio de la interfaz de usuario local. 
- **Core DNS**: Este complemento configura el nombre del servicio de asignación de registros DNS a la dirección IP del clúster.

Al conectarse a la interfaz de PowerShell del dispositivo, puede ver los componentes de red anteriores que se ejecutan en el clúster de Kubernetes.

## <a name="network-interfaces-switches"></a>Interfaces de red, conmutadores 

El dispositivo está disponible como una configuración de un nodo que constituye el clúster de infraestructura. El clúster de Kubernetes es independiente del clúster de infraestructura y se implementa a partir del clúster de infraestructura. El clúster de Kubernetes tiene un nodo maestro y un nodo de trabajo. Ambos nodos de Kubernetes son máquinas virtuales que ejecutan las aplicaciones y los flujos de trabajo en la nube.

Las máquinas virtuales maestra y de trabajo tienen dos interfaces de red, una que se conecta al conmutador virtual interno y otra que se conecta al conmutador virtual externo. 

- **Conmutador virtual externo:** este conmutador se crea cuando se habilita un puerto de dispositivo para el proceso por medio de la página **Proceso** en la interfaz de usuario local. Este es el conmutador que se usa para la infraestructura de proceso; por ejemplo, se usa para las máquinas virtuales que se implementan en el dispositivo. 
- **Conmutador virtual interno:** este conmutador se crea como parte de la configuración predeterminada de fábrica en el dispositivo. El conmutador virtual interno usa traducción de direcciones de red (NAT) para enrutar el tráfico que pasa por el puerto configurado con una puerta de enlace predeterminada. Por ejemplo, este conmutador enruta todas las solicitudes en tiempo de ejecución de IoT de las máquinas virtuales a Azure Portal. 

   <!--![Azure Stack Edge networking simplified diagram](./media/azure-stack-edge-gpu-kubernetes-networking/azure-stack-edge-networking-0.png)-->

## <a name="network-routes"></a>Rutas de red 

Para las máquinas virtuales de Kubernetes del dispositivo, puede enrutar el tráfico agregando una nueva configuración de ruta. Una configuración de ruta es una entrada en la tabla de enrutamiento que incluye los campos siguientes:

| Parámetro     | Descripción                                                                              |
|---------------|------------------------------------------------------------------------------------------|
| Destination   | Una dirección IP o un prefijo de dirección IP.                                            |
| Longitud del prefijo | La longitud del prefijo correspondiente a la dirección o el intervalo de direcciones en el destino. |
| Próximo salto      | Dirección IP a la que se reenvía el paquete.                                         |
| Interfaz     | Interfaz de red que reenvía el paquete IP.                                       |
| Métrica        | La métrica de enrutamiento determina la interfaz de red preferida que se usa para llegar al destino. |


## <a name="change-routing-on-compute-network"></a>Cambio del enrutamiento en la red de proceso

Use el cmdlet `Add-HcsNetRoute` para modificar el enrutamiento en las máquinas virtuales maestra y de trabajo de Kubernetes. Tenga en cuenta el diseño del diagrama siguiente. 

![Diagrama de redes de Azure Stack Edge](./media/azure-stack-edge-gpu-kubernetes-networking/azure-stack-edge-networking-1.png)

- El puerto 2 está conectado a Internet y es la ruta de acceso deseada para el tráfico saliente. 
- Ha habilitado el proceso en el puerto 3 y esto ha creado un conmutador virtual externo en esta interfaz de red. 
- El puerto 3 está conectado a una red privada que tiene cámaras y otros sensores que envían datos sin procesar al dispositivo Azure Stack Edge para su procesamiento. 


Si configura una puerta de enlace en su entorno de la red privada, considere la posibilidad de establecer rutas personalizadas para las máquinas virtuales maestra y de trabajo de Kubernetes de modo que puedan comunicarse con la puerta de enlace solo para el tráfico pertinente. Esto le permite tener el control del tráfico que fluye en la red de proceso frente a los otros puertos que puede haber configurado en el dispositivo Azure Stack Edge. Por ejemplo, es posible que quiera que todo el tráfico orientado a Internet fluya por los demás puertos físicos del dispositivo. En este caso, el tráfico orientado a Internet puede pasar por el puerto 2. 

También debe tener en cuenta estas otras consideraciones:

- Si tiene una subred plana, no es necesario agregar estas rutas a la red privada. Opcionalmente, puede agregar estas rutas cuando haya varias subredes en la red privada.
- Puede agregar estas rutas solo a las máquinas virtuales maestra y de trabajo de Kubernetes y no al dispositivo (host de Windows).
- No es necesario configurar el proceso de Kubernetes antes de agregar esta ruta. También puede agregar o actualizar rutas después de configurar el proceso de Kubernetes. 
- Solo puede agregar una nueva configuración de ruta por medio de la interfaz de PowerShell del dispositivo y no de la interfaz de usuario local.
- Asegúrese de que la interfaz de red que va a usar tiene una configuración estática.

## <a name="add-a-route-configuration"></a>Incorporación de una configuración de ruta

Para agregar una nueva ruta personalizada a la red privada, use el cmdlet como se muestra a continuación:

```powershell
Add-HcsNetRoute -InterfaceAlias <Port number> -DestinationPrefix <Destination IP address or IP address prefix> -NextHop <IP address of next hop> -RouteMetric <Route metric number> 
```
Esta es una salida de ejemplo.

```output
Add-HcsNetRoute -InterfaceAlias "Port3" -DestinationPrefix "192.168.20.0/24" -NextHop "192.168.20.1" -RouteMetric 100 
```

El comando anterior creará una entrada en la tabla de enrutamiento que define una subred de destino 192.168.20.0/24, especifica el próximo salto como 192.168.20.1 y asigna a esta entrada de enrutamiento una métrica de enrutamiento de 100. Cuanto menor sea la métrica de enrutamiento, mayor será la prioridad asignada a la ruta.

## <a name="check-route-configuration"></a>Comprobación de la configuración de rutas

Use este cmdlet para comprobar todas las configuraciones de ruta personalizadas que agregó en el dispositivo. Estas rutas no incluyen todas las rutas del sistema ni las rutas predeterminadas que ya existen en el dispositivo.

```powershell
Get-HcsNetRoute -InterfaceAlias <Port number>
```

## <a name="remove-a-route-configuration"></a>Eliminación de una configuración de ruta

Use este cmdlet para quitar una configuración de ruta que agregó en el dispositivo.

```powershell
Remove-HcsNetRoute -InterfaceAlias <Port number> -DestinationPrefix <Destination IP or IP prefix>
```

## <a name="routing-with-multiple-network-interfaces"></a>Enrutamiento con varias interfaces de red

Si hay varios puertos de dispositivo conectados, no se admite la formación de equipos NIC estándar ni Switch Embedded Teaming (SET) que permite agrupar varios adaptadores de red físicos en un único adaptador de red virtual en un entorno de Hyper-V.


## <a name="next-steps"></a>Pasos siguientes

Para configurar las redes de Kubernetes en un dispositivo Azure Stack Edge Pro con GPU, consulte:

- [Uso de un módulo IoT Edge para ejecutar una aplicación sin estado de Kubernetes en un dispositivo Azure Stack Edge Pro con GPU](azure-stack-edge-gpu-deploy-stateless-application-iot-edge-module.md)

- [Implementación de una aplicación sin estado de Kubernetes en un dispositivo Azure Stack Edge Pro con GPU por medio de kuebctl](./azure-stack-edge-gpu-deploy-stateless-application-kubernetes.md)
