---
title: Conexión SSH con los nodos de un clúster de Azure Kubernetes Service (AKS)
description: Aprenda a crear una conexión SSH con los nodos de clúster de Azure Kubernetes Service (AKS) para la solución de problemas y tareas de mantenimiento.
services: container-service
ms.topic: article
ms.date: 05/17/2021
ms.custom: contperf-fy21q4
ms.openlocfilehash: 87e548cd39c7c064b11131c28790d8335bd942fb
ms.sourcegitcommit: 34aa13ead8299439af8b3fe4d1f0c89bde61a6db
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/18/2021
ms.locfileid: "122418414"
---
# <a name="connect-with-ssh-to-azure-kubernetes-service-aks-cluster-nodes-for-maintenance-or-troubleshooting"></a>Conexión con SSH a los nodos de clúster de Azure Kubernetes Service (AKS) para mantenimiento o solución de problemas

Durante el ciclo de vida del clúster de Azure Kubernetes Service (AKS), es posible que necesite acceder a un nodo de AKS. Este acceso podría ser para mantenimiento, recopilación de registros u otras operaciones de solución de problemas. Puede acceder a los nodos de AKS mediante SSH, incluidos los nodos de Windows Server. También puede [conectarse a los nodos de Windows Server mediante conexiones de protocolo de escritorio remoto (RDP)][aks-windows-rdp]. Por motivos de seguridad, los nodos de AKS no están expuestos a Internet. Para conectarse mediante SSH a los nodos de AKS, use `kubectl debug` o la dirección IP privada.

En este artículo se muestra cómo crear una conexión SSH con un nodo de AKS.

## <a name="before-you-begin"></a>Antes de empezar

En este artículo se supone que ya tiene un clúster de AKS. Si necesita un clúster de AKS, consulte el inicio rápido de AKS [mediante la CLI de Azure][aks-quickstart-cli] o [mediante Azure Portal][aks-quickstart-portal].

En este artículo también se da por supuesto que tiene una clave SSH. Se pueden crear claves SSH en [macOS, Linux][ssh-nix] o [Windows][ssh-windows]. Si utiliza PuTTY Gen para crear el par de claves, guarde dicho par en un formato OpenSSH en lugar del formato de clave privada PuTTy predeterminado (archivo .ppk).

También es preciso tener instalada y configurada la versión 2.0.64 de la CLI de Azure u otra versión posterior. Ejecute `az --version` para encontrar la versión. Si necesita instalarla o actualizarla, vea [Instalación de la CLI de Azure][install-azure-cli].

## <a name="create-the-ssh-connection-to-a-linux-node"></a>Creación de la conexión SSH a un nodo de Linux

Para crear una conexión SSH a un nodo de AKS, use `kubectl debug` para ejecutar un contenedor con privilegios en el nodo. Para enumerar los nodos, use `kubectl get nodes`:

```output
$ kubectl get nodes -o wide

NAME                                STATUS   ROLES   AGE     VERSION   INTERNAL-IP   EXTERNAL-IP   OS-IMAGE                         KERNEL-VERSION     CONTAINER-RUNTIME
aks-nodepool1-12345678-vmss000000   Ready    agent   13m     v1.19.9   10.240.0.4    <none>        Ubuntu 18.04.5 LTS               5.4.0-1046-azure   containerd://1.4.4+azure
aks-nodepool1-12345678-vmss000001   Ready    agent   13m     v1.19.9   10.240.0.35   <none>        Ubuntu 18.04.5 LTS               5.4.0-1046-azure   containerd://1.4.4+azure
aksnpwin000000                      Ready    agent   87s     v1.19.9   10.240.0.67   <none>        Windows Server 2019 Datacenter   10.0.17763.1935    docker://19.3.1
```

Use `kubectl debug` para ejecutar una imagen de contenedor en el nodo y conectarse a ella.

```azurecli-interactive
kubectl debug node/aks-nodepool1-12345678-vmss000000 -it --image=mcr.microsoft.com/aks/fundamental/base-ubuntu:v0.0.11
```

Este comando inicia un contenedor con privilegios en el nodo y se conecta a él a través de SSH.

```output
$ kubectl debug node/aks-nodepool1-12345678-vmss000000 -it --image=mcr.microsoft.com/aks/fundamental/base-ubuntu:v0.0.11
Creating debugging pod node-debugger-aks-nodepool1-12345678-vmss000000-bkmmx with container debugger on node aks-nodepool1-12345678-vmss000000.
If you don't see a command prompt, try pressing enter.
root@aks-nodepool1-12345678-vmss000000:/#
```

Este contenedor con privilegios proporciona acceso al nodo.

## <a name="create-the-ssh-connection-to-a-windows-node"></a>Creación de la conexión SSH a un nodo de Windows

En este momento, no se puede conectar a un nodo de Windows Server mediante SSH directamente con `kubectl debug`. En su lugar, primero debe conectarse a otro nodo del clúster y, luego, conectarse al nodo de Windows Server desde ese nodo mediante SSH. Como alternativa, puede [conectarse a los nodos de Windows Server mediante conexiones RDP (protocolo de escritorio remoto)][aks-windows-rdp], en lugar de usar SSH.

Para conectarse a otro nodo del clúster, use `kubectl debug`. Para más información, consulte [Creación de la conexión SSH a un nodo de Linux][ssh-linux-kubectl-debug].

Para crear la conexión SSH al nodo de Windows Server desde otro nodo, use las claves SSH proporcionadas al crear el clúster de AKS y la dirección IP interna del nodo de Windows Server.

Abra una nueva ventana de terminal y use `kubectl get pods` para obtener el nombre del pod iniciado por `kubectl debug`.

```output
$ kubectl get pods

NAME                                                    READY   STATUS    RESTARTS   AGE
node-debugger-aks-nodepool1-12345678-vmss000000-bkmmx   1/1     Running   0          21s
```

En el ejemplo anterior, *node-debugger-aks-nodepool1-12345678-vmss000000-bkmmx* es el nombre del pod iniciado por `kubectl debug`.

Con `kubectl port-forward`, puede abrir una conexión al pod implementado:

```
$ kubectl port-forward node-debugger-aks-nodepool1-12345678-vmss000000-bkmmx 2022:22
Forwarding from 127.0.0.1:2022 -> 22
Forwarding from [::1]:2022 -> 22
```

En el ejemplo anterior se comienza a reenviar el tráfico de red desde el puerto 2022 del equipo de desarrollo al puerto 22 del pod implementado. Cuando se usa `kubectl port-forward` para abrir una conexión y reenviar el tráfico de red, la conexión permanece abierta hasta que se detiene el comando `kubectl port-forward`.

Abra un nuevo terminal y use `kubectl get nodes` para mostrar la dirección IP interna del nodo de Windows Server:

```output
$ kubectl get nodes -o wide

NAME                                STATUS   ROLES   AGE     VERSION   INTERNAL-IP   EXTERNAL-IP   OS-IMAGE                         KERNEL-VERSION     CONTAINER-RUNTIME
aks-nodepool1-12345678-vmss000000   Ready    agent   13m     v1.19.9   10.240.0.4    <none>        Ubuntu 18.04.5 LTS               5.4.0-1046-azure   containerd://1.4.4+azure
aks-nodepool1-12345678-vmss000001   Ready    agent   13m     v1.19.9   10.240.0.35   <none>        Ubuntu 18.04.5 LTS               5.4.0-1046-azure   containerd://1.4.4+azure
aksnpwin000000                      Ready    agent   87s     v1.19.9   10.240.0.67   <none>        Windows Server 2019 Datacenter   10.0.17763.1935    docker://19.3.1
```

En el ejemplo anterior, *10.240.0.67* es la dirección IP interna del nodo de Windows Server.

Cree una conexión SSH al nodo de Windows Server mediante la dirección IP interna. El nombre de usuario para los nodos de AKS es *azureuser*. Acepte el mensaje para continuar con la conexión. Luego, se le proporciona el símbolo del sistema de Bash del nodo de Windows Server:

```output
$ ssh -o 'ProxyCommand ssh -p 2022 -W %h:%p azureuser@127.0.0.1' azureuser@10.240.0.67

The authenticity of host '10.240.0.67 (10.240.0.67)' can't be established.
ECDSA key fingerprint is SHA256:1234567890abcdefghijklmnopqrstuvwxyzABCDEFG.
Are you sure you want to continue connecting (yes/no)? yes

[...]

Microsoft Windows [Version 10.0.17763.1935]
(c) 2018 Microsoft Corporation. All rights reserved.

azureuser@aksnpwin000000 C:\Users\azureuser>
```

En el ejemplo anterior se conecta al puerto 22 del nodo de Windows Server a través del puerto 2022 del equipo de desarrollo.

> [!NOTE]
> Si prefiere usar la autenticación con contraseña, use `-o PreferredAuthentications=password`. Por ejemplo:
>
> ```console
>  ssh -o 'ProxyCommand ssh -p 2022 -W %h:%p azureuser@127.0.0.1' -o PreferredAuthentications=password azureuser@10.240.0.67
> ```

## <a name="remove-ssh-access"></a>Eliminación del acceso SSH

Cuando haya finalizado, `exit` de la sesión de SSH y, a continuación, `exit` de la sesión del contenedor interactiva. Cuando la sesión de contenedor interactiva se cierra, se elimina el pod usado para el acceso SSH desde el clúster de AKS.

## <a name="next-steps"></a>Pasos siguientes

Si necesita datos adicionales para la solución de problemas, puede [ver los registros de kubelet][view-kubelet-logs] o [ver los registros del nodo maestro de Kubernetes][view-master-logs].


<!-- INTERNAL LINKS -->
[view-kubelet-logs]: kubelet-logs.md
[view-master-logs]: monitor-aks-reference.md#resource-logs
[aks-quickstart-cli]: kubernetes-walkthrough.md
[aks-quickstart-portal]: kubernetes-walkthrough-portal.md
[install-azure-cli]: /cli/azure/install-azure-cli
[aks-windows-rdp]: rdp.md
[ssh-nix]: ../virtual-machines/linux/mac-create-ssh-keys.md
[ssh-windows]: ../virtual-machines/linux/ssh-from-windows.md
[ssh-linux-kubectl-debug]: #create-the-ssh-connection-to-a-linux-node