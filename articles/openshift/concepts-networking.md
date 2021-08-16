---
title: 'Conceptos: diagrama de redes de Red Hat OpenShift en Azure 4'
description: Diagrama de redes e información general de las redes de Red Hat OpenShift en Azure
author: sakthi-vetrivel
ms.author: suvetriv
ms.topic: tutorial
ms.service: azure-redhat-openshift
ms.date: 11/23/2020
ms.openlocfilehash: 2e1825f80b5b2b185d5cf9d9ea75ea4e80ea0ef9
ms.sourcegitcommit: 6f21017b63520da0c9d67ca90896b8a84217d3d3
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 07/23/2021
ms.locfileid: "114653463"
---
# <a name="network-concepts-for-azure-red-hat-openshift-aro"></a>Conceptos de red de Red Hat OpenShift en Azure (ARO)

En esta guía se describe una introducción a las redes en clústeres de Red Hat OpenShift en Azure en OpenShift 4, junto con un diagrama y una lista de puntos de conexión importantes. Para más información sobre los conceptos básicos de las redes de OpenShift, consulte [Documentación de redes de Red Hat OpenShift en Azure 4](https://docs.openshift.com/container-platform/4.6/networking/understanding-networking.html).

![Diagrama de redes de Red Hat OpenShift en Azure 4](./media/concepts-networking/aro4-networking-diagram.png)

Al implementar Red Hat OpenShift en Azure 4, todo el clúster se encuentra dentro de una red virtual. Dentro de esta red virtual, los nodos principales y los nodos de trabajo tienen su propia subred. Cada subred usa un equilibrador de carga interno y un equilibrador de carga público.

## <a name="networking-components"></a>Componentes de red

En la lista siguiente se describen los componentes de redes más importantes de un clúster de Red Hat OpenShift en Azure.

* **aro-pls**
    * Es un punto de conexión de Azure Private Link, usado por los ingenieros de confiabilidad de sitios de Microsoft y Red Hat para administrar el clúster.
* **aro-internal-lb**
    * Este punto de conexión equilibra el tráfico al servidor de la API. Para este equilibrador de carga, los nodos maestros se encuentran en el grupo de back-end.
* **aro-public-lb**
    * Si la API es pública, este punto de conexión enruta y equilibra el tráfico al servidor de la API. Este punto de conexión asigna una dirección IP pública de salida para que los maestros puedan acceder a Azure Resource Manager e informar sobre el estado del clúster.
* **aro-internal**
    * Este punto de conexión equilibra el tráfico de servicio interno. Para este equilibrador de carga, los nodos de trabajo se encuentran en el grupo de back-end.
    * Este equilibrador de carga no se crea de forma predeterminada. Este equilibrador de carga se crea una vez que se crea un servicio de tipo equilibrador de carga con las anotaciones correctas. Por ejemplo: service.beta.kubernetes.io/azure-load-balancer-internal: "true".
* **aro-internal-lb**
    * Este punto de conexión se usa para cualquier tráfico público. Al crear una aplicación y una ruta, esta es la ruta para el tráfico de entrada.
    * Este equilibrador de carga también cubre la conectividad de salida a Internet desde cualquier pod que se ejecute en los nodos de trabajo mediante reglas de salida de Azure Load Balancer.
        * Actualmente no se pueden configurar las reglas de salida. Se asignan 1024 puertos TCP a cada nodo.
        * DisableOutboundSnat no está configurada en las reglas del equilibrador de carga, por lo que los pods podrían obtener como dirección IP de salida cualquier dirección IP pública configurada en este ALB.
        * Como consecuencia de los dos puntos anteriores, la única manera de agregar puertos SNAT efímeros es agregar a ARO servicios públicos de tipo equilibrador de carga.
* **aro-outbound-pip**
    * Este punto de conexión actúa como una dirección IP pública (PIP) para los nodos de trabajo.
    * Este punto de conexión permite a los servicios agregar a una lista de permitidos una dirección IP específica que procede de un clúster de Red Hat OpenShift en Azure.
* **aro-nsg**
    * Cuando se expone un servicio, la API crea una regla en este grupo de seguridad de red para que el tráfico fluya y tenga acceso al plano de control y los nodos.
    * De forma predeterminada, este grupo de seguridad de red permite todo el tráfico de salida. Actualmente, el tráfico de salida solo se puede restringir al plano de control de Red Hat OpenShift en Azure.
* **aro-controlplane-nsg**
  * Este punto de conexión solo permite que el tráfico entre por el puerto 6443 para los nodos maestros.
* **Azure Container Registry**
    * Se trata de un registro de contenedor que Microsoft proporciona y utiliza internamente. Este registro es de solo lectura y no está pensado para que lo utilicen los usuarios de Red Hat OpenShift en Azure.
        * Este registro proporciona las imágenes de la plataforma del host y los componentes del clúster. Por ejemplo, los contenedores de supervisión o registro.
        * Las conexiones a este registro se producen mediante el punto de conexión de servicio (conectividad interna entre los servicios de Azure).
        * Este registro interno no está disponible fuera del clúster de forma predeterminada.
* **Private Link**
    * Permite la conectividad de red desde el plano de administración en un clúster para que los ingenieros de confiabilidad de sitios de Microsoft y Red Hat ayuden a administrar el clúster.

## <a name="networking-policies"></a>Directivas de redes

* **Entrada**: la directiva de redes de entrada se admite como parte de [OpenShift SDN](https://docs.openshift.com/container-platform/4.5/networking/openshift_sdn/about-openshift-sdn.html). Esta directiva de red está habilitada de forma predeterminada y los usuarios llevan a cabo la aplicación. Aunque la directiva de red de entrada es compatible con V1 NetworkPolicy, no se admiten la salida ni los tipos IPBlock.

* **Salida**: las directivas de red de salida se admiten mediante la característica de [firewall de salida](https://docs.openshift.com/container-platform/4.5/networking/openshift_sdn/configuring-egress-firewall.html) de OpenShift. Solo hay una directiva de salida por espacio de nombres o proyecto. Las directivas de salida no se admiten en el espacio de nombres "default" (predeterminado) y se evalúan en orden (de la primera a la última).

## <a name="networking-basics-in-openshift"></a>Aspectos básicos de las redes en OpenShift

Las redes definidas por software [(SDN)](https://docs.openshift.com/container-platform/4.6/networking/openshift_sdn/about-openshift-sdn.html) de OpenShift se usan para configurar una red de superposición mediante Open vSwitch [(OVS)](https://www.openvswitch.org/), una implementación de OpenFlow basada en la especificación de la interfaz de red del contenedor (CNI). SDN admite distintos complementos: la directiva de red es el complemento que se usa en Red Hat OpenShift en Azure 4. SDN administra toda la comunicación de red, por lo que no se necesitan rutas adicionales en las redes virtuales para lograr la comunicación entre pod y pod.

## <a name="networking--for-azure-red-hat-openshift"></a>Redes para Red Hat OpenShift en Azure

Las siguientes características de redes son específicas de Red Hat OpenShift en Azure:  
* Los usuarios pueden crear su clúster de ARO en una red virtual existente o crear una red virtual al crear su clúster de ARO.
* Se pueden configurar los CIDR de la red de servicio y los pods.
* Los nodos y los maestros se encuentran en subredes diferentes.
* Las subredes de la red virtual de los nodos y los maestros deben ser /27 como mínimo.
* El CIDR del pod predeterminado es 10.128.0.0/14.
* El CIDR del servicio predeterminado es 172.30.0.0/16.
* Los CIDR de la red del servicio y el pod no deben solaparse con otros intervalos de direcciones que se utilicen en la red ni estar en el mismo intervalo de direcciones IP de la red virtual del clúster.
* El CIDR del pod debe tener un tamaño mínimo de /18. (La red del pod tiene direcciones IP no enrutables y solo se usa dentro del SDN de OpenShift).
* Cada nodo está asignado a una subred /23 (512 direcciones IP) para los pods. Este valor no puede modificarse.
* No se puede conectar un pod a varias redes.
* No se puede configurar una dirección IP estática de salida. (Esta es una característica de OpenShift. Para más información, consulte [Configuración de direcciones IP de salida](https://docs.openshift.com/container-platform/4.6/networking/openshift_sdn/assigning-egress-ips.html)).

## <a name="network-settings"></a>Configuración de red

La siguiente configuración de red está disponible para los clústeres de Red Hat OpenShift en Azure 4:

* **Visibilidad de la API**: establezca la visibilidad de la API mediante la ejecución del [comando az aro create](tutorial-create-cluster.md#create-the-cluster).
    * "Público": el servidor de la API es accesible para redes externas.
    * "Privado": el servidor de la API asignó una dirección IP privada de la subred de los maestros, solo accesible mediante las redes conectadas (redes virtuales emparejadas, otras subredes del clúster). Se creará una zona DNS privada en nombre del cliente.
* **Visibilidad de entrada**: establezca la visibilidad de la API mediante la ejecución del [comando az aro create](tutorial-create-cluster.md#create-the-cluster).
    * Las rutas "públicas" se establecerán de forma predeterminada en una instancia pública de Standard Load Balancer (se puede cambiar).
    * Las rutas "privadas" se establecerán de forma predeterminada en el equilibrador de carga interno (se puede cambiar).

## <a name="network-security-groups"></a>Grupos de seguridad de red
Los grupos de seguridad de red se crean en el grupo de recursos del nodo, que está bloqueado para los usuarios. Los grupos de seguridad de red se asignan directamente a las subredes, no a las NIC del nodo. Los grupos de seguridad de red son inmutables y los usuarios no tienen permisos para cambiarlos.

Con un servidor de API visible públicamente, no puede crear grupos de seguridad de red y asignarlos a las NIC.

## <a name="domain-forwarding"></a>Reenvío de dominios
Red Hat OpenShift en Azure usa CoreDNS. Se puede configurar el reenvío de dominios. No puede traer su propio sistema DNS a las redes virtuales. Para más información, consulte la documentación sobre [Uso del reenvío de DNS](https://docs.openshift.com/container-platform/4.6/networking/dns-operator.html#nw-dns-forward_dns-operator).

## <a name="whats-new-in-openshift-45"></a>Novedades de OpenShift 4.5

Con la compatibilidad de OpenShift 4.5, Red Hat OpenShift en Azure presenta algunos cambios importantes en la arquitectura. Estos cambios solo se aplican a los clústeres recién creados que ejecutan OpenShift 4.5. El proceso de actualización no modificará la arquitectura de redes en los clústeres existentes que se han actualizado a OpenShift 4.5. Los usuarios deberán volver a crear los clústeres para usar esta nueva arquitectura.

![Diagrama de redes de Red Hat OpenShift en Azure 4.5](./media/concepts-networking/aro-4-5-networking-diagram.png)

Como se incluye en el diagrama anterior, observará algunos cambios:
* Anteriormente, ARO usaba dos equilibradores de carga públicos: uno para el servidor de API y otro para el grupo de nodos de trabajo. Con esta actualización de la arquitectura, se ha consolidado en un solo equilibrador de carga. 
* Para reducir la complejidad, se han eliminado los recursos de dirección IP de salida dedicada.
* El plano de control de ARO ahora comparte el mismo grupo de seguridad de red que los nodos de trabajo de ARO.

Para más información sobre OpenShift 4.5, consulte [Notas de la versión de OpenShift 4.5](https://docs.openshift.com/container-platform/4.5/release_notes/ocp-4-5-release-notes.html).

## <a name="next-steps"></a>Pasos siguientes
Para más información sobre el tráfico de salida y lo que admite Red Hat OpenShift en Azure para la salida, consulte la documentación de [compatibilidad de directivas](support-policies-v4.md).
