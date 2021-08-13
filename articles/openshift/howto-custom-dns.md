---
title: Configuración de recursos DNS personalizados en un clúster Red Hat OpenShift en Azure (ARO)
description: Descubra cómo agregar un servidor DNS personalizado en todos los nodos de Red Hat OpenShift en Azure (ARO).
author: bryanro92
ms.author: suvetriv
ms.service: azure-redhat-openshift
ms.topic: article
ms.date: 06/02/2021
ms.openlocfilehash: 842e0a4d57254c4ec27bdadf2ff168fe2f4c0424
ms.sourcegitcommit: 070122ad3aba7c602bf004fbcf1c70419b48f29e
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 06/04/2021
ms.locfileid: "111442248"
---
# <a name="configure-custom-dns-for-your-azure-red-hat-openshift-aro-cluster"></a>Configuración de DNS personalizado para un clúster Red Hat OpenShift en Azure (ARO)

En este artículo se proporcionan los detalles necesarios que permiten configurar el clúster de Red Hat OpenShift en Azure (ARO) para que use un servidor DNS personalizado. Contiene los requisitos del clúster para una implementación básica de ARO.

## <a name="before-you-begin"></a>Antes de empezar

En este artículo se da por supuesto que va a crear un nuevo clúster o que tiene un clúster existente donde se han aplicado las actualizaciones más recientes. Si necesita un clúster de ARO, consulte el artículo de [inicio rápido de ARO](./tutorial-create-cluster.md) para obtener un clúster público, o el [tutorial de clústeres privados](./howto-create-private-cluster-4x.md) para un clúster privado. Estos pasos para configurar el clúster para usar un servidor DNS personalizado son los mismos para los clústeres tanto privados como públicos.

### <a name="confirm-cluster-compatibility-with-custom-dns"></a>Confirmación de la compatibilidad del clúster con un DNS personalizado

Para confirmar que el clúster es apto para admitir esta característica, valide la existencia de `99-master-aro-dns` y `99-worker-aro-dns` `machineconfigs`.

```
oc get machineconfig
```

Si los resultados del comando anterior incluyen las siguientes machineconfigs, el clúster es apto para la compatibilidad con un DNS personalizada.

```
NAME                 GENERATEDBYCONTROLLER                      IGNITIONVERSION   AGE
...
99-master-aro-dns                                               2.2.0             54d
99-worker-aro-dns                                               2.2.0             54d
...
```

## <a name="dns-architecture-overview"></a>Información general sobre la arquitectura de DNS

A medida que cada nodo del clúster de Red Hat OpenShift en Azure se enciende y se une a la red, DHCP configura la máquina virtual con información, como la dirección IP y el servidor DNS que se va a usar.

A continuación se muestra información general sobre el flujo de proceso de cómo se obtiene la configuración:

![DNS](media/concepts-networking/custom-dns-pfd.jpg)

Una concesión importante que se hace al usar su propio servidor DNS en lugar del servidor DNS predeterminado en la red virtual es que se pierde la configuración proporcionada por el servidor DNS. Los nombres de las máquinas virtuales ya no se resolverán a través de DNS en la red.

### <a name="update-process-overview"></a>Información general sobre el proceso de actualización

La configuración de un servidor DNS personalizado para el clúster se divide en dos pasos.

1. Modificación de los valores de configuración de los servidores DNS de la red virtual.
2. Reinicio de los nodos en el clúster para realizar los cambios.


## <a name="configure-a-custom-dns-server"></a>Configuración de un servidor DNS personalizado

Los pasos siguientes también se pueden realizar a través de la línea de comandos, pero en esta documentación se hace mediante la interfaz web del portal.

### <a name="update-dns-configuration-in-virtual-network"></a>Actualización de la configuración de DNS en la red virtual

Inicie sesión en Azure Portal y vaya a la red virtual que quiera actualizar. Seleccione **Servidores DNS** en la lista de configuración de redes virtuales.

![Selección de DNS](media/concepts-networking/vnet-dns-config.png)

Una vez que se encuentra en la pantalla de configuración de DNS, seleccione **Personalizado** en la configuración del botón de radio. Escriba las direcciones IP de los servidores DNS.

>[!IMPORTANT]
> Si decide especificar un servidor DNS personalizado, ya no podrá resolver los nombres de los nodos en la red virtual a través de DNS. Solo se podrá acceder a los nodos a través de la dirección IP.

![Especificación de servidores DNS personalizados](media/concepts-networking/vnet-custom-dns.png)

Seleccione **Guardar**.

>[!NOTE]
> Como se muestra en la interfaz del portal, tiene que reiniciar todas las máquinas virtuales para que los cambios tengan lugar.

Debería recibir una notificación de que la actualización se ha completado correctamente.

![Confirmación de cambios de DNS](media/concepts-networking/vnet-dns-confirm-saved.png)

### <a name="gracefully-reboot-your-cluster"></a>Reinicio correcto del clúster

Estos pasos requieren tener un kubeconfig válido en el clúster, consulte este [tutorial](./tutorial-connect-cluster.md) para obtener más información sobre cómo obtener un kubeconfig.

Los fragmentos de código siguientes crean `machineconfig` noop para nodos maestro y de trabajo. Esto le permite iniciar reinicios graduales para los nodos de trabajo o maestro. Para más información sobre Machine Config Operator (MCO), consulte el [código fuente](https://github.com/openshift/machine-config-operator) o la [documentación de OpenShift para MCO ](https://docs.openshift.com/container-platform/4.6/architecture/control-plane.html).
#### <a name="machineconfig-definitions"></a>Definiciones de MachineConfig

Reinicios del trabajo:

```
apiVersion: machineconfiguration.openshift.io/v1
kind: MachineConfig
metadata:
  labels:
    machineconfiguration.openshift.io/role: worker
  name: 25-machineconfig-worker-reboot
spec:
  config:
    ignition:
      version: 2.2.0
    storage:
      files:
      - contents:
          source: data:text/plain;charset=utf-8;base64,cmVzdGFydAo=
        filesystem: root
        mode: 0644
        path: /etc/mco-noop-worker-restart.txt
```

Reinicios del maestro:

```
apiVersion: machineconfiguration.openshift.io/v1
kind: MachineConfig
metadata:
  labels:
    machineconfiguration.openshift.io/role: master
  name: 25-machineconfig-master-reboot
spec:
  config:
    ignition:
      version: 2.2.0
    storage:
      files:
      - contents:
          source: data:text/plain;charset=utf-8;base64,cmVzdGFydAo=
        filesystem: root
        mode: 0644
        path: /etc/mco-master-noop-restart.txt
```

#### <a name="reboot-worker-nodes"></a>Reinicio de nodos de trabajo

Cree el archivo de reinicio de trabajos, en este ejemplo se llama al archivo `worker-restarts.yml` y se lo aplica.

```
[user@bastion ~]$ vim worker-restarts.yml
[user@bastion ~]$ oc apply -f worker-restarts.yml
machineconfig.machineconfiguration.openshift.io/25-machineconfig-worker-reboot created
```

MCO moverá las cargas de trabajo y, luego, reiniciará cada nodo uno a uno. Una vez que los trabajos vuelvan a estar en línea, se seguirá el mismo procedimiento para reiniciar los nodos maestros. Para comprobar el estado de los trabajos, puede consultar los nodos y validar que todos tengan el estado `Ready`.

>[!NOTE]
> Según el tamaño de la carga de trabajo que tenga el clúster, cada nodo puede tardar varios minutos en reiniciarse.


Nodos de trabajo de ejemplo que no están completamente listos:

```
NAME                                  STATUS                     ROLES    AGE     VERSION
dns-docs-tm45t-master-0               Ready                      master   5h40m   v1.19.0+a5a0987
dns-docs-tm45t-master-1               Ready                      master   5h40m   v1.19.0+a5a0987
dns-docs-tm45t-master-2               Ready                      master   5h40m   v1.19.0+a5a0987
dns-docs-tm45t-worker-eastus1-8t6q8   Ready                      worker   5h35m   v1.19.0+a5a0987
dns-docs-tm45t-worker-eastus2-ln2kq   Ready,SchedulingDisabled   worker   5h34m   v1.19.0+a5a0987
dns-docs-tm45t-worker-eastus3-gg75h   Ready                      worker   5h35m   v1.19.0+a5a0987
```

Cuando se reinicie el nodo, verá que cambia al estado NotReady:

```
dns-docs-tm45t-worker-eastus2-ln2kq   NotReady,SchedulingDisabled   worker   5h38m   v1.19.0+a5a0987
```

Totalmente listo:

```
[user@bastion ~]$ oc get nodes
NAME                                  STATUS   ROLES    AGE     VERSION
dns-docs-tm45t-master-0               Ready    master   5h45m   v1.19.0+a5a0987
dns-docs-tm45t-master-1               Ready    master   5h46m   v1.19.0+a5a0987
dns-docs-tm45t-master-2               Ready    master   5h46m   v1.19.0+a5a0987
dns-docs-tm45t-worker-eastus1-8t6q8   Ready    worker   5h41m   v1.19.0+a5a0987
dns-docs-tm45t-worker-eastus2-ln2kq   Ready    worker   5h40m   v1.19.0+a5a0987
dns-docs-tm45t-worker-eastus3-gg75h   Ready    worker   5h41m   v1.19.0+a5a0987
```

#### <a name="reboot-master-nodes"></a>Reinicio de nodos maestros

Ahora, repita el mismo proceso para los nodos maestros:

```
[user@bastion ~]$ vim master-restarts.yml
[user@bastion ~]$ oc apply -f master-restarts.yml
machineconfig.machineconfiguration.openshift.io/25-machineconfig-master-reboot created
```

Confirme que todos los nodos hayan vuelto al estado `Ready`:

```
[user@bastion ~]$ oc get nodes
NAME                                  STATUS   ROLES    AGE    VERSION
dns-docs-tm45t-master-0               Ready    master   6h8m   v1.19.0+a5a0987
dns-docs-tm45t-master-1               Ready    master   6h8m   v1.19.0+a5a0987
dns-docs-tm45t-master-2               Ready    master   6h8m   v1.19.0+a5a0987
dns-docs-tm45t-worker-eastus1-8t6q8   Ready    worker   6h3m   v1.19.0+a5a0987
dns-docs-tm45t-worker-eastus2-ln2kq   Ready    worker   6h2m   v1.19.0+a5a0987
dns-docs-tm45t-worker-eastus3-gg75h   Ready    worker   6h3m   v1.19.0+a5a0987
```

#### <a name="confirm-changes-on-a-node-optional"></a>Confirmación de cambios en un nodo (opcional)

Para validar el nuevo servidor DNS en un nodo, usaremos el pod `oc debug`.

```
[user@bastion ~]$ oc debug node/dns-docs-tm45t-worker-eastus2-ln2kq
Starting pod/dns-docs-tm45t-worker-eastus2-ln2kq-debug ...
To use host binaries, run `chroot /host`
chroot Pod IP: 10.0.2.6
If you don't see a command prompt, try pressing enter.
sh-4.4# chroot /host
sh-4.4# uptime
 18:40:16 up 1 min,  0 users,  load average: 0.82, 0.32, 0.12
sh-4.4# cat /etc/resolv.conf.dnsmasq
# Generated by NetworkManager
search reddog.microsoft.com
nameserver 192.168.0.1
```
## <a name="modifying-the-custom-dns-server"></a>Modificación del servidor DNS personalizado

El procedimiento para modificar el DNS personalizado en un clúster que ya tiene implementado un DNS personalizado sigue el mismo [proceso](#update-process-overview).

### <a name="modify-dns"></a>Modificación del DNS

Siga el procedimiento descrito [aquí](#update-dns-configuration-in-virtual-network) para actualizar la configuración de DNS en la red virtual.

### <a name="reboot-nodes"></a>Reinicio de los nodos

En lugar de crear `machineconfig`, eliminaremos los `machineconfig` que creamos la primera vez. Comenzaremos con los nodos de trabajo.

```
oc delete machineconfig 25-machineconfig-worker-reboot
```

Salida:
```
machineconfig.machineconfiguration.openshift.io "25-machineconfig-worker-reboot" deleted
```

Espere a que se reinicien todos los nodos de trabajo. Esto es similar al [reinicio de los nodos de trabajo](#reboot-worker-nodes) anterior.

Ahora reiniciaremos los nodos maestros.

```
oc delete machineconfig 25-machineconfig-master-reboot
```

Salida:

```
machineconfig.machineconfiguration.openshift.io "25-machineconfig-master-reboot" deleted
```

Espere a que todos los nodos maestros se reinicien y vuelvan a un estado Ready.